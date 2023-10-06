---
layout: post
title: Математические манипуляции с числами
date: 2023-09-04 09:16 +0600
description: 'Рассмотрим базовые математические манипуляции с числами: подсчет цифр в числе, обратная перестановка, палиндром и многое другое.'
image: cover.jpg
alt: Математические манипуляции с числами
category: dsa
tags: [циклы, математика]
source:
published: true
sitemap: true
excerpt_separator: "<!--more-->"
---

{{ page.description }}

<!--more-->

## <span class="attention">Оглавление</span> статьи

1. [Вводная часть](#intro)
2. [Извлечение цифр](#digit)
3. [Число в обратном порядке](#reverse-order)
4. [Количество цифр](#count-digit)
5. [Палиндром](#palindrom)
6. [Число Армстронга](#armstrong)
7. [Делители числа](#division)
8. [Простое число](#prime)

<h2 id="intro"><span class="attention">Вводная</span> часть</h2>

В этой статье представлены основные математические понятия используемые в программировании.

<h2 id="digit"><span class="attention">Извлечение</span> цифр</h2>

Деление по модулю любого числа на десять способствует извлечению чисел. Предположим у нас есть некое число, например **7789**. Если поделить его по модулю на десять **7789 % 10** результатом операции станет число **9**. Так как деление по модулю отдает остаток от деления.

**7780 % 10 = 0 остаток\
(7780 + 9) % 10 = 9 остаток**

Чтобы получить следующее число после **9**, нужно сократить число **7789** до **778** и повторить деление по модулю **10**. Сократить число **7789** до **778** можно поделив **7789** на **10** и откинув дробную часть.

**7789 / 10 = 778,9 => 778**

Таким образом если продолжить данную манипуляции с делением по модулю и с обычным делением, то можно извлечь все цифры, содержащиеся в числе.

Пример кода с извлечением всех цифр из числа.

{% capture code %}
let n = 7789

let digit1 = n % 10 // 7789 % 10 = 9
let num1 = parseInt(n / 10) // 7789 / 10 = 778

let digit2 = num1 % 10 // 778 % 10 = 8
let num2 = parseInt(num1 / 10) // 778 / 10 = 77

let digit3 = num2 % 10 // 77 % 10 = 7
let num3 = parseInt(num2 / 10) // 77 / 10 = 7

let digit4 = num3 % 10 // 7 % 10 = 7
let num4 = parseInt(num3 / 10) // 7 / 10 = 0

// "Исходное число:" 7789
console.log('Исходное число:', n)

// "Извлеченные цифры:" "9 8 7 7"
console.log('Извлеченные цифры:', `${digit1} ${digit2} ${digit3} ${digit4}`)
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="reverse-order"><span class="attention">Число</span> в обратном порядке</h2>

Используя вышеуказанную математическую технику можно отобразить число в обратном порядке.

{% capture code %}
let n = 7789
let clone = n
let str = ''

while (n > 0) {
  str += n % 10
  n = parseInt(n / 10)
}

// "Исходное число:" 7789
console.log('Исходное число:', clone)

// "Перевернутое число" 9877
console.log('Перевернутое число', parseInt(`${str}`))
{% endcapture %}
{% include component/code.html lang='js' content=code %}

### Вариант 2

Первый вариант заключался в создании строки и последующем переводом ее в целое число. Второй вариант сразу приводит к числу без использования строки.

{% capture code %}
let n = 7789
let clone = n
let revNum = 0

while (n > 0) {
  revNum = (revNum * 10) + n % 10
  n = parseInt(n / 10)
}

// "Исходное число:" 7789
console.log('Исходное число:', clone)

// "Перевернутое число" 9877
console.log('Перевернутое число', revNum)
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="count-digit"><span class="attention">Количество</span> цифр</h2>

Используя вышеуказанную математическую технику можно подсчитать количество цифр в числе.

{% capture code %}
let n = 7789
let count = 0

while ( n > 0) {
  n = parseInt(n / 10)
  count++
}

// "Количество цифр:" 4
console.log('Количество цифр:', count)
{% endcapture %}
{% include component/code.html lang='js' content=code %}

### Вариант 2

Так же получить количество цифр в числе можно через операцию логарифм от числа по основанию 10.

{% capture code %}
let n = 7789
let count = Math.ceil(Math.log10(n))

console.log('Количество цифр:', count)
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="palindrom"><span class="attention">Палиндром</span> число</h2>

Рассмотрим вариант определения является число палиндромом или нет используя всю туже математическую технику. Число является палиндромом если оно остается таким же как исходное после переворачивания в обратном порядке.

{% capture code %}
let n = 121
let clone = n
let newNum = 0

while (n > 0) {
  newNum = (newNum * 10) + n % 10
  n = parseInt(n / 10)
}

console.log('Исходное число:', clone)
console.log('Перевернутое число:', newNum)
console.log(clone === newNum ? 'Палиндром' : 'Не палиндром')
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="armstrong"><span class="attention">Число</span> Армстронга</h2>

Определим является ли исходное число числом Армстронга. Числом Армстронга является такое число в котором сумма его цифр возведенная в степень **3** равна исходному числу.

**Например:** 371 => 3^3 + 7^3 + 1^3 = 371

{% capture code %}
let n = 371 // 3^3 + 7^3 + 1^3 = 371
let clone = n
let newNum = 0

while (n > 0) {
  let res = n % 10
  newNum += Math.pow(res, 3)
  n = parseInt(n / 10)
}

console.log(newNum === clone ? 'Число Армстронга' : 'Не число Армстронга')
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="division"><span class="attention">Делители</span> числа</h2>

Определим все делители исходного числа на которые исходное число делится без остатка.

**Например:** 36 делится без остатка на 1 2 3 4 6 9 12 18 36

{% capture code %}
let n = 36 // 1 2 3 4 6 9 12 18 36
let list = []

for (let i = 1; i <= n; i++) {
  if (n % i === 0) {
    list.push(i)
  }
}

// "Делится без остатка на:" "1 2 3 4 6 9 12 18 36"
console.log('Делится без остатка на:', list.join(' '))
{% endcapture %}
{% include component/code.html lang='js' content=code %}

### Вариант 2

Во втором варианте сокращается проход по числам в два раза. Если поделить пополам все числа на которые делится число 36 без остатка, то получится **1 2 3 4 6** вместо **1 2 3 4 6 9 12 18 36**. Так как числа **9 12 18 36** можно получить при делении числа **36** на **1 2 3 4 6**.

**36 / 1 = 36\
36 / 2 = 18\
36 / 3 = 12\
36 / 4 = 9\
36 / 6 = 6**

{% capture code %}
let n = 36 // 1 2 3 4 6 9 12 18 36
let list = []

for (let i = 1; i <= Math.sqrt(n); i++) {
  if (n % i === 0) {
    list.push(i)

    if (n / i !== i) {
      list.push(n / i)
    }
  }
}

list.sort((a, b) => a - b)

// "Делится без остатка на:" "1 2 3 4 6 9 12 18 36"
console.log('Делится без остатка на:', list.join(' '))
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="prime"><span class="attention">Простое</span> число</h2>

Определим является ли исходное число простым. Простое число это такое число которое имеет только два делителя 1 и само исходное число.

{% capture code %}
let n = 11
let count = 0

for (let i = 1; i <= Math.sqrt(n); i++) {
  if (n % i === 0) {
    count++

    if (n / i !== i) count++
  }
}

// Простое число
console.log(count === 2 ? 'Простое число' : 'Не простое число')
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="gcd"><span class="attention">Наибольший</span> общий множитель или делитель</h2>

Решение задачи при которой необходимо найти для двух чисел один наибольший общий множитель или делитель, например:

**Число 9:**  1\*, 3\*, 9
**Число 12:** 1\*, 2, 3\*, 4, 6, 12

Общие делители отмечены звездочкой, наибольший из них это цифра 3.

{% capture code %}
let a = 9  // 1*, 3*, 9
let b = 12 // 1*, 2, 3*, 4, 6, 12
let res = 1

for (let i = Math.min(a,b); i > 0 ; i--) {
  if (a % i === 0 && b % i === 0) {
    res = i
    break
  }
}

// Общий делитель: 3
console.log('Общий делитель:' + res)
{% endcapture %}
{% include component/code.html lang='js' content=code %}

### Вариант 2

Решение с помощью алгоритма Евклида.

{% capture code %}
let a = 9  // 1*, 3*, 9
let b = 12 // 1*, 2, 3*, 4, 6, 12
let res = 1

while (a > 0 && b > 0) {
  if (a > b) a = a % b
  else b = b % a
}

if (a === 0) res = b
else res = a

// Общий делитель:3
console.log('Общий делитель:' + res)
{% endcapture %}
{% include component/code.html lang='js' content=code %}
