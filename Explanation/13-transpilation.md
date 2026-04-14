# Слайд 13: Транспиляция в ES5/ES6

## Описание слайда

Как работает транспиляция `using` в старые версии JavaScript.

## Зачем нужна транспиляция

Не все среды поддерживают `using` нативно. Для работы в старых браузерах и Node.js код нужно транспилировать.

## Пример транспиляции

### Исходный код
```javascript
{
    using r = getResource();
    r.use();
}
```

### Результат (ES5/ES6)
```javascript
{
    var r = getResource();
    try {
        r.use();
    } finally {
        r[Symbol.dispose]();
    }
}
```

## Механизм

### 1. Объявление переменной
`using r` превращается в обычное `var r` (или `let`/`const` в зависимости от настроек).

### 2. Обёртка в try-finally
Тело блока оборачивается в try-finally для гарантии вызова dispose.

### 3. Вызов dispose в finally
В блоке finally добавляется вызов `[Symbol.dispose]()`.

## Сложные случаи

### Множественные ресурсы
```javascript
// Исходный код
{
    using r1 = getResource1();
    using r2 = getResource2();
    use(r1, r2);
}

// Транспилированный
{
    var r1 = getResource1();
    try {
        var r2 = getResource2();
        try {
            use(r1, r2);
        } finally {
            r2[Symbol.dispose]();
        }
    } finally {
        r1[Symbol.dispose]();
    }
}
```

### await using
```javascript
// Исходный код
{
    await using r = getAsyncResource();
    await r.use();
}

// Транспилированный (упрощённо)
{
    var r = getAsyncResource();
    try {
        await r.use();
    } finally {
        await r[Symbol.asyncDispose]();
    }
}
```

## Поддержка транспиляторов

### TypeScript 5.2+
Полная поддержка `using` и `await using`. Можно настроить target для генерации кода.

### Babel
Требуется плагин `@babel/plugin-proposal-explicit-resource-management`.

### SWC
Нативная поддержка в новых версиях.

## Настройка TypeScript

```json
{
    "compilerOptions": {
        "target": "ES2022",
        "lib": ["ESNext"]
    }
}
```

Для транспиляции в ES5/ES6:

```json
{
    "compilerOptions": {
        "target": "ES5",
        "lib": ["ESNext"]
    }
}
```

## Полифиллы

Для работы в средах без Symbol.dispose нужен полифилл:

```javascript
if (typeof Symbol.dispose === 'undefined') {
    Symbol.dispose = Symbol('dispose');
}
```

## Ключевой вывод

Транспиляторы преобразуют `using` в try-finally, что позволяет использовать эту фичу в любой среде.
