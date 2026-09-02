# Entity Register

Stand: 2026-09-02
Inventur-Evidenz: letzter vorliegender `configuration.yaml`-Snapshot vom 2026-08-31; SolarFlow-2400-AC-Automationssnapshot vom 2026-08-27; bestätigte Nutzerangaben und ältere belegte HA-Stände.

Diese Datei ist die kanonische Zuordnung wichtiger Home-Assistant-Entities, Messpunkte, Einheiten und Semantik.

## 0. Evidenz- und Statuslogik

- `CONFIGURED 2026-08-31` – Entity/Pfad ist im letzten vorliegenden HA-Konfigurationssnapshot referenziert.
- `AUTOMATION 2026-08-27` – Entity/Pfad ist in der letzten vollständig vorliegenden Automation referenziert.
- `REPORTED ACTIVE` – vom Nutzer als aktuell im Betrieb berichtet.
- `HISTORICAL USER FACT` – früher konkret vom Nutzer genannt/verwendet; nicht automatisch heute aktiv.
- `LIVE VERIFY` – heutiger Entity-State/Availability nicht direkt gelesen.
- `CONTROL-CRITICAL` – darf für Regelentscheidungen nur mit expliziter Availability/Freshness verwendet werden.
- `DISPLAY/ANALYTICS` – darf historische Fallbackwerte verwenden, solange dies sichtbar gekennzeichnet ist; nicht automatisch als Live-Steuerwert verwenden.

Ein Konfigurationssnapshot beweist, dass ein Pfad konfiguriert war, nicht dass die Entity heute online ist.

---

# 1. Zentraler Netzanschlusspunkt – Shelly Pro 3EM

## 1.1 Roh-Entities

| Entity-ID | Größe | Einheit | Semantik | Nutzung | Status |
|---|---|---:|---|---|---|
| `sensor.shellypro3em_2cbcbbb84c18_phase_a_active_power` | Phase A Wirkleistung | W | Vorzeichen wird direkt in Netto-Summe übernommen | zentrale Netzbilanz | CONFIGURED 2026-08-31 / CONTROL-CRITICAL |
| `sensor.shellypro3em_2cbcbbb84c18_phase_b_active_power` | Phase B Wirkleistung | W | wie Phase A | zentrale Netzbilanz | CONFIGURED 2026-08-31 / CONTROL-CRITICAL |
| `sensor.shellypro3em_2cbcbbb84c18_phase_c_active_power` | Phase C Wirkleistung | W | wie Phase A | zentrale Netzbilanz | CONFIGURED 2026-08-31 / CONTROL-CRITICAL |

Availability des Netto-Templates: alle drei Rohwerte müssen numerisch sein.

## 1.2 Kanonische abgeleitete Netzleistung

| Entity-ID | unique_id | Größe | Einheit | Richtung / Formel | Nutzung | Status |
|---|---|---|---:|---|---|---|
| `sensor.shelly_3em_netto_leistung` | `shelly_3em_netto_leistung` | saldierte 3-Phasen-Nettoleistung | W | `L1 + L2 + L3`; Config-Semantik: **positiv = Netzbezug, negativ = Netzeinspeisung** | primärer Regelinput | CONFIGURED 2026-08-31 / AUTOMATION 2026-08-27 / CONTROL-CRITICAL |
| `sensor.shelly_3em_grid_import_power` | `shelly_3em_grid_import_power` | Netzbezug Leistung | W | `max(P_grid,0)` | Anzeige / Energy-Auswertung | CONFIGURED 2026-08-31 |
| `sensor.shelly_3em_grid_export_power` | `shelly_3em_grid_export_power` | Netzeinspeisung Leistung | W | `max(-P_grid,0)` | Anzeige / Energy-Auswertung | CONFIGURED 2026-08-31 |
| `sensor.shelly_3em_netto_leistung_30s_mittel` | `shelly_3em_netto_leistung_30s_mittel` | 30-s-Mittel der Nettoleistung | W | `time_simple_moving_average`, 30 s, precision 0 | SolarFlow-2400-AC Hochregel-Freigabe/Obergrenze | CONFIGURED 2026-08-31 / AUTOMATION 2026-08-27 / CONTROL-CRITICAL |

