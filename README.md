# Sophos Security Manager

[![Latest release](https://img.shields.io/github/v/release/eslamifar/SophosSecurityManager-Releases?display_name=tag&sort=semver)](https://github.com/eslamifar/SophosSecurityManager-Releases/releases/latest)
[![Platform](https://img.shields.io/badge/platform-Windows%20x64-0078D6)](https://github.com/eslamifar/SophosSecurityManager-Releases/releases/latest)

Sophos Security Manager is a Windows x64 application for managing supported Sophos Firewall features through the XML API and continuously collecting IPS/ATP threat events through Syslog.

Current version: **1.3.45**

## Download

**[Download the latest Sophos Security Manager installer](https://github.com/eslamifar/SophosSecurityManager-Releases/releases/latest)**

The installer is self-contained; a separate .NET runtime is normally not required.

## Important: Threat Collector service installation

Version 1.3.45 includes a Windows service named:

```text
SophosSecurityManagerThreatCollector
```

Setup performs these operations automatically with administrator permission:

1. Copies the collector into the application's `ThreatCollector` directory.
2. Creates/configures it as an **Automatic** Windows service.
3. Starts the service on UDP port **514**.
4. Adds an inbound Windows Firewall rule named **Sophos Security Manager Syslog** for UDP 514.
5. During upgrades, stops the service and waits for complete shutdown before replacing files; Setup aborts safely if shutdown cannot be confirmed.
6. Removes the service and firewall rule during uninstall.

The service runs independently of the desktop application. Closing Sophos Security Manager does **not** stop threat collection.

After installation, open **Manage > Service** to see the service state and uptime or use its **Start service**, **Restart service**, **Stop service**, and **Windows Services** controls. Collector diagnostics remain in Threats.

## Configure Sophos for Threats

The service cannot receive threat data until Sophos forwards Syslog events to this computer.

1. In Sophos Web Admin, open **System services > Log settings**.
2. Add or edit a Syslog server.
3. Set **Server IP** to this Windows computer's IP as seen from the Sophos network.
4. Set **Port** to `514`.
5. Select **Information** as the severity threshold when Moderate events are required.
6. In the Syslog destination column, enable:
   - **IPS > Anomaly**
   - **IPS > Signatures**
   - **Advanced threat protection > ATP events**
7. Apply the Sophos settings.

The application checks the target IP, port, IPS/ATP selections, and severity when it loads Threats. If the XML API cannot verify these settings, it reports that verification was unavailable instead of showing a false definitive warning.

## Threats tab

### Threat IP table

- Separate **All**, **Critical**, **Major**, **Moderate**, and dynamically discovered historical severity tabs show attack-event counts, matching the Widget and Sophos Reports; standard tabs remain visible when empty.
- Uses a configurable calendar-day lookback period from 1 to 365 days. Manager reads the connected firewall's time zone from its XML API on every connection and uses its date for Threats and Widget day filters, while preserving the calendar date carried by each Sophos event even if its reported UTC offset differs from Windows.
- Groups repeated events by public source IP.
- Shows both attack-event and unique source-IP totals in the Threats footer; eight events from seven IPs give **All (8)** and seven table rows.
- Shows severity, source IP, attack count, latest threat/signature, last-seen time, country, action, and interface.
- Shows Sophos group membership for exact IP hosts and containing CIDR network objects.
- Highlights Critical, Major, and Moderate rows with different colors.
- Lets the user select one or more public source IPs and add them to an existing Sophos IP group.
- Rejects private or invalid addresses from the add-to-group action.

### Collector service controls (Manage > Service)

- Displays the exact Windows service name.
- Shows Running/Stopped state, UDP 514 status, and running duration.
- Supports Start, Restart, and Stop with administrator elevation.
- Opens the Windows Services console for manual inspection.

### Collector report

The lower report table explains what the service has received:

- **Packets received:** all Syslog datagrams reaching the service and the time of the last packet.
- **Threat events stored:** total events currently stored, unique source IPs, events accepted during the current service run, and the time of the latest stored threat.
- **Non-threat logs ignored:** firewall, web, antivirus, system, and other messages that are intentionally not shown as Threat IPs.
- **Missing source IP:** IPS/ATP messages without a recognized source-address field.
- **Unknown severity:** IPS/ATP messages whose severity cannot yet be mapped.
- **Other rejected:** remaining rejected messages and the latest reason.
- **Data file:** the JSONL file used by the desktop application.

Explicit Sophos detection/signature severity takes precedence over Syslog message priority. When detection severity is absent, IPS `Warning` transport priority is treated as Moderate because transport priority does not necessarily represent attack severity. Compatible legacy IPS records previously stored as Major are normalized when read. Common Sophos values such as `Medium`, `Information`, `Informational`, `Info`, `Notice`, and `Notification` are mapped to Moderate. Alternate source/destination, signature, component, and Intrusion Prevention field names are also recognized.

### Live collection versus Sophos historical reports

Sophos Web Admin Reports reads historical records stored on the firewall. The Windows collector receives only events forwarded **after** Syslog was configured and the service was running.

- Existing historical report rows cannot be downloaded or backfilled through Syslog.
- If an attack occurred before service installation, it will remain visible in Sophos Reports but will not appear in the application.
- A new IPS/ATP event must be generated or received to validate end-to-end collection.
- Receiving many Firewall Rule messages with zero saved threats means networking is working, but no new IPS/ATP event has arrived yet.

### Threat data and diagnostics

- Threat events: `%ProgramData%\SophosSecurityManager\Threats\threats.jsonl`
- Collector status: `%ProgramData%\SophosSecurityManager\Threats\collector-status.json`
- Collector errors/rejected samples: `%ProgramData%\SophosSecurityManager\Logs`

Rejected-message samples are rate-limited to avoid excessive disk usage. Collector status is also throttled while preserving immediate writes for accepted threats.

## Other workspaces

### Home

- Configures the Sophos host/IP, HTTPS port, credentials, base group, capacity, SSL verification, and inactivity disconnect.
- Provides a compact icon dashboard for About, Help, Widget, Web Admin, Save Settings, and Connect/Disconnect, with a dedicated status row for connection, loading, and update progress.
- Opens the currently entered Sophos Web Admin address without requiring settings to be saved first.
- Uses 15 minutes as the default inactivity disconnect period, with Never disconnect, 5, 10, 15, and 30 minute choices.
- Displays firewall/API information and sequential load status.
- Keeps Hosts, Network, Threats, and Manage disabled until connection and initial loading complete.
- Disables them again after disconnect.
- Opens or closes the standalone Widget and updates the button label to match its running state.
- Minimizing Manager hides it to the notification area without disconnecting; double-click or Show Manager restores it, and Exit closes it completely.

### Hosts

- Imports IPv4 intelligence from TXT files or online sources.
- Previews valid/invalid/duplicate entries and runs a Dry Run before changes.
- Supports guarded Safe/Fast batch import, stop-after-batch, and last-batch rollback.
- Creates, searches, sorts, updates, and removes supported IP hosts, CIDR network objects, and groups.
- Provides `# ALL` and `# DUPLICATES` inventory views.
- Includes MAC-host creation and editing with validated single or multi-address input and duplicate detection.
- Includes FQDN hosts and groups, URL-to-hostname normalization, membership filtering, and safe removal from groups.

### Network

- Lists and edits supported interface properties.
- Displays gateway health and protects interface-managed WAN gateways.
- Refreshes gateway health every hour while connected and updates both the Network table and Widget without reloading interfaces or routes.
- Creates, edits, and deletes API-managed gateways where supported.
- Lists and manages supported static routes with validated interface, prefix, and IP-family selections.

### Manage

- Organizes management features into **Backup / Restore**, **Device Power**, **Rules**, and **Service** subtabs.
- Loads and applies Local, Email, and FTP backup settings and schedules.
- Preserves stored Sophos passwords unless replacements are entered.
- Requests immediate configuration backups.
- Opens the configured FTP destination in Windows Explorer without placing credentials in the URL.
- Creates, edits, duplicates, enables/disables, and deletes up to 10 locally persisted threat rules.
- Matches public source IPv4 addresses by attack count, severity, and a rolling minute/hour/day window.
- Previews matching IPs and current Sophos memberships before manually applying a rule to its target group.
- Shows each IP's outcome in a progress window during manual Apply. Cancel stops before the next IP after the current request finishes; OK appears at completion and partial results are retained.
- Reuses existing Host objects, preserves unrelated memberships, and supports permanent or expiring rule-created memberships while coordinating overlapping rules.
- Supports opt-in automatic Rule execution by the Windows service, including encrypted machine-scoped credentials, IPv4/CIDR allowlists, change limits, IP/severity deduplication, and audit history.
- Maintains expiring rule-created memberships while Manager is closed, preserves pre-existing manual memberships, and pauses automation after three consecutive failures.
- Stores shared Rules in `%ProgramData%\SophosSecurityManager\Rules`, where a normal Manager session can create, edit, preview, apply, and enable automatic Rules. The Threat Collector reads the same Rules even after Manager closes.
- Keeps service credentials and automation configuration protected in `%ProgramData%\SophosSecurityManager\Automation`. Already-configured, unpaused automatic Rules can run in the service while Manager is closed. Checking the automatic Rule option does not itself configure credentials; on a fresh installation without service credentials, automation will not run. Setup migrates previous shared Rules.
- If the shared Rules directory is inaccessible, Manager falls back to personal manual-only Rules instead of failing the Sophos connection. Local users who can edit shared Rules can cause service-driven Sophos changes, so use this feature on trusted workstations.
- Manage > Service contains two sections on the same subtab: Windows collector/automatic-Rule activity and Email server settings. The Windows section shows service state, credential readiness, and a searchable per-IP activity history with addition, scheduled expiry, removal, and failure results. Start, Restart, Stop, and Windows Services appear below the service status. Credential setup, test, and pause/resume controls remain hidden.
- The Rules grid refreshes while open, so Last run updates made by the service become visible without reopening the subtab. For a 180-day Rule, expiry is measured from the time the IP is added; the service removes its Rule-created group membership after expiry when able to connect. It keeps the Sophos Host object and any membership still required by another Rule.
- Email server settings include SMTP host, port, None/STARTTLS/SSL-TLS encryption, authentication, sender name/address, reply-to, default recipients, and timeout. The password is encrypted for the current Windows user. These settings are saved for a future feature; version 1.3.45 does not send email.

### Logging

- Displays the latest application log with automatic refresh.
- Supports Refresh, Copy all, Clear view, and Open log folder.
- Application logs are stored at `%LocalAppData%\SophosSecurityManager\logs`.
- Logs roll daily and the latest 14 daily files are retained.

### Desktop Widget

- Runs independently using `SophosSecurityManager.UI.exe --widget`; Setup does not install a duplicate standalone Widget runtime.
- Uses a shorter four-card layout for connection heartbeat, gateway health, latest backup, and attack events on the connected firewall's calendar date, including events whose reported UTC offset differs from Windows.
- Integrates collector health into the Threats card; when the Windows service is stopped, the card shows **Service is not running** in red instead of a potentially misleading threat count.
- Highlights disconnected gateways, notifies when a gateway newly needs attention, and makes every status card open Manager directly at Home, Network > Gateways, Manage > Backup / Restore, or Threats as appropriate. Opening Threats from the Widget selects Last days = 1 and runs Refresh.
- Uses green for backups up to 10 days old, amber for more than 10 through 30 days, and red for older or unavailable backup state.
- Opens Manager or restores its notification-area-hidden window without launching a duplicate process; starting Manager again also restores its existing window.
- Uses an embedded multi-resolution Widget window, tray, and shortcut icon, and supports an explicit close control, Always on top, new-threat notifications, single-instance protection, and optional Windows startup.
- Groups Manager, Widget, and Uninstall shortcuts under Sophos Security Manager in the Start Menu; upgrades remove obsolete standalone shortcuts and use a dedicated uninstall icon.

### Help

- Opens an installed English Microsoft Compiled HTML Help (`.chm`) file from Home.
- Provides categorized Home, Hosts (IP, Group, MAC and FQDN), Network, Threats, Manage, Logging, Widget, installation, setup, and troubleshooting topics.
- Includes a table of contents, index, and full-text search.

## Installation and updates

1. Open the [latest release](https://github.com/eslamifar/SophosSecurityManager-Releases/releases/latest).
2. Download `SophosSecurityManager-Setup-<version>-win-x64.exe`.
3. Run Setup and approve administrator elevation.
4. Setup detects an existing installation and performs an update or repair in the same directory.

The Setup executable uses the same high-contrast icon as Sophos Security Manager. Upgrading to version 1.3.24 or later removes the obsolete standalone `Widget` directory; Widget shortcuts now launch the main executable in independent Widget mode.

Release builds protect all first-party Manager, Widget, API, Core, Infrastructure, Models, and Services assemblies consistently. Before Setup is produced, the release builder launches both packaged Manager and Widget modes with strict startup error handling; either mode failing its smoke test stops the release build.

The application checks this repository's GitHub Releases API for new releases. The latest release tag and installer asset are authoritative; no separate `version.json` manifest is used. Downloads include progress, Pause/Resume, Cancel, SHA-256 verification, and a direct browser link if the in-app download fails.

When closing normally, the desktop application clears its XML API state first. The XML API is request-based and does not keep a persistent authenticated session; the independent Threat Collector remains running.

## Requirements

- Windows 10 or Windows 11 x64
- Sophos Firewall 17.5 or later
- Sophos XML API enabled and this computer's IP allowed
- Network access to the Sophos HTTPS API port
- Administrator permission for installation and service control
- Sophos Syslog configuration described above for the Threats tab

## Supported API limitations

The supported XML API does not reliably expose every Web Admin operation. Device restart/shutdown, direct download of a local firewall backup, and upload/restore of a backup file remain disabled and must be completed in Sophos Web Admin. Writable interface, gateway, route, and backup fields can also vary by SFOS/API version.

## Safety

- Management features remain disabled until Sophos is connected and data is loaded.
- Import requires analysis and backup confirmation.
- Imports run in limited batches with safe-stop and rollback support.
- Group capacity and duplicate/existing objects are checked.
- Destructive controls are disabled during automatic import.

Always create and verify a Sophos configuration backup before importing or deleting objects.

## Release history and contact

- [Latest release and release notes](https://github.com/eslamifar/SophosSecurityManager-Releases/releases/latest)
- [Complete changelog](https://github.com/eslamifar/SophosSecurityManager/blob/master/CHANGELOG.md)
- [Source-project documentation](https://github.com/eslamifar/SophosSecurityManager/blob/master/README.md)

Developed by **Mohsen Eslamifar**.

- [LinkedIn](https://www.linkedin.com/in/eslamifar)
- [GitHub](https://github.com/eslamifar)

## Disclaimer

This is an independent management utility and is not affiliated with, endorsed by, or sponsored by Sophos. Sophos and Sophos Firewall are trademarks of their respective owner.
