# Budget Checker – Projektdokumentation

## Was ist das?
Persönliches Budget-Tool für Benjamin & Victoria Hess.
Beantwortet die Frage: "Kann ich mir das leisten?"

## Live URL
**https://vitamindking.github.io/budget-checker/**

## GitHub Repo
https://github.com/vitamindking/budget-checker

## Architektur
- **Eine einzige Datei:** `index.html` + `icon.png` + `manifest.json` – kein Build-Step, kein Framework
- **Hosting:** GitHub Pages (auto-deploy bei jedem Push auf `main`)
- **Persistenz:** `localStorage` im Browser (pro Gerät)
- **Geräte-Sync:** JSONBin.io (beide Handys sehen gleichen Stand)
- **Fonts:** DM Sans + DM Serif Display via Google Fonts CDN

## Deploy-Methode (kein lokales git-Repo!)
```bash
# Datei lokal bearbeiten, dann:
cd /tmp/bc-fresh
git pull
cp /Users/vitamindking/Desktop/budget-checker/index.html .
# ggf. auch icon.png / manifest.json kopieren
git add <dateien>
git commit -m "Beschreibung"
TOKEN=$(python3 -c "import json; d=json.load(open('/Users/vitamindking/Library/Application Support/Claude/claude_desktop_config.json')); print(d['mcpServers']['github']['env']['GITHUB_PERSONAL_ACCESS_TOKEN'])")
git push "https://${TOKEN}@github.com/vitamindking/budget-checker.git" main
# GitHub Pages deployed automatisch innerhalb ~60 Sekunden
```
Hinweis: `/tmp/bc-fresh` ist bereits geklont. Falls nicht vorhanden:
`git clone https://github.com/vitamindking/budget-checker.git /tmp/bc-fresh`

## Features (Stand Mai 2026)

### Hauptfunktion
- Betrag eingeben → live Puffer-Balken färbt sich
- "Prüfen ✓" → Grün / Orange (1 Monat sparen) / Rot (X Monate + Sparplan)
- **"Ausgabe bestätigen ✓"** erscheint bei Grün → zieht Betrag sofort vom aktiven Topf ab → Sync

### 3 Töpfe (oben als Grid)
| Topf | Default | Verhalten |
|------|---------|-----------|
| Monatlich | 90,85 € | Wird von PDF-Analyse gesetzt ("Puffer übernehmen") |
| Hausinvest. | 300,00 € | Wächst monatlich um Sparrate, Ausgaben auto-abgezogen |
| Notfall | 222,99 € | Wächst monatlich um Sparrate, Ausgaben auto-abgezogen |

Topf antippen → Input und Prüfung beziehen sich auf diesen Topf.

### PDF-Upload + Claude API
- Kontoauszug hochladen → Claude (Haiku) analysiert automatisch
- Erkennt: Gehalt, alle 18 Fixkosten, sonstige Ausgaben
- Sonstige Ausgaben → Claude schlägt Topf vor (monatlich/hausinvest/notfall)
- **hausinvest/notfall Ausgaben** → werden bei neuem Monat **automatisch abgezogen**
- **monatlich Ausgaben** → manueller "Topf abziehen"-Button
- Bei neuem Monat (neue PDF): Sparraten werden **automatisch addiert**, TF Bank Schuld reduziert
- "Puffer übernehmen ✓" setzt den Monatlichen Topf auf den berechneten Wert

### Finanz-Vorschlag (Karte ganz unten)
- TF Bank: Restschuld + Monate bis schuldenfrei + genaues Datum
- Easy Bank: Restschuld + automatischer Vorschlag (Einmalzahlung oder Rate)
- Sparraten: Zeigt monatliche Raten + aktuellen Topf-Stand (nur wenn > 0 konfiguriert)

### Geräte-Sync (JSONBin.io)
Synct: puffer, tfbankSchuld, easybankSchuld, aokSchuld, toepfe (Beträge + Raten), lastAnalysis, lastAnalysisMonth
- Push: nach PDF-Upload, Ausgabe bestätigen, Puffer übernehmen, Einstellungen speichern
- Pull: beim App-Start

### Einstellungen (⚙️ – Gehaltszeile antippen)
- Monatliches Nettogehalt
- Monatlicher Puffer (€)
- Hausinvest. Topf-Betrag (€) + Sparrate (€/Monat)
- Notfall Topf-Betrag (€) + Sparrate (€/Monat)
- Easy Bank Restschuld (€)
- AOK Rückzahlung Restschuld (€)
- Name(n)
- JSONBin Master Key + Bin ID
- Anthropic API Key

