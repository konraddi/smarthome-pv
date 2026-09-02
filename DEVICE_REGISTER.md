# Device Register

Stand: 2026-09-02
Inventur-Evidenz: letzter vorliegender `configuration.yaml`-Snapshot vom 2026-08-31 + letzter vorliegender SolarFlow-2400-AC-Automationssnapshot vom 2026-08-27 + bestätigte Nutzerangaben.

Physische Geräte und ihre Projektrolle. Entity-IDs gehören nach `ENTITY_REGISTER.md`; Integrationen/Protokolle nach `INTEGRATION_REGISTER.md`.

## Statusklassen

- `ACTIVE / REPORTED` – vom Nutzer als aktuell im Betrieb berichtet.
- `CONFIGURED SNAPSHOT` – im letzten vorliegenden HA-Konfigurationssnapshot referenziert; beweist nicht automatisch, dass die Hardware heute online/aktiv ist.
- `ACTIVE / REPORTED + CONFIGURED` – sowohl als aktiv berichtet als auch im letzten Konfigurationssnapshot nachweisbar.
- `KNOWN / LIVE VERIFY` – Gerät gehört zum Projektbestand, aktueller Betriebs-/HA-Status ist aber nicht frisch belegt.
- `LEGACY/SECONDARY PATH / LIVE VERIFY` – Konfiguration existiert noch, aktive physische Rolle ist unklar.

## 1. Netz-/Unterverteilungsmessung

| ID | Gerät | Rolle / Messpunkt | Status | Technische Zuordnung / Bemerkung |
|---|---|---|---|---|
| DEV-METER-001 | Shelly Pro 3EM | zentraler Haus-/Netzanschlusspunkt, 3-phasige Netto-Betrachtung | ACTIVE / REPORTED + CONFIGURED | Roh-Entities mit Gerätekennung `2cbcbbb84c18`; Phasen A/B/C werden zum kanonischen Netto-Sensor summiert. Für Hausregelung saldierende Summe verwenden. |
| DEV-METER-002 | Shelly 3EM Gen3 / `Shelly3EM63G3 Garage Hinten` | dreiphasiger Messpunkt „Garage Hinten“ | CONFIGURED SNAPSHOT / LIVE VERIFY | Roh-Entities mit Gerätekennung `28372f367218`; exakter physischer Stromkreis und ob primär PV-Unterverteilung oder allgemeine Garage: `VERIFY`. Es existieren Netto-, Bezug-, Einspeise- und integrierte Energiepfade je Phase und gesamt. |

## 2. PV-Wechselrichter / Erzeuger

| ID | Gerät | Rolle | Status | Technische Zuordnung / Bemerkung |
|---|---|---|---|---|
| DEV-PV-001 | Hoymiles HM-1500-4T | PV-Erzeugung | ACTIVE / REPORTED | Modul-/Stringzuordnung noch offen. |
| DEV-PV-002 | Hoymiles HMS-800W-2T | PV-Erzeugung | ACTIVE / REPORTED + CONFIGURED METER PATH | Eigener Shelly-Messpfad `shellyplugsg3_8cbfea910720_*`; negative Rohleistung wird im Template als positive PV-Leistung dargestellt; Erzeugungsenergie über `energy_returned`. |
| DEV-PV-003 | Hoymiles HMS-2000-4T | PV-Erzeugung | ACTIVE / REPORTED | Modul-/Stringzuordnung noch offen. |
| DEV-PV-004 | SMA String-Wechselrichter | PV-Erzeugung | ACTIVE / REPORTED | ca. 4,5 kW laut Nutzer; exaktes Modell, Strings und HA-Entities `UNKNOWN`. |

### PV-Gesamtbestand

- 29 Module – `REPORTED / USER`.
- ungefähr 8–8,5 kWp Gesamtleistung – `REPORTED / USER`.
- Exakte Modulmodelle, Wp je Modul, Ausrichtungen, String-/WR-Zuordnungen: `INVENTORY GAP`.

## 3. Hausspeicher / Energiesysteme