### Vorzeichenstatus

Die **Konfiguration** definiert eindeutig:

- `sensor.shelly_3em_netto_leistung > 0` → Netzbezug.
- `sensor.shelly_3em_netto_leistung < 0` → Netzeinspeisung.

Status: `DOCUMENTED / CONFIG SEMANTICS`.  
Ein physischer AT-02-Gegencheck mit eindeutigem Bezug-/Einspeisezustand bleibt sinnvoll, damit Config-Bezeichnung und reale CT-Richtung nicht nur logisch, sondern physisch bestätigt sind.

### 30-s-Mittel: reale Rolle

Im letzten 2400-AC-Automationssnapshot wird der Rohwert für **sofortiges Herunterregeln** verwendet. Der 30-s-Mittelwert begrenzt nur das **Hochregeln**, sodass kurzfristiger Rohwert und Mittelwert zusätzlichen Überschuss gemeinsam bestätigen müssen. Das ist keine symmetrische Glättung des gesamten Reglers.

---

# 2. SolarFlow 2400 AC

## 2.1 Steuer-Entities aus letzter vollständiger Automation

| Entity-ID | Typ | Semantik | Verwendung | Status |
|---|---|---|---|---|
| `select.hoa1npn3n210948_acmode` | select | erwartete Optionen im Snapshot: `Input mode`, `Output mode` | Tages-/Nachtmodus | AUTOMATION 2026-08-27 / CONTROL-CRITICAL |
| `number.hoa1npn3n210948_outputlimit` | number | Ausgangs-/Entladelimit in W laut Automation | nachts 190 W, tagsüber 0 W im letzten Snapshot | AUTOMATION 2026-08-27 / CONTROL-CRITICAL |
| `number.hoa1npn3n210948_inputlimit` | number | AC-Eingangs-/Ladelimit in W | dynamische Überschussladung, 0–1600 W Logik mit Soft-Minimum 80 W | AUTOMATION 2026-08-27 / CONTROL-CRITICAL |

Die Automation prüft alle drei auf `unknown`/`unavailable`, bevor sie schreibt.

## 2.2 SOC

| Entity-ID | Rolle | Einheit | Fallback | Status |
|---|---|---:|---|---|
| `sensor.solarflow_2400_ac_electric_level` | Haupt-SOC für Gesamt-Speicher-Aggregat | % | `sensor.ab3000_00682_soc_level` | CONFIGURED 2026-08-31 |
| `sensor.ab3000_00682_soc_level` | AB3000X Backup-SOC für Aggregat | % | letzter gespeicherter Aggregatwert | CONFIGURED 2026-08-31 / DISPLAY FALLBACK |

Diese beiden SOC-Pfade sind im aktuellen Gesamt-SOC-Template vorhanden. Der letzte vorliegende 2400-AC-Regler verwendet für seine Venus-Prioritätslogik dagegen **nicht** den eigenen SolarFlow-SOC, sondern den Venus-D-SOC.

## 2.3 Separater bidirektionaler Shelly-Messpfad

Roh-Entity:

- `sensor.shellyplugsg3_8cbfea9fc83c_power` – W.

Aus der Template-Logik dokumentierte Semantik:

- positiv → SolarFlow lädt / AC-Verbrauch am Anschluss.
- negativ → SolarFlow entlädt / Einspeisung am Anschluss.

Abgeleitete Template-Namen / unique_ids:

| Anzeigename | unique_id | Formel |
|---|---|---|
| Zendure SF2400AC Ladeleistung | `shelly_plug_shelly1_ladeleistung_w` | `max(raw,0)` |
| Zendure SF2400AC Entladeleistung | `shelly_plug_shelly1_entladeleistung_w` | `max(-raw,0)` |
| Zendure SF2400AC System Power | `shelly_shelly1_system_power` | Rohwert unverändert |

Die tatsächlichen automatisch generierten Entity-IDs dieser drei Templates werden **nicht geraten**, da im Snapshot kein `default_entity_id` angegeben ist.

---

# 3. SolarFlow 800 Plus

## 3.1 SOC

