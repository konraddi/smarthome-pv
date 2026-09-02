# Current System Status

Stand: 2026-09-02

## Freshness-Hinweis

Es besteht in dieser Unterhaltung **kein direkter Live-Zugriff auf Home Assistant**. Der aktuelle kanonische Projektstand basiert deshalb auf:

1. aktuellen ausdrücklichen Nutzerangaben bis 2026-09-02,
2. letztem vorliegenden vollständigen `configuration.yaml`-Snapshot vom **2026-08-31**,
3. letztem vorliegenden vollständigen SolarFlow-2400-AC-Automationssnapshot vom **2026-08-27**.

Damit gilt:

- `CONFIGURED` = im Snapshot vorhanden.
- `REPORTED ACTIVE` = vom Nutzer aktuell als betrieben berichtet.
- `LIVE VERIFY` = heutiger State/Availability nicht direkt gelesen.

Keine Snapshot-Entity wird allein aufgrund ihrer Existenz als heute online bezeichnet.

---

# 1. Home Assistant

Status: **ACTIVE / REPORTED**.

## Dokumentierte Konfiguration

- `default_config` aktiv.
- Themes separat eingebunden.
- `automations.yaml`, `scripts.yaml`, `scenes.yaml` inkludiert.
- Template-Sensoren für Netz, PV, Speicher, Verbraucher und Gesamt-SOC.
- Filter-Sensor für 30-s-Nettoleistung.
- Integral-/Integration-Sensoren für Garage-Hinten-Energie.
- Generic Thermostat „Kühlschrank Cool Stash“.
- Command-Line-Diagnosesensoren.

## Recorder – letzter Config-Snapshot

- SQLite: `/config/home-assistant_v2.db`.
- 30 Tage Detail-Retention.
- Auto-Purge aktiv.
- Commit-Intervall 30 s.
- Sensor/Switch/Energy/Binary-Sensor werden explizit aufgenommen.

## Noch live zu inventarisieren

- HA Core-Version.
- Installationsart.
- aktuelle Integrations-/Custom-Component-Versionen.
- heutige Entity Registry inkl. disabled/hidden/unavailable.
- Backup-/Restore-Status.

---

# 2. Zentraler Netzfluss

Gerät: Shelly Pro 3EM.

Kanonische Template-Entity:

`sensor.shelly_3em_netto_leistung`

Rohquellen:

- `sensor.shellypro3em_2cbcbbb84c18_phase_a_active_power`
- `sensor.shellypro3em_2cbcbbb84c18_phase_b_active_power`
- `sensor.shellypro3em_2cbcbbb84c18_phase_c_active_power`

Formel:

`P_grid = L1 + L2 + L3`

Config-Semantik:

- **positiv = Netzbezug**.
- **negativ = Netzeinspeisung**.

Zusätzlich konfiguriert:

- `sensor.shelly_3em_grid_import_power`.
- `sensor.shelly_3em_grid_export_power`.
- `sensor.shelly_3em_netto_leistung_30s_mittel` mit 30-s-Zeitmittel.

Status: `CONFIGURED 2026-08-31 / PHYSICAL SIGN CHECK STILL RECOMMENDED`.

---

# 3. SolarFlow 2400 AC

Status: **REPORTED ACTIVE**.  
Letzter vollständiger YAML-Stand: **2026-08-27**, heutige Gleichheit `LIVE VERIFY`.

## Steuerpfade im Snapshot

- Modus: `select.hoa1npn3n210948_acmode`.
- Output-Limit: `number.hoa1npn3n210948_outputlimit`.
- Input-Limit: `number.hoa1npn3n210948_inputlimit`.

## Betriebslogik im Snapshot

### Tag 08:00–20:00

- Zielmodus `Input mode`.
- Output-Limit 0 W.
- dynamisches AC-Ladelimit.
- Ziel-Netzeinspeisung 200 W.
- Soft-Minimum 80 W.
- Maximum der Regellogik 1600 W.
- Hochregelung maximal +500 W pro Durchlauf.
- regulärer Tick 15 s.
- sofortige Abregelung nach Roh-Netzwert.
- Hochregelung nur, wenn Rohwert **und** 30-s-Mittel zusätzliche Leistung erlauben.

### Nacht 20:00–08:00

- Zielmodus `Output mode`.
- Output-Limit 190 W im Snapshot vom 27.08.
- Input-Limit 0 W.
- 5-min-Nachprüfung.

## Venus-D-Priorität / Failsafe

- Venus-SOC-Prioritätsgrenze: 90 %.
- reservierte Maximal-Lade-Headroom: 2200 W im Algorithmus.
- Venus entlädt stärker als 100 W → 2400 AC auf 80 W Lade-Minimum.
- Venus SOC oder Venus Power fehlt → volle 2200-W-Headroom-Reserve.
- alter Venus SOC wird im Regler nicht als Live-SOC verwendet.

## Offener Failsafe-Punkt

