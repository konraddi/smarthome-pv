# Energy Flow Model

Stand: 2026-09-02
Evidenzbasis: HA-Konfigurationssnapshot 2026-08-31 + SolarFlow-2400-AC-Automationssnapshot 2026-08-27.

Diese Datei definiert das gemeinsame physikalische und regelungstechnische Modell. Konkrete Entity-Details stehen in `ENTITY_REGISTER.md`.

## 1. Kanonischer Netzfluss

Kanonische Entity:

`sensor.shelly_3em_netto_leistung`

Quellen:

- Phase A: `sensor.shellypro3em_2cbcbbb84c18_phase_a_active_power`
- Phase B: `sensor.shellypro3em_2cbcbbb84c18_phase_b_active_power`
- Phase C: `sensor.shellypro3em_2cbcbbb84c18_phase_c_active_power`

Formel:

`P_grid = P_L1 + P_L2 + P_L3`

Konfigurationsvorzeichen:

> **P_grid > 0 = Netzbezug**  
> **P_grid < 0 = Netzeinspeisung**

Diese Konvention ist durch die Template-Struktur dokumentiert, weil daraus getrennte Import-/Export-Sensoren gebildet werden. Ein physischer Gegencheck der CT-/Messrichtung bleibt als Acceptance Test sinnvoll.

## 2. Warum saldieren

Der Hauszähler ist saldierend. Daher zählt für die Speicherregelung die dreiphasige Nettoleistung.

Beispiel:

- L1 +500 W Bezug
- L2 -800 W Einspeisung
- L3 +100 W Bezug

`P_grid = -200 W` → netto 200 W Einspeisung.

Eine Automation darf hier nicht die 500-W-Bezugsphase isoliert ausregeln.

## 3. Abgrenzung Garage-Hinten-Sensoren

Beim separaten Shelly 3EM Gen3 „Garage Hinten“ existieren:

- `Netto Gesamt = A + B + C`.
- `Bezug Gesamt = Summe positiver Phasenwerte`.
- `Einspeisung Gesamt = Summe der Beträge negativer Phasenwerte`.

Wichtig:

`Bezug Gesamt` und `Einspeisung Gesamt` können gleichzeitig positiv sein. Sie sind keine gegenseitig ausschließenden saldierten Netzzustände.

Für eine saldierte Regelung dieses Messpunkts ist ausschließlich `Netto Gesamt` physikalisch passend.

Exakter physischer Umfang des Garage-Hinten-Messpunkts: `VERIFY`.

## 4. Speichervorzeichen

### SolarFlow 2400 AC – separater Shelly

Raw: `sensor.shellyplugsg3_8cbfea9fc83c_power`.

- positiv = Laden / AC-Aufnahme.
- negativ = Entladen / AC-Abgabe.

### Marstek Venus D – separater Shelly

Raw: `sensor.shellyplugpmg3_d885ac0c44a4_power`.

- positiv = Laden.
- negativ = Entladen.

### SolarFlow 800 Plus System Power

Template:

`P_system = packinputpower - outputpackpower`

Konfigurationssemantik:

- positiv = Entladen.
- negativ = Laden.

### Jupiter C Plus System Power

`P_system = combined_power - (PV1 + PV2 + PV3 + PV4)`

Konfigurationssemantik:

- positiv = Entladen.
- negativ = Laden.

### HMJ-2/B2500-D System Power

`P_system = total_output_power - total_input_power`

Interpretation entspricht einer Output-minus-Input-Bilanz. Aktive physische Rolle heute `VERIFY`.

## 5. SolarFlow-2400-AC-Regelmodell

Letzter vollständiger Snapshot: 2026-08-27.

### 5.1 Regelziel

Tagesziel:

`P_grid_target = -200 W`

also ungefähr 200 W Rest-Netzeinspeisung.

Der Regler versucht bewusst nicht exakt 0 W zu halten.

### 5.2 Warum Rest-Einspeisung

Die Reserve dient dazu:

- Mess-/Kommunikationslatenz abzufangen.
- kurze Lastsprünge nicht sofort in Netzbezug kippen zu lassen.
- um den Nullpunkt weniger aggressiv zu pendeln.
- einem langsameren Speicherpfad Headroom zu lassen.

200 W ist ein Parameter der konkreten Automationen, keine universelle Systemgrenze.

### 5.3 Rohwert + 30-s-Mittel

Zusätzliche Entity:

