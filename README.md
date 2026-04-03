# AUR Deploy

A GitHub Action to deploy packages to the Arch User Repository (AUR).

Automatically extracts the version from the release tag, computes the source tarball SHA256, updates your PKGBUILD and .SRCINFO templates, and pushes to AUR.

## Usage

```yaml
name: Publish to AUR

on:
  release:
    types: [published]

jobs:
  aur-publish:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: rursache/aur-deploy@v2
        with:
          pkgname: my-package
          pkgbuild: aur/PKGBUILD
          srcinfo: aur/.SRCINFO
          commit_username: "Your Name"
          commit_email: "you@example.com"
          ssh_private_key: ${{ secrets.AUR_SSH_PRIVATE_KEY }}
```

That's it. No need to manually extract versions, compute checksums, or generate .SRCINFO — the action handles all of it.

## How it works

1. Extracts the version from the Git tag (strips `v` prefix: `v1.2.3` → `1.2.3`)
2. Downloads the source tarball from GitHub and computes its SHA256 checksum
3. Copies your PKGBUILD and .SRCINFO templates and updates:
   - `pkgver=` line in PKGBUILD
   - `sha256sums=` line in PKGBUILD
   - All version references in .SRCINFO
   - `sha256sums` in .SRCINFO
4. Pushes the updated files to AUR via SSH

## Template setup

Your repo needs a PKGBUILD and .SRCINFO with placeholder values. The action will replace the version and checksums automatically.

### PKGBUILD template

Use any version (e.g. `0.0.0`) and `SKIP` for the checksum — they'll be replaced:

```bash
# Maintainer: Your Name <you@example.com>
pkgname=my-package
pkgver=0.0.0
pkgrel=1
pkgdesc="My package description"
arch=('any')
url="https://github.com/user/my-package"
license=('MIT')
depends=('bash')
source=("${pkgname}-${pkgver}.tar.gz::https://codeload.github.com/user/my-package/tar.gz/v${pkgver}")
sha256sums=('SKIP')

package() {
    cd "$srcdir/my-package-${pkgver}"
    make DESTDIR="$pkgdir" install
}
```

### .SRCINFO template

Same placeholder version and checksum. **Important**: use the same placeholder version consistently — the action replaces all occurrences:

```
pkgbase = my-package
	pkgdesc = My package description
	pkgver = 0.0.0
	pkgrel = 1
	url = https://github.com/user/my-package
	arch = any
	license = MIT
	depends = bash
	source = my-package-0.0.0.tar.gz::https://codeload.github.com/user/my-package/tar.gz/v0.0.0
	sha256sums = SKIP

pkgname = my-package
```

> **Note**: .SRCINFO uses tab indentation (not spaces). Make sure your editor preserves tabs.

## Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `pkgname` | Yes | — | AUR package name |
| `pkgbuild` | Yes | — | Path to PKGBUILD template |
| `srcinfo` | Yes | — | Path to .SRCINFO template |
| `commit_username` | Yes | — | Git commit author name |
| `commit_email` | Yes | — | Git commit author email |
| `ssh_private_key` | Yes | — | SSH private key for AUR access |
| `commit_message` | No | `Update to vX.Y.Z` | Custom commit message |

## Prerequisites

1. [Register on AUR](https://aur.archlinux.org/register) and create your package
2. Add your SSH public key to your [AUR account](https://aur.archlinux.org/account)
3. Add the private key as `AUR_SSH_PRIVATE_KEY` in your repo's GitHub Secrets

## License

MIT
