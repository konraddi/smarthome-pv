# Automation Register

Stand: 2026-09-02

Dieses Register trennt **letzten vollständig vorliegenden YAML-Snapshot** von **heutigem Live-HA-Stand**. Ein hochgeladener YAML-Stand wird nicht automatisch als heute unverändert produktiv bezeichnet.

## Statusklassen

- `LATEST UPLOADED YAML` – vollständiger zuletzt vorliegender Automationscode.
- `REPORTED ACTIVE` – Nutzer berichtet aktuellen produktiven Betrieb.
- `LIVE VERIFY` – Gleichheit mit heutiger HA-Automation noch nicht direkt geprüft.
- `FUNCTIONAL DESCRIPTION ONLY` – Verhalten ist aus Projektverlauf belegt, vollständige aktuelle YAML fehlt.

---

# AUTO-ENERGY-001 – Zendure SolarFlow 2400 AC PV-Überschussladen und Nachtentladung

Alias im letzten vollständigen Snapshot:

`Zendure SolarFlow 2400 AC PV-Überschussladen und Nachtentladung`

Status: **`REPORTED ACTIVE / LATEST UPLOADED YAML 2026-08-27 / LIVE VERIFY`**.

## Zweck

- tagsüber AC-seitig PV-Überschuss aufnehmen.
- dem Marstek Venus D unterhalb seiner Prioritätsgrenze Lade-Headroom reservieren.
- bei sinkendem Überschuss/Netzbezug schnell zurückregeln.
- Hochregelung gegen kurze Leistungsspitzen durch 30-s-Mittelwert absichern.
- nachts feste Grundlastentladung.
- nach HA-Start bzw. Zeitfensterwechsel Zielmodus/-limits erneut setzen.

## Trigger

| Trigger-ID | Auslöser | Fenster / Bedeutung |
|---|---|---|
| `pv_tick` | alle 15 s | nur 08:00–20:00; reguläre Tagesregelung |
| `five_min_tick` | alle 5 min bei Sekunde 0 | nur Nacht; Zustand regelmäßig nachziehen |
| `day_start` | 08:00 | Tagesmodus erzwingen |
| `night_start` | 20:00 | Nachtmodus erzwingen |
| `ha_start` | Home Assistant Start | Modus/Limits nach Neustart nachziehen |
| `venus_discharge_high` | `sensor.shellyplugpmg3_d885ac0c44a4_power < -100` | Venus D entlädt deutlich; nur tagsüber relevant |
| `venus_discharge_low` | gleiche Entity `> -100` | Entladesperre endet; nur tagsüber |
| `grid_import_detected` | `sensor.shelly_3em_netto_leistung > 50` | Netzbezug erkannt; nur tagsüber |

Automation-Mode: `single`.  
`max_exceeded: silent`.

## Kritische Inputs

### Netzfluss

- Roh: `sensor.shelly_3em_netto_leistung`.
- 30-s-Mittel: `sensor.shelly_3em_netto_leistung_30s_mittel`.
- Config-Semantik: positiv = Netzbezug, negativ = Einspeisung.

### Venus D

- SOC: `sensor.hame_energy_vnsd_0_682499eefa15_battery_state_of_charge`.
- AC-Leistung: `sensor.shellyplugpmg3_d885ac0c44a4_power`.
- Venus-Leistung: positiv = Laden, negativ = Entladen.

### SolarFlow-2400-AC-Aktoren / Zustände

- `select.hoa1npn3n210948_acmode`
- `number.hoa1npn3n210948_outputlimit`
- `number.hoa1npn3n210948_inputlimit`

Alle drei müssen nicht `unknown`/`unavailable` sein, sonst erfolgen keine Writes.

## Zeitfenster und Zielmodus

- Ladefenster: `08:00 <= hour < 20:00`.
- Nacht: `20:00–08:00`.
- Tag: Zielmodus `Input mode`.
- Nacht: Zielmodus `Output mode`.
- Tag: `outputLimit = 0 W`.
- Nacht: `outputLimit = 190 W` im Snapshot vom 27.08.
- außerhalb Ladefenster: `inputLimit = 0 W`.

