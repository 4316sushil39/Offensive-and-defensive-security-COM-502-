# Experiment 6: Scanning DVWA with OWASP ZAP

## Aim

To use OWASP ZAP to scan the DVWA web application in an authorized lab and review the scan activity.

## Lab setup

- Kali Linux with OWASP ZAP
- A DVWA web server on the lab network
- Kali and the DVWA server connected to a network where they can communicate

Only scan systems that you own or have permission to test. Keep this experiment in your isolated lab.

## Theory

DVWA (Damn Vulnerable Web Application) is an intentionally vulnerable website used for security practice. OWASP ZAP (Zed Attack Proxy) is a web security testing tool that can send requests to a website and report possible vulnerabilities. Its automated scan explores the site and checks the pages it can reach. The results are alerts that should be reviewed and verified; they are not automatically proof that a vulnerability can be exploited.

## Procedure

### Step 1: Find the Metasploitable IP address

On the Metasploitable machine, run this command and note its IP address:

```bash
ip addr
```

Use the address shown for its active network interface when connecting to the lab machine.

![Step 1: Metasploitable network address](Screenshots/Screenshot%202026-10-16%20145938.png)

### Step 2: Find Kali's IP address

On Kali, run `ip addr` and note the address of the interface connected to the lab network. In this experiment, Kali's `eth1` is on the `192.168.25.0/24` network.

```bash
ip addr
```

![Step 2: Kali network interfaces and IP address](Screenshots/Screenshot%202026-10-16%20150027.png)

### Step 3: Open the DVWA login page

In Kali's browser, open the DVWA address for your lab. In this experiment, it is:

```text
http://192.168.25.129/dvwa/
```

Sign in with the account configured for your DVWA lab.

![Step 3: DVWA login page](Screenshots/Screenshot%202026-10-16%20150453.png)

### Step 4: Set DVWA's security level

Open **DVWA Security** and select the security level required for the exercise. The screenshot shows the level set to **low**. Use low security only in this isolated training environment.

![Step 4: DVWA security level set to low](Screenshots/Screenshot%202026-10-16%20150555.png)

### Step 5: Update Kali and install OWASP ZAP

Update Kali's package list and install ZAP:

```bash
sudo apt update
sudo apt install zaproxy
```

If `apt update` fails because it is trying to use an installation CD/DVD, comment out the old CD-ROM entry and try again:

```bash
sudo sed -i '/^deb cdrom:/s/^/#/' /etc/apt/sources.list
sudo apt update
sudo apt install zaproxy
```

After installation, open OWASP ZAP. The screenshot shows the ZAP start screen before beginning the scan.

![Step 5: OWASP ZAP start screen](Screenshots/Screenshot%202026-10-16%20152234.png)

### Step 6: Run an automated scan

In ZAP's **Automated Scan** screen, enter the DVWA URL and start the scan. For this lab, use:

```text
http://192.168.25.129/dvwa/
```

The screenshot shows ZAP actively scanning the site and recording requests and alerts. Let the scan finish before reviewing its results.

![Step 6: OWASP ZAP scanning DVWA](Screenshots/Screenshot%202026-10-16%20152505.png)

## Result

OWASP ZAP was configured to scan the DVWA site in the lab. The final screenshot shows the scan in progress, with requests and alerts appearing in ZAP.

## Conclusion

This experiment demonstrated how OWASP ZAP can scan a web application and report potential security issues. Review alerts carefully: a scan result is a finding to investigate, not by itself proof that a vulnerability can be exploited.
