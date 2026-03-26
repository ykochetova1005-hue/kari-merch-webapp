# Чат-бот мерчендайзинга KARI

Telegram-бот + Web App для навигации по документам мерчендайзинга kari.

---

## Архитектура

```
Пользователь → Telegram (@Merch_kariBot) → Web App (GitHub Pages) → Облако kari (Nextcloud)
                                          ↑
                                    Админ-панель (/admin)
                                          ↓
                                    Новости + Рассылка
```

**Бот** — Python-процесс, работает на ПК, принимает команды, управляет новостями и рассылкой.
**Web App** — HTML-страница на GitHub Pages, отображает меню с документами.
**Документы** — хранятся в облаке `cloud.kari.com` (Nextcloud).

---

## Структура файлов

```
telegram_bot/
├── bot.py                — основной код бота
├── config.json           — токены, ID админов, ссылки
├── webapp.html           — Web App (меню документов)
├── manifest.json         — PWA манифест (установка как приложение)
├── requirements.txt      — зависимости Python
├── users.db              — SQLite база пользователей (создаётся автоматически)
├── start_bot.bat         — запуск с автоперезапуском при сбоях
├── start_bot_silent.vbs  — тихий запуск (без окна cmd)
└── README.md             — этот файл
```

---

## Настройки (config.json)

| Параметр | Описание |
|----------|----------|
| `bot_token` | Токен Telegram-бота (получен через @BotFather) |
| `admin_ids` | Массив Telegram ID администраторов |
| `webapp_url` | URL Web App на GitHub Pages |
| `github_token` | GitHub Personal Access Token (для обновления новостей) |
| `github_repo` | Репозиторий Web App на GitHub |

---

## GitHub-репозитории

| Репозиторий | Назначение | Видимость |
|-------------|-----------|-----------|
| `ykochetova1005-hue/kari-merch-webapp` | Web App (меню) + новости | Публичный (GitHub Pages) |
| `ykochetova1005-hue/kari-merch-bot` | Бэкап кода бота | Приватный |

---

## Функционал

### Для пользователей
- `/start` — приветствие + кнопка «Открыть меню»
- Web App — навигация по разделам документов
- Поиск по всем документам
- Уведомления о новостях в чат

### Для администратора
- `/admin` — открывает админ-панель (видна только админам)
  - **Добавить новость** — текст сохраняется на главной Web App + рассылается всем
  - **Редактировать новость** — изменить текст существующей новости
  - **Удалить новость** — удалить новость с главной
  - **Статистика** — количество зарегистрированных пользователей
- `/cancel` — отмена текущей операции

---

## Разделы меню Web App

| Раздел | Тип | Подразделы |
|--------|-----|-----------|
| Бюллетень | Прямая ссылка | — |
| Витрины | Подменю | Сезонные инструкции, Регламент |
| Акции | Прямая ссылка | — |
| POSM | Прямая ссылка | — |
| Регламенты | Подменю | Обувь, Аксессуары, Kari Home, Kari Cosmetics, Kari Sport, Одежда, Флеш-коллекции, Фотоотчет |
| Планограммы | Подменю | Касса, G13, Miniso, АСС стойки, АСС стеллажи островные, АСС стеллажи пристенные |

Ссылки ведут на папки в `cloud.kari.com` (Nextcloud).

---

## Развёртывание на новом ПК

### Требования
- Python 3.10+
- Доступ в интернет
- Telegram-аккаунт

### Шаги

1. **Установить Python:** https://www.python.org/downloads/
   - При установке отметить «Add Python to PATH»

2. **Скопировать папку** `telegram_bot/` на новый ПК

3. **Установить зависимости:**
   ```
   cd telegram_bot
   pip install -r requirements.txt
   ```

4. **Проверить config.json:**
   - `bot_token` — токен бота (менять только если создаётся новый бот)
   - `admin_ids` — добавить Telegram ID нового админа
   - `github_token` — может потребоваться новый токен (срок действия)

5. **Запустить бота:**
   ```
   python bot.py
   ```

6. **Настроить автозапуск:**
   - Скопировать `start_bot_silent.vbs` в папку автозагрузки:
   - `Win+R` → `shell:startup` → вставить файл

### Как узнать Telegram ID нового админа
Отправьте боту `/start`, затем посмотрите в `users.db` (через DB Browser for SQLite или команду):
```
sqlite3 users.db "SELECT * FROM users;"
```

---

## Обновление Web App (меню)

Web App — это файл `webapp.html`. Для обновления:

1. Отредактировать `webapp.html` (добавить/изменить разделы, ссылки)
2. Загрузить на GitHub:
   - Через веб-интерфейс: https://github.com/ykochetova1005-hue/kari-merch-webapp
   - Файл → `index.html` → Edit → Commit
3. GitHub Pages обновится через 2-5 минут

### Добавление нового раздела в Web App
В `webapp.html` найти `<div id="menu-grid">` и добавить кнопку по аналогии с существующими.

### Добавление нового подраздела
Найти соответствующий `<div id="sub-...">` и добавить `<a class="sub-item">` по аналогии.

---

## Обновление новостей

Новости хранятся в `news.json` на GitHub (репозиторий `kari-merch-webapp`).
Формат:
```json
[
  {"date": "26.03", "text": "Текст новости"},
  {"date": "25.03", "text": "Текст предыдущей новости"}
]
```
Максимум 5 новостей. Старые удаляются автоматически.

Новости добавляются/редактируются/удаляются через `/admin` в боте.

---

## PWA (установка как приложение)

Пользователи могут установить Web App как приложение на телефон:
1. Открыть `https://ykochetova1005-hue.github.io/kari-merch-webapp/` в браузере
2. «Поделиться» → «Добавить на главный экран» (iOS) или меню → «Установить» (Android)
3. Иконка — Карик с туфелькой

---

## Безопасность

- **bot_token** — не публиковать, даёт полный доступ к боту
- **github_token** — не публиковать, даёт доступ к репозиториям
- Репозиторий `kari-merch-bot` — приватный (содержит токены)
- При компрометации токенов:
  - bot_token: @BotFather → /revoke
  - github_token: GitHub → Settings → Developer settings → Personal access tokens → Delete

---

## Контакты

- Бот в Telegram: https://t.me/Merch_kariBot
- Web App: https://ykochetova1005-hue.github.io/kari-merch-webapp/
- GitHub (webapp): https://github.com/ykochetova1005-hue/kari-merch-webapp
- GitHub (bot): https://github.com/ykochetova1005-hue/kari-merch-bot
- Облако документов: https://cloud.kari.com/index.php/apps/files/files/95706?dir=/MERCHANDISING
