# AUR Deploy

A GitHub Action to deploy packages to the Arch User Repository (AUR).

## Usage

```yaml
- uses: rursache/aur-deploy@v1
  with:
    pkgname: my-package
    pkgbuild: ./PKGBUILD
    srcinfo: ./.SRCINFO
    commit_username: "My Name"
    commit_email: "me@example.com"
    ssh_private_key: ${{ secrets.AUR_SSH_PRIVATE_KEY }}
    version: "1.2.3"  # optional
```

## Inputs

| Input | Required | Description |
|---|---|---|
| `pkgname` | Yes | AUR package name |
| `pkgbuild` | Yes | Path to PKGBUILD file |
| `srcinfo` | Yes | Path to .SRCINFO file |
| `commit_username` | Yes | Git commit author name |
| `commit_email` | Yes | Git commit author email |
| `ssh_private_key` | Yes | SSH private key for AUR access |
| `commit_message` | No | Commit message (default: "Update package") |
| `version` | No | Version string, used in default commit message |

## License

MIT
