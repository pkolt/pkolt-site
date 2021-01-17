---
title: Составные проекты на TypeScript
emoji: 🔦
tags:
  - TypeScript
---

В больших проектах на TypeScript код часто разделяется на независимые подпроекты. Рассмотрим возможность такого разделения в TypeScript с использованием [Project References](https://www.typescriptlang.org/docs/handbook/project-references.html).

Например в нашем проекте мы будем использовать такую структуру папок:

```
|- shared (общий код между проектами)
|- server (бекенд)
|- client (фронтенд)
```

## Shared-проект

### Создание shared-проекта

Выполняем команды:

- Создание папки проекта `mkdir shared && cd shared`
- Инициализация npm `npm init -y`
- Установка зависимостей `npm i -D typescript`
- Инициализация TypeScript `npx tsc --init`

### Настройка TypeScript в shared-проекте

tsconfig.json

```json
{
  "compilerOptions": {
    // ...
    "declaration": true,
    "declarationMap": true,
    "outDir": "./dist",
    "rootDir": "./src",
    "composite": true
  }
}
```

### Общий код в shared-проекте

Создадим в папке `shared` подпапку `src`, в которой будет находиться модуль `index.ts` с константными значениями.

```bash
mkdir src && cd src && touch index.ts
```

Напишем в модуле `index.ts` следующий код:

```typescript
export const HOST = "localhost";
export const PORT = 3000;
```

## Server-проект

### Создание server-проекта

Выполняем команды:

- Создание папки проекта `mkdir server && cd server`
- Инициализация npm `npm init -y`
- Установка зависимостей `npm i -D typescript`
- Инициализация TypeScript `npx tsc --init`

### Настройка TypeScript в server-проекте

tsconfig.json

```json
{
  "compilerOptions": {
    // ...
    "outDir": "./dist",
    "rootDir": "./src",
    "baseUrl": "./src"
  },
  "references": [
    {
      "path": "../shared"
    }
  ]
}
```

### Подключение общего кода код в server-проекте

Создадим в папке `shared` подпапку `src`, в которой будет находиться модуль `index.ts`.

```bash
mkdir src && cd src && touch index.ts
```

Напишем в модуле `index.ts` следующий код:

```typescript
import { HOST, PORT } from '../../shared/src';

console.log(`${HOST}:${PORT}`);
```

### Компиляция и запуск server-проекта

Компиляция server-проекта (добавляем флаг `-b` для сборки связанного проекта):

```bash
npx tsc -b
```

Запуск server-проекта:

```bash
node dist/index.js
```