| Entity-ID | Rolle | Einheit | Status |
|---|---|---:|---|
| `sensor.solarflow_800_plus_electric_level` | Haupt-SOC im Gesamt-Speicher-Aggregat | % | CONFIGURED 2026-08-31 |
| `sensor.ab2000_06731_soc_level` | Backup-SOC des Aggregats | % | CONFIGURED 2026-08-31 / DISPLAY FALLBACK |

Historisch konkret vom Nutzer genannt:

- `sensor.eoc1nln9n465067_electriclevel` – SOC-Pfad, `HISTORICAL USER FACT 2026-05-18 / LIVE VERIFY`.

Die Existenz des älteren direkten `eoc...electriclevel`-Pfads und des neueren Alias-/Templatepfads `sensor.solarflow_800_plus_electric_level` darf nicht ohne Live-Abgleich als Identität/Weiterleitung interpretiert werden.

## 3.2 Systemleistung

Rohpfade:

- `sensor.eoc1nln9n465067_packinputpower`
- `sensor.eoc1nln9n465067_outputpackpower`

Template:

- Anzeigename: `ZDSF800PLS System Power`
- unique_id: `ZDSF800PLS_system_power`
- Einheit: W
- Formel: `packinputpower - outputpackpower`
- im Template benannte Semantik: `packinputpower` = discharge, `outputpackpower` = charge.
- Ergebnis: **positiv = Entladen, negativ = Laden**.
- Energy-Dashboard-Kommentar: `Standard`, nicht `Inverted`.

Status: `CONFIGURED 2026-08-31`.

## 3.3 Historisch bestätigte Aktoren / Helper

Früher konkret vom Nutzer genannt:

- `number.eoc1nln9n465067_outputlimit` – Output-Limit, `HISTORICAL USER FACT 2026-05-18 / LIVE VERIFY`.
- `input_boolean.solarflow_day_100_enabled` – damaliger Helper für eine Tageslogik, `HISTORICAL USER FACT / LIVE VERIFY`.

Diese historischen IDs beweisen **nicht**, dass die heutige 800-Plus-Automation exakt dieselben Aktoren/Helper verwendet.

Exakte aktuelle 800-Plus-Aktor-Entity-IDs konnten in der vorhandenen File-Library nicht vollständig wiedergefunden werden und bleiben `LIVE IMPORT REQUIRED`.

---

# 4. Marstek Venus D

## 4.1 HAME/hm2mqtt SOC

- `sensor.hame_energy_vnsd_0_682499eefa15_battery_state_of_charge`
- Einheit: %.
- nomineller Updatepfad laut Nutzer: ungefähr 1 Minute.
- Rolle: **kritischer Prioritäts-/Failsafe-Input** der 2400-AC-Automation.
- bekannter Fehlerfall: über längeren Zeitraum `unavailable`.

Status: `REPORTED ACTIVE + CONFIGURED 2026-08-31 + AUTOMATION 2026-08-27 / CONTROL-CRITICAL`.

### Failsafe-Regel

Der 2400-AC-Regler prüft `has_value()` separat. Falls Live-SOC oder Venus-Power fehlt, wird **nicht** ein alter SOC benutzt; stattdessen reserviert der Regler vorsichtshalber die volle konfigurierte Venus-Lade-Headroom von 2200 W.

## 4.2 Shelly Plug PM Gen 3 Leistung

Roh-Entity:

- `sensor.shellyplugpmg3_d885ac0c44a4_power`

Config-Semantik:

- positiv = Venus D lädt / nimmt AC-Leistung auf.
- negativ = Venus D entlädt / gibt AC-Leistung ab.

Der 2400-AC-Regler nutzt diese Entity außerdem als Event-/Block-Input:

- `< -100 W` → `venus_discharge_high`, Tages-AC-Ladung des 2400 AC wird auf Soft-Minimum reduziert.
- `> -100 W` → `venus_discharge_low`.

Abgeleitete Template-Namen / unique_ids:

| Anzeigename | unique_id | Formel / Energy-Dashboard |
|---|---|---|
| Marstek Venus D Ladeleistung | `shelly_plug_VNSE3-0_ladeleistung_w` | `max(raw,0)` |
| Marstek Venus D Entladeleistung | `shelly_plug_VNSE3-0_entladeleistung_w` | `max(-raw,0)` |
| Marstek Venus D System Power | `shelly_vnse3_0_system_power` | Rohwert; Kommentar: im Energy Dashboard `Inverted` auswählen |

