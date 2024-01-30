---
layout: portfolio
title: Chix удобная обертка над Chi роутером с доступом к данным через контекст
date: 2024-01-30 07:00 +0600
description: В рамках этой работы была реализована обертка над <b>Chi</b> роутером, которая позволяет получать данный через контекст как во всех современных фреймворках на языке <b>Golang</b>.
image: cover.jpg
alt: Chix
work-year: 2024
roles: [backend]
tags: [golang, chi]
source: https://github.com/eliofery/go-chix
published: true
sitemap: false
excerpt_separator: "<!--more-->"
---

{{ page.description }}

<!--more-->

## <span class="attention">Оглавление</span> статьи

1. [Описание проекта](#intro)
2. [Итоги проекта](#total)

<h2 id="intro"><span class="attention">Описание</span> проекта</h2>

Основная работа над проектом заключалась в создании обертки вокруг готового роутера **Chi**, которая бы расширила его стандартный функционал по примеру как это сделано во всех современных фреймворках на языке <b>Golang</b>.

**Приведу пример:**

Стандартный обработчик **Chi** роутера выглядит следующим образом:

{% capture code %}
func Handler(w http.ResponseWrite, r *http.Request) {
  // w
  // r
}
{% endcapture %}
{% include component/code.html lang='go ' content=code %}

Обработчик **Chix** роутера выглядит следующим образом:

{% capture code %}
func Handler(ctx *chix.Ctx) {
  // ctx.ResponseWriter
  // ctx.Request
}
{% endcapture %}
{% include component/code.html lang='go ' content=code %}

В остальном все остается привычным как при использовании **Chi**.

<h2 id="total"><span class="attention">Итоги</span> проекта</h2>

Работа была выполнена в рамках **Пет проекта**.

**Chix** позволяет получить доступ к контексту в котором можем хранить неограниченное количество различных данных, а не только запрос, ответ.

<h3>Ссылки на проект</h3>

- [Страница проекта](https://github.com/eliofery/go-chix){:target="_blank" rel="nofollow"}
