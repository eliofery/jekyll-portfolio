---
layout: post
title: Базовая концепция одномерных массивов, решения типовых задач
date: 2023-09-08 09:16 +0600
description: 'Рассмотрим решения базовых задач на манипуляцию с массивом данных.'
image: cover.jpg
alt: Базовая концепция одномерных массивов
category: dsa
tags: [циклы, массивы]
source:
published: true
sitemap: true
excerpt_separator: "<!--more-->"
---

{{ page.description }}

<!--more-->

## <span class="attention">Оглавление</span> статьи

1. [Вводная часть](#intro)
2. [Повторения в массиве](#count)
3. [Поиск числа с большим и меньшим повторением](#min-max)

<h2 id="intro"><span class="attention">Вводная</span> часть</h2>

Массивы - это фундаментальная структура данных в программировании, которая позволяет хранить и организовывать множество элементов одного типа в одной переменной. В этой статье мы рассмотрим решения базовых задач с массивом данных.

<h2 id="count"><span class="attention">Повторения</span> в массиве</h2>

Рассмотрим решение задачи на подсчет повторяющихся элементов в массиве.

**Например:** [10,5,10,15,10,5] => [[10,3],[5,2],[15,1]]

{% capture code %}
const arr = [10,5,10,15,10,5]

// Число/повторы: [[10,3],[5,2],[15,1]]
console.log('Число/повторы:', countFreq(arr))

function countFreq(arr) {
  const res = []
  let visited = []

  for (let i = 0; i < arr.length; i++) {
    if (visited[i] == true) continue

    let count = 0
    for (let j = i; j < arr.length; j++) {
      if (arr[i] == arr[j]) {
        visited[j] = true
        count++
      }
    }

    res.push([arr[i], count])
  }

  return res
}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="min-max"><span class="attention">Поиск</span> числа с большим и меньшим повторением</h2>

Рассмотрим решение задачи на нахождение числа с большим и меньшим повторением в массиве.

**Например:** [10,5,10,15,10,5] => [10, 15]

{% capture code %}
let arr = [10, 5, 10, 15, 10, 5]

// Больше/меньше повторений: [10, 15]
console.log('Больше/меньше повторений:', countFreq(arr))

function countFreq(arr) {
  let visited = []
  let maxFreq = 0
  let minFreq = arr.length
  let maxEle = 0
  let minEle = 0

  for (let i = 0; i < arr.length; i++) {

    if (visited[i] == true) continue

    let count = 0
    for (let j = i; j < arr.length; j++) {
      if (arr[i] == arr[j]) {
        visited[j] = true;
        count++;
      }
    }

    if (count > maxFreq) {
      maxEle = arr[i];
      maxFreq = count;
    }

    if (count < minFreq) {
      minEle = arr[i];
      minFreq = count;
    }
  }

  return [maxEle, minEle]
}
{% endcapture %}
{% include component/code.html lang='js' content=code %}