Automatisch generierte Template-Entity-IDs werden nicht geraten.

---

# 5. Marstek Jupiter C Plus

## 5.1 Direkte HAME-Entities

| Entity-ID | Größe | Einheit | Semantik / Nutzung | Status |
|---|---|---:|---|---|
| `sensor.hame_energy_jpls_8h_24215ee563ae_battery_soc` | SOC | % | 4-Speicher-Gesamt-SOC | CONFIGURED 2026-08-31 |
| `sensor.hame_energy_jpls_8h_24215ee563ae_pv1_power` | PV1 Leistung | W | PV-Eingang | CONFIGURED 2026-08-31 |
| `sensor.hame_energy_jpls_8h_24215ee563ae_pv2_power` | PV2 Leistung | W | PV-Eingang | CONFIGURED 2026-08-31 |
| `sensor.hame_energy_jpls_8h_24215ee563ae_pv3_power` | PV3 Leistung | W | PV-Eingang | CONFIGURED 2026-08-31 |
| `sensor.hame_energy_jpls_8h_24215ee563ae_pv4_power` | PV4 Leistung | W | PV-Eingang | CONFIGURED 2026-08-31 |
| `sensor.hame_energy_jpls_8h_24215ee563ae_combined_power` | kombinierte Ausgangs-/Netzleistung | W | Bestandteil System-Power-Formel | CONFIGURED 2026-08-31 |

## 5.2 Templates

### Jupiter Battery System Power

- unique_id: `jupiter_battery_system_power`
- Einheit: W
- Formel: `combined_power - (PV1 + PV2 + PV3 + PV4)`.
- dokumentierte Template-Semantik: **positiv = Entladen, negativ = Laden**.
- Availability prüft nur `combined_power`; die vier PV-Eingänge werden im State mit `float(0)` verarbeitet. Damit können fehlende PV-Eingänge rechnerisch zu 0 werden, solange `combined_power` vorhanden ist. Für eine spätere **Steuerung** wäre das nicht ausreichend robust; aktuell als Anzeige-/Energy-Template behandeln.

### Kombinierte PV Eingangsleistung Jupiter C Plus

- unique_id: `combined_pv_input_jupiter_c_plus`
- Einheit: W
- Formel: Summe PV1–PV4.
- keine explizite Availability im vorliegenden Snapshot; jeder Einzelwert wird mit `float(0)` verarbeitet.
- Status: `DISPLAY/ANALYTICS`; nicht ohne zusätzliche Availability/Freshness als kritischer Regelinput verwenden.

---

# 6. Marstek B2500-D / HMJ-2

Rohpfade im letzten Config-Snapshot:

- `sensor.hame_energy_hmj_2_b42f03988c36_total_input_power`
- `sensor.hame_energy_hmj_2_b42f03988c36_total_output_power`

Template:

- Anzeigename: `Marstek HMJ-2 System Power`
- unique_id: `marstek_hmj2_system_power`
- Einheit: W
- Formel: `total_output_power - total_input_power`.
- Availability prüft nur `total_output_power`; `total_input_power` wird mit `float(0)` verwendet.

Status: `CONFIGURED 2026-08-31 / ACTIVE ROLE LIVE VERIFY`.  
Nicht Bestandteil des aktuellen 4-Speicher-Gesamt-SOC.

---

# 7. HMS-800W-2T – separater PV-Messpfad

Roh-Entities:

- `sensor.shellyplugsg3_8cbfea910720_power`
- `sensor.shellyplugsg3_8cbfea910720_energy_returned`

Templates:

| Anzeigename | unique_id | Einheit | Formel / Semantik |
|---|---|---:|---|
| PV HMS-800W-2T Leistung | `pv_hms_800_leistung` | W | `max(0,-raw_power)`; negative Rohleistung wird positive PV-Erzeugung |
| PV HMS-800W-2T | `pv_hms_800_energie` | kWh | übernimmt `energy_returned`; `total_increasing` |

