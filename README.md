# ai-jail-arm

Unofficial **Linux ARM64 (aarch64)** release builds of
[akitaonrails/ai-jail](https://github.com/akitaonrails/ai-jail).

Upstream publishes Linux x86_64 and macOS arm64 binaries. This repo fills the
Linux ARM gap: a scheduled GitHub Actions workflow checks upstream every 6
hours and, for any new release, verifies the upstream tag, builds that exact
commit unmodified on a native `ubuntu-22.04-arm` runner, runs upstream's test
suite, and (after manual approval) publishes a release here with the same tag.

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
gh attestation verify ai-jail-linux-aarch64.tar.gz -R delfinogomes/ai-jail-arm \
  --signer-workflow delfinogomes/ai-jail-arm/.github/workflows/release-arm.yml
```

The upstream tag, commit, and signing key are recorded in a `BUILD-INFO` file
inside the tarball and at `/usr/share/doc/ai-jail/BUILD-INFO` in the .deb.
The attestation covers the artifact digest, so it covers that file too.

## Trust chain

1. **Verify** (no upstream code runs): the upstream tag must
   - be an annotated tag,
   - be reachable from upstream's default branch,
   - match the `Cargo.toml` version,
   - be GPG-signed by a key pinned in [`keys/`](keys/) of *this* repo, not
     the upstream keyring, so a compromised upstream can't swap keys,
   - point to the same commit that upstream's own release attestation
     (`akitaonrails/ai-jail/.github/workflows/release.yml`) records for its
     x86_64 build.
2. **Build**: runs on the verified commit SHA rather than the tag, so a moved
   tag can't swap the source. The job has a read-only token and no secrets.
3. **Release**: runs in the `release-publish` environment, which needs manual
   approval. It attests the artifacts and creates the release. Releases are
   never overwritten. A published tag is never rebuilt, and only leftover
   drafts from interrupted runs are replaced.

### When upstream rotates its signing key

Verification fails closed. To trust the new key, check its fingerprint
through a separate channel, then add it to `keys/` and `keys/fingerprints.txt`.
The current key expires 2028-08-15.

## Assets per release

| File | Description |
| --- | --- |
| `ai-jail-linux-aarch64.tar.gz` | stripped release binary |
| `ai-jail_<version>_arm64.deb` | Debian package made with upstream's `build-deb.sh`, arch set to arm64 |
| `*.sha256` | checksums |

## Manual runs

Go to **Actions → Release ARM64 → Run workflow**. You can set:

- `tag`: build a specific upstream tag (e.g. `v3.4.2`)
- `skip_tests`: skip `cargo test`

Each run pauses before publishing until someone approves the `release-publish`
deployment. GitHub emails the required reviewers.

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
