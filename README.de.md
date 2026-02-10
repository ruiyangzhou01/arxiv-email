# arxiv-email

![GitHub Actions Workflow Status](https://img.shields.io/github/actions/workflow/status/ruiyangzhou01/arxiv-email/math-email.yml)

[English](README.md) | [中文](README.zh-CN.md) | [Deutsch](README.de.md) | [Español](README.es.md) | [Français](README.fr.md) | [日本語](README.ja.md)

**arxiv-email** ist eine auf GitHub gehostete Automatisierung, die täglich HTML-E-Mails mit den neuesten arXiv-Papieren zu deinen abonnierten Themen versendet.

<img width="667" alt="arxiv-email" src="https://github.com/tbrazel/arxiv-email/assets/42276623/082de276-3624-4b3e-9e87-97426b165aff">

## Funktionsweise

- Liest die Umgebungsvariable `FREELOADERS`, um Empfänger und Abonnements zu ermitteln.
- Ruft RSS-Feeds von `http://rss.arxiv.org/rss/<primary-tag>` für jedes Haupt-Tag ab.
- Behält nur Papers, deren Tags mit der Sekundärliste des Haupt-Tags übereinstimmen.
- Nutzt `subject_list.csv`, um Tags in ausgeschriebene Fachgebiete im E-Mail-Text umzuwandeln.
- Rendert HTML mit `template/daily_feed.html` und versendet über Gmail SMTP.

## Voraussetzungen

- Ein GitHub-Konto, um das Repository zu forken und GitHub Actions auszuführen.
- Ein Gmail-Konto und ein App-Passwort (siehe [Gmail-App-Passwörter](https://support.google.com/mail/answer/185833?hl=en-GB)).
- arXiv-Tags aus `subject_list.csv` (bei Bedarf ergänzen).
- Python 3.x, wenn du lokal ausführst.

## Einrichtung (GitHub Actions)

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
   | Variable | `FREELOADERS` | Empfänger und Tag-Abonnements. |

4. **`FREELOADERS` konfigurieren**  
   Jede Zeile steht für einen Empfänger und enthält durch Doppelpunkte getrennte Tag-Gruppen:

   ```text
   recipient@example.com:primary,secondary1,secondary2:primary2,secondaryA
   colleague@example.com:math.NT,math.NT
   ```

   - Der erste Eintrag pro Gruppe ist das Haupt-Tag.
   - Jedes Haupt-Tag braucht mindestens ein Sekundär-Tag. Wenn du alle Papers eines Haupt-Tags erhalten willst, füge das Haupt-Tag selbst als Sekundär-Tag hinzu (z. B. `math.NT,math.NT`).

5. **`subject_list.csv` bei Bedarf erweitern**  
   Ergänze fehlende Tags und deren ausgeschriebene Namen.

6. **Zeitplan anpassen (optional)**  
   Der Workflow läuft standardmäßig **um 10:00 UTC, Montag bis Freitag**. Ändere bei Bedarf den
   Cron-Ausdruck in `.github/workflows/math-email.yml`.

## Lokal ausführen

1. Abhängigkeiten installieren:

   ```bash
   python -m pip install -r requirements.txt
   ```

2. Eine `.env`-Datei (wird von `python-dotenv` geladen) mit Zugangsdaten und Abos anlegen:

   ```text
   GMAIL_USER=you@gmail.com
   GMAIL_PASSWORD=your_app_password
   FREELOADERS="you@gmail.com:math.NT,math.NT
   colleague@example.com:cs.LG,cs.LG,cs.AI"
   ```

3. Skript starten:

   ```bash
   python daily_runner.py
   ```

## Fehlerbehebung

- **Keine E-Mail erhalten**: Prüfe die GitHub Actions-Logs und ob der Workflow erfolgreich war.
- **Gmail-Login schlägt fehl**: Verwende ein App-Passwort und aktiviere 2FA.
- **`ValueError` bei einem Tag**: Stelle sicher, dass das Tag in `subject_list.csv` enthalten ist.
- **Leere E-Mail-Abschnitte**: Die Sekundär-Tags müssen mit den arXiv-Tags übereinstimmen (füge das Haupt-Tag als Sekundär-Tag hinzu, wenn du alles erhalten willst).

## Mitwirken

Beiträge sind willkommen! Bitte eröffne ein Issue für Fehler oder Vorschläge und reiche dann einen
Pull Request aus deinem Fork ein.

## Lizenz

Dieses Projekt steht unter der GPL-3.0-Lizenz.
