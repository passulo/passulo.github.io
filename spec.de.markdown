---
layout: pure_markdown
title: Spezifikation
permalink: /spec/
menu: spec
lang: de
---

# Spezifikation des QR-Codes

Der QR-Code ist eine ISO 8859-1-codierte URL, die auf eine Instanz des Passulo-Servers zeigt und einen Code, eine Signatur und eine Key-ID als Query-Parameter enthält:

```shell
https://app.passulo.com/?code=<token>&sig=<signature>&kid=<keyid>
```

### Token

Der Token eine `base64url` (nach [RFC 4648](https://en.wikipedia.org/wiki/Base64#The_URL_applications)) codierte, binäre repräsentation einer Protobuf-Nachricht.
Die Protobuf-Definition befindet sich in https://github.com/passulo/Token, zusammen mit Bindings für C++, Java, JavaScript, Kotlin, Objective C, PHP, Python, Ruby, Scala und Swift.

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

### Signatur

### Key-ID