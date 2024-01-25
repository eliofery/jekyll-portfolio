---
layout: portfolio
title: Разработка интернет магазина недвижимости на нативном JavaScript
date: 2024-01-25 07:06 +0600
description: Реализация интернет магазина продажи квартир на нативном <b>JavaScript</b>.
image: cover.jpg
alt: Блог-портфолио сайт
work-year: 2023
roles: [frontend]
tags: [javascript, webpack, spa, linter, redux]
source: https://eliofery.github.io/javascript-framework-shop/
published: true
sitemap: false
excerpt_separator: "<!--more-->"
---

{{ page.description }}

<!--more-->

## <span class="attention">Оглавление</span> статьи

1. [Описание проекта](#intro)
2. [Функционал проекта](#base)
3. [Итоги проекта](#total)

<h2 id="intro"><span class="attention">Описание</span> проекта</h2>

Основная идея заключалась в создании проекта с применением полученных знаний при изучении **JavaScript**. За основу был взят проект, разработанный в рамках [этой статьи]({{ site.baseurl }}/blog/2023-09-18-sozdaem-svoj-framework-na-nativnom-javascript-ispolzuya-webpack-frontend.html){:target="_blank"}.

Дизайн, идея проекта, а так же API был взят из платного курса от [webcademy.ru](https://webcademy.ru/jscourse/){:target="_blank" rel="nofollow"}.

<h2 id="base"><span class="attention">Функционал</span> проекта</h2>

<h3>Роутинг</h3>

Маршрутизация в проекте выполнена как **spa**, что позволяет переходить по ссылкам сайта без обновления самой страницы.
Поддерживаются два вида маршрутов:

1. Старый вариант с использование хэшей **#/foo/bar**
2. Актуальный вариант с использованием привычных ссылок **/foo/bar**

<h3>Фильтр товаров</h3>

При первом переходе на сайт пользователю отображаются все доступные товары. Фильтр позволяет выполнить выборку товаров по нужным критериям. Всего доступно 4 критерия по которым можно отфильтровать товары:

1. Выбор проекта
2. Количество комнат
3. Площадь
4. Стоимость

<figure>
  {% include component/picture.html fancybox='true' slug=page.slug img='img-1.jpg' type='original' alt='Фильтр товаров' %}
  <figcaption>Всего доступно 4 критерия по которым можно отфильтровать товары.</figcaption>
</figure>

При выборке отображается количество найденных совпадений, с которыми пользователь может ознакомиться при нажатии на кнопку "Показать".

<h3>Список товаров</h3>

Отображение товаров выполнено в двух стилях, между которыми можно переключаться:

1. Плиткой
2. Таблицей

<figure>
  {% include component/picture.html fancybox='true' slug=page.slug img='img-2.jpg' type='original' alt='Товары в виде плитки' %}
  <figcaption>Плитка.</figcaption>
</figure>

<figure>
  {% include component/picture.html fancybox='true' slug=page.slug img='img-3.jpg' type='original' alt='Товары в виде таблицы' %}
  <figcaption>Таблица.</figcaption>
</figure>

<h3>Сортировка товаров</h3>

Для каждого стиля отображения товаров предусмотрена свое оформление сортировки.

<figure>
  {% include component/picture.html fancybox='true' slug=page.slug img='img-4.jpg' type='original' alt='Сортировка в виде выпадающего списка' %}
  <figcaption>Сортировка в виде выпадающего списка.</figcaption>
</figure>

<figure>
  {% include component/picture.html fancybox='true' slug=page.slug img='img-5.jpg' type='original' alt='Сортировка в виде заголовков таблицы' %}
  <figcaption>Сортировка в виде заголовков таблицы.</figcaption>
</figure>

<h3>Избранное</h3>

Каждый товар может быть добавлен в избранное нажатием по соответствующей иконке в виде сердца.

<figure>
  {% include component/picture.html fancybox='true' slug=page.slug img='img-6.jpg' type='original' alt='Избранные товары' %}
  <figcaption>Каждый товар может быть добавлен в избранное.</figcaption>
</figure>

Добавить товар в избранное можно как из главной страницы, так и через страницу самого товара.

<figure>
  {% include component/picture.html fancybox='true' slug=page.slug img='img-7.jpg' type='original' alt='Страница товара' %}
  <figcaption>Так и через страницу самого товара.</figcaption>
</figure>

Все избранные товары отображаются в отдельной странице "Избранное".

<figure>
  {% include component/picture.html fancybox='true' slug=page.slug img='img-8.jpg' type='original' alt='Избранные товары' %}
  <figcaption>Все избранные товары отображаются в отдельной странице "Избранное".</figcaption>
</figure>

Так же избранные товары могут быть убраны из страницы "Избранное", повторным нажатием на иконку сердца.

<h3>Страница товара</h3>

Имеется возможность ознакомиться с понравившимся товаром подробнее перейдя на его отдельную страницу.

<figure>
  {% include component/picture.html fancybox='true' slug=page.slug img='img-9.jpg' type='original' alt='Страница товара' %}
  <figcaption>Страница товара.</figcaption>
</figure>

<h3>Бронь товара</h3>

Там же на странице товара имеется возможность его забронировать, нажав по кнопке **"Забронировать"** и заполнив форму.

<figure>
  {% include component/picture.html fancybox='true' slug=page.slug img='img-10.jpg' type='original' alt='Бронь товара' %}
  <figcaption>Форма бронирования товара.</figcaption>
</figure>

<h3>Список заявок</h3>

Все отправленные заявки на бронь будут отображаться на отдельной странице **"Заявки"**.

<figure>
  {% include component/picture.html fancybox='true' slug=page.slug img='img-11.jpg' type='original' alt='Заявки' %}
  <figcaption>Страница заявок.</figcaption>
</figure>

На странице заявок предусмотрена пагинация.

<h2 id="total"><span class="attention">Итоги</span> проекта</h2>

Работа была выполнена в рамках **Пет проекта**.

<h3>Ссылки на проект</h3>

- [Исходный код](https://github.com/eliofery/javascript-framework-shop){:target="_blank" rel="nofollow"}
- [Страница проекта](https://eliofery.github.io/javascript-framework-shop){:target="_blank" rel="nofollow"}
