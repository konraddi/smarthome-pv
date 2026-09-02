# Integration Register

Stand: 2026-09-02
Evidenzbasis: HA-Konfigurationssnapshot 2026-08-31, SolarFlow-2400-AC-Automationssnapshot 2026-08-27, bestätigte Nutzerangaben und ältere belegte HA-Stände.

## Statuslogik

- `CONFIGURED SNAPSHOT` – Integration/Pfad ist aus vorliegender Konfiguration belegbar.
- `ACTIVE / REPORTED` – Nutzer bestätigt praktische Nutzung.
- `HISTORICAL / LIVE VERIFY` – früher konkret verwendet, heutige Rolle nicht bestätigt.
- `VERSION UNKNOWN` – keine aktuelle Versionsnummer belegt.
- `WRITE VERIFY` – Lesepfad belegt, Schreibfähigkeit oder deren Zuverlässigkeit nicht vollständig verifiziert.

## 1. Home Assistant Kern / Konfigurationsstruktur

| ID | Integration / Pfad | Read | Write/Control | Status | Details |
|---|---|---|---|---|---|
| INT-HA-CORE | Home Assistant Core | YES | YES | ACTIVE / REPORTED / VERSION UNKNOWN | zentrale Orchestrierungsplattform; exakte Core-Version und Installationsart noch nicht live inventarisiert. |
| INT-HA-DEFAULT | `default_config` | YES | n/a | CONFIGURED SNAPSHOT | Standardintegrationen werden geladen. |
| INT-HA-TEMPLATE | Template Integration | YES | abgeleitete States | CONFIGURED SNAPSHOT | umfangreiche Energie-, Leistungs-, SOC- und Availability-Templates. Kritisch: Availability muss alle relevanten Quellen abdecken. |
| INT-HA-FILTER | Filter Sensor | YES | abgeleiteter State | CONFIGURED SNAPSHOT | 30-s `time_simple_moving_average` für zentrale Shelly-Nettoleistung. |
| INT-HA-INTEGRATION-SENSOR | Integral / `integration` Sensor | YES | abgeleiteter Energiezähler | CONFIGURED SNAPSHOT | Garage-Hinten-Leistungswerte werden in kWh integriert; `method: left`, `max_sub_interval: 5 min`. |
| INT-HA-GENERIC-THERMOSTAT | Generic Thermostat | YES | switch control | CONFIGURED SNAPSHOT | „Kühlschrank Cool Stash“: Temperaturquelle + Shelly-Schaltaktor, Kühllogik. |
| INT-HA-COMMAND-LINE | Command Line | YES | lokaler Shell-Read | CONFIGURED SNAPSHOT | DB-Größensensor + Test-Echo; keine produktive Geräte-Steuerung. |
| INT-HA-FRONTEND | Frontend/Themes | n/a | n/a | CONFIGURED SNAPSHOT | Themes via `!include_dir_merge_named themes`. |
| INT-HA-INCLUDES | automations/scripts/scenes includes | n/a | n/a | CONFIGURED SNAPSHOT | `automations.yaml`, `scripts.yaml`, `scenes.yaml` separat eingebunden. |

## 2. Recorder / Datenhaltung

| ID | Pfad | Status | Konfiguration im Snapshot 2026-08-31 |
|---|---|---|---|
| INT-HA-RECORDER | Home Assistant Recorder | CONFIGURED SNAPSHOT | SQLite unter `/config/home-assistant_v2.db`; 30 Tage Detailaufbewahrung; Auto-Purge aktiv; Commit-Intervall 30 s. |

Recorder-Filter im Snapshot:

- ausgeschlossen: `updater`, `persistent_notification`, `script`.
- eingeschlossen: `sensor`, `switch`, `energy`, `binary_sensor`.

Bedeutung für Datenpipeline:

- SQLite ist damit die **aktuell dokumentierte** Historienquelle.
- 30 Tage ist die dokumentierte Detail-Retention im Snapshot.
- Ein heutiger Live-Abgleich ist weiterhin nötig, bevor ein automatischer Export dagegen gebaut wird.

## 3. Shelly ↔ Home Assistant

| ID | Geräte/Pfade | Read | Write | Verbindung | Status |
|---|---|---|---|---|---|
| INT-HA-SHELLY | Shelly Pro 3EM, Shelly 3EM Gen3, Plug S Gen 3, Plug PM Gen 3, Outdoor | YES | Switches YES where exposed; measurement entities read-only | lokale HA-Shelly-Integration anzunehmen, exakte Config Entry/Version nicht exportiert | ACTIVE / REPORTED + CONFIGURED SNAPSHOT |

Bestätigte Gerätepunkte aus Entity-Pfaden:

