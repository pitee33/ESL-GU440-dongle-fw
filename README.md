# GU440 Dongle — Bekötési és Kezelési Útmutató

4,2" e-ink polccímke (fekete–fehér–piros) vezérlése **Raspberry Pi Pico W** dongle-ról, telepítés nélkül.

A szerkesztő egy weboldal, amit maga a dongle szolgál ki — **nem kell semmilyen programot telepíteni**, csak egy böngésző, és a gépnek/telefonnak ugyanazon a WiFi-n kell lennie.

```
Böngésző (rajz/szöveg/QR) → WiFi → Pico W dongle → 5 vezeték → GU440 címke
```

A címke elem nélkül is megtartja a képet — az e-ink csak frissítéskor fogyaszt. A címkét a dongle táplálja.

---

## 1. Bekötés

**Öt vezeték** a címkéről a dongle-ra. A címkén a pontok (padek) fel vannak írva, a Pico W-n a **tűszám** szerint kell kötni (a lap szélén nyomtatott számozás).

| Címke pad | Pico W tű (GPIO) | Mit csinál |
|---|---|---|
| **DC** | 6. tű (GP4) | Debug órajel |
| **DD** | 7. tű (GP5) | Debug adat |
| **GND** | 8. tű (GND) | Közös föld |
| **RST** | 9. tű (GP6) | Reset |
| **VCC** | 36. tű (3V3 OUT) | Táp — a dongle táplálja a címkét |

> ⚠️ **SOHA ne kössd a 40. tűre (VSYS)!** Az 5 V — tönkreteszi a címkét. A táp kizárólag a **36. tű (3V3 OUT)** lehet.

> ⚠️ **A címke elemét hagyd KI** — a címkét kizárólag a dongle 3,3 V-ja táplálja. Az elem bent maradva rontja a flash megbízhatóságát.

> 💡 A kábelek maradhatnak kötve — a címkét csak flash-eléskor kell a dongle-ra kötni.

---

## 2. Használat lépésről lépésre

1. **Dugd be a donglét** USB-be (bármelyik gép, töltő vagy power bank is jó). A dongle automatikusan csatlakozik a beállított WiFi-re (kb. 30 mp).
2. **Nyisd meg a böngészőben:** `http://cc.local` — ha ez nem működne, a dongle IP-címén: `http://192.168.0.131`
3. **Szerkeszd a képet** a weboldalon:
   - 🎨 **Ceruza** — fekete / piros / fehér színválasztóval, ujjal vagy egérrel
   - ✏️ **Szöveg** — írd be a mezőbe, állítsd a méretet, nyomd meg a gombot, majd kattints a helyre a vásznon
   - 🔲 **QR-kód** — tartalom beírása → gomb → középre teszi, húzható
   - 🖼️ **Kép beszúrása** — fájlból; a bepipált dithering sötétszürke árnyalatokat pöttyökké alakít

   A címke 3 színt tud: **fekete, piros, fehér** — minden más szín közelítésre kerül.
4. **Nyomd meg a 📡 Küldés gombot.** A log ablak mutatja a folyamatot: feltöltés → törlés → írás → ellenőrzés. Kb. **20–30 másodperc**. A weboldal közben is használható marad.
5. **Kész!** A címke magától frissül az új képpel. A címkét ezután le lehet venni a dongle-ról — a kép áram nélkül is megmarad.

> ⚠️ Ne mozgasd a kábeleket a Küldés alatt — a megszakított flash félkész firmware-t hagyhat.

---

## 3. WiFi váltása (új helyszín)

### Vezetékes WiFi-n át (ha az aktuális hálózat él)

1. Nyisd meg: `http://192.168.0.131/admin` (vagy `cc.local/admin`)
2. A **📶 WiFi hálózat** kártyában add meg az új SSID-t és jelszót → **Mentés + újracsatlakozás**
3. A dongle rögtön átvált az új hálózatra. Ha nem sikerül (rossz jelszó, nincs jel), magától hotspotot nyit — lásd lent.

### Hotspoton át (ha nincs elérhető WiFi)

