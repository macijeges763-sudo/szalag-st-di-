# Szalag — natív mobilapp építése (Android + iOS)

Ez a mappa egy **Capacitor** projekt váza. A Capacitor a te meglévő
`www/index.html` fájlodat (a stúdiót, változtatás nélkül) egy valódi,
natív Android/iOS app köré csomagolja — ugyanaz a Web Audio + mikrofon
kód fut benne, csak natív alkalmazásként telepíthető, és feltölthető
a Google Play / App Store-ba.

Ezt a részt már a saját gépeden kell futtatnod (itt, ebben a
beszélgetésben nincs internet-hozzáférésem a csomagok letöltéséhez és
nincs Android Studio / Xcode).

## Amire szükséged lesz

- **Node.js** (18+) — https://nodejs.org
- **Android app-hoz:** Android Studio (ingyenes) — https://developer.android.com/studio
- **iOS app-hoz:** Mac gép + Xcode (ingyenes az App Store-ból) — iOS-t Windows/Linux alól nem lehet buildelni
- **Fejlesztői fiókok a közzétételhez:**
  - Google Play Console — egyszeri **25 USD**
  - Apple Developer Program — **99 USD/év**

## 1. Alap telepítés

A projekt mappájában (ahol a `package.json` van):

```bash
npm install
npm install @capacitor/core @capacitor/android @capacitor/ios
npm install -D @capacitor/cli
```

## 2. Android projekt létrehozása

```bash
npx cap add android
npx cap sync android
npx cap open android
```

Ez megnyitja Android Studio-t. Onnantól:
- **Run ▶** gombbal azonnal futtathatod telefonon/emulátoron
- **Build > Generate Signed Bundle/APK** — ezzel készül a Play Store-ba feltölthető fájl

**Mikrofon engedély (kötelező, mert felvételt is készít az app):**
Nyisd meg `android/app/src/main/AndroidManifest.xml`, és a `<manifest>` tag
alá, a `<application>` elé írd be:

```xml
<uses-permission android:name="android.permission.RECORD_AUDIO" />
```

## 3. iOS projekt létrehozása (Mac + Xcode szükséges hozzá)

```bash
npx cap add ios
npx cap sync ios
npx cap open ios
```

Xcode-ban:
- Állítsd be a **Signing & Capabilities** alatt a saját Apple fejlesztői
  csapatodat (Team)
- **Info.plist**-be vedd fel a mikrofon-engedély szövegét (enélkül az
  App Store rögtön elutasítja):

```xml
<key>NSMicrophoneUsageDescription</key>
<string>A stúdió a mikrofont a saját vokálfelvételedhez használja.</string>
```

- **Product > Archive** — ezzel készül a App Store Connect-be feltölthető build

## 4. Ha módosítod az index.html-t

Minden változtatás után:

```bash
npx cap sync
```

Ez átmásolja a frissített `www/index.html`-t mindkét natív projektbe.

## 5. Ikon és splash screen

A Capacitor tud automatikusan ikont/splash képet generálni egyetlen
forrásképből:

```bash
npm install -D @capacitor/assets
npx capacitor-assets generate
```

Ehhez kell egy `assets/icon.png` (1024×1024) és `assets/splash.png`
(2732×2732) képed — ha akarod, ezeket is meg tudom veled tervezni.

## 6. Store-feltöltés röviden

- **Google Play:** Play Console → új app → adatvédelmi kérdőív
  (mikrofon-hozzáférést jelezni kell) → aláírt AAB feltöltése → belső
  teszt → nyilvános kiadás. Átfutás jellemzően pár óra – 1-2 nap.
- **App Store:** App Store Connect → új app → build feltöltése
  Xcode-ból → adatvédelmi nyilatkozat (mikrofon) → review beküldés.
  Átfutás jellemzően 1-3 nap, és Apple szigorúbban nézi az első
  beküldést.

Mivel az appod **semmilyen adatot nem küld sehova** (minden helyben,
a telefonon fut), az adatvédelmi nyilatkozat mindkét store-nál
egyszerű: csak a mikrofon-hozzáférést kell jelezni, adatgyűjtés
nincs.

## Amit én itt tudok segíteni

Ebben a beszélgetésben nem tudok Android Studio-t/Xcode-ot futtatni
vagy csomagot telepíteni internet nélkül, de tudok:
- további funkciókat, UI-t fejleszteni az `index.html`-ben
- app ikont / splash képet tervezni
- store-leírást, screenshotokat, marketing szöveget megírni
- ha elakadsz egy konkrét hibaüzenetnél a buildelés közben, azt is
  átnézzük együtt
