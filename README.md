# Recolector de noticias: Corrupción en Ucayali

Este repositorio es un prototipo para recolectar noticias y publicaciones relacionadas con corrupción en Ucayali (incluye las 4 provincias: Coronel Portillo, Padre Abad, Atalaya, Purús) desde varias fuentes: RSS/Google News, NewsAPI y CrowdTangle (Facebook).

Características
- Ingesta desde: Google News RSS (query), feeds locales, NewsAPI (opcional) y CrowdTangle para páginas de Facebook.
- Extracción de texto con newspaper3k.
- Filtrado por keywords (corrupción + variantes + nombres de provincias, municipios y entidades).
- Deduplicado por hash.
- Almacenamiento en SQLite (prototipo). Fácil de migrar a Postgres/Elasticsearch.
- Contenedor Docker y GitHub Actions para ejecuciones programadas.

Requisitos
- Python 3.9+
- Dependencias en `requirements.txt`
- Variables de entorno (ver `.env.example`)

Obtención de claves
- CrowdTangle: https://www.crowdtangle.com/ (token de investigación)
- NewsAPI: https://newsapi.org/ (opcional)

Instalación y ejecución local (rápido)
1. Clona el repositorio.
2. Crea un virtualenv e instala dependencias:
   python -m venv venv
   source venv/bin/activate
   pip install -r requirements.txt
3. Copia `.env.example` a `.env` y añade tus keys.
4. Inicializa la BD:
   python scripts/init_db.py
5. Ejecuta:
   python -m src.main

Ejecución en Docker
- Build:
  docker build -t ucayali-news-collector .
- Run:
  docker run --env-file .env -v $(pwd)/data:/app/data ucayali-news-collector

Ejecución programada en GitHub Actions
- En `.github/workflows/schedule.yml` hay un ejemplo que ejecuta el colector cada hora. Añade los secrets (CROWDTANGLE_TOKEN, NEWSAPI_KEY) en GitHub Settings -> Secrets.

Dónde ajustar palabras clave y listas
- `src/config.py` contiene:
  - LIST_FEEDS: feeds RSS
  - FACEBOOK_PAGES: lista de páginas (nombres). Puedes añadir account IDs si los tienes.
  - KEYWORDS y ENTIDADES_UCAYALI (provincias, municipios, entidades locales).

Limitaciones y consideraciones legales
- Se respetan políticas: para Facebook se recomienda CrowdTangle. Evitar scraping directo de Facebook por ToS.
- Respetar robots.txt de sitios web al hacer scraping.
- Manejar rate-limits y backoff (implementado de forma básica).

Siguientes pasos recomendados
- Probar con tus API keys y revisar los logs.
- Añadir una cola/ordenador (RabbitMQ, Redis) si el volumen crece.
- Migrar a Postgres y añadir search (Elasticsearch) para consultas y deduplicado semántico.