Status: `CONFIGURED 2026-08-31`.

---

# 8. SBS4 Klimaanlage

Roh-Entities:

- `sensor.shellyplugsg3_b08184a64470_power`
- `sensor.shellyplugsg3_b08184a64470_energy_consumed`

Templates:

| Anzeigename | unique_id | Einheit | Rolle |
|---|---|---:|---|
| SBS4 Klimaanlage Leistung | `sbs4_klimaanlage_power` | W | Verbrauchsleistung |
| SBS4 Klimaanlage Energie | `sbs4_klimaanlage_energy` | kWh | Verbrauchsenergie, `total_increasing` |

Wichtiges Delta: ältere Snapshots verwendeten noch `..._energy`; der Snapshot vom 31.08. verwendet ausdrücklich `..._energy_consumed` und hat Vorrang.

---

# 9. SBS4 Anzucht

Roh-Entities:

- `sensor.shellyplugsg3_e4b063e51e78_power`
- `sensor.shellyplugsg3_e4b063e51e78_energy_consumed`

Templates:

| Anzeigename | unique_id | Einheit | Rolle |
|---|---|---:|---|
| SBS4 Anzucht Leistung | `sbs4_anzucht_leistung_w` | W | aktuelle Verbrauchsleistung |
| SBS4 Anzucht Energie | `sbs4_anzucht_energie_kwh` | kWh | aktuelle Verbrauchsenergie, `total_increasing` |

Aktuelle Semantik: **Verbrauch / consumed**.  
Historische frühere Nutzung desselben Steckers an einem Mikrowechselrichter bzw. anderen Energiepfad ändert die aktuelle Rolle nicht.

---

# 10. SBS4 Outdoor Steckdose

Roh-Entities:

- `sensor.shellyoutdoorsg3_b08184ee7554_power`
- `sensor.shellyoutdoorsg3_b08184ee7554_consumed_energy`
- `sensor.shellyoutdoorsg3_b08184ee7554_returned_energy`
- `sensor.shellyoutdoorsg3_b08184ee7554_current`
- `sensor.shellyoutdoorsg3_b08184ee7554_voltage`
- `sensor.shellyoutdoorsg3_b08184ee7554_frequency`

Feste Template-Entities mit explizitem `default_entity_id`:

| Entity-ID | unique_id | Einheit | Bedeutung |
|---|---|---:|---|
| `sensor.sbs4_outdoor_steckdose_leistung` | `sbs4_outdoor_steckdose_leistung` | W | Wirkleistung |
| `sensor.sbs4_outdoor_steckdose_verbrauch` | `sbs4_outdoor_steckdose_verbrauch` | kWh | bezogene/verbrauchte Energie |
| `sensor.sbs4_outdoor_steckdose_einspeisung` | `sbs4_outdoor_steckdose_einspeisung` | kWh | zurückgespeiste Energie |
| `sensor.sbs4_outdoor_steckdose_strom` | `sbs4_outdoor_steckdose_strom` | A | Strom |
| `sensor.sbs4_outdoor_steckdose_spannung` | `sbs4_outdoor_steckdose_spannung` | V | Spannung |
| `sensor.sbs4_outdoor_steckdose_frequenz` | `sbs4_outdoor_steckdose_frequenz` | Hz | Netzfrequenz |

Aktuelle Energy-Dashboard-Rolle: Verbrauchsseite über `..._verbrauch`; Einspeisezähler existiert separat und darf nicht mit Verbrauch vermischt werden.

---

# 11. „Bluetti Balco PV“ – konfigurierter Messpfad

Roh-Entities:

- `sensor.shellyplugsg3_8cbfeaa040a4_power`
- `sensor.shellyplugsg3_8cbfeaa040a4_returned_energy`

Templates:

| Anzeigename | unique_id | Einheit | Semantik |
|---|---|---:|---|
| Bluetti Balco PV Leistung | `bluetti_balco_pv_power` | W | nur negative Rohleistung wird positiv als PV-Leistung ausgegeben; sonst 0 |
| Bluetti Balco PV Energie | `bluetti_balco_pv_energy` | kWh | `returned_energy`, `total_increasing` |