## Regelparameter im Snapshot vom 27.08.

| Parameter | Wert | Bedeutung |
|---|---:|---|
| `soft_min_charge_w` | 80 W | Mindest-AC-Ladung im Tagesfenster / Failsafe-Rückzug |
| `ramp_up_step_w` | 500 W | maximale Erhöhung pro Automationsdurchlauf |
| `max_ac_charge_w` | 1600 W | oberes Input-Limit der Regellogik |
| `target_grid_export_w` | 200 W | gewünschte Rest-Netzeinspeisung |
| `venus_max_charge_w` | 2200 W | rechnerische maximale Venus-D-Ladeleistung für Headroom-Reservierung |
| `venus_priority_soc_limit` | 90 % | unterhalb wird Venus D Prioritäts-Headroom reserviert |
| `venus_discharge_limit_w` | -100 W | darunter gilt Venus D als deutlich entladend |

Diese Zahlen beschreiben **den hochgeladenen Automationsstand vom 27.08.**, nicht automatisch heutige Geräte-Nennwerte oder regulatorische Grenzen.

## Venus-Prioritätslogik

Zunächst:

`venus_charge_w = max(venus_power_w, 0)`.

### Fall A – Venus SOC und Leistung gültig, SOC < 90 %

Reservierter Headroom:

`max(2200 W - aktuelle Venus-Ladeleistung, 0)`.

Folge: SolarFlow 2400 AC erhält nur Überschuss, der nach Ziel-Resteinspeisung **und** dieser Venus-Reserve übrig bleibt.

### Fall B – Venus SOC oder Leistung nicht verfügbar

Reservierter Headroom = **volle 2200 W**.

Das ist der implementierte Failsafe gegen den bekannten Fehlerfall mit ausgefallenem Venus-D-SOC. Der Regler verwendet **nicht** den letzten Venus-SOC als Live-Wert.

### Fall C – Venus SOC >= 90 % und Werte gültig

Reservierter Headroom = 0 W.

Begründung im YAML: oberhalb 90 % kann der Venus D durch Balancing begrenzt sein; keine starre 2200-W-Reservierung mehr.

## Sollwertformel

Mit aktuellem SolarFlow-Input-Limit `current`:

`desired_raw = current - P_grid_raw - 200 W - venus_reserved_headroom`

`desired_smoothed = current - P_grid_30s - 200 W - venus_reserved_headroom`

Beide werden auf **80–1600 W** begrenzt.

## Asymmetrische Regelstrategie

### 1. Venus D entlädt stärker als 100 W

Wenn Tagesfenster und `venus_power < -100 W`:

→ SolarFlow Input sofort auf **80 W**.

Zweck: vermeiden, dass SolarFlow AC lädt, während Venus D gleichzeitig Energie abgibt.

### 2. Soll liegt unter aktuellem Input

→ **sofort** auf `desired_raw_clamped` reduzieren.

Der Roh-Netzwert darf damit sofort auf Netzbezug oder verschwindenden Überschuss reagieren; keine Abwärtsrampe.

### 3. Mehr Ladung wäre möglich

Zum Hochregeln wird:

`upward_ceiling = min(desired_raw_clamped, desired_smoothed_clamped)`.

Damit müssen Rohwert und 30-s-Mittel zusätzliche Leistung erlauben.

Erhöhung je Lauf maximal:

`min(upward_ceiling, current + 500 W)`.

Bei normalem 15-s-Tick also im Design maximal +500 W je regulärem Tick.

### 4. Rohwert zeigt mehr Überschuss, Mittelwert bestätigt noch nicht

→ aktuelles Input-Limit beibehalten.

## Shelly-Ausfall

Wenn `sensor.shelly_3em_netto_leistung` im Tagesfenster nicht verfügbar ist:

→ `target_in = 80 W`.

