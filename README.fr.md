# arxiv-email

![GitHub Actions Workflow Status](https://img.shields.io/github/actions/workflow/status/ruiyangzhou01/arxiv-email/math-email.yml)

[English](README.md) | [中文](README.zh-CN.md) | [Deutsch](README.de.md) | [Español](README.es.md) | [Français](README.fr.md)

**arxiv-email** est une automatisation hébergée sur GitHub qui envoie chaque jour des e-mails HTML avec les articles arXiv les plus récents pour les sujets que vous suivez.

<img width="667" alt="arxiv-email" src="https://github.com/tbrazel/arxiv-email/assets/42276623/082de276-3624-4b3e-9e87-97426b165aff">

## Présentation

- Récupère les flux RSS des catégories arXiv.
- Filtre par tags principaux et secondaires optionnels.
- Génère un e-mail HTML formaté via `template/daily_feed.html`.
- S'exécute selon un planning GitHub Actions et envoie via Gmail.

## Prérequis

- Un compte GitHub pour forker le dépôt et exécuter GitHub Actions.
- Un compte Gmail et un mot de passe d'application (voir [mots de passe d'application Gmail](https://support.google.com/mail/answer/185833?hl=en-GB)).
- Une connaissance des tags arXiv (par exemple `math.AG`, `cs.LG`).

## Installation et configuration

1. **Forker ce dépôt**  
   Créez votre propre fork sur GitHub.

2. **Créer un mot de passe d'application Gmail**  
   Ce projet utilise Gmail SMTP, vous aurez donc besoin d'un mot de passe d'application.

3. **Ajouter des Secrets et Variables GitHub**  
   Dans votre fork, allez dans **Settings** → **Secrets and variables** → **Actions**, puis ajoutez :

   | Type | Nom | Description |
   | --- | --- | --- |
   | Secret | `GMAIL_USER` | Adresse Gmail utilisée pour envoyer les e-mails. |
   | Secret | `GMAIL_PASSWORD` | Mot de passe d'application Gmail pour ce compte. |
   | Variable | `FREELOADERS` | Configuration des destinataires et des abonnements. |

4. **Configurer la variable `FREELOADERS`**  
   Chaque ligne représente un destinataire, avec des tags séparés par des deux-points :

   ```text
   example0@gmail.com:primary1,secondary11,secondary12:primary2:primary3,secondary31
   example1@gmail.com:quant-ph,cs.LG,cs.CV,cs.AI,cs.GR:math.AG
   example2@gmail.com:math.NT
   ```

   L'exemple ci-dessus devient :

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

5. **Mettre à jour `subject_list.csv` si nécessaire**  
   Ajoutez les nouveaux tags et leurs intitulés complets si besoin.

6. **Ajuster le planning (optionnel)**  
   Le workflow s'exécute par défaut **à 10:00 UTC, du lundi au vendredi**. Modifiez le cron dans
   `.github/workflows/math-email.yml` si vous souhaitez un autre horaire.

## Utilisation

### GitHub Actions (recommandé)

Le workflow `math-email` s'exécute selon le planning et peut aussi être déclenché manuellement
depuis l'onglet Actions de GitHub.

### Exécuter localement

1. Installer les dépendances :

   ```bash
   python -m pip install -r requirements.txt
   ```

2. Créer un fichier `.env` avec vos identifiants et abonnements :

   ```text
   GMAIL_USER=you@gmail.com
   GMAIL_PASSWORD=your_app_password
   FREELOADERS="you@gmail.com:math.NT
   colleague@example.com:cs.LG,cs.AI"
   ```

3. Lancer le script :

   ```bash
   python daily_runner.py
   ```

## Dépannage

- **Aucun e-mail reçu** : vérifiez les logs GitHub Actions et assurez-vous que le workflow a réussi.
- **Échec de connexion Gmail** : utilisez un mot de passe d'application et activez la 2FA.
- **`ValueError` pour un tag** : vérifiez que le tag existe dans `subject_list.csv`.

## Contribution

Les contributions sont bienvenues ! Ouvrez un issue pour les bugs ou suggestions, puis soumettez un
pull request depuis votre fork.

## Licence

Ce projet est sous licence GPL-3.0.
