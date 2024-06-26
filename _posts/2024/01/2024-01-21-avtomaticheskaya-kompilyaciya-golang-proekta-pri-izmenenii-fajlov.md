---
layout: post
title: Автоматическая компиляция Golang проекта при изменении файлов
date: 2024-01-21 07:00 +0600
description: В этой статье настроим слежение за изменением файлов. При котором будет автоматически перекомпилироваться <b>Golang</b> проект.
image: cover.jpg
alt: Основное изображение
category: backend
tags: [golang, modd]
published: true
sitemap: true
excerpt_separator: "<!--more-->"
---

{{ page.description }}

<!--more-->

## <span class="attention">Оглавление</span> статьи

1. [Вводная часть](#intro)
2. [Установка modd](#download)
3. [Настройка modd](#setting)
4. [Запуск modd](#run)
5. [Подведем итоги](#end)

<h2 id="intro"><span class="attention">Вводная</span> часть</h2>

**Golang** является компилируемым языком программирования. Вследствие чего каждое изменение кода необходимо каждый раз заново компилировать, чтобы увидеть изменения в программе.

Данная манипуляция может утомить если приходится раз за разом перекомпилировать проект. В данной статье мы рассмотрим как можно упростить этот момент в разработке.

<h2 id="download"><span class="attention">Установка</span> modd</h2>

Для решения нашей задачи необходимо установить [пакет modd](https://github.com/cortesi/modd){:target="_blank" rel="nofollow"}. Для этого в терминале введем команду:

{% capture code %}
go install github.com/cortesi/modd/cmd/modd@latest
{% endcapture %}
{% include component/code.html lang='bash ' content=code %}

Данный пакет будет скачен в каталог **~/golang/packages/bin**, который мы настроили в [прошлой статье]({{ site.baseurl }}/blog/2024-01-20-ustanovka-i-ispolzovanie-neskolkih-versij-golang-backend.html){:target="_blank"}.

<h2 id="setting"><span class="attention">Настройка</span> modd</h2>

В корне проекта создадим файл **modd.conf** со следующим содержимым:

{% capture code %}
# Проверка всех тестов при первой запуске
**/*.go {
  prep: go test @dirmods
}

# Автоматическая компиляция проекта при изменении файлов
# Исключает все тестовые файлы *_test.go
**/*.go !**/*_test.go {
  prep: go build -o ./bin ./cmd/main.go
  daemon +sigterm: ./bin
}
{% endcapture %}
{% include component/code.html lang='modd.conf ' content=code %}

При первом запуске **modd** пакета будут проверены все имеющиеся тесты. Далее уже идет непосредственно настройки слежения за изменением файлов.

В данном случае мы следим за изменением всех файлов с расширением **.go** кроме тестовых. При изменении содержимого какого либо **go** файла будет происходить перекомпиляция всего проекта.

Строкой **prep: go build -o ./bin ./cmd/main.go** мы говорим, чтобы компилировался файл **./cmd/main.go**, а результат его компиляции был помещен в каталоге **./bin**.

Строкой **daemon +sigterm: ./bin** мы автоматически запускаем скомпилированный файл, в результате чего отобразится актуальный результат.

<h2 id="run"><span class="attention">Запуск</span> modd</h2>

Чтобы запустить режим слежения за изменением файлов, достаточно ввести в терминале команду:

{% capture code %}
modd
{% endcapture %}
{% include component/code.html lang='bash ' content=code %}

Иногда могут возникнуть случаи при которых вы будете ожидать перекомпиляцию, но она не произойдет. Например, при какой-либо серьезной ошибки в программе. Чтобы решить этот нюанс, необходимо отменить слежение за изменение файлов, нажав в терминале горячие клавиши <kbd>Ctrl + C</kbd> и по новой выполнить команду **modd**.

<h2 id="end"><span class="attention">Подведем</span> итоги</h2>

В этой статье мы научились пользоваться довольно полезным инструментом **modd**, который позволяет автоматизировать рутину компиляции программы при разработке.
