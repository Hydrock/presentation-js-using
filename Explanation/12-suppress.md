# Слайд 12: Suppress - игнорирование null/undefined

## Описание слайда

Обработка случаев, когда ресурс может быть null или undefined.

## Проблема

Если переменная, объявленная с `using`, равна null или undefined, возникнет ошибка:

```javascript
function readFile(path) {
    using file = fs.openSync(path); // может быть null
    
    // Ошибка, если file === null!
}
```

При попытке вызвать `[Symbol.dispose]()` на null/undefined будет выброшено исключение.

## Решение 1: Null-объект паттерн

```javascript
using file = fs.openSync(path) ?? { [Symbol.dispose]() {} };
```

Если `fs.openSync` возвращает null, используется объект с пустым методом dispose. Это паттерн "Null Object" — создание объекта-заглушки, который ничего не делает.

## Решение 2: Условное использование

```javascript
const file = fs.openSync(path);
if (file) {
    using f = file;
    // работа с файлом
}
```

Проверка перед использованием, но это добавляет вложенность.

## Решение 3: SuppressInputStream (экспериментально)

В некоторых реализациях доступны специальные утилиты для подавления ошибок:

```javascript
using file = new SuppressInputStream(fs.openSync(path));
```

Это обёртка, которая игнорирует null и ошибки при dispose.

## Рекомендации

### Проверка существования
Всегда проверяйте, может ли функция вернуть null/undefined:

```javascript
function safeOpen(path) {
    const file = fs.openSync(path);
    if (!file) {
        return { [Symbol.dispose]() {} }; // Null object
    }
    return file;
}

using file = safeOpen('data.txt');
```

### Явная обработка
Для критически важного кода:

```javascript
const maybeFile = fs.openSync(path);
using file = maybeFile ?? throw new Error('File not found');
```

## Best Practice

Используйте null-объект паттерн для необязательных ресурсов:

```javascript
const nullDisposable = { [Symbol.dispose]() {} };

function getResource() {
    if (condition) {
        return realResource;
    }
    return nullDisposable;
}

using r = getResource(); // Всегда безопасно
```

## Ключевой вывод

При работе с `using` важно учитывать возможность null/undefined значений и использовать null-object паттерн для безопасной обработки.
