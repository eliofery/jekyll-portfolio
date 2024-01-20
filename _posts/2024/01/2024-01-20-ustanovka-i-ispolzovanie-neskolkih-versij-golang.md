---
layout: post
title: Установка и использование нескольких версий Golang
date: 2024-01-20 11:44 +0600
description: В этой статье установим различные версии языка программирования <b>Golang</b> для дальнейшего их использования.
image: cover.jpg
alt: Основное изображение
category: backend
tags: [golang, пакеты]
source:
published: true
sitemap: true
excerpt_separator: "<!--more-->"
---

{{ page.description }}

<!--more-->

## <span class="attention">Оглавление</span> статьи

1. [Вводная часть](#intro)
2. [Скачивание Golang](#download)
3. [Переменные среды](#path)
4. [Подведем итоги](#end)

<h2 id="intro"><span class="attention">Вводная</span> часть</h2>

На официальном сайте **Golang** предоставлена наглядная пошаговая [инструкция по установке](https://go.dev/doc/install){:target="_blank"} этого замечательного языка программирования. Здесь же поделюсь своим немного видоизмененным вариантом.

<h2 id="download"><span class="attention">Скачивание</span> Golang</h2>

Перед скачиванием нужной версии, создадим каталог в котором будут храниться различные версии **Golang**. Путь до каталога не принципиален, можно выбрать абсолютно любой какой вам удобен и привычен. Я обычно храню все версии **Golang** и его пакеты в каталоге **golang** который размещаю в **Домашней папке (/home/&lt;user&gt;/golang)**:

{% capture code %}
mkdir ~/golang
cd ~/golang
{% endcapture %}
{% include component/code.html lang='bash ' content=code %}

Далее переходим на страницу [официального сайта](https://go.dev/dl/){:target="_blank"}, с перечнем доступных версий **Golang** и скачиваем нужный нам архив.

Распаковываем скаченный архив в созданный каталог, у меня это **~/golang** и переименовываем его так, чтобы было понятно какая это версия, например **go-1.21.4**. И того должно получиться **~/golang/go1.21.4**.

Предположим, что вышла новая версия **Golang 1.21.5**. Так же скачиваем архив, с данной версией на официальном сайте и распаковываем ее в созданный каталог **~/golang/go1.21.5**. И того должна получиться следующая структура:

{% capture code %}
~/golang
├── go-1.21.4
├── go-1.21.5
{% endcapture %}
{% include component/code.html lang='bash ' content=code %}

Аналогичным образом в дальнейшем добавляем другие версии.

<h2 id="path"><span class="attention">Переменные</span> среды</h2>

### GOROOT

**GOROOT** - хранит путь до используемой версии **Golang**.

Сейчас если вести в терминале команду:

{% capture code %}
go version
{% endcapture %}
{% include component/code.html lang='bash ' content=code %}

Мы получим ошибку **go: команда не найдена**. Так как переменные среды не были настроены и терминал не знает где ему искать исполняемый файл **go**. Чтобы это исправить откроем в привычном вам редакторе файл **~/.profile** и добавим в него следующую конструкцию:

{% capture code %}
# golang
export GOROOT=~/golang/go1.21.5
export PATH=$PATH:$GOROOT/bin
{% endcapture %}
{% include component/code.html lang='.profile ' content=code %}

Далее мы расширяем переменную сред **PATH** добавив к ее значениям **$GOROOT/bin**, ссылающуюся на исполняемый файл **Golang**.

Перезапустим терминал либо введем команду, чтобы изменения вступили в силу:

{% capture code %}
source ~/.profile
{% endcapture %}
{% include component/code.html lang='bash ' content=code %}

Теперь если ввести в терминале команду:

{% capture code %}
go version
{% endcapture %}
{% include component/code.html lang='bash ' content=code %}

Вместо ошибки увидим текущую версию **go version go1.21.5 linux/amd64**.

### GOPATH

**GOPATH** - хранит путь до сторонних пакетов.

{% capture code %}
go get &lt;package&gt;
go install &lt;package&gt;
{% endcapture %}
{% include component/code.html lang='bash ' content=code %}

Необходимо явно указать каталог куда сторонние пакеты будут скачены. Для этого создадим каталог **~/golang/packages** рядом с версиями **Golang**.

{% capture code %}
mkdir ~/golang/packages
{% endcapture %}
{% include component/code.html lang='bash ' content=code %}

Остается добавить, созданный каталог в переменные среды по аналогии как это было сделано с **GOROOT** и того должно получиться:

{% capture code %}
# golang
export GOROOT=~/golang/go1.21.5
export GOPATH=~/golang/packages
export PATH=$PATH:$GOROOT/bin:$GOPATH/bin
{% endcapture %}
{% include component/code.html lang='.profile ' content=code %}

Перезапустим терминал либо введем команду, чтобы изменения вступили в силу:

{% capture code %}
source ~/.profile
{% endcapture %}
{% include component/code.html lang='bash ' content=code %}

<h2 id="end"><span class="attention">Подведем</span> итоги</h2>

В этой статье мы научились устанавливать и использовать различные версии **Golang**. При использовании **IDE GoLand** можно с легкостью переключаться между версиями, а так же добавлять новые, скачивая их через раздел настроек **GoLand**.