Die Automation prüft `has_value()`, aber keine explizite Zeitstempel-/Age-Grenze. Ein numerischer, aber zu alter HAME-State könnte deshalb theoretisch noch als gültig gelten. **Freshness != Availability** bleibt ein offener Verbesserungs-/Testpunkt.

---

# 4. SolarFlow 800 Plus

Status: **REPORTED ACTIVE**.

## Config-Sensorik belegt

- `sensor.solarflow_800_plus_electric_level`.
- `sensor.ab2000_06731_soc_level`.
- `sensor.eoc1nln9n465067_packinputpower`.
- `sensor.eoc1nln9n465067_outputpackpower`.

System-Power-Template:

`packinputpower - outputpackpower`, positiv = Entladen, negativ = Laden.

## Bekannte Automation

Alias:

`Zendure SolarFlow 800 Plus AC-Zusatzladung und Nachtentladung`

Belegter Funktionsstand:

- zusätzliche AC-Ladung 11:00–17:00.
- ungefähr bis 800 W.
- Freigabe u. a. bei 2400-AC-SOC > ca. 89 %.
- ungefähr 200 W Rest-Netzeinspeisung.
- Zusatzladung pausiert bei deutlicher Venus-D-Entladung.
- tagsüber Output-Limit 0 für PV-Passthrough.
- Nachtentladung ungefähr 120 W.
- regelmäßige Nachtkontrolle von Modus/Input/Output.

**Vollständige aktuelle YAML wurde in der File-Library trotz erneuter Suche nicht gefunden.** Exakte Aktoren, Trigger, Deadbands und Freshness bleiben `IMPORT REQUIRED`.

---

# 5. Marstek Venus D

Status: **REPORTED ACTIVE / CONFIGURED**.

## Telemetrie

SOC:

`sensor.hame_energy_vnsd_0_682499eefa15_battery_state_of_charge`

Nutzerangabe:

- HAME Relay + hm2mqtt.
- ungefähr ein Update pro Minute.
- SOC war über längere Zeit nicht verfügbar.

## Separater AC-Leistungsmesspfad

`sensor.shellyplugpmg3_d885ac0c44a4_power`

Config-Semantik:

- positiv = Laden.
- negativ = Entladen.

## Steuerstatus

- als Prioritäts-/Block-Input des 2400 AC aktiv genutzt.
- zuverlässige direkte Write-Steuerung über hm2mqtt: `VERIFY`.
- Modbus-RS485: diskutiert, nicht verifiziert.

---

# 6. Marstek Jupiter C Plus

Status: **CONFIGURED 2026-08-31 / CURRENT ROLE STRONGLY INDICATED / LIVE VERIFY**.

Begründung:

- eigener HAME-SOC-Pfad.
- vier PV-Leistungspfade.
- Combined-Power-Pfad.
- eigener System-Power-Sensor.
- Bestandteil des aktuellen 4-Speicher-Gesamt-SOC mit 5,12 kWh.

HAME-Prefix:

`hame_energy_jpls_8h_24215ee563ae_*`

Nutzbereich im Gesamt-SOC-Template: 12–100 %.

---

# 7. Gesamt-SOC der vier Hausspeicher

Konfigurierte Speicher:

- SolarFlow 800 Plus 1,92 kWh.
- SolarFlow 2400 AC 2,88 kWh.
- Venus D 5,12 kWh.
- Jupiter C Plus 5,12 kWh.

Gesamt Nennkapazität: **15,04 kWh**.  
Template-Nutzenergie: **12,8512 kWh**.

Entities:

- `sensor.verbleibende_energie_bis_mindest_soc`.
- `sensor.gesamt_soc_hausspeicher_nutzbar`.

## Datenqualitätsbesonderheit

`Verbleibende Energie bis Mindest-SoC` speichert die letzten gültigen SOC-Werte je Speicher und verwendet sie als Fallback.

Attribute zeigen u. a.:

- `quellen_mit_letztem_wert`.
- `daten_vollstaendig_aktuell`.

Bewertung:

- gut geeignet für Display/Trend mit sichtbarer Datenqualität.
- **nicht automatisch für Steuerfreigaben geeignet**, wenn Fallbackwerte verwendet werden.

---

# 8. Marstek HMJ-2 / B2500-D

Status: **CONFIGURED PATH / LIVE ROLE VERIFY**.

Im Snapshot vorhanden:

- `sensor.hame_energy_hmj_2_b42f03988c36_total_input_power`.
- `sensor.hame_energy_hmj_2_b42f03988c36_total_output_power`.
- Template `Marstek HMJ-2 System Power`.

Nicht im aktuellen 4-Speicher-Gesamt-SOC enthalten. Daher kein automatischer Status als aktiver Hausspeicher.

---

# 9. PV-/Verbrauchsmesspfade

## HMS-800W-2T

- Shelly-Rohleistung `shellyplugsg3_8cbfea910720_power`.
- negative Rohleistung wird positive PV-Leistung.
- Energie über `energy_returned`.

## SBS4 Anzucht

- Power `shellyplugsg3_e4b063e51e78_power`.
- Energie aktuell `..._energy_consumed`.
- aktuelle Rolle: **Verbrauch**.

