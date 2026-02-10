# arxiv-email

![GitHub Actions Workflow Status](https://img.shields.io/github/actions/workflow/status/ruiyangzhou01/arxiv-email/math-email.yml)

[English](README.md) | [中文](README.zh-CN.md) | [Deutsch](README.de.md) | [Español](README.es.md) | [Français](README.fr.md) | [日本語](README.ja.md)

**arxiv-email** es una automatización alojada en GitHub que envía correos HTML diarios con los artículos más recientes de arXiv según los temas que sigues.

<img width="667" alt="arxiv-email" src="https://github.com/tbrazel/arxiv-email/assets/42276623/082de276-3624-4b3e-9e87-97426b165aff">

## Cómo funciona

- Lee la variable de entorno `FREELOADERS` para obtener destinatarios y etiquetas suscritas.
- Obtiene feeds RSS desde `http://rss.arxiv.org/rss/<primary-tag>` para cada etiqueta primaria.
- Conserva los artículos cuyas etiquetas coinciden con la lista secundaria de esa etiqueta primaria.
- Usa `subject_list.csv` para mapear los códigos de etiquetas a nombres completos en el correo.
- Renderiza HTML con `template/daily_feed.html` y envía por Gmail SMTP.

## Requisitos

- Una cuenta de GitHub para hacer fork y ejecutar GitHub Actions.
- Una cuenta de Gmail y una contraseña de aplicación (consulta [contraseñas de aplicación de Gmail](https://support.google.com/mail/answer/185833?hl=en-GB)).
- Etiquetas de arXiv listadas en `subject_list.csv` (agrega las que necesites).
- Python 3.x si quieres ejecutar localmente.

## Configuración (GitHub Actions)

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
   | Variable | `FREELOADERS` | Destinatarios y etiquetas suscritas. |

4. **Configura la variable `FREELOADERS`**  
   Cada línea representa un destinatario, con grupos de etiquetas separados por dos puntos:

   ```text
   recipient@example.com:primary,secondary1,secondary2:primary2,secondaryA
   colleague@example.com:math.NT,math.NT
   ```

   - El primer elemento de cada grupo es la etiqueta primaria.
   - Cada etiqueta primaria necesita al menos una etiqueta secundaria. Si quieres todos los artículos de una etiqueta primaria, incluye la etiqueta primaria como secundaria (por ejemplo `math.NT,math.NT`).

5. **Actualiza `subject_list.csv` si es necesario**  
   Agrega nuevas etiquetas y sus nombres completos si faltan.

6. **Ajusta el horario (opcional)**  
   El flujo de trabajo se ejecuta **a las 10:00 UTC, de lunes a viernes**. Cambia el cron en
   `.github/workflows/math-email.yml` si necesitas otro horario.

## Ejecutar localmente

1. Instala dependencias:

   ```bash
   python -m pip install -r requirements.txt
   ```

2. Crea un archivo `.env` (lo carga `python-dotenv`) con tus credenciales y suscripciones:

   ```text
   GMAIL_USER=you@gmail.com
   GMAIL_PASSWORD=your_app_password
   FREELOADERS="you@gmail.com:math.NT,math.NT
   colleague@example.com:cs.LG,cs.LG,cs.AI"
   ```

3. Ejecuta el script:

   ```bash
   python daily_runner.py
   ```

## Solución de problemas

- **No llega ningún correo**: Revisa los registros de GitHub Actions y confirma que el flujo terminó.
- **Fallo al iniciar sesión en Gmail**: Usa una contraseña de aplicación y habilita 2FA.
- **`ValueError` con una etiqueta**: Comprueba que la etiqueta exista en `subject_list.csv`.
- **Secciones de correo vacías**: Asegúrate de que las etiquetas secundarias coincidan con las etiquetas de arXiv (incluye la etiqueta primaria como secundaria si quieres todo).

## Contribuciones

¡Las contribuciones son bienvenidas! Abre un issue para errores o sugerencias y envía un pull
request desde tu fork cuando esté listo.

## Licencia

Este proyecto está bajo la licencia GPL-3.0.
