# (Unofficial) Guide to Connecting to SIM Wi-Fi on Ubuntu and Other Linux Distributions

This guide explains how to connect to the **SIM_WiFi** network on Ubuntu and other Linux distributions using the terminal and `nmcli`.

> **Disclaimer:** This is an unofficial community guide and is not affiliated with or endorsed by SIM. Network configurations may change over time.

## Prerequisites

You will need `nmcli`, the command-line interface for **NetworkManager**.

NetworkManager is commonly installed by default on distributions such as Ubuntu and Fedora.

You can check whether `nmcli` is installed by running:

```bash
nmcli --version
```

---

## 1. Find the Wi-Fi Network

Open a terminal and run:

```bash
nmcli device wifi list
```

Look for the school Wi-Fi network:

```text
SIM_WiFi
```

Take note of the SSID. For this guide, we will use:

```text
SIM_WiFi
```

---

## 2. Find Your Wi-Fi Interface

Run:

```bash
nmcli device
```

You should see output similar to:

```text
DEVICE          TYPE      STATE          CONNECTION
wlp2s0          wifi      disconnected   --
lo              loopback  connected      lo
```

Look for the device where `TYPE` is `wifi`.

In the example above, the Wi-Fi interface is:

```text
wlp2s0
```

Your interface name will likely be different.

### If You Cannot Identify Your Wi-Fi Interface

One option is to connect to your phone's hotspot first:

```bash
nmcli device wifi connect "YOUR_HOTSPOT_SSID" password "YOUR_HOTSPOT_PASSWORD"
```

Then run:

```bash
nmcli device
```

The interface associated with the hotspot connection should be your Wi-Fi interface.

---

## 3. Create a Network Profile

Create a new NetworkManager connection profile:

```bash
nmcli connection add type wifi \
    ifname YOUR_WIFI_INTERFACE \
    con-name school-wifi \
    ssid "SIM_WiFi"
```

Replace:

```text
YOUR_WIFI_INTERFACE
```

with the interface name you found earlier.

For example:

```bash
nmcli connection add type wifi \
    ifname wlp2s0 \
    con-name school-wifi \
    ssid "SIM_WiFi"
```

`school-wifi` is simply the local name of the NetworkManager connection profile. You can change it if you prefer.

---

## 4. Configure WPA-Enterprise Authentication

Configure the connection to use WPA-Enterprise with PEAP and MSCHAPv2:

```bash
nmcli connection modify school-wifi \
    wifi-sec.key-mgmt wpa-eap \
    802-1x.eap peap \
    802-1x.identity "YOUR_USERNAME" \
    802-1x.password "YOUR_PASSWORD" \
    802-1x.phase2-auth mschapv2
```

Replace `YOUR_USERNAME` and `YOUR_PASSWORD` with your SIM credentials.

For example, if your required username format is:

```text
simstudent\username
```

enter the appropriate username provided to you by SIM.

> **Security:** Never upload your actual username or password to GitHub. Keep all credentials in examples as placeholders.

---

## 5. Certificate Configuration

If the SIM network configuration does not provide or require a CA certificate, NetworkManager can be configured without specifying one:

```bash
nmcli connection modify school-wifi 802-1x.ca-cert ""
```

> **Security note:** CA certificate validation normally helps protect WPA-Enterprise users from connecting to a malicious authentication server. Only use this configuration if it matches SIM's current network requirements. If SIM provides an official CA certificate or certificate-validation instructions, follow those instead.

---

## 6. Connect to SIM Wi-Fi

Activate the connection:

```bash
nmcli connection up school-wifi
```

If the connection is successful, NetworkManager should display a message similar to:

```text
Connection successfully activated
```

You can verify your connection with:

```bash
nmcli connection show --active
```

---

## Troubleshooting

If the connection fails, monitor NetworkManager's logs:

```bash
journalctl -u NetworkManager -f
```

Then try connecting again in another terminal:

```bash
nmcli connection up school-wifi
```

Watch the NetworkManager logs for authentication or configuration errors.

You can also check the current state of your network devices with:

```bash
nmcli device
```

### Remove the Profile and Start Again

If you want to delete the configuration and recreate it:

```bash
nmcli connection delete school-wifi
```

Then repeat the setup process.

---

## Notes

- This guide was written for Linux systems using **NetworkManager**.
- Commands and interface names may differ depending on your Linux distribution and hardware.
- The Wi-Fi configuration used by SIM may change.
- If an official SIM configuration conflicts with this guide, follow the official instructions.

## Contributing

If you discover that a command no longer works or find a configuration that works better on another Linux distribution, feel free to open an issue or submit a pull request.

## Author

Created by **[Your Name / GitHub Username]** as an unofficial community resource for SIM students using Linux.
