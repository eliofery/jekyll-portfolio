---
layout: post
title: Базовая концепция рекурсии, решения типовых задач
date: 2023-09-06 09:16 +0600
description: 'Рассмотрим решения базовых, типовых задач на рекурсию.'
image: cover.jpg
alt: Базовая концепция рекурсии
category: dsa
tags: [циклы, рекурсия]
source:
published: true
sitemap: true
excerpt_separator: "<!--more-->"
---

{{ page.description }}

<!--more-->

## <span class="attention">Оглавление</span> статьи

1. [Вводная часть](#intro)
2. [Сумма всех чисел](#sum)
3. [Факториал числа](#fact)
4. [Обратный порядок массива](#array-reverse)
5. [Палиндром строки](#palindrom)
6. [Число Фибоначчи](#fibonacci)

<h2 id="intro"><span class="attention">Вводная</span> часть</h2>

Рекурсия - позволяет функциям вызывать сами себя в своем исполнении. Этот метод решения задач и алгоритмов не только эффективен, но и обладает уникальной красотой в своей структуре. В этой статье мы рассмотрим, что такое рекурсия, как она работает и как ее можно применить для решения базовых задач в программировании.

<h2 id="sum"><span class="attention">Сумма</span> всех чисел</h2>

Решение задачи на сумму чисел заданного числа.

**Например:** 4 => 1 + 2 + 3 + 4 = 10

{% capture code %}
const num = 4

// Сумма всех чисел: 10
console.log('Сумма всех чисел:', sum(num))

function sum(num) {
  if (num < 1) {
     return num
  }

  return num + sum(num - 1)
}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="fact"><span class="attention">Факториал</span> числа</h2>

Решение задачи на нахождение факториала числа.

**Например:** 4 => 1 * 2 * 3 * 4 = 24

{% capture code %}
const num = 4

// Факториал числа: 24
console.log('Факториал числа:', fact(num))

function fact(num) {
  if (num === 1) {
     return 1
  }

  return num * fact(num - 1)
}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="array-reverse"><span class="attention">Обратный</span> порядок массива</h2>

Решение задачи на переворачивание массива в обратный порядок.

**Например:** [2,5,1,3,6,4] => [4,6,3,1,5,2]

{% capture code %}
const arr = [2,5,1,3,6,4]

// Исходный массив: [2,5,1,3,6,4]
console.log('Исходный массив:', arr)

reverse(0, arr)

// Перевернутый массив: [4,6,3,1,5,2]
console.log('Перевернутый массив', arr)

function reverse(i, arr) {
  const length = arr.length

  if (i >= Math.floor(length / 2)) {
    return
  }

  const tmp = arr[i]
  arr[i] = arr[length - i - 1]
  arr[length - i - 1] = tmp

  reverse(i + 1, arr)
}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="palindrom"><span class="attention">Палиндром</span> строки</h2>

Решение задачи на определение является ли строка палиндромом.

**Например:** madam => madam с лево на право и справа на лево читается одинаково

{% capture code %}
let str = 'madam'

// Исходная строка: madam
console.log('Исходная строка:', str)

let newStr = reverse(0, str)

// Исходная строка: madam
console.log('Перевернутая строка:', newStr)

// Палиндром
console.log(str === newStr ? 'Палиндром' : 'Не палиндром')

function reverse(i, str) {
  const length = str.length

  if (i >= length / 2) {
    return str
  }

  const newStr = str.split('')

  const tmp = newStr[i]
  newStr[i] = newStr[length - 1 - i]
  newStr[length - 1 - i] = newStr[i]

  return reverse(i + 1, newStr.join(''))
}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="fibonacci"><span class="attention">Число</span> Фибоначчи</h2>

Решение задачи на нахождение числа Фибоначчи. Числовая последовательность, первые два числа которой являются 0 и 1, а каждое последующее за ними число является суммой двух предыдущих.

**Например:** 10 => 1 + 1 + 2 + 3 + 5 + 8 + 13 + 21 + 34 + 55

{% capture code %}
let num = 10

// Исходное число: 10
console.log('Исходное число:', num) // 10

num = fibonacci(num)

// Число Фибоначчи: 55
console.log('Число Фибоначчи:', num) // 55

function fibonacci(num) {
  if (num <= 1) {
    return num
  }

  return fibonacci(num - 1) + fibonacci(num - 2)
}
{% endcapture %}
{% include component/code.html lang='js' content=code %}
