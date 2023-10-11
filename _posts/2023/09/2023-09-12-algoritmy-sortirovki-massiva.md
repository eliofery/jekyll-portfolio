---
layout: post
title: Алгоритмы сортировки массива
date: 2023-09-12 09:16 +0600
description: 'Рассмотрим популярные решения для сортировки массива от медленных до быстрых.'
image: cover.jpg
alt: Алгоритмы сортировки массива
category: dsa
tags: [циклы, массивы, сортировка]
source:
published: true
sitemap: true
excerpt_separator: "<!--more-->"
---

{{ page.description }}

<!--more-->

## <span class="attention">Оглавление</span> статьи

1. [Вводная часть](#intro)
2. [Сортировка выбором](#selection)
3. [Сортировка пузырьком](#bubble)
4. [Сортировка вставкой](#insertion)
5. [Сортировка шейкерная](#shaker)
6. [Сортировка слиянием](#merge)
7. [Сортировка быстрая](#quick)

<h2 id="intro"><span class="attention">Вводная</span> часть</h2>

Сортировка массива - важная операция в программировании, которая позволяет упорядочить элементы в массиве в соответствии с определенным порядком. От быстрой сортировки до сортировки пузырьком, существует множество алгоритмов для достижения этой цели. В этой статье мы рассмотрим различные методы сортировки массивов и их практическое применение.

<h2 id="selection"><span class="attention">Сортировка</span> выбором</h2>

Сортировка выбором - находит минимальный элемент в массиве и перемещает его на начало. Затем он повторяет этот процесс для оставшейся части массива.

{% capture code %}
const arr = [13, 46, 52, 20, 9]

// Исходный массив: [13,46,52,20,9]
console.log('Исходный массив:', arr)

// Отсортированный массив: [9,13,20,46,52]
console.log('Отсортированный массив', selectionSort(arr))

// O(N^2)
function selectionSort(arr) {
  for (let i = 0; i <= arr.length - 2; i++) {
    for (let j = i + 1; j <= arr.length - 1; j++) {
      if (arr[i] > arr[j]) {
        const temp = arr[i]
        arr[i] = arr[j]
        arr[j] = temp
      }
    }
  }

  return arr
}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="bubble"><span class="attention">Сортировка</span> пузырьком</h2>

Сортировка пузырьком - основывается на сравнении и обмене соседних элементов массива до тех пор, пока весь массив не будет отсортирован.

{% capture code %}
const arr = [13, 46, 52, 20, 9]

// Исходный массив: [13,46,52,20,9]
console.log('Исходный массив:', arr)

// Отсортированный массив: [9,13,20,46,52]
console.log('Отсортированный массив', bubbleSort(arr))

// O(N^2)
function bubbleSort(arr) {
  for(let i = 0; i < arr.length - 1; i++) {
    let swap = false

    for(let j = 0; j < arr.length - 1 - i; j++) {
      if (arr[j] > arr[j + 1]) {
        [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]]
        swap = true
      }
    }

    if (!swap) break
  }

  return arr
}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="insertion"><span class="attention">Сортировка</span> вставкой</h2>

Сортировка вставкой - по мере прохода по массиву элементы "вставляются" на свои места в уже отсортированной части массива.

{% capture code %}
const arr = [13, 46, 52, 20, 9]

// Исходный массив: [13,46,52,20,9]
console.log('Исходный массив:', arr)

// Отсортированный массив: [9,13,20,46,52]
console.log('Отсортированный массив', insertionSort(arr))

// O(N^2)
function insertionSort(arr) {
  for(let i = 1; i < arr.length; i++) {
    let j = i

    while(j > 0 && arr[j - 1] > arr[j]) {
      const temp = arr[j - 1]
      arr[j - 1] = arr[j]
      arr[j] = temp

      j--
    }
  }

  return arr
}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="shaker"><span class="attention">Сортировка</span> шейкерная</h2>

Шейкерная сортировка - комбинирует в себе принципы сортировки пузырьком и сортировки вставкой. Этот метод сортировки особенно полезен, когда необходимо упорядочить массив элементов, и он обладает интересной особенностью: движение "вперед-назад".

{% capture code %}
const arr = [13, 46, 52, 20, 9]

// Исходный массив: [13,46,52,20,9]
console.log('Исходный массив:', arr)

// Отсортированный массив: [9,13,20,46,52]
console.log('Отсортированный массив', cocktailSort(arr))

// O(N^2)
function cocktailSort(arr) {
  let isSorted = true;
  let begin = 0;
  let end = arr.length - 1;

  while (isSorted) {
    isSorted = false;

    // Проход слева направо
    for (let i = begin; i < end; i++) {
      if (arr[i] > arr[i + 1]) {
        [arr[i], arr[i + 1]] = [arr[i + 1], arr[i]];
        isSorted = true;
      }
    }

    // Если элементы не были перемещены, то массив уже отсортирован
    if (!isSorted) {
      break;
    }

    // Сбрасываем флаг isSorted и двигаем конечный индекс на 1 назад
    isSorted = false;
    end--;

    // Проход справа налево
    for (let i = end - 1; i >= begin; i--) {
      if (arr[i] > arr[i + 1]) {
        [arr[i], arr[i + 1]] = [arr[i + 1], arr[i]];
        isSorted = true;
      }
    }

    // Увеличиваем начальный индекс на 1
    begin++;
  }

  return arr;
}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="merge"><span class="attention">Сортировка</span> слиянием</h2>

Сортировка слиянием - это эффективный алгоритм сортировки, основанный на принципе разделения массива на более мелкие части, и последующем их слиянии в упорядоченный массив. Этот алгоритм отличается стабильностью и хорошей производительностью, что делает его важным инструментом для сортировки больших объемов данных.

{% capture code %}
const arr = [13, 46, 52, 20, 9]

// Исходный массив: [13,46,52,20,9]
console.log('Исходный массив:', arr)

// Отсортированный массив: [9,13,20,46,52]
console.log('Отсортированный массив', mergeSort(arr))

// O(n log n)
function mergeSort(arr) {
  if (arr.length <= 1) {
    return arr;
  }

  let mid = Math.floor(arr.length / 2);
  let left = arr.slice(0, mid);
  let right = arr.slice(mid);

  return merge(mergeSort(left), mergeSort(right));
}

function merge(left, right) {
  let result = [];

  let leftIndex = 0;
  let rightIndex = 0;

  while (leftIndex < left.length && rightIndex < right.length) {
    if (left[leftIndex] < right[rightIndex]) {
      result.push(left[leftIndex]);
      leftIndex++;
    } else {
      result.push(right[rightIndex]);
      rightIndex++;
    }
  }

  while (leftIndex < left.length) {
    result.push(left[leftIndex])
    leftIndex++
  }

  while (rightIndex < right.length) {
    result.push(right[rightIndex])
    rightIndex++
  }

  return result
}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="quick"><span class="attention">Сортировка</span> быстрая</h2>

Быстрая сортировка - это эффективный алгоритм сортировки, который основан на принципе разделения массива на подмассивы, с последующей сортировкой каждого подмассива и их объединением. Этот алгоритм известен своей высокой производительностью и широким применением в различных областях программирования.

{% capture code %}
const arr = [13, 46, 52, 20, 9]

// Исходный массив: [13,46,52,20,9]
console.log('Исходный массив:', arr)

// Отсортированный массив: [9,13,20,46,52]
console.log('Отсортированный массив', quickSort(arr))

// O(n log n)
function quickSort(arr) {
  if (arr.length <= 1) {
    return arr;
  }

  let pivot = arr[0];
  let left = [];
  let right = [];

  for (let i = 1; i < arr.length; i++) {
    if (arr[i] < pivot) {
      left.push(arr[i]);
    } else {
      right.push(arr[i]);
    }
  }

  return quickSort(left).concat(pivot, quickSort(right));
 }
{% endcapture %}
{% include component/code.html lang='js' content=code %}
