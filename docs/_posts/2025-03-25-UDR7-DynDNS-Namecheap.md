---
title: UDR7 Dynamic DNS using Namecheap
description: How to configure Namecheap Dynamic DNS service and Unifi Dream Router 7
date: 2025-03-25 09:00:00 +0100
categories: [Homelab]
tags: [ddns, udr7, namecheap] 
---
This procedure describes how to configure the Unifi Dream Router 7 Dynamic DNS service to periodically update A + Dynamic DNS Record `router.dubravko.com` at Namecheap DNS server with the public IP address of `Primary (WAN1)` interface.

## Namecheap DNS Configuration

1. Enable Dynamic DNS in the `Advanced DNS` settings
2. Note the `Dynamic DNS Password` value, as it will be needed later
3. Add a new `A + Dynamic DNS Record` with the following details

|Parameter | Value                                 |
| -------: | :------------------------------------ |
|Type      | `A + Dynamic DNS Record`{: .filepath} |
|Host      | `router`{: .filepath}                 |
|Value     | `1.1.1.1`{: .filepath}                |
|TTL       | `Automatic`{: .filepath}              |

## UDR7 Dynamic DNS Configuration

1. In the <https://unifi.ui.com> web console, navigate to `Settings > Internet > Primary (WAN1)`{: .filepath}
2. Set the Advanced option to `Manual`
3. Under Dynamic DNS, click <kbd>+ Create New Dynamic DNS</kbd> and set the following values

|Parameter | Value                                                                                       |
| -------: | :------------------------------------------------------------------------------------------ |
|Service   | `namecheap`{: .filepath}                                                                    |
|Hostname  | `router`{: .filepath}                                                                       |
|Username  | `dubravko.com`{: .filepath}                                                                 |
|Password  | Paste the password noted during [Namecheap DNS Configuration](#namecheap-dns-configuration) |
|Server    | `https://dynamicdns.park-your-domain.com/update?`{: .filepath}                              |

## Set up UDR7 periodic updates

When the UDR7 is behind a non-bridged telecom router, it cannot detect changes in its public IP address. This limitation prevents the automatic triggering of DDNS updates. To overcome this issue, you we can set up a cron job to periodically force DDNS updates. This involves editing the `root crontab` to add a line that runs the `inadyn` command at regular intervals, typically every five minutes.

> This step is required only if your UDR7 is behind non-bridged telecom router. If the telecom router is set to bridge mode and the UDR7 is configured to connect to the Internet directly via a PPPoE connection, UDR7 will automatically detect each public IP change and trigger DynDNS update.
{: .prompt-info }

### Detect inadyn configuration file name

The file `/run/ddns-ethX-inadyn.conf`{: .filepath} is created by the UniFi Network Controller when you [configure the Dynamic DNS service through the web UI](#udr7-dynamic-dns-configuration). This configuration file contains the necessary settings for the `inadyn` DDNS client to update your Namecheap DDNS record. The filename may vary depending on which Ethernet interface is used for your WAN connection. It's crucial to use the correct filename in your cron job to ensure proper DDNS updates.

To detect the proper file name for your configuration:

1. SSH into your UDR7 device
2. Navigate to the `/run`{: .filepath} directory:
```bash
cd /run
```
3. List DDNS configuration files:
```bash
ls ddns-eth*-inadyn.conf
```
4. Note the exact filename (e.g., `ddns-eth3-inadyn.conf`{: .filepath})

### Edit the Crontab

1. Open the root crontab for editing:
```bash
sudo crontab -e
```
2. Add the following line to the crontab:
```
# Namecheap DynDNS record update every 5 minutes
*/5 * * * * /usr/bin/inadyn -n -s -C -f /run/ddns-eth3-inadyn.conf -1 -l --foreground --force
```

### Save the Crontab

1. Press <kbd>Esc</kbd>
2. Type `:wq`
3. Press <kbd>Enter</kbd>

### Verify the Cron Job

1. Display the current crontab:
```bash
sudo crontab -l
```
2. Confirm the new line is present and correctly formatted.

This configuration will execute the Dynamic DNS update every 5 minutes, ensuring your public IP address remains current with your Namecheap Dynamic DNS record.