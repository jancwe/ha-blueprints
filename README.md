# ha-blueprints

Home Assistant Blueprints von @jancwe.

## IKEA BILRESA + MYGGBETT – Lichtsteuerung mit lernbarer Helligkeit

[![Blueprint importieren](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fjancwe%2Fha-blueprints%2Fblob%2Fmain%2Fblueprints%2Fautomation%2Fjancwe%2Fbilresa_myggbett_light.yaml)

Datei: `blueprints/automation/jancwe/bilresa_myggbett_light.yaml`

Steuert ein Licht über einen **IKEA BILRESA** Taster (2 Tasten) und einen **IKEA MYGGBETT**
Bewegungsmelder, beide direkt per **Matter over Thread** in Home Assistant eingebunden.

### Verhalten

| Auslöser | Verhalten |
|---|---|
| Bewegung (Modus `automatik`) | Licht an mit Bewegungs-Helligkeit (Tag/Nacht), Modus `bewegung` |
| Keine Bewegung + Nachlauf | Licht aus, zurück zu `automatik` |
| Taste 1 kurz | Licht an mit Schalter-Helligkeit, Modus `schalter_an` (Bewegungslogik pausiert) |
| Taste 2 kurz | Licht aus, Modus `schalter_aus` (Bewegung schaltet nicht wieder ein) |
| Taste 1 / 2 lang | Heller / Dunkler dimmen, solange gehalten |
| Loslassen nach Dimmen | Neue Helligkeit wird in den passenden Helfer gespeichert und zukünftig verwendet |
| Raum lange leer (Bypass-Zeit) | Licht aus, zurück zu `automatik` |
| Nacht aktiv | Bewegung nutzt niedrige Nacht-Helligkeit, Schalter bleibt bei normaler Helligkeit |

### Zustandsmodell

| Modus | Bedeutung |
|---|---|
| `automatik` | Licht aus, wartet auf Bewegung |
| `bewegung` | Licht an durch Bewegung, geht nach Nachlauf aus |
| `schalter_an` | Licht an per Taste, Bewegungslogik pausiert |
| `schalter_aus` | Licht aus per Taste, Bewegung schaltet nicht ein |

### Benötigte Helfer

Vor dem Anlegen der Automation müssen folgende Helfer existieren (siehe `examples/wohnzimmer_helpers.yaml`):

- `input_select` mit den Optionen `automatik`, `bewegung`, `schalter_an`, `schalter_aus`
- `input_number` (1–100 %) für **Schalter**, **Bewegung Tag**, **Bewegung Nacht**
- `timer` für **Nachlauf** und **Bypass-Reset**
- optional: `schedule`, `binary_sensor` oder `input_boolean` als Nachtmodus

### Einrichtung

1. Helfer anlegen (UI oder Package aus `examples/wohnzimmer_helpers.yaml`).
2. Blueprint importieren (Button oben) oder Datei nach `config/blueprints/automation/jancwe/` kopieren.
3. Automation aus dem Blueprint erstellen und Geräte/Helfer zuordnen (Beispiel: `examples/wohnzimmer_automation.yaml`).
4. Event-Typen prüfen: Unter *Entwicklerwerkzeuge → Zustände* die `event`-Entitäten der BILRESA-Tasten
   beobachten. Standard ist `short_release` (kurz) und `long_press` (lang); abweichende Werte können im
   Blueprint-Dialog angepasst werden.

### Hinweise

- Dimmen im Modus `bewegung` ändert den Modus nicht – das Licht geht nach dem Nachlauf aus, nutzt aber beim
  nächsten Mal die neue Helligkeit.
- Wird bei ausgeschaltetem Licht Taste 1 lang gedrückt, startet das Licht bei 1 % und wechselt in `schalter_an`.
- Die Option *Nacht-Helligkeit per Dimmer lernen* kann deaktiviert werden, damit der Nachtwert fix bleibt.

## Lizenz

MIT