Das ist ein expliziter Netzsensor-Failsafe; kein `0 W`-Ersatzwert wird als echter Netzfluss interpretiert.

## Write-Deadbands / Wiederholungslogik

`force_resend` bei:

- `day_start`
- `night_start`
- `ha_start`
- manueller Ausführung
- erkannter Modusabweichung

Input-Writes im Tagesbetrieb:

- bei Venus-Entladesperre schreiben, wenn aktuell nicht 80 W.
- wenn Soll >= 1600 W: nur schreiben, wenn aktuelles Limit < 1550 W.
- sonst nur bei Differenz von mindestens 50 W.

Dadurch werden unnötige Kleinst-Writes reduziert.

## Command-Sequenz

1. ggf. `select.select_option` für AC-Modus.
2. danach 2 s Delay.
3. ggf. `number.set_value` auf `outputlimit`.
4. ggf. `number.set_value` auf `inputlimit`.

## Failsafe-Bewertung

Positiv implementiert:

- Venus-SOC wird nur bei `has_value()` als gültig betrachtet.
- Venus-Leistung wird separat geprüft.
- bei fehlendem Venus-Wert volle Headroom-Reserve statt alter SOC.
- bei fehlendem zentralem Shelly Tagesladung nur 80 W.
- Zendure-Aktoren werden bei `unknown`/`unavailable` nicht beschrieben.

Noch offen:

- **Freshness ist Availability, nicht Alter:** `has_value()` beweist nicht, dass der letzte HAME-Wert ausreichend frisch ist. Für den minutenweisen Venus-Pfad fehlt im Snapshot eine explizite Altersprüfung.
- Command-Write wird nicht innerhalb derselben Automation durch ein separates physisches Rücklese-Gate bestätigt.
- heutige Live-YAML muss noch gegen diesen Snapshot diff-geprüft werden.

## Acceptance-Testbedarf

Prioritär:

- AT-02 Vorzeichen physisch verifizieren.
- AT-03 Availability + echte Stale-Simulation.
- AT-05 konkurrierende Zendure-Regler/HEMS.
- AT-06 Command → Device → Physical Effect.
- AT-08 HA-Neustart.
- AT-09 HAME/MQTT-Ausfall.
- AT-10 Last-/PV-Sprung.
- AT-11 Multi-Speicher-Koordination.

---

# AUTO-ENERGY-002 – Zendure SolarFlow 800 Plus AC-Zusatzladung und Nachtentladung

Bekannter Alias:

`Zendure SolarFlow 800 Plus AC-Zusatzladung und Nachtentladung`

Status: **`REPORTED ACTIVE / FUNCTIONAL DESCRIPTION ONLY / EXACT CURRENT YAML NOT FOUND`**.

## Belegtes Verhalten aus dem Projektverlauf

- 11:00–17:00 Uhr zusätzliche AC-Ladung.
- bis ungefähr 800 W zusätzliche AC-Ladung.
- Freigabe u. a. wenn SolarFlow 2400 AC über ungefähr 89 % SOC liegt.
- ungefähr 200 W Rest-Netzeinspeisung am zentralen Shelly anstreben.
- AC-Zusatzladung pausieren, wenn Venus D/Marstek tagsüber über seinen Shelly spürbar entlädt.
- tagsüber `outputLimit 0` für PV-Passthrough.
- nachts 20:00–08:00 Uhr ungefähr 120 W Entladung.
- nachts regelmäßige Prüfung, ob Modus, Input und Output weiterhin korrekt sind.

## Bekannte Sensorpfade des Geräts aus `configuration.yaml`

- `sensor.solarflow_800_plus_electric_level`
- `sensor.ab2000_06731_soc_level`
- `sensor.eoc1nln9n465067_packinputpower`
- `sensor.eoc1nln9n465067_outputpackpower`

## Nicht belastbar rekonstruieren

Bis die reale Automation vorliegt, werden **nicht** erfunden:

