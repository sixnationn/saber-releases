# Saber — releases

Installers for [Saber](https://github.com/sixnationn/saber) and the `latest.yml`
its auto-updater reads. Nothing else: the source, the changelog and the issues
live in the main repository.

## Why this repository exists

Saber's source is private, and a private repository's release assets are not
readable without a credential — `releases.atom`, the API and the asset URLs all
answer 404 to an anonymous request. A shipped installer has no token and must not
have one, so every update check any released Saber has ever made returned 404.

That failed silently, on every launch, because the launch-time check discarded its
errors: a permanently broken updater looked exactly like one with nothing to do.
Saber 0.9.2 changed that — three consecutive background failures now say so once,
and the last error is reported in `Help: Diagnostics` — and this repository is the
other half of the fix. Binaries are public; source stays private.

## What is here

Each release carries three files:

| File | Purpose |
|---|---|
| `Saber-Setup-<version>.exe` | The Windows installer |
| `latest.yml` | The version, size and SHA-512 the updater reads |
| `Saber-Setup-<version>.exe.blockmap` | Lets the updater fetch only changed blocks |

`latest.yml` is the file that matters. Without it, an installed copy cannot find a
new version however many installers sit beside it.
