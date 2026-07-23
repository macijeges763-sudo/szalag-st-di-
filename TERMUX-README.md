# Szalag app build — Termux + GitHub, Android Studio nélkül

Ez az út: a **kódot Termuxból tolod fel GitHubra**, a **buildelést
GitHub Actions végzi a felhőben**, te pedig a végén letöltöd a kész
APK-t és telepíted a telefonodra. Nem kell Android Studio, nem kell
Gradle a telefonon.

## 0. Ha már van másik projekted Termuxban

Nyugodtan dolgozz egy külön mappában (pl. `~/szalag-app`) — a git és
npm parancsok csak az adott mappára vonatkoznak, nem nyúlnak a másik
projektedhez. Egyetlen dolog, amire érdemes figyelni: ha a másik
AI-asszisztensed Node.js-t futtat és foglal egy portot (pl. 3000,
8080), ez a projekt nem indít saját szervert, szóval nem fog
ütközni — csak git/npm parancsokat és a GitHub Actions felhő-buildet
használjuk.

## 1. Termux előkészítése

```bash
pkg update && pkg upgrade -y
pkg install git nodejs-lts openssh -y
git config --global user.name "A neved"
git config --global user.email "az emailed"
```

## 2. GitHub repó létrehozása

A telefonos böngészőből (github.com):
1. Jelentkezz be → jobb felül **+** → **New repository**
2. Nevezd el pl. `szalag-studio`, legyen **Private** vagy **Public**, ahogy szeretnéd
3. Ne pipáld be a README/gitignore automatikus létrehozását — üresen hozd létre

## 3. Personal Access Token (a jelszó helyett kell)

GitHub jelszóval már nem lehet pusholni parancssorból. Kell egy token:
1. github.com → jobb felül profilkép → **Settings**
2. **Developer settings** → **Personal access tokens** → **Tokens (classic)**
3. **Generate new token**, adj neki `repo` jogot, másold ki a tokent (csak egyszer látod)

## 4. A projekt feltöltése Termuxból

Ha már kicsomagoltad ide a `mobile-app` mappát a telefonodon
(pl. Letöltések), lépj be:

```bash
cd ~/storage/downloads/mobile-app    # vagy ahova kicsomagoltad
git init
git add -A
git commit -m "Szalag stúdió - kezdeti verzió"
git branch -M main
git remote add origin https://github.com/FELHASZNALONEV/szalag-studio.git
git push -u origin main
```

Amikor jelszót kér, a **tokent** írd be jelszó helyett.

> Ha a `~/storage/downloads` nem elérhető, előbb futtasd:
> `termux-setup-storage` és engedélyezd a hozzáférést.

## 5. A build automatikusan elindul

A repóban lévő `.github/workflows/build-android.yml` fájl miatt
minden `git push` után a GitHub automatikusan elindítja a buildet:

1. Menj a repódba a böngészőben → **Actions** fül
2. Látni fogod a futó workflow-t (pár percig tart)
3. Ha zöld pipa lesz, kattints rá → lent az **Artifacts** részben ott
   lesz a `szalag-debug-apk` — töltsd le (egy .zip-ben lesz benne az APK)

## 6. Telepítés a telefonra

1. Csomagold ki a letöltött zip-et → benne az `app-debug.apk`
2. Nyisd meg a fájlkezelőből → engedélyezd az "Ismeretlen forrásból
   telepítés"-t, ha kéri → Telepítés

Ezzel már egy valódi, telepített Android app fut a telefonodon,
Android Studio és PC nélkül.

## 7. Ha módosítasz valamit

```bash
git add -A
git commit -m "változtatás leírása"
git push
```

— minden push újra lefuttatja a buildet, és készül egy friss APK.

## Fontos: ez egy debug build, nem Play Store-ra kész csomag

A workflow egy **debug APK**-t készít, ami telepíthető és tesztelhető
a telefonodon, de a Google Play-re **aláírt release build (AAB)**
kell. Ha odáig eljutunk, a workflow-t ki tudjuk egészíteni: a
signing key-t GitHub Secrets-ként tárolva, biztonságosan, a felhőben
történne az aláírás is — szólj, ha ezt a lépést is szeretnéd.
