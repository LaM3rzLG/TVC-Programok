# TVC-Programok — projekt-memória (indulás: 2026-11-02)

**NYILVÁNOS repó: https://github.com/LaM3rzLG/TVC-Programok** - a felhasználó kész, futtatható TVC-programjai, programonként
egy mappában (.CAS, lemezkép, adat, README a használatról). **Forrás NEM kerül ide** (az a privát repókban van: SIDSoftEMU,
WaterPumpGame, ...) - kivétel a GPL-es `sid2tvc` (`SID2TVC_Converter/forras/`) és a MIT-es SID unit (`SID2TVC_TRSE_Unit/`). Commit a felhasználó nevében (`LaM3rzLG <lorinczg77@gmail.com>`); a felhasználó nem gitel.

## Kiadás menete (programonként)
1. A program privát repójában build (pl. SIDSoftEMU: `bash resources/build_mac.sh --no-emu` -> `SD_KARTYA/`, `*.dsk`).
2. A fájlok ide, a program mappájába (a régiek helyére), README frissítése (verzió, változások).
3. Commit + push, aztán GitHub-release: címke `<program>-<verzió>` (pl. `sidsoftemu-1.0`), melléklet egy ZIP a mappa
   tartalmával (`gh release create ... <zip>`); a főoldali README táblázatában a letöltési link.

## Tartalom
- **SIDSoftEMU 1.1** (2026-11-04): `SIDTVC.CAS` (a felhasználó kérése: egységes név), `SID_TVC/` (LNINJA1 11 dal, LNINJA2
  12 dal), `SIDTVC.dsk` (egy lemez: LN1 + LN2 1-6, 9), `kepek/`, `MUKODES.md`. Forrás: a SIDSoftEMU repó `SD_KARTYA/` +
  `SIDTVC.dsk`. (1.0 = 2026-11-02, SIDSOFT.CAS + két lemez; a release-e megmarad.)
- **2026-11-04 este:** a `sid2tvc/` mappa neve `SID2TVC_Converter/` (a felhasználó kérése: a SIDSoftEMU-hoz és a unithoz
  is kell); **sid2tvc 1.4** (`-b` BIN a unithoz, `.STC` bemenet, `-t` hosszkorlát), release `sid2tvc-1.4`; **1.4.1**: magyarosabb szövegek (a SID fájlon belüli N. dal, ismétlődő szakasz - a felhasználó kérése: nem aldal, nem hurok), release `sid2tvc-1.4.1`.
- **SID2TVC TRSE Unit 1.0** (2026-11-04): `SID2TVC_TRSE_Unit/` = a SID unit (`SID.tru`), minta (SIDPelda), demózene BIN,
  README, LICENSE (MIT), `kepek/sidplayer.png`; forrása a SID-Player-for-TRSE repó (onnan másolva); release
  `sid2tvc-trse-unit-1.0`. Kiadási kép: a `tvc-kiadas-kep` skill stílusa.
- (régi) **sid2tvc 1.3** (2026-11-04): `sid2tvc/` = README, LICENSE (GPL-3), `forras/` (sid2tvc.c + Makefile, a SIDSoftEMU
  `tools/sid2tvc/`-ból másolva), rendszerenként egy mappa a programmal (windows-x64/x86, macos universal, linux-x86_64/
  i686/arm64/armhf). A programok a SIDSoftEMU repó GitHub Actionsével készülnek (`.github/workflows/sid2tvc.yml`, bájtra
  ellenőrizve a Last Ninja dalain): `gh run download` -> ide; release `sid2tvc-1.3`, rendszerenként egy ZIP.
  A SIDSoftEMU mappában `MUKODES.md` = a lejátszó működése. Grafikus felület: később.
