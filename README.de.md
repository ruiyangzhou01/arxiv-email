# arxiv-email

![GitHub Actions Workflow Status](https://img.shields.io/github/actions/workflow/status/ruiyangzhou01/arxiv-email/math-email.yml)

[English](README.md) | [中文](README.zh-CN.md) | [Deutsch](README.de.md) | [Español](README.es.md) | [Français](README.fr.md)

**arxiv-email** ist eine auf GitHub gehostete Automatisierung, die täglich HTML-E-Mails mit den neuesten arXiv-Papieren zu deinen abonnierten Themen versendet.

<img width="667" alt="arxiv-email" src="https://github.com/tbrazel/arxiv-email/assets/42276623/082de276-3624-4b3e-9e87-97426b165aff">

## Überblick

- Ruft RSS-Feeds aus arXiv-Fachkategorien ab.
- Filtert nach primären und optionalen sekundären Tags, die du konfigurierst.
- Rendert eine formatierte HTML-Mail über `template/daily_feed.html`.
- Läuft nach Zeitplan in GitHub Actions und versendet über Gmail.

## Voraussetzungen

- Ein GitHub-Konto, um das Repository zu forken und GitHub Actions auszuführen.
- Ein Gmail-Konto und ein App-Passwort (siehe [Gmail-App-Passwörter](https://support.google.com/mail/answer/185833?hl=en-GB)).
- Grundkenntnisse zu arXiv-Tags (z. B. `math.AG`, `cs.LG`).

## Installation & Einrichtung

1. **Fork dieses Repository**  
   Erstelle deinen eigenen Fork auf GitHub.

2. **Gmail-App-Passwort erstellen**  
   Dieses Projekt verwendet Gmail SMTP, daher benötigst du ein App-Passwort.

3. **GitHub Secrets und Variables hinzufügen**  
   Gehe in deinem Fork zu **Settings** → **Secrets and variables** → **Actions** und füge hinzu:

   | Typ | Name | Beschreibung |
   | --- | --- | --- |
   | Secret | `GMAIL_USER` | Gmail-Adresse, die die E-Mails sendet. |
   | Secret | `GMAIL_PASSWORD` | Gmail-App-Passwort für dieses Konto. |
   | Variable | `FREELOADERS` | Empfänger und Abonnement-Konfiguration. |

4. **`FREELOADERS` konfigurieren**  
   Jede Zeile steht für einen Empfänger und enthält eine durch Doppelpunkte getrennte Tag-Liste:

   ```text
   example0@gmail.com:primary1,secondary11,secondary12:primary2:primary3,secondary31
   example1@gmail.com:quant-ph,cs.LG,cs.CV,cs.AI,cs.GR:math.AG
   example2@gmail.com:math.NT
   ```

   Das Beispiel wird wie folgt ausgewertet:

   ```python
   SUBSCRIPTIONS0 = {
       "primary1": ["secondary11", "secondary12"],
       "primary2": [],
       "primary3": ["secondary31"],
   }

   SUBSCRIPTIONS1 = {
       "quant-ph": ["cs.LG", "cs.CV", "cs.AI", "cs.GR"],
       "math.AG": [],
   }

   SUBSCRIPTIONS2 = {
       "math.NT": [],
   }
   ```

5. **`subject_list.csv` bei Bedarf erweitern**  
   Ergänze fehlende Tags und deren ausgeschriebene Namen.

6. **Zeitplan anpassen (optional)**  
   Der Workflow läuft standardmäßig **um 10:00 UTC, Montag bis Freitag**. Ändere bei Bedarf den
   Cron-Ausdruck in `.github/workflows/math-email.yml`.

## Verwendung

### GitHub Actions (empfohlen)

Der Workflow `math-email` läuft nach dem Zeitplan und kann im GitHub-Tab „Actions“ auch manuell
ausgelöst werden.

### Lokal ausführen

1. Abhängigkeiten installieren:

   ```bash
   python -m pip install -r requirements.txt
   ```

2. Eine `.env`-Datei mit Zugangsdaten und Abos anlegen:

   ```text
   GMAIL_USER=you@gmail.com
   GMAIL_PASSWORD=your_app_password
   FREELOADERS="you@gmail.com:math.NT
   colleague@example.com:cs.LG,cs.AI"
   ```

3. Skript starten:

   ```bash
   python daily_runner.py
   ```

## Fehlerbehebung

- **Keine E-Mail erhalten**: Prüfe die GitHub Actions-Logs und ob der Workflow erfolgreich war.
- **Gmail-Login schlägt fehl**: Verwende ein App-Passwort und aktiviere 2FA.
- **`ValueError` bei einem Tag**: Stelle sicher, dass das Tag in `subject_list.csv` enthalten ist.

## Mitwirken

Beiträge sind willkommen! Bitte eröffne ein Issue für Fehler oder Vorschläge und reiche dann einen
Pull Request aus deinem Fork ein.

## Lizenz

Dieses Projekt steht unter der GPL-3.0-Lizenz.
