# Скрипт для защиты лабораторных работ (5 минут на каждую)

**Студент:** Абакунов Кирилл, группа K3340  
**Формат:** что сказать + что показать на экране + на что обратить внимание.

---

## Lab 1 — Сетевое программирование на Python

### 1) Коротко про цель (20–30 сек)
- Цель: отработать базовые протоколы и подходы: **UDP**, **TCP**, простой **HTTP**, **многопоточность**, обработка **GET/POST**.
- Все задания локальные, без внешних зависимостей — только стандартная библиотека Python.

### 2) Что сделал (1 мин)
- **Задание 1 (UDP):** клиент/сервер, обмен сообщениями (порт 9999).
  - Использованы `socket.socket(AF_INET, SOCK_DGRAM)` для UDP, `recvfrom()` и `sendto()` для обмена.
  - Сервер слушает на порту 9999, клиент отправляет сообщение и получает ответ.
- **Задание 2 (TCP):** сервер принимает \(a,b\), возвращает гипотенузу (порт 9998).
  - TCP-сокет (`SOCK_STREAM`), `accept()` для новых подключений, `recv()`/`send()` для обмена.
  - Парсинг строки формата "a,b", вычисление через `math.sqrt(a² + b²)`.
- **Задание 3 (HTTP):** мини HTTP-сервер, отдаёт `index.html` или 404 (порт 8080).
  - Ручной парсинг HTTP-запроса (извлечение метода и пути), чтение файла `index.html`.
  - Формирование HTTP-ответа: статус-код, заголовки (`Content-Type: text/html`), тело ответа.
- **Задание 4 (чат):** многопользовательский TCP-чат, потоки + broadcast (порт 9997).
  - На каждого клиента создаётся отдельный поток (`threading.Thread`).
  - Общий список клиентов (`clients = []`) с блокировкой (`threading.Lock`) для потокобезопасности.
  - Функция `broadcast()` рассылает сообщения всем, кроме отправителя.
- **Задание 5 (GET/POST):** мини веб‑сервер "журнал оценок", хранение данных в памяти процесса (порт 8000).
  - Разбор HTTP-запроса: определение метода (`GET` или `POST`), парсинг query-параметров и body.
  - Данные хранятся в словаре `grades = {}` в памяти процесса.
  - После POST — редирект (`302 Found`) обратно на `/` для обновления страницы.

### 3) Что показать (2–3 мин)
- **UDP**:
  - Запустить `task1_server.py`, потом `task1_client.py`.
  - Показать, что сервер получает и отвечает.
- **TCP математика**:
  - Запустить `task2_server.py`, `task2_client.py`.
  - Показать формат ввода и полученный результат.
- **HTTP статическая страница**:
  - Запустить `task3_server.py`, открыть `http://localhost:8080/`.
  - Показать отдачу `index.html`, можно коротко упомянуть простую сборку HTTP-ответа.
- **Чат**:
  - Запустить `task4_server.py`, в 2 терминалах `task4_client.py`.
  - Показать рассылку сообщений всем подключённым.
- **GET/POST журнал**:
  - Запустить `task5_server.py`, открыть `http://localhost:8000/`.
  - Добавить запись формой, показать что она появилась.

### 4) На что в коде обратить внимание (30–60 сек)
- **UDP**: `recvfrom()` получает данные + адрес отправителя, `sendto()` отправляет по адресу.
- **TCP**: `accept()` создаёт новый сокет для клиента, `recv()`/`send()` для обмена данными.
- **Чат**: 
  - `threading.Thread(target=handle_client, args=(...))` для каждого клиента.
  - `clients_lock.acquire()`/`release()` или `with clients_lock:` для безопасного доступа к списку.
  - Обработка исключений при отправке (клиент отключился) и удаление из списка.
- **GET/POST**: 
  - Парсинг HTTP-запроса: разделение на строки, извлечение метода и пути.
  - Для POST: парсинг `application/x-www-form-urlencoded` через `urllib.parse.parse_qs()`.
  - Формирование HTML с динамическим списком оценок из словаря.

---

## Lab 2 — Django сайт для бронирования отелей

### 1) Коротко про цель (20–30 сек)
- Цель: сделать **классическое Django web-приложение** (шаблоны, формы, авторизация) + запуск в Docker Compose.
- Вариант: **список отелей / номера / бронирования / отзывы**.

### 2) Что сделал (1–1.5 мин)
- **Модели данных** (`hotels/models.py`):
  - `Hotel` — отель (название, владелец, адрес, описание).
  - `RoomType` — тип номера с `ForeignKey` к `Hotel`, цена, вместимость, удобства (`JSONField`).
  - `Reservation` — бронирование с `ForeignKey` к `User` и `RoomType`, статусы (pending/confirmed/checked_in/checked_out/cancelled).
  - `Review` — отзыв с рейтингом 1–10, период проживания, связь с `Reservation` (опционально).
  - Методы: `Reservation.total_price` (расчёт стоимости), `Reservation.get_recent_guests()` (класс-метод для фильтрации).