- zentraler Shelly Pro 3EM `2cbcbbb84c18`.
- Garage Hinten Shelly 3EM Gen3 `28372f367218`.
- HMS-800W-2T-Messplug `8cbfea910720`.
- Klimaanlage `b08184a64470`.
- Anzucht `e4b063e51e78`.
- Outdoor `b08184ee7554`.
- Venus-D-Messplug `d885ac0c44a4`.
- SolarFlow-2400-AC-Messplug `8cbfea9fc83c`.
- Bluetti-Balco-PV-Messpfad `8cbfeaa040a4`.
- Schuko-Lader `9070695a1600`.
- Kühlschrank-Cool-Stash-Schaltplug `8cbfea9b1c28`.
- historischer HiBattery-Messpfad mit Kennung `8cbfea91086c`.

Keine IP-Adressen oder Zugangsdaten werden im öffentlichen Repo gespeichert.

## 4. Zendure ↔ Home Assistant

| ID | Geräte | Read | Write | Status | Bekannte Pfade / Besonderheiten |
|---|---|---|---|---|---|
| INT-ZENDURE | SolarFlow 2400 AC, SolarFlow 800 Plus | YES | YES / projektpraktisch genutzt | ACTIVE / REPORTED / VERSION UNKNOWN | aktuelle Entity-Präfixe zeigen HA-Entities; genaue verwendete Custom-Component-/MQTT-Betriebsart und Version heute noch live verifizieren. |

### SolarFlow 2400 AC

Letzter vollständiger Automationssnapshot schreibt direkt:

- `select.hoa1npn3n210948_acmode`
- `number.hoa1npn3n210948_outputlimit`
- `number.hoa1npn3n210948_inputlimit`

Damit ist ein produktiv verwendeter HA-Write-Pfad belegt.

### SolarFlow 800 Plus

Read-Pfade im Config-Snapshot:

- `solarflow_800_plus_electric_level`
- `ab2000_06731_soc_level`
- `eoc1nln9n465067_packinputpower`
- `eoc1nln9n465067_outputpackpower`

Historisch konkret genannt:

- `sensor.eoc1nln9n465067_electriclevel`.
- `number.eoc1nln9n465067_outputlimit`.
- `input_boolean.solarflow_day_100_enabled`.

Diese älteren IDs werden nicht automatisch als heutige Steuerpfade übernommen.

Ein exakter aktueller 800-Plus-Write-Entity-Satz ist in der vorhandenen File-Library nicht vollständig wiedergefunden und bleibt `LIVE IMPORT REQUIRED`.

### Control Ownership

Aus dem Projektverlauf ist bekannt, dass Zendure HEMS und eigene HA-Regelung dieselben Lade-/Entladelimits überschreiben können. Für individuell gesteuerte Geräte gilt daher:

> HEMS/Herstellerregler nicht parallel auf dieselben Limits schreiben lassen, sofern Koordination nicht ausdrücklich verifiziert ist.

Aktueller HEMS-Status jedes Zendure-Geräts: `LIVE VERIFY`.

## 5. HAME Relay + hm2mqtt

| ID | Geräte | Read | Write | Update | Status |
|---|---|---|---|---|---|
| INT-HAME-MQTT | Marstek Venus D, Jupiter C Plus, HMJ-2/B2500-D | YES | Venus D: WRITE VERIFY; andere: UNKNOWN | Venus D laut Nutzer ungefähr 1 min | ACTIVE / REPORTED + CONFIGURED SNAPSHOT |

Bestätigte Prefixe:

- Venus D: `hame_energy_vnsd_0_682499eefa15_*`.
- Jupiter C Plus: `hame_energy_jpls_8h_24215ee563ae_*`.
- HMJ-2/B2500-D: `hame_energy_hmj_2_b42f03988c36_*`.

### Regeldynamik

Eine ungefähr minutenweise Venus-D-Telemetrie ist für Zustands-/Prioritätslogik nutzbar, aber **nicht** als schnelle Sekundenregelung des Netzanschlusspunktes geeignet. Die schnelle Regelschleife des 2400 AC basiert deshalb auf Shelly-Nettoleistung; Venus D wirkt als langsamer Prioritäts-/Sperrpfad.

### Known Failure Mode

Venus-D-SOC kann lange `unavailable` sein. Ein HAME-/MQTT-Restore oder letzter bekannter SOC darf nicht als Live-SOC umetikettiert werden.

## 6. Möglicher Marstek Modbus-RS485-Pfad

| ID | Gerät | Read | Write | Status |
|---|---|---|---|---|
| INT-MARSTEK-RS485 | Venus D | UNKNOWN | UNKNOWN | DISCUSSED / NOT VERIFIED |

Keine Registeradresse, Baudrate, Funktion oder Steuerfähigkeit wird ohne belastbare Quelle erfunden. Nur weiterverfolgen, falls hm2mqtt für gewünschte Stellgrößen/Latenz nicht genügt.

## 7. Historische Hoymiles-/MQTT-Pfade

