---
lang: de
order: 2
title: Fälschungssicher & Verifizierbar
icon: ic:outline-verified
---

Beim Erstellen der Ausweise werden Name, Mitgliedsnummer, Verband und Status **signiert** in den QR-Code geschrieben.
Dieser enthält außerdem die URL zu einem Passulo-Server, auf dem der öffentliche Schlüssel des Verbands liegt.

Beim Auslesen der Daten wird der öffentliche Schlüssel vom Server geladen und die Daten damit verifiziert.

Die Signatur nutzt Public-Key-Kryptografie mit EdDSA-Signaturen.

<a class="btn btn-outline-primary" href="/spec" role="button" target="_blank">
<span class="iconify-inline" data-icon="ic:outline-file-copy"></span> Zur Spezifikation</a>