- **Представления** (`hotels/views.py`):
  - Функциональные views с декораторами `@login_required` для защищённых страниц.
  - Использование `get_object_or_404()` для безопасного получения объектов.
  - Работа с формами Django (`UserRegistrationForm`, `ReservationForm`, `ReviewForm`).
  - Валидация дат бронирования (выезд > заезда, даты не в прошлом).
  - Ограничения: редактирование/удаление только для статуса `pending`.
- **Формы** (`hotels/forms.py`):
  - Кастомная форма регистрации с полями: username, email, first_name, last_name, password.
  - Форма бронирования с валидацией дат через `clean()`.
  - Форма отзыва с валидацией рейтинга (1–10) и периода проживания.
- **Шаблоны** (`templates/hotels/`):
  - Наследование от `base.html` с Bootstrap 5.
  - Использование Django template tags: `{% for %}`, `{% if %}`, `{% url %}`.
  - Отображение среднего рейтинга через `Avg('reviews__rating')` в views.
- **Docker Compose**:
  - Сервисы: `db` (PostgreSQL 15), `web` (Django приложение).
  - `entrypoint.sh` выполняет миграции, создаёт суперпользователя, загружает тестовые данные через `load_sample_data`.
  - Healthcheck для БД, volumes для персистентности данных.

### 3) Что показать (2–3 мин)
- `docker-compose up --build` (можно сказать, что уже поднято).
- В браузере:
  - `/` список отелей
  - открыть отель → открыть комнату
  - залогиниться/зарегистрироваться
  - создать бронирование → посмотреть “мои бронирования”
  - добавить отзыв
  - `/admin/` показать админку (если нужно — логин `admin` / `admin123` из README)

### 4) На что в коде обратить внимание (30–60 сек)
- **Модели** (`hotels/models.py`):
  - Связи: `RoomType.hotel = ForeignKey(Hotel)`, `Reservation.room_type = ForeignKey(RoomType)`, `Reservation.user = ForeignKey(User)`.
  - `related_name` для обратных связей: `hotel.room_types.all()`, `room_type.reservations.all()`.
  - `JSONField` для удобств в `RoomType` (список строк).
  - Метод `total_price` как `@property` для расчёта стоимости.
- **Views** (`hotels/views.py`):
  - `select_related()` и `annotate()` для оптимизации запросов (избежание N+1).
  - Использование `messages.success()`/`messages.error()` для уведомлений пользователя.
  - Проверка прав: `if reservation.status != 'pending':` перед редактированием/удалением.
- **Формы** (`hotels/forms.py`):
  - Переопределение `clean()` для кастомной валидации (даты, пересечения бронирований).
  - Использование `ModelForm` для автоматической генерации полей из модели.
- **Docker**:
  - `entrypoint.sh` — скрипт инициализации: миграции (`python manage.py migrate`), создание суперпользователя, загрузка данных.
  - `depends_on` с `condition: service_healthy` для ожидания готовности БД.

---

## Lab 3 — Django REST API для гостиницы (+ документация API)

### 1) Коротко про цель (20–30 сек)
- Цель: реализовать **REST API** на Django REST Framework: CRUD + кастомные эндпоинты + токенная авторизация.
- Дополнительно: документация через **Swagger/ReDoc**.

### 2) Что сделал (1–1.5 мин)
- **Модели** (`api/models.py`):
  - `Room` — номер с типами (single/double/triple), этаж, телефон, цена за сутки.
    - Метод `is_available(check_in_date, check_out_date)` проверяет пересечения с активными гостями через `Q` объекты.
  - `Guest` — клиент с паспортом (unique), ФИО, город, даты заселения/выселения, связь с `Room` (`PROTECT`).
    - Валидация в `clean()`: `check_out_date >= check_in_date`.
    - `@property is_current` для проверки текущего проживания.
  - `Staff` — сотрудник с ФИО, статус `is_active`, даты найма/увольнения.
  - `CleaningSchedule` — расписание с `unique_together = ['staff', 'floor', 'weekday']`.
