# CONTEXT – Tanklog

## Zweck
Private Web-App zur Dokumentation des Spritverbrauchs für drei Fahrzeuge.
Gehostet auf GitHub Pages (`hyper472.github.io/Tanklog/`), Daten in JSONBin.io.

---

## Fahrzeuge (fest eingebaut, nicht löschbar)
| ID | Anzeigename | Icon |
|----|-------------|------|
| `apollo13` | Apollo 13 | 🚀 (Emoji) |
| `skoda` | Škoda | SVG-Silhouette grau |
| `schnauferl` | Schnauferl | BMW C1 Roller, Cartoon-PNG eingebettet (base64, transparenter Hintergrund) |

Zusätzliche Fahrzeuge können per UI hinzugefügt und wieder gelöscht werden.
Sie werden unter dem Key `extra_vehicles` im JSONBin-Bin gespeichert.

---

## Technischer Aufbau
**Eine einzige Datei:** `index.html` – kein Build-Schritt, kein Framework, kein Node.

### Abhängigkeiten (alle per CDN, kein lokales Bundle)
| Bibliothek | Version | Zweck |
|------------|---------|-------|
| Google Fonts | – | Barlow Condensed (Überschriften), DM Sans (Fließtext) |
| SheetJS (xlsx) | 0.18.5 | Excel-Export (.xlsx) |
| JSONBin.io REST API | v3 | Persistenter gemeinsamer Datenspeicher |

### Farbschema
| Variable | Wert | Bedeutung |
|----------|------|-----------|
| `--bg` | `#0c1a2e` | Seitenhintergrund (Navy-Blau) |
| `--surface` | `#12263d` | Karten-Hintergrund |
| `--surface2` | `#1c3554` | Eingabefelder |
| `--amber` | `#f07800` | Akzentfarbe (Orange) |
| `--amber-dk` | `#c05e00` | Akzentfarbe dunkel |
| `--text` | `#e8ecf0` | Haupttext |
| `--red` | `#e05555` | Fehlermeldungen, Löschen |
| `--green` | `#55bb77` | Erfolgsmeldungen |

### Schriften
- **Barlow Condensed** – Fahrzeugnamen, Verbrauchswerte, Buttons
- **DM Sans** – Labels, Fließtext, Eingabefelder

---

## Datenspeicher (JSONBin.io)

### Konfiguration (oben in `<script>`)
```js
const CONFIG = {
  MASTER_KEY: '$2a$10$Xodn...',   // JSONBin Master Key
  BIN_ID:     '6a1c54e1...',      // ID des Datenbins
};
```

### Storage-Abstraction
Die App erkennt automatisch die Umgebung:
1. **JSONBin** – wenn `CONFIG.MASTER_KEY` gesetzt (GitHub Pages, Normalfall)
2. **`window.storage`** – wenn in Claude-Artifact-Umgebung ausgeführt
3. **`localStorage`** – Fallback (kein Sharing zwischen Geräten)

### Datenstruktur im Bin
```json
{
  "fuel_apollo13":    [ { "id": "uuid", "timestamp": "ISO8601", "liters": 45.5, "km": 123456 }, … ],
  "fuel_skoda":       [ … ],
  "fuel_schnauferl":  [ … ],
  "extra_vehicles":   [ { "id": "veh_...", "name": "Vespa", "icon": "🛵" } ],
  "fuel_veh_...":     [ … ]
}
```

### Caching
`_jbCache` hält den letzten gelesenen Bin-Stand im Arbeitsspeicher.
Bei jedem Schreibvorgang wird der Cache aktualisiert und das Bin per PUT überschrieben.
**Race Condition:** Bei gleichzeitigem Schreiben zweier Fahrer gewinnt der letzte Write.
Für eine Familie mit 3 Fahrzeugen akzeptabel.

---

## Funktionen (Überblick)

### Datenhaltung
| Funktion | Beschreibung |
|----------|-------------|
| `sLoad(vid)` | Einträge eines Fahrzeugs laden |
| `sSave(vid, entries)` | Einträge eines Fahrzeugs speichern |
| `loadExtraVehs()` | Zusatz-Fahrzeuge laden und in `VEHS` mergen |
| `saveExtraVehs()` | Zusatz-Fahrzeuge persistieren |

### Berechnungen
| Funktion | Beschreibung |
|----------|-------------|
| `calcLast(entries)` | Verbrauch L/100 km beim letzten Tankvorgang |
| `calcAvg(entries)` | Gesamtdurchschnitt (Gesamtliter ÷ Gesamtkilometer) |
| `calcForEntry(entries, id)` | Verbrauch für einen einzelnen Eintrag |

### UI
| Funktion | Beschreibung |
|----------|-------------|
| `renderHome()` | Startseite mit Fahrzeugkarten neu zeichnen |
| `renderVehicle()` | Fahrzeugdetailseite (Statistik + Historie) neu zeichnen |
| `showVehicle(vid)` | Zur Fahrzeugdetailseite navigieren |
| `goHome()` | Zurück zur Startseite |
| `doSave()` | Neuen Tankvorgang speichern |
| `doDel()` | Eintrag löschen (nach Bestätigung) |
| `doEdit()` | Eintrag bearbeiten |
| `doAddVeh()` | Neues Fahrzeug hinzufügen |
| `confirmDelVeh()` | Fahrzeug entfernen (nach Bestätigung) |
| `exportExcel()` | Alle Daten als .xlsx exportieren |

---

## Features
- Tankvorgang erfassen: Datum (editierbar, Vorschlag = heute), Liter, Kilometerstand
- Automatische Verbrauchsberechnung (L/100 km) pro Eintrag und als Gesamtdurchschnitt
- Erster Eintrag je Fahrzeug gilt als Referenzwert (kein Verbrauch berechenbar)
- Einträge editierbar und löschbar (je mit Sicherheitsabfrage)
- Fahrzeuge dynamisch hinzufügbar und löschbar (Stammfahrzeuge geschützt)
- Excel-Export aller Fahrzeuge als .xlsx (ein Tab pro Fahrzeug)
- Crawler-Sperre: Meta-Tags (noindex, noai) + `robots.txt`
- Responsive, mobile-first (primär für Smartphone-Nutzung)
- Bottom-Sheet-Modals, Toast-Benachrichtigungen

---

## Dateien im Repository
| Datei | Zweck |
|-------|-------|
| `index.html` | Komplette App (HTML + CSS + JS) |
| `robots.txt` | Sperrt Suchmaschinen und KI-Crawler |
| `README.md` | Einrichtungsanleitung (JSONBin, GitHub Pages) |
| `CONTEXT.md` | Diese Datei – technische Dokumentation |

---

## Bekannte Einschränkungen
- JSONBin Free Tier: 10.000 Requests/Monat (für eine Familie ausreichend)
- Race Condition bei gleichzeitigem Schreiben (last write wins)
- Master Key liegt im öffentlichen HTML-Quelltext (Repo ist public) – für einen privaten Familientracker akzeptables Risiko; bei Bedarf Key im JSONBin-Dashboard rotieren
- `robots.txt` schützt nur wohlerzogene Crawler; kein technischer Zugriffsschutz
