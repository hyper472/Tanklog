# ⛽ Tanklog

Private Spritverbrauchs-App für **Apollo 13**, **Škoda** und **Schnauferl**.  
Gehostet auf GitHub Pages, Daten in JSONBin.io gespeichert.

---

## Einmalige Einrichtung

### Schritt 1 – GitHub-Repository anlegen

1. Auf [github.com](https://github.com) einloggen (oder kostenloses Konto anlegen).
2. **New repository** klicken.
3. Name z. B. `tanklog`, Sichtbarkeit **Private** wählen → **Create repository**.
4. Die drei Dateien hochladen:
   - `index.html`
   - `robots.txt`
   - `README.md`  
   *(Drag & Drop direkt auf der Repository-Seite funktioniert.)*

### Schritt 2 – GitHub Pages aktivieren

1. Im Repository → **Settings** → **Pages**.
2. Unter *Source*: **Deploy from a branch** → Branch `main` → Ordner `/ (root)`.
3. Speichern. Nach ca. 1–2 Minuten erscheint die öffentliche URL:  
   `https://DEIN-BENUTZERNAME.github.io/tanklog/`

### Schritt 3 – JSONBin.io einrichten (gemeinsamer Datenspeicher)

JSONBin.io ist ein kostenloser REST-Datenspeicher – keine eigene Datenbank nötig.

1. Auf [jsonbin.io](https://jsonbin.io) ein **kostenloses Konto** anlegen.
2. Nach dem Login im Dashboard auf **API Keys** klicken.
3. Den **Master Key** kopieren (sieht aus wie `$2a$10$abc123...`).

### Schritt 4 – Master Key eintragen

In der Datei `index.html` ganz oben im `<script>`-Bereich:

```js
const CONFIG = {
  MASTER_KEY: '',   // ← Master Key hier eintragen (mit Anführungszeichen!)
  BIN_ID:     '',   // ← bleibt zunächst leer
};
```

**Beispiel:**
```js
const CONFIG = {
  MASTER_KEY: '$2a$10$MeinLangerSchluessel...',
  BIN_ID:     '',
};
```

Datei speichern und auf GitHub hochladen (bestehende `index.html` ersetzen).

### Schritt 5 – Ersten Start durchführen (BIN_ID ermitteln)

1. Die GitHub Pages-URL im Browser öffnen.
2. Einen Tankvorgang eintragen und speichern.
3. Es erscheint eine **Meldung mit der BIN_ID** (sieht aus wie `6642a1f1acd3cb34a836e123`).
4. Diese BIN_ID in `index.html` eintragen:

```js
const CONFIG = {
  MASTER_KEY: '$2a$10$MeinLangerSchluessel...',
  BIN_ID:     '6642a1f1acd3cb34a836e123',   // ← hier eintragen
};
```

5. Datei erneut auf GitHub hochladen → fertig!

Ab jetzt sehen alle Fahrer mit dem Link dieselben Daten in Echtzeit.

---

## Fahrzeuge hinzufügen

Über den **＋**-Knopf auf der Startseite können beliebig viele weitere Fahrzeuge
ergänzt werden – ohne dass Daten der bestehenden Fahrzeuge berührt werden.

Jedes Fahrzeug wird unter einem eigenen Schlüssel gespeichert  
(`fuel_apollo13`, `fuel_skoda`, `fuel_schnauferl`, `fuel_veh_...`).

---

## Excel-Export

Knopf **↓ Excel-Export** auf der Startseite erzeugt eine `.xlsx`-Datei  
mit einem Tabellenblatt pro Fahrzeug (Einträge nach km-Stand sortiert).

---

## Ohne JSONBin (Offline-Modus)

Wenn `MASTER_KEY` leer bleibt, speichert die App Daten im **localStorage**  
des jeweiligen Geräts. Sharing zwischen Geräten ist dann nicht möglich,  
die App funktioniert aber vollständig für einen einzelnen Nutzer.

---

## Sicherheit

- Die `robots.txt` blockiert alle bekannten Suchmaschinen und KI-Crawler.
- Der **Master Key** ist in der `index.html` sichtbar – für eine Familie  
  mit privatem Link akzeptabel. Für höhere Sicherheit: Repository auf  
  **Private** lassen und niemals den direkten GitHub-Raw-Link teilen.
- Das JSONBin-Bin ist als **privat** angelegt (nur mit Master Key lesbar).

---

## Dateien im Überblick

| Datei | Zweck |
|-------|-------|
| `index.html` | Die komplette App (HTML + CSS + JS in einer Datei) |
| `robots.txt` | Blockiert Suchmaschinen und KI-Crawler |
| `README.md` | Diese Anleitung |
