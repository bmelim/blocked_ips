# Blocked IPs List

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
![Update Frequency](https://img.shields.io/badge/updates-every%202%20hours-blue.svg)
![Community](https://img.shields.io/badge/contributions-welcome-orange.svg)

---

## Overview

This repository publishes a **curated list of malicious or suspicious IP addresses** observed in firewall/IPS logs.  
Addresses are cleaned, deduplicated, and filtered to remove trusted vendor ranges before release.

- **Update cadence:** every 2 hours  
- **Reporting:** confirmed bad actors are reported to threat-intel services  
- **Format:** plain text, one IP per line

---

## Contents

- `lists/blocked-ips.txt` — latest blocklist (one IP per line)
- `archive/` — optional historical snapshots (if retained)

---

## Usage

Import the list into your firewall, IPS, or other security tooling using the raw URL.

### iptables

    curl -s https://raw.githubusercontent.com/bmelim/blocked_ips/main/lists/blocked-ips.txt -o /etc/firewall/blocked-ips.txt
    while read ip; do
      iptables -A INPUT -s "$ip" -j DROP
    done < /etc/firewall/blocked-ips.txt

### PostgreSQL

    COPY blocked_ips (ip)
    FROM PROGRAM 'curl -s https://raw.githubusercontent.com/bmelim/blocked_ips/main/lists/blocked-ips.txt'
    WITH (FORMAT text);

### Sophos XGS (Active Threat Response)

1. Go to **Protect → Active Threat Response → Third-party threat feeds**  
2. Add a **Third-Party Feed** and paste the raw URL:  
   `https://raw.githubusercontent.com/bmelim/blocked_ips/main/lists/blocked-ips.txt`  
3. Sophos will ingest and auto-refresh the feed (this list updates every 2 hours)

---

## How It’s Built

1. **Collection** — IPs gathered from Sophos firewall/IPS events  
2. **Enrichment** — checked against threat-intel (e.g., AbuseIPDB)  
3. **Cleaning** — remove trusted vendor ranges (Apple, Microsoft, Google Services, AWS, Cloudflare, etc.) to reduce false positives  
4. **Publishing** — export a deduplicated IP list to GitHub every 2 hours

---

## Benefits

- Reduce attack surface by blocking **known bad actors**
- Clean, vendor-filtered, deduplicated list
- Easy to consume via a stable **raw URL**
- Open to the community and free to use

---

## Important Notes

Care is taken to maintain accuracy of this blocklist, including **whitelisting trusted service providers** such as Apple, Microsoft, Google, AWS, and Cloudflare.  
However, environments differ and trusted providers can still have compromised or abusive hosts.  

- Always validate the list against your own **network traffic and business needs**.  
- Use monitoring to detect if legitimate services are being blocked.  
- Apply this list in **"alert/test" mode** first, before enforcing in production firewalls.

This project aims to **reduce noise and known bad traffic**, but should complement — not replace — your own security monitoring and threat intelligence processes.

---

## License

Released under the [MIT License](LICENSE). Free for commercial and non-commercial use.
