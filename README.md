# arxiv-email

![GitHub Actions Workflow Status](https://img.shields.io/github/actions/workflow/status/ruiyangzhou01/arxiv-email/math-email.yml)

[English](README.md) | [中文](README.zh-CN.md) | [Deutsch](README.de.md) | [Español](README.es.md) | [Français](README.fr.md) | [日本語](README.ja.md)

**arxiv-email** is a GitHub-hosted automation that sends daily HTML emails with the newest arXiv papers for the subjects you subscribe to.

<img width="667" alt="arxiv-email" src="https://github.com/tbrazel/arxiv-email/assets/42276623/082de276-3624-4b3e-9e87-97426b165aff">

## How it works

- Reads the `FREELOADERS` environment variable to find recipient emails and their subscription tags.
- Fetches RSS feeds from `http://rss.arxiv.org/rss/<primary-tag>` for each primary tag.
- Keeps papers whose tags intersect with the secondary tag list for that primary tag.
- Uses `subject_list.csv` to map tag codes to full subject names in the email.
- Renders HTML with `template/daily_feed.html` and sends mail via Gmail SMTP.

## Requirements

- A GitHub account to fork the repository and run GitHub Actions.
- A Gmail account and an app password (see [Gmail app passwords](https://support.google.com/mail/answer/185833?hl=en-GB)).
- arXiv subject tags listed in `subject_list.csv` (add any new ones you need).
- Python 3.x if you want to run locally.

## Setup (GitHub Actions)

1. **Fork this repository**  
   Create your own fork on GitHub.

2. **Create a Gmail app password**  
   This repository uses Gmail SMTP, so you will need an app password for your Gmail account.

3. **Add GitHub Secrets and Variables**  
   In your forked repository, go to **Settings** → **Secrets and variables** → **Actions**, then add:

   | Type | Name | Description |
   | --- | --- | --- |
   | Secret | `GMAIL_USER` | Gmail address used to send emails. |
   | Secret | `GMAIL_PASSWORD` | Gmail app password for that account. |
   | Variable | `FREELOADERS` | Recipient emails and tag subscriptions. |

4. **Configure the `FREELOADERS` variable**  
   Each line represents one recipient, with colon-separated tag groups:

   ```text
   recipient@example.com:primary,secondary1,secondary2:primary2,secondaryA
   colleague@example.com:math.NT,math.NT
   ```

   - The first entry in each group is the primary tag.
   - Every primary tag should include at least one secondary tag. To receive all papers for a primary tag, include the primary tag itself as a secondary tag (as in `math.NT,math.NT`).

5. **Update `subject_list.csv` if needed**  
   Add new subject tags and their full names if they are missing.

6. **Adjust the schedule (optional)**  
   The workflow runs at **10:00 UTC, Monday–Friday** by default. Update the cron schedule in
   `.github/workflows/math-email.yml` if you want a different time.

## Run locally

1. Install dependencies:

   ```bash
   python -m pip install -r requirements.txt
   ```

2. Create a `.env` file (loaded by `python-dotenv`) with your credentials and subscriptions:

   ```text
   GMAIL_USER=you@gmail.com
   GMAIL_PASSWORD=your_app_password
   FREELOADERS="you@gmail.com:math.NT,math.NT
   colleague@example.com:cs.LG,cs.LG,cs.AI"
   ```

3. Run the script:

   ```bash
   python daily_runner.py
   ```

## Troubleshooting

- **No email arrives**: Check the GitHub Actions logs and confirm the workflow completed.
- **Gmail login fails**: Ensure you are using an app password and that 2FA is enabled.
- **`ValueError` for a subject tag**: Confirm the tag exists in `subject_list.csv`.
- **Empty email sections**: Ensure the secondary tags intersect with the arXiv tags (include the primary tag as a secondary tag if you want everything).

## Contributing

Contributions are welcome! Please open an issue for bugs or suggestions, and submit pull requests
from your fork when you are ready.

## License

This project is licensed under the GPL-3.0 license.
