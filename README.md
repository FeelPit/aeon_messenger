# Aeon Messenger Backend

Backend для мессенджера Aeon - Telegram Mini App, построенный на FastAPI.

## Возможности

- 🔐 Аутентификация через Telegram Mini App
- 💬 Создание и управление чатами (приватные, групповые)
- 📨 Отправка и получение сообщений в реальном времени
- 📎 Поддержка медиа файлов (фото, видео, аудио, документы)
- 🎤 Поддержка голосовых сообщений
- ✅ Отметки о прочтении сообщений
- 🔄 Пересылка сообщений
- ✏️ Редактирование и удаление сообщений
- 👥 Управление участниками чатов
- 🌐 WebSocket для уведомлений в реальном времени
- 📱 Полная совместимость с Telegram Mini App

## Технологический стек

- **Backend**: FastAPI
- **База данных**: PostgreSQL
- **ORM**: SQLAlchemy
- **Миграции**: Alembic
- **Кэш**: Redis (опционально)
- **WebSocket**: FastAPI WebSocket
- **Аутентификация**: Telegram Mini App Data

## Установка и запуск

### 1. Клонирование репозитория

```bash
git clone <repository_url>
cd aeon_messenger
```

### 2. Установка зависимостей

```bash
pip install -r requirements.txt
```

### 3. Настройка окружения

Скопируйте `env.example` в `.env` и заполните необходимые переменные:

```bash
cp env.example .env
```

Основные переменные для настройки:

- `DATABASE_URL` - URL подключения к PostgreSQL
- `TELEGRAM_BOT_TOKEN` - токен вашего Telegram бота
- `SECRET_KEY` - секретный ключ для JWT (сгенерируйте новый!)

### 4. Настройка базы данных

```bash
# Создайте базу данных PostgreSQL
createdb aeon_messenger

# Примените миграции
alembic upgrade head
```

### 5. Запуск приложения

```bash
# Для разработки
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000

# Для продакшена
uvicorn app.main:app --host 0.0.0.0 --port 8000
```

## API Документация

После запуска приложения документация будет доступна по адресам:

- Swagger UI: `http://localhost:8000/docs`
- ReDoc: `http://localhost:8000/redoc`

## Основные эндпоинты

### Аутентификация

- `GET /api/v1/me` - Получение информации о текущем пользователе

### Чаты

- `GET /api/v1/chats/` - Получение списка чатов пользователя
- `POST /api/v1/chats/` - Создание нового чата
- `GET /api/v1/chats/{chat_id}` - Получение информации о чате
- `PUT /api/v1/chats/{chat_id}` - Обновление чата
- `DELETE /api/v1/chats/{chat_id}` - Удаление чата

### Сообщения

- `GET /api/v1/messages/chat/{chat_id}` - Получение сообщений из чата
- `POST /api/v1/messages/` - Отправка сообщения
- `PUT /api/v1/messages/{message_id}` - Редактирование сообщения
- `DELETE /api/v1/messages/{message_id}` - Удаление сообщения
- `POST /api/v1/messages/upload-media` - Загрузка медиа файла

### WebSocket

- `WS /ws` - WebSocket соединение для реального времени

## Структура проекта

```
aeon_messenger/
├── app/
│   ├── api/                 # API роуты
│   │   ├── chats.py        # Роуты для чатов
│   │   └── messages.py     # Роуты для сообщений
│   ├── auth/               # Аутентификация
│   │   ├── telegram.py     # Валидация Telegram данных
│   │   └── dependencies.py # Зависимости FastAPI
│   ├── models/             # Модели базы данных
│   │   ├── user.py         # Модель пользователя
│   │   ├── chat.py         # Модель чата
│   │   └── message.py      # Модель сообщения
│   ├── schemas/            # Pydantic схемы
│   │   ├── chat.py         # Схемы для чатов
│   │   └── message.py      # Схемы для сообщений
│   ├── websocket/          # WebSocket поддержка
│   │   ├── manager.py      # Менеджер соединений
│   │   └── router.py       # WebSocket роуты
│   ├── config.py           # Конфигурация приложения
│   ├── database.py         # Настройка базы данных
│   └── main.py             # Основное приложение
├── alembic/                # Миграции базы данных
├── requirements.txt        # Зависимости Python
├── alembic.ini            # Конфигурация Alembic
├── env.example            # Пример файла окружения
└── README.md              # Документация
```

## Интеграция с Telegram Mini App

### Аутентификация

Для аутентификации используйте заголовок `X-Telegram-Init-Data` со значением `window.Telegram.WebApp.initData`:

```javascript
fetch('/api/v1/me', {
  headers: {
    'X-Telegram-Init-Data': window.Telegram.WebApp.initData
  }
})
```
### ENV

```
# Database settings
DATABASE_URL=postgresql://user:password@localhost:5432/aeon_messenger

# Telegram settings
TELEGRAM_BOT_TOKEN=your_telegram_bot_token
TELEGRAM_WEBHOOK_SECRET=your_webhook_secret

# JWT settings
SECRET_KEY=your-super-secret-key-change-in-production
ALGORITHM=HS256
ACCESS_TOKEN_EXPIRE_MINUTES=30

# Redis settings
REDIS_URL=redis://localhost:6379

# File upload settings
UPLOAD_DIR=uploads
MAX_FILE_SIZE=52428800

# CORS settings
CORS_ORIGINS=["*"]

# App settings
APP_NAME=Aeon Messenger
DEBUG=true 
```
### WebSocket подключение

```javascript
const ws = new WebSocket(`ws://localhost:8000/ws?init_data=${window.Telegram.WebApp.initData}`);

ws.onmessage = function(event) {
  const data = JSON.parse(event.data);
  // Обработка уведомлений
};
```

## Развертывание

### Docker (рекомендуется)

```bash
# Создайте Dockerfile
# Настройте docker-compose.yml с PostgreSQL и Redis
docker-compose up -d
```

### Heroku

```bash
# Добавьте PostgreSQL addon
heroku addons:create heroku-postgresql:hobby-dev

# Настройте переменные окружения
heroku config:set TELEGRAM_BOT_TOKEN=your_token
heroku config:set SECRET_KEY=your_secret_key

# Деплой
git push heroku main
```

## Безопасность

- Все данные от Telegram проверяются криптографической подписью
- Используется HTTPS в продакшене
- Секретные ключи хранятся в переменных окружения
- Валидация всех входящих данных через Pydantic

## Лицензия

MIT License

## Поддержка

Если у вас возникли вопросы или проблемы, создайте issue в репозитории проекта. 
