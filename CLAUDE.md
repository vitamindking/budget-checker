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
cd /tmp/bc-fresh
git pull
cp /Users/vitamindking/Desktop/budget-checker/index.html .
git add index.html
git commit -m "Beschreibung"
TOKEN=$(python3 -c "import json; d=json.load(open('/Users/vitamindking/Library/Application Support/Claude/claude_desktop_config.json')); print(d['mcpServers']['github']['env']['GITHUB_PERSONAL_ACCESS_TOKEN'])")
git push "https://${TOKEN}@github.com/vitamindking/budget-checker.git" main
# GitHub Pages deployed automatisch innerhalb ~60 Sekunden
```
Falls `/tmp/bc-fresh` fehlt: `git clone https://github.com/vitamindking/budget-checker.git /tmp/bc-fresh`

## Features

### Hauptfunktion
- Betrag eingeben → live Puffer-Balken färbt sich
- "Prüfen ✓" → Grün / Orange (1 Monat sparen) / Rot (X Monate + Sparplan)
- **"Ausgabe bestätigen ✓"** erscheint bei Grün → zieht Betrag sofort vom aktiven Topf ab → Sync

### 3 Töpfe (oben als Grid)
| Topf | Aktuell (Mai 2026) | Verhalten |
|------|-------------------|-----------|
| Monatlich | 221,38 € | Wird von PDF-Analyse gesetzt ("Puffer übernehmen") |
| Hausinvest. | 521,39 € | Wächst monatlich um Sparrate, Ausgaben auto-abgezogen |
| Notfall | 402,68 € | Wächst monatlich um Sparrate, Ausgaben auto-abgezogen |

Topf antippen → Input und Prüfung beziehen sich auf diesen Topf.

### PDF-Upload + Claude API
- Kontoauszug hochladen → Claude (Haiku) analysiert automatisch
- Erkennt: Gehalt, alle 18 Fixkosten, sonstige Ausgaben
- Sonstige Ausgaben → Claude schlägt Topf vor (monatlich/hausinvest/notfall)
- **hausinvest/notfall Ausgaben** → werden bei neuem Monat **automatisch abgezogen**
- **monatlich Ausgaben** → manueller "Topf abziehen"-Button
- Bei neuem Monat (neue PDF): TF Bank + AOK Schuld werden automatisch reduziert
- "Puffer übernehmen ✓" setzt den Monatlichen Topf
- Bei positivem Puffer (Zulage): Easy Bank zuerst tilgen, dann 50/25/25 auf Töpfe

### Überschuss-Verteilung (bei Zulage)
- Easy Bank zuerst tilgen → Rest: 50% Notfall · 25% Hausinvest · 25% Monatlich
- Prozentsätze in Einstellungen konfigurierbar

### Finanz-Vorschlag (Karte ganz unten)
- TF Bank: Restschuld + Monate bis schuldenfrei + genaues Datum
- Easy Bank: Restschuld + automatischer Vorschlag
- Sparraten: monatliche Raten + aktueller Topf-Stand

### Geräte-Sync (JSONBin.io)
- Push: nach PDF-Upload, Ausgabe bestätigen, Puffer übernehmen, Einstellungen speichern
- Pull: beim App-Start (Timestamp-Vergleich: lokale Änderung gewinnt immer)

### Einstellungen (⚙️ – Gehaltszeile antippen)
Gehalt · Puffer · Hausinvest Betrag+Rate · Notfall Betrag+Rate · Easy Bank · AOK Schuld · Namen · JSONBin Key+BinID · Anthropic API Key · Überschuss-Split %

## localStorage Keys (aktuelle Defaults)
| Key | Beschreibung | Aktuell |
|-----|-------------|---------|
| `bc_gehalt` | Nettogehalt | 4845.28 |
| `bc_puffer` | Monatlicher Puffer | 221.38 |
| `bc_tfbank_schuld` | TF Bank Restschuld | 6200.00 |
| `bc_easybank_schuld` | Easy Bank Restschuld | 0.00 |
| `bc_aok_schuld` | AOK Restschuld | 2476.66 |
| `bc_topf_hausinvest` | Hausinvest Topf | 521.39 |
| `bc_topf_notfall` | Notfall Topf | 402.68 |
| `bc_topf_hausinvest_rate` | Hausinvest Sparrate | 0 |
| `bc_topf_notfall_rate` | Notfall Sparrate | 0 |
| `bc_last_analysis_month` | Letzter analysierter Monat | 05/2026 |

## Finanzdaten Benjamin Hess (Stand: 27. Mai 2026)
- **Nettogehalt:** 4.845,28 € (Glücks Krone GmbH, ca. 24.–27. des Monats)
- **Fixkosten gesamt: 4.790,41 €**
- **Ehrlicher Basispuffer: +54,87 €/Monat**