### INT-HIBATTERY-MQTT – Hoymiles HiBattery 1920 AC / MS-A2

Status: `HISTORICAL / LIVE VERIFY`.

Früher konkret verwendet:

- `select.msa_280425300101_mqtt_select` – EMS-/MQTT-Moduspfad, damalige Nutzung `ems_mode`/`mqtt_ctrl`.
- `number.msa_280425300101` – damaliger Power-Control-Pfad; 200-W-Zeitplan wurde im Projekt genutzt.

Read-/Write-Steuerung war damit historisch praktisch belegt. Der Pfad ist **nicht** Teil des letzten 4-Speicher-SOC-Snapshots und wird heute nicht ohne Live-Prüfung als aktiv behandelt.

### INT-OPENDTU – OpenDTU PV

Status: `HISTORICAL / LIVE VERIFY`.

Früher konkret belegt:

- `sensor.opendtu_f86310_yield_total` – Erzeugungsenergie.
- `sensor.opendtu_f86310_ac_power` – AC-PV-Leistung.
- damalige Templates `sensor.pv_opendtu_energie` und `sensor.pv_opendtu_leistung`.

Aktueller OpenDTU-Betriebsstatus: `UNKNOWN`.

## 8. Klima-Integrationen

| ID | Geräte | Read | Write | Status |
|---|---|---|---|---|
| INT-SWITCHBOT | SwitchBot Thermo-Hygrometer | YES / REPORTED | n/a für reine Sensorik | ACTIVE / REPORTED / ENTITY INVENTORY OPEN |
| INT-GOVEE | Govee Temperatur-/Feuchtesensoren | YES / REPORTED | n/a für reine Sensorik | ACTIVE / REPORTED / ENTITY INVENTORY OPEN |
| INT-ACINFINITY | AC Infinity | UNKNOWN | UNKNOWN | OPEN / RESEARCH REQUIRED |
| INT-WIFI-THERMOMETER | `Thermometer Grow` | READ PATH KNOWN | n/a | HISTORICAL/RECENT STATE EVIDENCE / LIVE VERIFY |

Für `Thermometer Grow` ist `sensor.wifi_thermometer` konkret bekannt; in einem früheren State-Snapshot war der Sensor `unavailable`. Physische Integration/Modell bleibt `UNKNOWN`.

Konkrete SwitchBot-/Govee-Entity-IDs und Updateintervalle sind noch nicht aus einem aktuellen HA-Export belegt.

## 9. HTTP / TLS

Im HA-Konfigurationssnapshot ist TLS direkt im `http:`-Block konfiguriert:

- Zertifikatspfad `/ssl/fullchain.pem`
- Private-Key-Pfad `/ssl/privkey.pem`

Der **Pfad** ist kein Secret; der Schlüsselinhalt wird selbstverständlich nicht in Git persistiert.

Ältere kommentierte externe URLs werden nicht in dieser öffentlichen Projektakte übernommen.

Reverse-Proxy-Nutzung: `UNKNOWN / nicht aus aktivem Config-Block belegt`.

## 10. Integrationsqualität – offene technische Punkte

Noch nicht frisch verifiziert:

- Home-Assistant-Core-Version.
- HA-Installationsart.
- Shelly-Integration-Version/Updateintervalle.
- Zendure-Integrationsversion und aktuelle Betriebsart Cloud vs. lokales MQTT je Gerät.
- hm2mqtt-/HAME-Relay-Version.
- tatsächliche Write-Fähigkeiten des Venus D über HAME/hm2mqtt.
- aktueller HiBattery-/OpenDTU-Status.
- Reconnect-/Offline-Verhalten aller kritischen Integrationen.
- Restore-Verhalten der kritischen Quellsensoren.

## 11. Control-Ownership-Matrix

| Stellgröße / Gerät | aktuell bekannte Steuerquelle | weitere mögliche Regler | Status |
|---|---|---|---|
| SF2400 AC `acmode/inputlimit/outputlimit` | Home Assistant Automation | Zendure HEMS/App | HA-WRITE BELEGT; HEMS-STATUS LIVE VERIFY |
| SF800 Plus Lade-/Entladelimits | Home Assistant Automation berichtet | Zendure HEMS/App | FUNCTION REPORTED; EXACT WRITE ENTITIES OPEN |
| Venus D Ladeverhalten | Hersteller-/Geräteautomatik + in HA als Prioritätsinput beobachtet | HAME/hm2mqtt Write, Modbus möglich | CONTROL OWNER / WRITE PATH VERIFY |
| Cool-Stash-Steckdose | HA Generic Thermostat | manueller Switch | CONFIGURED SNAPSHOT |
| HiBattery/MS-A2 | historisch HA/MQTT | Geräte-/Herstellerlogik | HISTORICAL / LIVE VERIFY |

Grundsatz: **ONE CONTROLLER PER ACTUATOR UNLESS COORDINATION IS VERIFIED.**