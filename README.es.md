# arxiv-email

![GitHub Actions Workflow Status](https://img.shields.io/github/actions/workflow/status/ruiyangzhou01/arxiv-email/math-email.yml)

[English](README.md) | [中文](README.zh-CN.md) | [Deutsch](README.de.md) | [Español](README.es.md) | [Français](README.fr.md)

**arxiv-email** es una automatización alojada en GitHub que envía correos HTML diarios con los artículos más recientes de arXiv según los temas que sigues.

<img width="667" alt="arxiv-email" src="https://github.com/tbrazel/arxiv-email/assets/42276623/082de276-3624-4b3e-9e87-97426b165aff">

## Resumen

- Obtiene feeds RSS de categorías de arXiv.
- Filtra por etiquetas primarias y secundarias opcionales que configures.
- Renderiza un correo HTML con `template/daily_feed.html`.
- Se ejecuta con un calendario de GitHub Actions y envía por Gmail.

## Requisitos previos

- Una cuenta de GitHub para hacer fork y ejecutar GitHub Actions.
- Una cuenta de Gmail y una contraseña de aplicación (consulta [contraseñas de aplicación de Gmail](https://support.google.com/mail/answer/185833?hl=en-GB)).
- Familiaridad con etiquetas de arXiv (por ejemplo, `math.AG`, `cs.LG`).

## Instalación y configuración

1. **Haz fork de este repositorio**  
   Crea tu propio fork en GitHub.

2. **Crea una contraseña de aplicación de Gmail**  
   Este proyecto usa SMTP de Gmail, por lo que necesitas una contraseña de aplicación.

3. **Añade Secrets y Variables en GitHub**  
   En tu fork ve a **Settings** → **Secrets and variables** → **Actions** y agrega:

   | Tipo | Nombre | Descripción |
   | --- | --- | --- |
   | Secret | `GMAIL_USER` | Dirección de Gmail que enviará los correos. |
   | Secret | `GMAIL_PASSWORD` | Contraseña de aplicación de Gmail. |
   | Variable | `FREELOADERS` | Configuración de destinatarios y suscripciones. |

4. **Configura la variable `FREELOADERS`**  
   Cada línea representa un destinatario, con etiquetas separadas por dos puntos:

   ```text
   example0@gmail.com:primary1,secondary11,secondary12:primary2:primary3,secondary31
   example1@gmail.com:quant-ph,cs.LG,cs.CV,cs.AI,cs.GR:math.AG
   example2@gmail.com:math.NT
   ```

   El ejemplo anterior se convierte en:

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

5. **Actualiza `subject_list.csv` si es necesario**  
   Agrega nuevas etiquetas y sus nombres completos si faltan.

6. **Ajusta el horario (opcional)**  
   El flujo de trabajo se ejecuta **a las 10:00 UTC, de lunes a viernes**. Cambia el cron en
   `.github/workflows/math-email.yml` si necesitas otro horario.

## Uso

### GitHub Actions (recomendado)

El flujo `math-email` se ejecuta según el calendario y también se puede iniciar manualmente desde
la pestaña Actions de GitHub.

### Ejecutar localmente

1. Instala dependencias:

   ```bash
   python -m pip install -r requirements.txt
   ```

2. Crea un archivo `.env` con tus credenciales y suscripciones:

   ```text
   GMAIL_USER=you@gmail.com
   GMAIL_PASSWORD=your_app_password
   FREELOADERS="you@gmail.com:math.NT
   colleague@example.com:cs.LG,cs.AI"
   ```

3. Ejecuta el script:

   ```bash
   python daily_runner.py
   ```

## Solución de problemas

- **No llega ningún correo**: Revisa los registros de GitHub Actions y confirma que el flujo terminó.
- **Fallo al iniciar sesión en Gmail**: Usa una contraseña de aplicación y habilita 2FA.
- **`ValueError` con una etiqueta**: Comprueba que la etiqueta exista en `subject_list.csv`.

## Contribuciones

¡Las contribuciones son bienvenidas! Abre un issue para errores o sugerencias y envía un pull
request desde tu fork cuando esté listo.

## Licencia

Este proyecto está bajo la licencia GPL-3.0.
