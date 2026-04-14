# Слайд 18: DisposableStack

## Описание слайда

Утилита для группового управления множественными ресурсами.

## Что такое DisposableStack

`DisposableStack` — это вспомогательный класс, который позволяет управлять несколькими ресурсами как одним целым.

## Пример использования

```javascript
using stack = new DisposableStack();
const r1 = stack.use(getResource1());
const r2 = stack.use(getResource2());
const r3 = stack.use(getResource3());

// Все ресурсы освобождаются одной командой
stack.dispose(); // или автоматически при выходе из scope
```

## Как это работает

### Метод use
```javascript
const resource = stack.use(getResource());
```

Метод `use` регистрирует ресурс в стеке и возвращает его. При освобождении стека все зарегистрированные ресурсы будут освобождены в обратном порядке (LIFO).

### Автоматическое освобождение
```javascript
{
    using stack = new DisposableStack();
    const r1 = stack.use(getResource1());
    const r2 = stack.use(getResource2());
} // Все ресурсы освобождаются автоматически
```

`DisposableStack` сам реализует `Symbol.dispose`, поэтому может использоваться с `using`.

## AsyncDisposableStack

Асинхронная версия для работы с асинхронными ресурсами:

```javascript
using asyncStack = new AsyncDisposableStack();
await asyncStack.use(getAsyncResource());
await asyncStack.use(getAnotherAsyncResource());
```

## Преимущества

### 1. Группировка ресурсов
Несколько ресурсов управляются как один.

### 2. Порядок освобождения
Ресурсы освобождаются в обратном порядке регистрации (LIFO), что важно для зависимых ресурсов.

### 3. Атомарность
Либо все ресурсы освобождаются, либо обрабатываются ошибки.

### 4. Меньше boilerplate
Не нужно писать много `using` для каждого ресурса.

## Пример: сложная инициализация

```javascript
function initializeApp() {
    using stack = new DisposableStack();
    
    const db = stack.use(connectDatabase());
    const cache = stack.use(createCache());
    const logger = stack.use(createLogger());
    const server = stack.use(createServer());
    
    // Если что-то пойдёт не так, все ресурсы будут освобождены
    
    return {
        db,
        cache,
        logger,
        server,
        [Symbol.dispose]: stack[Symbol.dispose].bind(stack)
    };
}
```

## Методы DisposableStack

| Метод | Описание |
|-------|----------|
| `use(resource)` | Регистрирует ресурс и возвращает его |
| `adopt(value, onDispose)` | Регистрирует с кастомной функцией очистки |
| `defer(onDispose)` | Регистрирует функцию очистки без значения |
| `move()` | Перемещает ресурсы в новый стек |
| `dispose()` | Освобождает все ресурсы |

## Пример с adopt

```javascript
using stack = new DisposableStack();
const handle = stack.adopt(
    openHandle(),
    h => h.close() // кастомная функция очистки
);
```

## Ключевой вывод

`DisposableStack` — мощная утилита для управления множественными ресурсами с гарантией их освобождения в правильном порядке.
