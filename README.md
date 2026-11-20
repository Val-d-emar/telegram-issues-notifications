# 📌 Telegram Issues Notifications

[🇷🇺 Русский](#русский) | [🇬🇧 English](#english)

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

<a name="english"></a>

## 🇬🇧 English

A simple, fast, and pure-Bash GitHub Action to send Telegram notifications for GitHub Issues events. **Zero dependencies, no Node.js required!**

### ✨ Features

- **Zero dependencies:** Runs instantly using pure Bash and `curl`.
- **Smart Mentioning:** Maps GitHub usernames to Telegram usernames.
- **Bot Ignoring:** Automatically ignores actions triggered by bots (e.g., `dependabot`).
- **Thread Support:** Can send messages to specific topics in Telegram Supergroups.
- **i18n:** Supports English and Russian notification languages.
- **Safe:** Escapes HTML characters (`&`, `<`, `>`) and uses URL-encoding so special symbols or quotes in issue titles and labels won't break message delivery.

### 🚀 Supported Events

- `opened` (New issue created)
- `reopened` (Issue reopened)
- `closed` (Distinguishes between _Completed_ and _Not Planned_)
- `assigned` (Assignee set or changed)

### 🛠 Usage

Create a workflow file in your repository, e.g., `.github/workflows/issues-tg-notify.yml`:

```yaml
name: Issues Notifier

on:
  issues:
    types: [opened, closed, reopened, assigned]

permissions:
  contents: read

# Prevents race conditions and spam for the same issue action
concurrency:
  group: issues-notify-${{ github.event.issue.number }}-${{ github.event.action }}-${{ github.event.assignee.login || github.run_id }}
  cancel-in-progress: false

jobs:
  notify:
    runs-on: ubuntu-latest
    steps:
      - name: Send Notification
        uses: Val-d-emar/telegram-issues-notifications@v1
        with:
          bot_token: ${{ secrets.TELEGRAM_BOT_TOKEN }}
          chat_id: ${{ secrets.TELEGRAM_CHAT_ID }}
          # message_thread_id: "12345" # Optional: for topic groups
          user_map: ${{ secrets.TELEGRAM_USER_MAP }}
          ignore_bots: "true"
          language: "en"
```

### ⚙️ Inputs

| Input               | Required | Default | Description                                        |
| ------------------- | -------- | ------- | -------------------------------------------------- |
| `bot_token`         | **Yes**  |         | Your Telegram Bot Token.                           |
| `chat_id`           | **Yes**  |         | Target chat ID (Group, Channel, or User).          |
| `message_thread_id` | No       |         | Target Thread ID (if your group uses Topics).      |
| `user_map`          | No       |         | Mapping of GitHub usernames to Telegram usernames. |
| `ignore_bots`       | No       | `true`  | Ignore actions triggered by `[bot]` accounts.      |
| `language`          | No       | `ru`    | Notification language (`en` or `ru`).              |

### 👥 User Mapping (`user_map`)

To ping correct Telegram users, create a GitHub Secret (e.g., `TELEGRAM_USER_MAP`) with the following format:

```text
github_username1=telegram_username1
github_username2=@telegram_username2
```

_Note: The `@` symbol for Telegram usernames is optional, the action will add it automatically if missing._

### License

This project is licensed under the [MIT License](LICENSE).

---

<a name="русский"></a>

## 🇷🇺 Русский

Простой и быстрый GitHub Action на чистом Bash для отправки уведомлений о событиях в GitHub Issues прямо в Telegram. **Никаких зависимостей и сборок на Node.js!**

### ✨ Особенности

- **Zero dependencies:** Работает мгновенно, использует только Bash и `curl`.
- **Умные упоминания:** Связывает никнеймы GitHub с аккаунтами в Telegram.
- **Игнорирование ботов:** Не спамит в чат от лица `dependabot` или других ботов.
- **Поддержка топиков (Threads):** Умеет отправлять сообщения в конкретные ветки супергрупп.
- **i18n:** Поддерживает русский и английский языки.
- **Безопасность:** Экранирует HTML-символы (`&`, `<`, `>`) и использует URL-encoding, поэтому спецсимволы, кавычки и переносы строк в названии задачи или метках не сломают отправку.

### 🚀 Поддерживаемые события

- `opened` (Создана новая задача)
- `reopened` (Задача переоткрыта)
- `closed` (Отличает обычное закрытие от _Закрыта как неактуальная / Not Planned_)
- `assigned` (Назначен исполнитель задачи)

### 🛠 Использование

Создайте файл в вашем репозитории, например `.github/workflows/issues-tg-notify.yml`:

```yaml
name: Issues Notifier

on:
  issues:
    types: [opened, closed, reopened, assigned]

permissions:
  contents: read

# Защита от спама и "гонок" при дублирующихся событиях
concurrency:
  group: issues-notify-${{ github.event.issue.number }}-${{ github.event.action }}-${{ github.event.assignee.login || github.run_id }}
  cancel-in-progress: false

jobs:
  notify:
    runs-on: ubuntu-latest
    steps:
      - name: Send Notification
        uses: Val-d-emar/telegram-issues-notifications@v1
        with:
          bot_token: ${{ secrets.TELEGRAM_BOT_TOKEN }}
          chat_id: ${{ secrets.TELEGRAM_CHAT_ID }}
          # message_thread_id: "12345" # Опционально: ID топика в форумах
          user_map: ${{ secrets.TELEGRAM_USER_MAP }}
          ignore_bots: "true"
          language: "ru"
```

### ⚙️ Параметры (Inputs)

| Параметр            | Обязательный | По умолчанию | Описание                                               |
| ------------------- | ------------ | ------------ | ------------------------------------------------------ |
| `bot_token`         | **Да**       |              | Токен вашего Telegram бота.                            |
| `chat_id`           | **Да**       |              | ID чата (группа, канал или ЛС).                        |
| `message_thread_id` | Нет          |              | ID топика (ветки), если группа в режиме форума.        |
| `user_map`          | Нет          |              | Маппинг никнеймов GitHub -> Telegram.                  |
| `ignore_bots`       | Нет          | `true`       | Не отправлять уведомления, если действие совершил бот. |
| `language`          | Нет          | `ru`         | Язык уведомлений (`en` или `ru`).                      |

### 👥 Маппинг пользователей (`user_map`)

Чтобы бот тегал нужных людей в Telegram, создайте секрет в GitHub (например, `TELEGRAM_USER_MAP`) в следующем формате:

```text
github_username1=telegram_username1
github_username2=@telegram_username2
```

_Примечание: Символ `@` перед ником в Telegram писать не обязательно, скрипт добавит его автоматически._

### Лицензия

Этот проект распространяется под лицензией [MIT License](LICENSE).
