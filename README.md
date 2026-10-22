# PHP 8.1 Windows build with CVE-2026-109618 / CVE-2026-109619 backport

PHP 8.1 is EOL, so php.net ships only a source backport (branch `PHP-8.1-CVE-2026-109618-109619`, commit `1d7dec1`), no Windows binaries. This workflow builds them with the official `php/php-windows-builder` (VS2019 / vs16, PGO, same config as windows.php.net).

## Build

1. Create a GitHub repo and push this folder to it (keep the `.github/workflows/build.yml` path).
2. `gh workflow run php-8.1-cve-windows` (or Actions tab > php-8.1-cve-windows > Run workflow).
3. `gh run download` once the run finishes (about 30-60 min).

## Output

Four artifacts (nts-x64, ts-x64, nts-x86, ts-x86), each with:

- `php-8.1.34[-nts]-Win32-vs16-{x64,x86}.zip`
- debug pack, devel pack, test pack

Use NTS for IIS/FastCGI/CLI, TS for Apache mod_php. Needs the VC++ 2015-2022 Redistributable.

## Note

The patched build still reports `PHP 8.1.34`, same as the unpatched official build. Tell them apart by zip SHA-256 or build date. Existing 8.1 vs16 extension DLLs should still load: the patch adds functions but changes no public struct layout.
