---
layout: pure_markdown
title: Architektur
permalink: /architecture/
menu: architecture
lang: de
---

# Architektur

Passulo besteht aus mehreren Software-Komponenten.

## PassCreator

Der PassCreator ist eine Anwendung die eine CSV-Datei mit Mitgliederdaten liest und für jedes Mitglied einen Apple Wallet Pass (`pkpass`) erstellt.

Benötigt werden hierfür:

1. **Ein Private/Public Keypair basierend auf Ed25519**

   Dies kann mit `openssl` erstellt werden:
   
   ```shell
   $ openssl genpkey -algorithm ed25519 -outform PEM -out private.pem
   $ openssl pkey -in private.pem -pubout -out public.pem
   ```
   
   Die Dateien sehen wie folgt aus:
   
   ```text
   -----BEGIN PRIVATE KEY-----
   MC4CAQAwBQYDK2VwBCIEIE+Yg8vj7GtRUe8pzkarIn6Uvbktu1INR58hCasc9202
   -----END PRIVATE KEY-----
   ```
   
   ```text
   -----BEGIN PUBLIC KEY-----
   MCowBQYDK2VwAyEAJLJnj3rUwrTk3lhbc9uPxUlhivbtX8Op71cDcza1HWI=
   -----END PUBLIC KEY-----
   ```

2. **Der Public Key muss beim Passulo Server registriert werden.**

   Dies ist ein manueller Prozess, da der Betreiber des Servers verifizieren muss, 
   dass der Ersteller des Schlüssels befugt ist, Ausweise für seine Organisation auszustellen.

3. **Ein `Pass Type ID Certificate` von Apple**

   Hierfür ist ein Apple-Developer-Account nötig.

   Zunächst muss ein "identifier" erstellt werden: https://developer.apple.com/account/resources/identifiers/list/passTypeId.

   Danach kann hier ein Zertifikat beantragt werden: https://developer.apple.com/account/resources/certificates/add.

4. **Das Apple WWDR CA G4 Zertifikat**

   Dies kann auf https://www.apple.com/certificateauthority/AppleWWDRCAG4.cer heruntergeladen werden.

5. **Logo, Icon und Stip-Images**
   
   In einem `template`-Ordner müssen `logo.png`, `icon.png` und optional `strip.png` gespeichert werden. 
   Diese sollten auch als `@2x` und `@3x`-Varianten verfügbar sein.
   Die Dimensionen (für die einfache Variante) betragen:
   
   * `logo.png` 160 x 50 points (wird vorne auf dem Pass angezeigt)
   * `icon.png` 29 x 29 points (wird z.B. in Notifications angezeigt)
   * `strip.png` 375 x 123 points (Hintergrundbild)

   Beispiele finden sich in https://github.com/passulo/PassCreator/tree/main/template.
   Weitere Erklärungen findet man auf https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/PassKit_PG/Creating.html.

6. **Eine Konfigurations-Datei**

   siehe https://github.com/passulo/PassCreator/blob/main/src/main/resources/passulo.conf

7. **Eine CSV-Datei mit den Mitgliederdaten**

   zum Beispiel https://github.com/passulo/PassCreator/blob/main/src/main/resources/members.csv

PassCreator erstellt für jeden Eintrag in den Mitgliederdaten eine Datei mit dem Namen `<mitgliedsnummer>-<name>.pkpass`. 
Diese kann auf jedem Mac, iPhone oder iPad oder auf Android-Geräten mit der [WalletPasses](https://play.google.com/store/apps/details?id=io.walletpasses.android) App geöffnet werden. 

