---
layout: post
title: Структура данных связанный список
date: 2023-09-16 09:16 +0600
description: 'Рассмотрим структуру данных список, научимся добавлять, удалять, находить, выводить элементы списка.'
image: cover.jpg
alt: Структура данных связанный список
category: dsa
tags: [список]
source:
published: true
sitemap: true
excerpt_separator: "<!--more-->"
---

{{ page.description }}

<!--more-->

## <span class="attention">Оглавление</span> статьи

1. [Вводная часть](#intro)
2. [Узел списка (LinkedListNode)](#node)
3. [Список (LinkedList)](#list)
4. [Пример использования](#example)

<h2 id="intro"><span class="attention">Вводная</span> часть</h2>

Связанный список (List) — представляет собой структуру данных, где элементы хранятся последовательно. Каждый элемент содержит своё значение и ссылку на следующий элемент в списке. Ссылка представляет собой указатель на другой узел или значение null, если следующего элемента нет.

<h2 id="node"><span class="attention">Узел</span> списка (LinkedListNode)</h2>

Узел списка представляет собой элемент в связанном списке. Связанный список - это структура данных, которая состоит из узлов, каждый из которых содержит две части: данные (обычно это какое-то значение) и ссылку (или указатель) на следующий узел в списке.

{% capture code %}
class LinkedListNode {
  constructor(value, next = null) {
    this.value = value;
    this.next = next;
  }

  toString(callback) {
    return callback ? callback(this.value) : `${this.value}`;
  }
}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="list"><span class="attention">Список</span> (LinkedList)</h2>

Список это структура данных, используемая для организации коллекции элементов, в которой каждый элемент, называемый узлом, состоит из двух частей: данных и ссылки (или указателя) на следующий элемент в списке. Это отличается от массива, где элементы хранятся в последовательности и имеют индексы для доступа к ним.

{% capture code %}
class LinkedList {
  constructor() {
    this.head = null;
    this.tail = null;
  }

  prepend(value) {
    // Создаём новый узел, который будет новым head,
    // при создании передаем второй аргумент, который указывает
    // что его "next" будет текущий head,
    // так как новый узел будет стоять перед текущем head.
    const newNode = new LinkedListNode(value, this.head);

    // Переназначаем head на новый узел
    this.head = newNode;

    // Если ещё нет tail, делаем новый узел tail.
    if (!this.tail) {
      this.tail = newNode;
    }

    // Возвращаем весь список.
    return this;
  }

  append(value) {
    // Создаём новый узел.
    const newNode = new LinkedListNode(value);

    // Если нет head или tail, делаем новым узлом head и tail.
    if (!this.head || !this.tail) {
      this.head = newNode;
      this.tail = newNode;

      return this;
    }

    // Присоединяем новый узел к концу связного списка.
    // Берём последний узел и указываем, что его next будет новым узлом.
    this.tail.next = newNode;

    // Переназначаем tail на новый узел.
    this.tail = newNode;

    return this;
  }

  delete(value) {
    // Если нет head значит список пуст.
    if (!this.head) {
      return null;
    }

    let deletedNode = null;

    // Если head должен быть удален, то делаем следующий узел новым head.
    while (this.head && this.head.value === value) {
      deletedNode = this.head;

      // Переназначаем следующий за head узел на новый head.
      this.head = this.head.next;
    }

    let currentNode = this.head;

    // Если следующий узел должен быть удален,
    // делаем узел через один, следующим для проверки.
    // Перебираем все узлы и удаляем их, если их значение равно указанному.
    if (currentNode !== null) {
      while (currentNode.next) {
        if (currentNode.next.value === value) {
          deletedNode = currentNode.next;
          // Перезаписываем, чтобы узел через один стал следующим узлом.
          currentNode.next = currentNode.next.next;
        } else {
          currentNode = currentNode.next;
        }
      }
    }

    // Проверяем, должен ли tail быть удален.
    // Так как, если в цикле мы удаляем последний узел,
    // то с предпоследнего узла убираем только ссылку на него.
    // Поэтому делаем проверку на его удаление с "tail".
    if (this.tail && this.tail.value === value) {
      // в данном случае currentNode это или предпоследний узел или head.
      this.tail = currentNode;
    }

    return deletedNode;
  }

  find(value) {
    // Если нет head значит список пуст.
    if (!this.head) {
      return null;
    }

    let currentNode = this.head;

    // Перебираем все узлы в поиске значения.
    while (currentNode) {
      // Если указано значение, пробуем сравнить его по значению.
      if (value !== undefined && currentNode.value === value) {
        return currentNode;
      }

      // Перематываем на один узел вперед.
      currentNode = currentNode.next;
    }

    return null;
  }


  deleteTail() {
    // Если нет tail, значит список пуст.

    if (!this.tail) {
      return null;
    }

    // Сохраняем значение последнего узла.
    const deletedTail = this.tail;

    // Если head и tail равны, значит в списке только один узел.
    if (this.head === this.tail) {
      this.head = null;
      this.tail = null;

      return deletedTail;
    }

    // Если в связном списке много узлов.
    // Перебираем все узлы и находим предпоследний узел,
    // убираем ссылку «next» на последний узел.
    let currentNode = this.head;
    while (currentNode.next) {
      // Если у следующего узла нет следующего узла,
      // значит текущий узел предпоследний.
      if (!currentNode.next.next) {
        // убираем ссылку «next» на последний узел.
        currentNode.next = null;
      } else {
        // Перематываем на один узел вперед.
        currentNode = currentNode.next;
      }
    }

    // В данном случае currentNode - это предпоследний узел или head,
    // который становится последним узлом.
    this.tail = currentNode;

    return deletedTail;
  }

  deleteHead() {
    // Если нет head значит список пуст.
    if (!this.head) {
      return null;
    }

    const deletedHead = this.head;

    // Если у head есть ссылка на следующий "next" узел
    // то делаем его новым head.
    if (this.head.next) {
      this.head = this.head.next;
    } else {
      // Если у head нет ссылки на следующий "next" узел
      // то мы удаляем последний узел.
      this.head = null;
      this.tail = null;
    }

    return deletedHead;
  }

  // Создаём новые узлы из массива и добавляем в конец списка.
  fromArray(values) {
    values.forEach(value => this.append(value));

    return this;
  }

  // Создаём массив из всех узлов
  toArray() {
    const nodes = [];

    let currentNode = this.head;

    // Перебираем все узлы и добавляем в массив.
    while (currentNode) {
      nodes.push(currentNode);
      currentNode = currentNode.next;
    }

    // Возвращаем массив из всех узлов.
    return nodes;
  }

  toString(callback) {
    // Сначала создаём массив из всех узлов.
    return this.toArray()
      // На каждом узле вызываем метод toString
      // что бы получить значение в виде строки.
      .map(node => node.toString(callback))
      // Вызываем метод toString на массиве строк.
      .toString();
  }

  // Обратный список
  reverse() {
    let currNode = this.head;
    let prevNode = null;
    let nextNode = null;

    // Перебираем все узлы.
    while (currNode) {
      // Сохраняем следующий узел.
      nextNode = currNode.next;

      // Меняем ссылку на следующий "next" узел текущего узла,
      // чтобы он ссылался на предыдущий узел.
      // Так как мы меняем их местами, нужно поменять и ссылки на узлы.
      // Изначально, первый узел не имеет предыдущего узла,
      // поэтому после перестановки его "next" станет "null".
      currNode.next = prevNode;

      // Перемещаем узлы prevNode и currNode на один шаг вперед.

      // Текущий узел делаем предыдущим.
      prevNode = currNode;

      // Следующий узел становится текущим.
      currNode = nextNode;
    }

    // Меняем head и tail местами.
    this.tail = this.head;

    // В данном случае prevNode это последний узел,
    // поэтому, после reverse, он становится первым.
    this.head = prevNode;

    // Возвращаем список.
    return this;
  }

  insertAfter(value, prevNode) {
    if (prevNode === null) {
      return this
    }

    const newNode = new LinkedListNode(value)

    newNode.next = prevNode.next
    prevNode.next = newNode

    if (newNode.next === null) {
      this.tail = newNode
    }

    return this
  }
}
{% endcapture %}
{% include component/code.html lang='js' content=code %}

<h2 id="example"><span class="attention">Пример</span> использования</h2>

{% capture code %}
let list = new LinkedList();

console.log(list);
// LinkedList { head: null, tail: null }

list.append("Щит");

console.log(list.find("Щит"));
// LinkedListNode { value: 'Щит', next: null }

list.append("Шлем");

console.log(list.find("Щит"));
// LinkedListNode {
//  value: 'Щит',
//  next: LinkedListNode { value: 'Шлем', next: null }
// }

list.append(1).append(2).append(3).append(4)

const find = list.find(3)

console.log(list.insertAfter(5, find))

/*
Вывод

{
  "head": {
    "value": 1,
    "next": {
      "value": 2,
      "next": {
        "value": 3,
        "next": {
          "value": 5,
          "next": {
            "value": 4,
            "next": null
          }
        }
      }
    }
  },
  "tail": {
    "value": 4,
    "next": null
  }
}
*/
{% endcapture %}
{% include component/code.html lang='js' content=code %}
