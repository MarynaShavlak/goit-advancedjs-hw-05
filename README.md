# goit-advancedjs-hw-05

ДЗ по темі «Вступ до TypeScript». Налаштувала середовище: компілятор, проєкт
через `tsconfig.json`, локальний сервер і source maps для налагодження в
браузері.

## Запуск

```bash
npm install
npm start       # спочатку tsc, потім відкриває сторінку на web-dev-server
npm run watch   # tsc -w в окремому терміналі, перекомпілює при збереженні
```

На сторінці є поле і кнопка Concat!. По кліку в консоль виводиться текст з поля
і `hello!`.

## Структура

```
index.html            підключає dist/index.js як модуль
src/index.ts          кнопка і обробник кліку
src/concatenation.ts  сама функція
tsconfig.json
dist/                 з'являється після tsc, в git не йде
```

## tsconfig.json

- `rootDir: ./src` і `outDir: ./dist`: ts лежить окремо, скомпільований js
  окремо
- `lib: ["dom", "ES2021"]`: без `dom` TypeScript не знає, що таке `document`
- `module: es2020`, `target: es2019`
- `strictNullChecks`: `querySelector('button')` має тип
  `HTMLButtonElement | null`, тому перед `addEventListener` перевіряю, що
  елемент знайшовся
- `sourceMap: true`: поруч з кожним `.js` у `dist` з'являється `.map`

## Debugging

DevTools → Sources: там видно `src/index.ts` і `src/concatenation.ts`.
Breakpoint можна ставити прямо в `.ts`, натискаю Concat!, і виконання
зупиняється на рядку TypeScript, хоча браузер насправді виконує `dist/*.js`.
Зв'язок між ними і є той `.map` файл.

## Що було не очевидно

- У TypeScript 7 прибрали `moduleResolution: "node"`, з конфігом з конспекту
  `tsc` падає з `TS5108`. Поставила в проєкт `typescript@5` як devDependency.
  Заодно версія компілятора тепер записана в проєкті, а не залежить від того, що
  стоїть глобально.
- `@web/dev-server` 0.4.6 з `--node-resolve` падав на кожному запиті з
  `Cannot read properties of undefined (reading 'pluginContext')`. Оновила до
  1.0.0.
- `dist` в `.gitignore`, тож після клонування `index.html` нема що підключати.
  Додала `prestart: tsc`, щоб `npm start` спочатку компілював.
- `tsc` не дописує `.js` в імпортах: у `dist/index.js` лишається
  `from './concatenation'`, і такий файл браузер напряму не знайде (404). Працює
  тільки тому, що web-dev-server з `--node-resolve` сам переписує шлях на
  `./concatenation.js`.
- Якщо відкрити `index.html` просто подвійним кліком (`file://`), модулі не
  завантажаться через CORS. Потрібен сервер.
