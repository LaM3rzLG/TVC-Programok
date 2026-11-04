# Hogyan működik a SIDSoftEMU

A C64 SID-chipje három, egymástól független hanggenerátor: mindegyiknek van saját frekvenciája, hullámformája
(háromszög, fűrész, négyszög változtatható kitöltéssel, zaj), hangereje-burkolója (ADSR), és van egy közös szűrő is.
A TVC-ben nincs ilyen chip, csak egy **4 bites hangkimenet** (DAC, a 6-os port 2–5. bitje): 16 feszültségszint,
amit a processzornak kell elég sűrűn írnia. A SIDSoftEMU a munkát kétfelé osztja:

- **a PC** (`sid2tvc`) elvégzi mindazt, amihez sok számolás kell: lefuttatja a C64-es zenét, és kiszámolja, hogyan
  szólna a SID;
- **a TVC** csak a kész, tömörített adatból szintetizál: három hangot kever valós időben.

## 1. A PC oldal (sid2tvc)

1. **A C64-es lejátszó futtatása.** A `.sid` fájl valójában egy C64-es program (a zene lejátszó rutinja + adat). A
   `sid2tvc` egy 6502-es processzor-emulátorban képkockánként (50-szer másodpercenként) meghívja a lejátszó rutint,
   és feljegyzi, mit írt a SID regisztereibe.
2. **Az ismétlődő szakasz megkeresése.** A legtöbb játékzene egy idő után ismétlődik. A program megkeresi az ismétlődés kezdetét
   és hosszát, kétféleképpen: a C64 teljes memóriájának ismétlődéséből, vagy abból, hogy a hallható hang mikor kezd
   ismétlődni. A rövidebb eredmény nyer. A TVC-n a dal így a végtelenségig szól: a végén az ismétlődő szakasz elejéről folytatódik.
3. **A SID hangjának kiszámolása.** Hangonként, képkockánként:
   - a **burkoló** (ADSR) a SID valódi időzítéseivel, ebből a pillanatnyi hangerő;
   - a **hangszín**: a program kiszámolja a hullámforma egy teljes periódusát úgy, ahogy a SID adná ki – a szűrővel
     (a 6581-es chip görbéjével), a kevert hullámformákkal, a gyűrűmodulációval és a szinkronnal együtt.
4. **Hangszínpaletta.** Egy dalban több ezer különböző hullámalak fordulhat elő, a TVC-n viszont csak kevésnek van
   hely. A program a felharmonikusaik alapján csoportosítja őket, és **dalonként 14 hangszín-táblát** választ
   (egy tábla = a hullám egy periódusa 256 mintában, mintánként 0–15). Ezek mellé jön a zaj és a négyszög (ezt a TVC
   tábla nélkül, összehasonlítással állítja elő, így a kitöltési tényező szabadon változhat).
5. **Hangerő.** A 4 bites kimenet 16 szintjét a három hang közösen használja. A program dalonként egy állandó
   erősítést választ úgy, hogy a hangos részek kitöltsék a tartományt, de ne torzuljanak; hangonként 16
   hangerőszint van.
6. **Tömörítés.** Hangonként, képkockánként egy rekord kerül a fájlba, de csak az, ami változott: a hangmagasság
   (lépésköz), a hangszín és hangerő („szelet”: tábla + szint), a négyszög kitöltése. A zenében sok az ismétlődés
   (ugyanaz az ütem többször), ezeket visszahivatkozás helyettesíti („játszd le újra az ott lévő n rekordot”). Egy
   dal így legfeljebb kb. 50 KB.
7. **Ellenőrzés.** A program a kész fájlt a TVC lejátszójának pontos másán visszajátssza. Kérésre (`-w`) WAV-fájlt
   is ír, ami pontosan úgy szól, mint a TVC-n.

## 2. A TVC oldal (a lejátszó)

### A hang-megszakítás – 5008-szor másodpercenként

