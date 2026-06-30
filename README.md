# kernel-soup-gen

Generate **IEC 62304 §8 SOUP records** for Linux kernel drivers automatically.

Point it at any driver in a Linux kernel source tree — it extracts maintainers,
copyright, Kconfig dependencies, git history, CVE links, and kernel version,
and outputs a complete, filled-in SOUP record in Markdown.

```
$ python3 kernel-soup-gen.py drivers/iio/adc/ti-ads1298.c

kernel-soup-gen: drivers/iio/adc/ti-ads1298.c
  Kernel root : /home/user/linux
  Kernel ver  : 6.10
  Commits     : 23
  Maintainers : 4 found
  Output      : SOUP_ti-ads1298_6.10.md
```

---

## Why

Every medical device team using the Linux kernel must maintain IEC 62304 §8
SOUP records for every OSS component. Writing these manually is time-consuming,
error-prone, and requires kernel expertise to do correctly.

This tool automates the mechanical part — extracting the facts from the source
tree — so engineers can focus on the parts that require judgment: hazard
classification, risk assessment, and device-specific verification steps.

---

## What it extracts automatically

| Field | Source |
|---|---|
| Driver name, license | `MODULE_*` macros in source file |
| Copyright holders | SPDX + copyright comments in source |
| Kernel version | Top-level `Makefile` |
| Official maintainers | `scripts/get_maintainer.pl` |
| Kconfig dependencies | `Kconfig` in driver directory |
| Commit count, first/last date | `git log --follow` |
| Top contributors | `git log --format=%aN` |
| CVE and bugzilla links | Generated from driver name |

Fields requiring engineer judgment (safety class, hazard table, purpose in device) are left as clearly marked placeholders.

---

## Usage

```bash
# Basic — auto-detects kernel root from driver path
python3 kernel-soup-gen.py drivers/iio/adc/ti-ads1298.c

# Specify kernel root explicitly
python3 kernel-soup-gen.py drivers/net/ethernet/intel/igb/igb_main.c \
    --kernel-root /path/to/linux

# Custom output file
python3 kernel-soup-gen.py drivers/spi/spi-pl022.c -o my_SOUP.md
```

**Requirements:** Python 3.8+, git, perl (for `get_maintainer.pl`)

---

## Output format

The generated Markdown follows IEC 62304 §8.1 structure:

- Identification (name, version, license, maintainer)
- Copyright
- Kconfig dependencies
- Maintainers (from `get_maintainer.pl`)
- Git history (contributors, recent commits)
- Known anomalies (CVE/bugzilla links pre-filled)
- Risk classification table (ISO 14971:2019) — placeholders
- Verification requirements checklist
- Change control table

---

## Example output

See [`examples/SOUP_ti-ads1298.md`](examples/SOUP_ti-ads1298.md) — generated
from the TI ADS1298/ADS1299 biopotential ADC driver
([drivers/iio/adc/ti-ads1298.c](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/tree/drivers/iio/adc/ti-ads1298.c)).

---

## Who is this for?

- Medical device teams using Linux kernel drivers in IEC 62304 Class A/B/C software
- Automotive safety teams (ISO 26262) using Linux
- Any team required to maintain SOUP records for kernel OSS components
- Engineers doing initial SOUP identification for a new product

---

## License

Apache 2.0 — free to use in commercial medical device projects.

## Author

Md Shofiqul Islam — Linux kernel IIO contributor
([lore.kernel.org](https://lore.kernel.org/all/?q=Md+Shofiqul+Islam))
