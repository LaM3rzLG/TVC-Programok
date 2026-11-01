# SIDSoftEMU 1.0

**Commodore 64-es SID-zenék a Videoton TV-Computeren, SID-kártya nélkül.** A TVC saját, 4 bites hangkimenetén szól mind a
három SID-hang. A zenéket PC-n alakítjuk át, a TVC csak lejátssza őket.

![Fájllista](kepek/fajllista.png)
![Lejátszás](kepek/lejatszas.png)

## Mi van ebben a mappában

| Fájl | Mire való |
|---|---|
| `SIDSOFT.CAS` | a program (SD-kártyára / NanoSD-re) |
| `SID_TVC/` | a zenék (`.STC`): `LNINJA1` (11 dal), `LNINJA2` (12 dal) |
| `SIDSoftEMU.dsk` | 1. lemez (720 KB): a program + a Last Ninja 1 dalai |
| `SIDSOFT2.dsk` | 2. lemez (720 KB): a program + a Last Ninja 2 dalai |

## Mi kell hozzá

- **TV-Computer 64 KB memóriával** (a lejátszó a teljes 64 KB-ot használja),
- és az alábbiak egyike:
  - **lemezegység VT-DOS 1.2-vel** (a könyvtárlistához a VT-DOS 1.2 kell; az 1.1-gyel a lista üres marad, 255-ös hibával),
  - **SD Dual** (VT-DOS),
  - **NanoSD**.

Emulátorban (freeTVC, ep128emu) lemezzel és NanoSD-vel ki van próbálva, SD Duallal még nem. **Valódi gépen még nem** -
ha kipróbálod, szívesen veszem a visszajelzést.

## Indítás

### Lemezről (floppy vagy lemezkép)
1. Tedd be a `SIDSoftEMU.dsk`-t (Last Ninja 1) vagy a `SIDSOFT2.dsk`-t (Last Ninja 2).
2. BASIC-ben: `LOAD "SIDSOFT"` - a program magától elindul.

### SD-kártyáról (SD Dual)
1. Másold a kártya gyökerébe a `SIDSOFT.CAS`-t és a `SID_TVC` mappát (a szerkezete maradjon így:
   `SID_TVC\LNINJA1\SONG1.STC` ...).
2. BASIC-ben: `LOAD "SIDSOFT"`.

### NanoSD-ről
1. Másold a kártya gyökerébe a `SIDSOFT.CAS`-t és a `SID_TVC` mappát.
2. Bekapcsoláskor a NanoSD menüjében válaszd a `SIDSOFT.CAS`-t, és nyomj Entert (vagy BASIC-ben `LOAD "SIDSOFT"`).

## Kezelés

**Fájlkezelő**

| Gomb | Mit csinál |
|---|---|
| Fel / Le | választás |
| Balra / Jobbra | lapozás |
| Enter | belépés a könyvtárba, ill. a `.STC` zene lejátszása |
| ESC | egy könyvtárral feljebb |
| ESC a legfelső könyvtárban | kilépés - „Biztosan kilépsz?” **I** = igen (vissza a BASIC-be, Graphics 4), **N** vagy ESC = nem |

**Lejátszás közben** bármely gomb = vissza a fájlkezelőbe.

A lejátszás alatt a képernyő közepén látszik a zene címe, szerzője, kiadása és a fájl neve, mellette hogy hol tart a dal
(0-100 %), a szöveg két oldalán pedig a keret a zene ritmusára villog. A dalok a végük után az ismétlődő résztől szólnak
tovább, amíg meg nem állítod.

## A zenék

| Mappa | Zene | Szerző | Kiadás |
|---|---|---|---|
| `LNINJA1` | The Last Ninja | Ben Daglish & Anthony Lees | 1987 System 3 |
| `LNINJA2` | Last Ninja 2 | Matt Gray | 1988 System 3 |

A zenék szerzői jogai a szerzőké és a kiadóé. Az eredeti `.sid` fájlok forrása a
[High Voltage SID Collection (HVSC)](https://www.hvsc.c64.org/). A Last Ninja 2 12. dala túl nagy, ezért kimaradt.

Saját `.sid` zenékhez kell majd az átalakító (`sid2tvc`) - ez külön érkezik, Windowsra, Macre és Linuxra.

## Hogyan működik (röviden)

- A PC a `.sid` fájl 6502-es lejátszóját futtatja, és képkockánként (50/s) rögzíti a SID-regisztereket.
- Ebből kiszámolja a valódi SID-hangot (hullámformák, szűrő, burkoló), és dalonként 14 hangszín-táblát készít.
- A TVC egy 5008 Hz-es megszakításban keveri a három hangot (hangonként 16 hangerőszint), és a 4 bites hangkimenetre küldi.
- A `.STC` fájl (1.3-as formátum) tartalmazza a dal címét, szerzőjét, hosszát, a hangszín-táblákat és a tömörített zenét
  (legfeljebb kb. 50 KB).

---
SIDSoftEMU - L@M3rZ (LaM3rzLG), 2026.
