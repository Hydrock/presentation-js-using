# Слайд 7: Пример: файлы (до)

## Описание слайда

Демонстрация традиционного подхода к работе с файлами без использования `using`.

## Пример кода

```javascript
let file;
try {
    file = await fs.open('data.txt', 'r');
    const content = await file.readFile();
    console.log(content);
} finally {
    if (file) {
        await file.close();
    }
}
```

## Разбор кода

### Объявление переменной
```javascript
let file;
```
Переменная объявляется вне блока try, чтобы быть доступной в блоке finally.

### Открытие файла
```javascript
file = await fs.open('data.txt', 'r');
```
Асинхронное открытие файла. Здесь может произойти исключение (файл не существует, нет прав доступа и т.д.).

### Работа с файлом
```javascript
const content = await file.readFile();
console.log(content);
```
Основная логика работы с файлом. Здесь также могут возникнуть исключения.

### Блок finally
```javascript
finally {
    if (file) {
        await file.close();
    }
}
```
Гарантирует выполнение кода закрытия файла. Проверка `if (file)` необходима, так как файл мог не быть открыт из-за ошибки.

## Проблемы этого подхода

### Много boilerplate-кода
Значительная часть кода — это инфраструктура для обработки ресурсов, а не бизнес-логика.

### Легко допустить ошибку
- Можно забыть объявить переменную вне try
- Можно забыть проверить file на null/undefined
- Можно забыть await при асинхронном закрытии

### Вложенность
При работе с несколькими ресурсами код становится громоздким:

```javascript
let file1, file2;
try {
    file1 = await fs.open('file1.txt', 'r');
    try {
        file2 = await fs.open('file2.txt', 'r');
        // работа с файлами
    } finally {
        if (file2) await file2.close();
    }
} finally {
    if (file1) await file1.close();
}
```

## Вывод

Традиционный подход работает, но требует много рутинного кода и подвержен ошибкам.
