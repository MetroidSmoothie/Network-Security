# Project Tasks & Solutions

This section documents the security and network configuration tasks completed within the configured lab environment. Each task simulates a real-world operational requirement and demonstrates practical firewall, NAT, and intrusion detection skills.

---

# Project Task 1 – Blocking Unwanted Traffic

## Objective

HR identified that employees were misusing company resources for non-business activities. The requirement was to restrict unnecessary protocols while keeping normal business web traffic available. HTTP/HTTPS access must remain functional while non-essential ICMP traffic is restricted.

## Implementation

### Step 1 – Baseline Testing
From the Debian client:

- Run `ping <LAN hosts>`
- Run `ping 4.2.2.2`
- Run `ping 8.8.8.8`

Result: All traffic was initially allowed.

### Step 2 – LAN Firewall Rule
Created a rule on the LAN interface:

- Action: Block  
- Protocol: ICMP  
- Destination: 8.8.8.8  
- Logging: Enabled  

Purpose: Prevent unwanted traffic and log matches.

### Step 3 – WAN Rule
Created a WAN rule:

- Action: Allow  
- Protocol: ICMP  

Purpose: Allow testing tools while keeping LAN restrictions in place.

## Validation / Evidence

- HTTP/HTTPS browsing still works  
- Ping to 8.8.8.8 fails  
- Firewall logs show blocked ICMP packets  
- LAN systems remain reachable  

## Outcome

Non-essential traffic successfully blocked while business web communication remained unaffected.

---

# Project Task 2 – Quick Solution for Remote Access

## Objective

An employee required temporary remote access to:

- Internal web server (HTTP)
- Ubuntu server (SSH)

Since VPN was unavailable, a temporary solution was needed.

## Identified Issue

From the physical host:

- Web server unreachable  
- SSH unreachable  

Cause: No inbound NAT/firewall rules.

## Solution

Implemented NAT port forwarding on pfSense.

Created rules:

- WAN port 80 → Ubuntu LAN IP port 80  
- WAN port 22 → Ubuntu LAN IP port 22  

Enabled automatic WAN firewall rule creation.

## Validation / Evidence

- Web server reachable via `http://<WAN-IP>`  
- SSH login successful using `ssh ubuntu@<WAN-IP>`  
- Internal LAN connectivity verified  
- Services accessible externally only through forwarded ports  

## Outcome

Temporary secure remote access provided without VPN deployment.

---

# Project Task 3 – The All-Seeing Eye

## Objective

Deploy a detection and prevention mechanism capable of identifying:

- Brute-force attacks  
- Denial-of-Service attacks  

## Service Selected

Suricata (IDS/IPS)

Reason: Native pfSense integration with real-time alerting and rule-based detection.

## Implementation

### Step 1 – Update pfSense
Verified the system was running the latest stable version.

### Step 2 – Install Suricata
Installed Suricata via the pfSense package manager.

### Step 3 – Configure Detection Rules
Configured custom rules for:

- SSH brute-force detection  
- HTTP SYN flood detection  

### Step 4 – Attack Simulation (Kali)

Executed:

`ncrack -p 22 --user ubuntu -P rockyou.txt <WAN-IP>`

`hping3 -S --flood -p 80 <WAN-IP>`

## Validation / Evidence

- Suricata alerts triggered  
- IDS logs populated  
- Alert timestamps matched attack execution  
- Dashboard showed detected events  
- Packet captures confirmed suspicious traffic  

## Outcome

Suricata successfully detected both brute-force and DoS attack simulations, validating that monitoring and prevention controls were functioning correctly.

---

# Skills Demonstrated

- Firewall rule creation  
- Traffic filtering  
- NAT port forwarding  
- Remote service exposure  
- IDS/IPS deployment  
- Attack simulation and detection  
- Network troubleshooting  
- Evidence-based validation

