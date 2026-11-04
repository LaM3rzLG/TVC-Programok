# sid2tvc 1.3

**C64-es `.sid` zenéből a TVC [SIDSoftEMU](../SIDSoftEMU/) lejátszójának dalfájlja (`.STC`).** A PC-n fut, és
elvégzi mindazt, amihez sok számolás kell. A TVC utána már csak lejátssza a kész fájlt. Hogy ez hogyan működik:
[MUKODES.md](../SIDSoftEMU/MUKODES.md).

Ingyenes `.sid` zenék tízezrei: [High Voltage SID Collection (HVSC)](https://www.hvsc.c64.org/).

## Letöltés

Mindegyik ugyanaz a program, és bájtra ugyanazt a `.STC`-t adja.

| Rendszer | Mappa | Megjegyzés |
|---|---|---|
| Windows, 64 bites | [`windows-x64/`](windows-x64/) | Windows 7-től; ARM-os Windowson is fut |
| Windows, 32 bites | [`windows-x86/`](windows-x86/) | régebbi gépekre |
| macOS | [`macos/`](macos/) | Apple Silicon (M1…) és Intel is, macOS 10.13-tól |
| Linux, PC 64 bites | [`linux-x86_64/`](linux-x86_64/) | bármely disztribúció (statikus, nem kell hozzá semmi) |
| Linux, PC 32 bites | [`linux-i686/`](linux-i686/) | |
| Linux, ARM 64 bites | [`linux-arm64/`](linux-arm64/) | pl. Raspberry Pi 3/4/5 64 bites rendszerrel |
| Linux, ARM 32 bites | [`linux-armhf/`](linux-armhf/) | pl. Raspberry Pi 32 bites rendszerrel |

Rendszerenként egy ZIP-ben is letölthető a [release oldalon](https://github.com/LaM3rzLG/TVC-Programok/releases/tag/sid2tvc-1.3).

### Első indítás

A program nincs digitálisan aláírva, ezért az első indításkor a rendszer figyelmeztet:

- **Windows:** „A Windows megvédte a számítógépet” → *További információ* → *Futtatás mindenképp*.
- **macOS:** Terminálban egyszer: `xattr -d com.apple.quarantine sid2tvc` (abban a mappában, ahol a program van).
  Vagy Finderben jobb klikk → *Megnyitás* → *Megnyitás*.
- **Linux:** futtathatóvá kell tenni: `chmod +x sid2tvc`.

## Használat

### Egyszerűen: húzd rá a fájlt

**Windowson** húzz egy vagy több `.sid` fájlt a `sid2tvc.exe` ikonjára. A program a zene **összes aldalát**
átalakítja, és a `.sid` mellé, egy `SID_TVC` mappába teszi:

```
Last_Ninja.sid
SID_TVC\LAST_NIN\SONG1.STC
SID_TVC\LAST_NIN\SONG2.STC
...
```

Ugyanez a parancssorban (minden rendszeren): `sid2tvc Last_Ninja.sid` (több fájl is megadható).

A `SID_TVC` mappát változatlan szerkezetben kell a TVC lemezére / SD-kártyájára / NanoSD-kártyájára másolni, a
`SIDTVC.CAS` mellé. A SIDSoftEMU fájlkezelőjében a `SID_TVC` → `LAST_NIN` könyvtárban lesznek a dalok.

### Kapcsolókkal

```
sid2tvc [-s N | -a] [-o MAPPA] [-n NÉV] [-w] [-f N] [-m] [-q] fajl.sid [fajl2.sid ...]
```

| Kapcsoló | Jelentés |
|---|---|
| `-s N` | csak az N. aldal (ha nincs `-a`, akkor alapból a `.sid` kezdő dala) |
| `-a` | az összes aldal |
| `-o MAPPA` | a kimenet helye: `MAPPA/<NÉV>/SONG<n>.STC` (alapból `SID_TVC` az aktuális mappában) |
| `-n NÉV` | a dalok mappájának neve, legfeljebb 8 betű (alapból a `.sid` fájlnevéből) |
| `-w` | WAV-fájl is, pontosan úgy, ahogy a TVC-n szól: `SONG<n>.WAV` |
| `-f N` | hurok nélküli zenénél legfeljebb N képkocka (50 = 1 másodperc; alapból 30000 = 10 perc) |
| `-m` | a hurkot csak a C64 memóriájának ismétlődéséből keresi |
| `-q` | csak a hibákat írja ki |

Példa: `sid2tvc -a -w -n NINJA -o D:\TVC Last_Ninja.sid` → `D:\TVC\NINJA\SONG1.STC`, `SONG1.WAV`, …

A program aldalanként kiírja a dal hosszát, a hurkot és a fájl méretét. Ha egy dal nem fér el a TVC memóriájában
(kb. 50 KB fölött), azt kiírja, és kihagyja.

### Mit nem tud (egyelőre)

- a gyorsított (képkockánként többször hívott, CIA-időzítésű) zenéket – ezek lassabban szólnak;
- a digi-hangokat (minták);
- a nagyon hosszú, ismétlés nélküli zenék nem férnek el.

## Fordítás a forrásból

A forrás egyetlen C99 fájl, külső könyvtár nélkül: [`forras/sid2tvc.c`](forras/sid2tvc.c). Bármely C-fordítóval
lefordul. A `forras` mappában:

| Rendszer | Mi kell | Parancs |
|---|---|---|
| macOS | Xcode parancssori eszközök: `xcode-select --install` | `make` |
| Linux | `gcc` vagy `clang` (pl. `sudo apt install build-essential`) | `make` |
| Windows (MSYS2) | [msys2.org](https://www.msys2.org), majd `pacman -S mingw-w64-x86_64-gcc make` | `make` |
| Windows (Visual Studio) | „Developer Command Prompt” | `cl /O2 /fp:precise sid2tvc.c` |

`make` nélkül: `cc -O2 -std=c99 -ffp-contract=off -o sid2tvc sid2tvc.c -lm`.

Két dologra kell figyelni, hogy a kimenet minden gépen bájtra ugyanaz legyen:

- a **`-ffp-contract=off`** (Visual Studióban `/fp:precise`) kapcsoló kell, különben a fordító egyes lebegőpontos
  műveleteket összevon, és az eredmény az utolsó tizedesjegyben eltérhet;
- **32 bites x86** gépre fordításnál még `-msse2 -mfpmath=sse` is kell (a régi x87-es lebegőpontos egység más
  pontossággal számol).

A letölthető programok így készültek: GitHub Actionsszel, mindegyik rendszeren lefordítva, és a Last Ninja 1-2
dalain bájtra ellenőrizve.

## Licenc

GPL-3.0-or-later ([LICENSE](LICENSE)). A `sid2tvc` szabad szoftver: használhatod, módosíthatod és továbbadhatod,
ha a módosított forrást is ugyanígy közzéteszed.

---
sid2tvc – L@M3rZ (LaM3rzLG), 2026.
