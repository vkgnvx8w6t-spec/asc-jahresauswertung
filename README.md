# ASC Jahresauswertung · Update-Kanal

Gemeinsame maschinenlesbare Einstellungen, öffentliche Sportdaten und verschlüsselte, signierte Programmpatches. Private Fotos, interne Tabellen, Installationszugänge und lesbarer Programmcode bleiben lokal.

Ab Version 4.32 prüft die Anwendung den Kanal auch während des Betriebs etwa alle drei Sekunden. Änderungen werden inkrementell geladen. Reine Einstellungs- und deklarative UI-Änderungen werden ohne Fensterneustart übernommen; Codeänderungen erst nach Signaturprüfung und isoliertem Probestart durch einen kontrollierten internen Serverwechsel. Offline bleibt der letzte geprüfte Stand nutzbar.

## Gemeinsame Einstellungen

`config/system.json` enthält `schemaVersion: 1`, eine freie `revision` sowie `design`, `scoring`, `data` und optional `ui`. Für Design/Wertung gelten `defaults` (nur frischer Arbeitsstand), `managed` (explizite zentrale Änderung einmal übernehmen) und `preserve`. Nicht angegebene lokale Werte bleiben erhalten. `data.mode` ist `newer`, `managed` oder `preserve`.

Designprofile bleiben getrennt nach `system`, `light`, `dark` und jeweils `clear`, `glass`, `photo`. Bildauswahl und Diashow-Fortschritt bleiben lokal. Wertungsprofile `old` und `v2` unterstützen `pointFactors`, `pointCutoffs`, `qualificationBonuses`, `qualityPreset` unabhängig voneinander.

## Geräteprofile

Jeder PC erhält eine zufällige lokale Geräte-ID. Unter `profiles/<UUID>.json` steht sein öffentliches Einstellungsprofil. Geräte speichern und empfangen Änderungen über den geschützten Profilserver, der nach einmaliger GitHub-App-Freigabe des Betreibers die Dateien beidseitig synchronisiert. Empfänger brauchen weder GitHub-Anmeldung noch Schreibtoken. Gerätezugänge liegen Windows-geschützt lokal; sie werden nicht mit dem Versandprofil kopiert.

Ein Geräteprofil hat `schemaVersion: 1`, `deviceId`, `revision`, `settings` und optional `ui`, `program`. Unter `settings` stehen `design`, `scoring`, `workspace`, `years`. Private Fotos, Suchtexte, lokale Pfade, Anmeldeinformationen und Diashow-Positionen gehören nicht hinein. Bei gleichzeitig geänderten unterschiedlichen Feldern werden beide Seiten zusammengeführt; ein ausdrücklicher zentraler Konflikt gewinnt und wird gemeldet.

## UI- und Programmänderungen

`ui.variables` setzt sichere CSS-Variablen. `ui.text`, `ui.attributes` und `ui.styles` richten sich ausschließlich an eindeutige IDs. Erlaubt sind Texte, zugängliche Beschriftungen und freigegebene Layout-/Farbwerte. Kein ausführbarer JavaScript-Text oder HTML wird akzeptiert. Gemeinsame UI-Angaben werden vor gerätespezifischen angewendet.

`releases/current.json` referenziert ein AES-256-GCM-verschlüsseltes und Ed25519-signiertes Paket in `patches/`. `program.manifest` in einem Geräteprofil kann ein zusätzliches signiertes Paket referenzieren. Dessen Signatur und Verschlüsselung sind zusätzlich an die Geräte-ID gebunden; nur ausgewählte geänderte Quelldateien werden überschrieben. Globale Updates behalten den passenden Gerätepatch. Entfernen des Gerätepatches stellt die globale Fassung wieder her.

Der private Signierschlüssel wird niemals verteilt. Die Verschlüsselung verhindert die Klartextveröffentlichung, jedoch keinen vollständigen Reverse-Engineering-Zugriff aus einer Installation. Private Bilder, Excel-Vorlage und native Laufzeit werden nicht als öffentliche Programmpatches verteilt.
