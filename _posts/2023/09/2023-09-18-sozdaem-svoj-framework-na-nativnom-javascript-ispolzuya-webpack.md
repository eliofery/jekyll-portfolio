---
layout: post
title: Создаем самописный framework на нативном JavaScript используя Webpack
date: 2023-09-18 14:23 +0600
description: В этой статье речь пойдет о написании своего велосипеда в виде <b>JavaScript framework</b>. Затронем такие технологии как <b>Webpack</b>, <b>Linter</b>, <b>Redux</b>.
image: cover.jpg
alt: Основное изображение
category: frontend
tags: [javascript, webpack, linter, redux]
source: https://github.com/eliofery/javascript-framework-webpack
published: true
sitemap: true
excerpt_separator: "<!--more-->"
---

{{ page.description }}

<!--more-->

## <span class="attention">Оглавление</span> статьи

1. [Вводная часть](#intro)
2. [Базовая инициализация](#base)
3. [Установка Babel](#babel)
4. [Установка линтеров](#linter)
5. [Настройка линтеров](#linter-settings)
6. [Установка Webpack](#webpack)
7. [Настройка Webpack](#webpack-settings)
8. [Структура проекта](#structure)
9. [Базовая настройка проекта](#base-settings)
10. [Ядро фремворка](#core)
11. [Класс Router](#router)
12. [Класс App](#app)
13. [Класс BaseComponent](#base-component)
14. [Класс BasePage](#base-page)
15. [Класс MainLayout](#main-layout)
16. [Страницы HomePage, AboutPage, ErrorPage](#pages)
17. [Компонент LogoComponent](#logo-component)
18. [Компонент Link](#link-component)
19. [Компонент Response](#reponse)
20. [Компонент Store](#store)
21. [Итоги создания фреймворка](#end)

<h2 id="intro"><span class="attention">Вводная</span> часть</h2>

Основная идея заключается в создании простого, легковесного **фреймворка** используя возможности нативного **JavaScript**. В дальнейшем на основе него будет создано одностраничное приложение (**SPA**).

Каждый шаг создания **JavaScript framework** можно будет отследить [в репозитории](https://github.com/eliofery/javascript-framework-webpack/commits/main){:target="_blank" rel="nofollow"}.

<h2 id="base"><span class="attention">Базовая </span> инициализация</h2>

Создадим базовые файлы для проекта, инициализируем **git** и **npm**. О том как [установить]({{ site.baseurl }}/blog/2023-08-21-linux-dlya-nachinayushhih-chast-3-soft-dlya-povsednevnyh-zadach-linux.html#git){:target="_blank"} и [настроить]({{ site.baseurl }}/blog/2023-08-23-nastrojka-git-program.html){:target="_blank"} **git** говорилось в соответствующих статьях, так же и про [установку npm]({{ site.baseurl }}/blog/2023-08-21-linux-dlya-nachinayushhih-chast-3-soft-dlya-povsednevnyh-zadach-linux.html#node){:target="_blank"}.

### Cоздание README.md

В корень проекта добавим файл **README.md** для вводной информации о сборке.

{% capture code %}
# JavaScript framework

Свой велосипед на нативном **javascript** для создания **spa** приложений.
{% endcapture %}
{% include component/code.html lang='README.md' content=code %}

### Инициализация Git

**Git** нам понадобится в качестве системы учета контроля версий. В дальнейшем мы будем делать **commit** предварительно пропуская сохраняемые файлы через **linter**.

{% include component/code.html lang='bash' content='git init' %}

### Создание .gitignore

В корне проекта создадим файл **.gitignore**, в нем мы будем прописывать пути к каталогам и файлам которые не следует добавлять в репозиторий.

{% capture code %}
.idea
.vscode
node_modules
.eslintcache
{% endcapture %}
{% include component/code.html lang='.gitignore' content=code %}

### Инициализация Npm

Удостоверьтесь что у вас установлен [Node.js]({{ site.baseurl }}/blog/2023-08-21-linux-dlya-nachinayushhih-chast-3-soft-dlya-povsednevnyh-zadach-linux.html#node){:target="_blank"}. Инициализируем пакетный менеджер зависимостей **npm**, с его помощью мы будем устанавливать необходимые зависимости для нашего проекта.

{% include component/code.html lang='bash' content='npm init -y' %}

В корне проекта создастся файл **package.json**, примерное содержимое файла:

{% capture code %}
{
  "name": "javascript-framework-webpack",
  "version": "0.0.1",
  "description": "JavaScript framework для создания spa приложений.",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": ["webpack"],
  "author": "",
  "license": "ISC"
}
{% endcapture %}
{% include component/code.html lang='json' content=code %}

<h2 id="babel"><span class="attention">Установка</span> и настройка Babel</h2>

**Babel** - это инструмент для компиляции и транспиляции **JavaScript** кода. Он применяется для преобразования современного **JavaScript**, написанного с использованием новых синтаксических возможностей ES6+, в старый совместимый код, который может работать в различных браузерах и средах выполнения.

Более подробно об установке **Babel** говорилось статье про [создание **Gulp** сборки]({{ site.baseurl }}/blog/2023-08-29-gulp-sborka-dlya-verstki-s-ispolzovaniem-pug-shablonizatora-frontend.html#gulp){:target="_blank"}, здесь же коснемся самого основного.

Установим необходимые зависимости.

{% capture code %}
npm i -D @babel/core @babel/register @babel/preset-env core-js
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

### Настройка Babel

В корне проекта создадим файл **.babelrc** со следующим содержимым.

{% capture code %}
{
  "presets": [
    [
      "@babel/preset-env",
      {
        "debug": false,
        "useBuiltIns": "usage",
        "corejs": "3"
      }
    ]
  ]
}
{% endcapture %}
{% include component/code.html lang='.babelrc' content=code %}

### Настройка Browserslist

**Browserslist** - это инструмент, который используется для определения набора браузеров, поддерживаемых веб-проектом.

В дальнейшем его настройки пригодятся для компиляции **JavaScript** кода, а так же использования **Autoprefixer** для **CSS** стилей. Отредактируем файл **package.json**, добавив следующую конструкцию:

{% capture code %}
"browserslist": [
  "last 2 version",
  "not dead"
],
{% endcapture %}
{% include component/code.html lang='json' content=code %}

Здесь мы поддерживаем последние 2 версии браузеров и все браузеры которые продолжают развиваться.

<h2 id="linter"><span class="attention">Установка</span> линтеров</h2>

Линтеры позволяют поддерживать весь код проекта в единообразном стиле, придерживаясь определенных правил написания кода. Линтер не позволит пользователю создать **commit** до тех пор, пока код не будет отредактирован согласно правилам.

Более подробно **линтеры** были рассмотрены в статье [создание **Gulp** сборки]({{ site.baseurl }}/blog/2023-08-29-gulp-sborka-dlya-verstki-s-ispolzovaniem-pug-shablonizatora-frontend.html#linter){:target="_blank"}, здесь же коснемся самого основного.

### JavaScript

Установим необходимые зависимости.

{% capture code %}
npm i -D eslint @babel/eslint-parser eslint-plugin-import eslint-config-airbnb-base eslint-config-prettier eslint-import-resolver-webpack
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

**eslint-import-resolver-webpack** - это плагин который используется для определения местоположения модулей при разрешении импортов в проектах.

Описание остальных плагинов смотрите в статье [создание **Gulp** сборки]({{ site.baseurl }}/blog/2023-08-29-gulp-sborka-dlya-verstki-s-ispolzovaniem-pug-shablonizatora-frontend.html#linter){:target="_blank"}.

### CSS и SCSS

Установим необходимые зависимости.

{% capture code %}
npm i -D stylelint stylelint-config-rational-order stylelint-config-recommended-scss stylelint-config-standard stylelint-order stylelint-scss
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

### Editorconfig и Prettier

Установим необходимые зависимости.

{% capture code %}
npm i -D editorconfig-checker prettier
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

### Lint staged

Установим необходимые зависимости.

{% capture code %}
npx mrm lint-staged
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

<h2 id="linter-settings"><span class="attention">Настройка </span> линтеров</h2>

Более подробно настройки **линтеров** были описаны в статье [создание **Gulp** сборки]({{ site.baseurl }}/blog/2023-08-29-gulp-sborka-dlya-verstki-s-ispolzovaniem-pug-shablonizatora-frontend.html#setting-linter){:target="_blank"}, здесь же коснемся самого основного.

Откроем файл **package.json** и отредактируем фрагмент настроек **lint-staged** следующим образом:

{% capture code %}
"lint-staged": {
  "*": "editorconfig-checker --exclude '.git|.husky|node_modules|.eslintcache'",
  "*.scss": "stylelint --fix",
  "*.js": [
    "eslint --cache --fix",
    "prettier --write"
  ]
}
{% endcapture %}
{% include component/code.html lang='json' content=code %}

### Создание .editorconfig

В корне проекта создадим файл **.editorconfig**, в нем мы будем задавать правила того как редактор кода, **IDE** должны стандартизировать содержимое файлов.

{% capture code %}
root = true

[*]
charset = utf-8
indent_style = space
indent_size = 2
end_of_line = lf
trim_trailing_whitespace = true
insert_final_newline = true

[*.{md,json}]
trim_trailing_whitespace = false
{% endcapture %}
{% include component/code.html lang='.editorconfig' content=code %}

### Создание .prettierrc

В корне проекта создадим файл **.prettierrc**, в нем мы будем задавать дополнительные правила того как редактор кода, **IDE** должны стандартизировать содержимое **js** файлов.

{% capture code %}
{
  "singleQuote": true,
  "printWidth": 120,
  "semi": false,
  "tabWidth": 2,
  "trailingComma": "all",
  "useTabs": false,
  "endOfLine": "lf",
  "bracketSpacing": true,
  "arrowParens": "avoid"
}
{% endcapture %}
{% include component/code.html lang='.prettierrc' content=code %}

### Создание .stylelintrc

В корне проекта создадим файл **.stylelintrc**, в нем мы будем задавать дополнительные правила того как редактор кода, **IDE** должны стандартизировать содержимое **scss** файлов.

{% capture code %}
{
  "extends": [
    "stylelint-config-recommended-scss",
    "stylelint-config-rational-order"
  ],
  "plugins": [
    "stylelint-scss"
  ],
  "rules": {
    "at-rule-no-unknown": null,
    "scss/at-if-no-null": null,
    "scss/at-rule-no-unknown": [
      true,
      {
        "ignoreAtRules": [
          "tailwind"
        ]
      }
    ],
    "declaration-empty-line-before": null,
    "order/properties-order": [],
    "plugin/rational-order": [
      true,
      {
        "empty-line-between-groups": true
      }
    ],
    "no-descending-specificity": null,
    "block-no-empty": null,
    "import-notation": null,
    "string-quotes": "double",
    "selector-class-pattern": "^(?:(?:o|c|u|t|s|is|has|_|js|qa)-)?[a-zA-Z0-9]+(?:-[a-zA-Z0-9]+)*(?:__[a-zA-Z0-9]+(?:-[a-zA-Z0-9]+)*)?(?:--[a-zA-Z0-9]+(?:-[a-zA-Z0-9]+)*)?(?:\\[.+\\])?$",
    "selector-no-vendor-prefix": null,
    "scss/no-global-function-names": null
  }
}
{% endcapture %}
{% include component/code.html lang='.stylelintrc' content=code %}

### Создание .eslintrc.js

В корне проекта создадим файл **.eslintrc**, в нем мы будем задавать дополнительные правила того как редактор кода, **IDE** должны стандартизировать содержимое **js** файлов.

{% capture code %}
module.exports = {
  root: true,
  env: {
    browser: true,
    es2023: true,
    node: true,
  },
  extends: ['airbnb-base', 'prettier'],
  parser: '@babel/eslint-parser',
  rules: {
    indent: ['off', 2, { MemberExpression: 'off' }],
    'no-var': 'error',
    'no-extra-semi': 'warn',
    'comma-dangle': ['error', 'always-multiline'],
    'computed-property-spacing': 'warn',
    'no-mixed-spaces-and-tabs': 'warn',
    'one-var': [
      'error',
      {
        var: 'never',
        let: 'never',
        const: 'never',
      },
    ],
    'unicode-bom': 'warn',
    'object-curly-spacing': ['error', 'always'],
    'class-methods-use-this': 'off',
    'default-param-last': 'off',
    'new-cap': ['error', { properties: false }],
    'no-plusplus': 'off',
    'no-prototype-builtins': 'off',
    'no-restricted-syntax': 'off',
    'no-nested-ternary': 'warn',
    'no-obj-calls': 'warn',
    'no-undefined': 'off',
    'object-curly-newline': 'off',
    'no-return-assign': 'off',
    'import/prefer-default-export': 'off',
    'max-len': 'off',
    'no-multi-assign': 'off',
    'no-unused-vars': 'error',
    'no-undef': 'off',
    'no-console': 'error',
    quotes: [2, 'single'],
    'import/no-dynamic-require': 'off',
    'global-require': 'off',
    semi: ['error', 'never'],
    'arrow-parens': ['error', 'as-needed'],
    'no-underscore-dangle': 'off',
    'no-use-before-define': 'off',
    'no-useless-return': 'off',
    'no-param-reassign': 'off',
    'no-new': 'off',
  },
  settings: {
    'import/resolver': {
      webpack: {
        config: 'webpack.config.js',
      },
    },
  },
}
{% endcapture %}
{% include component/code.html lang='.eslintrc' content=code %}

### Создание .eslintignore

В корне проекта создадим файл **.eslintignore**. Он будет использоваться для указания файлов и каталогов, которые необходимо игнорировать во время анализа **ESLint**.

Изначальное содержимое файла **.eslintignore** будет пустым.

### Создание .stylelintignore

В корне проекта создадим файл **.stylelintignore**. Он будет использоваться для указания файлов и каталогов, которые необходимо игнорировать во время анализа **Stylelint**.

Изначальное содержимое файла **.stylelintignore** будет пустым.

### Создание .prettierignore

В корне проекта создадим файл **.prettierignore**. Он будет использоваться для указания файлов и каталогов, которые необходимо игнорировать во время анализа **JavaScript**.

Изначальное содержимое файла **.prettierignore** будет пустым.

<h2 id="webpack"><span class="attention">Установка</span> Webpack</h2>

**Webpack** - это инструмент сборки для веб-разработки, который позволяет разработчикам объединять, управлять и оптимизировать ресурсы, такие как **JavaScript**, **CSS**, изображения и многое другое, в один или несколько пакетов (**bundles**).

### Основные зависимости

Установим базовые зависимости.

{% capture code %}
npm i -D webpack webpack-cli webpack-dev-server webpack-merge dotenv glob-all cross-env
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

**webpack** - непосредственно само ядро сборщика. Предоставляет инструменты для определения и настройки процесса сборки, включая загрузчики (loaders) и плагины (plugins), для преобразования и оптимизации ресурсов.

**webpack-cli** - этот пакет предоставляет интерфейс командной строки для работы с Webpack. Он позволяет разработчикам управлять процессом сборки, запускать сборку, указывать параметры и опции, а также выполнять различные задачи, связанные с сборкой проекта, такие как создание пакетов, запуск сервера разработки и другие.

**webpack-dev-server** - этот пакет представляет собой инструмент для локальной разработки веб-приложений. Он позволяет создавать и запускать локальный веб-сервер, который автоматически перезагружает приложение при изменениях в исходном коде. Это упрощает процесс разработки, поскольку разработчики могут видеть результаты своей работы в реальном времени без необходимости каждый раз вручную пересобирать и перезапускать проект.

**webpack-merge** - это пакет для объединения конфигураций Webpack. Он часто используется в проектах, где требуется разделение конфигурации на несколько файлов (например, один файл для разработки и другой для продакшена) или когда необходимо объединить различные части конфигурации из разных источников.

**dotenv** - это пакет используются в приложении для хранения конфиденциальных данных, настроек и другой информации, которая может изменяться в разных средах, таких как разработка, тестирование и продакшн.

**glob-all** - это пакет предназначенный для выполнения сложных операций над наборами файлов, включая копирование, удаление, обработку и другие манипуляции.

**cross-env** - это удобная утилита командной строки, которая предназначена для устранения различий в командной оболочке между разными операционными системами. Она часто используется в сценариях сборки и запуска **JavaScript** проектов, где необходимо задавать переменные окружения и выполнять скрипты, независимо от операционной системы.

### Обработчики ресурсов (загрузчики)

Обработчики ресурсов играют важную роль в обработке различных типов файлов и ресурсов в вашем проекте. Они представляют собой функции или модули, которые используются для преобразования файлов веб-приложения перед тем, как они будут включены в сборку.

{% capture code %}
npm i -D mini-css-extract-plugin style-loader css-loader postcss-loader autoprefixer sass sass-loader file-loader babel-loader image-webpack-loader
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

**mini-css-extract-plugin** - этот плагин извлекает стили из **JavaScript** файлов и сохраняет их в отдельные **CSS** файлы. Это улучшает производительность и кэширование, так как стили загружаются параллельно с **JavaScript**.

**style-loader** - этот загрузчик внедряет стили непосредственно в **HTML** документ с использованием тегов **&lt;style&gt;**. Это полезно для разработки, но менее эффективно в продакшн среде, поскольку это может замедлить начальную загрузку страницы.

**css-loader** - этот загрузчик позволяет **Webpack** понимать и импортировать файлы **CSS** в вашем **JavaScript** коде. Он также позволяет решать проблемы, такие как обработка импортов, **CSS** модулей и минимизация **CSS**.

**postcss-loader** - этот загрузчик используется для автоматической обработки **CSS** с использованием **PostCSS**. Вы можете использовать его для автопрефиксации, оптимизации **CSS**, поддержки новых возможностей **CSS** и многого другого.

**autoprefixer** - этот загрузчик добавляет вендорные префиксы к CSS-свойствам и значкам, чтобы обеспечить совместимость с различными браузерами.

**sass** - само ядро предпроцессора sass.

**sass-loader** - этот загрузчик преобразует файлы **SASS** или **SCSS** в обычные **CSS**.

**file-loader** - этот загрузчик позволяет импортировать и обрабатывать файлы, такие как изображения, в вашем **JavaScript** коде. Он может переносить файлы и предоставлять url к ним.

**babel-loader** - этот загрузчик преобразует современный **JavaScript** (**ES6+**) в старый **JavaScript** (**ES5**), который совместим с большинством браузеров. Он позволяет использовать современный синтаксис и возможности языка в вашем коде и обеспечивает поддержку различных плагинов и пресетов **Babel**.

**image-webpack-loader** - этот загрузчик оптимизирует изображения.

### Плагины

Плагины позволяют выполнять различные дополнительные задачи при сборке проекта, предоставляют более широкие и гибкие возможности, чем загрузчики (loaders), и могут использоваться для различных целей, таких как оптимизация, минификация, управление ресурсами, создание HTML-страниц, инжекция зависимостей, генерация исходных карт и многое другое.

{% capture code %}
npm i -D clean-webpack-plugin html-webpack-plugin mini-css-extract-plugin svg-sprite-html-webpack svg-inline-loader copy-webpack-plugin terser-webpack-plugin purgecss-webpack-plugin
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

**clean-webpack-plugin** - это предназначен для удаления файлов и каталогов во время сборки проекта. Этот плагин полезен, когда вы хотите гарантировать, что во время каждой новой сборки вашего проекта старые файлы и каталоги будут удалены, предотвращая накопление мусора.

**html-webpack-plugin** - этот плагин позволяет автоматически создавать **HTML** файл, который будет включать в себя ссылки на все сгенерированные **JavaScript** и **CSS** файлы.

**mini-css-extract-plugin** - этот плагин позволяет извлекать **CSS** код из **JavaScript** файлов и сохранять его в отдельных **CSS** файлах.

**svg-sprite-html-webpack** - этот плагин создает спрайт из **svg** иконок.

**svg-inline-loader** - этот плагин позволяет подключать файлы по ссылке до них, например в **css** файле.

**copy-webpack-plugin** - этот плагин позволяет копировать файлы и директории из одного места в другое в процессе сборки.

**terser-webpack-plugin** - этот плагин выполняет минимизацию и оптимизацию **JavaScript** кода, уменьшая его размер и улучшая производительность веб-приложения.

**purgecss-webpack-plugin** - этот плагин предназначен для удаления неиспользуемых стилей из ваших **CSS** файлов. Это позволяет уменьшить размер **CSS** файлов и улучшить производительность загрузки страницы.

<h2 id="webpack-settings"><span class="attention">Настройка</span> Webpack</h2>

В процессе написания конфигураций для сборщика **webpack** мы разделим логику для разработки (**developer**) и готовой версией (**production**), чтобы сократить дублирования и улучшить дальнейшее сопровождение кода.

В корне фреймворка создадим файл **webpack.config.js**, данный файл будет отправной точкой для отслеживания путей до файлов проекта. Благодаря его настройкам и прописанному правилу **import/resolver** в файле **.eslintrc.js** редактор кода или IDE будут понимать где искать файл, в прописанных путях. **Например:** вместо **../../components** мы сможем указать более читаемый путь **@/components**.

Содержимое файла **webpack.config.js** будет следующим:

{% capture code %}
const { resolve } = require('path') // работа с путями к файлам и каталогам

module.exports = {
  resolve: {
    extensions: ['.js'],

    // короткий путь до js файлов через символ @, например @/components/ButtonComponent
    // поиск файлов будет производиться в каталоге src
    alias: {
      '@': resolve(__dirname, 'src'),
    },
  },
}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

Далее в корне проекта создадим каталог **webpack**. Внутри данного каталога создадим следующую структуру:

{% capture code %}
./webpack
├── loaders
├── webpack.common.js
├── webpack.dev.js
└── webpack.prod.js
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

**loaders** - данный каталог будет хранить **webpack** модули для обработки ресурсов во время сборки проекта.

**webpack.common.js** - конфигурационный файл, отвечающий за общую логику как для **developer**, так и для **production** версии.

**webpack.dev.js** - конфигурационный файл, отвечающий за логику **developer** версии.

**webpack.prod.js** - конфигурационный файл, отвечающий за логику **production** версии.

### Описание файла webpack.common.js

Откроем файл **webpack.common.js** и пропишем в него следующее содержимое:

{% capture code %}
const { join } = require('path') // работа с путями к файлам и каталогам
const webpack = require('webpack') // ядро webpack
const { merge } = require('webpack-merge') // объединяет конфигурации

const HtmlWebpackPlugin = require('html-webpack-plugin') // динамически встраивает данные в html
const MiniCssExtractPlugin = require('mini-css-extract-plugin') // сохраняет css и js в отдельные файлы
const CopyWebpackPlugin = require('copy-webpack-plugin') // копирует файлы и директории из одного места в другое
const SvgSpriteHtmlWebpackPlugin = require('svg-sprite-html-webpack') // создает svg спрайт

const jsLoaders = require('./loaders/js-loaders') // обработка js
const cssLoaders = require('./loaders/css-loaders') // обработка стилей
const imageLoaders = require('./loaders/image-loaders') // обработка изображений
const fontLoaders = require('./loaders/font-loaders') // обработка шрифтов

const webpackBase = require('../webpack.config') // базовый конфигурационный файл

// загрузка переменных окружения
require('dotenv').config({
  path: `${process.env.NODE_ENV}.env`, // путь до файла .env, например development.env или production.env
})

module.exports = merge(webpackBase, {
  target: 'web', // целевая среду сборки браузер

  // входная точка
  entry: {
    app: join(__dirname, '../src/main.js'), // основной JavaScript файл приложения
  },

  // исходная точка
  output: {
    publicPath: '/', // публичный путь для доступа к выходным файлам
    path: join(__dirname, '../dist'), // директория для сохранения собранных файлов
    filename: 'js/[name].bundle.js', // имя выходного файла с динамическими именами [name]
    chunkFilename: 'js/[name]-[id].js', // имя файлов чанков с динамическими именами [name]-[id]
    clean: true, // очистка выходной директории перед каждой сборкой
  },

  // дополнительные модули для обработки файлов
  module: {
    rules: [
      // правило для обработки изображений
      {
        test: /\.(png|jpe?g|gif|webp)$/i,
        use: imageLoaders,
      },

      // вместо данной конструкции будет использоваться asset/inline,
      // приведенное ниже правило
      // {
      //   test: /\.svg$/,
      //   use: SvgSpriteHtmlWebpackPlugin.getLoader(),
      //   exclude: /node_modules/,
      // },

      // встроенные ресурсы
      // background: url("@/assets/icons/icon-blank.svg")
      {
        test: /\.svg$/,
        type: 'asset/inline',
        resourceQuery: /inline/,
      },

      // правило для обработки шрифтов
      {
        test: /\.(woff2)$/i,
        use: fontLoaders,
      },

      // правило для обработки css модулей
      {
        test: /\.(sa|sc|c)ss$/i,
        use: cssLoaders.map(item => {
          const clone = { ...item }

          if (clone.loader === 'css-loader') {
            clone.options = {
              modules: true, // включение CSS модулей для css-loader
            }
          }

          return clone
        }),
        include: /\.module\.css$/, // применяется только к файлам с расширением .module.css
      },

      // правило для обработки стилей
      {
        test: /\.(sa|sc|c)ss$/i,
        use: cssLoaders,
        exclude: /\.module\.css$/, // исключение файлов с расширением .module.css.
      },

      // правило для обработки скриптов
      {
        test: /\.m?js$/i,
        use: jsLoaders,
        exclude: /node_modules/, // исключение файлов из node_modules
      },
    ],
  },

  // плагины webpack
  plugins: [
    // определение переменных окружения
    new webpack.DefinePlugin({
      // API_URL будет взять с подключенного файла development.env или production.env
      // Далее в коде проекта переменная process.env.API_URL при сборки заменится на свое значение
      'process.env.API_URL': JSON.stringify(process.env.API_URL),
    }),

    // создание HTML файла на основе шаблона
    new HtmlWebpackPlugin({
      filename: 'index.html', // имя HTML файла
      template: join(__dirname, '../src/index.html'), // шаблон основного index.html файла
    }),

    // извлечение стилей в отдельные CSS файлы
    new MiniCssExtractPlugin({
      filename: 'css/[name].css', // имя CSS файла
      chunkFilename: 'css/[id].css', // имя файлов чанков стилей
    }),

    // svg спрайт
    // пример: &lt;svg&gt; &lt;use xlink:href="#icon-test"&gt;&lt;/use&gt; &lt;/svg&gt;
    new SvgSpriteHtmlWebpackPlugin({
      append: false, // вставить спрайт вначале &lt;body&gt; для false
      includeFiles: [
        'src/assets/icons/*.svg', // подключаемые в спрайт файлы
      ],
    }),

    // копирование файлов и ресурсов
    new CopyWebpackPlugin({
      patterns: [
        {
          from: join(__dirname, '../src/assets'), // откуда копировать
          to: 'assets/[path][name][ext]', // куда копировать
          noErrorOnMissing: true, // не генерировать ошибки при отсутствии файлов
        },
      ],
    }),
  ],
})
{% endcapture %}
{% include component/code.html lang='js' content=code %}

### Обработчики ресурсов

Описывая содержимое файла **webpack.common.js** была добавлена конструкция, которая подключает файлы **loaders**. Данные файлы не что иное, как вынесенная логика обработки различных форматов файлов такие как **css**, **images** и т.д. в отдельные файлы.

{% capture code %}
const jsLoaders = require('./loaders/js-loaders') // обработка js
const cssLoaders = require('./loaders/css-loaders') // обработка стилей
const imageLoaders = require('./loaders/image-loaders') // обработка изображений
const fontLoaders = require('./loaders/font-loaders') // обработка шрифтов
{% endcapture %}
{% include component/code.html lang='js' content=code %}

Создадим эти файлы внутри каталога **loaders** и опишем их:

{% capture code %}
./loaders
├── js-loaders.js
├── css-loaders.js
├── font-loaders.js
└── image-loaders.js
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

### Описание файла js-loaders.js

Откроем файл **js-loaders.js** и пропишем в него следующее содержимое:

{% capture code %}
module.exports = [
  {
    loader: 'babel-loader', // преобразование es6+ в es5
  },
]
{% endcapture %}
{% include component/code.html lang='js' content=code %}

### Описание файла css-loaders.js

Откроем файл **css-loaders.js** и пропишем в него следующее содержимое:

{% capture code %}
const MiniCssExtractPlugin = require('mini-css-extract-plugin') // извлекать css из js

const devMode = process.env.NODE_ENV === 'development' // определение окружения

module.exports = [
  devMode ? { loader: 'style-loader' } : { loader: MiniCssExtractPlugin.loader }, // инлайн стили или стили в отдельный файл
  { loader: 'css-loader' }, // поддержка css файлов
  { loader: 'postcss-loader' }, // автопрефиксер и другие улучшения
  { loader: 'sass-loader' }, // поддержка scss файлов
]
{% endcapture %}
{% include component/code.html lang='js' content=code %}

Здесь подключили различные загрузчики, которые обрабатывают **css** стили, в числе которых присутствует **postcss-loader**. Это мощный инструмент позволяет подключить множество дополнительных расширений, которые улучшат опыт взаимодействия со стилями.

Для того чтобы иметь возможность подключать в **postcss-loader** различные расширения, создадим в корне проекта файл **postcss.config.js** и пропишем в него следующее содержимое:

{% capture code %}
module.exports = {
  plugins: [
    require('autoprefixer'), // добавляет вендорные префексы браузеров к стилям
  ],
}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

В данном случае был подключен **autoprefixer**, но ничто не мешает добавлять и другие требуемые расширения.

### Описание файла font-loaders.js

Откроем файл **font-loaders.js** и пропишем в него следующее содержимое:

{% capture code %}
module.exports = [
  {
    loader: 'file-loader', // импорт и обработка файлов
    options: {
      outputPath: 'fonts', // каталог куда будет происходить импорт
    },
  },
]
{% endcapture %}
{% include component/code.html lang='js' content=code %}

### Описание файла image-loaders.js

Откроем файл **image-loaders.js** и пропишем в него следующее содержимое:

{% capture code %}
module.exports = [
  {
    loader: 'file-loader', // импорт и обработка файлов
    options: {
      name: '[path][name].[ext]', // имя файла
    },
  },
  {
    loader: 'image-webpack-loader', // оптимизация изображений
    options: {
      mozjpeg: {
        progressive: true,
      },
      optipng: {
        enabled: true,
      },
      pngquant: {
        quality: [0.65, 0.90],
        speed: 4,
      },
      gifsicle: {
        interlaced: false,
      },
      webp: {
        quality: 75,
      },
    },
  },
]
{% endcapture %}
{% include component/code.html lang='js' content=code %}

### Переменные окружения

В файле **webpack.common.js** был заложен фундамент для использования переменных окружений, а именно благодаря следующим блокам кода:

{% capture code %}
// загрузка переменных окружения
require('dotenv').config({
  path: `${process.env.NODE_ENV}.env`, // путь до файла .env, например development.env или production.env
})

...

// определение переменных окружения
new webpack.DefinePlugin({
  // API_URL будет взять с подключенного файла development.env или production.env
  // Далее в коде проекта переменная process.env.API_URL при сборки заменится на свое значение
  'process.env.API_URL': JSON.stringify(process.env.API_URL),
}),
{% endcapture %}
{% include component/code.html lang='js' content=code %}

Теперь необходимо создать сами файлы, хранящие переменные окружения. Для этого в корне проекта создадим два файла:

{% capture code %}
./
├── development.env
└── production.env
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

Внутри данных файлов для примера добавим одну переменную, в разработке этих переменных может быть столько сколько необходимо.

{% capture code %}
API_URL=https://example.com
{% endcapture %}
{% include component/code.html lang='env' content=code %}

Теперь добавим правила для запуска сборки, которые будут собирать наш фрэймворк в режиме **developer** или **production**. Для этого отредактируем содержимое файла **package.json**:

{% capture code %}
"scripts": {
  "prepare": "husky install",
  "develop": "cross-env NODE_ENV=development webpack serve --config webpack/webpack.dev.js",
  "build": "cross-env NODE_ENV=production webpack --config webpack/webpack.prod.js"
},
{% endcapture %}
{% include component/code.html lang='json' content=code %}

Здесь мы добавили в секцию **scripts** правила **develop** и **build**.

**develop** - запускает сборку в режиме разработки, позволяя изменять код и видеть изменения в браузере \
**build** - собирает сборку в готовый проект, который можно выгрузить на хостинг

Команда **cross-env NODE_ENV=development** создает переменную **NODE_ENV** с содержимым **development** к которой далее в коде мы можем обращаться.

**webpack serve** позволяет запустить сборку и поднять локальный сервер который отобразит сайт в браузере.

**--config webpack/webpack.dev.js** запускает сборку с настройками прописанными в файле **webpack.dev.js**.

По аналогии тоже самое происходит в **build** режиме за исключением некоторых изменений.

### Описание файла webpack.dev.js

Откроем файл **webpack.dev.js** и пропишем в него следующее содержимое:

{% capture code %}
const { join } = require('path') // работа с путями к файлам и каталогам
const { merge } = require('webpack-merge') // объединяет конфигурации
const { CleanWebpackPlugin } = require('clean-webpack-plugin') // удаляет файлы и каталоги перед каждой сборкой

const common = require('./webpack.common') // общие конфигурации для dev и prod версий

module.exports = merge(common, {
  mode: 'development', // режим разработки
  devtool: 'inline-source-map', // устанавливаем source map для отладки (eval-cheap-module-source-map)
  devServer: { // настройки для сервера разработки
    port: 3003, // порт
    hot: true, // вносит изменения в код приложения без перекомпиляции всего проекта
    compress: true, // сжатие данных
    https: true, // использование https
    historyApiFallback: true, // поддержка HTML5 History API
    static: {
      directory: join(__dirname, '../dist') // указываем путь к статическим файлам
    },
    devMiddleware: { // позволяет моментально видеть изменения
      index: true, // автоматически отображение индексного файла
      publicPath: '/', // базовый url для сервера разработки
      writeToDisk: true, // исходные карты и ресурсы будут записаны на диск
    },
    client: {
      logging: 'log' // логи будут отображаться в консоли браузера
    },
  },
  plugins: [ // плагины webpack
    new CleanWebpackPlugin({ // очистка указанных файлов и каталогов перед сборкой
      cleanOnceBeforeBuildPatterns: [
        join(__dirname, '../dist')
      ]
    })
  ]
})
{% endcapture %}
{% include component/code.html lang='js' content=code %}

### Описание файла webpack.prod.js

Откроем файл **webpack.prod.js** и пропишем в него следующее содержимое:

{% capture code %}
const { join, resolve } = require('path') // работа с путями к файлам и каталогам
const globAll = require('glob-all') // операции над файлами
const { merge } = require('webpack-merge') // объединяет конфигурации

const TerserPlugin = require('terser-webpack-plugin') // выполняет минимизацию и оптимизацию js
const { PurgeCSSPlugin } = require('purgecss-webpack-plugin') // удаляет неиспользуемые css стили

const common = require('./webpack.common') // общие конфигурации для dev и prod версий

module.exports = merge(common, {
  mode: 'production', // режим production
  output: {
    // исходная точка
    publicPath: '/', // публичный путь для доступа к выходным файлам
    path: resolve(__dirname, '../build'), // устанавливает путь для сохранения собранного кода
    filename: 'js/[name].[contenthash].js', // настройка имени собранного файла с хэшем содержимого
    clean: true, // очистка каталога сборки перед каждой новой сборкой
  },
  optimization: {
    // настройки для оптимизации сборки
    minimizer: [
      new TerserPlugin({
        parallel: true, // параллельная минимизация для ускорения сборки
        extractComments: false, // не извлекать комментарии из сжатого кода
        terserOptions: {
          format: {
            comments: false, // не сохранять комментарии
          },
        },
      }),
    ],
  },
  plugins: [
    new PurgeCSSPlugin({
      paths: globAll.sync(join(__dirname, '../src/**/*.js'), { nodir: true }), // пути к js файлам для анализа стилей
    }),
  ],
})
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="structure"><span class="attention">Структура</span> проекта</h2>

Опишем структуру фреймворка, в нашем случае будет использована самая простая классическая файловая структура без какой-либо сложной архитектуры на подобии **FSD**.

Первое что нам понадобится это создать в корне проекта каталог **src** и внутри него будут находиться все исходники фреймворка.

{% capture code %}
./src
├── assets
│    ├── favicons
│    ├── icons
│    └── scss
├── core
├── components
├── pages
├── layouts
├── reducers
├── routers
├── services
├── store
├── utils
├── main.js
└── index.html
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

**assets** - содержит различные иконки, картинки, общие стили, и т.п.

**core** - ядро нашего фреймворка

**components** - отдельные компоненты проектам

**pages** - страницы проекта

**layouts** - общий шаблон для всех страниц проекта, содержит повторяющиеся части от страницы к странице, например шапка, подвал сайта

**reducers** - содержит логику по управлению состоянием в проекте

**routers** - содержит маршруты проекта

**services** - содержит api для подключения к сторонним ресурсам

**store** - содержит state проекта, связан с **reducers**

**utils** - содержит вспомогательные пользовательские функции

**main.js** - основной файл запускающий программную часть проекта

**index.html** - основной файл отображающий проект в браузере

<h2 id="base-settings"><span class="attention">Базовая</span> настройка проекта</h2>

Прежде чем приступить к написанию движка нашего будущего фреймворка начнем с описания файла **index.html** и связанных с ним **assets** файлов.

### Описание файла index.html

Откроем файл **index.html** и введем в него следующее содержимое:

{% capture code %}
&lt;!DOCTYPE html&gt;
&lt;html lang="ru"&gt;
  &lt;head&gt;
    &lt;meta charset="UTF-8"&gt;
    &lt;meta name="viewport" content="width=device-width, initial-scale=1"&gt;
    &lt;title&gt;Главная страница&lt;/title&gt;

    &lt;meta name="theme-color" content="#000"&gt;
    &lt;meta name="msapplication-TileColor" content="#000"&gt;

    &lt;link rel="icon" href="/assets/favicons/favicon.ico" sizes="any"&gt;
    &lt;link rel="icon" href="/assets/favicons/icon.svg" type="image/svg+xml"&gt;
    &lt;link rel="apple-touch-icon" href="/assets/favicons/apple-touch-icon.png"&gt;
    &lt;link rel="manifest" href="/assets/favicons/manifest.json"&gt;
  &lt;/head&gt;
  &lt;body&gt;
    &lt;div id="app"&gt;&lt;/div&gt;
  &lt;/body&gt;
&lt;/html&gt;
{% endcapture %}
{% include component/code.html lang='html' content=code %}

### Описание структуры favicons

Добавим в каталог **./src/assets/favicons** следующие файлы:

{% capture code %}
./src/assets/favicons/
├── apple-touch-icon.png
├── favicon.ico
├── icon-192.png
├── icon-512.png
├── icon.svg
└── manifest.json
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

**Favicons будут иметь следующие разрешения:**

**apple-touch-icon.png** - 180 на 180 \
**favicon.ico** - 32 на 32 \
**icon-192.png** - 192 на 192 \
**icon-512** - 512 на 512 \
**icon.svg** - svg иконка для любого другого изображения

#### Опишем содержимое файла **manifest.json**:

{% capture code %}
{
  "name": "JavaScript framework",
  "short_name": "JavaScript framework",
  "description": "JavaScript framework для создания spa приложений",
  "lang": "ru",
  "dir": "ltr",
  "id": "/",
  "start_url": "/",
  "scope": "/",
  "display": "minimal-ui",
  "orientation": "any",
  "theme_color": "#000",
  "background_color": "#000",
  "prefer_related_applications": false,
  "icons": [
    {
      "src": "icon-192.png",
      "type": "image/png",
      "sizes": "192x192"
    },
    {
      "src": "icon-512.png",
      "type": "image/png",
      "sizes": "512x512"
    },
    {
      "src": "icon.svg",
      "sizes": "any",
      "type": "image/svg",
      "purpose": "maskable"
    }
  ]
}
{% endcapture %}
{% include component/code.html lang='json' content=code %}

### Описание структуры scss

Добавим в каталог **./src/assets/scss** следующие файлы:

{% capture code %}
./src/assets/scss/
├── global.scss
├── mixin.scss
└── variable.scss
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

**global.scss** - содержит глобальные стили для всего проекта \
**mixin.scss** - содержит пользовательские scss миксины \
**variable.scss** - содержит пользовательские scss переменные

#### Опишем содержимое файла **global.scss**:

{% capture code %}
@import "variable";

*,
*::before,
*::after {
  box-sizing: inherit;
}

html,
body {
  height: 100vh;
}

html {
  box-sizing: border-box;
}

body {
  position: relative;

  min-width: 380px;
  margin: 0;

  color: $color-black;
  font-size: 16px;
  font-family: Arial, sans-serif;
  line-height: 1.5;
  text-rendering: optimizeLegibility;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;

  background-color: $color-grey;
}

img {
  max-width: 100%;
  height: auto;
}

#app {
  height: 100%;
}

.container {
  width: 100%;
  max-width: 1210px;
  margin: 0 auto;
  padding: 0 20px;
}

.sr-only {
  position: absolute;

  width: 1px;
  height: 1px;
  margin: -1px;
  padding: 0;
  overflow: hidden;

  border: 0;

  clip: rect(0, 0, 0, 0);
  clip-path: inset(100%);
}

.active {
  color: red;
}
{% endcapture %}
{% include component/code.html lang='scss' content=code %}

#### Опишем содержимое файла **mixin.scss**:

{% capture code %}
@mixin list-reset {
  margin: 0;
  padding: 0;

  list-style: none;
}
{% endcapture %}
{% include component/code.html lang='scss' content=code %}

#### Опишем содержимое файла **variable.scss**:

{% capture code %}
$color-black: #222;
$color-white: #fff;
$color-red: #f31b2c;
$color-grey: #edf1f5;
$color-blue: #1c90e9;
$color-yellow: #ffdd56;
{% endcapture %}
{% include component/code.html lang='scss' content=code %}

### Описание структуры icons

Для примера добавим в каталог с иконками любой svg файл, например **icon-heart.svg**:

{% capture code %}
&lt;svg width="800" height="800" viewBox="0 0 24 24" fill="currentColor" xmlns="http://www.w3.org/2000/svg"&gt;
  &lt;path
    d="M2 9.1371C2 14 6.01943 16.5914 8.96173 18.9109C10 19.7294 11 20.5 12 20.5C13 20.5 14 19.7294 15.0383 18.9109C17.9806 16.5914 22 14 22 9.1371C22 4.27416 16.4998 0.825464 12 5.50063C7.50016 0.825464 2 4.27416 2 9.1371Z"
    fill="currentColor"/&gt;
&lt;/svg&gt;
{% endcapture %}
{% include component/code.html lang='svg' content=code %}

### Завершение базовой настройки проекта

И так мы завершили базовую настройку проекта, пришло время приступить не посредственно к созданию самого ядра фреймворка.

<h2 id="core"><span class="attention">Ядро</span> фреймворка</h2>

Определимся с функциональностью нашего фреймворка. Я выделил следующие составляющие которые в процессе мы реализуем:

- **Роутинг** - фреймворк должен предоставлять механизм для маршрутизации, который позволяет управлять тем, как различные URL-адреса и маршруты в приложении соотносятся с компонентами и действиями.
- **Управление состоянием** - возможность эффективно управлять состоянием приложения. Мы реализуем **Redux** подобное хранилище состояний.
- **Компоненты** - фреймворк должен предоставлять инструменты для создания и взаимодействия с компонентами, позволяющими разделять интерфейс на более мелкие и переиспользуемые части.
- **HTTP запросы** - механизм для выполнения запросов и обработки ответов с сервера. Это может включать в себя AJAX-запросы, работу с API и управление данными.
- **Шаблонизация** - возможность генерации и рендеринга HTML-кода на основе шаблонов или компонентов.
- **События и обработка событий** - механизм для обработки пользовательских событий, как клики, изменения ввода и другие.
- **Маршрутизация по компонентам** - механизм для динамической загрузки и переключения между компонентами на основе URL-адреса и маршрутизации.

<h2 id="router"><span class="attention">Класс</span> Router</h2>

Создадим в каталоге **./src/routers** файл **index.js**. Данный файл будет содержать маршруты нашего приложения. Можно по-разному придумать в каком виде хранить и каким образом регистрировать маршруты. В своем решении мне захотелось сделать это чем то напоминающим регистрацию маршрутов во **Vue**.

И так содержимое файла **./src/routers/index.js** будет следующим:

{% capture code %}
import Router from '@/core/Router/Router' // роутер фрейморка

/**
 * Регистрация роутов
 *
 * @type {*|null}
 */
const router = Router.createRoute({
  // Вид роутинга, в рамках фреймворка будут реализованы
  // два вида:
  // createWebHistory - стандартный роутинг, вида /foo/bar
  // createWebHashHistory - роутинг с хэшем, вида /#/foo/bar
  history: Router.createWebHistory(),

  // Список роутов
  // Здесь мы используем динамический импорт для улучшения производительности
  routes: [
    {
      path: '/', // ссылка на главную страницу
      component: import('@/pages/HomePage'), // класс отвечающий за отображение главной страницы
    },
    {
      path: '/about',
      component: import('@/pages/AboutPage'),
    },
    {
      path: '/.*', // 404 страница
      component: import('@/pages/ErrorPage'), // класс отвечающий за отображение 404 страницы
    },
  ],
})

export default router
{% endcapture %}
{% include component/code.html lang='js' content=code %}

Страница **AboutPage** нам пригодится, чтобы протестировать переход между страницами без перезагрузки страницы, а страница **ErrorPage** нужна для того чтобы при переходе по не зарегистрированному маршруту отображалась пользовательская страница 404.

Подключим созданный файл **./src/routers/index.js** в главный исполняемый файл нашего фреймворка **main.js**:

{% capture code %}
import router from '@/routers' // роутер приложения
{% endcapture %}
{% include component/code.html lang='js' content=code %}

Теперь приступим непосредственно к созданию **Router** для фреймворка. Для этого в каталоге **./scr/core** создадим каталог **Router**. Почему создается целый каталог вместо одного файла **Router.js** так как я предполагаю, что класс **Router** получится довольно большим и чтобы его немного разгрузить я создам некоторые другие файлы в которые вынесу часть логики файла **Router**. И так должна получиться следующая структура:

{% capture code %}
./src/core/Router/
├── Dispatcher.js
├── HistoryTypeEnum.js
└── Router.js
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

**Dispatcher.js** - будет являться родительским классом для класса **Router**, будет содержать вспомогательные методы для **Router**.

**HistoryTypeEnum.js** - будет содержать **ENUM** подобный объект, хранящий виды роутинга (обычный или с хэшем).

**Router.js** - непосредственно сам роутинг, хранящий всю основную логику.

### Описание файла HistoryTypeEnum.js

Данный файл очень простым:

{% capture code %}
/**
 * Режимы истории
 *
 * @type {Readonly<{STATE: string, HASH: string}>}
 */
const HISTORY_TYPE_ENUM = Object.freeze({
  HASH: 'hashchange', // для ссылки с хэшем
  STATE: 'popstate', // для стандартной ссылки
})

export default HISTORY_TYPE_ENUM
{% endcapture %}
{% include component/code.html lang='js' content=code %}

### Описание файла Dispatcher.js

Данный класс будет использовать паттерн **Singleton** для того, чтобы все созданные маршруты аккумулировались внутри одного объекта.

{% capture code %}
import HISTORY_TYPE_ENUM from '@/core/Router/HistoryTypeEnum' // виды роутинга

/**
 * Родительский класс для Router
 *
 * Содержит вспомогательные методы для Router.
 */
export default class Dispatcher {
  /**
   * Разрешение на создание экземпляра класса
   *
   * @type {boolean}
   * @protected
   */
  static _initializing = false

  /**
   * Экземпляр класса
   *
   * @type {null}
   * @protected
   */
  static _instance = null

  /**
   * Режим истории по умолчанию с использованием хэша
   *
   * @type {string}
   * @property
   */
  _history = HISTORY_TYPE_ENUM.HASH

  /**
   * Роуты
   *
   * @type {[]}
   * @property
   */
  _routes = []

  /**
   * Основной корневой элемент в который будет добавляться разметка
   *
   * @type {string}
   * @protected
   */
  _root = '#app'

  /**
   * Данный класс является абстрактным
   */
  constructor() {
    if (!Dispatcher._initializing) {
      throw new TypeError('Нельзя напрямую создать экземпляр данного класса')
    }
  }

  /**
   * Получение основного корневого элемента
   *
   * @returns {string}
   */
  get root() {
    return this._root
  }

  /**
   * Изменение основного корневого элемента
   *
   * @param selector
   */
  set root(selector) {
    this._root = selector
  }

  /**
   * Создание экземпляра класса
   *
   * @returns {*|null}
   */
  static get instance() {
    // Возвращаем объект данного класса если он был уже создан
    if (this._instance instanceof this) {
      return this._instance
    }

    // Если объект не был создан создаем его и возвращаем
    Dispatcher._initializing = true
    this._instance = new this()
    Dispatcher._initializing = false

    return this._instance
  }

  /**
   * Получить активный режим истории
   *
   * @returns {string}
   */
  get history() {
    return this._history
  }

  /**
   * Получить список всех роутов
   *
   * @returns {[]}
   */
  get routes() {
    return this._routes
  }

  /**
   * Режим роута с хэшем
   *
   * @returns {string}
   */
  static createWebHashHistory() {
    return HISTORY_TYPE_ENUM.HASH
  }

  /**
   * Режим роута стандартный
   *
   * @returns {string}
   */
  static createWebHistory() {
    return HISTORY_TYPE_ENUM.STATE
  }

  /**
   * Форматирование текущего адреса страницы для режима хэш
   *
   * @returns {string}
   * @protected
   */
  _strippedHashPath() {
    return `/${window.location.hash.replace(/^#\//, '')}` // '#/foo/bar' -> '/foo/bar'
  }

  /**
   * Форматирование текущего адреса страницы в стандартном режиме
   *
   * @returns {string}
   * @protected
   */
  _strippedPath() {
    return `/${window.location.pathname
      .replace(/^\/+|\/+$/g, '') // '////foo/bar////' -> 'foo/bar'
      .replace(/\/+/g, '/')}` // 'foo/////bar' -> 'foo/bar'
  }
}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

### Описание файла Router.js

Непосредственно сам роутер который берет на себя всю основную работу.

{% capture code %}
{% raw %}
import Dispatcher from '@/core/Router/Dispatcher' // родительский класс
import HISTORY_TYPE_ENUM from '@/core/Router/HistoryTypeEnum' // виды роутинга

/**
 * Роутер
 *
 */
export default class Router extends Dispatcher {
  /**
   * Хранит область где будет располагаться разметка страницы
   *
   * @type {null}
   * @protected
   */
  _pageElement = null

  /**
   * Создание роутера
   *
   * @param history - вид роута
   * @param routes - маршруты
   * @returns {*|null}
   */
  static createRoute({ history, routes }) {
    // Получаем объект класса Router
    const router = Router.instance

    // Определяем режим навигации по сайту
    router._history = history ?? router._history

    // Получаем зарегистрированные маршруты
    router._routes.push(...routes)

    // Возвращаем объект класса Router
    return router
  }

  /**
   * Отрисовка готовой страницы
   *
   * @returns {Promise&lt;void&gt;}
   */
  render = async () => {
    // Находим нужный роут и получаем его component,
    // то что прописано при регистрации роутов.
    // Например: component: import('@/pages/ProductPage')
    const { component } = this._findRoute(this.getUri())

    // Если роутер не был найден выводим служебную
    // 404 страницу об ошибке.
    // Для того чтобы выводилась пользовательская страница 404,
    // при перечислении маршрутов в самом конце списка нужно указать:
    // path: '/.*' и component: import('@/pages/ErrorPage').
    // Далее уже в файле ErrorPage описать как будет выглядеть страница об ошибках.
    if (!component) {
      this._page404()

      return
    }

    // Получаем параметры страницы
    const params = this._getParams()

    // Получаем саму страницу
    const page = await this._getComponent(component, params)

    // Если у страницы задан шаблон, то рендерим шаблон.
    if (page.layout) {
      await this._renderLayout(page.layout)
    } else {
      this._pageElement = null
    }

    // Рендерим страницу
    await this._renderPage(page)
  }

  /**
   * Рендер шаблона страницы
   *
   * @param layout - import('@/layouts/MainLayout')
   * @returns {Promise&lt;void&gt;}
   * @protected
   */
  _renderLayout = async layout => {
    // Страница будет содержать путь до шаблона вида import('@/layouts/MainLayout'),
    // при создании объекта этого класса MainLayout нужно взять саму разметку шаблона
    // и разметку его внутренних элементов.
    const { component, elements } = await this._getComponent(layout)

    // Определяем область где будет располагаться разметка страницы
    this._pageElement = elements.page

    // Получаем корневой элемент в который рендерится весь сайт
    const root = document.querySelector(this.root)

    // Очищаем старое содержимое
    root.innerHTML = ''

    // Рендерим новое
    root.insertAdjacentElement('afterbegin', component)
  }

  /**
   * Рендер страницы
   *
   * @param component
   * @returns {Promise&lt;void&gt;}
   * @protected
   */
  _renderPage = async ({ component }) => {
    // Получаем корневой элемент в который рендерится весь сайт
    let root = document.querySelector(this._root)

    // Если у страницы был найден шаблон, то рендерить нужно только саму страницу,
    // так как шаблон уже добавлен на страницу сайта
    if (this._pageElement) {
      root = this._pageElement
    }

    // Очищаем старое содержимое
    root.innerHTML = ''

    // Рендерим новое
    root.insertAdjacentElement('afterbegin', component)

    // Очищаем область где будет располагаться разметка страницы
    this._pageElement = null
  }

  /**
   * Базовая страница 404
   *
   * @protected
   */
  _page404() {
    document.querySelector(this._root).innerHTML = 'Страница 404 не найдена'
  }

  /**
   * Получение текущей ссылки
   *
   * @returns {string}
   */
  getUri() {
    // Ссылка будет форматирована согласно выбранному режиму
    if (this._history === HISTORY_TYPE_ENUM.STATE) {
      return this._strippedPath()
    }

    return this._strippedHashPath()
  }

  /**
   * Получение параметров из ссылки
   *
   * Пример: /foo/1/bar/2 => { foo: 1, bar: 2 }
   *
   * @returns {{}}
   * @protected
   */
  _getParams() {
    return this.getUri()
      .slice(1)
      .split('/')
      .reduce((acc, item, index, array) => {
        if (index % 2 === 0 && array[index + 1]) {
          acc[item] = array[index + 1]
        }

        return acc
      }, {})
  }

  /**
   * Поиск текущего роута
   *
   * @param route
   * @returns {*}
   * @protected
   */
  _findRoute(route) {
    return this._routes.find(item => {
      const regex = new RegExp(`^${item.path}$`)

      return route.match(regex)
    })
  }

  /**
   * Получение шаблона или страницы
   *
   * @param component - import('@/foo/bar')
   * @param params - { foo: 1, bar: 2 }
   * @returns {Promise&lt;*&gt;}
   * @protected
   */
  async _getComponent(component, params = {}) {
    let Component = component

    // Так как компонент страницы может быть передан как динамически так и нет,
    // то мы должны проверить при динамическом импорте мы получим Promise
    if (Component instanceof Promise) {
      const module = await Component

      Component = module.default
    }

    return new Component({
      ...params,
      router: this,
    })
  }
}
{% endraw %}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="app"><span class="attention">Класс</span> App</h2>

Класс **App** будет исполнять роль инициализации приложения. Он будет получать созданный роут и вызывать его метод на отображение страницы. Прежде всего откроем файл **./src/main.js** и добавим в него к уже имеющемуся коду следующее содержимое:

{% capture code %}
import router from '@/routers' // роутер приложения
import App from '@/core/App' // для инициализации приложения

import '@/assets/scss/global.scss' // глобальные стили приложения

const app = new App(router) // создаем приложение

app.run() // инициализируем приложение
{% endcapture %}
{% include component/code.html lang='js' content=code %}

Входной файл приложения получился у нас довольно компактным и простым. Теперь приступим непосредственно к описанию самого класса **App**. Для этого каталоге **./src/core** создадим файл **App.js** со следующим содержимым:

{% capture code %}
{% raw %}
/**
 * Инициализация приложения
 *
 */
export default class App {
  /**
   * Объект класса Router
   *
   * @type {{}}
   */
  #router = {}

  /**
   * Создание приложения
   *
   * @param router
   */
  constructor(router) {
    // Получаем объект класса Router
    this.#router = router
  }

  /**
   * Инициализация приложения
   *
   * @param selector
   */
  run(selector = '#app') {
    // Определяем основной селектор приложения
    // в котором будет производиться отрисовка сайта
    this.#router['root'] = selector

    // Рендерим страницу сайта
    this._render()

    // При изменении ссылки заново рендерим страницу
    window.addEventListener(this.#router.history, () => this._render())
  }

  /**
   * Рендер страницы сайта
   *
   * @protected
   */
  _render() {
    this.#router.render()
  }
}
{% endraw %}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

Мы полностью закончили с классом **App** и с входным файлом **main.js**.

<h2 id="base-component"><span class="attention">Класс</span> BaseComponent</h2>

Мы создали прочную основу для нашего фреймворка в роли классов **App** и **Router**, но сами по себе они ни чего не дадут без механизма работы с **DOM** деревом. Именно этим мы сейчас займемся, создадим класс **BaseComponent** который будет служить родительским классом для всех наших страниц и компонентов приложения.

В каталоге **./src/core** создадим файл **BaseComponent.js** со следующим содержимым:

{% capture code %}
{% raw %}
export default class BaseComponent {
  /**
   * Node элемент самого компонента
   *
   * @type {null}
   * @protected
   */
  _component = null

  /**
   * Вложенные в компонент элементы верстки
   *
   * С которыми в процессе необходимо будет взаимодействовать.
   *
   * @type {{}}
   * @protected
   */
  _elements = {}

  /**
   * Вложенные в компонент другие компоненты
   *
   * @type {{}}
   * @protected
   */
  _components = {}

  /**
   * Для отмены прослушки событий
   *
   * @type {AbortController}
   * @protected
   */
  _abortController = new AbortController()

  /**
   * Абстрактный класс
   */
  constructor() {
    if (this.constructor.name === 'BaseComponent') {
      throw new TypeError('Абстрактный класс!')
    }
  }

  /**
   * Получение разметки компонента
   *
   * @returns {string}
   * @protected
   */
  get _template() {
    return ''
  }

  /**
   * Получение компонента
   *
   * @returns {null}
   */
  get component() {
    return this._component
  }

  /**
   * Получение вложенных в компонент элементов верстки
   *
   * @returns {{}}
   */
  get elements() {
    return this._elements
  }

  /**
   * Установка вложенных компонентов
   *
   * @param components
   * @protected
   */
  _setComponents(components) {
    this._components = components
  }

  /**
   * Переотрисовка вложенных компонентов
   *
   * @param components
   * @protected
   */
  _reloadComponents(components) {
    this._setComponents(components)
    this._initComponents()
  }

  /**
   * Инициализация компонента
   *
   * @protected
   */
  _init() {
    this._beforeInit().then()
    this._initComponent()
    this._initElements()
    this._initComponents()
    this._initListeners()
    this._afterInit().then()
  }

  /**
   * Пользовательские действия, происходящие до инициализации компонента
   *
   * @returns {Promise&lt;void&gt;}
   * @protected
   */
  async _beforeInit() {
    // Абстрактный метод!
    // await this._loadData()
    // await this._updateData()
  }

  /**
   * Пользовательские действия, происходящие после инициализации компонента
   *
   * @returns {Promise&lt;void&gt;}
   * @protected
   */
  async _afterInit() {
    // Абстрактный метод!
    // await this._loadData()
    // await this._updateData()
  }

  /**
   * Загрузка данных, например по api
   *
   * @protected
   */
  _loadData() {
    // Абстрактный метод!
  }

  /**
   * Обновление данных
   *
   * @protected
   */
  _updateData() {
    // Абстрактный метод!
  }

  /**
   * Создание ноды компонента
   *
   * @protected
   */
  _initComponent() {
    // Создаем элемент обертки
    const wrapper = document.createElement('div')

    // Помещаем внутрь обертки разметку html компонента
    wrapper.innerHTML = this._template

    // Получаем разметку компонента в виде Node
    this._component = wrapper.firstElementChild || wrapper
  }

  /**
   * Получение вложенных элементов в компоненте
   *
   * @param component
   * @protected
   */
  _initElements(component = this._component) {
    // Находим все элементы внутри компонента имеющие атрибут data-el
    const list = component.querySelectorAll('[data-el]')

    // Перебираем каждый вложенный элемент с атрибутом data-el
    list.forEach(item => {
      // Получаем значение атрибута data-el
      const name = item.dataset.el

      // Сохраняем Node каждого элемента
      this._elements[name] = item
    })
  }

  /**
   * Инициализация вложенных компонентов
   *
   * @protected
   */
  _initComponents() {
    // Проходимся по каждому ключу объекта _components
    for (const componentName of Object.keys(this._components)) {
      // Ключи подключаемых компонентов должны совпадать с названием
      // атрибута data-el у вложенных в родительский компонент элемента,
      // находим такой элемент, на его место будет подставляться, вложенный компонент.
      let root = this._elements[componentName]

      // Вложенный компонент можно передать двумя способами:
      // _setComponents({ nameComponent: new SomeComponent() })
      // _setComponents({ nameComponent: SomeComponent })
      // здесь идет определение того каким образом был передан вложенный компонент
      // и на основе этого идет получение свойства _component, который в свою очередь хранит
      // Node элемент компонента
      const { component } =
        typeof this._components[componentName] === 'object' &&
        !Array.isArray(this._components[componentName])
          ? this._components[componentName]
          : new this._components[componentName]()

      // Если элементы найдены, то рендерим компонент
      if (root && component) {
        // Получаем data-el
        component.dataset.el = root.dataset.el

        // Переопределяем внутренний элемент
        this._elements[root.dataset.el] = component

        // Рендерим компонент
        root.insertAdjacentElement('beforebegin', component)
        root.remove()
        root = null

        // Получаем вложенные элементы в компоненте
        this._initElements(component)
      }
    }
  }

  /**
   * Прослушка событий
   *
   * @protected
   */
  _initListeners() {
    // Абстрактный метод!
  }

  /**
   * Обновление содержимого компонента
   *
   * @param data
   */
  update(data = {}) {
    // Перебираем все необходимые вложенные элементы
    // и изменяем их внутреннее содержимое
    for (const [key, value] of Object.entries(data)) {
      if (this._elements[key]) {
        this._elements[key].innerHTML = value
      }
    }
  }

  /**
   * Полное удаление компонента
   */
  destroy() {
    this._remove()
    this._removeListeners()
  }

  /**
   * Удаление компонента
   *
   * @protected
   */
  _remove() {
    // this._component?.remove()
    this._component.innerHTML = ''
    this._elements = {}
  }

  /**
   * Удаление прослушек событий
   *
   * @protected
   */
  _removeListeners() {
    this._abortController.abort()
  }
}
{% endraw %}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

Теперь можно непосредственно приступить к созданию страниц приложения.

<h2 id="base-page"><span class="attention">Класс</span> BasePage</h2>

У всех страниц есть общие повторяющиеся параметры поэтому создадим родительский класс **BasePage** для всех страниц. В каталоге **./src/core** создайте файл **BasePage.js** со следующим содержимым:

{% capture code %}
{% raw %}
import BaseComponent from '@/core/BaseComponent' // Базовый компонент

/**
 * Базовая страница
 */
export default class BasePage extends BaseComponent {
  /**
   * Шаблон страницы
   *
   * @type {Promise&lt;{readonly default?: *}&gt;}
   * @protected
   */
  _layout = import('@/layouts/MainLayout')

  /**
   * Заголовок страницы
   *
   * @type {string}
   * @protected
   */
  _title = 'JavaScript framework'

  /**
   * Создание страницы
   */
  constructor() {
    super()

    document.title = 'Главая страница'
  }

  /**
   * Получение шаблона страницы
   *
   * @returns {Promise&lt;{readonly default?: *}&gt;}
   */
  get layout() {
    return this._layout
  }
}
{% endraw %}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="main-layout"><span class="attention">Класс</span> MainLayout</h2>

При создании класса **BaseComponent** шаблоном по умолчанию для всех страниц мы определили **MainLayout**, в этом разделе давайте займемся его созданием. Создадим каталог **./src/layouts**, который будет хранить различные шаблоны приложения и внутри данного каталога создадим каталог **MainLayout** с файлом внутри **index.js** со следующим содержимым:

{% capture code %}
{% raw %}
import BaseComponent from '@/core/BaseComponent' // базовый компонент

// Стили шаблона
import '@/layouts/MainLayout/main-layout.scss'

/**
 * Основной шаблон
 */
export default class MainLayout extends BaseComponent {
  /**
   * Создание шаблона
   */
  constructor() {
    super()

    // Инициализируем шаблон
    this._init()
  }

  /**
   * Разметка шаблона
   *
   * @returns {string}
     * @protected
     */
  get _template() {
      return `
        &lt;div class="main-layout"&gt;
          &lt;header class="main-header"&gt;
            &lt;div class="container"&gt;Шапка сайта&lt;/div&gt;
          &lt;/header&gt;

          &lt;main data-el="page"&gt;&lt;!-- PageComponent --&gt;&lt;/main&gt;

          &lt;footer class="main-footer"&gt;
            &lt;div class="container"&gt;Copyright 2023&lt;/div&gt;
          &lt;/footer&gt;
        &lt;/div&gt;
      `
  }
}
{% endraw %}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

Сразу создадим файл **main-layout.scss** внутри каталога с нашим шаблоном. В нем будут стили текущего шаблона.

{% capture code %}
{% raw %}
.main-layout {
  display: flex;
  flex-direction: column;
  height: 100%;
}

.main-header {
  margin-bottom: 20px;

  color: #fff;

  background-color: #000;
}

.main-layout main {
  flex-grow: 1;
}

.main-footer {
  margin-top: 20px;
  padding: 20px 0;

  background-color: #c2c2c2;
}
{% endraw %}
{% endcapture %}
{% include component/code.html lang='scss' content=code %}

Не обращайте внимание, что у нас нет разделения на отдельные стили для шапки, подвала и контента, это сделано, чтобы упростить пример. В идеале конечно хорошо было бы вынести шапку и подвал в отдельные компоненты и подключить их в шаблоне. О том как внедрять компоненты на страницу и друг в друга будет рассмотрено в следующем разделе, когда мы приступим к созданию самой страницы.

А сейчас рассмотрим основную логику работы шаблона. У нас имеется атрибут **data-el="page"** он является служебным. В него всегда будет рендериться текущая страница, которая определена в файле **./src/routers/index.js**. При создании шаблона нужно всегда указывать **data-el="page"**.

<h2 id="pages"><span class="attention">Страницы</span> HomePage, AboutPage, ErrorPage</h2>

В разделе [класс Router](/blog/2023-09-18-sozdaem-svoj-framework-na-nativnom-javascript-ispolzuya-webpack-frontend.html#router){:target="_blank"} при создании маршрутов были описаны страницы **HomePage**, **AboutPage** и **ErrorPage** давайте их создадим. Для этого создадим каталог **./src/pages** и внутри него файлы **HomePage.js**, **AboutPage.js** и **ErrorPage.js**.

Если нужно иметь для страницы свои стили, то можно по аналогии с **MainLayout** создать каталог с названием страницы, например **HomePage** и внутри него создать файл **index.js** который будет содержать код логики страницы. И рядом с этим файлом создать файл со стилями, например **home-page.scss**. Затем подключить его в файле **HomePage/index.js** следующим образом: **@import '@/pages/HomePage/home-page.scss'**.

### Страница HomePage.js

Внутри главной страницы подключаем компонент **LogoComponent**, пока он еще не создан к нему приступим немного позже.

{% capture code %}
{% raw %}
import BasePage from '@/core/BasePage' // базовая страница
import LogoComponent from '@/components/LogoComponent' // логотип

// Подключаемые компоненты
const components = {
  logo: new LogoComponent({ title: 'JavaScript Framework' }),
}

/**
 * Главная страница
 */
export default class HomePage extends BasePage {
  /**
   * Создание главной страницы
   */
  constructor() {
    super()

    document.title = `Главная страница - ${this._title}`

    // Подключение компонентов
    this._setComponents(components)

    // Инициализация главной страницы
    this._init()
  }

  /**
   * Разметка страницы
   *
   * @returns {string}
   * @protected
   */
  get _template() {
    return `
      &lt;div class="home-page"&gt;
        &lt;div class="container"&gt;
          &lt;div data-el="logo"&gt;&lt;!-- LogoComponent --&gt;&lt;/div&gt;

          &lt;button type="button" data-el="btn"&gt;Кнопка&lt;/button&gt;
        &lt;/div&gt;
      &lt;/div&gt;
    `
  }

  /**
   * Прослушка событий
   *
   * @protected
   */
  _initListeners() {
    this._elements['btn'].addEventListener('click', () => console.log('Клик по кнопке'))
  }
}
{% endraw %}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

В разметке страницы или другого компонента через атрибут **data-el** определяются области, в которые мы хотим отрисовать другие компоненты. В коде есть объект **components** хранящий различные компоненты. Важно чтобы ключи этого объекта совпадали с названием атрибута **data-el**:

{% capture code %}
{% raw %}
// Значение logo подставится на место data-el="logo"
const components = {
  logo: new LogoComponent({ title: 'JavaScript Framework' }),
}

...

// Значение components.logo подставится на место data-el="logo"
&lt;div data-el="logo"&gt;&lt;!-- LogoComponent --&gt;&lt;/div&gt;
{% endraw %}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

Так же доступ к элементам с атрибутом **data-el** можно получить через свойство **this._elements['сюда указываем имя нужного атрибута']**, например:

{% capture code %}
{% raw %}
...

&lt;button type="button" data-el="btn"&gt;Кнопка&lt;/button&gt;

...
{% endraw %}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

Далее в коде мы можем обратиться к этому элементу например повесив на него событие:

{% capture code %}
{% raw %}
...

_initListeners() {
  this._elements['btn'].addEventListener('click', () => console.log('Клик по кнопке'))
}
{% endraw %}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

Если нужно по новой рендерить компонент, то можно вызвать метод **_reloadComponents**, например:

{% capture code %}
{% raw %}
components.logo = new LogoComponent({ title: 'JavaScript Framework 2.0' })

this._reloadComponents(components)
{% endraw %}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

Основные механики заложенные в наш фреймворк по работе с компонентами мы разобрали, напоминаю что данная логика прописана в файле **./src/core/BaseComponent.js**.

### Страница AboutPage.js

Страницы **AboutPage** будет носить чисто информативный характер, для того чтобы мы могли протестировать переключение между страницами без перезагрузки страницы.

{% capture code %}
{% raw %}
import BasePage from '@/core/BasePage' // базовая страница

/**
 * Страница о нас
 */
export default class AboutPage extends BasePage {
  /**
   * Создание страницы о нас
   */
  constructor() {
    super()

    document.title = `О нас - ${this._title}`

    // Инициализация страницы о нас
    this._init()
  }

  /**
   * Разметка страницы
   *
   * @returns {string}
     * @protected
     */
  get _template() {
      return `
        &lt;div&gt;
          Создание самописного framework на нативном JavaScript с использованием Webpack.
        &lt;/div&gt;
      `
  }

  /**
   * Прослушка событий
   *
   * @protected
   */
  _initListeners() {}
}
{% endraw %}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

### Страница ErrorPage.js

Страницы **ErrorPage** будет отображаться при переходе по не зарегистрированному маршруту.

{% capture code %}
{% raw %}
import BasePage from '@/core/BasePage' // базовая страница

/**
 * Страница 404
 */
export default class ErrorPage extends BasePage {
  /**
   * Создание страницы
   */
  constructor() {
    super()

    document.title = `Страница 404 - ${this._title}`

    // Инициализация страницы
    this._init()
  }

  /**
   * Разметка страницы
   *
   * @returns {string}
     * @protected
     */
  get _template() {
      return `
        &lt;div&gt;
          Страница не найдена 404.
        &lt;/div&gt;
      `
  }

  /**
   * Прослушка событий
   *
   * @protected
   */
  _initListeners() {}
}
{% endraw %}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="logo-component"><span class="attention">Компонент</span> LogoComponent</h2>

В предыдущем разделе мы создали страницу **HomePage**, где внедряли компонент **LogoComponent**. В этом разделе мы опишем его.

Создадим каталог **./src/components/LogoComponent** и внутри него файл **index.js** который будет содержать всю логику компонента. Так же рядом можно создать файл со стилями данного компонента, например **logo.scss**. Опишем наш компонент:

{% capture code %}
{% raw %}
import BaseComponent from '@/core/BaseComponent' // базовый компонент

import '@/components/LogoComponent/logo.scss'

/**
 * Логотип
 */
export default class LogoComponent extends BaseComponent {
  /**
   * Создание главной страницы
   */
  constructor({ title = '' } = {}) {
    super()

    this._title = title

    // Инициализация компонента лого
    this._init()
  }

  /**
   * Разметка компонента
   *
   * @returns {string}
   * @protected
   */
  get _template() {
    return `
      &lt;div&gt;
        &lt;img src="logo.svg" width="150" height="80" alt="${this._title}"&gt;
      &lt;/div&gt;
    `
  }

  /**
   * Прослушка событий
   *
   * @protected
   */
  _initListeners() {}
}
{% endraw %}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

Главным отличием компонента от страницы является, то что он наследуется не от **BasePage**, а напрямую от **BaseComponent**. В остальном он так же может внедрять внутрь себя другие компоненты, как это было показано на примере **HomePage**.

В конструктор компонента мы можем передавать какие-то специфичные для этого компонента параметры и затем в коде обращаться к ним, как это сделано на примере значения **title**.

Теперь в **HomePage** на место **&lt;div data-el="logo"&gt;&lt;!-- LogoComponent --&gt;&lt;/div&gt;** подставится содержимое **get _template()** компонента **LogoComponent**.

<h2 id="link-component"><span class="attention">Компонент</span> Link</h2>

Компонент **Link** нужен для того, что бы реализовать переключение между страницами без перезагрузки страницы. Добавим данный компонент для этого создадим файл **./src/core/Link.js**. Опишем его код:

{% capture code %}
{% raw %}
import Router from '@/core/Router/Router' // роутер
import BaseComponent from '@/core/BaseComponent' // базовый компонент

/**
 * Ссылка на внутренние страницы приложения
 */
export default class Link extends BaseComponent {
  /**
   * Список всех проинициализированных ссылок
   *
   * @type {[]}
   * @protected
   */
  static _links = []

  /**
   * Создание ссылки
   *
   * @param url
   * @param html
   * @param attributes
   * @param activeClass
   */
  constructor({
    url = '',
    html = '',
    attributes = {},
    activeClass = 'active',
  } = {}) {
    super()

    this._router = Router.instance // роутер

    this._url = this._correctUrl(url) // ссылка
    this._html = html // содержимое ссылки
    this._attributes = attributes // атрибуты ссылки
    this._activeClass = activeClass // класс активной ссылки

    this._init() // инициализация компонента Link
  }

  /**
   * Получение роутера
   *
   * @returns {Router.instance}
   */
  get router() {
    return this._router
  }

  /**
   * Получение активного класса
   *
   * @returns {string}
   */
  get activeClass() {
    return this._activeClass
  }

  /**
   * Исправление ссылки для ссылки вида хэш
   *
   * @param url
   * @returns {*}
   * @protected
   */
  _correctUrl(url) {
    if (this._router.history === Router.createWebHashHistory()) {
      url = url.replace(/^\//, '/#/') // меняет / -> /#/
    }

    return url
  }

  /**
   * Инициализация компонента
   *
   * @protected
   */
  _initComponent() {
    // Создаем элемент ссылка
    this._component = document.createElement('a')

    // Если урл ссылки соответствует урл в браузере делаем ссылку активной
    if (this._url === this._router.getUri()) {
      this._component.classList.add(this._activeClass)
    }

    // Добавляем урл в ссылку
    this._component.setAttribute('href', this._url)

    // Добавляем содержимое в ссылку
    this._component.innerHTML = this._html

    // Добавляем аттрибуты ссылки
    Object.entries(this._attributes).forEach(([prop, value]) => {
      this._component.setAttribute(prop, `${value}`)
    })

    // Аккумулируем все ссылки
    Link._links.push({
      link: this._component,
      activeClass: this._activeClass,
    })
  }

  /**
   * Прослушка событий
   *
   * @protected
   */
  _initListeners() {
    this._component.addEventListener('click', async evt => {
      // Отменяем стандартный переход по ссылки
      evt.preventDefault()

      // Получаем урл ссылки
      const path = evt.currentTarget.getAttribute('href')

      // Меняем урл в браузере на урл ссылки
      window.history.pushState(null, null, path)

      // Переключаем классы у активных ссылок
      this._toggleClass()

      // Рендарим страницу
      await this._router.render()
    })

    // Переключаем классы у активных ссылок при изменении страницы
    window.addEventListener(this._router.history, () => this._toggleClass(), {
      signal: this._abortController.signal,
    })
  }

  /**
   * Переключаем классы у активных ссылок
   *
   * @protected
   */
  _toggleClass() {
    Link._links.forEach(({ link, activeClass }) => {
      const linkUri = new URL(link.href).pathname

      if (linkUri === this._router.getUri()) {
        link.classList.add(activeClass)
      } else {
        link.classList.remove(activeClass)
      }
    })
  }
}
{% endraw %}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

Теперь мы можем подключить компонент **Link** к примеру в **MainLayout**, например:

{% capture code %}
{% raw %}
...

const components = {
  homeLink: new Link({
    url: '/',
    html: 'Главная страница',
    attributes: { class: 'home' },
  }),

  aboutLink: new Link({
    url: '/about',
    html: 'О фреймворке',
    attributes: { class: 'framework' },
  }),
}

...

constructor() {
  super()

  // Установка компонентов
  this._setComponents(components)

  // Инициализируем шаблон
  this._init()
}

get _template() {
  return `
    &lt;div class="main-layout"&gt;
      &lt;header class="main-header"&gt;
        &lt;div class="container"&gt;
          &lt;div data-el="homeLink"&gt;&lt;!-- homeLink --&gt;&lt;/div&gt;
          &lt;div data-el="aboutLink"&gt;&lt;!-- aboutLink --&gt;&lt;/div&gt;
        &lt;/div&gt;
      &lt;/header&gt;

      &lt;main data-el="page"&gt;&lt;!-- PageComponent --&gt;&lt;/main&gt;

      &lt;footer class="main-footer"&gt;
        &lt;div class="container"&gt;Copyright 2023&lt;/div&gt;
      &lt;/footer&gt;
    &lt;/div&gt;
  `
}
{% endraw %}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="response"><span class="attention">Класс</span> Response</h2>

Создадим простой аналог библиотеки **axios** для запросов данных у сервера по **api**. Для этого создадим файл **./src/core/Response.js** со следующим содержимым:

{% capture code %}
{% raw %}
/**
 * Запросы к серверу
 *
 * Вольная реализация axios.
 */
export default class Response {
  // Используется для создания объекта класса
  static _initialization = false

  // Опции запроса
  _options = {}

  // Базовый урл запроса
  _baseUrl = ''

  /**
   * Создание запроса напрямую запрещен
   */
  constructor() {
    if (!Response._initialization) {
      throw new TypeError('Нельзя напрямую создать экземпляр данного класса')
    }
  }

  /**
   * Создание запроса
   *
   * @param options
   * @returns {Response}
   */
  static create(options = {}) {
    // Создаем объект класса
    Response._initialization = true
    const response = new Response()
    Response._initialization = false

    // Получаем базовый урл
    response._baseUrl = options.baseUrl ?? ''

    // Удаляем базовый урл из опциональных параметров
    delete options.baseUrl

    // Получаем опциональные параметры
    response._options = options

    return response
  }

  /**
   * Запрос на сервер
   *
   * @param url
   * @param options
   * @returns {Promise&lt;any&gt;}
   * @private
   */
  _response = async (url, options = {}) => {
    try {
      const response = await fetch(url, options)

      return await response.json()
    } catch (e) {
      // eslint-disable-next-line
      if (process.env.NODE_ENV === 'development') console.log(e.message)
      throw e
    }
  }

  /**
   * Получение данных методом GET
   *
   * @param url
   * @returns {Promise&lt;*&gt;}
   */
  get(url = '') {
    url = new URL(url, this._baseUrl)

    return this._response(url.href, this._options)
  }

  /**
   * Получение данных методом POST
   *
   * @param url
   * @param data
   * @returns {Promise&lt;*&gt;}
   */
  post(url = '', data = {}) {
    url = new URL(url, this._baseUrl)

    this._options = {
      ...this._options,
      method: 'post',
      body: data,
    }

    return this._response(url.href, this._options)
  }
}
{% endraw %}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

### ProductService.js

Теперь создадим **api** сервис который используя созданный класс **Response** будет позволять получать данные с удаленного сервера.

Для этого создадим файл **/src/services/ProductService.js** со следующим содержимым:

{% capture code %}
{% raw %}
import Response from '@/core/Response' // подобие axios

// Подготовительные POST запросы
const apiClientPost = Response.create({
  baseUrl: process.env.API_URL, // смотреть файл development.env или production.env
  credentials: 'omit',
  headers: {
    'Content-Type': 'application/json; charset=UTF-8',
  },
})

// Подготовительные GET запросы
const apiClientGet = Response.create({
  baseUrl: process.env.API_URL, // смотреть файл development.env или production.env
  credentials: 'omit',
  headers: {
    'Content-Type': 'application/x-www-form-urlencoded',
  },
})

export default {
  /**
   * Добавление продукта
   *
   * @param data
   * @returns {*}
   */
  addProducts(data) {
    return apiClientPost.post('/products/add', data)
  },

  /**
   * Получение продуктов
   *
   * @returns {*}
   */
  loadProducts() {
    return apiClientGet.get('/products')
  },
}
{% endraw %}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

Далее мы можем получить данные с сервера например на странице **HomePage**:

{% capture code %}
{% raw %}
import ProductService from '@/services/ProductService'

const { addProducts, loadProducts } = ProductService

const products = await loadProducts()
const res = await addProducts(JSON.stringify({ name: 'Product new', type: 'discount' }))
{% endraw %}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

Это примерный псевдо код как можно воспользоваться созданным сервисом для общего понимания принципа.

Мы могли без проблем напрямую через класс **Response** обратиться к **api** серверу, но тогда бы у нас обращение к серверу было бы разбросанно по всему коду нашего проекта. Прелесть создания сервисов в том, что мы собираем подключение к серверу в одном месте и потом в нужных участках кода обращаемся к нему. И если в дальнейшем понадобится изменить какие-то параметры подключения мы это сделаем в одном месте.

<h2 id="store"><span class="attention">Класс</span> Store</h2>

Создадим простой аналог библиотеки **Redux** для сохранения состояний приложения. Для этого добавим файл **./src/core/Store.js** со следующим содержимым:

{% capture code %}
{% raw %}
/**
 * Менеджер состояний
 *
 * Вольная реализация Redux.
 */
export default class Store {
  /**
   * Редьюсеры
   *
   * @type {[]}
   * @protected
   */
  _reducers = []

  /**
   * Состояния
   *
   * @type {{}}
   * @protected
   */
  _state = {}

  /**
   * События
   *
   * @type {{}}
   * @protected
   */
  _listeners = {}

  /**
   * Создание хранилища
   *
   * @param reducers
   * @param initState
   */
  constructor(reducers = [], initState = {}) {
    this._reducers = reducers
    this._state = initState
  }

  /**
   * Получить состояние
   *
   * @param name
   * @returns {{}|*}
   */
  getState(name = '') {
    if (this._state[name]) {
      return this._state[name]
    }

    return this._state
  }

  /**
   * Установить состояние
   *
   * @param newState
   * @protected
   */
  _setState(newState) {
    this._state = newState
  }

  /**
   * Подписки на события
   *
   * @param name
   * @param callback
   * @returns {(function(): void)|*}
   */
  subscribe(name, callback) {
    if (!this._listeners[name]) {
      this._listeners[name] = []
    }

    this._listeners[name].push(callback)

    return () => {
      this._listeners = this._listeners[name].filter(
        listener => listener !== callback,
      )
    }
  }

  /**
   * Вызов события
   *
   * @param action
   */
  dispatch(action) {
    for (const reducer of this._reducers) {
      const previousState = this.getState()
      const newState = reducer(previousState, action)

      if (newState) {
        this._setState(newState)

        const listeners = this._listeners[action.type]

        if (listeners) {
          const currentState = this.getState()

          for (const listener of listeners) {
            listener(currentState)
          }
        }
      }
    }
  }
}
{% endraw %}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

### bidsReducer

Пример того как мог бы выглядеть **reducer**. Создайте файл **./src/reducers/bidsReducer.js** со следующим содержимым:

{% capture code %}
{% raw %}
import BidService from '@/services/BidService' // запросы заявок

const { loadBids } = BidService // получение заявок

export const REFRESH_BIDS = 'REFRESH_BIDS' // для обновления заявок

/**
 * Обновление заявок
 *
 * @param bids
 * @returns {{bids, type: string}}
 */
export const refreshBids = bids => ({
  type: REFRESH_BIDS,
  bids,
})

/**
 * Получение заявок
 *
 * @type {*}
 */
const bids = await loadBids()

/**
 * Состояние заявок
 *
 * @type {{bids: *, countBids}}
 */
export const bidsState = {
  bids,
  countBids: bids.length,
}

/**
 * Reducer заявок
 *
 * @param previousState
 * @param action
 * @returns {*|null}
 */
export const bidsReducer = (previousState, action) => {
  switch (action.type) {
    case REFRESH_BIDS:
      previousState.bids = action.bids

      return {
        ...previousState,
      }

    default:
      return null
  }
}

export default bidsReducer
{% endraw %}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

### Создание менеджера состояния

Теперь когда у нас есть менеджер состояний и редьюсер добавим наше хранилище. Для этого создадим файл **./src/store/index.js** со следующим содержимым:

{% capture code %}
{% raw %}
import Store from '@/core/Store' // хранилище типа Redux
import { bidsReducer, bidsState } from '@/reducers/bidsReducer' // reducer для заявок

// Объединяем все reducer
const reducers = [bidsReducer]

// Объединяем все состояния
const initState = {
  ...bidsState,
}

// Регистрация хранилища состояний
const store = new Store(reducers, initState)
const storeKey = Symbol.for('storeKey')

// Не обязательно использовать globalThis можно просто вернуть store
// globalThis в дальнейшем может пригодиться для отладки
globalThis[storeKey] = store

export default globalThis[storeKey]
{% endraw %}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

### Пример использования менеджера состояния

Где-то в коде страницы или компонента мы можем подписаться на событие **REFRESH_BIDS**.

{% capture code %}
{% raw %}
import store from '@/store'

store.subscribe(REFRESH_BIDS, () => {
  // Здесь происходит какая то логика.
  // Данный колбэк сработает при вызове редьюсера у которого параметр type будет равен REFRESH_BIDS.
})
{% endraw %}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

И затем к примеру в другом компоненте вызвать действие редьюсера:

{% capture code %}
{% raw %}
const data = [1,2,3,4,5]

store.dispatch(refreshBids(data))
{% endraw %}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="end"><span class="attention">Итоги</span> создания фреймворка</h2>

Мы проделали большую работу в создании собственного фреймворка. Многие вещи скорей всего до конца не раскрыты ввиду того, что требуется большое количество времени чтобы все более подробно и детально расписать.

Насколько мог я, прокомментировал каждую строчку кода, и надеюсь это поможет хоть немного при изучении данного материала понять логику и суть проделанной работы.

Ну а пока можете ознакомиться с [готовым сайтом](https://github.com/eliofery/javascript-framework-shop){:target="_blank" rel="nofollow"} написанном мной на этом фреймворке. [Исходники готового сайта](https://github.com/eliofery/javascript-framework-shop){:target="_blank" rel="nofollow"} так же будут доступны в репозитории.

Успешного изучения **JavaScript**.
