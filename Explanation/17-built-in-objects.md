# Слайд 17: Встроенные объекты

## Описание слайда

Обзор встроенных объектов JavaScript и браузерных API, которые уже поддерживают протокол dispose.

## Объекты с Symbol.dispose

### FileSystemHandle (File System Access API)
API для работы с файловой системой в браузере. Дескрипторы файлов автоматически освобождаются при выходе из scope.

```javascript
const handle = await window.showOpenFilePicker();
using file = await handle.getFile();
// Автоматическое освобождение при выходе из scope
```

### ReadableStream и WritableStream
Streams API поддерживает автоматическое освобождение:

```javascript
{
    using stream = new ReadableStream({...});
    const reader = stream.getReader();
    // работа со стримом
} // stream автоматически закрывается
```

### WebSocket (в некоторых реализациях)
Некоторые браузеры добавляют поддержку dispose для WebSocket соединений.

## Примеры использования

### File System Access API
```javascript
async function readFile() {
    const [handle] = await window.showOpenFilePicker();
    using file = await handle.getFile();
    
    const text = await file.text();
    console.log(text);
    
    // Дескриптор файла освобождается автоматически
}
```

### Работа с множественными ресурсами
```javascript
async function processFiles() {
    const handles = await window.showOpenFilePicker({ multiple: true });
    
    for (const handle of handles) {
        using file = await handle.getFile();
        await processFile(file);
    } // Каждый файл освобождается после обработки
}
```

## Node.js встроенные API

В Node.js постепенно добавляется поддержка:

### fs.FileHandle
```javascript
const fs = require('fs/promises');

async function readConfig() {
    using file = await fs.open('config.json', 'r');
    return JSON.parse(await file.readFile('utf8'));
}
```

### Streams
```javascript
const { Readable } = require('stream');

function createStream() {
    using stream = new Readable({...});
    return stream;
}
```

## Будущие API

Ожидается поддержка в:
- IndexedDB connections
- WebGL contexts
- Audio contexts
- WebGPU devices

## Проверка поддержки

```javascript
if (Symbol.dispose in FileSystemHandle.prototype) {
    console.log('FileSystemHandle поддерживает dispose');
}
```

## Создание адаптеров

Для API без нативной поддержки можно создать адаптер:

```javascript
function withDispose(handle) {
    return {
        handle,
        [Symbol.dispose]() {
            handle.close();
        }
    };
}

using resource = withDispose(someHandle);
```

## Ключевой вывод

Современные браузерные API и Node.js API уже начинают поддерживать протокол dispose, что делает `using` ещё более полезным.
