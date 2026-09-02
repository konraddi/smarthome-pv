# Device Register

Stand: 2026-09-02

Physische Geräte und ihre Projektrolle. Entity-IDs gehören nach `ENTITY_REGISTER.md`; Protokolle/Integrationen nach `INTEGRATION_REGISTER.md`.

| ID | Gerät | Kategorie | Rolle | Status | Relevante Fakten | Evidenz |
|---|---|---|---|---|---|---|
| DEV-METER-001 | Shelly Pro 3EM | Smart Meter | zentrale Phasen-/Netzmessung | ACTIVE | für saldierende Haus-/Netzbilanz verwendet | REPORTED / USER |
| DEV-PV-001 | Hoymiles HM-1500-4T | Mikrowechselrichter | PV-Erzeugung | ACTIVE/KNOWN | exakte Modulzuordnung noch zu inventarisieren | REPORTED / USER |
| DEV-PV-002 | Hoymiles HMS-800W-2T | Mikrowechselrichter | PV-Erzeugung | ACTIVE/KNOWN | exakte Modulzuordnung noch zu inventarisieren | REPORTED / USER |
| DEV-PV-003 | Hoymiles HMS-2000-4T | Mikrowechselrichter | PV-Erzeugung | ACTIVE/KNOWN | exakte Modulzuordnung noch zu inventarisieren | REPORTED / USER |
| DEV-PV-004 | SMA String-WR | Stringwechselrichter | PV-Erzeugung | ACTIVE/KNOWN | ca. 4,5 kW; Modell UNKNOWN | REPORTED / USER |
| DEV-BAT-001 | Zendure SolarFlow 2400 AC | AC-Speicher | Hausenergieoptimierung | ACTIVE | mit AB3000X 2,88 kWh; HA-Regelung aktiv | REPORTED / USER |
| DEV-BAT-002 | Zendure SolarFlow 800 Plus | PV-/AC-Speicher | Zusatzladung/Nachtentladung | ACTIVE | integrierter Speicher 1,92 kWh; HA-Regelung aktiv | REPORTED / USER |
| DEV-BAT-003 | Marstek Venus D | Speicher | zusätzlicher Speicher / Regelabhängigkeit | ACTIVE/PARTIAL TELEMETRY | HAME Relay + hm2mqtt; Update ca. 1 min; SOC zeitweise unavailable | REPORTED / USER |
| DEV-BAT-004 | Jackery 2000 Ultra | Speicher/Powerstation | Energiesystem | KNOWN | aktuelle HA-Steuerrolle UNKNOWN | REPORTED / USER |
| DEV-BAT-005 | Bluetti Elite 300 + Transfer Hub | Speicher | Energiesystem | KNOWN | aktuelle HA-Steuerrolle UNKNOWN | REPORTED / USER |
| DEV-BAT-006 | Marstek Venus E Mini | Speicher/Testgerät | Integration/Testkontext | KNOWN | Review-Details gehören ins Heise-Repo | REPORTED / USER |
| DEV-PLUG-001 | Shelly Plug S Gen3 – Anzucht | Smart Plug | Verbrauchsmessung | ACTIVE | historisch einmal Mikrowechselrichter; aktuelle Nutzung Verbrauch | REPORTED / USER |
| DEV-PLUG-002 | Outdoor Plug | Smart Plug | Verbrauchsmessung | ACTIVE/KNOWN | in Energy-Verbrauchslogik aufgenommen | REPORTED / USER |
| DEV-CLIMATE-001 | SwitchBot Thermo-Hygrometer | Klimasensor | Temperatur/Feuchte | ACTIVE/KNOWN | genaue Anzahl/IDs noch zu inventarisieren | REPORTED / USER |
| DEV-CLIMATE-002 | Govee Klimasensor(en) | Klimasensor | Temperatur/Feuchte | ACTIVE/KNOWN | genaue Modelle/IDs noch zu inventarisieren | REPORTED / USER |

## PV-Gesamtbestand

- 29 Module
- ungefähr 8–8,5 kWp

Exakte Modulmodelle, Wp je Modul, String-/WR-Zuordnung und Ausrichtung: `INVENTORY GAP`.

## Registerregeln

- Neue physische Hardware erhält stabile `DEV-*`-ID.
- Austausch eines Geräts gegen andere Hardware = neue Geräte-ID, wenn Identität/Revision für Diagnose relevant ist.
- Firmwarestände nicht als stabile Geräteeigenschaft behandeln; aktueller Stand gehört nach `CURRENT_SYSTEM_STATUS.md` oder in die jeweilige Integrations-/Gerätenotiz mit Datum.
- Seriennummern und Secrets nicht im öffentlichen Repository speichern.