A lejátszás alatt a TVC hangidőzítője **5008 Hz-en** megszakítást kér (624 processzorciklusonként). A megszakítás
mindhárom hangra ugyanazt csinálja:

1. **Fázis:** a hang 16 bites számlálójához hozzáadja a lépésközt. A számláló felső bájtja (0–255) megmondja, hol
   tart a hullám a periódusán belül. Nagyobb lépésköz = gyorsabban körbeér = magasabb hang.
2. **Minta:** ezzel a bájttal kiolvassa a hang aktuális hangszín-táblájának értékét (0–15).
3. **Hangerő:** egy második táblából (szintlap) kiolvassa az érték × hangerő szorzatot – szorzás helyett ez csak egy
   memóriaolvasás.

A három hang összege + egy kiegyenlítő érték (hogy a csend a tartomány közepén legyen) megy a 4 bites kimenetre.
Ehhez a Z80 minden regiszterkészletét felhasználja (IX, IY, és a második regiszterkészlet HL-je a három
számláló), így a megszakítás nem ment és nem tölt vissza regisztereket.

A hangonkénti kódrészlet **önmódosító**: a 10 bájtos „rés” vagy a táblás változatot tartalmazza (a tábla lapjának
címével és a hangerővel), vagy a négyszöget (a számláló összehasonlítása a kitöltéssel). A 50 Hz-es lejátszó
ezeket a bájtokat írja át, amikor a hangszín, a hangerő vagy a kitöltés változik.

A megszakítás kb. 330 ciklus a 624-ből, vagyis **a processzor idejének kb. 53%-át** viszi el.

### A lejátszó – 50-szer másodpercenként

Minden 100. hang-megszakítás (= 50 Hz, a C64 képkockája) jelez a főprogramnak. Az ekkor hangonként beolvas egy
rekordot a dalból, és beírja a megszakítás kódjába az új lépésközt, a rést és a kitöltést. Ha visszahivatkozást
talál, onnan olvas tovább; a dal végén az ismétlődő szakasz elejére ugrik. Ugyanekkor frissíti a százalékot a képernyőn.

A képernyő 312 sora pontosan 100 hang-megszakítás ideje, így a megszakítás a képhez van igazítva: ezért tudja a
keret a szöveg mellett a kimenő hang szintjét mutatni (a villogó sáv), mindig ugyanazokon a sorokon.

### A memória

A dal a teljes 64 KB RAM-ot használja, ROM nélkül:

| Cím | Mi van ott |
|---|---|
| `$0000–$22FF` | a lejátszó (megszakítás, betöltő, dekódoló) |
| `$2300–$EBFF` | a dal (legfeljebb 51 456 bájt) |
| `$EC00–$EFFF` | a százalék-kijelző kódja és számjegyei, a keret színtáblája |
| `$F000–$FDFF` | a dal 14 hangszín-táblája |
| `$FE00`, `$FF00` | a zaj táblája, a szintlap |

A dal felülírja a program fájlkezelő részét (menü, betűkészlet, forgó logó). Ezért lejátszás előtt a program ezt a
részt a képmemória nem látható részébe menti (a képből ilyenkor csak a felső sáv látszik), és a lejátszás után
visszamásolja.

## Korlátok

- A TVC 5008 Hz-en mintavételez, ezért **2,5 kHz fölötti hangot** nem tud tisztán lejátszani (a SID 4 kHz fölé is megy).
- A hangszínek közelítések: dalonként 14 tábla jut a SID végtelen sok lehetséges hangjára, a szűrő
  „befagyasztva” kerül a táblába.
- A **gyorsított** (képkockánként többször hívott, CIA-időzítésű) zenéket és a **digi-hangokat** (minták a SID
  hangerő-regiszterén keresztül) a `sid2tvc` még nem kezeli.
- Egy dal legfeljebb kb. 50 KB lehet; a nagyon hosszú, ismétlés nélküli zenék nem férnek el.
