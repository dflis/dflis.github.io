---
title: UDR7 Dynamic DNS using Namecheap
description: How to configure Namecheap Dynamic DNS service and Unifi Dream Router 7
date: 2025-03-25 09:00:00 +0100
categories: [Homelab]
tags: [ddns, udr7, namecheap] 
---
This procedure describes how to configure the Unifi Dream Router 7 Dynamic DNS service to periodically update A + Dynamic DNS Record `router.dubravko.com on` at Namecheap DNS server with the public IP address of `Primary (WAN1)` interface.

> This configuration will only periodically update the DNS record if the telecom router is set to bridge mode and the UDR7 is configured to connect to the Internet directly via a PPPoE connection. Otherwise, the UDR7 will not automatically detect changes in the public IP address, which would prevent automatic DynDNS updates from triggering.
{: .prompt-info }


## Namecheap DNS Configuration

1. Enable Dynamic DNS in the Advanced DNS settings
2. Note the Dynamic DNS Password value, as it will be needed later
3. Add a new A + Dynamic DNS Record with the following details

|Parameter | Value                                 |
| -------: | :------------------------------------ |
|Type      | `A + Dynamic DNS Record`{: .filepath} |
|Host      | `router`{: .filepath}                 |
|Value     | `1.1.1.1`{: .filepath}                |
|TTL       | `Automatic`{: .filepath}              |

## UDR7 Configuration

1. In the <https://unifi.ui.com> web console, navigate to `Settings > Internet > Primary (WAN1)`{: .filepath}
2. Set the Advanced option to `Manual`
3. Under Dynamic DNS, click `+ Create New Dynamic DNS` and set the following values

|Parameter | Value                                                                                       |
| -------: | :------------------------------------------------------------------------------------------ |
|Service   | `namecheap`{: .filepath}                                                                    |
|Hostname  | `router`{: .filepath}                                                                       |
|Username  | `dubravko.com`{: .filepath}                                                                 |
|Password  | Paste the password noted during [Namecheap DNS Configuration](#namecheap-dns-configuration) |
|Server    | `https://dynamicdns.park-your-domain.com/update?`{: .filepath}                              |
