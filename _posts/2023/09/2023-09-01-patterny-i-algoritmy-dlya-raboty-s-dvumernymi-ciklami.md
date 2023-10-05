---
layout: post
title: Паттерны и алгоритмы для работы с двумерными циклами
date: 2023-09-02 09:16 +0600
description: Рассмотрим различные варианты манипуляции с двумерными циклами. Полученные знания в дальнейшем пригодятся для решения различных типов задач, например обход двухмерной матрицы и прочее.
image: cover.jpg
alt: Паттерны для работы с двумерными циклами
category: dsa
tags: [циклы, узоры]
source:
published: true
sitemap: true
excerpt_separator: "<!--more-->"
---

{{ page.description }}

<!--more-->

## <span class="attention">Оглавление</span> статьи

1. [Вводная часть](#intro)
2. [Паттерн 1 (простой квадрат)](#pattern-1)
3. [Паттерн 2 (лесенка вправо)](#pattern-2)
4. [Паттерн 3 (лесенка влево)](#pattern-3)
5. [Паттерн 4 (перевернутая лесенка вправо)](#pattern-4)
6. [Паттерн 5 (перевернутая лесенка влево)](#pattern-5)
7. [Паттерн 6 (числовая лесенка вправо)](#pattern-6)
8. [Паттерн 7 (числовая лесенка влево)](#pattern-7)
9. [Паттерн 8 (перевернутая числовая лесенка вправо)](#pattern-8)
10. [Паттерн 9 (перевернутая числовая лесенка влево)](#pattern-9)
11. [Паттерн 10 (числовая лесенка вправо 2)](#pattern-10)
12. [Паттерн 11 (треугольник)](#pattern-11)
13. [Паттерн 12 (перевернутый треугольник)](#pattern-12)
14. [Паттерн 13 (почти ромб)](#pattern-13)
15. [Паттерн 14 (стрелка вправо)](#pattern-14)
16. [Паттерн 15 (бинарная лесенка вправо)](#pattern-15)
17. [Паттерн 16 (зеркальные числовые лесенки)](#pattern-16)
18. [Паттерн 17 (числовая лесенка вправо 3)](#pattern-17)
19. [Паттерн 18 (буквенная лесенка вправо)](#pattern-18)
20. [Паттерн 19 (перевернутая буквенная лесенка вправо)](#pattern-19)
21. [Паттерн 20 (буквенная лесенка вправо 2)](#pattern-20)
22. [Паттерн 21 (буквенный треугольник)](#pattern-21)
23. [Паттерн 22 (буквенная лесенка вправо 3)](#pattern-22)
24. [Паттерн 23 (внутренний ромб)](#pattern-23)
25. [Паттерн 24 (бабочка)](#pattern-24)
26. [Паттерн 25 (прозрачный квадрат)](#pattern-25)
27. [Паттерн 26 (матрица убывающих чисел)](#pattern-26)

<h2 id="intro"><span class="attention">Вводная</span> часть</h2>

У двумерного цикла имеется внешний циклы и внутренний цикл. Внешний цикл отвечает за строки, а внутренний цикл отвечает за столбцы.

<pre>
    с т о л б цы
с   * * * * * *
т   * * * * * *
р   * * * * * *
о   * * * * * *
ки  * * * * * *
</pre>

### Этапы оценки шаблона

1. Для внешнего цикла посчитать количество строк
2. Для внутреннего цикла посчитать количество столбцов и определенным образом соединить их со строками
3. Выводить данные во внутреннем цикле
4. Соблюдать симметрию при необходимости

{% capture code %}
// 1.
// внешний цикл обрабатывает строки
// каждая итерация внешнего цикла ждет полного завершения внутреннего цикла
// и только после этого переходит к следующей итерации строки
for (let i = 0; i < n; i++) {

  // 2.
  // внутренний цикл обрабатывает столбцы один за другим пока число столбцов не будет
  // соответствовать условию завершения внутреннего цикла
  for (let j = 0; j < n; j++) {

    // 3.
    // Вывод данных
  }

  // 4.
  // итерация внешнего цикла завершилась
  // переводим каретку (курсор) на новую строку и повторяем одну строку за другой
  // пока число строк не будет соотвествовать условию завершения внешнего цикла
}
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

<h2 id="pattern-1"><span class="attention">Паттерн</span> первый (простой квадрат)</h2>

<pre>
******
******
******
******
******
</pre>

{% capture code %}
console.log(pattern(5))

function pattern(n) {
  let result = '\n'

  for (let i = 0; i < n; i++) {

    for (let j = 0; j < n; j++) {
      result += '*'
    }

    result += '\n'
  }

  return result
}
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

<h2 id="pattern-2"><span class="attention">Паттерн</span> второй (лесенка вправо)</h2>

<pre>
*
**
***
****
*****
</pre>

{% capture code %}
console.log(pattern(5))

function pattern(n) {
  let result = '\n'

  for (let i = 0; i < n; i++) {

      for (let j = 0; j <= i; j++) {
        result += '*'
      }

      result += '\n'
  }

  return result
}
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

<h2 id="pattern-3"><span class="attention">Паттерн</span> третий (лесенка влево)</h2>

<pre>
    *
   **
  ***
 ****
*****
</pre>

{% capture code %}
console.log(pattern(5))

function pattern(n) {
  let result = '\n'

  for (let i = 0; i < n; i++) {

      for (let j = 0; j <= n; j++) {
        if (j > n - i - 1) {
          result += '*'
        } else {
          result += ' '
        }
      }

      result += '\n'
  }

  return result
}
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

<h2 id="pattern-4"><span class="attention">Паттерн</span> четвертый (перевернутая лесенка вправо)</h2>

<pre>
*****
****
***
**
*
</pre>

{% capture code %}
console.log(pattern(5))

function pattern(n) {
  let result = '\n'

  for (let i = 0; i < n; i++) {

      for (let j = 0; j < n - i; j++) {
        result += '*'
      }

      result += '\n'
  }

  return result
}
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

<h2 id="pattern-5"><span class="attention">Паттерн</span> пять (перевернутая лесенка влево)</h2>

<pre>
*****
 ****
  ***
   **
    *
</pre>

{% capture code %}
console.log(pattern(5))

function pattern(n) {
  let result = '\n'

  for (let i = 0; i < n; i++) {

      for (let j = 0; j < n; j++) {
        if (j >= i) {
          result += '*'
        } else {
          result += ' '
        }
      }

      result += '\n'
  }

  return result
}
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

<h2 id="pattern-6"><span class="attention">Паттерн</span> шесть (числовая лесенка вправо)</h2>

<pre>
12345
1234
123
12
1
</pre>

{% capture code %}
console.log(pattern(5))

function pattern(n) {
  let result = '\n'

  for (let i = 0; i < n; i++) {

      for (let j = 1; j <= n - i; j++) {
        result += `${j}`
      }

      result += '\n'
  }

  return result
}
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

<h2 id="pattern-7"><span class="attention">Паттерн</span> семь (числовая лесенка влево)</h2>

<pre>
12345
 1234
  123
   12
    1
</pre>

{% capture code %}
console.log(pattern(5))

function pattern(n) {
  let result = '\n'

  for (let i = 0; i < n; i++) {

      for (let j = 1; j <= n; j++) {
        if (j >= n - (n - i - 1)) {
          result += `${j - (n - (n - i - 1)) + 1}`
        } else {
          result += ` `
        }
      }

      result += '\n'
  }

  return result
}
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

<h2 id="pattern-8"><span class="attention">Паттерн</span> восемь (перевернутая числовая лесенка вправо)</h2>

<pre>
1
12
123
1234
12345
</pre>

{% capture code %}
console.log(pattern(5))

function pattern(n) {
  let result = '\n'

  for (let i = 0; i < n; i++) {

      for (let j = 1; j <= i + 1; j++) {
        result += `${j}`
      }

      result += '\n'
  }

  return result
}
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

<h2 id="pattern-9"><span class="attention">Паттерн</span> девять (перевернутая числовая лесенка влево)</h2>

<pre>
    1
   12
  123
 1234
12345
</pre>

{% capture code %}
console.log(pattern(5))

function pattern(n) {
  let result = '\n'

  for (let i = 0; i < n; i++) {

      for (let j = 1; j <= n; j++) {
        if (j > n - i - 1) {
          result += `${j - (n - i - 1)}`
        } else {
          result += ' '
        }
      }

      result += '\n'
  }

  return result
}
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

<h2 id="pattern-10"><span class="attention">Паттерн</span> десять (числовая лесенка вправо 2)</h2>

<pre>
1
22
333
4444
55555
</pre>

{% capture code %}
console.log(pattern(5))

function pattern(n) {
  let result = '\n'

  for (let i = 1; i <= n; i++) {

        for (let j = 0; j < i; j++) {
          result += `${i}`
        }

        result += '\n'
  }

  return result
}
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

<h2 id="pattern-11"><span class="attention">Паттерн</span> одиннадцать (треугольник)</h2>

<pre>
    *
   ***
  *****
 *******
*********
</pre>

{% capture code %}
console.log(pattern(5))

function pattern(n) {
  let result = '\n'

  for (let i = 0; i < n; i++) {

        for (let j = 0; j < n * 2 - 1; j++) {
          if (j >= n - i - 1 && j <= n + i - 1) {
            result += '*'
          } else {
            result += ' '
          }
        }

        result += '\n'
  }

  return result
}
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

<h2 id="pattern-12"><span class="attention">Паттерн</span> двенадцать (перевернутый треугольник)</h2>

<pre>
*********
 *******
  *****
   ***
    *
</pre>

{% capture code %}
console.log(pattern(5))

function pattern(n) {
  let result = '\n'

  for (let i = 0; i < n; i++) {

        for (let j = 0; j < n * 2 - 1; j++) {
          if (j >= i && j < n * 2 - 1 - i) {
            result += '*'
          } else {
            result += ' '
          }
        }

        result += '\n'
  }

  return result
}
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

<h2 id="pattern-13"><span class="attention">Паттерн</span> тринадцать (почти ромб)</h2>

<pre>
    *
   ***
  *****
 *******
*********
*********
 *******
  *****
   ***
    *
</pre>

{% capture code %}
console.log(pattern(5))

function pattern(n) {
  let result = '\n'

  for (let i = 0; i < n; i++) {

        for (let j = 0; j < n * 2 - 1; j++) {
          if (j >= n - i - 1 && j <= n + i - 1) {
            result += '*'
          } else {
            result += ' '
          }
        }

        result += '\n'
  }

  for (let i = 0; i < n; i++) {

        for (let j = 0; j < n * 2 - 1; j++) {
          if (j >= i && j < n * 2 - 1 - i) {
            result += '*'
          } else {
            result += ' '
          }
        }

        result += '\n'
  }

  return result
}
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

<h2 id="pattern-14"><span class="attention">Паттерн</span> четырнадцать (стрелка вправо)</h2>

<pre>
*
**
***
****
*****
****
***
**
*
</pre>

{% capture code %}
console.log(pattern(5))

function pattern(n) {
  let result = ''

  for (let i = 0; i < n * 2; i++) {
      let col = i

      if (i >= n) col = n * 2 - i

      for (let j = 0; j < col; j++) {
        result += '*'
      }

      result += '\n'
  }

  return result
}
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

<h2 id="pattern-15"><span class="attention">Паттерн</span> пятнадцать (бинарная лесенка вправо)</h2>

<pre>
1
0 1
1 0 1
0 1 0 1
1 0 1 0 1
</pre>

{% capture code %}
console.log(pattern(5))

function pattern(n) {
  let result = '\n'

  for (let i = 0; i < n; i++) {
      let start = 0

      if (i % 2 === 0 ) start = 1

      for (let j = 0; j <= i; j++) {
        result += `${start}`
        start = 1 - start
      }

      result += '\n'
  }

  return result
}
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

<h2 id="pattern-16"><span class="attention">Паттерн</span> шестнадцать (зеркальные числовые лесенки)</h2>

<pre>
1      1
12    21
123  321
12344321
</pre>

{% capture code %}
console.log(pattern(5))

function pattern(n) {
  let result = '\n'

  let space = 2 * (n - 1)

  for (let i = 1; i <= n; i++) {

      for (let j = 1; j < i + 1; j++) {
        result += `${j}`
      }

      for (let j = 0; j < space; j++) {
        result += ' '
      }

      for (let j = i; j > 0; j--) {
        result += `${j}`
      }

      space -= 2
      result += '\n'
  }

  return result
}
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

<h2 id="pattern-17"><span class="attention">Паттерн</span> семнадцать (числовая лесенка вправо 3)</h2>

<pre>
1
2 3
4 5 6
7 8 9 10
11 12 13 14 15
</pre>

### Вариант 1

{% capture code %}
console.log(pattern(5))

function pattern(n) {
  let result = '\n'

  let start = 1

  for (let i = 1; i <= n; i++) {

      for (let j = 0; j < i; j++) {
        result += `${j + start} `
      }

      start += i
      result += '\n'
  }

  return result
}
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

### Вариант 2

{% capture code %}
console.log(pattern(5))

function pattern(n) {
  let result = '\n'

  let start = 1

  for (let i = 0; i < n; i++) {

    for (let j = 0; j <= i; j++) {
      result += `${start} `
      start += 1
    }

    result += '\n'
  }

  return result
}
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

<h2 id="pattern-18"><span class="attention">Паттерн</span> восемнадцать (буквенная лесенка вправо)</h2>

<pre>
A
AB
ABC
ABCD
ABCDE
</pre>

{% capture code %}
console.log(pattern(5))

function pattern(n) {
  let result = '\n'

  let start = 'A'.charCodeAt()

  for (let i = 0; i < n; i++) {

    for (let j = start; j <= start + i; j++) {
      result += String.fromCharCode(j)
    }

    result += '\n'
  }

  return result
}
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

<h2 id="pattern-19"><span class="attention">Паттерн</span> девятнадцать (перевернутая буквенная лесенка вправо)</h2>

<pre>
ABCDE
ABCD
ABC
AB
A
</pre>

{% capture code %}
console.log(pattern(5))

function pattern(n) {
  let result = '\n'

  let start = 'A'.charCodeAt()

  for (let i = n; i > 0; i--) {

    for (let j = start; j < start + i; j++) {
      result += String.fromCharCode(j)
    }

    result += '\n'
  }

  return result
}
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

<h2 id="pattern-20"><span class="attention">Паттерн</span> двадцать (буквенная лесенка вправо 2)</h2>

<pre>
A
BB
CCC
DDDD
EEEEE
</pre>

{% capture code %}
console.log(pattern(5))

function pattern(n) {
  let result = '\n'

  let start = 'A'.charCodeAt()

  for (let i = 0; i < n; i++) {

    for (let j = 0; j <= i; j++) {
      result += String.fromCharCode(start + i)
    }

    result += '\n'
  }

  return result
}
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

<h2 id="pattern-21"><span class="attention">Паттерн</span> двадцать один (буквенный треугольник)</h2>

<pre>
    A
   ABA
  ABCBA
 ABCDCBA
ABCDEDCBA
</pre>

{% capture code %}
console.log(pattern(5))

function pattern(n) {
let result = '\n'

let start = 'A'.charCodeAt()

for (let i = 0; i < n; i++) {

    for (let j = 0; j <= i; j++) {
      result += String.fromCharCode(start + i)
    }

    result += '\n'
}

return result
}
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

<h2 id="pattern-22"><span class="attention">Паттерн</span> двадцать два (буквенная лесенка вправо 3)</h2>

<pre>
E
D E
C D E
B C D E
A B C D E
</pre>

{% capture code %}
console.log(pattern(5))

function pattern(n) {
  let result = '\n'

  let ch = 'E'.charCodeAt()

  for (let i = 0; i < n; i++) {
    for (let j = ch - i; j <= ch; j++) {
      result += String.fromCharCode(j) + ' '
    }

    result += '\n'
  }

  return result
}
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

<h2 id="pattern-23"><span class="attention">Паттерн</span> двадцать три (внутренний ромб)</h2>

<pre>
**********
****  ****
***    ***
**      **
*        *
*        *
**      **
***    ***
****  ****
**********
</pre>

{% capture code %}
console.log(pattern(5))

function pattern(n) {
  let result = '\n'

  let space = 0

  for (let i = 0; i < n; i++) {
    for (let j = 1; j <= n - i; j++) {
      result += '*'
    }

    for (let j = 0; j < space; j++) {
      result += ' '
    }

    for (let j = 1; j <= n - i; j++) {
      result += '*'
    }

    result += '\n'
    space += 2
  }

  space = n * 2 - 2

  for (let i = 1; i <= n; i++) {
    for (let j = 1; j <= i; j++) {
      result += '*'
    }

    for (let j = 0; j < space; j++) {
      result += ' '
    }

    for (let j = 1; j <= i; j++) {
      result += '*'
    }

    result += '\n'
    space -= 2
  }

  return result
}
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

<h2 id="pattern-24"><span class="attention">Паттерн</span> двадцать четыре (бабочка)</h2>

<pre>
*        *
**      **
***    ***
****  ****
**********
****  ****
***    ***
**      **
*        *
</pre>

{% capture code %}
console.log(pattern(5))

function pattern(n) {
  let result = '\n'

  let space = n * 2 - 2

  for (let i = 1; i <= n * 2 - 1; i++) {
    let stars = i

    if (i > n) stars = n * 2 - i

    for (let j = 1; j <= stars; j++) {
      result += '*'
    }

    for (let j = 1; j <= space; j++) {
      result += ' '
    }

    for (let j = 1; j <= stars; j++) {
      result += '*'
    }

    if (i < n) space -= 2
    else space += 2

    result += '\n'
  }

  return result
}
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

<h2 id="pattern-25"><span class="attention">Паттерн</span> двадцать пять (прозрачный квадрат)</h2>

<pre>
******
*    *
*    *
*    *
*    *
******
</pre>

{% capture code %}
console.log(pattern(5))

function pattern(n) {
  let result = '\n'

  for(let i = 0; i < n; i++) {

    for(let j = 0; j < n; j++) {
      if(i === 0 || j === 0 || i === n - 1 || j === n - 1) {
        result += "*"
      } else {
        result += " "
      }
    }

    result += '\n'

  }

  return result
}
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

<h2 id="pattern-26"><span class="attention">Паттерн</span> двадцать шесть (матрица убывающих чисел)</h2>

<pre>
5 5 5 5 5 5 5 5 5
5 4 4 4 4 4 4 4 5
5 4 3 3 3 3 3 4 5
5 4 3 2 2 2 3 4 5
5 4 3 2 1 2 3 4 5
5 4 3 2 2 2 3 4 5
5 4 3 3 3 3 3 4 5
5 4 4 4 4 4 4 4 5
5 5 5 5 5 5 5 5 5
</pre>

{% capture code %}
console.log(pattern(5))

function pattern(n) {
  let result = '\n'

  for(let i = 0; i < n * 2 - 1; i++) {

    for(let j = 0; j < n * 2 - 1; j++) {
      let top = i
      let left = j
      let bottom = (n * 2 - 2) - i
      let right = (n * 2 - 2) - j

      result += (n - Math.min(Math.min(top, bottom), Math.min(left, right))) + ' '
    }

    result += '\n'

  }

  return result
}
{% endcapture %}
{% include component/code.html lang='bash' content=code %}
