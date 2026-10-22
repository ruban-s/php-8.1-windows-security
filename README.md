# PHP 8.1 for Windows: CVE-2026-109618 and CVE-2026-109619 security fix

[![Latest release](https://img.shields.io/github/v/release/ruban-s/php-8.1-windows-security?label=download)](https://github.com/ruban-s/php-8.1-windows-security/releases/latest)
[![Downloads](https://img.shields.io/github/downloads/ruban-s/php-8.1-windows-security/total)](https://github.com/ruban-s/php-8.1-windows-security/releases)
[![Build](https://github.com/ruban-s/php-8.1-windows-security/actions/workflows/build.yml/badge.svg)](https://github.com/ruban-s/php-8.1-windows-security/actions/workflows/build.yml)
[![Build provenance](https://img.shields.io/badge/build%20provenance-signed-brightgreen)](https://github.com/ruban-s/php-8.1-windows-security/attestations)

Patched **PHP 8.1.34 Windows binaries** (x64 and x86, Non Thread Safe and Thread Safe) with the official fix for the two critical remote code execution vulnerabilities announced on 21 October 2026:

| CVE | Advisory | Issue | Severity |
|---|---|---|---|
| CVE-2026-109618 | [GHSA-w8hw-w9fx-j79w](https://github.com/php/php-src/security/advisories/GHSA-w8hw-w9fx-j79w) | RCE via OOM corruption | Critical, CVSS v4.0 9.2 |
| CVE-2026-109619 | [GHSA-6wmf-4rv5-p3rp](https://github.com/php/php-src/security/advisories/GHSA-6wmf-4rv5-p3rp) | RCE via stale global state | Critical, CVSS v4.0 9.2 |

PHP 8.1 is end of life. The PHP project fixed these issues in 8.2.35, 8.3.36, 8.4.27 and 8.5.12. For 8.1 it published only a [source backport](https://github.com/php/php-src/tree/PHP-8.1-CVE-2026-109618-109619), with no Windows binaries ([php.net announcement](https://www.php.net/archive/2026.php#2026-10-21-5)). This repository compiles that backport into ready-to-use Windows builds.

> [!IMPORTANT]
> This is an **unofficial** build and is not affiliated with the PHP Group. It exists for servers that cannot move off PHP 8.1 right now. The real fix is to [upgrade to a supported PHP version](https://www.php.net/supported-versions.php).

## Download

| Package | Use it for | Download |
|---|---|---|
| x64 Non Thread Safe | IIS, FastCGI, Nginx + php-cgi, CLI | [php-8.1.34-pl1-cve2026-nts-Win32-vs16-x64.zip](https://github.com/ruban-s/php-8.1-windows-security/releases/latest/download/php-8.1.34-pl1-cve2026-nts-Win32-vs16-x64.zip) |
| x64 Thread Safe | Apache with mod_php (`php8apache2_4.dll`) | [php-8.1.34-pl1-cve2026-Win32-vs16-x64.zip](https://github.com/ruban-s/php-8.1-windows-security/releases/latest/download/php-8.1.34-pl1-cve2026-Win32-vs16-x64.zip) |
| x86 Non Thread Safe | 32-bit IIS / FastCGI / CLI | [php-8.1.34-pl1-cve2026-nts-Win32-vs16-x86.zip](https://github.com/ruban-s/php-8.1-windows-security/releases/latest/download/php-8.1.34-pl1-cve2026-nts-Win32-vs16-x86.zip) |
| x86 Thread Safe | 32-bit Apache mod_php | [php-8.1.34-pl1-cve2026-Win32-vs16-x86.zip](https://github.com/ruban-s/php-8.1-windows-security/releases/latest/download/php-8.1.34-pl1-cve2026-Win32-vs16-x86.zip) |

The [release page](https://github.com/ruban-s/php-8.1-windows-security/releases/latest) also has debug packs (PDB symbols), devel packs (for compiling extensions), the test pack, and `SHA256SUMS`.

Requires the [Microsoft Visual C++ 2015-2022 Redistributable](https://learn.microsoft.com/cpp/windows/latest-supported-vc-redist) for the matching architecture.

## Upgrade an existing PHP 8.1 install

1. Stop the web server or application pool that runs PHP (IIS, Apache, php-cgi, or a Windows service).
2. Back up your current PHP folder.
3. Extract the zip that matches your current build (NTS or TS, x64 or x86) into a new folder.
4. Copy your `php.ini` and any third-party extension DLLs (for example `ext\php_xdebug.dll` or `ext\php_sqlsrv_81_nts_x64.dll`) from the old folder to the new one.
5. Swap the new folder into place and start the server again.
6. Run `php -v`. It should print `PHP 8.1.34-pl1-cve2026`.

Extensions built for official PHP 8.1 vs16 should keep working, because the patch changes no public struct layout and the extension API number is still `20210902`.

## Verify the download

Check the SHA-256 against `SHA256SUMS` from the release:

```powershell
Get-FileHash .\php-8.1.34-pl1-cve2026-nts-Win32-vs16-x64.zip -Algorithm SHA256
```

Check the signed build provenance. It proves the zip was built by this repository's GitHub Actions workflow and was not modified afterwards:

```powershell
gh attestation verify .\php-8.1.34-pl1-cve2026-nts-Win32-vs16-x64.zip --repo ruban-s/php-8.1-windows-security
```

Check that the fix is compiled in. Only patched builds contain the new `zend_mm_safe_error_ex` function (use `php8ts.dll` for Thread Safe builds):

```powershell
findstr /m zend_mm_safe_error_ex php8.dll
```

## How it is built

The [workflow](.github/workflows/build.yml) is short enough to audit in a minute:

- It checks out php-src at commit [`1d7dec1`](https://github.com/php/php-src/commit/1d7dec178a094e6e75b229ce22754045f8c6070a), the head of the official `PHP-8.1-CVE-2026-109618-109619` branch: PHP 8.1.34 plus the security team's backport.
- It changes only the version string, from `8.1.34` to `8.1.34-pl1-cve2026`, so a patched build can be told apart from the vulnerable one.
- It compiles with [php/php-windows-builder](https://github.com/php/php-windows-builder), the tool that produces the official windows.php.net builds. Same Visual Studio 2019 (vs16) toolchain, same dependency libraries, same configure flags, PGO-optimised.
- Before publishing, it checks every binary for the fix and the version string, writes `SHA256SUMS`, and signs [build provenance](https://github.com/ruban-s/php-8.1-windows-security/attestations) with Sigstore.

To build it yourself, fork this repository and run **Actions > Build PHP 8.1 for Windows > Run workflow**, or `gh workflow run build.yml`.

## FAQ

**Is this an official PHP release?**
No. The source code fix is official. The Windows compilation is done here, with the official build tooling.

**Why does the version say `8.1.34-pl1-cve2026`?**
So you can tell it apart from the vulnerable official `8.1.34`. `pl` is PHP's "patch level" marker, so `version_compare()` treats this build as newer than 8.1.34 and older than 8.1.35. `PHP_VERSION_ID` stays `80134`.

**Will vulnerability scanners still flag it?**
Scanners that only read the version number may still flag 8.1, because 8.1 is end of life. Use the verification steps above to show that the fix is present.

**Does it work with XAMPP, WampServer or Laragon?**
These stacks bundle the same windows.php.net vs16 builds, so replacing their PHP 8.1 folder should work. Pick Thread Safe for XAMPP and WampServer (Apache mod_php). Back up first. This repository does not test these stacks.

**What about PHP 7.4 and 8.0?**
The PHP security team published backports for them too (`PHP-7.4-CVE-2026-109618-109619` and `PHP-8.0-CVE-2026-109618-109619`), but this repository builds only 8.1. Open an issue if you need them.

**What is fixed beyond these two CVEs?**
Nothing. 8.1 had its last regular release (8.1.34) before end of life. This build adds only the backport for CVE-2026-109618 and CVE-2026-109619.

## Security

See [SECURITY.md](SECURITY.md). Report problems with these builds through [private vulnerability reporting](https://github.com/ruban-s/php-8.1-windows-security/security/advisories/new). Report vulnerabilities in PHP itself to the [PHP project](https://github.com/php/php-src/security).

## License

The PHP binaries are distributed under the [PHP License v3.01](https://www.php.net/license/3_01.txt). Bundled libraries keep their own licenses. The scripts and documentation in this repository are under the [MIT License](LICENSE).