`sensor.shelly_3em_netto_leistung_30s_mittel`

Filter:

- `time_simple_moving_average`
- 30 s Fenster
- precision 0

Die Automation nutzt die beiden Signale **asymmetrisch**:

- Rohwert darf sofort nach unten regeln.
- zum Hochregeln muss sowohl Rohwert als auch 30-s-Mittel genügend Überschuss anzeigen.

Damit wird nicht die gesamte Regelung geglättet. Die Strategie ist:

> **FAST DOWN / CONFIRMED UP**

## 6. SolarFlow-2400-AC-Sollwertberechnung

Parameter des Snapshots:

- Soft-Minimum: 80 W.
- Maximum der Automationslogik: 1600 W.
- Aufwärtsrampe: max. +500 W pro Lauf.
- regulärer Tageslauf: 15 s.
- Ziel-Export: 200 W.

Mit aktuellem SolarFlow-Input-Limit `P_current`:

`P_desired_raw = P_current - P_grid_raw - 200 W - P_venus_reserved`

`P_desired_avg = P_current - P_grid_30s - 200 W - P_venus_reserved`

Beide werden auf 80–1600 W begrenzt.

Zum Hochregeln:

`P_up_ceiling = min(P_desired_raw, P_desired_avg)`

`P_next = min(P_up_ceiling, P_current + 500 W)`

Zum Herunterregeln:

wenn `P_desired_raw < P_current`, dann direkt `P_desired_raw` verwenden.

## 7. Venus-D-Prioritätsmodell

Venus-D-SOC:

`sensor.hame_energy_vnsd_0_682499eefa15_battery_state_of_charge`

Venus-D-Leistung:

`sensor.shellyplugpmg3_d885ac0c44a4_power`

Parameter des 2400-Snapshots:

- SOC-Prioritätsgrenze: 90 %.
- angenommene maximale Venus-Ladeleistung für Headroom: 2200 W.
- deutliche Venus-Entladung: weniger als -100 W.

### 7.1 SOC < 90 %, beide Venus-Werte gültig

`P_venus_charge = max(P_venus,0)`

`P_venus_reserved = max(2200 W - P_venus_charge,0)`

SolarFlow 2400 AC bekommt damit nur den Überschuss oberhalb der für Venus D reservierten möglichen Ladeaufnahme.

### 7.2 SOC >= 90 %, Werte gültig

`P_venus_reserved = 0`

Grund im Automationskommentar: Venus D kann bei hohem SOC/Balancing seine Aufnahme reduzieren; eine starre Reserve würde dann Überschuss unnötig ins Netz lassen.

### 7.3 Venus-SOC oder Leistung fehlt

`P_venus_reserved = 2200 W`

Das ist der aktuelle Failsafe im letzten Snapshot:

- **kein letzter SOC als Live-Wert**.
- stattdessen maximale konservative Reserve.

### 7.4 Venus D entlädt

Wenn `P_venus < -100 W`:

SolarFlow 2400 AC wird im Tagesfenster auf 80 W Input begrenzt.

Ziel: nicht gleichzeitig Venus D entladen und SolarFlow AC-seitig stark laden.

## 8. Freshness vs. Availability

Der bestehende Regler prüft `has_value()`.

Damit sind getrennt:

- `unknown`/`unavailable` → erkannt.
- numerischer, aber alter Wert → **nicht zwingend erkannt**.

Das ist aktuell der wichtigste verbleibende Modell-Gap beim Venus-D-Pfad.

Für einen Control-Sensor mit ungefähr minutenweiser Telemetrie muss zusätzlich definiert werden:

- erwartetes Updateintervall.
- maximal erlaubtes Alter.
- welcher Zeitstempel technisch zuverlässig nutzbar ist.
- Verhalten bei stale, aber numerischem State.

Grundsatz:

> **AVAILABLE IS NOT NECESSARILY FRESH.**

## 9. Nachtmodell SolarFlow 2400 AC

Im Snapshot 2026-08-27:

- 20:00–08:00.
- `Output mode`.
- `outputLimit = 190 W`.
- `inputLimit = 0 W`.
- alle 5 min Zustandsnachzug.

Dieser Wert ist der Snapshot-Parameter, nicht zwingend der heutige Live-Wert.

## 10. SolarFlow 800 Plus – bekannte Rolle

Vollständige aktuelle YAML fehlt, Funktionsmodell aus Projektverlauf:

