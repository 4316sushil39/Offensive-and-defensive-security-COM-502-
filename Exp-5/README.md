# Experiment 5: ARP Spoofing and Session Hijacking

## Aim

To study ARP spoofing in an isolated virtual lab and observe HTTP traffic exchanged with DVWA. The experiment demonstrates why unencrypted web traffic can put session information at risk.

## Lab Setup

| Machine | Role | IP address |
| --- | --- | --- |
| Kali Linux (`eth0`) | Testing machine | `192.168.188.128` |
| Metasploitable 2 with DVWA | Web server | `192.168.188.134` |
| Lab gateway | Gateway | `192.168.188.2` |

**Tools:** `arpspoof` (dsniff), Wireshark, and a web browser.

> Perform this experiment only on your own isolated lab or on systems for which you have permission.

## Theory

### ARP spoofing

Devices on a local network use ARP to associate an IP address with a MAC address. ARP spoofing sends false ARP replies so that a device may temporarily associate another machine's IP address with the tester's MAC address. In a two-way setup, the tester attempts to place itself between the victim and gateway. Enabling IP forwarding allows packets passing through the tester to continue to their destination.

The two commands used in this lab target each side of the connection:

```bash
# Send false replies to the victim about the gateway
sudo arpspoof -i eth0 -t 192.168.188.134 192.168.188.2

# Send false replies to the gateway about the victim
sudo arpspoof -i eth0 -t 192.168.188.2 192.168.188.134
```

### HTTP traffic and session cookies

HTTP does not encrypt its contents. If a web application sends a session cookie over plain HTTP, someone able to capture that traffic may be able to read it. A session cookie can act like a temporary password, so it must be kept private. HTTPS encrypts traffic in transit and helps protect cookies from network observers.

**How the concepts connect:** ARP spoofing may redirect local traffic; packet capture can then be used to inspect traffic that is visible to the tester; and HTTP traffic may expose sensitive data if the application sends it without encryption.

## Procedure

The screenshots below follow the lab workflow. Each one is placed next to the concept it demonstrates.

### Step 1: Check the Kali network

On Kali, use `ip a` to confirm that `eth0` is up and has the expected lab address.

The screenshot shows `eth0` configured as `192.168.188.128/24`, placing Kali and Metasploitable on the same subnet.

![Step 1: Kali eth0 address](Screenshots/Step1.png)

### Step 2: Check the Metasploitable network

On Metasploitable 2, use `ip a` to identify its address and ping the gateway to check basic connectivity.

```bash
ip a
ping -c 2 192.168.188.2
```

The screenshot shows the Metasploitable address (`192.168.188.134`), successful replies from the gateway, and its interface configuration.

![Step 2: Metasploitable IP address and gateway connectivity](Screenshots/Step2.png)

### Step 3: Send false gateway information to the victim

Enable packet forwarding on Kali, then run this command in a Kali terminal. It sends ARP replies to Metasploitable (`192.168.188.134`) claiming that the gateway (`192.168.188.2`) is at Kali's MAC address.

```bash
sudo sysctl -w net.ipv4.ip_forward=1
sudo arpspoof -i eth0 -t 192.168.188.134 192.168.188.2
```

The repeated ARP replies in the screenshot are evidence that `arpspoof` is running in this direction. They do not, by themselves, confirm that both directions of the traffic are being intercepted.

![Step 3: ARP replies sent to the victim](Screenshots/Step3.png)

### Step 4: Send false victim information to the gateway

In a second Kali terminal, run the reverse command. This sends ARP replies to the gateway (`192.168.188.2`) claiming that Metasploitable (`192.168.188.134`) is at Kali's MAC address. Running both commands is intended to place Kali between the two machines.

```bash
sudo arpspoof -i eth0 -t 192.168.188.2 192.168.188.134
```

![Step 4: ARP replies sent to the gateway](Screenshots/Step4.png)

### Step 5: Capture traffic and log in to DVWA

Start capturing on Kali's `eth0` in Wireshark. In the lab browser, open `http://192.168.188.134/dvwa/` and sign in with the lab account (`admin` / `password`).

The browser screenshot shows the DVWA page and the authenticated `admin` session. This confirms the lab login; it is not evidence that the session was hijacked.

![Step 5: Authenticated DVWA page](Screenshots/Step5.png)

### Step 6: Inspect the HTTP packets

In Wireshark, apply the display filter `http` to find the web traffic.

The screenshot shows HTTP packets exchanged between `192.168.188.128` (Kali) and `192.168.188.134` (Metasploitable). HTTP does not encrypt the packet contents. However, the screenshot does not show a session cookie or prove that a cookie was extracted or reused.

![Step 6: HTTP packets between Kali and Metasploitable](Screenshots/Step6.png)

## Result

The screenshots document successful lab connectivity, the Kali interface configuration, ARP spoofing commands, an authenticated DVWA page, and HTTP packets between Kali and Metasploitable. The evidence illustrates the risks of plain HTTP, but does not visibly demonstrate session-cookie extraction or reuse.

## Conclusion

ARP spoofing can manipulate local network address information, while unencrypted HTTP can expose data sent between a browser and a web application. Use HTTPS and secure cookie settings in real applications. Keep security testing isolated, authorized, and careful with any captured session data.
