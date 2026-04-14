# Слайд 14: Поддержка в TypeScript

## Описание слайда

Детали поддержки ключевого слова `using` в TypeScript.

## Таблица поддержки

| Версия | Возможности |
|--------|-------------|
| 5.2+ | Полная поддержка `using` |
| target: ESNext | Нативная генерация |
| target: ES5/ES6 | Транспиляция в try-finally |

## TypeScript 5.2+

### Дата выхода
TypeScript 5.2 был выпущен в августе 2023 года и стал первой версией с поддержкой Explicit Resource Management.

### Поддерживаемые возможности
- Ключевое слово `using`
- Ключевое слово `await using`
- Интерфейсы `Disposable` и `AsyncDisposable`
- Автоматическая проверка типов

## Пример с типизацией

```typescript
interface Disposable {
    [Symbol.dispose](): void;
}

class Resource implements Disposable {
    [Symbol.dispose](): void {
        console.log('disposed');
    }
}

function useResource(r: Resource) {
    using resource = r; // Тип проверяется
}
```

## Настройка tsconfig.json

### Для нативной генерации
```json
{
    "compilerOptions": {
        "target": "ES2022",
        "lib": ["ESNext"]
    }
}
```

При этом генерируется нативный код с `using`.

### Для транспиляции
```json
{
    "compilerOptions": {
        "target": "ES5",
        "lib": ["ESNext"]
    }
}
```

Код будет транспилирован в try-finally.

## Встроенные типы

TypeScript предоставляет встроенные типы:

```typescript
interface Disposable {
    [Symbol.dispose](): void;
}

interface AsyncDisposable {
    [Symbol.asyncDispose](): Promise<void>;
}

interface SuppressedError extends Error {
    error: Error;
    suppressed: Error;
}
```

## Проверка типов

TypeScript проверяет, что объект, используемый с `using`, реализует нужный интерфейс:

```typescript
class BadResource {
    // Нет Symbol.dispose
}

using r = new BadResource(); // Ошибка компиляции!
```

## Использование с generic

```typescript
function useDisposable<T extends Disposable>(resource: T): void {
    using r = resource;
    // работа с r
}
```

## Ключевой вывод

TypeScript 5.2+ обеспечивает полную поддержку `using` с проверкой типов и возможностью транспиляции в любые версии JS.
