# Слайд 20: Лучшие практики

## Описание слайда

Рекомендации по эффективному использованию ключевого слова `using`.

## Практика 1: Используйте using для ресурсов с детерминированным освобождением

`using` предназначен для ресурсов, которые требуют явного освобождения в определённый момент:

✅ **Подходит**:
- Файловые дескрипторы
- Сетевые соединения
- Соединения с БД
- Мьютексы и блокировки
- Хэндлы операционной системы

❌ **Не подходит**:
- Обычные объекты в памяти (сборщик мусора справится)
- Простой код без внешних ресурсов

## Практика 2: Реализуйте Symbol.dispose в своих классах

Любой класс, управляющий ресурсами, должен реализовывать протокол:

```javascript
class MyResource {
    #handle;
    
    constructor() {
        this.#handle = acquireResource();
    }
    
    [Symbol.dispose]() {
        releaseResource(this.#handle);
    }
}
```

Это делает класс удобным для потребителей и следует принципу инкапсуляции.

## Практика 3: Используйте await using для асинхронных ресурсов

Для асинхронных операций освобождения:

```javascript
class AsyncResource {
    async [Symbol.asyncDispose]() {
        await this.close();
    }
}

// Использование
await using resource = new AsyncResource();
```

Не пытайтесь использовать синхронный `using` с асинхронными ресурсами.

## Практика 4: Группируйте ресурсы с DisposableStack

Для множества связанных ресурсов:

```javascript
using stack = new DisposableStack();
const db = stack.use(connectDB());
const cache = stack.use(createCache());
const queue = stack.use(createQueue());
// Все ресурсы освобождаются вместе
```

Это упрощает код и гарантирует правильный порядок освобождения.

## Практика 5: Проверяйте поддержку в целевой среде

Перед использованием:

```javascript
// Проверка в runtime
if (typeof Symbol.dispose !== 'undefined') {
    // using доступен
} else {
    // fallback или транспиляция
}
```

Или настройте транспиляцию в build-процессе.

## Дополнительные рекомендации

### Обработка null/undefined
```javascript
using file = openFile(path) ?? { [Symbol.dispose]() {} };
```

### Документирование
```javascript
/**
 * Ресурс, который должен быть освобождён после использования.
 * Используйте с ключевым словом `using`.
 */
class MyResource implements Disposable {
    // ...
}
```

### Именование
Используйте осмысленные имена переменных:
```javascript
using dbConnection = createConnection();
using fileHandle = openFile();
```

### Избегайте побочных эффектов
Не создавайте ресурсы с побочными эффектами в объявлении:
```javascript
// Плохо
using r = createResourceWithSideEffects();

// Лучше
const r = createResourceWithSideEffects();
using wrapped = wrapDisposable(r);
```

## Ключевой вывод

Следование лучшим практикам делает код с `using` надёжным, читаемым и поддерживаемым.
