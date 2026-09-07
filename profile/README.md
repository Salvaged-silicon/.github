# Salvaged Silicon

**Network hardware outlives its software.** A switch does not stop forwarding at line
rate when the vendor stops shipping updates — the silicon is fine, the optics are fine,
the fans still spin. What ends is the operating system, and with it the security fixes,
the new features, and any way to run something modern on a box that is mechanically and
electrically healthy.

That hardware becomes e-waste, or a lab curiosity, or it quietly stays in production
unpatched. This organisation exists to give it a current, open, maintained OS instead.

---

## NOSaic

**[nosaic-switch](https://github.com/Salvaged-silicon/nosaic-switch)** — a network
operating system built from source for switches their vendors have abandoned.

Not a distribution installed on a switch. Its own cross-toolchains, package format, base
system, kernel builds, and the daemons that drive the switching silicon. A clean clone on
a machine with only Docker and `make` produces a bootable switch.

It runs on two of them today, both installed on their own flash with the vendor OS still
present only as a way back:

| Switch | Silicon | CPU | Bootloader |
|---|---|---|---|
| Arista DCS-7050SX2-72Q | Broadcom Trident2+ (BCM56860) | x86_64 | Aboot |
| Edgecore AS5610-52X | Broadcom Trident+ (BCM56846) | PowerPC e500v2, big-endian | ONIE over U-Boot |

Both forward in hardware, hold OSPFv2 and OSPFv3 adjacencies, and come back from a cold
power cut in about a minute and a half. Upgrades are A/B: a new image goes into the slot
that is not running, boots on trial, and either confirms itself healthy and commits or
rolls back to the image the switch was on — without anyone watching.

Two CPU architectures, two silicon generations, one set of commands. The PowerPC board
cannot run the main CLI at all, because the Go toolchain has never targeted 32-bit
big-endian PowerPC; it runs a second CLI in C that answers the same contract, checked by
running both on a board that can host either and diffing the output byte for byte.

**Status: experimental, and the project says so.** Nothing here is described as
production. Each board states what is proven on it and what is still missing.

## The other repositories

**[nosaic-sources](https://github.com/Salvaged-silicon/nosaic-sources)** — pinned
upstream source archives, every file hash-verified by the build.

This exists because abandoned hardware and abandoned source archives are on the same
timeline. Several components NOSaic builds from — gcc, glibc, binutils, gmp, mpfr, isl —
have no repository on GitHub at all and are served from FTP mirrors that come and go.
Upstream is still tried first, because it is the real provenance; the mirror is what
stops a vanished tarball from stopping a build.

**[OpenBCM](https://github.com/Salvaged-silicon/OpenBCM)** and
**[OpenMDK](https://github.com/Salvaged-silicon/OpenMDK)** — forks of Broadcom's
source-available SDKs, so a build never depends on an upstream that could move. Where a
vendor SDK exists and its licence permits shipping it, NOSaic uses it rather than
reimplementing a driver; no SDK source is copied into the NOSaic tree, it is referenced
by `file:line`.

## How the work is written up

Every board carries its own documentation: how to install it, how to build for it, a
hardware reference down to registers and port maps, and a list of what is still wrong.
The reverse-engineering notes are deliberately detailed, including the approaches that
did not work, so the next person with a different switch spends their time somewhere new
rather than rediscovering the same registers.

---

Built by **Christopher Wright**.

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Christopher_Wright-0A66C2?logo=linkedin&logoColor=white)](https://www.linkedin.com/in/christopher-wright-498b3859)
[![Buy Me a Coffee](https://img.shields.io/badge/Buy_me_a_coffee-FFDD00?logo=buymeacoffee&logoColor=black)](https://buymeacoffee.com/Wrightca1)

Every platform added starts with buying the switch. They are cheap second-hand; the
optics, cables and lab power are not.
