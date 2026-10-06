# Sophos Security Manager

[![Latest release](https://img.shields.io/github/v/release/eslamifar/SophosSecurityManager-Releases?display_name=tag&sort=semver)](https://github.com/eslamifar/SophosSecurityManager-Releases/releases/latest)
[![Platform](https://img.shields.io/badge/platform-Windows%20x64-0078D6)](https://github.com/eslamifar/SophosSecurityManager-Releases/releases/latest)

Sophos Security Manager is a Windows x64 desktop application for managing supported Sophos Firewall features through the XML API. It combines host/group workflows, network configuration, backup settings, application logging, automatic updates, continuous IPS/ATP Syslog collection, and an optional standalone desktop status widget.

Manager requires Windows Administrator approval at startup. If elevation is unavailable or the UAC request is cancelled, Manager closes without opening. The independent Widget does not require elevation merely to display status.

Current release: **1.3.64**

Supported firewall baseline: **Sophos Firewall 17.5 or later**

## Download

**[Download the latest self-contained Windows x64 installer](https://github.com/eslamifar/SophosSecurityManager-Releases/releases/latest)**

> **Version 1.3.64 includes a Windows service for threat logs and optional rule automation.** Setup creates and starts `SophosSecurityManagerThreatCollector`, configures it for automatic startup on UDP 514, and adds the required Windows Firewall rule. The service keeps collecting IPS/ATP Syslog events after the desktop application is closed. See [Threat Collector setup](#threat-collector-setup) before using the Threats tab.

## Main workspaces

### Home

- Configure the firewall host/IP, HTTPS port, username, password, group capacity, SSL verification, and inactivity timeout. The import target group is selected from the connected firewall's group list in Hosts > IPs.
- Open the entered Sophos Web Admin address directly, before or after saving settings.
- Verify the XML API connection and display hostname, model, negotiated API version, compatibility profile, and connected duration.
- Automatically disconnect after a configurable inactivity period.
- Show sequential initial-load progress for Hosts, MAC/FQDN, Network, Threats, and Manage.
- Disable operational workspaces until connection and their first successful data load; disable them again after disconnect.
- Check GitHub for updates at startup and from About.
- Use the compact icon dashboard for About, Help, Widget, Web Admin, Save Settings, and Connect/Disconnect; connection, update, and initial-load messages share the dedicated status row.
- Minimize Manager to the Windows notification area without disconnecting; double-click its tray icon or choose **Show Manager** to restore it, or choose **Exit** to close it. Launching the Manager shortcut again detects the existing elevated process before requesting UAC and restores that same window instead of starting another Manager.

### Hosts

- Import IPv4 addresses from a TXT file or an online source.
- Preview valid, unique, and invalid entries before changing Sophos.
- Run a dry-run analysis and use guarded Safe/Fast batch modes.
- Require backup confirmation before production import.
- Stop safely after the current batch and roll back the last successful batch when available.
- List, search, sort, create, update, and remove IP hosts and groups.
- Treat Sophos system-generated hosts (including interface and built-in `#`/`##` objects) as read-only even when they appear in the virtual inventory.
- Edit a selected IPv4 host or network while preserving its Sophos object name.
- Support individual IPv4 hosts and canonical IPv4 network objects in CIDR notation.
- Provide virtual `# ALL` and `# DUPLICATES` inventory views.
- Select multiple group members and choose whether to remove only their group membership or also delete their IP host objects. Multiple memberships are removed with one group update; optional host-object deletions continue individually and can be cancelled between API requests. Sophos may reject host deletion when another group or policy still references the object.

#### MAC subtab

- Load MAC host objects directly from Sophos.
- Search MAC objects by host name, MAC address, or description.
- Create or edit a named MAC host containing one address or a validated list of addresses separated by commas or new lines.
- Delete a selected MAC host when Sophos confirms it is not blocked by a dependent configuration.

#### FQDN subtab

- Load FQDN hosts and FQDN host groups directly from Sophos.
- Search the selected FQDN view or group by object name, address, or group membership.
- Filter the virtual FQDN inventory by all, grouped, or ungrouped membership and edit the selected FQDN while preserving its object name and group memberships.
- Provide `ALL` and `DUPLICATE` categorized views followed by the real FQDN groups.
- Accept either an FQDN or a complete URL; complete URLs are normalized to their hostname because Sophos stores FQDN values rather than URL paths.
- Create an independent FQDN object from `ALL`, or add it to the selected Sophos FQDN group.
- Create new FQDN groups, remove a selected object from a group without deleting it, and delete independent objects when they are not referenced.

### Network

- View interfaces, interface status, zones, IP assignment, addresses, netmasks/prefixes, gateway details, MTU, and admin state.
- Edit supported interface properties while keeping the physical interface identity read-only.
- View gateway connectivity with healthy/unhealthy visual status.
- Refresh gateway health automatically every minute while connected, updating the Gateways table and Widget from the same snapshot without reloading interfaces or routes.
- Fall back to interface configuration when dedicated gateway entities are unavailable, and preserve the last known Widget state when a refresh returns no gateways.
- Create, edit, and delete API-managed gateways when supported; interface-managed WAN gateways must be edited through their interface.
- View, create, edit, and delete supported static routes.
- Select interfaces, zones, IP families, and prefixes from validated lists.
- Availability and writable fields depend on the XML API capabilities exposed by the connected Sophos version.

### Threats

- Browse suspicious source IPs in separate All, Critical, Major, Moderate, and dynamically discovered historical severity tabs; standard severity tabs remain visible even when empty.
- Keep Severity in the All table; severity-specific tables replace that redundant column with the latest event's Destination IP immediately after Source IP.
- Show suspicious public source IPs collected from Sophos IPS and ATP Syslog events.
- Filter by **Critical**, **Major**, and optional **Moderate** severity and a configurable calendar-day period. Manager reads the firewall's configured time zone from its XML API at each connection, uses that zone to determine today's date, and preserves the calendar date carried by each Sophos event even when its reported UTC offset differs from Windows.
- Group repeated events by source IP and show attack count, latest threat, last-seen time, country, action, and interface.
- Use fixed severity colors in **All** (Critical red, Major yellow, Moderate blue); inside an individual severity tab use attack-count colors (3 or more red, 2 yellow, 1 blue), independent of sorting.
- Place the All and dynamic severity tabs above their Last days and Target group controls. Rules and Logs use the same top navigation without showing threat-only controls.
- Sort each threat inventory by Last seen descending initially; select any column header to switch sorting to that field and toggle ascending/descending order.
- Show attack-event counts in the Widget and every Threats tab, matching Sophos Reports. The Threats table still groups repeated events by IP and shows both event and unique-IP totals in its footer.
- Show whether each exact IP or containing CIDR network object already belongs to Sophos groups.
- Select one or more public IPs and add them to an existing Sophos IP group.
- Follow per-IP group-addition progress and cancel remaining operations after the current request finishes.
- Use the first Target group entry as an explicit selection prompt; adding selected IPs is blocked until a real Sophos group is selected.
- Review current timed Rule memberships, upcoming expirations, overdue entries, and recent expiration outcomes in **Memberships / Expiring**.
- Double-click an IP in a threat or membership table to view its complete stored attack history and Rule-automation activity.
- Chart daily Critical, Major, Moderate, and other threat-event trends over a selectable 1-365 day period in **Trends**.
- View the Windows collector service state and controls in Manage > Config.
- Verify that Sophos Log settings target this computer, use UDP 514, enable IPS/ATP forwarding, and use a severity threshold suitable for Moderate events.
- Distinguish explicit Sophos detection severity from Syslog transport priority. Legacy IPS records use `rule_priority` when detection severity is absent; transport priority remains the final fallback.
- Report received, stored, non-threat, missing-source, unknown-severity, and other rejected message counts, plus total stored events, unique source IPs, current-service accepted events, and last packet/threat times.

#### Threat Collector setup

The installer creates the `SophosSecurityManagerThreatCollector` Windows service, starts it automatically, and adds a Windows Firewall inbound rule for UDP 514. The service continues collecting while the desktop application is closed.

In Sophos Web Admin:

1. Open **System services > Log settings**.
2. Add or edit a Syslog server whose address is the management computer's Sophos-facing IP.
3. Set the port to **514** and the severity to **Information** if Moderate events are required.
4. Enable the Syslog destination for **IPS > Anomaly**, **IPS > Signatures**, and **Advanced threat protection > ATP events**.
5. Apply the settings. Setup restarts the collector during an upgrade; use Manage > Config if the diagnostic counters do not appear.

Important behavior:

- The Sophos Reports page reads historical data stored on the firewall; the collector only receives events forwarded after Syslog was configured and the service was running.
- Existing historical report rows cannot be backfilled through Syslog.
- Firewall, web, antivirus, and other non-IPS/ATP messages may be received but are counted as non-threat logs and are not added to the Threat IP table.
- Threat data is stored at `%ProgramData%\SophosSecurityManager\Threats\threats.jsonl`.
- Collector diagnostics are stored in the same Threats directory; limited rejected-message samples are written under `%ProgramData%\SophosSecurityManager\Logs`.

### Threat Rules

- Rules is followed by Logs in Threats. Set Order while adding or editing a Rule, or use Move up / Move down; the service evaluates enabled automatic Rules in that order. Logs shows Windows-service Rule activity with date and search filters.
- Rules act on threat source IPs and Sophos groups only; there are no email-action Rules yet.

### Manage

- Organize management features into **Backup / Restore**, **Device Power**, **Service**, and **Email** subtabs.
- View and control the Threat Collector Windows service from the Service subtab.
- Load existing Sophos backup settings.
- Configure and apply Local, Email, or FTP backup modes and Never/Daily/Weekly/Monthly schedules.
- Preserve stored Sophos encryption and FTP passwords unless a replacement is entered.
- Request an immediate configuration backup.
- Persist the latest successful in-app backup request and its Local, Email, or FTP type in the local widget state.
- Show only the backup date/time and Local, Email, or FTP type on the Widget's Last backup card.
- Treat Widget history as optional local metadata: a history write/read failure is logged and does not change a successful Sophos backup result.
- Refresh backup status every 10 seconds. After the FTP password is entered once, Manager protects it for the current Windows user and can count remote files and archive later FTP backups without requiring it again each session. Sophos itself does not return stored FTP passwords through XML API.
- Open the configured FTP destination in Windows Explorer without placing credentials in the URL.
- Restart, shutdown, direct backup download, and restore-file upload remain unavailable when they are not exposed by the supported XML API; use Sophos Web Admin for those operations.
- Create, edit, duplicate, enable/disable, and delete up to 10 locally stored threat rules.
- Keep the visibly selected Rule synchronized with Edit, Duplicate, Enable/Disable, Delete, and Preview / Apply actions after the grid refreshes.
- Match public source IPv4 addresses by attack count, selected Critical/Major/Moderate severities, and a rolling minute/hour/day window.
- Preview matching IPs and their current Sophos memberships before manually applying a rule to its target IP group.
- A Rule uses its own rolling time window, independent of Threats > Last days. If every match already belongs to the target group, Preview reports that no changes are needed and does not offer Apply.
- When no IP matches, explain the severity totals inside the selected rolling window and whether the configured attack threshold was reached.
- Keep existing group memberships intact, reuse existing host objects, and support permanent or expiring rule-created memberships.
- Process expired memberships after a successful connection or before applying another rule, without removing memberships still required by another rule.
- Optionally run enabled rules automatically in the Windows service whenever a matching-severity threat arrives.
- Use **Manage > Config > Set up automatic Rules** for a one-time administrator-approved connection test and protected service-credential setup. The form supports an IPv4/CIDR allowlist, per-minute change limit, and optional catch-up evaluation of stored threats within Rule windows.
- Max changes / minute delays excess queued additions until the next minute; the IP/CIDR allowlist excludes trusted addresses and networks; stored-threat catch-up is a one-time evaluation of events still inside enabled Rule windows.
- Create, edit, preview, manually apply, and enable automatic shared Rules from the elevated Manager. Both Manager and the Threat Collector use `%ProgramData%\SophosSecurityManager\Rules`.
- Rename a selected IP host group from Hosts > Groups using the Edit button or by double-clicking the group. Members are preserved, and matching Rule targets and tracked timed memberships are migrated to the new name.
- Service credentials and automation configuration stay protected under `%ProgramData%\SophosSecurityManager\Automation`. Once configured and unpaused, enabled automatic Rules run in the service without Manager elevation or an open Manager window. The Rule checkbox alone does not configure service credentials; use the setup button once. If shared Rules storage is unavailable, Manager falls back to personal manual-only Rules.
- Local Windows users with access to the shared Rules directory can change automatic Rules, which may cause the service to make Sophos changes using its configured credentials. Grant this access only on trusted workstations.
- Deduplicate queued IP/severity evaluations, avoid changing existing manual memberships, maintain expirations while Manager is closed, and pause automatically after three consecutive failures.
- Manage > Config shows the collector status, Start/Restart/Stop/Windows Services controls, the automatic-Rule setup button, and configurable folders for automatic-block CSV history and FTP backup copies. Automatic-block CSV rows include source and destination IPs; older rows receive a blank destination during schema migration. If Critical.csv, Major.csv, or Moderate.csv is moved or deleted, the running service recreates the missing file with the correct header within about ten seconds. Manager itself now requires Administrator approval at startup; automatic service execution continues independently after Manager closes.
- The Windows-service section now shows credential readiness and searchable per-IP automatic-Rule activity, including the exact expiry date and whether an expired IP was removed from its group or retained by another Rule. Last run in the Rules grid refreshes while the tab is open when the service updates a Rule.
- Service activity excludes manual Apply; it records automatic additions, failures, expiry removals, and matching IPs already managed or already in the target group. It remains empty until the service has automation credentials and checks a matching automatic Rule. The Last report timestamp is historical, not a live-error indicator.
- Filter Service activity to the last 24 hours, 7 days (default), 30 days, or all time. The grid displays at most the newest 100 matches and reads history newest-first.
- If an IP is already in the target group, automatic evaluation preserves its existing membership and duration. A Rule expiry is created only when that Rule actually adds the IP.
- After each successful IP addition, Manager reloads Sophos membership and refreshes every Threats severity tab; it also detects service-side additions and expiry removals while Threats or Manage > Config is open.
- Successful automatic service additions are appended to separate Critical, Major, and Moderate CSV files with block time, block-until time (or Permanent), source IP, Rule, and group. Existing tracked automatic memberships fill missing rows on their next evaluation, without duplicate CSV entries. Older CSV files are migrated and historical expiry values that cannot be reconstructed are marked Unknown. The folder is configured in Manage > Config.
- Before an immediate FTP backup, Manager requires the FTP password when no protected copy exists; cancelling the password prompt cancels the backup request. A successful request is copied from the FTP destination into the configured local archive folder, and the password is protected for the current Windows user for later copies.
- If Sophos or the FTP server still has the new backup file locked, Manager retries the local copy every five seconds for up to two minutes instead of failing on the first busy-file response.
- Manual Preview / Apply displays each IP's outcome as it happens; Cancel stops before the next IP after any in-flight request completes. A completed run shows OK and preserves partial results.
- Manage > Email saves SMTP host, port, encryption mode, authentication, sender, reply-to, default recipients, timeout, and an optional one-line site heading. Every Manager and Threat Collector email includes that heading and a footer identifying the Sophos URL, reported model, configured device address, API version, compatibility profile, and firewall time zone. The SMTP password is encrypted for the current Windows user.
- Choose the local delivery hour for the previous day's threat summary from 00:00 through 23:00; the Threat Collector sends it on its first check at or after that hour, once per day.
- Optionally email when an existing gateway changes between connected and disconnected states. Initial discovery does not generate an alert; the message includes the gateway identity, interface, previous/current state, raw Sophos status, and event time.
- Email headings combine the configured site name with the message title (and date where applicable) for daily summaries, backup results, and attack alerts.

### Logging

- View the latest application log with automatic refresh.
- Refresh manually, copy all displayed text, clear only the view, or open the log folder.
- Application logs are stored under `%LocalAppData%\SophosSecurityManager\logs`.
- Logs roll daily and the latest 14 daily files are retained.

### Desktop widget

- Run as an independent process from the main `SophosSecurityManager.UI.exe --widget` executable; closing Manager does not close the Widget.
- Show Manager connection heartbeat, gateway health, latest successful in-app backup and its type, and the number of threats received during the current local calendar day in a shorter four-card layout.
- Integrate Threat Collector health into the Threats card; when the service is stopped, show **Service is not running** in red instead of a threat count.
- Highlight disconnected gateways, notify when a gateway newly needs attention, and make every status card open Manager directly at Home, Network > Gateways, Manage > Backup / Restore, or Threats as appropriate. Opening Threats from the Widget selects Last days = 1 and runs Refresh.
- Color the latest backup green up to 10 days old, amber from more than 10 through 30 days, and red when older or unavailable.
- Refresh status every 10 seconds and show a Windows notification when a new threat arrives after the Widget starts.
- Open or close the Widget from Manager, and open or restore Manager from the Widget or its tray menu; controls follow the current process and window state.
- Restore an already-running Manager when it is hidden in the notification area instead of attempting to start another instance.
- Use an embedded multi-resolution Widget window/tray/shortcut icon, show it from the notification-area icon, close it explicitly, and prevent multiple Widget instances.
- Optionally start the Widget with Windows or launch it after Setup. Manager, Widget, and Uninstall shortcuts are grouped under Sophos Security Manager in the Start Menu; upgrades remove obsolete standalone shortcuts.

### Help

- Open the installed Microsoft Compiled HTML Help (`.chm`) file from Home.
- Browse categorized Home, Hosts (IPs, Groups, MAC and FQDN), Network, Threats, Manage, Logging, setup, and troubleshooting topics.
- Use the built-in table of contents, index, and full-text search.

## Updates

- The application checks the dedicated `SophosSecurityManager-Releases` repository.
- Update metadata comes directly from the latest GitHub Release tag and installer asset; no separate `version.json` manifest is used.
- Manager checks at startup and every hour while it remains open. When a newer version exists, the hourly check also displays the update prompt.
- The main title shows the installed version and keeps an update-available notice visible while a newer release exists.
- Versions are compared numerically, so `1.3.52` is newer than `1.3.51`.
- Downloads support progress, Pause/Resume, and Cancel.
- The update cache keeps only the installed version and the version being downloaded or installed. When no update is pending, it keeps the installed version and only the newest previous version. Active <code>.download</code> files are preserved, while older version folders are removed on a successful update check.
- The installer SHA-256 digest supplied by GitHub is verified before Setup starts.
- If in-app download or verification fails, the direct HTTPS release link is shown for browser download.
- When closing normally with Alt+F4 or the Close button, the application clears its connection state first. The Threat Collector service continues running.

For upgrades, close Manager and Widget, run the new Setup as Administrator, and keep the existing installation directory. Before replacing files, Setup must stop the installed Threat Collector and waits for it to finish; the upgrade is aborted if a safe stop cannot be confirmed. Setup then replaces and reconfigures the service for Automatic startup, restores the UDP 514 firewall rule, and starts the new service. After Setup completes, open Threats and confirm the collector reports Running.

## Requirements and initial configuration

- Windows x64 and administrator privileges for installation/service control.
- Sophos Firewall 17.5 or later.
- Enable XML API access in Sophos and allow the management computer's IP.
- Configure Syslog as described above to use the Threats workspace.
- Enter firewall-specific credentials locally after installation; redistributable builds do not include `appsettings.json`.

## Known API limitations

The application deliberately disables operations that the supported Sophos XML API does not expose reliably, including device restart/shutdown, downloading a locally stored backup, and uploading/restoring a backup file. Some gateway, interface, route, and backup fields vary by SFOS/API version and are enabled only when supported.

The integration layer keeps the official XML API, the official SFOS 22 REST API, and internal Web Admin Controller requests separate. Firmware/API discovery selects a `SophosCompatibilityProfile` and capability set for SFOS families through version 22; unknown firmware is handled with safe read-only probes. The internal `mode=55` IP-group rename request is version-scoped to its validated SFOS 17.5 profile. Diagnostic Web Admin request capture stores sanitized metadata only and must never be treated as a public, stable Sophos API contract.

## Release history and source

- [Latest release and release notes](https://github.com/eslamifar/SophosSecurityManager-Releases/releases/latest)
- [Complete changelog](https://github.com/eslamifar/SophosSecurityManager/blob/master/CHANGELOG.md)
- [Source-project documentation](https://github.com/eslamifar/SophosSecurityManager/blob/master/README.md)

Developed by **Mohsen Eslamifar**. This is an independent utility and is not affiliated with, endorsed by, or sponsored by Sophos.
