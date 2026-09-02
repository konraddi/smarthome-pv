# Security & Privacy

Stand: 2026-09-02

## Repository-Status

`konraddi/smarthome-pv` ist beim Projektstart öffentlich.

Das beeinflusst, welche Betriebsdaten dauerhaft gespeichert werden dürfen.

## Niemals in Git speichern

- Passwörter
- Home-Assistant Long-Lived Access Tokens
- MQTT-/Broker-Passwörter
- API-Keys
- OAuth-Secrets
- private Schlüssel/Zertifikat-Private-Keys
- WLAN-Zugangsdaten
- Cloud-Credentials
- Recovery Codes
- sensible Support- oder Accountdaten

## Im öffentlichen Repo ebenfalls vermeiden

- exakte externe Home-Assistant-Zugangs-URLs, wenn nicht zwingend erforderlich
- private IP-/Netzwerktopologie
- Seriennummern/MAC-Adressen ohne technischen Grund
- genaue Zeitreihen, die Anwesenheit/Tagesabläufe erkennen lassen
- Bewegungs-/Tür-/Präsenzdaten
- unnötig genaue Verbrauchsprofile

## YAML und Secrets

Wenn später Home-Assistant-YAML übernommen wird:

- Secret-Werte durch `!secret`, Platzhalter oder dokumentierte Secret-Namen ersetzen.
- Keine Credentials aus bestehenden Dateien in Git kopieren.
- Vor jedem Commit nach Token-/Passwortmustern prüfen.

## Screenshots und Logs

Vor Persistenz prüfen auf:

- externe URLs
- Tokens
- IP-Adressen
- Device IDs/Seriennummern
- persönliche Namen/Standorte
- QR-Codes

## Privacy-Gate für Zeitreihen

Automatischer GitHub-Export von Klima-/Energiezeitreihen bleibt blockiert, bis bewusst entschieden wurde:

- Repo privat oder öffentlich?
- welche Sensoren dürfen exportiert werden?
- welche Auflösung ist nötig?
- wie lange werden Rohdaten gehalten?

## Sicherheitsprinzip

Dokumentationskomfort rechtfertigt nicht, Zugangsdaten oder eine unnötig detaillierte Heimnetz-/Anwesenheitskarte öffentlich zu machen.