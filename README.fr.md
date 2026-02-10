# arxiv-email

![GitHub Actions Workflow Status](https://img.shields.io/github/actions/workflow/status/ruiyangzhou01/arxiv-email/math-email.yml)

[English](README.md) | [中文](README.zh-CN.md) | [Deutsch](README.de.md) | [Español](README.es.md) | [Français](README.fr.md) | [日本語](README.ja.md)

**arxiv-email** est une automatisation hébergée sur GitHub qui envoie chaque jour des e-mails HTML avec les articles arXiv les plus récents pour les sujets que vous suivez.

<img width="667" alt="arxiv-email" src="https://github.com/tbrazel/arxiv-email/assets/42276623/082de276-3624-4b3e-9e87-97426b165aff">

## Fonctionnement

- Lit la variable d’environnement `FREELOADERS` pour obtenir les destinataires et les tags abonnés.
- Récupère les flux RSS depuis `http://rss.arxiv.org/rss/<primary-tag>` pour chaque tag principal.
- Conserve les articles dont les tags croisent la liste secondaire du tag principal.
- Utilise `subject_list.csv` pour associer les codes de tags aux intitulés complets dans l’e-mail.
- Génère le HTML avec `template/daily_feed.html` et envoie via Gmail SMTP.

## Prérequis

- Un compte GitHub pour forker le dépôt et exécuter GitHub Actions.
- Un compte Gmail et un mot de passe d’application (voir [mots de passe d’application Gmail](https://support.google.com/mail/answer/185833?hl=en-GB)).
- Des tags arXiv listés dans `subject_list.csv` (ajoutez ceux dont vous avez besoin).
- Python 3.x si vous exécutez en local.

## Configuration (GitHub Actions)

1. **Forker ce dépôt**  
   Créez votre propre fork sur GitHub.

2. **Créer un mot de passe d’application Gmail**  
   Ce projet utilise Gmail SMTP, vous aurez donc besoin d’un mot de passe d’application.

3. **Ajouter des Secrets et Variables GitHub**  
   Dans votre fork, allez dans **Settings** → **Secrets and variables** → **Actions**, puis ajoutez :

   | Type | Nom | Description |
   | --- | --- | --- |
   | Secret | `GMAIL_USER` | Adresse Gmail utilisée pour envoyer les e-mails. |
   | Secret | `GMAIL_PASSWORD` | Mot de passe d’application Gmail pour ce compte. |
   | Variable | `FREELOADERS` | Destinataires et tags abonnés. |

4. **Configurer la variable `FREELOADERS`**  
   Chaque ligne représente un destinataire, avec des groupes de tags séparés par des deux-points :

   ```text
   recipient@example.com:primary,secondary1,secondary2:primary2,secondaryA
   colleague@example.com:math.NT,math.NT
   ```

   - Le premier élément de chaque groupe est le tag principal.
   - Chaque tag principal a besoin d’au moins un tag secondaire. Pour recevoir tous les articles d’un tag principal, ajoutez le tag principal comme secondaire (ex. `math.NT,math.NT`).

5. **Mettre à jour `subject_list.csv` si nécessaire**  
   Ajoutez les nouveaux tags et leurs intitulés complets si besoin.

6. **Ajuster le planning (optionnel)**  
   Le workflow s’exécute par défaut **à 10:00 UTC, du lundi au vendredi**. Modifiez le cron dans
   `.github/workflows/math-email.yml` si vous souhaitez un autre horaire.

## Exécuter localement

1. Installer les dépendances :

   ```bash
   python -m pip install -r requirements.txt
   ```

2. Créer un fichier `.env` (chargé par `python-dotenv`) avec vos identifiants et abonnements :

   ```text
   GMAIL_USER=you@gmail.com
   GMAIL_PASSWORD=your_app_password
   FREELOADERS="you@gmail.com:math.NT,math.NT
   colleague@example.com:cs.LG,cs.LG,cs.AI"
   ```

3. Lancer le script :

   ```bash
   python daily_runner.py
   ```

## Dépannage

- **Aucun e-mail reçu** : vérifiez les logs GitHub Actions et assurez-vous que le workflow a réussi.
- **Échec de connexion Gmail** : utilisez un mot de passe d’application et activez la 2FA.
- **`ValueError` pour un tag** : vérifiez que le tag existe dans `subject_list.csv`.
- **Sections d’e-mail vides** : assurez-vous que les tags secondaires correspondent aux tags arXiv (ajoutez le tag principal comme secondaire si vous voulez tout).

## Contribution

Les contributions sont bienvenues ! Ouvrez un issue pour les bugs ou suggestions, puis soumettez un
pull request depuis votre fork.

## Licence

Ce projet est sous licence GPL-3.0.
