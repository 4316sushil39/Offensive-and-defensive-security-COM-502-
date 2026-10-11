# Experiment 4: Firewall and IDS Setup with pfSense and Snort

## Aim

To review firewall rules in pfSense, monitor network traffic with Snort, and check whether test ICMP traffic appears in the IDS alert log.

## Tools and Lab Setup

- pfSense firewall virtual machine
- Kali Linux virtual machine on the pfSense LAN
- Snort package enabled on the pfSense LAN interface


## Theory

A firewall checks network traffic against rules and allows or blocks packets based on those rules. Rule order matters: traffic is evaluated against the rules on the interface it enters.

An intrusion detection system (IDS), such as Snort, watches network traffic for patterns that match its configured rules and records alerts. In this experiment, Snort is enabled on the LAN interface with blocking mode disabled, so it monitors and logs rather than blocking traffic.

## Procedure

### Step 1: Check the pfSense network addresses

Start the pfSense virtual machine and check its console. In this lab, the LAN address is `192.168.1.1/24`. The console also displays the web configurator address.

![pfSense console showing the network addresses](screenshots/step_1_pfsense_console.png)

### Step 2: Open the pfSense web configurator

From a browser in the lab network, open the LAN address shown on the console. For this setup, go to:

```text
https://192.168.1.1
```

Sign in using the credentials configured for your pfSense lab.

![pfSense sign-in page](screenshots/step_2_pfsense_login.png)

### Step 3: Review the LAN firewall rules

In pfSense, open **Firewall > Rules > LAN**. Review the existing rules and their order before making changes. If you edit or add a rule, verify its action, protocol, source, and destination. Click **Apply Changes** to activate pending rule changes.

The screenshot shows a pending-change notice. Make sure any changes you intended to make are applied before testing.

![LAN firewall rules](screenshots/step_3_firewall_rules.png)

### Step 4: Check Kali's LAN address

Open a terminal in Kali Linux and identify the active network interface and its IPv4 address:

```bash
ip a
```

Confirm that Kali is connected to the pfSense LAN. In this lab, Kali uses `192.168.1.10/24`.

![Kali network interface and IP address](screenshots/step_4_kali_network.png)

### Step 5: Enable Snort monitoring on the LAN

In the pfSense web configurator, open **Services > Snort > Interfaces**. Enable Snort on the LAN interface and save the configuration. The screenshot shows the LAN interface active with **Blocking Mode** disabled, which keeps this setup in alert-only mode.

![Snort interface status](screenshots/step_5_snort_interface.png)

### Step 6: Generate test ICMP traffic

From Kali, send a few ping requests to the pfSense LAN address:

```bash
ping -c 4 192.168.1.1
```

Successful replies confirm that Kali can reach the pfSense LAN address.

![Ping test from Kali to pfSense](screenshots/step_6_ping_test.png)

### Step 7: Review the Snort alerts

In pfSense, open **Services > Snort > Alerts** and select the LAN interface. Review the alert log for events related to the test traffic. The screenshot shows ICMP test alerts in the log.

![Snort alert log showing ICMP events](screenshots/step_7_snort_alerts.png)

## Result

The LAN firewall rules were reviewed, Snort monitoring was enabled on the LAN interface, and ICMP traffic was sent from Kali to pfSense. ICMP test events are visible in the Snort alert log.

## Conclusion

This experiment demonstrated how pfSense firewall rules control traffic and how Snort can monitor a network interface and report matching activity. IDS alerts help identify events for investigation; they do not, by themselves, mean that traffic was blocked.