- 11:00–17:00 zusätzliche AC-Ladung.
- bis ungefähr 800 W.
- Freigabe u. a. abhängig vom SolarFlow-2400-AC-SOC.
- Ziel ca. 200 W Rest-Einspeisung.
- Pause bei relevanter Venus-D-Entladung.
- tagsüber Output-Limit 0 für PV-Passthrough.
- Nachtentladung ungefähr 120 W im letzten bekannten Funktionsstand.

Ohne exakte aktuelle YAML keine genaue dynamische Gleichung, Rampen- oder Freshness-Logik kanonisieren.

## 11. Multi-Speicher-Koordination

Aktuelles konfiguriertes SOC-Aggregat enthält:

- SolarFlow 800 Plus.
- SolarFlow 2400 AC.
- Venus D.
- Jupiter C Plus.

Diese vier Speichersysteme teilen sich teilweise denselben Haus-Netzfluss als Informationsgrundlage. Daraus folgen potenzielle Regelschleifen:

- Speicher A lädt → anderer Regler sieht zusätzlichen Hausverbrauch → Speicher B entlädt.
- Speicher A entlädt → anderer Regler sieht vermeintlichen Überschuss → Speicher B lädt.

Deshalb:

> **ONE CONTROLLER PER ACTUATOR UNLESS COORDINATION IS VERIFIED.**

Der 2400-AC-Regler versucht einen Teil dieser Koordination explizit über Venus-Headroom und Entladeblock zu lösen.

## 12. Gesamt-SOC-Modell

Konfiguriert:

- SF800: 1,92 kWh; 10–90 %.
- SF2400: 2,88 kWh; 10–90 %.
- Venus D: 5,12 kWh; 12–100 %.
- Jupiter C Plus: 5,12 kWh; 12–100 %.

Nennkapazität 15,04 kWh.  
Nutzbereich 12,8512 kWh.

`Verbleibende Energie bis Mindest-SoC` speichert bei Sensorausfall letzte gültige SOC-Werte.

Daher müssen zwei Ebenen strikt getrennt werden:

### Anzeige-/Bilanzmodell

Fallbackwerte zulässig, wenn Quellen mit letztem Wert kenntlich gemacht werden.

### Steuerungsmodell

Nur ausreichend frische Einzelwerte oder ein Aggregat mit nachweislich vollständig aktuellen Quellen verwenden.

## 13. Leistung vs. Energie

- W = Momentanleistung.
- Wh/kWh = Energie über Zeit.

HA `integration`-Sensoren in Garage Hinten integrieren W zu kWh mit:

- Methode `left`.
- max. Unterintervall 5 min.

Energy-Dashboard-Zähler müssen zusätzlich die reale Richtung und `state_class` korrekt abbilden.

## 14. Einweg-Verbraucher / Erzeuger

### Anzucht

Aktuelle physikalische Rolle: Verbrauch. Energie aus `energy_consumed`.

### Klimaanlage

Verbrauchspfad aus `energy_consumed`.

### HMS-800W-2T-Messung

PV-Erzeugung: negative Plug-Rohleistung wird als positive PV-Leistung verwendet; Energie aus `energy_returned`.

### Bluetti-Balco-PV-Pfad

gleiches Vorzeichenprinzip im konfigurierten Template; heutiges physisches Mapping noch verifizieren.

## 15. Failsafe-Grundsätze des Modells

- keine kritische `unknown`/`unavailable`-Quelle still auf 0 setzen.
- Last-Value-Fallback nicht als Current-State tarnen.
- Regelgeschwindigkeit nicht höher wählen als Informationsqualität erlaubt.
- langsame HAME-Telemetrie als Prioritäts-/Statuspfad behandeln; schnelle Netzregelung auf den schnelleren zentralen Messpfad stützen.
- ein erfolgreicher Service-Call ist erst Stufe 1 der Erfolgskette:

`COMMAND → DEVICE STATE → PHYSICAL POWER FLOW → GRID FEEDBACK`.

## 16. Noch physisch zu verifizieren

- tatsächliche CT-/Vorzeichenrichtung des zentralen Shelly Pro 3EM durch klaren Bezug-/Export-Test.
- reales Updateintervall des zentralen Shelly-Pfads.
- reale Update-/Stale-Charakteristik des Venus-D-SOC.
- exakter physischer Messpunkt „Garage Hinten“.
- aktuelle Control Ownership je Speicher.
- heutige 800-Plus-Regelgleichung nach YAML-Import.