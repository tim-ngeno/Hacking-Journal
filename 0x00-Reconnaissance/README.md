# Recon Tools


### Autonomous Systems (AS)
- MXToolbox - [Autonomous System Number Lookup](https://mxtoolbox.com/asn.aspx)
- [HackerTarget](https://hackertarget.com/as-ip-lookup/)
- [ANSLookup](https://asnlookup.com/)

### Shodan
Shodan is a search engine for finding specific devices, and devices types, that exist online.
From webcams, linksys, cisco, netgear, to SCADA, etc.
It works by scanning the entire internet and parsing the banners that are returned by various devices. Using that information, Shodan returns info like web server (and version), number of FTP servers, and what make/model they may be.

- [Shodan Search Queries](https://github.com/jakejarvis/awesome-shodan-queries)
- [Getting the Most Out of Shodan Searches (SANS Penetration Testing blog):](https://www.sans.org/blog/getting-the-most-out-of-shodan-searches/)

### Whois
The `whois` system allows access to directory info stored by domain name registrars.
`whois google.com`

#### `sublist3r`
#### `subfinder`
`dnsdumpster.com`


### Fierce
`Fierce` is a semi-lightweight scanner that helps locate non-contiguous IP space and hostnames against specified domains. It’s really meant as a pre-cursor to nmap, unicornscan, nessus, nikto, etc, since all of those require that you already know what IP space you are looking for. This does not perform exploitation and does not scan the whole internet indiscriminately. It is meant specifically to locate likely targets both inside and outside a corporate network.

### SSL Certificates
SSL certificates can be a useful source of hostnames that may be of interest in a penetration test. 

[SSL Server Test](https://www.ssllabs.com/ssltest/)

##### Using NMAP
`nmap -p 443 --script ssl-cert example.com`




## PIVOT
In a professional pentest, finding subdomains via SSL certificates is part of Attack Surface Mapping. Every new subdomain discovered is a new potential entry point that might be less secure than the main website.

Here is how those subdomains are used as entry points:
1. Finding "Forgotten" Infrastructure

Large companies often have a very secure main page (e.g., google.com), but their subdomains might be running older, unpatched software.

    The Scenario: You find dev-test.target.com in the SSL SAN list.

    The Entry Point: Because it's a "test" site, the developers might have left debugging mode active, or it might be running an old version of WordPress or Jenkins that has a known RCE (Remote Code Execution) exploit.

2. Bypassing Main Protections (WAF Bypassing)

Many companies put a Web Application Firewall (WAF) in front of their main domain to block SQL injection and XSS.

    The Scenario: The WAF is active on www.target.com, but the company forgot to put it in front of api-staging.target.com.

    The Entry Point: You can launch your attacks against the staging subdomain. Since both sites likely talk to the same database, you can compromise the data via the unprotected subdomain.

3. Takeover Vulnerabilities (Subdomain Takeover)

This is a high-impact "bug bounty" favorite.

The Scenario: The SSL certificate lists helpdesk.target.com. When you visit it, you see a "404 Not Found" page from a provider like Zendesk or AWS.

The Entry Point: This means the company pointed their DNS to a third-party service but then canceled the service without removing the DNS record. An attacker can sign up for that service, claim the name helpdesk.target.com, and completely control that subdomain to steal cookies or host phishing pages.

4. Lateral Movement & Trust

Subdomains often share Cookies or CORS (Cross-Origin Resource Sharing) permissions.

The Scenario: You find a minor XSS (Cross-Site Scripting) vulnerability on a useless "https://www.google.com/search?q=internal-weather.target.com" subdomain.

The Entry Point: Because browsers often treat subdomains as "related," you might be able to use that XSS to steal a session cookie that is valid for the main portal.target.com.


### Example: Scanning a newly discovered subdomain for vulnerabilities
```bash
nmap -p 80,443 --script vuln <subdomain.target.com>
```
