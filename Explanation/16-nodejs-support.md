# Слайд 16: Поддержка в Node.js

## Описание слайда

Информация о поддержке `using` в различных версиях Node.js.

## Таблица поддержки

| Версия | Статус |
|--------|--------|
| Node.js 20.10+ | ✅ Полная поддержка |
| Node.js 20.x (экспериментально) | ⚠️ Флаг `--harmony-explicit-resource-management` |
| Node.js 18.x | ❌ Не поддерживается |

## Детали по версиям

### Node.js 20.10+
Начиная с версии 20.10.0, `using` поддерживается нативно без дополнительных флагов. Это LTS-версия, рекомендованная для production.

### Node.js 20.x (до 20.10)
Поддержка доступна экспериментально с флагом:

```bash
node --harmony-explicit-resource-management app.js
```

### Node.js 18.x
Поддержка отсутствует. Рекомендуется обновиться до Node.js 20+.

## Использование в Node.js

### Файловая система
```javascript
const fs = require('fs/promises');

async function readData() {
    using file = await fs.open('data.txt', 'r');
    const content = await file.readFile('utf8');
    return content;
} // Файл автоматически закрывается
```

### Streams
```javascript
const { Readable } = require('stream');

async function processStream() {
    using stream = new Readable({...});
    // работа со стримом
} // stream.destroy() вызывается автоматически
```

## Встроенные API

Node.js постепенно добавляет поддержку Symbol.dispose к своим API:

- `fs.FileHandle` — дескриптор файла
- `stream.Readable` и `stream.Writable`
- `http.ClientRequest`

## Проверка версии

```javascript
const version = process.versions.node;
const [major, minor] = version.split('.').map(Number);

if (major > 20 || (major === 20 && minor >= 10)) {
    console.log('using поддерживается');
}
```

## N-API и native addons

Нативные модули могут реализовывать поддержку `using` через N-API, добавляя метод `[Symbol.dispose]` к экспортируемым объектам.

## Рекомендации

1. **Production**: Используйте Node.js 20.10+ (LTS)
2. **Development**: Можно использовать флаг `--harmony-explicit-resource-management`
3. **Legacy**: Для Node.js 18 используйте транспиляцию

## Ключевой вывод

Node.js 20.10+ полностью поддерживает `using`. Рекомендуется использовать эту версию или выше.