| ID | Gerät | Kapazität / Betriebsfenster | Rolle | Status | Technische Zuordnung / Bemerkung |
|---|---|---|---|---|---|
| DEV-BAT-001 | Zendure SolarFlow 2400 AC + AB3000X | 2,88 kWh; im aktuellen Gesamt-SOC-Template 10–90 % Nutzbereich | primärer AC-Speicher / PV-Überschussladen / Nachtentladung | ACTIVE / REPORTED + CONFIGURED | Steuerpfad im letzten Automationssnapshot: `hoa1npn3n210948_*`; separater bidirektionaler Shelly-Messpfad `shellyplugsg3_8cbfea9fc83c_power`. |
| DEV-BAT-002 | Zendure SolarFlow 800 Plus | 1,92 kWh; im aktuellen Gesamt-SOC-Template 10–90 % Nutzbereich | Zusatz-AC-Ladung, PV-Passthrough, Nachtentladung | ACTIVE / REPORTED + CONFIGURED | SOC-Haupt-/Backup-Pfade vorhanden; System-Power aus `eoc1nln9n465067_packinputpower` und `...outputpackpower`. Exakte aktuelle Steuer-YAML fehlt noch. |
| DEV-BAT-003 | Marstek Venus D | 5,12 kWh; im aktuellen Gesamt-SOC-Template 12–100 % Nutzbereich | zusätzlicher Speicher; Priorität/Headroom beeinflusst SolarFlow 2400 AC | ACTIVE / REPORTED + CONFIGURED | HAME/hm2mqtt-SOC-Pfad `vnsd_0_682499eefa15`; separater Shelly Plug PM Gen 3 `d885ac0c44a4` für bidirektionale AC-Leistung. Telemetrie ungefähr 1 min; SOC war zeitweise lange unavailable. |
| DEV-BAT-004 | Jackery 2000 Ultra | `UNKNOWN` in dieser Betriebsinventur | Energiesystem | KNOWN / LIVE VERIFY | Im Projektbestand bekannt; im letzten ausgewerteten HA-Config-Snapshot kein eindeutiger aktueller Pfad identifiziert. |
| DEV-BAT-005 | Bluetti Elite 300 + Transfer Hub | `UNKNOWN` in dieser Betriebsinventur | Energiesystem | KNOWN / LIVE VERIFY | Im Projektbestand bekannt; nicht mit dem separaten Template-Namen „Bluetti Balco PV“ gleichsetzen. |
| DEV-BAT-006 | Marstek Venus E Mini | ca. 2 kWh Produktklasse; betriebliche Konfiguration hier nicht kanonisch | Test-/Integrationsgerät | KNOWN / LIVE VERIFY | Review-Messreihen/Claims verbleiben im Heise-Testberichte-Repo. |
| DEV-BAT-007 | Marstek Jupiter C Plus | 5,12 kWh; im aktuellen Gesamt-SOC-Template 12–100 % Nutzbereich | PV-/Hausspeicher, Bestandteil des 4-Speicher-Gesamt-SOC | CONFIGURED SNAPSHOT / CURRENT ROLE STRONGLY INDICATED | HAME-Pfad `jpls_8h_24215ee563ae`; vier PV-Leistungseingänge + `combined_power` + SOC. Aktive Einbindung wird durch Gesamt-SOC-Konfiguration gestützt, heutiger Live-State aber nicht direkt gelesen. |
| DEV-BAT-008 | Marstek B2500-D / HAME `HMJ-2` | `UNKNOWN` | Speicher-/PV-Pfad | LEGACY/SECONDARY PATH / LIVE VERIFY | Template `Marstek HMJ-2 System Power` ist im letzten Config-Snapshot vorhanden; nutzt HAME-Pfad `hmj_2_b42f03988c36`. Nicht Bestandteil des aktuellen 4-Speicher-Gesamt-SOC; daher nicht automatisch als aktuell aktiver Hausspeicher behandeln. |

### Aktuelles konfiguriertes 4-Speicher-Aggregat

Das letzte vorliegende HA-Template bildet genau diese vier Speicher ab:

1. SolarFlow 800 Plus – 1,92 kWh
2. SolarFlow 2400 AC – 2,88 kWh
3. Marstek Venus D – 5,12 kWh
4. Marstek Jupiter C Plus – 5,12 kWh

Summe Nennkapazität: **15,04 kWh**.  
Im Template definierte nutzbare Energie zwischen den dort gesetzten Min-/Max-SOC-Grenzen: **12,8512 kWh**.  
Reserve außerhalb dieser Nutzbereiche: **2,1888 kWh**.

Diese Werte sind `DOCUMENTED / HA CONFIG SNAPSHOT`, keine heutige Live-SOC-Aussage.

## 4. Einzelmessung / Shelly-Steckdosen

