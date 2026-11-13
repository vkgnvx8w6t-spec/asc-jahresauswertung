# ASC · Jahresauswertung – Update-Kanal

Öffentlicher Update-Kanal für die installierte ASC-Jahresauswertung.

Hier liegen ausschließlich maschinenlesbare gemeinsame Einstellungen, öffentliche Ergebnisdaten und verschlüsselte, signierte Programmpakete. Lesbarer Systemcode, private Fotos, interne Tabellen, persönliche Profile und Schlüssel werden nicht veröffentlicht.

## Installation und Updates

Die Anwendung wird einmal über das private Installationspaket installiert. Danach prüft der Desktop-Starter bei jedem Öffnen diesen Kanal. Ab Programmstand 4.21 wird er auch im laufenden Betrieb normalerweise alle 15 Sekunden über die öffentlichen Git-Referenzen geprüft. Nur geänderte Dateien werden geladen. Nach Signatur-/Integritätsprüfung und isoliertem Probestart übernimmt ein kontrollierter Serverneustart auf demselben Port das Update, sobald laufende Importe und Exporte abgeschlossen sind. Das Fenster lädt automatisch neu. Ein bereits laufender Stand 4.20 benötigt zunächst einen normalen Neustart. Lokale Einstellungen bleiben erhalten; ausdrücklich geänderte managed-Konfigurationen können sie aktualisieren. Ohne Verbindung startet die letzte lokal verfügbare Version. Fehlerhafte oder nicht authentische Programmpakete werden zurückgewiesen; ein Startfehler führt zur vorherigen Version zurück.

`releases/current.json` benennt das aktuelle Programmpaket unter `patches/` und enthält SHA-256-Prüfsummen sowie eine Ed25519-Signatur. Der Client entschlüsselt das AES-256-GCM-Paket ausschließlich lokal mit den Installationsschlüsseln. Persönliche Fotos verbleiben im privaten Installationspaket und auf dem jeweiligen Rechner.

Die Verschlüsselung verhindert die Klartext-Veröffentlichung des Systemcodes. Weil der Entschlüsselungsschlüssel in der verteilten Anwendung vorhanden sein muss, ist dies kein vollständiger Schutz vor Reverse Engineering. Der private Signierschlüssel wird nicht verteilt; nur der Herausgeber kann gültige neue Programmpakete erzeugen.

Fotos, die private ASC-Excel-Vorlage und hochgeladene Vorlagen bleiben ausschließlich im privaten Installationspaket bzw. lokalen Profil. Sie sind auch aus dem verschlüsselten Programmpatch ausgeschlossen. Eine neue private Basis kann native Bibliotheken ergänzen; bestehende Python-Patches ersetzen die ursprüngliche Python-/Native-Runtime oder den Bootstrap nicht beliebig. PDF-Exporte benötigen die PDF-/Bildbibliotheken der Basisinstallation 4.20 oder eine entsprechend ausgestattete lokale Python-Installation. Ein neuerer installierter Basisstand wird auch offline nicht durch einen älteren lokalen oder entfernten Patch zurückgesetzt.

## Gemeinsame Einstellungen

`config/system.json` ist direkt auf GitHub bearbeitbar. Eine Änderung daran benötigt kein neues verschlüsseltes Programmpaket. Die Anwendung lädt die Konfiguration beim nächsten Start zusammen mit den öffentlichen Daten.

`schemaVersion` bleibt `1`. `revision` ist eine frei vergebene Bezeichnung. Für `design.mode` und `scoring.mode` gelten:

- `defaults`: nur bei einem noch nicht angelegten Profil anwenden.
- `managed`: angegebene Werte einmalig übernehmen, sobald sich dieser Konfigurationsabschnitt ändert. Lokale Anpassungen bleiben danach bis zur nächsten zentralen Änderung erhalten.
- `preserve`: vorhandene Einstellungen unverändert lassen.

`design.values` enthält allgemeine Designwerte. `design.profiles` erlaubt getrennte Werte für `system`, `light`, `dark` und jeweils `clear`, `glass`, `photo`. Bildauswahl und Diashow-Fortschritt werden niemals zentral verändert.

`scoring.profiles.old` und `scoring.profiles.v2` unterstützen `pointFactors`, `pointCutoffs`, `qualificationBonuses` und `qualityPreset`. Alte und neue Wertung bleiben getrennt. Beispielsweise:

```json
{"schemaVersion":1,"revision":"2026-11-a","scoring":{"mode":"managed","profiles":{"v2":{"pointFactors":{"5":25},"qualityPreset":{"nationalBonus":100},"qualificationBonuses":{"national":100,"europe":500,"world":750,"olympics":1000}}}},"data":{"mode":"newer"}}
```

`qualificationBonuses.national` steuert den normalen nationalen Jahresnormbonus und erlaubt 50, 75 oder 100 Punkte. `qualityPreset.nationalBonus` ist der getrennte nationale Bonus der Qualitätswertung. Beide gelten dauerhaft für das jeweilige Wertungsprofil; `old` und `v2` werden nicht gegenseitig überschrieben. Ab der zweiten nationalen Teamnorm halbiert die Qualitätswertung ihren Bonus, Einzelnormen behalten ihn vollständig. Europa/Welt/Olympia stehen separat unter `qualificationBonuses`.

`data.mode` ist `newer` (nur neuere öffentliche Daten), `managed` (geänderte veröffentlichte Daten gezielt übernehmen) oder `preserve`. Optional beschränkt `data.files` die Übernahme, etwa auf `["data/2026.json"]`. Vor Ersetzungen legt die Anwendung lokal Sicherungen an. Interne Tabellen, Sonderfälle, private Vorlagen und Fotos sind davon ausgenommen.

Große öffentliche Datenstände werden platzsparend als `.json.gz` veröffentlicht und vom Client automatisch zu JSON entpackt. Normale `.json`-Dateien werden weiterhin unterstützt; liegt beides vor, verwendet die Anwendung die unkomprimierte Datei. `config/system.json` bleibt immer direkt lesbar und bearbeitbar. Geänderte Dateien werden anhand ihrer Git-Hashes erkannt: Bei einer reinen Konfigurationsänderung werden vorhandene Datendateien und der unveränderte signierte Programmpatch aus dem lokalen Cache verwendet.

## Programmänderungen

Programmänderungen werden lokal getestet, anschließend vom Herausgeber verschlüsselt und signiert und als neues Paket veröffentlicht. Die Installationsschlüssel und der private Herausgeberschlüssel dürfen nie in dieses Repository gelangen. Dieses Repository enthält keine Build-Quellen.
