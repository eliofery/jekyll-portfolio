---
layout: post
title: Создаем свой framework на нативном JavaScript используя Webpack
date: 2023-09-18 14:23 +0600
description: В этой статье речь пойдет о написании своего велосипеда в виде <b>JavaScript framework</b>. Затронем такие технологии как <b>Webpack</b>, <b>Linter</b>, <b>Jest</b>.
image:
alt: Основное изображение
category: frontend
tags: [javascript, webpack, linter, jest]
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

<h2 id="intro"><span class="attention">Вводная</span> часть</h2>

Основная идея заключается в создании простого, легковесного **фреймворка** используя возможности нативного **JavaScript**. В дальнейшем на основе него будет создано одностраничное приложение (**SPA**).

Каждый шаг создания **JavaScript framework** я старался комментировать свои шаги [в репозитории](https://github.com/eliofery/javascript-framework-webpack/commits/main){:target="_blank" rel="nofollow"}. Обязательно загляните туда если походу статьи вам будет что-то не понятно.

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

Установим соответствующие зависимости.

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

<h2 id="linter"><span class="attention">Установка</span> линтеров</h2>

Линтеры позволяют поддерживать весь код проекта в единообразном стиле, придерживаясь определенных правил написания кода. Линтер не позволит пользователю создать **commit** до тех пор, пока код не будет отредактирован согласно правилам.

Более подробно **линтеры** были рассмотрены в статье про [создание **Gulp** сборки]({{ site.baseurl }}/blog/2023-08-29-gulp-sborka-dlya-verstki-s-ispolzovaniem-pug-shablonizatora-frontend.html#linter){:target="_blank"}, здесь же коснемся самого основного.

### JavaScript

Установим соответствующие зависимости.

{% capture code %}
npm i -D eslint @babel/eslint-parser eslint-plugin-import eslint-config-airbnb-base eslint-config-prettier eslint-import-resolver-alias
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

### CSS и SCSS

Установим соответствующие зависимости.

{% capture code %}
npm i -D stylelint stylelint-config-rational-order stylelint-config-recommended-scss stylelint-config-standard stylelint-order stylelint-scss
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

### Editorconfig и Prettier

Установим соответствующие зависимости.

{% capture code %}
npm i -D editorconfig-checker prettier
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

### Lint staged

Установим соответствующие зависимости.

{% capture code %}
npx mrm lint-staged
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

<h2 id="linter-settings"><span class="attention">Настройка </span> линтеров</h2>

Более подробно настройки **линтеров** были описаны в статье про [создание **Gulp** сборки]({{ site.baseurl }}/blog/2023-08-29-gulp-sborka-dlya-verstki-s-ispolzovaniem-pug-shablonizatora-frontend.html#setting-linter){:target="_blank"}, здесь же коснемся самого основного.

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
  "printWidth": 80,
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
    'import/no-extraneous-dependencies': [
      'off',
      { devDependencies: ['gulpfile.babel.js', 'gulp/**/*'] },
    ],
    'import/no-import-module-exports': [
      'off',
      { exceptions: ['gulpfile.babel.js', 'gulp/**/*'] },
    ],
    'import/resolver': [
      'off',
      { exceptions: ['gulpfile.babel.js', 'gulp/**/*'] },
    ],
    'implicit-arrow-linebreak': [
      'off',
      { exceptions: ['gulpfile.babel.js', 'gulp/**/*'] },
    ],
    'no-var': 'error',
    'no-extra-semi': 'warn',
    'computed-property-spacing': 'warn',
    'no-mixed-spaces-and-tabs': 'warn',
    'one-var': [
      'error',
      {
        var: 'never',
        let: 'never',
        const: 'never'
      }
    ],
    'unicode-bom': 'warn',
    'no-nested-ternary': 'warn',
    'no-obj-calls': 'warn',
    'no-undefined': 'warn',
    'object-curly-newline': 'off',
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
      alias: {
        map: [['@', './src']],
        extensions: ['.js'],
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



































