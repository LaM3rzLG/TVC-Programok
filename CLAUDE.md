# TVC-Programok — projekt-memória (indulás: 2026-11-02)

**NYILVÁNOS repó: https://github.com/LaM3rzLG/TVC-Programok** - a felhasználó kész, futtatható TVC-programjai, programonként
egy mappában (.CAS, lemezkép, adat, README a használatról). **Forrás NEM kerül ide** (az a privát repókban van: SIDSoftEMU,
WaterPumpGame, ...). Commit a felhasználó nevében (`LaM3rzLG <lorinczg77@gmail.com>`); a felhasználó nem gitel.

## Kiadás menete (programonként)
1. A program privát repójában build (pl. SIDSoftEMU: `bash resources/build_mac.sh --no-emu` -> `SD_KARTYA/`, `*.dsk`).
2. A fájlok ide, a program mappájába (a régiek helyére), README frissítése (verzió, változások).
3. Commit + push, aztán GitHub-release: címke `<program>-<verzió>` (pl. `sidsoftemu-1.0`), melléklet egy ZIP a mappa
   tartalmával (`gh release create ... <zip>`); a főoldali README táblázatában a letöltési link.

## Tartalom
- **SIDSoftEMU 1.0** (2026-11-02): `SIDSOFT.CAS`, `SID_TVC/` (LNINJA1 11 dal, LNINJA2 12 dal), `SIDSoftEMU.dsk`,
  `SIDSOFT2.dsk`, `kepek/`. A `sid2tvc` átalakító később (Windows/Mac/Linux, felülettel; GPL-3 -> a forrás is mellé).