- exakte Aktor-Entity-IDs.
- Triggerfrequenzen.
- Übergangslogik 08:00/20:00.
- Deadbands/Hysteresen.
- aktuelle Nachtleistung, falls nach dem letzten Gespräch geändert.
- Freshness-Prüfungen.
- `mode` und Parallelitätsverhalten.

Priorität: vollständige YAML direkt aus HA exportieren.

---

# AUTO-ENERGY-003 – Gesamt-SOC / verbleibende nutzbare Energie

Typ: triggerbasierte Template-Berechnung, keine Aktor-Automation.

Status: `CONFIGURED SNAPSHOT 2026-08-31`.

Beteiligte Entities:

- `sensor.verbleibende_energie_bis_mindest_soc`
- `sensor.gesamt_soc_hausspeicher_nutzbar`

## Besonderheit

Der vorgelagerte Sensor speichert zuletzt gültige SOC-Werte und rechnet damit weiter, wenn Live-SOC-Sensoren ausfallen.

Das ist zulässig für **Anzeige/Trend**, weil die Datenqualität über Attribute kenntlich gemacht wird. Für Regelungen gilt jedoch:

> Aggregat nur als Live-Steuerwert verwenden, wenn `daten_vollstaendig_aktuell` true bzw. die benötigten Einzel-SOCs nachweislich frisch sind.

Insbesondere der bekannte Venus-D-Failsafe darf dadurch nicht umgangen werden.

---

# AUTO-ENERGY-004 – Energy-Dashboard Mess-/Richtungslogik

Typ: Template-/Integrationssensorik.

Status: `CONFIGURED SNAPSHOT 2026-08-31`.

Enthält unter anderem:

- zentrale Shelly-Nettoleistung + Import/Export-Aufteilung.
- 30-s-Nettofilter.
- bidirektionale System-Power-Templates für Venus D und SolarFlow 2400 AC.
- SolarFlow-800-Plus-System-Power.
- Jupiter-System-Power.
- HMJ-2-System-Power.
- HMS-800W-2T-PV-Leistung/-Energie.
- Anzucht/Klimaanlage/Outdoor/Schuko-Lader.
- Garage-Hinten-Phasen- und Energieintegration.

Die exakte Energy-Dashboard-Auswahl selbst ist nicht vollständig aus `configuration.yaml` ableitbar und bleibt separat zu prüfen.

---

# AUTO-CLIMATE-001 – Generic Thermostat Kühlschrank Cool Stash

Typ: `generic_thermostat`.

Status: `CONFIGURED SNAPSHOT 2026-08-31`.

- Aktor: `switch.shellyplugsg3_8cbfea9b1c28`.
- Temperatur: `sensor.cool_stash_temperature`.
- Kühlmodus: `ac_mode: true`.
- Ziel: 17 °C.
- erlaubter Einstellbereich: 10–25 °C.
- Cold tolerance: 2 °C.
- Hot tolerance: 2 °C.
- Mindestzyklus: 5 min.
- Keep-alive: 2 min.
- Initialmodus: `cool`.

Physische Anwendung und heutige Aktivität: `LIVE VERIFY`.

---

# Registerpflicht je echte Aktor-Automation

Für jede produktive Automation müssen nach Live-Import dokumentiert sein:

- Automation-ID / Alias.
- Quell-YAML / aktueller Stand.
- Trigger und Zeitfenster.
- kritische Inputs.
- erwartete Updateintervalle und maximale Freshness.
- Conditions.
- Stellgrößen/Aktoren.
- Control Owner.
- Deadband/Hysterese/Rampen.
- Failsafe je kritischem Input.
- Verhalten nach HA-Neustart/Reload.
- manueller Override.
- Race-Condition-/Parallelitätsrisiko.
- Command-Rücklesung / physische Wirkung.
- letzter Acceptance-Test-Stand.

## Kanonische Regel

Ein hochgeladener Snapshot ist hochwertige Konfigurationsevidenz, aber **CURRENT LIVE STATE BEFORE OLD CONFIG**. Vor einer zukünftigen Änderung muss die aktuell in Home Assistant gespeicherte Automation erneut gelesen werden.