- **ViewSets** (`api/views.py`):
  - `RoomViewSet`, `GuestViewSet`, `StaffViewSet`, `CleaningScheduleViewSet` — наследуются от `ModelViewSet`.
  - Кастомные `@action` методы:
    - `RoomViewSet.available()` — список свободных номеров через `is_available()`.
    - `RoomViewSet.guests_history()` — история гостей в номере за период (query params `start_date`, `end_date`).
    - `GuestViewSet.current()` — текущие гости через фильтрацию по датам.
    - `GuestViewSet.from_city()` — фильтр по городу.
    - `GuestViewSet.cleaning_staff()` — кто убирал номер клиента в указанный день недели.
    - `GuestViewSet.concurrent_guests()` — гости, проживавшие одновременно (пересечение периодов).
    - `GuestViewSet.check_in()` / `check_out()` — действия для заселения/выселения.
    - `StaffViewSet.hire()` / `fire()` — приём/увольнение сотрудников.
    - `ReportViewSet.quarterly()` — отчёт за квартал с расчётом дохода по каждому номеру.
- **Сериализаторы** (`api/serializers.py`):
  - `ModelSerializer` для базового CRUD.
  - Кастомные сериализаторы: `GuestCreateSerializer`, `GuestCheckOutSerializer`, `StaffCreateSerializer`, `StaffFireSerializer`.
  - Валидация в `validate()`: проверка пересечений бронирований, доступности номеров.
  - `SerializerMethodField` для вычисляемых полей (`is_available`, `full_name`, `current_guests_count`).
- **Роутинг** (`api/urls.py`):
  - `DefaultRouter` автоматически создаёт эндпоинты: `/api/rooms/`, `/api/rooms/{id}/`, `/api/rooms/available/` и т.д.
- **Аутентификация** (`hotel_system/urls.py`):
  - Djoser: `/api/auth/users/` (регистрация), `/api/auth/token/login/` (получение токена).
  - `TokenAuthentication` в `settings.py`, `IsAuthenticated`/`IsAuthenticatedOrReadOnly` в permissions.
- **Документация**:
  - `drf-yasg` для Swagger UI (`/swagger/`) и ReDoc (`/redoc/`).
  - Автоматическая генерация схемы из ViewSets и сериализаторов.
- **Тестовые данные**:
  - Management-команда `load_hotel_data` создаёт номера, сотрудников, расписание, гостей.

### 3) Что показать (2–3 мин)
- Поднять проект:
  - `docker-compose up --build`
- В браузере:
  - Swagger: `/swagger/` (показать эндпоинты, попробовать `GET /api/rooms/`)
  - ReDoc: `/redoc/` (показать документацию)
  - Admin: `/admin/` (при необходимости)
- Примеры “фишек”:
  - `GET /api/rooms/available/` (свободные номера)
  - `GET /api/guests/current/` (текущие гости)
  - `GET /api/reports/quarterly/?year=YYYY&quarter=1..4` (отчёт)

### 4) На что в коде обратить внимание (30–60 сек)
- **Модели** (`api/models.py`):
  - Метод `Room.is_available()` использует сложные `Q` объекты для проверки пересечений периодов:
    ```python
    active_guests = self.guests.filter(
        check_in_date__lte=check_in_date
    ).filter(
        Q(check_out_date__isnull=True) | Q(check_out_date__gte=check_in_date)
    )
    ```
  - `unique_together` в `CleaningSchedule` предотвращает дублирование расписания.
- **ViewSets** (`api/views.py`):
  - `@action(detail=False)` для действий на коллекции (например, `/api/rooms/available/`).
  - `@action(detail=True)` для действий на объекте (например, `/api/rooms/{id}/guests_history/`).
  - Использование `query_params` для фильтрации: `request.query_params.get('start_date')`.
  - В `ReportViewSet.quarterly()` — расчёт дохода с учётом пересечений периодов и границ квартала.
- **Сериализаторы** (`api/serializers.py`):
  - `get_serializer_class()` в ViewSet для выбора сериализатора в зависимости от действия.
  - Валидация в `validate()` проверяет доступность номера перед созданием/обновлением гостя.
  - `SerializerMethodField` для вычисляемых полей без сохранения в БД.
- **Роутинг**:
  - `DefaultRouter` автоматически создаёт стандартные эндпоинты (list, create, retrieve, update, destroy).
  - Кастомные `@action` добавляют дополнительные маршруты.
- **Аутентификация**:
  - Токены генерируются через Djoser, передаются в заголовке `Authorization: Token <token>`.
  - `permission_classes` на уровне ViewSet контролируют доступ (чтение для всех, запись только для авторизованных).

---

## Мини‑шпаргалка: что открыть перед защитой
- **Lab 1**: 2–3 терминала + 2 вкладки браузера (`8080`, `8000`).
- **Lab 2**: браузер с `/`, `/login/`, `/reservations/`, `/admin/`.
- **Lab 3**: браузер с `/swagger/`, `/redoc/`, `/api/...` (и при необходимости `/admin/`).

