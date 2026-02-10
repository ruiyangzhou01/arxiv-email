# arxiv-email

![GitHub Actions Workflow Status](https://img.shields.io/github/actions/workflow/status/ruiyangzhou01/arxiv-email/math-email.yml)

[English](README.md) | [中文](README.zh-CN.md) | [Deutsch](README.de.md) | [Español](README.es.md) | [Français](README.fr.md)

**arxiv-email** is a GitHub-hosted automation that sends daily HTML emails with the newest arXiv papers for the subjects you follow.

<img width="667" alt="arxiv-email" src="https://github.com/tbrazel/arxiv-email/assets/42276623/082de276-3624-4b3e-9e87-97426b165aff">

## Overview

- Pulls RSS feeds from arXiv subject categories.
- Filters papers by the primary and optional secondary tags you configure.
- Renders a formatted HTML email using `template/daily_feed.html`.
- Runs on a GitHub Actions schedule and sends mail via Gmail.

## Prerequisites

- A GitHub account to fork the repository and run GitHub Actions.
- A Gmail account and an app password (see [Gmail app passwords](https://support.google.com/mail/answer/185833?hl=en-GB)).
- Familiarity with arXiv subject tags (e.g., `math.AG`, `cs.LG`).

## Installation & setup

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
   | Variable | `FREELOADERS` | Recipient emails and subject subscriptions. |

4. **Configure the `FREELOADERS` variable**  
   Each line represents one recipient, with a colon-separated list of subject tags:

   ```text
   example0@gmail.com:primary1,secondary11,secondary12:primary2:primary3,secondary31
   example1@gmail.com:quant-ph,cs.LG,cs.CV,cs.AI,cs.GR:math.AG
   example2@gmail.com:math.NT
   ```

   The example above becomes:

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

5. **Update `subject_list.csv` if needed**  
   Add new subject tags and their full names if they are missing.

6. **Adjust the schedule (optional)**  
   The workflow runs at **10:00 UTC, Monday–Friday** by default. Update the cron schedule in
   `.github/workflows/math-email.yml` if you want a different time.

## Usage

### GitHub Actions (recommended)

The `math-email` workflow runs on the schedule above and can also be triggered manually from the
Actions tab in GitHub.

### Run locally

1. Install dependencies:

   ```bash
   python -m pip install -r requirements.txt
   ```

2. Create a `.env` file with your credentials and subscriptions:

   ```text
   GMAIL_USER=you@gmail.com
   GMAIL_PASSWORD=your_app_password
   FREELOADERS="you@gmail.com:math.NT
   colleague@example.com:cs.LG,cs.AI"
   ```

3. Run the script:

   ```bash
   python daily_runner.py
   ```

## Troubleshooting

- **No email arrives**: Check the GitHub Actions logs and confirm the workflow completed.
- **Gmail login fails**: Ensure you are using an app password and that 2FA is enabled.
- **`ValueError` for a subject tag**: Confirm the tag exists in `subject_list.csv`.

## Contributing

Contributions are welcome! Please open an issue for bugs or suggestions, and submit pull requests
from your fork when you are ready.

## License

This project is licensed under the GPL-3.0 license.
