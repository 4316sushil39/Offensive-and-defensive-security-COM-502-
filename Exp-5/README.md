# Experiment 5: ARP Spoofing and Session Hijacking

## Aim

To demonstrate, in an isolated lab, how ARP spoofing can place an attacker machine between a victim and the gateway, and how unencrypted HTTP traffic can expose a web session.

## Lab Setup

- **Attacker:** Kali Linux (`192.168.188.128`, interface `eth0`)
- **Victim:** Metasploitable 2 running DVWA (`192.168.188.134`)
- **Gateway:** `192.168.188.2`
- **Tools:** `arpspoof` from dsniff and Wireshark

Only perform this experiment on systems and networks you own or are authorized to test.

## Theory

**ARP spoofing** sends false ARP replies on a local network. In this lab, the replies make the victim and gateway associate each other's IP address with the attacker's MAC address, allowing traffic to pass through the Kali machine.

**Session hijacking** is the unauthorized reuse of a valid web session. If a session cookie is sent over unencrypted HTTP, it may be visible to someone who can capture that traffic. HTTPS helps protect session data while it is in transit.

## Procedure

### Step 1: Check the victim's network connection

On Metasploitable 2, use `ip a` to check its IP address. Verify that it can reach the gateway on the lab network.

Commands used:
```bash
ip a
ping -c 2 192.168.188.2
```

![Step 1: Metasploitable IP address and connectivity](Screenshots/Screenshot%202026-10-15%20175549.png)

### Step 2: Check the Kali network interface

On Kali, run `ip a` and confirm that `eth0` has the expected lab IP address.

Command used:
```bash
ip a
```

![Step 2: Kali IP address](Screenshots/Screenshot%202026-10-15%20175614.png)

### Step 3: Enable packet forwarding

On Kali, enable IPv4 forwarding so that intercepted traffic can continue to travel between the victim and gateway.

Command used:
```bash
sudo sysctl -w net.ipv4.ip_forward=1
```

### Step 4: Start ARP spoofing in both directions

Open two Kali terminal windows. Run one command in each so the victim and gateway both send their traffic through Kali. The screenshots below follow the order in which the two commands were run.

In the first terminal, poison the gateway's ARP entry for the victim:
```bash
sudo arpspoof -i eth0 -t 192.168.188.2 192.168.188.134
```

![Step 4a: ARP spoofing toward the gateway](Screenshots/Screenshot%202026-10-15%20175700.png)

In the second terminal, poison the victim's ARP entry for the gateway:
```bash
sudo arpspoof -i eth0 -t 192.168.188.134 192.168.188.2
```

![Step 4b: ARP spoofing toward the victim](Screenshots/Screenshot%202026-10-15%20175941.png)

### Step 5: Start a Wireshark capture

In Wireshark, start capturing on `eth0` before logging in to DVWA. Use the display filter `http` to find the captured HTTP packets.

### Step 6: Log in to DVWA

On the victim machine, open `http://192.168.188.134/dvwa/` and sign in using the lab account (`admin` / `password`).

![Step 6: DVWA page after login](Screenshots/Screenshot%202026-10-15%20175839.png)

### Step 7: Inspect the captured HTTP traffic

In Wireshark, apply the `http` display filter and inspect the HTTP packets captured during the login. In an authorized lab, the session cookie may be visible because HTTP does not encrypt it. Treat the cookie as a temporary password and do not share it.

![Step 7: HTTP packets displayed in Wireshark](Screenshots/Screenshot%202026-10-15%20175909.png)

## Result

The lab demonstrated ARP spoofing between the victim and gateway and captured HTTP traffic during a DVWA login. This illustrates why web applications should use HTTPS and protect session cookies.

## Conclusion

This experiment showed how ARP spoofing can redirect local network traffic and why unencrypted HTTP can put web sessions at risk. Use HTTPS and secure cookie settings in real applications, and conduct testing only in an authorized, isolated environment.
