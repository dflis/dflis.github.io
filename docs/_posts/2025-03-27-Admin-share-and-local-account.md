---
title: Accessing Administrative Shares with Local Administrator Accounts
description: How to solve issue of accessing administrative shares with local administrator accounts on Windows systems
date: 2025-03-27 16:45:00 +0100
categories: [Windows]
tags: [windows, administration, uac] 
---
Windows systems restrict access to administrative shares (e.g., `C$') for local user accounts, even if they are members of the Administrators group. This limitation can pose challenges in various scenarios, such as:

 * Remote management of Windows machines
 * Automated file transfers to administrative shares
 * Scripted access to system resources across multiple computers

The restriction is a result of the User Account Control (UAC) mechanism, which filters access tokens for local administrator accounts, effectively blocking access to administrative shares.

## Possible Solutions
Several approaches can be taken to address this issue.

### Create New Shares
Microsoft's recommended solution is to create new shares with appropriate permissions for the resources you need to access. This method provides better control over access rights and is more secure than using administrative shares.

### Use Domain Account with Administrative Privileges
Domain accounts with administrative rights on the target machine can access administrative shares without restrictions.

### Use Built-in Administrator Account
The `built-in` Administrator account retains full access to administrative shares. 
> This approach is not recommended for regular use due to security concerns.
{: .prompt-warning }

### Modify Registry Settings (Not Recommended)
A workaround involves adding the `LocalAccountTokenFilterPolicy` registry key:

|Parameter | Value                                                                                       |
| -------: | :------------------------------------------------------------------------------------------ |
| Path     | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System`{: .filepath} |
| Name     | `LocalAccountTokenFilterPolicy`{: .filepath}                                                |
| Type     | `DWORD`{: .filepath}                                                                        |
| Value    | `1`{: .filepath}                                                                            |

> This allows local administrator accounts to access administrative shares. However, this method is not recommended as it can have significant security implications.
{: .prompt-danger }