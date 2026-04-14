# Слайд 11: Протокол Symbol.dispose

## Описание слайда

Технические детали протокола dispose: какие символы используются и как они работают.

## Таблица символов

| Symbol | Тип | Описание |
|--------|-----|----------|
| `Symbol.dispose` | Синхронный | Метод для `using` |
| `Symbol.asyncDispose` | Асинхронный | Метод для `await using` |

## Symbol.dispose

### Определение
`Symbol.dispose` — это встроенный символ (well-known symbol) в JavaScript, используемый для определения метода синхронного освобождения ресурса.

### Использование
```javascript
const resource = {
    [Symbol.dispose]() {
        console.log('Cleanup!');
    }
};

using r = resource; // вызывает r[Symbol.dispose]()
```

### Когда вызывается
Метод вызывается автоматически при выходе из области видимости, где был объявлен ресурс с `using`.

## Symbol.asyncDispose

### Определение
`Symbol.asyncDispose` — символ для асинхронного освобождения ресурсов.

### Использование
```javascript
const asyncResource = {
    async [Symbol.asyncDispose]() {
        await this.close();
    }
};

await using r = asyncResource; // вызывает await r[Symbol.asyncDispose]()
```

### Когда вызывается
Метод вызывается при выходе из области видимости с автоматическим await.

## Реализация в классах

### Полный пример с обоими методами
```javascript
class SmartResource {
    [Symbol.dispose]() {
        this.syncClose();
    }
    
    async [Symbol.asyncDispose]() {
        await this.asyncClose();
    }
}

// Синхронное использование
{
    using r = new SmartResource();
    // r[Symbol.dispose]() будет вызван
}

// Асинхронное использование
async function example() {
    await using r = new SmartResource();
    // await r[Symbol.asyncDispose]() будет вызван
}
```

## Интерфейсы TypeScript

```typescript
interface Disposable {
    [Symbol.dispose](): void;
}

interface AsyncDisposable {
    [Symbol.asyncDispose](): Promise<void>;
}
```

## Взаимодействие с использованием

### Что происходит "под капотом"

```javascript
// Исходный код
{
    using r = getResource();
    use(r);
}

// Примерно транслируется в
{
    const r = getResource();
    try {
        use(r);
    } finally {
        r[Symbol.dispose]();
    }
}
```

## Проверка поддержки

```javascript
function isDisposable(obj) {
    return Symbol.dispose in obj;
}

function isAsyncDisposable(obj) {
    return Symbol.asyncDispose in obj;
}
```

## Встроенные объекты

Следующие встроенные объекты поддерживают протокол:
- `FileSystemHandle` (File System Access API)
- `ReadableStream`
- `WritableStream`

## Ключевой вывод

Протокол dispose основан на символах `Symbol.dispose` и `Symbol.asyncDispose`, которые определяют методы освобождения ресурсов.
