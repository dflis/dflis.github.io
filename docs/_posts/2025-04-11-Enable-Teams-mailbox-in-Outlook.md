---
title: Teams Mailbox Inaccessible in Outlook
description: How to resolve the issue when team mailboxes cannot be accessed from Outlook due to Microsoft's default "HiddenFromExchangeClientsEnabled" setting on new Teams
date: 2025-04-11 15:46:00 +0200
categories: [Microsoft 365]
tags: [teams, sharepoint, outlook, mailbox]
---
This procedure describes how to fix an issue where team mailboxes cannot be accessed through Outlook, which becomes apparent when clicking the <kbd>Conversations</kbd> link in a Team's SharePoint site redirects to your personal mailbox instead of the team's shared mailbox.

## Issue Description
When you create a new Team in Microsoft Teams and then attempt to access the team's mailbox through various methods (SharePoint Conversations link, Outlook groups, etc.), you are unable to see or access the team's shared mailbox. Instead, you may be redirected to your personal mailbox or receive an error that the mailbox cannot be found.

## Root Cause
Microsoft has changed the default settings for new Teams-enabled Microsoft 365 Groups. By default, new Teams are now configured with the setting `HiddenFromExchangeClientsEnabled` set to `true` (or `don't show team email address in Outlook` enabled in the admin portal), which hides the team mailbox from Outlook and Outlook Web App clients. This prevents users from accessing the team's shared conversations through normal Outlook interfaces.

## Administrator Solution

### Modify Group Settings via PowerShell
1. Verify if the team mailbox is hidden by running:
```powershell
Get-UnifiedGroup "TeamName" | Select-Object HiddenFromExchangeClientsEnabled
```
2. Make the team mailbox visible:
```powershell
Get-UnifiedGroup "TeamName" | Set-UnifiedGroup -HiddenFromExchangeClientsEnabled:$false
```

### Modify Group Settings via Admin Portal
1. Navigate to the Microsoft 365 admin portal
2. Go to <kbd>Teams & groups</kbd> > <kbd>Active teams & groups</kbd>
3. Select the specific Team
4. Click on <kbd>Settings</kbd>
5. Locate and disable the option `don't show team email address in Outlook`
6. Save the changes