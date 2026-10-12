# Elva Labs Scoop bucket

Scoop manifests for Elva Labs applications.

## Availability

The `awsesh-desktop` manifest will be added after the first stable Windows
desktop release of [awsesh](https://github.com/elva-labs/awsesh). The update
workflow skips execution until a manifest is present.

## Installation

Once the manifest is available, run these commands in PowerShell with
[Scoop](https://scoop.sh) installed:

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

## Adding the first manifest

Use a new published stable release containing both
`awsesh-desktop-win32-x64.zip` and `SHA256SUMS`. Do not modify existing release
tags or assets. Version `1.1.3` does not contain the Windows desktop package.

These commands require Bun, the GitHub CLI, and a sibling `../awsesh` checkout
containing the merged Windows support. Run them from this repository on macOS,
replacing `1.1.4` with the first published Windows desktop version:

```sh
(
  set -e
  version=1.1.4
  directory=$(mktemp -d)
  gh release download "v$version" --repo elva-labs/awsesh \
    --pattern awsesh-desktop-win32-x64.zip --pattern SHA256SUMS \
    --dir "$directory"
  sha256=$(awk '$2 == "awsesh-desktop-win32-x64.zip" { print $1 }' "$directory/SHA256SUMS")
  printf '%s  %s\n' "$sha256" "$directory/awsesh-desktop-win32-x64.zip" | shasum -a 256 -c -
  bun run ../awsesh/packages/desktop/script/scoop.ts "$version" "$sha256" \
    > "$directory/awsesh-desktop.json"
  mkdir -p bucket
  cp "$directory/awsesh-desktop.json" bucket/awsesh-desktop.json
)
```

Validate installation and launch on Windows 11 x64 before committing the
manifest. Check an update between versions and uninstall without removing
shared configuration. The application is initially unsigned, so Windows may
display a SmartScreen warning.

## Automatic updates

[ScoopInstaller/GithubActions](https://github.com/ScoopInstaller/GithubActions)
checks manifests every four hours and can also be run manually through GitHub
Actions. It commits updated versions, download URLs, and checksums to the
default branch using this repository's `GITHUB_TOKEN`. No additional secret or
cross-repository token is required.

Keep `awsesh-desktop` on stable releases using its GitHub `checkver` setting.
For releases that publish `SHA256SUMS`, the manifest can use the existing
checksums by adding this property inside `autoupdate`:

```json
"hash": {
  "url": "$baseurl/SHA256SUMS"
}
```

Without this property, Scoop's updater downloads the ZIP and calculates its
checksum. If branch protection prevents direct commits, configure the updater
to submit pull requests instead.
