# Budget Checker – Task-Liste

## ✅ Abgeschlossen
- [x] Budget Checker HTML v1 (3 States: grün/orange/rot)
- [x] Live-Puffer-Balken (Inputrahmen + Fortschrittsbalken beim Tippen)
- [x] Sparplan ausklappbar (chevron toggle)
- [x] Einstellungen Bottom-Sheet (Gehalt, Puffer, Namen → localStorage)
- [x] Komma-Eingabe korrigiert (parseFloat mit ,/. Handling)
- [x] Deutsche Zahlenformatierung (toLocaleString de-DE)
- [x] CONFIG-Block zentralisiert
- [x] GitHub Repo erstellt: vitamindking/budget-checker
- [x] GitHub Pages aktiviert → https://vitamindking.github.io/budget-checker/
- [x] CLAUDE.md + TASKS.md erstellt

## 🔄 In Arbeit
- [ ] **PDF Upload + Claude API** – Kontoauszug hochladen → Claude analysiert Transaktionen → Puffer automatisch berechnen
  - PDF.js für client-side Text-Extraktion
  - Anthropic API direkt aus Browser (`anthropic-dangerous-direct-browser-access: true`)
  - API Key in Einstellungen speicherbar
  - Ergebnis: diskretionäre Ausgaben + verfügbarer Puffer
  - "Puffer übernehmen"-Button

## 📋 Geplant
- [ ] **Geräte-Sync** – Puffer-Wert über geteilte URL-Quelle synchronisieren
  - Option A: Google Sheets öffentliche JSON-Zelle
  - Option B: Einfacher Netlify Function Endpunkt
  - Beide Handys (Beni + Victoria) sehen immer gleichen Puffer
  - Konfigurierbare "Live-URL" in Einstellungen

- [ ] **GoCardless Bank-Anbindung**
  - Account erstellen auf bankaccountdata.gocardless.com
  - Bank prüfen (welche Bank hat Beni?)
  - 90-Tage PSD2 Reauth einplanen

- [ ] **Make.com Automation-Pipeline**
  - Szenario: GoCardless → Transaktionen holen → Claude kategorisieren → Google Sheets → Budget Checker Puffer aktualisieren
  - Täglich automatisch ausführen
  - Bei Budget-Überschreitung: Telegram-Nachricht

- [ ] **PWA** – Homescreen-Icon, Offline-Modus

## 📝 Technische Notizen
- **Deploy:** Immer SHA aus vorherigem API-Response mitgeben beim Update
- **CORS:** Anthropic API braucht Header `anthropic-dangerous-direct-browser-access: true`
- **PDF.js CDN:** `https://cdn.jsdelivr.net/npm/pdfjs-dist@3.11.174/build/pdf.min.js`
- **Modell für PDF-Analyse:** `claude-haiku-4-5-20251001` (günstig + schnell)
