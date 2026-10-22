# PHP 8.1.34 for Windows with CVE-2026-109618 / CVE-2026-109619 fix

Unofficial build. PHP 8.1 is EOL, so php.net published only a source backport (branch `PHP-8.1-CVE-2026-109618-109619`, php-src commit `1d7dec1`) and no Windows binaries. These are built from that exact commit with the official `php/php-windows-builder` (VS2019 / vs16, PGO, same configure flags as windows.php.net).

## Which file

| File | Use |
|---|---|
| `php-8.1.34-nts-Win32-vs16-x64.zip` | IIS, FastCGI, CLI |
| `php-8.1.34-Win32-vs16-x64.zip` | Apache mod_php (thread-safe) |
| `*-x86.zip` | Same, 32-bit |
| `php-debug-pack-*` | PDB debug symbols |
| `php-devel-pack-*` | SDK for building extensions |

Needs the VC++ 2015-2022 Redistributable.

## Notes

- The build still reports `PHP 8.1.34`, same as the unpatched official build. Use the SHA-256 shown next to each file on the release page to tell them apart.
- Existing 8.1 vs16 extension DLLs should still load: the patch adds functions but changes no public struct layout.
- PHP 8.1 gets no further fixes. Upgrade to a supported version (8.2+) when you can.

## Rebuild

Actions > php-8.1-cve-windows > Run workflow. It builds all 4 variants from the pinned commit, checks each binary contains the fix, and publishes the release.
