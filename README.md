# Elva Labs Scoop bucket

Scoop manifests for Elva Labs applications.

## Availability

`awsesh-desktop` installs the native [Sesh desktop client](https://github.com/elva-labs/awsesh)
on Windows 11 x64. The application is unsigned, so Windows may display a
SmartScreen warning.

## Installation

Run these commands in PowerShell with [Scoop](https://scoop.sh) installed:

```powershell
scoop bucket add elva https://github.com/elva-labs/scoop-elva
scoop install elva/awsesh-desktop
```

Launch Sesh from the Start menu or with `awsesh-desktop`.

Close Sesh before updating or uninstalling:

```powershell
scoop update
scoop update awsesh-desktop
scoop uninstall awsesh-desktop
```

Uninstalling removes the application, not the AWS credentials or shared awsesh
configuration stored outside the installation directory.

## Manifest maintenance

The package manifest is
[`bucket/awsesh-desktop.json`](bucket/awsesh-desktop.json). It downloads
`awsesh-desktop-win32-x64.zip` from published stable releases and verifies its
SHA-256 checksum. When changing the manifest manually, verify the downloaded
ZIP against the release's `SHA256SUMS`. Do not modify existing release tags or
assets.

Validate installation and launch on Windows 11 x64 after packaging changes.
Check updates between versions and uninstall without removing shared
configuration.

## Automatic updates

[ScoopInstaller/GithubActions](https://github.com/ScoopInstaller/GithubActions)
checks manifests every four hours and can also be run manually through GitHub
Actions. It commits updated versions, download URLs, and checksums to the
default branch using this repository's `GITHUB_TOKEN`. No additional secret or
cross-repository token is required.

The manifest's GitHub `checkver` setting follows stable releases only; beta
releases are ignored. Automatic updates read the release's `SHA256SUMS` for the
new Windows ZIP checksum. If branch protection prevents direct commits,
configure the updater to submit pull requests instead.
