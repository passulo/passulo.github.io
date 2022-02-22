---
layout: pure_markdown
title: Spezifikation
permalink: /spec/
menu: spec
lang: de
---

# Spezifikation des QR-Codes

Der QR-Code ist eine ISO 8859-1-codierte URL, die auf eine Instanz des Passulo-Servers zeigt und einen Code, eine Version, eine Signatur und eine Key-ID als Query-Parameter enthält:

```shell
https://app.passulo.com/?code=<token>&v=1&sig=<signature>&kid=<keyid>
```

### Token

Der Token eine `base64url` (nach [RFC 4648](https://en.wikipedia.org/wiki/Base64#The_URL_applications)) codierte, binäre repräsentation einer Protobuf-Nachricht.
Die Protobuf-Definition befindet sich in [github.com/passulo/Token](https://github.com/passulo/Token), zusammen mit Bindings für C++, Java, JavaScript, Kotlin, Objective C, PHP, Python, Ruby, Scala und Swift.

Über die Kommandozeilen-Befehle `basenc` und `protoc` können Token untersucht werden:

```text
CiQ2MDViZWQyMy03ODJkLTRhMmItYTFjYy1mZWQwMDYzMzRjNTkSBVRhbmphIgVMYW5nZSoBZjIGNDMyMzI0OgRHb2xkQiFUZWNobmlzY2hlIFVuaXZlcnNpdGVpdCBFaW5kaG92ZW5KEmxhbmdlQGVpbmRob3Zlbi5ubFITKzMxICgwKSA0MCAyNDcgNDc2NFoMSGFtYnVyZ0BXb3JrYgYI_5nDnQZqBgj_q4DkAw==
```

```shell
$ cat token.txt | basenc --base64url --decode | protoc --decode com.passulo.v1.Token com/passulo/token.proto
id: "605bed23-782d-4a2b-a1cc-fed006334c59"
firstName: "Tanja"
lastName: "Lange"
gender: "f"
number: "432324"
status: "Gold"
company: "Technische Universiteit Eindhoven"
email: "lange@eindhoven.nl"
telephone: "+31 (0) 40 247 4764"
association: "Hamburg@Work"
validUntil {
  seconds: 1672531199
}
memberSince {
  seconds: 1015027199
}
```


> Hintergrund: Die Formate Protobuf und Ed25519 wurden gewählt, um den Token möglichst klein zu halten. 
> Während QR-Code _theoretisch_ bis zu 2953 Bytes speichern können, ist das Scannen von Codes mit über 400 Zeichen in der Praxis nicht immer erfolgreich.
> Hinzu kommt, dass der Code i.d.R. auf einem Handy-Display angezeigt wird und also nicht sehr groß ist.
> Es ist deshalb darauf zu achten, die Gesamtgröße des Tokens nicht unnötig aufzublähen.

### Version

Der `v` Parameter ist die Version des Tokens.

### Signatur

Der `sig` Parameter ist die `base64url` codierte Ed25519 Signatur über die Byte-Repräsentation des Tokens.

Ed25519 ist in den meisten Programmiersprachen bereits verfügbar, 
in Java ab Version 15 (in `java.security.Signature`), in Swift ab iOS 13 (in `CryptoKit.Curve25519`).

Alternativ stell [libsodium](https://libsodium.gitbook.io/doc/bindings_for_other_languages) Bindings für fast jede Sprache bereit.

### Key-ID

Der `kid` (für key-id) Parameter ist der Klartext-Name des Public Keys der ausstellenden Organisation. 
Unter diesem Identifier stellt der Passulo-Server (hinter der jeweiligen URL) den Key sowie die im Token erlaubten Namen bereit.

### Server-URL

In der "Chain of Trust" bauen alle Schritte aufeinander auf, sodass die Client-Applikation am Ende nur noch dem Server in der URL des QR-Codes vertrauen muss.
Hierfür bietet es sich an, entweder eine feste Liste an Servern, denen vertraut wird, auszuliefern, oder den Benutzer des Clients darauf hinzuweisen,
welcher Server benutzt wird.