| Fixkosten | Betrag | Empfänger | Hinweis |
|-----------|--------|-----------|---------|
| Miete kalt | 1.500,00 € | Schamma | |
| Gas | 170,00 € | Knauber | |
| Strom | 100,00 € | – | Erstmals ab Juni 2026 |
| Wasser + Wasserfilter | 30,00 € | Volkswasser | 418 € war einmalige Jahresrechnung Mai 26 |
| Müllabholung | 40,00 € | – | Noch nicht in Mai gebucht |
| Krankenversicherung | 689,48 € | uniVersa | |
| Fitness | 36,00 € | MYNEOpur | |
| Internet | 45,00 € | 1&1 | Tatsächlich 44,42 €, gerundet 45 |
| Apple Abos | 33,98 € | Apple | CapCut 23,99 + iCloud+ 9,99 |
| Instagram Verified | 16,99 € | Meta | Standard (Business-Abo läuft aus) |
| CAYA Briefkasten | 12,99 € | CAYA | |
| Gmail 100GB | 1,99 € | Google | Erscheint als APPLE.COM/BILL |
| Finanzguru Plus | 0,99 € | Finanzguru | |
| ALDI Talk | 2,99 € | ALDI | |
| TF Bank Rate | 310,00 € | TF Bank | |
| AOK Rückzahlung | 100,00 € | AOK | Kommt ca. 15. Juni |
| Kindesunterhalt | 1.000,00 € | Rüya | |
| Lebensmittel & Drogerie | 700,00 € | – | |

## Schulden (Stand 27. Mai 2026)
| Schuld | Restbetrag | Rate | Frei |
|--------|-----------|------|------|
| TF Bank | 6.200,00 € | 310 €/Mon | Jan 2028 |
| AOK Rückzahlung | 2.476,66 € | 100 €/Mon | Jun 2028 |
| Easy Bank | 0,00 € | – | **getilgt Mai 2026** |

## Töpfe (Stand 27. Mai 2026)
| Topf | Betrag | Herkunft |
|------|--------|---------|
| Monatlich | 221,38 € | 25% der Zulage nach Easy Bank |
| Hausinvest | 521,39 € | Basis 300 + 25% Zulage |
| Notfall | 402,68 € | Basis 222,99 + 50% Zulage − 263,08 € Arzt (BFS) |

**Zulage Mai 2026:** 3× Auslagen Glücks Krone (Media Markt 739,99 + Sofa 333 + Ikea 689,99 = 1.762,98 €)
**Easy Bank gesamt getilgt:** 877,44 € (377,44 + 500 €)
**Verteilungsbasis:** 1.762,98 − 877,44 = 885,54 €

## Migrations-Flags (NIEMALS löschen)
| Flag | Was |
|------|-----|
| `bc_mig_aok1_l` | AOK als Fixkosten hinzugefügt |
| `bc_mig_pdf_reset_v1` | Reset nach fehlerhaftem PDF-Upload |
| `bc_mig_zulage_korrektur_v1` | Zulage 50/25/25 korrekt verteilt |
| `bc_mig_mai26_v1` | TF Bank reset 6200, BFS Arzt 263,08 aus Notfall |
| `bc_mig_eb500_v1` | Easy Bank 877,44 total (500+377,44) eingerechnet |

## Claude API Details
- Model: `claude-haiku-4-5-20251001`
- max_tokens: 2500
- Kontoauszug-Textlimit: 18.000 Zeichen
- Header: `anthropic-dangerous-direct-browser-access: true`
- Schulden-Prompt: **dynamisch** aus CONFIG (nicht mehr hardcodiert)

## Geplante Erweiterungen (nach Priorität)
1. **GoCardless + C24** – vollautomatische Transaktions-Pipeline ohne PDF
2. **Make.com Automation** – Bank → Make → Claude → Budget Checker + Telegram
3. **Investment-Feature** – ab Schuldenfreiheit (Jan/Jun 2028): Überschuss in ETF/BTC

## Make.com Account
- Org ID: 7685041 · Team ID: 1718123 · Zone: eu1.make.com
- Telegram Bot bereits verbunden

## Wichtige Hinweise für Claude
- **Nur ändern was explizit verlangt wird** – keine unnötigen Refactors
- API Key wird NIE im Chat eingegeben – nur in App-Einstellungen (⚙️)
- Deploy immer über `/tmp/bc-fresh` mit Token aus `claude_desktop_config.json`
- Bei neuen Migrationen: lokale Flags + syncPush-Payload + syncPull-Handler
- Migrations-Reihenfolge beachten: spätere Migrationen überschreiben frühere Topf-Werte
