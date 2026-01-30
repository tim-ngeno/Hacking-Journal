## Scanning with Nmap

### Host Discovery
```bash
sudo nmap -sn TARGET

Starting Nmap 7.95 ( https://nmap.org ) at 2026-01-27 07:53 EST
Nmap scan report for 10.0.2.3
Host is up (0.00060s latency).
MAC Address: 08:00:27:AE:46:FB (PCS Systemtechnik/Oracle VirtualBox virtual NIC)
Nmap done: 1 IP address (1 host up) scanned in 0.57 seconds
```

### TCP Port Scanning
Has 2 types, either a connect scan (`-sT`) or a SYN scan (`sS`).
```bash
sudo nmap -sT TARGET
sudo nmap -sS TARGET
```
We use sudo so Nmap can generate arbitrary network packets for the scan.

```bash
Starting Nmap 7.95 ( https://nmap.org ) at 2026-01-27 08:16 EST
Nmap scan report for 10.0.2.3
Host is up (0.0012s latency).
Not shown: 977 closed tcp ports (reset)
PORT     STATE SERVICE
21/tcp   open  ftp
22/tcp   open  ssh
23/tcp   open  telnet
25/tcp   open  smtp
53/tcp   open  domain
80/tcp   open  http
111/tcp  open  rpcbind
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
512/tcp  open  exec
513/tcp  open  login
514/tcp  open  shell
1099/tcp open  rmiregistry
1524/tcp open  ingreslock
2049/tcp open  nfs
2121/tcp open  ccproxy-ftp
3306/tcp open  mysql
5432/tcp open  postgresql
5900/tcp open  vnc
6000/tcp open  X11
6667/tcp open  irc
8009/tcp open  ajp13
8180/tcp open  unknown
MAC Address: 08:00:27:AE:46:FB (PCS Systemtechnik/Oracle VirtualBox virtual NIC)

```

### UDP Port Scanning
`sudo nmap -sU TARGET`
UDP is connectionless and thus inaccurate to see if a UDP port is open or not.
We can enable service and version scanning for more accurate results.
By default a UDP scan takes long, we can also add a timing template for faster results.


### OS Detection
`sudo nmap -O TARGET`
This implicitly performs port discovery as well.

```bash
...
Device type: general purpose
Running: Linux 2.6.X
OS CPE: cpe:/o:linux:linux_kernel:2.6
OS details: Linux 2.6.9 - 2.6.33
Network Distance: 1 hop
...
```

### Version and service scanning
`sudo nmap -sV TARGET`

### Complete scanning
`sudo nmap -A TARGET`
