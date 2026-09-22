# Лабораторные работы по Web-программированию

**Выполнил:** Абакунов Кирилл, группа K3340

В документации собраны отчеты по трем лабораторным работам, выполненным на базе одного backend-проекта: платформы для поиска партнеров в проекты.

## Навигация

- [Лабораторная работа 1](lab1.md) - FastAPI-приложение, PostgreSQL, SQLAlchemy, Alembic и JWT.
- [Лабораторная работа 2](lab2.md) - threading, multiprocessing, asyncio и параллельный парсинг.
- [Лабораторная работа 3](lab3.md) - Docker, отдельный parser-сервис, Redis и Celery.

## Общая идея проекта

В первой лабораторной реализовано основное серверное приложение: пользователи, профили, навыки, проекты, команды и задачи. Во второй лабораторной добавлены примеры параллельного и асинхронного выполнения задач. В третьей лабораторной проект контейнеризован и дополнен отдельным сервисом парсинга и очередью фоновых задач.

## Основные технологии

- Python
- FastAPI
- PostgreSQL
- SQLAlchemy
- Alembic
- Pydantic
- JWT
- threading
- multiprocessing
- asyncio
- aiohttp
- BeautifulSoup
- Docker
- Docker Compose
- Redis
- Celery

## Запуск документации локально

Из папки `students/k3340/Abakunov_Kirill/`:

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements-docs.txt
mkdocs serve
```

После запуска документация будет доступна по адресу:

```text
http://127.0.0.1:8000
```

