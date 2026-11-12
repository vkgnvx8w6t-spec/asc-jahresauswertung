# ASC � Jahresauswertung � Update-Kanal

�ffentlicher Update-Kanal f�r die installierte ASC-Jahresauswertung.

Hier liegen ausschlie�lich maschinenlesbare gemeinsame Einstellungen, �ffentliche Ergebnisdaten und verschl�sselte, signierte Programmpakete. Lesbarer Systemcode, private Fotos, interne Tabellen, pers�nliche Profile und Schl�ssel werden nicht ver�ffentlicht.

## Installation und Updates

Die Anwendung wird einmal �ber das private Installationspaket installiert. Danach pr�ft der Desktop-Starter bei jedem �ffnen diesen Kanal. Neue �nderungen werden automatisch �bernommen. Ohne Verbindung startet die letzte lokal verf�gbare Version. Fehlerhafte oder nicht authentische Programmpakete werden zur�ckgewiesen; ein Startfehler f�hrt zur vorherigen Version zur�ck.

`releases/current.json` benennt das aktuelle Programmpaket unter `patches/` und enth�lt SHA-256-Pr�fsummen sowie eine Ed25519-Signatur. Der Client entschl�sselt das AES-256-GCM-Paket ausschlie�lich lokal mit den Installationsschl�sseln. Pers�nliche Fotos verbleiben im privaten Installationspaket und auf dem jeweiligen Rechner.

Die Verschl�sselung verhindert die Klartext-Ver�ffentlichung des Systemcodes. Weil der Entschl�sselungsschl�ssel in der verteilten Anwendung vorhanden sein muss, ist dies kein vollst�ndiger Schutz vor Reverse Engineering. Der private Signierschl�ssel wird nicht verteilt; nur der Herausgeber kann g�ltige neue Programmpakete erzeugen.

## Gemeinsame Einstellungen

`config/system.json` ist direkt auf GitHub bearbeitbar. Eine �nderung daran ben�tigt kein neues verschl�sseltes Programmpaket. Die Anwendung l�dt die Konfiguration beim n�chsten Start zusammen mit den �ffentlichen Daten.

`schemaVersion` bleibt `1`. `revision` ist eine frei vergebene Bezeichnung. F�r `design.mode` und `scoring.mode` gelten:

- `defaults`: nur bei einem noch nicht angelegten Profil anwenden.
- `managed`: angegebene Werte einmalig �bernehmen, sobald sich dieser Konfigurationsabschnitt �ndert. Lokale Anpassungen bleiben danach bis zur n�chsten zentralen �nderung erhalten.
- `preserve`: vorhandene Einstellungen unver�ndert lassen.

`design.values` enth�lt allgemeine Designwerte. `design.profiles` erlaubt getrennte Werte f�r `system`, `light`, `dark` und jeweils `clear`, `glass`, `photo`. Bildauswahl und Diashow-Fortschritt werden niemals zentral ver�ndert.

`scoring.profiles.old` und `scoring.profiles.v2` unterst�tzen `pointFactors`, `pointCutoffs`, `qualificationBonuses` und `qualityPreset`. Alte und neue Wertung bleiben getrennt. Beispielsweise:

```json
{"schemaVersion":1,"revision":"2026-11-a","scoring":{"mode":"managed","profiles":{"v2":{"pointFactors":{"5":25},"qualityPreset":{"nationalBonus":100},"qualificationBonuses":{"europe":500,"world":750,"olympics":1000}}}},"data":{"mode":"newer"}}
```

`data.mode` ist `newer` (nur neuere �ffentliche Daten), `managed` (ge�nderte ver�ffentlichte Daten gezielt �bernehmen) oder `preserve`. Optional beschr�nkt `data.files` die �bernahme, etwa auf `["data/2026.json"]`. Vor Ersetzungen legt die Anwendung lokal Sicherungen an. Interne Tabellen, Sonderf�lle und Fotos sind davon ausgenommen.

## Programm�nderungen

Programm�nderungen werden lokal getestet, anschlie�end vom Herausgeber verschl�sselt und signiert und als neues Paket ver�ffentlicht. Die Installationsschl�ssel und der private Herausgeberschl�ssel d�rfen nie in dieses Repository gelangen. Dieses Repository enth�lt keine Build-Quellen.
