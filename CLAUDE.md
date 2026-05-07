# CLAUDE.md — mini-app Event PWA

## Важно для работы

- **Git-корень — папка `app/`**, не корень проекта. Все `git` команды выполнять из `app/`.
- **GitHub Pages:** `https://o6594340-sys.github.io/mini-app-program/`
- **Репозиторий:** `https://github.com/o6594340-sys/mini-app-program`
- **Пароль админки:** `forum2024`

После изменений в JS/CSS/HTML — **обязательно поднять версию кэша** в `sw.js` (`mice-vN`), иначе пользователи получают старый файл из Service Worker.

---

## Структура

```
app/
├── index.html        # Сайт участника
├── admin.html        # Панель организатора
├── sw.js             # Service Worker — кэш mice-v6
├── manifest.json
├── css/
│   ├── main.css      # Стили участника
│   └── admin.css     # Стили панели
└── js/
    ├── data.js       # Дефолтные данные (константы)
    ├── app.js        # Логика сайта участника
    ├── templates.js  # HTML-шаблоны рендеринга
    └── admin.js      # Логика панели (IIFE, возвращает Admin)
```

---

## Архитектура данных

`data.js` содержит дефолтные константы: `EVENT`, `HOTEL`, `DAYS`, `BUSINESS_SESSIONS`, `SIGHTS`, `RESTAURANTS`, `CUISINE`, `HISTORY`, `FAQ`, `NEARBY`, `CONTACTS`.

Панель сохраняет изменения в **localStorage** с префиксом `admin_`:
```
admin_event  admin_days  admin_business  admin_hotel
admin_sights admin_restaurants admin_cuisine admin_history
admin_announcement admin_typography admin_gradient
admin_card_style admin_motion admin_brand_kits
```

`app.js` при загрузке читает localStorage — если есть `admin_*`, берёт их; иначе берёт из `data.js`.

---

## Вкладки сайта участника (6 штук)

| id | Заголовок | Рендер |
|----|-----------|--------|
| `program` | 📅 Программа | `renderProgram()` |
| `hotel` | 🏨 Отель | `renderHotel()` — включает секцию NEARBY |
| `sights` | 🏛 Места | `renderSights()` |
| `cuisine` | 🥢 Кухня | `renderCuisine()` |
| `history` | 📜 История | `renderHistory()` |
| `contacts` | 📞 Контакты | `renderContacts()` |

---

## Импорт программы в админке

Раздел **Импорт программы** — текстовый парсер без внешних сервисов.

Формат:
```
День 1 | 18 ноября, понедельник
09:00 | Завтрак
11:00 | Деловая: Название сессии
13:00 | Обед
```

- Разделители `|` и `—` равнозначны
- `Деловая: ...` → автоматически в `business[]`
- Типы по ключевым словам: завтрак/обед/ужин → `meal`, трансфер → `transfer`, экскурсия → `excursion`

Функции: `parseImportText()`, `parseProgram()`, `applyAIResult()`.

---

## Service Worker

Файл `sw.js`. Кэширует только same-origin запросы (внешние URL — Unsplash и т.п. — пропускает).

При изменении любого JS/CSS/HTML файла **поднять версию**:
```js
const CACHE = 'mice-v6'; // → mice-v7 и т.д.
```

---

## PRO-оформление (admin.js)

- **Brand Kit** — сохранить/применить пресет оформления
- **Градиент** — 5 рецептов: Glow / Diagonal / Vertical / Mesh / Flat
- **Стили карточек** — Elevated / Flat / Glass / Outlined (body-классы `cs-*`)
- **Типографика** — 5 пар шрифтов, подгружаются из Google Fonts динамически
- **Анимации** — Swift / Elegant / Minimal (CSS-переменные `--motion-*`)

---

## Деплой

Папка `app/` — статика, сервер не нужен. Текущий деплой: GitHub Pages (бесплатно).

`index.html` и `admin.html` должны быть на **одном домене** — иначе localStorage не шарится.
