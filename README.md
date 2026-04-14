# Ключевое слово using в ECMAScript

Презентация о новом ключевом слове `using` в JavaScript для автоматического управления ресурсами.

## Просмотр презентации

🔗 **[Открыть презентацию](https://hydrock.github.io/presentation-js-using/)**

## Описание

Ключевое слово `using` — это нововведение ECMAScript 2024, которое привносит детерминированное управление ресурсами в JavaScript. Аналогично `using` в C# и `with` в Python, оно гарантирует автоматическое освобождение ресурсов при выходе из области видимости.

## Структура проекта

```
├── index.html              # Презентация (Reveal.js)
├── Explanation/            # Подробные описания слайдов
│   ├── README.md          # Общее описание
│   ├── 01-intro.md        # Титульный слайд
│   ├── 02-contents.md     # Содержание
│   └── ...                # Остальные слайды
├── package.json           # Зависимости проекта
└── README.md              # Этот файл
```

## Содержание презентации

1. Что такое `using`?
2. История появления
3. Аналоги в других языках
4. Зачем нужен `using`?
5. Синтаксис и примеры
6. Протокол Symbol.dispose
7. Транспиляция
8. Поддержка в браузерах и Node.js
9. Лучшие практики

## Технологии

- [Reveal.js](https://revealjs.com/) — фреймворк для презентаций
- [GitHub Pages](https://pages.github.com/) — хостинг
- [CDN](https://cdnjs.com/) — доставка контента

## Полезные ссылки

- [TC39 Proposal](https://github.com/tc39/proposal-explicit-resource-management)
- [TypeScript 5.2 Release Notes](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-5-2.html)
- [MDN Documentation](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/using)

## Запуск локально

```bash
# Установка зависимостей
npm install

# Запуск локального сервера
npx serve .
```

## Автор

Презентация создана для доклада о современных возможностях JavaScript.

---

📝 **Ключевой вывод**: `using` — это современный стандарт для управления ресурсами в JavaScript, который делает код безопаснее и чище.