| ID | Gerät / logische Rolle | Status | Rohpfad / Semantik |
|---|---|---|---|
| DEV-PLUG-001 | Shelly Plug S Gen 3 – Anzucht | ACTIVE / REPORTED + CONFIGURED | `shellyplugsg3_e4b063e51e78_power`; Energie aktuell über `..._energy_consumed`; heutige Rolle Verbrauch. Historisch vorher Mikrowechselrichter. |
| DEV-PLUG-002 | Shelly Outdoor – SBS4 Outdoor Steckdose | ACTIVE / REPORTED + CONFIGURED | `shellyoutdoorsg3_b08184ee7554_*`; Power, consumed/returned energy, Strom, Spannung, Frequenz. Exaktes Handelsmodell `VERIFY`. |
| DEV-PLUG-003 | Shelly Plug S Gen 3 – HMS-800W-2T-Messung | CONFIGURED SNAPSHOT | `shellyplugsg3_8cbfea910720_power`; negative Rohleistung = Erzeugung im Template; Energie aus `energy_returned`. |
| DEV-PLUG-004 | Shelly Plug S Gen 3 – SBS4 Klimaanlage | CONFIGURED SNAPSHOT | `shellyplugsg3_b08184a64470_power`; Verbrauchsenergie aktuell aus `energy_consumed`. |
| DEV-PLUG-005 | Shelly Plug PM Gen 3 – Marstek Venus D | ACTIVE / REPORTED + CONFIGURED | `shellyplugpmg3_d885ac0c44a4_power`; positiv = Laden/Verbrauch aus Netzseite, negativ = Entladen/Einspeisung laut Templates. |
| DEV-PLUG-006 | Shelly Plug S Gen 3 – SolarFlow 2400 AC | ACTIVE / REPORTED + CONFIGURED | `shellyplugsg3_8cbfea9fc83c_power`; positiv = Laden, negativ = Entladen laut Templates. |
| DEV-PLUG-007 | Shelly Plug S Gen 3 – „Bluetti Balco PV“ | CONFIGURED SNAPSHOT / LIVE VERIFY | `shellyplugsg3_8cbfeaa040a4_power`; negative Rohleistung wird als positive PV-Leistung abgebildet; Erzeugungsenergie über `returned_energy`. Physische Zuordnung zum aktuellen Bluetti-System `VERIFY`. |
| DEV-PLUG-008 | Shelly Plug PM Gen 3 – SBS4 Schuko Lader | CONFIGURED SNAPSHOT / LIVE VERIFY | `shellyplugpmg3_9070695a1600_power`; Verbrauchsenergie `energy_consumed`; Template schneidet negative Leistung auf 0 ab. Welches Fahrzeug/Gerät geladen wird: `UNKNOWN`. |
| DEV-PLUG-009 | Shelly Plug S Gen 3 – Kühlschrank Cool Stash | CONFIGURED SNAPSHOT | Schaltaktor `switch.shellyplugsg3_8cbfea9b1c28`; wird durch Generic Thermostat geregelt. |

## 5. Klima / Thermostat / Sensorik

| ID | Gerät / Funktion | Status | Bemerkung |
|---|---|---|---|
| DEV-CLIMATE-001 | SwitchBot Thermo-Hygrometer | ACTIVE / REPORTED | genaue Anzahl, Modelle und Entity-IDs noch nicht aus Live HA exportiert. |
| DEV-CLIMATE-002 | Govee Klima-Sensor(en) | ACTIVE / REPORTED | genaue Modelle und Entity-IDs noch nicht aus Live HA exportiert. |
| DEV-CLIMATE-003 | Temperaturquelle `cool_stash_temperature` | CONFIGURED SNAPSHOT | Ziel-/Quellsensor des Generic Thermostat „Kühlschrank Cool Stash“; physisches Sensormodell `UNKNOWN`. |
| DEV-HVAC-001 | Generic Thermostat „Kühlschrank Cool Stash“ | CONFIGURED SNAPSHOT | Kühllogik: Ziel 17 °C, Bereich 10–25 °C, `ac_mode: true`, ±2 °C Toleranzen, min. Zyklus 5 min, Keep-alive 2 min; schaltet DEV-PLUG-009. |

## 6. Nicht aus Konfiguration ableiten

Folgendes wird ausdrücklich **nicht** aus einem vorhandenen Entity-/Templatepfad automatisch behauptet:

- Gerät ist heute online.
- Gerät ist physisch noch am selben Anschluss.
- Entity ist nicht deaktiviert/verwaist.
- Hersteller-App/HEMS oder andere Regler sind deaktiviert.
- ein konfigurierter Speicher ist Bestandteil der aktiven Regelstrategie.

Für diese Aussagen ist ein Live-HA-/Geräteabgleich erforderlich.

## Registerregeln

- Neue physische Hardware erhält stabile `DEV-*`-ID; bestehende IDs nicht neu vergeben.
- Firmwarestände sind zeitabhängig und gehören nicht als unveränderliche Geräteeigenschaft hierher.
- Seriennummern, Tokens, private IP-Adressen und externe Zugangsdaten nicht im öffentlichen Repository speichern.
- Gerätekennungen aus Entity-IDs werden nur dort dokumentiert, wo sie für die eindeutige technische Zuordnung der produktiven HA-Pfade erforderlich sind.