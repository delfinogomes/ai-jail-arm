# ai-jail-arm

Unofficial **Linux ARM64 (aarch64)** release builds of
[akitaonrails/ai-jail](https://github.com/akitaonrails/ai-jail).

Upstream publishes Linux x86_64 and macOS arm64 binaries. This repo fills the
Linux ARM gap: a scheduled GitHub Actions workflow checks upstream every 6
hours and, for any new release, builds the unmodified upstream tag natively
on an `ubuntu-22.04-arm` runner, runs upstream's test suite, and publishes a
release here with the same tag.

Not affiliated with or endorsed by the upstream project. Report ai-jail bugs
upstream. Report packaging and build problems here.

## Install

Grab the latest release from the
[Releases page](https://github.com/delfinogomes/ai-jail-arm/releases/latest).

```bash
# Debian / Ubuntu / Raspberry Pi OS (64-bit)
sudo apt install ./ai-jail_<version>_arm64.deb

# Any glibc >= 2.35 distro (install bubblewrap first)
tar xzf ai-jail-linux-aarch64.tar.gz
sudo install -m 0755 ai-jail /usr/local/bin/ai-jail
```

## Verify

Every artifact carries a GitHub build-provenance attestation:

```bash
gh attestation verify ai-jail-linux-aarch64.tar.gz -R delfinogomes/ai-jail-arm
```

Each release's notes also list the upstream commit it was built from and the
SHA256 checksums.

## Assets per release

| File | Description |
| --- | --- |
| `ai-jail-linux-aarch64.tar.gz` | stripped release binary |
| `ai-jail_<version>_arm64.deb` | Debian package made with upstream's `build-deb.sh`, arch set to arm64 |
| `*.sha256` | checksums |

## Manual runs

Go to **Actions → Release ARM64 → Run workflow**. You can set:

- `tag`: build a specific upstream tag (e.g. `v3.4.2`)
- `force`: rebuild and replace assets on an existing release
- `skip_tests`: skip `cargo test`

## Notes

- Only aarch64 is built. 32-bit ARM (armv7) is not supported because
  upstream's seccomp dependency (`seccompiler`) only targets x86_64, aarch64,
  and riscv64.
- GitHub disables scheduled workflows in repos with no activity for 60 days.
  If releases stop showing up, re-enable the workflow in the Actions tab.

## License

ai-jail is GPL-3.0-only. The binaries here are built from upstream source
without modification. See the
[upstream repository](https://github.com/akitaonrails/ai-jail) for source and
license text.