### PWA / iPhone App-Icon
- `icon.png` (1024×1024) im Repo
- `manifest.json` für standalone App-Modus
- `<link rel="apple-touch-icon">` in HTML → zeigt custom Icon beim "Zum Homescreen hinzufügen"

## localStorage Keys
| Key | Beschreibung | Default |
|-----|-------------|---------|
| `bc_gehalt` | Monatliches Nettogehalt | 4845.28 |
| `bc_puffer` | Monatlicher Puffer (Topf Monatlich) | 90.85 |
| `bc_namen` | Anzeigename | Benjamin & Victoria |
| `bc_apikey` | Anthropic API Key | – |
| `bc_tfbank_schuld` | TF Bank Restschuld (dynamisch) | 6200.00 |
| `bc_easybank_schuld` | Easy Bank Restschuld (dynamisch) | 377.44 |
| `bc_aok_schuld` | AOK Rückzahlung Restschuld (dynamisch) | 2476.66 |
| `bc_synckey` | JSONBin Master Key | – |
| `bc_syncbin` | JSONBin Bin ID | – |
| `bc_topf_hausinvest` | Hausinvest Topf-Stand | 300.00 |
| `bc_topf_notfall` | Notfall Topf-Stand | 222.99 |
| `bc_topf_hausinvest_rate` | Hausinvest monatliche Sparrate | 0 |
| `bc_topf_notfall_rate` | Notfall monatliche Sparrate | 0 |
| `bc_last_analysis` | Letzter PDF-Analyse-JSON | – |
| `bc_last_analysis_month` | Monat der letzten Analyse | – |

## Finanzdaten Benjamin Hess (Mai 2026)
- **Nettogehalt:** 4.845,28 € (von Glücks Krone GmbH, ca. 24.-27. des Monats)
- **18 Fixkosten gesamt: 4.154,43 €**

| Fixkosten | Betrag | Empfänger |
|-----------|--------|-----------|
| Miete kalt | 1.500,00 € | Schamma |
| Gas | 170,00 € | Knauber |
| Strom | 100,00 € | – |
| Wasser + Wasserfilter | 55,00 € | Volkswasser |
| Müllabholung | 40,00 € | – |
| Krankenversicherung | 689,48 € | uniVersa |
| Fitness | 36,00 € | MYNEOpur |
| Internet | 53,00 € | 1&1 |
| Apple Abos | 65,00 € | Apple |
| Instagram Verified | 16,99 € | Meta |
| CAYA Briefkasten | 12,99 € | CAYA |
| Gmail 100GB | 1,99 € | Google |
| Finanzguru Plus | 0,99 € | Finanzguru |
| ALDI Talk | 2,99 € | ALDI |
| TF Bank Rate | 310,00 € | TF Bank |
| AOK Rückzahlung | 100,00 € | AOK |
| Kindesunterhalt | 1.000,00 € | Rüya |
| Lebensmittel & Drogerie | 700,00 € | – |

- **Monatlicher Puffer:** −9,15 € (90,85 € − 100 € AOK-Rate → rechnerisch im Minus)
- **Schulden:** TF Bank 6.200 € (Rate 310 €/Mon) · AOK Rückzahlung 2.476,66 € (Rate 100 €/Mon) · Easy Bank 377,44 € (flexibel)

## Claude API Details
- Model: `claude-haiku-4-5-20251001`
- max_tokens: 2500
- Kontoauszug-Textlimit: 18.000 Zeichen
- Header: `anthropic-dangerous-direct-browser-access: true`

## Geplante Erweiterungen (nach Priorität)
1. **GoCardless Bank-API** – vollautomatische Transaktions-Pipeline
2. **Make.com Automation** – Bank → Make → Claude → Sheets → Budget Checker + Telegram
3. **Google Sheets Sync** (Alternative zu JSONBin für Geräte-Sync)

## Make.com Account
- Org ID: 7685041 · Team ID: 1718123 · Zone: eu1.make.com
- Telegram Bot bereits verbunden

## Wichtige Hinweise für Claude
- **Nur ändern was explizit verlangt wird** – keine unnötigen Refactors
- API Key wird NIE im Chat eingegeben – nur in den App-Einstellungen (⚙️)
- URL bleibt immer https://vitamindking.github.io/budget-checker/
- `/tmp/bc-fresh` ist der git-Klon für Deploys