Status: `CONFIGURED 2026-08-31 / PHYSICAL DEVICE MAPPING LIVE VERIFY`.

Der Name wird nicht automatisch mit `Bluetti Elite 300 + Transfer Hub` gleichgesetzt.

---

# 12. SBS4 Schuko Lader

Roh-Entities:

- `sensor.shellyplugpmg3_9070695a1600_power`
- `sensor.shellyplugpmg3_9070695a1600_energy_consumed`

Templates:

| Anzeigename | unique_id | Einheit | Semantik |
|---|---|---:|---|
| SBS4 Schuko Lader Leistung | `sbs4_schuko_lader_leistung` | W | `max(raw_power,0)` |
| SBS4 Schuko Lader Energie | `sbs4_schuko_lader_energie` | kWh | Verbrauchsenergie |

Physisch geladenes Gerät/Fahrzeug: `UNKNOWN`.

---

# 13. Garage Hinten – Shelly 3EM Gen3

## 13.1 Roh-Entities

- `sensor.shelly3em63g3_28372f367218_phase_a_power`
- `sensor.shelly3em63g3_28372f367218_phase_b_power`
- `sensor.shelly3em63g3_28372f367218_phase_c_power`

Config-Semantik je Phase:

- positive Rohleistung = Bezug.
- negative Rohleistung = Einspeisung.

## 13.2 Phasen-Templates

### Phase A

- unique `shelly3em63g3_garage_hinten_phase_a_leistung_netto` = Rohwert.
- unique `shelly3em63g3_garage_hinten_phase_a_bezug_leistung` = `max(A,0)`.
- unique `shelly3em63g3_garage_hinten_phase_a_einspeisung_leistung` = `max(-A,0)`.

### Phase B

- `shelly3em63g3_garage_hinten_phase_b_leistung_netto`
- `shelly3em63g3_garage_hinten_phase_b_bezug_leistung`
- `shelly3em63g3_garage_hinten_phase_b_einspeisung_leistung`

### Phase C

- `shelly3em63g3_garage_hinten_phase_c_leistung_netto`
- `shelly3em63g3_garage_hinten_phase_c_bezug_leistung`
- `shelly3em63g3_garage_hinten_phase_c_einspeisung_leistung`

## 13.3 Gesamt-Templates

- unique `shelly3em63g3_garage_hinten_leistung_netto_gesamt` = **A + B + C**, also echte saldierte Gesamtleistung dieses Messpunkts.
- unique `shelly3em63g3_garage_hinten_bezug_leistung_gesamt` = Summe **aller positiven Phasenanteile**.
- unique `shelly3em63g3_garage_hinten_einspeisung_leistung_gesamt` = Summe **aller negativen Phasenanteile als positiver Betrag**.

### Kritische Semantik

`Bezug Leistung Gesamt` und `Einspeisung Leistung Gesamt` sind **nicht** dasselbe wie saldierter Import bzw. Export des gesamten dreiphasigen Messpunkts, wenn verschiedene Phasen gleichzeitig unterschiedliche Richtungen haben. Für eine saldierte Regelung ist `Leistung Netto Gesamt` maßgeblich.

## 13.4 Availability

Template-Binary-Sensor:

- Anzeigename `Shelly3EM63G3 Garage Hinten Daten verfuegbar`
- unique_id `shelly3em63g3_garage_hinten_daten_verfuegbar`
- `device_class: connectivity`
- true nur wenn alle drei Roh-Phasenwerte vorhanden sind.

## 13.5 Integrierte Energie-Entities

Über HA `integration`-Sensoren mit `unit_prefix: k`, `unit_time: h`, `method: left`, `round: 2`, `max_sub_interval: 5 min`:

- `shelly3em63g3_garage_hinten_phase_a_bezug_energie`
- `shelly3em63g3_garage_hinten_phase_a_einspeisung_energie`
- `shelly3em63g3_garage_hinten_phase_b_bezug_energie`
- `shelly3em63g3_garage_hinten_phase_b_einspeisung_energie`
- `shelly3em63g3_garage_hinten_phase_c_bezug_energie`
- `shelly3em63g3_garage_hinten_phase_c_einspeisung_energie`
- `shelly3em63g3_garage_hinten_bezug_energie_gesamt`
- `shelly3em63g3_garage_hinten_einspeisung_energie_gesamt`

