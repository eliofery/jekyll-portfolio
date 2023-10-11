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
2. [Максимальный элемент в массиве](#max-el)
3. [Второй максимальный и минимальный элемент](#two-max-min)
4. [Проверить отсортирован ли массив](#is-sort)
5. [Удаление дубликатов из массива](#remove-duplicate)
6. [Повернуть массив влево на одну позицию](#rotate-array-left)
7. [Повернуть массив влево на N позиций](#rotate-array-left-n)
8. [Повернуть массив вправо на N позиций](#rotate-array-right-n)
9. [Переместить нули в конец массива](#move-zero)
10. [Линейный поиск](#linear-search)
11. [Объединение двух отсортированных массивов](#union-two-sort-array)
12. [Пересечение двух отсортированных массивов](#intersection-two-sort-array)
13. [Поиск пропущенного числа](#missing-number)
14. [Максимальная последовательность в массиве](#max-consecutive-number)
15. [Число с одним повторением](#one-count)
16. [Самый длинный подмассив с сумой k](#sum-k)
17. [Повторения в массиве](#count)
18. [Поиск числа с большим и меньшим повторением](#min-max)
19. [Поиск двух чисел](#sum-2)
20. [Сортироватьмассив с тремя числами](#sort-3)
21. [Количество повторений числа](#maj-el)
22. [Максимальная сумма подмассива](#max-subarray)
23. [Покупка продажа акции](#buy-stock)
24. [Чередование положительных и отрицательных чисел](#rearrange)
25. [Лидирующие числа](#leaders)
26. [Самая длинная последовательность](#long-consecutive)
27. [Подсчет подмассивов с заданной суммой](#count-subarray-sum)

<h2 id="intro"><span class="attention">Вводная</span> часть</h2>

Массивы - это фундаментальная структура данных в программировании, которая позволяет хранить и организовывать множество элементов одного типа в одной переменной. В этой статье мы рассмотрим решения базовых задач с массивом данных.

<h2 id="max-el"><span class="attention">Максимальный</span> элемент в массиве</h2>

Решение задачи с нахождением максимального элемента в массиве.

**Например:** [3,2,1,5,2] => 5

{% capture code %}
const arr = [3,2,1,5,2]
let max = arr[0]

for (let i = 0; i < arr.length; i++) {
  if (arr[i] > max) max = arr[i]
}

// Максимальный элемент: 5
console.log('Максимальный элемент:', max)
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="two-max-min"><span class="attention">Второй</span> максимальный и минимальный элемент</h2>

Решение задачи с нахождением второго по счету максимального и минимального элемента в массиве.

**Например:** [4,5,33,1,8,9,9,6,9,0] => [0,1], [33,9]

{% capture code %}
const arr = [4,5,33,1,8,9,9,6,9,0]
const min = secondMin(arr)
const max = secondMax(arr)

// Минимальный: [0,1]
console.log('Минимальный:', min)

// Максимальный: [33,9]
console.log('Максимальный:', max)

function secondMin(arr) {
  let min = arr[0]
  let smin = arr[1]

  for (let i = 2; i < arr.length; i++) {
    if (arr[i] < min) {
      smin = min
      min = arr[i]
    } else if (arr[i] !== min && arr[i] < smin) {
      smin = arr[i]
    }
  }

  return [min, smin]
}

function secondMax(arr) {
  let max = arr[0]
  let smax = arr[1]

  for (let i = 2; i < arr.length; i++) {
    if (arr[i] > max) {
      smax = max
      max = arr[i]
    } else if (arr[i] !== max && arr[i] > smax) {
      smax = arr[i]
    }
  }

  return [max, smax]
}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="is-sort"><span class="attention">Проверить</span> отсортирован ли массив</h2>

Решение задачи с проверкой массива на отсортированность элементов.

**Например:** [1,2,3,4,5,6,7,8] => true

{% capture code %}
const sort = [1,2,3,4,5,6,7,8]
const notSort = [1,2,5,3,6,4,8,7]

// true
console.log('Отсортированный массив:', isSort(sort))

// false
console.log('Не отсортированный массив:', isSort(notSort))

function isSort(arr) {
  for (let i = 1; i < arr.length; i++) {
    if (arr[i] < arr[i - 1]) {
      return false
    }
  }

  return true
}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="remove-duplicate"><span class="attention">Удаление</span> дубликатов из отсортированного массива</h2>

Решение задачи с удалением дубликатов из массива.

**Например:** [1,1,2,3,4,4,5,6,7,8] => [1,2,3,4,5,6,7,8]

{% capture code %}
const arr = [1,1,2,3,4,4,5,6,7,8]
const uniq = []

for (let i = 0; i < arr.length; i++) {
  if (arr[i] !== uniq[uniq.length - 1]) {
    uniq.push(arr[i])
  }
}

// Уникальные числа: [1,2,3,4,5,6,7,8]
console.log('Уникальные числа:', uniq)
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="rotate-array-left"><span class="attention">Повернуть</span> массив влево на одну позицию</h2>

Решение задачи с поворотом массива на одну позицию влево.

**Например:** [1,2,3,4,5]  => [2,3,4,5,1]

{% capture code %}
const arr = [1,2,3,4,5]

for (let i = 0; i < arr.length - 1; i++) {
  const tmp = arr[i]
  arr[i] = arr[i + 1]
  arr[i + 1] = tmp
}

// Повернутый массив: [2,3,4,5,1]
console.log('Повернутый массив:', arr)
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="rotate-array-left-n"><span class="attention">Повернуть</span> массив влево на N позиций</h2>

Решение задачи с поворотом массива на N позицию влево.

**Например:** [1,2,3,4,5] 4  => [2,3,4,5,1]

{% capture code %}
let n = 4
const arr = [1,2,3,4,5]

while (n > 0) {
  for (let i = 0; i < arr.length - 1; i++) {
    const tmp = arr[i]
    arr[i] = arr[i + 1]
    arr[i + 1] = tmp
  }

  n--
}

// Повернутый массив: [5,1,2,3,4]
console.log('Повернутый массив:', arr)
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="rotate-array-right-n"><span class="attention">Повернуть</span> массив вправо на N позиций</h2>

Решение задачи с поворотом массива на N позиций вправо.

**Например:** [1,2,3,4,5] 4  => [2,3,4,5,1]

{% capture code %}
const arr = [1, 2, 3, 4, 5];
const n = 4;

const shiftedArr = [];

for (let i = 0; i < arr.length; i++) {
  const newIndex = (i + n) % arr.length
  shiftedArr[newIndex] = arr[i]
}

// Повернутый массив: [2,3,4,5,1]
console.log('Повернутый массив:', shiftedArr)
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="move-zero"><span class="attention">Переместить</span> нули в конец массива</h2>

Решение задачи с перемещением нулей в конец массива.

**Например:** [0, 1, 0, 2, 3, 2, 0, 0, 4, 5, 1, 0]  => [1,2,3,2,4,5,1,0,0,0,0,0]

{% capture code %}
const arr = [0, 1, 0, 2, 3, 2, 0, 0, 4, 5, 1, 0]
const tmp = []

for (let i = 0; i < arr.length; i++) {
  if (arr[i] !== 0) {
    tmp.push(arr[i])
  }
}

let end = arr.length - tmp.length
for (let i = 0; i < end; i++) {
  tmp.push(0)
}

// Исходный массив: [0, 1, 0, 2, 3, 2, 0, 0, 4, 5, 1, 0]
console.log('Исходный массив:', arr)

// Новый массив: [1,2,3,2,4,5,1,0,0,0,0,0]
console.log('Новый массив', tmp)
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="linear-search"><span class="attention">Линейный</span> поиск</h2>

Решение задачи с линейным поиском элемента в массиве.

**Например:** [1,6,7,3,8,2,4,0,5] 3  => 3

{% capture code %}
const search = 3
const arr = [1,6,7,3,8,2,4,0,5]
let position = -1

for (let i = 0; i < arr.length; i++) {
  if (arr[i] === search) {
    position = i
    break
  }
}

console.log('Позиция найденного элемента:', position)
console.log('Найденный элемент:', arr[position])
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="union-two-sort-array"><span class="attention">Объединение</span> двух отсортированных множеств</h2>

Решение задачи с объединением двух отсортированных множеств.

**Например:** [1,1,2,3,4,5,9] [2,3,4,4,5,6]  => [1,2,3,4,5,9,6]

{% capture code %}
const arr1 = [1,1,2,3,4,5,9]
const arr2 = [2,3,4,4,5,6]
const union = new Set()

for (let i = 0; i < arr1.length; i++) {
  union.add(arr1[i])
}

for (let i = 0; i < arr2.length; i++) {
  union.add(arr2[i])
}

const res = []
for (let item of union) {
  res.push(item)
}

// Объединенное множество: [1,2,3,4,5,9,6]
console.log('Объединенное множество:', res)
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="intersection-two-sort-array"><span class="attention">Пересечение</span> двух отсортированных массивов</h2>

Решение задачи с пересечением двух отсортированных массивов.

**Например:** [1,2,2,3,3,4,5,6] [2,3,3,5,6,6,7]  => [2,3,3,5,6]

{% capture code %}
const arr1 = [1,2,2,3,3,4,5,6]
const arr2 = [2,3,3,5,6,6,7]
const visit = Array(Math.max(arr1.length, arr2.length)).fill(0)
const res = []

for (let i = 0; i < arr1.length; i++) {
  for (let j = 0; j < arr2.length; j++) {
    if (arr2[j] > arr1[i]) {
      break
    }

    if (arr1[i] === arr2[j] && visit[j] === 0) {
      res.push(arr1[i])
      visit[j] = 1
      break
    }
  }
}

// Пересеченный массив: [2,3,3,5,6]
console.log('Пересеченный массив:', res)
{% endcapture %}
{% include component/code.html lang='js' content=code %}

### Вариант 2

{% capture code %}
const arr1 = [1,2,2,3,3,4,5,6]
const arr2 = [2,3,3,5,6,6,7]
let i = 0
let j = 0
const res = []

while (i < arr1.length && j < arr2.length) {
  if (arr1[i] < arr2[j]) {
    i++
  } else if (arr2[j] < arr1[i]) {
    j++
  } else {
    res.push(arr1[i])
    i++
    j++
  }
}

// Пересеченный массив: [2,3,3,5,6]
console.log('Пересеченный массив:', res)
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="missing-number"><span class="attention">Поиск</span> пропущенного числа</h2>

Решение задачи с поиском пропущенного числа.

**Например:** [1,2,3,4,5,7,8,10]  => 6, 9

{% capture code %}
const arr = [1,2,3,4,5,7,8,10]
let res = []

for (let i = 0; i < arr.length - 1; i++) {
  if (arr[i] + 1 !== arr[i + 1]) {
    res.push(arr[i] + 1)
  }
}

// Пропущенное число: [6, 9]
console.log('Пропущенное число:', res)
{% endcapture %}
{% include component/code.html lang='js' content=code %}

### Вариант 2

{% capture code %}
const arr = [1,2,3,4,5,6,7,8,10]
let xor1 = 0
let xor2 = 0

for (let i = 0; i < arr.length - 1; i++) {
  xor1 = xor1 ^ arr[i]
  xor2 = xor2 ^ (i + 1)
}

xor1 = xor1 ^ arr.length - 1

const res = xor1 ^ xor2

// Пропущенное число: 9
console.log('Пропущенное число:', res)
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="max-consecutive-number"><span class="attention">Максимальная</span> последовательность в массиве</h2>

Решение задачи с поиском максимальной последовательностью в массиве.

**Например:** [1,1,0,1,1,1,0,1,1] => 3

{% capture code %}
const arr = [1,1,0,1,1,1,0,1,1]
let count = 0
let max = 0

for (let i = 0; i < arr.length; i++) {
  if (arr[i] === 1) {
    count++
    max = Math.max(max, count)
  } else {
    count = 0
  }
}

// Максимальное число повторений: 3
console.log('Максимальное число повторений:', max)
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="one-count"><span class="attention">Число</span> с одним повторением</h2>

Решение задачи с поиском числа с одним повторением.

**Например:** [1,1,2,3,3,4,4] => 2

{% capture code %}
let arr = [1,1,2,3,3,4,4]
let xorr = 0

for (let i = 0; i < arr.length; i++) {
  xorr ^= arr[i]
}

// Число с одним повторением: 2
console.log('Число с одним повторением:', xorr)
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="sum-k"><span class="attention">Самый</span> длинный подмассив с сумой k</h2>

Решение задачи с поиском самого длинного подмассива с сумой k.

**Например:** [1,2,1,4,1,1,1,1,2,1,1,0,4] 3 => [1,1,1]

{% capture code %}
const k = 3
const arr = [1,2,1,4,1,1,1,1,2,1,1,0,4]
const pos = Array(2).fill(0)

for (let i = 0; i < arr.length; i++) {
  let sum = arr[i]

  for (let j = i + 1; j < arr.length; j++) {
    sum += arr[j]

    if (sum === k && pos[1] < j - i + 1) {
      pos[0] = i
      pos[1] = j + 1
      break
    }
  }
}

// Самый длинный подмассив: [1,1,1]
console.log(arr.slice(pos[0], pos[1]))
{% endcapture %}
{% include component/code.html lang='js' content=code %}

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

<h2 id="sum-2"><span class="attention">Поиск</span> двух чисел</h2>

Рассмотрим решение задачи на нахождение двух чисел сумма которых равна заданному числу.

**Например:** [6,8,12,1,4,5,10] 18 => [6,12]

{% capture code %}
const target = 18
const arr = [6,8,12,1,4,5,10]
let map = new Map()
let res = []

for (let i = 0; i < arr.length; i++) {
  const search = target - arr[i]

  map.set(search, arr[i])

  if (map.has(arr[i])) {
    res = [map.get(arr[i]), arr[i]]
    break
  }
}

// Сумма двух чисел: [6,12]
console.log('Сумма двух чисел:', res)
{% endcapture %}
{% include component/code.html lang='js' content=code %}

### Вариант 2

{% capture code %}
const target = 18
const arr = [6,8,12,1,4,5,10]
let left = 0
let right = arr.length -1
let res = []

arr.sort((a, b) => a - b)

while (left < right) {
  let sum = arr[left] + arr[right]

  if (sum === target) {
    res = [arr[left], arr[right]]
    break
  } else if (sum < target) {
    left++
  } else {
    right--
  }
}

// Сумма двух чисел: [6,12]
console.log('Сумма двух чисел:', res)
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="sort-3"><span class="attention">Сортировать</span> массив с тремя числами</h2>

Рассмотрим решение задачи на сортировку массива с тремя числами.

**Например:** [0,1,2,0,1,2,1,2,0,0,0,1] => [0,0,0,0,0,1,1,1,1,2,2,2]

{% capture code %}
const arr = [0,1,2,0,1,2,1,2,0,0,0,1]
const res = []

for (let i = 0; i < arr.length; i++) {
  for (let j = 0; j < arr.length; j++) {
    if (arr[j] === i) {
      res.push(arr[j])
    }
  }
}

// Отсортированный массив: [0,0,0,0,0,1,1,1,1,2,2,2]
console.log('Отсортированный массив:', res)
{% endcapture %}
{% include component/code.html lang='js' content=code %}

### Вариант 2

{% capture code %}
const arr = [0,1,2,0,1,2,1,2,0,0,0,1]
let low = 0
let mid = 0
let high = arr.length - 1

while (mid <= high) {
  if (arr[mid] === 0) {
    const temp = arr[low]
    arr[low] = arr[mid]
    arr[mid] = temp

    low++
    mid++
  }

  else if (arr[mid] === 1) {
    mid++
  }

  else {
    const temp = arr[mid]
    arr[mid] = arr[high]
    arr[high] = temp

    high--
  }
}

// Отсортированный массив: [0,0,0,0,0,1,1,1,1,2,2,2]
console.log('Отсортированный массив:', arr)
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="maj-el"><span class="attention">Количество</span> повторений числа</h2>

Рассмотрим решение задачи на поиск числа которое повторяется больше чем длина массива в два раза.

**Например:** [7,7,5,7,5,1,5,7,5,5,7,7,5,5,5,5] => 5

{% capture code %}
const arr = [7,7,5,7,5,1,5,7,5,5,7,7,5,5,5,5]
let count = 0
let el = null

for (let i = 0; i < arr.length; i++) {
  if (count === 0) {
    count = 1
    el = arr[i]
  }

  else if (arr[i] === el) {
    count++
  }

  else {
    count--
  }
}

count = 0
for (let item of arr) {
  if (item === el) count++
}

if (count <= arr.length / 2) {
  el = -1
}

// Наибольшее число: 5
console.log('Наибольшее число:', el)
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="max-subarray"><span class="attention">Максимальная</span> сумма подмассива</h2>

Рассмотрим решение задачи на поиск максимальной суммы подмассива.

**Например:** [-2,1,-3,4,-1,2,1,-5,4] => 6

{% capture code %}
const arr = [-2,1,-3,4,-1,2,1,-5,4]
let max = 0

for (let i = 0; i < arr.length; i++) {
  let sum = arr[i]

  for (let j = i + 1; j < arr.length; j++) {
    sum += arr[j]

    if (sum > max) {
      max = sum
    }
  }
}

// Максимальная сумма подмассива: 6
console.log('Максимальная сумма подмассива:', max)
{% endcapture %}
{% include component/code.html lang='js' content=code %}

### Вариант 2

{% capture code %}
const arr = [-2,1,-3,4,-1,2,1,-5,4]
let sum = 0
let max = 0

for (let item of arr) {
  sum += item
  max = Math.max(sum, max)

  if (sum < 0) {
    sum = 0
  }
}

console.log(max)

// Максимальная сумма подмассива: 6
console.log('Максимальная сумма подмассива:', max)
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="buy-stock"><span class="attention">Покупка</span> продажа акции</h2>

Рассмотрим решение задачи на нахождение максимальной прибыли от покупки и продажи акции.

**Например:** [7,1,5,3,6,4] => 5

{% capture code %}
const arr = [7,1,5,3,6,4]
let sell = 0
let buy = 0

for (let i = 0; i < arr.length; i++) {
  for (let j = i + 1; j < arr.length; j++) {
    if (arr[j] - arr[i] > arr[buy] - arr[sell]) {
      sell = i
      buy = j
    }
  }
}

// Купил: 1
// Продал: 6
// Максимальная прибыль: 5
console.log('Купил:', arr[sell])
console.log('Продал:', arr[buy])
console.log('Максимальная прибыль:', arr[buy] - arr[sell])
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="rearrange"><span class="attention">Чередование</span> положительных и отрицательных чисел</h2>

Рассмотрим решение задачи на формирование массива в котором положительные числа чередуются с отрицательными.

**Например:** [3,1,-2,-5,2,-4,3,6,7,8,9] => [3,-2,1,-5,2,-4,3,6,7]

{% capture code %}
const arr = [3,1,-2,-5,2,-4,3,6,7,8,9]
const minus = []
const plus = []
const res = []

for (let i = 0; i < arr.length; i++) {
  if (arr[i] < 0) {
    minus.push(arr[i])
  } else {
    plus.push(arr[i])
  }
}

for (let i = 0; i < Math.ceil(arr.length / 2); i++) {
  if (plus[i]) res[2 * i] = plus[i]
  if (minus[i]) res[2 * i + 1] = minus[i]
}

// Чередующиеся числа: [3,-2,1,-5,2,-4,3,6,7]
console.log('Чередующиеся числа:', res.filter(item => item))
{% endcapture %}
{% include component/code.html lang='js' content=code %}

### Вариант 2

{% capture code %}
const arr = [3,1,-2,-5,2,-4,3, 6,7,8,9]
let posIndex = 0
let negIndex = 1
const res = Array(arr.length).fill(0)

for (let i = 0; i < arr.length; i++) {
  if (arr[i] < 0) {
    res[negIndex] = arr[i]
    negIndex += 2
  } else {
    res[posIndex] = arr[i]
    posIndex += 2
  }
}

// Чередующиеся числа: [3,-2,1,-5,2,-4,3,6,7]
console.log('Чередующиеся числа:', res.filter(item => item))
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="leaders"><span class="attention">Лидирующие</span> числа</h2>

Рассмотрим решение задачи на поиск чисел которые больше всех элементов в его правой части массива.

**Например:** [10,22,12,3,0,6] => [22,12,6]

{% capture code %}
const arr = [10,22,12,3,0,6]
const res = []

for(let i = 0; i < arr.length; i++) {
  let leader = true

  for(let j = i + 1; j < arr.length; j++) {
    if (arr[i] < arr[j]) {
      leader = false
      break
    }
  }

  if (leader) res.push(arr[i])
}

// Лидирующие числа: [22,12,6]
console.log('Лидирующие числа:', res)
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="long-consecutive"><span class="attention">Самая</span> длинная последовательность</h2>

Рассмотрим решение задачи на поиск самой длинной последовательности чисел в массиве.

**Например:** [102,4,100,1,101,3,2,1,1,5] => 5

{% capture code %}
let arr = [102,4,100,1,101,3,2,1,1,5]
let res = 0
let max = 1

arr.sort((a, b) => a - b)

arr = [... new Set(arr)]

for(let i = 0; i < arr.length - 1; i++) {
  if (arr[i] + 1 === arr[i + 1]) {
    max += 1
  } else {
    res = Math.max(res, max)
    max = 1
  }
}

// Самая длинная последовательность: 5
console.log('Самая длинная последовательность:', res)
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="count-subarray-sum"><span class="attention">Подсчет</span> подмассивов с заданной суммой</h2>

Рассмотрим решение задачи на подсчет количества подмассивов равное заданной сумме.

**Например:** [1,2,2,3,-3,1,1,1,4,2,-3] 3 => 7

{% capture code %}
const arr = [1,2,2,3,-3,1,1,1,4,2,-3]
const k = 3
let count = 0

for (let i = 0; i < arr.length; i++) {
  let sum = 0

  for (let j = i; j < arr.length; j++) {
    sum += arr[j]

    if (sum === k) count++
  }
}

// Количество подмассивов: 7
console.log('Количество подмассивов:', count)
{% endcapture %}
{% include component/code.html lang='js' content=code %}
