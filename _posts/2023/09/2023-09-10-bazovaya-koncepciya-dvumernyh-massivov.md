---
layout: post
title: Базовая концепция двумерных массивов, решения типовых задач
date: 2023-09-10 09:16 +0600
description: 'Рассмотрим решения базовых задач на манипуляцию с массивом данных.'
image: cover.jpg
alt: Базовая концепция двумерных массивов
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
2. [Переписать матрицу нулями](#matrix-zero)
3. [Перевернутая матрица](#reverse-matrix)
4. [Повернутая матрица на 90 градусов](#rotate-90)
5. [Элементы матрицы по спирали](#spiral-matrix)

<h2 id="intro"><span class="attention">Вводная</span> часть</h2>

Массивы - это фундаментальная структура данных в программировании, которая позволяет хранить и организовывать множество элементов одного типа в одной переменной. В этой статье мы рассмотрим решения базовых задач с массивом данных.

<h2 id="matrix-zero"><span class="attention">Переписать</span> матрицу нулями</h2>

Рассмотрим решение задачи на замену 1 на 0 в столбцах и строках пересекающих 0.

**Например:**\
[\
  [1,1,1,1],\
  [1,0,1,1],\
  [1,1,0,1],\
  [1,0,0,1]\
]\
=>\
[\
  [1,0,0,1],\
  [0,0,0,0],\
  [0,0,0,0],\
  [0,0,0,0]\
]

{% capture code %}
let matrix = [
  [1,1,1,1],
  [1,0,1,1],
  [1,1,0,1],
  [1,0,0,1],
]
let col = Array(matrix.length).fill(0)
let row = Array(matrix[0].length).fill(0)

for (let i = 0; i < row.length; i++) {
  for (let j = 0; j < col.length; j++) {
    if (matrix[i][j] === 0) {
      row[i] = 1
      col[j] = 1
    }
  }
}

for (let i = 0; i < row.length; i++) {
  for (let j = 0; j < col.length; j++) {
    if (row[i] || col[j]) {
      matrix[i][j] = 0
    }
  }
}

/*
Новая матрица:
[\
[1,0,0,1],
[0,0,0,0],
[0,0,0,0],
[0,0,0,0]
]
*/
console.log('Новая матрица:', matrix)
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="reverse-matrix"><span class="attention">Перевернутая</span> матрица</h2>

Рассмотрим решение задачи на переворачивание матрицы.

**Например:**\
[\
[1,2,3,4],\
[5,6,7,8],\
[9,10,11,12],\
[13,14,15,16],\
]\
=>\
[\
[13,14,15,16],\
[9,10,11,12],\
[5,6,7,8],\
[1,2,3,4]\
]

{% capture code %}
const arr = [
  [1,2,3,4],
  [5,6,7,8],
  [9,10,11,12],
  [13,14,15,16],
]
const res = []

for (let i = 0; i < arr.length; i++) {
  res[i] = Array(arr.length).fill(0)

  for (let j = 0; j < arr.length; j++) {
    res[i][j] = arr[arr.length - 1 - i][j]
  }
}

/*
Перевернутая матрица:
[
[13,14,15,16],
[9,10,11,12],
[5,6,7,8],
[1,2,3,4]
]
*/
console.log('Перевернутая матрица:', res)
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="rotate-90"><span class="attention">Повернутая</span> матрица на 90 градусов</h2>

Рассмотрим решение задачи на поворачивание матрицы на 90 градусов.

**Например:**\
[\
[1,2,3,4],\
[5,6,7,8],\
[9,10,11,12],\
[13,14,15,16],\
]\
=>\
[\
[13,9,5,1],\
[14,10,6,2],\
[15,11,7,3],\
[16,12,8,4]\
]

{% capture code %}
const arr = [
  [1,2,3,4],
  [5,6,7,8],
  [9,10,11,12],
  [13,14,15,16],
]

const res = JSON.parse(JSON.stringify(arr))

for (let i = 0; i < arr.length; i++) {
  for (let j = 0; j < arr.length; j++) {
    res[j][i] = arr[arr.length - 1 - i][j]
  }
}

/*
Повернутая матрица на 90 градусов:
[
[13,9,5,1],
[14,10,6,2],
[15,11,7,3],
[16,12,8,4]
]
*/
console.log('Повернутая матрица на 90 градусов:', res)
{% endcapture %}
{% include component/code.html lang='js' content=code %}

### Вариант 2

{% capture code %}
const arr = [
  [1,2,3,4],
  [5,6,7,8],
  [9,10,11,12],
  [13,14,15,16],
]

for (let i = 0; i < arr.length; i++) {
  for (let j = i + 1; j < arr.length; j++) {
    const tmp = arr[i][j]
    arr[i][j] = arr[j][i]
    arr[j][i] = tmp
  }
}

let p1 = 0
let p2 = arr.length - 1

while (p1 < p2) {
  const tmp = arr[p1]
  arr[p1] = arr[p2]
  arr[p2] = tmp

  p1++
  p2--
}

/*
Повернутая матрица на 90 градусов:
[
[4,8,12,16],
[3,7,11,15],
[2,6,10,14],
[1,5,9,13]
]
*/
console.log('Повернутая матрица на 90 градусов:', arr)
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="spiral-matrix"><span class="attention">Элементы</span> матрицы по спирали</h2>

Рассмотрим решение задачи на разворачивание элементов матрицы по спирали.

**Например:**\
[\
[1,2,3,4,5,6],\
[20,21,22,23,24,7],\
[19,32,33,34,25,8],\
[18,31,36,35,26,9],\
[17,30,29,28,27,10],\
[16,15,14,13,12,11],\
]\
=>\
[1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36]

{% capture code %}
const arr = [
  [1,2,3,4,5,6],
  [20,21,22,23,24,7],
  [19,32,33,34,25,8],
  [18,31,36,35,26,9],
  [17,30,29,28,27,10],
  [16,15,14,13,12,11],
]

let n = arr.length // row
let m = arr[0].length // col

let left = 0
let right = m - 1

let top = 0
let bottom = n - 1

const res = []

while(top <= bottom && left <= right) {
  // right
  for (let i = left; i <= right; i++) {
    res.push(arr[top][i])
  }

  top++

  // bottom
  for (let i = top; i <= bottom; i++) {
    res.push(arr[i][right])
  }

  right--

  // left
  for (let i = right; i >= left; i--) {
    res.push(arr[bottom][i])
  }

  bottom--

  // top
  for (let i = bottom; i >= top; i--) {
    res.push(arr[i][left])
  }

  left++
}

// Разворачивание элементов матрицы: [1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36]
console.log('Разворачивание элементов матрицы:', res)
{% endcapture %}
{% include component/code.html lang='js' content=code %}
