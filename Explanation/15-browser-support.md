# Слайд 15: Поддержка в браузерах

## Описание слайда

Актуальная информация о поддержке `using` в современных браузерах.

## Таблица поддержки (апрель 2024)

| Браузер | Версия | Статус |
|---------|--------|--------|
| Chrome | 122+ | ✅ Поддержка |
| Firefox | 125+ | ✅ Поддержка |
| Safari | 17.4+ | ✅ Поддержка |
| Edge | 122+ | ✅ Поддержка |

## Детали по браузерам

### Chrome 122+
Выпущен в феврале 2024. Первая версия Chrome с полной поддержкой Explicit Resource Management.

### Firefox 125+
Выпущен в апреле 2024. Поддержка включена по умолчанию.

### Safari 17.4+
Выпущен в марте 2024. Часть обновлений WebKit.

### Edge 122+
Следует за Chromium, поэтому поддержка совпадает с Chrome.

## Проверка поддержки

### Программная проверка
```javascript
if ('using' in (function(){}.constructor.prototype)) {
    console.log('using поддерживается');
}
```

Или проверка символа:

```javascript
if (typeof Symbol.dispose !== 'undefined') {
    console.log('Symbol.dispose поддерживается');
}
```

## Can I Use

Актуальную информацию можно найти на сайте:
https://caniuse.com/?search=using

## Что делать, если поддержки нет

### 1. Транспиляция
Используйте TypeScript или Babel для транспиляции в try-finally.

### 2. Полифилл
Добавьте полифилл для Symbol.dispose:

```javascript
if (typeof Symbol.dispose === 'undefined') {
    Symbol.dispose = Symbol('dispose');
}
```

### 3. Условное использование
```javascript
if (isSupported()) {
    using r = getResource();
} else {
    try {
        const r = getResource();
        // ...
    } finally {
        r.dispose();
    }
}
```

## Динамика adoption

Учитывая быстрое обновление браузеров (автообновление), через 6-12 месяцев поддержка будет практически повсеместной.

## Ключевой вывод

Все современные браузеры уже поддерживают `using`. Для старых браузеров используйте транспиляцию.