## SBS4 Klimaanlage

- Power `shellyplugsg3_b08184a64470_power`.
- Energie `..._energy_consumed`.

## SBS4 Outdoor

Feste Templates für:

- Leistung.
- Verbrauch.
- Einspeisung.
- Strom.
- Spannung.
- Frequenz.

## SBS4 Schuko Lader

- Power `shellyplugpmg3_9070695a1600_power`.
- Verbrauchsenergie `..._energy_consumed`.
- angeschlossenes Ladeziel `UNKNOWN`.

## Bluetti Balco PV

Konfigurierter Shelly-Messpfad vorhanden, aktuelles physisches Mapping `VERIFY`.

---

# 10. Garage Hinten – Shelly 3EM Gen3

Status: **CONFIGURED 2026-08-31 / LIVE VERIFY**.

Drei Rohphasen + Templates für:

- Netto je Phase.
- Bezug je Phase.
- Einspeisung je Phase.
- Netto Gesamt.
- Summe Bezugskomponenten Gesamt.
- Summe Einspeisekomponenten Gesamt.
- kWh-Integrale je Phase und gesamt.
- Connectivity-Binary-Sensor.

Wichtige Semantik:

**Nur Netto Gesamt ist dreiphasig saldiert.**  
`Bezug Leistung Gesamt` und `Einspeisung Leistung Gesamt` summieren Richtungsanteile phasenweise und können gleichzeitig beide >0 sein.

Exakter physischer Messumfang: `OPEN / VERIFY`.

---

# 11. Klima / Kühlschrank Cool Stash

Klima allgemein:

- SwitchBot Thermo-Hygrometer – REPORTED ACTIVE.
- Govee – REPORTED ACTIVE.
- genaue Entity-IDs/Modelle noch offen.

Generic Thermostat im letzten Config-Snapshot:

- Name: `Kühlschrank Cool Stash`.
- Aktor: `switch.shellyplugsg3_8cbfea9b1c28`.
- Temperatursensor: `sensor.cool_stash_temperature`.
- Ziel 17 °C.
- min/max 10/25 °C.
- Toleranzen ±2 °C.
- Mindestzyklus 5 min.
- Keep-alive 2 min.

Heutige Aktivität: `LIVE VERIFY`.

---

# 12. Datenpipeline-Readiness

Recorder-/DB-Pfad ist nun aus dem letzten Snapshot bekannt:

- SQLite.
- 30 Tage Detaildaten.

Damit ist die technische Ausgangslage für einen täglichen Klimaexport deutlich klarer. Weiter blockierend:

- Repo aktuell öffentlich.
- Sensor-Whitelist fehlt.
- genaue Klima-Entities fehlen.
- HA-Version/Installationsart live unbekannt.

---

# 13. Wichtigste aktuelle technische Risiken / Gaps

## HOCH – Freshness Venus D

`has_value()` reicht nicht für „frisch“. Minutenweise Telemetrie braucht für Control eine explizite Altersgrenze oder einen belastbaren Freshness-Indikator.

## HOCH – 800-Plus-YAML fehlt

Funktionslogik ist bekannt, aber der tatsächliche heutige Regler kann noch nicht auf Race Conditions/Failsafes vollständig auditiert werden.

## HOCH – Live-vs-Config-Drift

Die Entity-Inventur ist jetzt aus dem Snapshot detailliert, aber ein Live-Export muss klären:

- welche Entities heute existieren.
- welche unavailable/disabled sind.
- welche Altpfade nur noch Konfigurationsreste sind.

## MITTEL – Aggregate mit Last-Value-Fallback

Gesamt-SOC ist absichtlich resilient für Anzeige, darf aber nicht versehentlich als frischer Control-Sensor benutzt werden.

## MITTEL – Jupiter/HMJ Availability

Einige System-Power-Templates prüfen nicht alle Quellsensoren und verwenden `float(0)`. Für Anzeigen vertretbar, für Regelung nicht ausreichend.

## MITTEL – Garage-Hinten-Semantik

Physischer Messpunkt ist noch nicht eindeutig dokumentiert; phasenweise Import-/Export-Summen dürfen nicht mit saldierter Gesamtleistung verwechselt werden.

## MITTEL – Control Ownership

HEMS/App/HA-Status je Zendure-Gerät heute nicht direkt verifiziert.

---

# 14. Nächste technische Prioritäten

1. Live-HA-System-/Entity-Export gegen dieses Inventar diffen.
2. aktuelle 800-Plus-Automation vollständig sichern.
3. heutige 2400-AC-Automation gegen Snapshot 27.08. diffen.
4. Freshness statt nur Availability für Venus-D-Control definieren/testen.
5. Control Ownership HEMS/App/HA je Speicher verifizieren.
6. Garage-Hinten-Messpunkt physisch benennen.
7. SwitchBot-/Govee-Entity-Inventar durchführen.
8. Repo-Privacy entscheiden, bevor detaillierte Historien automatisiert exportiert werden.