# Слайд 10: await using

## Описание слайда

Описание асинхронной версии ключевого слова `using` для работы с ресурсами, требующими асинхронного освобождения.

## Проблема

Не все ресурсы могут быть освобождены синхронно. Например:
- Закрытие сетевого соединения
- Сброс буферов на диск
- Корректное завершение асинхронных операций

Для таких случаев используется `await using`.

## Пример кода

```javascript
class AsyncResource {
    [Symbol.asyncDispose]() {
        return this.close(); // возвращает Promise
    }
}

{
    await using resource = new AsyncResource();
    await resource.doSomething();
} // await resource[Symbol.asyncDispose]() автоматически
```

## Разбор

### Асинхронный dispose метод
```javascript
[Symbol.asyncDispose]() {
    return this.close(); // возвращает Promise
}
```

Метод `[Symbol.asyncDispose]` возвращает Promise. Это позволяет выполнять асинхронные операции при освобождении ресурса.

### Объявление с await using
```javascript
await using resource = new AsyncResource();
```

Ключевые слова `await using` вместе указывают, что:
1. Ресурс требует асинхронного освобождения
2. При выходе из блока будет автоматически вызван `await resource[Symbol.asyncDispose]()`

### Автоматическое ожидание
```javascript
} // await resource[Symbol.asyncDispose]() автоматически
```

При выходе из блока runtime автоматически выполнит `await` для метода `[Symbol.asyncDispose]`.

## Разница между using и await using

| Аспект | using | await using |
|--------|-------|-------------|
| Метод | Symbol.dispose | Symbol.asyncDispose |
| Возврат | void | Promise |
| Контекст | Любой | async функция/модуль |
| Освобождение | Синхронное | Асинхронное |

## Реальный пример

```javascript
class DatabaseConnection {
    async [Symbol.asyncDispose]() {
        await this.endPool();
        await this.flushLogs();
    }
}

async function main() {
    await using db = new DatabaseConnection();
    await db.query('SELECT * FROM users');
} // await db[Symbol.asyncDispose]() вызывается автоматически
```

## Важное замечание

`await using` может использоваться только:
- В async-функциях
- В async-модулях
- На верхнем уровне модуля (top-level await)

## Обработка ошибок

Если освобождение ресурса завершается с ошибкой, она выбрасывается после основной ошибки (если она была):

```javascript
{
    await using resource = new AsyncResource();
    throw new Error('Main error');
} // Если asyncDispose тоже выбросит ошибку, обе ошибки будут доступны
```

## Ключевой вывод

`await using` — это асинхронная версия `using` для ресурсов, освобождение которых требует асинхронных операций.