Die automatisch generierten `sensor.*`-Entity-IDs werden ohne explizites `default_entity_id` nicht erfunden; oben stehen die bestätigten `unique_id`-Werte.

Physischer Messumfang „Garage Hinten“: `VERIFY`.

---

# 14. Gesamt-Speicher-Aggregate

## 14.1 `sensor.verbleibende_energie_bis_mindest_soc`

Explizites `default_entity_id`: `sensor.verbleibende_energie_bis_mindest_soc`.

- Einheit: kWh.
- `device_class: energy_storage`.
- `state_class: measurement`.
- triggerbasiert.
- Quellen: SF800 Haupt/Backup, SF2400 Haupt/Backup, Venus D SOC, Jupiter C Plus SOC.

### Eingebaute Fallbacklogik

Der Sensor speichert zuletzt gültige SOC-Werte in eigenen Attributen und rechnet bei vorübergehend `unknown`/`unavailable` mit dem letzten gültigen Wert weiter.

Gespeicherte Attribute:

- `solarflow_800_plus_soc_verwendet`
- `solarflow_2400_ac_soc_verwendet`
- `venus_d_soc_verwendet`
- `jupiter_c_plus_soc_verwendet`

Zusatzattribute im letzten Snapshot:

- `quellen_mit_letztem_wert`
- `daten_vollstaendig_aktuell`
- `gesamte_nennkapazitaet_kwh: 15.04`
- `maximal_nutzbare_energie_kwh: 12.8512`
- `reserve_ausserhalb_des_nutzbereichs_kwh: 2.1888`
- Einzelkapazitäten 1.92 / 2.88 / 5.12 / 5.12 kWh
- SolarFlow-Nutzbereich 10–90 %
- Marstek-Nutzbereich 12–100 %
- `mg4_enthalten: false`

### Control-Suitability

**DISPLAY/ANALYTICS ONLY, sobald irgendeine Quelle aus letztem Wert stammt.**

Die Last-Value-Fallbacklogik ist für ein Dashboard sinnvoll, aber sie darf nicht still zum Live-Steuerinput werden. Eine Automation, die diesen Sensor nutzt, muss mindestens `daten_vollstaendig_aktuell` bzw. Quellen-Freshness berücksichtigen.

## 14.2 `sensor.gesamt_soc_hausspeicher_nutzbar`

Explizites `default_entity_id`: `sensor.gesamt_soc_hausspeicher_nutzbar`.

- Einheit: %.
- kapazitätsgewichteter Nutz-SOC auf Basis von `sensor.verbleibende_energie_bis_mindest_soc`.
- Formel: `remaining_kWh / 12.8512 * 100`, auf 0–100 begrenzt.
- 0 % = alle vier Speicher an jeweiligem Mindest-SOC.
- 100 % = SolarFlow-Systeme bei 90 %, Marstek-Systeme bei 100 %, entsprechend der im Template definierten Nutzbereiche.

Auch dieser Sensor erbt die Datenqualitätsgrenzen des vorgelagerten Aggregats.

---

# 15. Kühlschrank Cool Stash / Generic Thermostat

Direkt bestätigte Konfigurationspfade:

- Aktor: `switch.shellyplugsg3_8cbfea9b1c28`.
- Temperatursensor: `sensor.cool_stash_temperature`.
- Generic-Thermostat-Anzeigename: `Kühlschrank Cool Stash`.

Parameter im letzten Snapshot:

- `min_temp: 10`
- `max_temp: 25`
- `ac_mode: true`
- `target_temp: 17`
- `cold_tolerance: 2`
- `hot_tolerance: 2`
- `min_cycle_duration: 5 min`
- `keep_alive: 2 min`
- `initial_hvac_mode: cool`

Die automatisch erzeugte `climate.*`-Entity-ID wird ohne Live-Abgleich nicht geraten.

---

# 16. Recorder-/Systemdiagnose-Entities

Im letzten Config-Snapshot sind zwei `command_line`-Sensoren definiert:

