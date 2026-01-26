1. What command did you use to find the IP Address of your metasploitable2 VM?
`ifconfig`

2. What command did you use for the nmap scan?
`nmap -sCV -p- -T4 -A 192.168.56.101 -oN <scan-results-file>`

3. From the results of your Nmap scan, what TCP port is the nfs listening on?
`2049/tcp   nfs`
