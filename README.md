# Sophos Security Manager

[![Latest release](https://img.shields.io/github/v/release/eslamifar/SophosSecurityManager-Releases?display_name=tag&sort=semver)](https://github.com/eslamifar/SophosSecurityManager-Releases/releases/latest)
[![Platform](https://img.shields.io/badge/platform-Windows%20x64-0078D6)](https://github.com/eslamifar/SophosSecurityManager-Releases/releases/latest)

Sophos Security Manager is a Windows desktop application for safely importing and managing large IPv4 threat-intelligence lists on Sophos Firewall through the Sophos XML API.

> This repository contains public release files only. The application source code is maintained in a separate private repository.

## Download the latest version

**[Download the latest Sophos Security Manager release](https://github.com/eslamifar/SophosSecurityManager-Releases/releases/latest)**

This link always opens the newest published release. The application also checks this repository for updates and can download and launch a newer installer.

Current update metadata: [version.json](https://github.com/eslamifar/SophosSecurityManager-Releases/blob/main/version.json)

Current version: **1.2.4**

## What the application does

- Connects to Sophos Firewall through the XML API.
- Negotiates the available XML API version and selects a compatibility profile for SFOS 17.5, 18/19, 20, 21, and newer releases.
- Reads IPv4 addresses from a local TXT file or an online source.
- Displays valid, duplicate, and invalid entries.
- Runs Dry Run / Analyze before production changes.
- Detects existing Sophos host objects and avoids duplicates.
- Creates capacity-aware groups such as `Afta-001` and `Afta-002`.
- Uses the exact base group name when the entire list fits in one group.
- Fills free slots in existing managed groups before creating new groups.
- Imports in controlled batches with progress, elapsed time, safe stop, and last-batch rollback.
- Offers **Safe** mode for maximum verification and **Fast** mode with cached inventory and periodic synchronization for large imports.
- Can create managed host names such as `groupAfta-001-1.2.3.4`.
- Refreshes group information during long imports.
- Shows groups, member counts, host names, and addresses.
- Provides an `ALL` view for grouped and independent IP host objects.
- Searches and filters grouped or independent host objects and shows group membership in the `ALL` view.
- Displays inventory-health information before management operations.
- Creates a JSON safety snapshot before import and exports operation reports in CSV and JSON formats.
- Supports safe single-host and group management while import is stopped.
- Records operations and errors for troubleshooting.
- Prevents multiple application instances.
- Disconnects from Sophos safely before exit.
- Organizes the main workspace into `Home`, `IPs`, `Groups`, `Manage`, and `Logging` tabs.
- Schedules an immediate encrypted or unencrypted local Sophos configuration backup through the supported XML API.
- Provides guarded Web Admin handoffs for device restart, shutdown, and configuration restore where the public XML API does not expose a supported operation.
- Displays detailed rolling diagnostic logs inside the application, including sanitized Sophos API response details.
- Keeps unsupported restart, shutdown, and restore controls disabled on SFOS versions where the public XML API does not expose them.
- Shows where locally created firewall backups can be listed, downloaded, or deleted safely in Sophos Web Admin.

## Safety features

1. Sophos must be connected before management features are enabled.
2. Dry Run / Analyze must finish before import starts.
3. The user must confirm that a Sophos configuration backup exists.
4. Imports run in limited batches.
5. Stop waits for the current batch to finish safely.
6. Failed batches are rolled back when possible.
7. Group capacity is enforced.
8. Duplicate and existing host objects are detected before creation.
9. Destructive controls are disabled during automatic import.

> Always create and verify a Sophos configuration backup before importing or deleting objects.

## Requirements

- Windows 10 or Windows 11, 64-bit
- A supported Sophos Firewall with the XML API enabled
- Network access to the Sophos API HTTPS port
- A Sophos administrator account with the required permissions

The installer is self-contained; a separate .NET runtime is not normally required.

## Installation and updates

1. Open the [latest release](https://github.com/eslamifar/SophosSecurityManager-Releases/releases/latest).
2. Download `SophosSecurityManager-Setup-<version>-win-x64.exe`.
3. Run Setup and approve elevation if Windows requests it.
4. Existing installations are detected as update or repair operations and retain their installation path.

Versions affected by the earlier updater file-lock issue may require one manual update to version 1.1.5 or later. Later supported versions can use the application's update checker.

## Publishing a new version

Update information is stored in [version.json](https://github.com/eslamifar/SophosSecurityManager-Releases/blob/main/version.json).

1. Publish the installer as a GitHub Release asset.
2. Mark the release as **Latest**.
3. Update `version.json` with the version and installer filename.

The README download link and version badge use GitHub's `releases/latest` endpoint, so they update automatically whenever a new latest release is published.

## Contact

Developed by **Mohsen Eslamifar**.

- [LinkedIn](https://www.linkedin.com/in/eslamifar)
- [GitHub](https://github.com/eslamifar)

## Disclaimer

This is an independent management utility and is not affiliated with, endorsed by, or sponsored by Sophos. Sophos and Sophos Firewall are trademarks of their respective owner.
