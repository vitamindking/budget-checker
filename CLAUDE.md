# Budget Checker – Projektdokumentation

## Was ist das?
Persönliches Budget-Tool für Benjamin & Victoria Hess.
Beantwortet die Frage: "Kann ich mir das gerade leisten?"

## Live URL
**https://vitamindking.github.io/budget-checker/**

## GitHub Repo
https://github.com/vitamindking/budget-checker

## Architektur
- **Eine einzige Datei:** `index.html` – kein Build-Step, kein Framework
- **Hosting:** GitHub Pages (auto-deploy bei jedem Push auf `main`)
- **Persistenz:** `localStorage` im Browser (pro Gerät)
- **Fonts:** DM Sans + DM Serif Display via Google Fonts CDN

## Aktuelle Features (Stand Mai 2026)
1. **3-Stufen-Ergebnis:** Grün (passt) / Orange (1 Monat sparen) / Rot (X Monate sparen)
2. **Live-Puffer-Balken:** Inputrahmen + Fortschrittsbalken färben sich beim Tippen
3. **Sparplan:** Ausklappbar mit Monatsübersicht
4. **⚙️ Einstellungen** (Gehaltszeile antippen): Gehalt, Puffer, Namen → localStorage
5. **PDF Upload + Claude API:** Kontoauszug hochladen → automatische Puffer-Berechnung

## localStorage Keys
| Key | Beschreibung | Default |
|-----|-------------|---------|
| `bc_gehalt` | Monatliches Nettogehalt | 4845 |
| `bc_puffer` | Verfügbarer Puffer | 96 |
| `bc_namen` | Namen Anzeige | Benjamin & Victoria |
| `bc_apikey` | Anthropic API Key für PDF-Analyse | – |

## Finanzplan Benjamin (Mai 2026)
- **Nettogehalt:** 4.845 € (von Glücks Krone GmbH)
- **Fixkosten:** Miete 2.000 € · KV 689 € · Unterhalt 1.000 € · Lebensmittel ~700 € · Abos 99 € · Fitness 36 € · Schulden 310 €
- **Puffer:** 96 €/Monat
- **Schulden gesamt:** ~7.223 € (TF Bank 6.400 € + Easy Bank 823 €)

## Geplante Erweiterungen (nach Priorität)
1. **Geräte-Sync** – gemeinsamer Puffer via Google Sheets JSON-Endpunkt (beide Handys sehen gleichen Wert)
2. **GoCardless Bank-API** – vollautomatische Transaktions-Pipeline über Make.com
3. **Make.com Automation** – Kette: Bank → Make → Claude → Sheets → Budget Checker

## Make.com Account
- Org ID: 7685041 · Team ID: 1718123 · Zone: eu1.make.com
- Telegram Bot bereits verbunden

## So Code ändern und deployen
```bash
# Datei bearbeiten:
/Users/vitamindking/Desktop/budget-checker/index.html

# Auf GitHub pushen (über Claude MCP – kein Terminal nötig):
# Claude nutzt mcp__github__create_or_update_file mit SHA der aktuellen Datei

# GitHub Pages deployed automatisch innerhalb ~60 Sekunden
```

## GitHub Token Speicherort (für Claude)
`~/Library/Application Support/Claude/claude_desktop_config.json`
→ `mcpServers.github.env.GITHUB_PERSONAL_ACCESS_TOKEN`

## Aktueller SHA des index.html auf GitHub
Nach jedem Push aus dem API-Response die neue SHA speichern, sonst schlägt der nächste Update fehl.
Letzter bekannter SHA: `955ee0cdde1c862ed82346fbaf0d54e6bb05d75e`
(vor Update durch PDF-Feature – nach nächstem Push neu auslesen)
