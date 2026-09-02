# Decisions

Stand: 2026-09-02

Nur tatsächlich getroffene oder aus dem bisherigen Projektstand klar bestätigte Entscheidungen.

## DEC-001 – GitHub als kanonische Projektakte

Datum: 2026-09-02

Entscheidung:

`konraddi/smarthome-pv` wird die kanonische beschreibbare Source of Truth. ChatGPT-Projektquellen werden nicht als parallele Dateikopie gepflegt.

## DEC-002 – Review-Daten nicht doppelt pflegen

Datum: 2026-09-02

Entscheidung:

Review-spezifische Testmethodik, Claims, Testmessreihen und Publikationsarbeit verbleiben in `konraddi/Heise-Testberichte`. Dieses Repo dokumentiert den realen Smart-Home-/PV-Betrieb und seine Integrationen.

## DEC-003 – Venus-D-SOC darf bei Ausfall nicht fortgeschrieben werden

Datum: 2026-08/09, kanonisch übernommen 2026-09-02

Entscheidung:

Wenn der Live-SOC des Marstek Venus D nicht verfügbar bzw. nicht frisch ist, darf der letzte bekannte SOC nicht als aktueller Steuerwert für die SolarFlow-2400-AC-Logik verwendet werden.

Begründung:

Ein alter SOC hatte zu einer falschen Regelentscheidung beigetragen; Teile des Überschusses gingen ins Netz.

## DEC-004 – Anzucht-Shelly ist aktuell Verbrauch

Datum: 2026-08-31, kanonisch übernommen 2026-09-02

Entscheidung:

Der früher einmal für einen Mikrowechselrichter verwendete Shelly wird in seiner aktuellen Nutzung an der Anzucht als Verbrauchs-/`consumed`-Zähler behandelt. Historische frühere Erzeugungsnutzung ändert die aktuelle Semantik nicht.

## DEC-005 – Outdoor Plug als Verbrauch erfassen

Datum: 2026-08-31, kanonisch übernommen 2026-09-02

Entscheidung:

Der Outdoor Plug wird ebenfalls in die Verbrauchslogik aufgenommen.

## DEC-006 – Kleine Rest-Einspeisung als Regelreserve

Datum: aus bestehenden Automationen übernommen, kanonisch 2026-09-02

Entscheidung:

Bestimmte Speicherautomationen dürfen bewusst auf ungefähr 200 W Rest-Netzeinspeisung regeln, statt exakt 0 W anzustreben. Das ist eine automation-spezifische Leitlinie, keine allgemeine feste Systemgrenze.

## DEC-007 – HEMS und HA nicht unkoordiniert auf dieselben Limits

Datum: aus bestehendem Zendure-Projektstand übernommen, kanonisch 2026-09-02

Entscheidung:

Hersteller-HEMS/App-Automatik und Home Assistant sollen nicht parallel unkoordiniert dieselben Lade-/Entlade-Limits regeln.

## DEC-008 – 1-Minuten-Daten für Klima ausreichend

Datum: 2026-09-01, kanonisch übernommen 2026-09-02

Entscheidung:

Für die geplante Klimaauswertung ist ungefähr 1-Minuten-Auflösung ausreichend.

## DEC-009 – Keine sensiblen Zeitreihen automatisch ins öffentliche Repo

Datum: 2026-09-02

Entscheidung:

Solange `smarthome-pv` öffentlich ist, werden detaillierte Klima-/Nutzungs-/Anwesenheitszeitreihen nicht automatisch persistiert. Erst Privacy-/Visibility-Entscheidung treffen.