- Anzeigename `CL Test Echo`, unique_id `cl_test_echo`, `echo 12345`, Scan 300 s.
- Anzeigename `HA DB Size`, unique_id `ha_db_size`, ermittelt Größe von `/config/home-assistant_v2.db` in MB, Scan 300 s, Timeout 10 s.

Automatisch generierte Entity-IDs werden nicht geraten.

---

# 17. Klima-Entities

Vom Nutzer als vorhanden berichtet:

- SwitchBot Thermo-Hygrometer.
- Govee Temperatur-/Feuchtesensoren.

Konkrete SwitchBot-/Govee-Entity-IDs, Gerätezuordnungen, Updateintervalle und aktuelle Availability wurden in den bisher ausgewerteten Snapshots nicht vollständig gefunden. Sie bleiben `LIVE HA EXPORT REQUIRED`.

Zusätzlich aus einem früheren HA-State-Snapshot konkret bekannt:

| Entity-ID | Friendly Name / Gerät | Größe | Einheit | beobachteter Zustand | Status |
|---|---|---|---:|---|---|
| `sensor.wifi_thermometer` | `Thermometer Grow` / friendly `Thermometer Grow sensorTemperature` | Temperatur | °C | `unavailable` im damaligen Snapshot | HISTORICAL/RECENT STATE EVIDENCE / LIVE VERIFY |

Physisches Modell und Integration dieses Thermometers bleiben `UNKNOWN`.

---

# 18. Historische/ältere HA-Pfade – nicht als aktuellen Live-State verwenden

Diese Entities sind aus älteren konkreten Nutzerangaben belegt, erscheinen aber nicht als maßgebliche aktuelle Pfade im Snapshot vom 31.08.

## 18.1 Hoymiles HiBattery 1920 AC / MS-A2

- `select.msa_280425300101_mqtt_select` – historischer MQTT-/EMS-Steuerpfad; damalige Nutzung umfasste `ems_mode`/`mqtt_ctrl`.
- `number.msa_280425300101` – historischer Leistungssteuerpfad; in damaliger Nutzung wurde u. a. 200 W Zeitplan/Power-Control genannt.
- Shelly-Gerätekennung `8cbfea91086c` – 2026-06 als HiBattery-Lade-/Entlade-Messpfad genannt; vollständige heutige Entity-ID nicht aus dem alten Fakt ableiten.

Status: `HISTORICAL USER FACT / LIVE VERIFY`.

## 18.2 OpenDTU PV

- `sensor.opendtu_f86310_yield_total` – historischer PV-Energiepfad.
- `sensor.opendtu_f86310_ac_power` – historischer PV-Leistungspfad.
- `sensor.pv_opendtu_energie` – damalige Template-Entity.
- `sensor.pv_opendtu_leistung` – damalige Template-Entity.

Status: `HISTORICAL USER FACT 2026-06 / LIVE VERIFY`.

## 18.3 SolarFlow 800 Plus ältere direkte IDs

- `sensor.eoc1nln9n465067_electriclevel` – historischer direkter SOC-Pfad.
- `number.eoc1nln9n465067_outputlimit` – historischer Output-Limit-Pfad.
- `input_boolean.solarflow_day_100_enabled` – historischer Helper.

Status: `HISTORICAL USER FACT / LIVE VERIFY`.

Historische IDs bleiben für Migration/Altlastensuche wichtig, dürfen aber nicht ohne Live-Prüfung in neue Automationen übernommen werden.

---

# 19. Pflichtprüfung vor Nutzung als Regelinput

Für jede `CONTROL-CRITICAL` Entity vor neuer/angepasster Regelung klären:

1. Entity existiert heute.
2. State ist aktuell und numerisch.
3. Messpunkt ist korrekt.
4. Vorzeichen ist physisch verifiziert.
5. Einheit stimmt.
6. nominales/reales Updateintervall ist bekannt.
7. maximal zulässiges Datenalter ist definiert.
8. `unknown`/`unavailable`/Restore-State ist explizit behandelt.
9. bei abgeleiteten Sensoren sind **alle** physikalisch notwendigen Eingangssensoren in der Availability berücksichtigt.

Kritische Templates dürfen fehlende Werte nicht mit `float(0)` in eine scheinbar gültige Regelgröße verwandeln.