---
layout: post
title: Решение задачек через бинарный поиск
date: 2023-09-14 09:16 +0600
description: 'Рассмотрим решение типовых задачек на бинарный поиск.'
image: cover.jpg
alt: Решение задачек через бинарный поиск
category: dsa
tags: [циклы, массивы, бинарный поиск]
source:
published: true
sitemap: true
excerpt_separator: "<!--more-->"
---

{{ page.description }}

<!--more-->

## <span class="attention">Оглавление</span> статьи

1. [Вводная часть](#intro)
2. [Найти элемент в отсортированном массиве](#search-el)
3. [Нахождение нижней границы](#low-bound)
4. [Нахождение верхней границы](#high-bound)
5. [Поиск позиции для вставки](#insert-position)
6. [Поиск нижней и верхней позиции](#floor-ceil)
7. [Поиск количества заданного элемента](#count-el)
8. [Поиск элемента в повернутом массиве](#rotate-arr)
9. [Поиск минимального элемента в повернутом массиве](#rotate-min-el)
10. [Найти пиковый элемент](#find-peak)
11. [Найти ближайшее число к квадрату](#find-square)
12. [Поиск степени числа](#find-root)
13. [Коко ест бананы](#koko)
14. [Минимальное количество дней для изготовления букетов](#bouquets)
15. [Найти наименьший делитель](#smallest)


<h2 id="intro"><span class="attention">Вводная</span> часть</h2>

Бинарный поиск — это эффективный алгоритм поиска, который широко применяется в информатике и программировании. В основе этого метода лежит идея последовательного деления данных пополам, что позволяет быстро находить нужный элемент в упорядоченном массиве или списке.

<h2 id="search-el"><span class="attention">Найти</span> элемент в отсортированном массиве</h2>

Рассмотрим решение задачи на поиск элемента в отсортированном массиве.

**Например:** [3,4,6,7,9,12,16,17] 7 => 3

{% capture code %}
const arr = [3,4,6,7,9,12,16,17]
const target = 7
let low = 0
let high = arr.length - 1
let pos = -1

while (low <= high) {
  const mid = Math.floor((low + high) / 2)

  if (arr[mid] === target) {
    pos = mid
    break
  } else if (arr[mid] > target) {
    high = mid - 1
  } else {
    low = mid + 1
  }
}

// Позиция искомого элемента: 3
console.log('Позиция искомого элемента:', pos)
{% endcapture %}
{% include component/code.html lang='js' content=code %}

### Вариант 2

Через рекурсию.

{% capture code %}
const arr = [3,4,6,7,9,12,16,17]
const target = 16

// Позиция искомого элемента: 6
console.log('Позиция искомого элемента:', binary(arr, target))

function binary(arr, target) {
  const search = (arr, low, high) => {
    const mid = Math.floor((low + high) / 2)

    if (low > high) {
      return -1
    }

    if (arr[mid] === target) {
      return mid
    }

    if (arr[mid] > target) {
      high = mid - 1
    } else {
      low = mid + 1
    }

    return search(arr, low, high)
  }

  return search(arr, 0, arr.length - 1)
}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="low-bound"><span class="attention">Нахождение</span> нижней границы</h2>

Рассмотрим решение задачи на поиск нижней границы в отсортированном массиве.

**Например:** [3,5,5,5,5,8,8,15,19,19] 8 => 5 позиция

{% capture code %}
const arr = [3,5,5,5,5,8,8,15,19,19]
const target = 8
let low = 0
let high = arr.length - 1

while (low <= high) {
  const mid = Math.floor((low + high) / 2)

  if (arr[mid] >= target) {
    high = mid - 1
  } else {
    low = mid + 1
  }
}

// Нижняя граница: 5
console.log('Нижняя граница:', low)
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="high-bound"><span class="attention">Нахождение</span> верхней границы</h2>

Рассмотрим решение задачи на поиск верхней границы в отсортированном массиве.

**Например:** [3,5,5,5,5,8,8,15,19,19] => 5 позиция

{% capture code %}
const arr = [3,5,5,5,5,8,8,15,19,19]
const target = 8
let low = 0
let high = arr.length - 1

while (low <= high) {
  const mid = Math.floor((low + high) / 2)

  if (arr[mid] > target) {
    high = mid - 1
  } else {
    low = mid + 1
  }
}

// Верхняя граница: 6
console.log('Верхняя граница:', high)
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="insert-position"><span class="attention">Поиск</span> позиции для вставки</h2>

Рассмотрим решение задачи на поиск позиции где должен был бы располагаться не достающий элемент.

**Например:** [1,2,4,7] 6 => 3 позиция

{% capture code %}
const arr = [1,2,4,7]
const target = 6
let low = 0
let high = arr.length - 1

while (low <= high) {
  const mid = Math.floor((low + high) / 2)

  if (arr[mid] > target) {
    high = mid - 1
  } else {
    low = mid + 1
  }
}

// 'Пропущенная позиция: 3'
console.log('Пропущенная позиция:', low)
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="floor-ceil"><span class="attention">Поиск</span> нижней и верхней позиции</h2>

Рассмотрим решение задачи на поиск нижней и верхней позиции где должен был бы располагаться не достающий элемент.

**Например:** [10,20,30,40,50] 25 => [1, 2]

{% capture code %}
const arr = [10,20,30,40,50]
const target = 25

let low = 0
let high = arr.length - 1

let floor = -1
let ceil = -1

while (low <= high) {
  const mid = Math.floor((low + high) / 2)

  if (arr[mid] >= target) {
    high = mid - 1
    ceil = mid
  } else if (arr[mid] <= target) {
    low = mid + 1
    floor = mid
  }
}

// Позиция от / до
console.log('Позиция от / до:', [floor, ceil])
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="count-el"><span class="attention">Поиск</span> количества заданного элемента</h2>

Рассмотрим решение задачи на поиск количества заданного элемента в массиве.

**Например:** [2,4,6,8,8,8,8,8,1,1,13] 8 => 5

{% capture code %}
const arr = [2,4,6,8,8,8,8,8,1,1,13] // 1,1,2,4,6,8,8,8,8,13
const target = 8
let result = [-1, -1]

const start = first(arr, target)
if (start !== -1) result = [start, last(arr, target)]

// Количество элементов: 5
console.log('Количество элементов:', count(result))

function first(arr, target) {
  arr.sort((a,b) => a - b)

  let low = 0
  let high = arr.length - 1
  let result = -1

  while (low <= high) {
    const mid = Math.floor((low + high) / 2)

    if (arr[mid] >= target) {
      result = mid
      high = mid - 1
    } else {
      low = mid + 1
    }
  }

  return result
}

function last(arr, target) {
  arr.sort((a,b) => a - b)

  let low = 0
  let high = arr.length - 1
  let result = -1

  while (low <= high) {
    const mid = Math.floor((low + high) / 2)

    if (arr[mid] > target) {
      high = mid - 1
    } else {
      result = mid
      low = mid + 1
    }
  }

  return result
}

function count(result) {
  if (result[0] === -1) return 0

  return result[1] - result[0] + 1
}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="rotate-arr"><span class="attention">Поиск</span> элемента в повернутом массиве</h2>

Рассмотрим решение задачи на поиск позиции элемента в повернутом массиве.

**Например:** [7,8,9,1,2,3,4,5,6] 3 => 5

{% capture code %}
const arr = [7,8,9,1,2,3,4,5,6]
const target = 3
let low = 0
let high = arr.length - 1
let res = -1

while (low <= high) {
  const mid = Math.floor((low + high) / 2)

  if (arr[mid] === target) {
    res = mid
    break
  }

  if (arr[low] <= arr[mid]) {
    if (arr[low] <= target && target <= arr[mid]) {
      high = mid - 1
    } else {
      low = mid + 1
    }
  } else {
    if (arr[mid] <= target && target <= arr[high]) {
      low = mid + 1
    } else {
      high = mid - 1
    }
  }
}

// Позиция элемента: 5
console.log('Позиция элемента:', res)
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="rotate-min-el"><span class="attention">Поиск</span> минимального элемента в повернутом массиве</h2>

Рассмотрим решение задачи на поиск минимального элемента в повернутом массиве.

**Например:** [7,8,0,1,2,3,4,5,6] => 0

{% capture code %}
const arr = [7,8,0,1,2,3,4,5,6]
let low = 0
let high = arr.length - 1
let min = arr[0]

while (low <= high) {
  const mid = Math.floor((low + high) / 2)

  if (arr[low] <= arr[high]) {
    min = Math.min(min, arr[low])
    break
  }

  if (arr[mid] >= arr[low]) {
    min = Math.min(min, arr[low])
    low = mid + 1
  } else {
    min = Math.min(min, arr[mid])
    high = mid - 1
  }
}

// Минимальный элемент: 0
console.log('Минимальный элемент:', min)
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="find-peak"><span class="attention">Найти</span> пиковый элемент</h2>

Рассмотрим решение задачи на поиск позиции пикового элемента.

**Например:** [1,2,3,4,5,6,7,8,5,1] => 7

{% capture code %}
const arr = [1,2,3,4,5,6,7,8,5,1]

// Позиция пикового элемента: 7
console.log('Позиция пикового элемента:', find(arr))

function find(arr) {
  let n = arr.length

  if (n === 1) return 0
  if (arr[0] > arr[1]) return 0
  if (arr[n-1] > arr[n-2]) return n - 1

  let low = 1
  let high = n - 2

  while (low <= high) {
    let mid = Math.floor((low + high) / 2)

    if (arr[mid] > arr[mid - 1] && arr[mid] > arr[mid + 1]) {
      return mid
    } else if (arr[mid] > arr[mid - 1] ) {
      low = mid + 1
    } else {
      high = mid - 1
    }
  }

  return -1
}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="find-square"><span class="attention">Найти</span> ближайшее число к квадрату</h2>

Рассмотрим решение задачи на поиск ближайшего числа квадрат которого наиболее близок к заданному числу.

**Например:** 28 => 5 так как 5 * 5 = 25

{% capture code %}
const n = 28

let low = 1
let high = n

while (low <= high) {
  const mid = Math.floor((low + high) / 2)
  const val = mid * mid

  if (val <= n) {
    low = mid + 1
  } else {
    high = mid - 1
  }
}

// Ближайшее число: 5
console.log('Ближайшее число:', high)
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="find-root"><span class="attention">Поиск</span> числа степени</h2>

Рассмотрим решение задачи на поиск числа степени.

**Например:** X ^ 2 = 16 => 4 так как 4 ^ 2 = 16

{% capture code %}
const n = 2
const m = 16

// Число в степени 2 дает 16: 4
console.log('Число в степени 2 дает 16:', find(n, m))

function find(n, m) {
    let low = 1
    let high = m

    while (low <= high) {
        let mid = Math.floor((low + high) / 2)
        let pow = Math.pow(mid, n)

        if (pow === m) {
            return mid
        } else if (pow < m) {
            low = mid + 1
        } else {
            high = mid - 1
        }
    }

    return -1
}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="koko"><span class="attention">Коко</span> ест бананы</h2>

Рассмотрим решение задачи на поиск количества бананов которые может съесть обезьяна в час.

**Например:** [3,6,11,7] => 4 банана в час

{% capture code %}
const arr = [3,6,11,7] // количество бананов
const h = 8 // общее отведенное время на поедание бананов

// Количество бананов в час:
console.log('Количество бананов в час:', koko(arr, h))

function koko(arr, h) {
  let low = 1
  let high = Math.max(...arr)
  let mid = -1

  while(low <= high) {
    mid = Math.floor((low + high) / 2)
    const sum = calculate(arr, mid)

    if (sum > h) {
      low = mid + 1
    } else {
      high = mid - 1
    }
  }

  return mid
}

function calculate(arr, max) {
  let sum = 0

  for (let i = 0; i < arr.length; i++) {
    sum += Math.ceil(arr[i] / max)
  }

  return sum
}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="bouquets"><span class="attention">Минимальное</span> количество дней для изготовления букетов</h2>

Рассмотрим решение задачи на поиск минимального количества дней для изготовления букетов.

**Например:** [7,7,7,7,13,11,12,7] => на 12 день можно будет сделать 2 букета с 3 розами

{% capture code %}
const arr = [7,7,7,7,13,11,12,7] // день когда распускается цветок
const m = 2 // букетов
const k = 3 // роз в одном букете

// Букеты можем сделать в день: 12
console.log('Букеты можем сделать в день:', bouquets(arr, m, k))

function bouquets(arr, m, k) {
  let low = Math.min(...arr)
  let high = Math.max(...arr)

  while (low <= high) {
     const mid = Math.floor((low + high) / 2)
     let bouquet = count(mid)

     if (bouquet === m) {
       return mid
     } else if (bouquet < m) {
       low = mid + 1
     } else {
       high = mid - 1
     }
  }

  return -1
}

function count(min) {
  let sum = 0
  let bouquet = 0

  for (let i = 0; i < arr.length; i++) {
    if (arr[i] <= min) {
      sum++
    } else {
      bouquet += Math.floor(sum / k)
      sum = 0
    }
  }

  bouquet += Math.floor(sum / k)

  return bouquet
}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="smallest"><span class="attention">Найти</span> наименьший делитель</h2>

Рассмотрим решение задачи на поиск наименьшего положительного целочисленного делителя, чтобы при делении на него всех элементов данного массива сумма результата деления была меньше или равна заданному пороговому значению.

**Например:** [1,2,3,4,5] 8 => 3

{% capture code %}
const arr = [1,2,3,4,5]
const threshold = 8

// Наименьший делитель: 3
console.log('Наименьший делитель:', smallest(arr, threshold))

function smallest(arr, threshold) {
  if (arr.length > threshold) return -1

  let low = 1
  let high = Math.max(...arr)

  while (low <= high) {
    const mid = Math.floor((low + high) / 2)

    if (sum(arr, mid) <= threshold) {
      high =  mid - 1
    } else {
      low = mid + 1
    }
  }

  return low
}

function sum(arr, mid) {
  let total = 0

  for (let i = 0; i < arr.length; i++) {
    total += Math.ceil(arr[i] / mid)
  }

  return total
}
{% endcapture %}
{% include component/code.html lang='js' content=code %}