1. Ha a beállított WiFi **25 mp alatt nem elérhető** (pl. új helyszín, router nincs), a dongle magától nyit egy `ESL-Flasher` hotspotot.
2. Csatlakozz telefonnal/géppel az `ESL-Flasher` WiFi-re (nyílt hálózat, jelszó nélkül).
3. Böngészőbe: `192.168.4.1/admin` → 📶 WiFi hálózat → új SSID + jelszó → Mentés
4. A dongle újracsatlakozik a megadott hálózatra. A beállítás megmarad — következő booton is ezt használja.

> 💤 A WiFi-beállítás a dongle belső tárolóján marad — firmware-frissítésnél sem veszik el.

---

## 4. Firmware frissítés (GitHub OTA)

1. Nyisd meg: `/admin` → **🐙 GitHub OTA** kártya
2. **„Frissítés letöltése + telepítése"** gomb → a dongle letölti a legutolsó Release `firmware.bin`-jét és telepíti
3. Kb. 1–2 perc, közben a web él. A végén a dongle magától újraindul az új fw-vel.

USB-n (BOOTSEL) is telepíthető: BOOTSEL lenyomva + bedugás → az `RPI-RP2` meghajtóra másold az UF2-et.

---

## 5. Hibaelhárítás

<details>
<summary>A böngésző nem találja a cc.local-t</summary>

A gép/telefon legyen **ugyanazon a WiFi-n**. Egyes hálózatok nem engedik az mDNS-t — ilyenkor IP-vel nyisd meg: `http://192.168.0.131`. (Új WiFi-n a router admin felületén keresd a donglét.)
</details>

<details>
<summary>Az oldal fehér / üres marad</summary>

Nyomj **Ctrl+F5**-öt (merev frissítés) — a böngésző a régi verziót tarthatja a gyorsítótárában.
</details>

<details>
<summary>„Device id: 0" vagy „hibás chip id"</summary>

A dongle nem látja a címkét: valamelyik vezeték nincs jól bekötve. Nézd át az 5 kábelt a **tűszám** szerint (6, 7, 8, 9, 36) — nem a GPIO-felirat a mérvadó. A VCC kizárólag a 36. tű (3V3), SOHA a 40. (VSYS = 5 V!).
</details>

<details>
<summary>„verify HIBA" a Küldés alatt</summary>

**Nyomd meg újra a Küldést.** Ha többször ismétlődik: gyenge az érintkezés (padek tiszta legyen), vagy túl hosszúak a kábelek — rövid, jó minőségű dupont-kábelek a jobbak.
</details>

<details>
<summary>A kép nem frissül a Küldés után</summary>

Várd meg a logban a **„kesz — a cimke frissul"** sort. Ha megvan, de a címkén nem változott semmi: húzd ki-be a VCC kábelt — a firmware induláskor kirajzolja az új képet.
</details>

<details>
<summary>A dongle nem csatlakozik a WiFi-re</summary>

A dongle 25 mp próbálkozás után `ESL-Flasher` hotspotot nyit — azon át ellenőrizd/írd át a WiFi-adatokat (lásd 3. fejezet). A jel erőssége az admin oldal 📶 kártyájában látszik (RSSI).
</details>

<details>
<summary>A GitHub OTA letöltés megszakad</summary>

Gyenge WiFi-n a letöltés megszakadhat — ilyenkor **BOOTSEL-lel** (USB) frissíts.
</details>

---

## 6. Összefoglaló tábla

| | |
|---|---|
| Szerkesztő címe | `http://cc.local` · `http://192.168.0.131` |
| Admin oldal | `/admin` — WiFi, GitHub OTA, állapot |
| WiFi váltás | admin 📶 kártya, vagy `ESL-Flasher` AP → `192.168.4.1/admin` |
| Flash ideje | kb. 20–30 másodperc (a web közben is él) |
| Címke táplálás | Pico W 36. tű (3V3 OUT) — elem nélkül, SOHA VSYS (40. tű) |
| Képméret | 400×300 pixel, 3 szín (fekete/piros/fehér) |
| Kép megmarad áram nélkül | ✅ igen (e-ink) |
| Frissítés | GitHub OTA (admin gomb) vagy BOOTSEL (USB) |

---

GU440 ESL rendszer — Pite építette · 2026. szeptember