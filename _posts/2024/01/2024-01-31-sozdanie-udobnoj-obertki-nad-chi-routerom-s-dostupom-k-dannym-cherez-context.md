---
layout: post
title: Создание удобной обертки над Chi роутером с доступом к данным через context
date: 2024-01-31 07:00 +0600
description: В этой статье создадим обертку над <b>Chi</b> роутером, которая позволит получать данные через контекст как во всех современных фреймворках на языке <b>Golang</b>.
image: cover.jpg
alt: Основное изображение
category: backend
tags: [golang, chi, context]
source: https://github.com/eliofery/chix-router
published: true
sitemap: true
excerpt_separator: "<!--more-->"
---

{{ page.description }}

<!--more-->

## <span class="attention">Оглавление</span> статьи

1. [Вводная часть](#intro)
2. [Подготовительные работы](#prepare)
3. [Создание роутера](#router)
4. [Создание контекста](#context)
5. [Создание роутера продолжение](#router-2)
6. [Использование сторонних middleware](#middleware)
7. [Подведем итоги](#end)

<h2 id="intro"><span class="attention">Вводная</span> часть</h2>

В работе со стандартным роутером в **Golang**, обработчикам в качестве параметров передается запрос, ответ:

{% capture code %}
// http
func Handler(w http.ResponseWrite, r *http.Request) {
  // code
}
{% endcapture %}
{% include component/code.html lang='golang ' content=code %}

В работе с различными фреймворками на языке **Golang**, при создании обработчиков используется паттерн, где в качестве параметра передается **context**, например:

{% capture code %}
// Gin
func Handler(ctx *gin.Context) {
  // code
}

// Fiber
func Handler(ctx *fiber.Ctx) error {
  // code
}
{% endcapture %}
{% include component/code.html lang='golang ' content=code %}

Подход с контекстом является более гибким, он позволяет передавать помимо стандартного запроса, ответа еще и другие необходимые данные.

В этой статье мы создадим обертку, которая позволит создавать аналогичный с приведенными примерами обработчик.

{% capture code %}
// Chix
func Handler(ctx *chix.Ctx) error {
  // code
}
{% endcapture %}
{% include component/code.html lang='golang ' content=code %}

<h2 id="prepare"><span class="attention">Подготовительные</span> работы</h2>

Первым делом необходимо инициализировать наш пакет (не забудьте изменить ссылку на репозиторий и название пакета на свои):

{% capture code %}
go mod init github.com/eliofery/go-chix
{% endcapture %}
{% include component/code.html lang='bash ' content=code %}

Далее установим пакет роутера **Chi** на основе которого мы будем создавать свою обертку:

{% capture code %}
go get -u github.com/go-chi/chi/v5
{% endcapture %}
{% include component/code.html lang='bash ' content=code %}

После проделанных манипуляций создастся файл **go.mod**, примерно со следующим содержимым:

{% capture code %}
module github.com/eliofery/go-chix

go 1.21.5

require github.com/go-chi/chi/v5 v5.0.12 // indirect
{% endcapture %}
{% include component/code.html lang='go.mod ' content=code %}

<h2 id="router"><span class="attention">Создание</span> роутера</h2>

Начнем с создания обертки над **Chi** роутером.

В корне проекта создадим файл **router.go**:

{% capture code %}
touch router.go
{% endcapture %}
{% include component/code.html lang='bash ' content=code %}

Внутри созданного файла создадим структуру нашего будущего роутера:

{% capture code %}
package chix

import "github.com/go-chi/chi/v5"

// Router обертка над chi роутером
type Router struct {
  *chi.Mux
}
{% endcapture %}
{% include component/code.html lang='router.go' content=code %}

В структуре нашего роутера мы используем ***chi.Mux** которая является **Chi** роутером.

Далее создадим конструктор для нашей структуры **Router**:

{% capture code %}
// NewRouter создание роутера
func NewRouter() *Router {
  return &Router{
    Mux: chi.NewRouter(),
  }
}
{% endcapture %}
{% include component/code.html lang='router.go' content=code %}

При создании нашего роутера создается новый роутер **Chi**, который мы будем использовать в описании своего обработчика.

В корне проекта создадим каталог **_example** и файл **main.go** внутри него:

{% capture code %}
mkdir _example
touch _example/main.go
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

Со следующим содержимым:

{% capture code %}
package main

import "github.com/eliofery/go-chix"

func main() {
  // Инициализация нашего роутера
  route := chix.NewRouter()
}
{% endcapture %}
{% include component/code.html lang='main.go' content=code %}

Теперь когда роутер, который является оберткой над роутером **Chi** создан, мы можем приступить к описанию методов.

### Метод Get

Первый метод который мы опишем, будет метод **Get**. Рассмотрим подробнее, что происходит:

{% capture code %}
// Get запрос на получение данных
// Обертка над методом Get у Chi роутера
// В качестве параметров мы так же используем path для определении маршрута,
// но далее мы переопределяем стандартный обработчик func(w http.ResponseWriter, r *http.Request) на handler Handler.
// Мы еще не описывали тип Handler, это будет сделано далее в статье.
func (rt *Router) Get(path string, handler Handler) {
  // Здесь мы вызываем стандартный метод Get у Chi роутера в обработчик которого в качестве логики
  // прописываем вызов приватного метода handler нашей структуры Router.
  // Далее мы разберем что такое handler Handler и rt.handler.
	rt.Mux.Get(path, func(w http.ResponseWriter, r *http.Request) {
		rt.handler(handler, w, r)
	})
}
{% endcapture %}
{% include component/code.html lang='router.go' content=code %}

### Handler и rt.handler

Создадим тип Handler, прописав его в самом верху файла **router.go**:

{% capture code %}
// Handler обработчик
type Handler func(ctx *Ctx) error
{% endcapture %}
{% include component/code.html lang='router.go' content=code %}

Как вы могли заметить это тот самый обработчик, который похож на обработчики используемые в фреймворках.

Опишем приватный метод **handler** структуры **Router**, который принимает в качестве параметров, созданный выше тип **Handler**.

{% capture code %}
// handler запускает обработчик роутера
func (rt *Router) handler(handler Handler, w http.ResponseWriter, r *http.Request) {
  // При создании типа Handler мы прописали, что Handler является функцией, которая
  // принимает в качестве параметра контекст и возвращает ошибку: type Handler func(ctx *Ctx) error
  // Данной строкой мы создаем тот самый контекст, который принимает функция типа Handler
  // Мы еще не описывали внутреннюю логику создания контекста, это будет сделано далее.
  ctx := NewCtx(w, r)

  // Важно понимать что handler это тот самый обработчик нашего маршрута, который при использовании стандартного роутера
  // пакета http принимал в качестве параметров w http.ResponseWrite и r *http.Request.
  // Но сейчас наш handler принимает контекст.
  // Выше мы создали контекст ctx := NewCtx(w, r)
  // Теперь мы передаем, созданный контекст в наш обработчик handler(ctx).
  // Сразу не отходя от кассы мы проверяем была ли ошибка,
  // так как наш обработчик должен возвращать ошибку: func(ctx *Ctx) error
  // Если обработчик вернет ошибку мы формируем JSON ответ в который передаем значения
  // success (успешен ли запрос) и message (текст ошибки).
  // Мы так же пока не знакомы с содержимым метода JSON, далее в статье мы непременно его напишем.
  if err := handler(ctx); err != nil {
    err = ctx.JSON(Map{
      "success": false,
      "message": err.Error(),
    })

    // При отправки ответа так же может произойти ошибка, поэтому метод JSON так же возвращает ошибку.
    // Если ошибка произошла, то мы отображаем ее стандартным выводом пакета http.
    if err != nil {
      http.Error(ctx.ResponseWriter, "Не предвиденная ошибка", http.StatusInternalServerError)
    }
  }
}
{% endcapture %}
{% include component/code.html lang='router.go' content=code %}

### Пример использования метода Get

Прежде чем идти дальше и описывать код контекста **(NewCtx)**. Для лучшего понимания происходящего напишем обработчик для, созданного метода **Get**.

В файле **_example/main.go** к уже имеющемуся коду добавим:

{% capture code %}
  route.Get("/profile", func(ctx *chix.Ctx) error {
    // some code

    return nil
  })
{% endcapture %}
{% include component/code.html lang='main.go' content=code %}

**Теперь можно проследить логику:**

1. Создается маршрут **profile** для метода **Get**, с некоторым обработчиком **func(ctx \*chix.Ctx) error**.
2. При создании маршрута вызывается метод обертка **Get**. Который вызывает стандартный метод **Get** у роутера **Chi**. В данном методе прописан вызов приватного метода **handler** нашего **Chix** роутера.
3. В приватный метод **handler** передается обработчик маршрута **func(ctx \*chix.Ctx) error**.
4. Приватный метод **handler** создает новый контекст.
5. Вызывается переданный обработчик как **callback** функция, которая принимает в качестве параметра, созданный контекст **handler(ctx)**.
6. При вызове обработчика исполняется его внутренний код **func(ctx \*chix.Ctx) error { return nil }** и возвращается ошибка.
7. При ошибке отправляется **JSON** ответ с сообщением об ошибке.
8. Если при отправке **JSON** ответа произошла ошибка, то ошибка отобразится стандартным способом отображения ошибок пакета **http**.

**На данный момент мы создали:**

- type Handler func(ctx *Ctx) error
- type Router struct / func NewRouter() *Router
- func (rt *Router) handler(handler Handler, w http.ResponseWriter, r *http.Request)
- func (rt *Router) Get(path string, handler Handler)

**Еще предстоит создать множество методов такие как:**

- Post
- Put
- Patch
- Delete
- NotFound
- MethodNotAllowed
- Use
- Group
- With
- Route
- Mount
- ServeHTTP
- Listen

Возможно это не все методы которые предоставляет **Chi** роутер, перечисленные методы это некая база часто используемых методов при работе с **Chi** роутером. Если обнаружится, что какой-то метод необходимый вам был пропущен, вы всегда сможете вокруг него создать обертку по аналогии с этой статьей.

Прежде чем продолжить создавать обертки для выше изложенных методов предлагаю разобраться с созданием контекста, который мы описывали ранее **ctx := NewCtx(w, r)**.

<h2 id="context"><span class="attention">Создание</span> контекста</h2>

В корне проекта создадим файл **context.go**:

{% capture code %}
touch context.go
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

Со следующим содержимым:

{% capture code %}
package chix

import "net/http"

// Map шаблон для передачи данных
// Данный тип уже был использован когда мы обрабатывали ошибку handler:
// Map{ "success": false, "message": err.Error() }
// Здесь мы его определяем.
type Map map[string]any

// Ctx контекст предоставляемый в обработчик
type Ctx struct {
  // Аналог w http.ResponseWrite
  http.ResponseWriter

  // Аналог r *http.Request
  *http.Request

  // Используется для middleware
  NextHandler http.Handler

  // Хранит статус ответа от сервера
  status int
}

// NewCtx создание контекста
// Здесь вместо обработчика сам контекст принимает запрос, ответ.
func NewCtx(w http.ResponseWriter, r *http.Request) *Ctx {
  return &Ctx{
    ResponseWriter: w,
    Request:        r,

    // Здесь мы получаем следующий обработчик в цепочке middleware
    // Мы еще не знакомились с middleware и внутренней логикой кода NextHandler, это будет сделанно позже.
    NextHandler: NextHandler(r.Context()),

    status: http.StatusOK,
  }
}
{% endcapture %}
{% include component/code.html lang='context.go' content=code %}

При описании обработки ошибки **handler(ctx)** мы использовали **ctx.JSON**, опишем этот метод и многие другие необходимые для создания Rest API:

{% capture code %}
// Status установка статуса ответа
func (ctx *Ctx) Status(status int) *Ctx {
  ctx.status = status
  return ctx
}

// Header установка заголовка
// Более компактная обертка для создания заголовков.
func (ctx *Ctx) Header(key, value string) {
  ctx.ResponseWriter.Header().Set(key, value)
}

// Decode декодирование тела запроса
// Декодирование json запроса.
func (ctx *Ctx) Decode(data any) error {
  if err := json.NewDecoder(ctx.Request.Body).Decode(data); err != nil {
    ctx.Status(http.StatusBadRequest)

    if errors.Is(err, io.EOF) {
      return errors.New("пустое тело запроса")
    }

    return errors.New("не корректный json")
  }

  return nil
}

// JSON формирование json ответа
// Отправка json ответа.
func (ctx *Ctx) JSON(data Map) error {
  ctx.Header("Content-Type", "application/json")
  ctx.WriteHeader(ctx.status)

  encoder := json.NewEncoder(ctx.ResponseWriter)
  encoder.SetIndent("", "  ")
  if err := encoder.Encode(data); err != nil {
    return err
  }

  return nil
}
{% endcapture %}
{% include component/code.html lang='context.go' content=code %}

Для лучшего понимая, что здесь произошло, вернемся к файлу **_example/main.go** и напишем следующий код:

{% capture code %}
// Данные запрос, которые ожидаем получить от клиента
type Request struct {
  Name string
  Age  int
}

// Данные ответа, которые хотим отправить клиенту
type Response struct {
  Date time.Time
}

route.Get("/profile", func(ctx *chix.Ctx) error {
  // Запрос
  var req Request
  if err := ctx.Decode(&req); err != nil {
    return ctx.Status(http.StatusBadRequest).JSON(chix.Map{
      "success": false,
      "message": err.Error(),
    })
  }

  // Некая бизнес логика
  var res Response
  res.Date = time.Now()

  // Ответ
  return ctx.JSON(chix.Map{
    "success": true,
    "message": "Время ответа",
    "data":    res,
  })
})
{% endcapture %}
{% include component/code.html lang='main.go' content=code %}

Как видим наш обработчик роутера становится похожим на привычные обработчики из любимых фреймворков.

<h2 id="router-2"><span class="attention">Создание</span> роутера продолжение</h2>

Настала пора вернуться к реализации описанных ранее недостающих методов для нашего роутера:

- Post
- Put
- Patch
- Delete
- NotFound
- MethodNotAllowed
- Use
- Group
- With
- Route
- Mount
- ServeHTTP
- Listen

Методы **Post**, **Put**, **Patch**, **Delete**, **NotFound**, **MethodNotAllowed** мало чем отличаются от созданного ранее метода **Get**. Поэтому я не буду вдаваться в подробности как они работают, просто опишу их логику:

{% capture code %}
// Post запрос на добавление данных
func (rt *Router) Post(path string, handler Handler) {
  rt.Mux.Post(path, func(w http.ResponseWriter, r *http.Request) {
    rt.handler(handler, w, r)
  })
}

// Put запрос на обновление всех данных
func (rt *Router) Put(path string, handler Handler) {
  rt.Mux.Put(path, func(w http.ResponseWriter, r *http.Request) {
    rt.handler(handler, w, r)
  })
}

// Patch запрос на обновление конкретных данных
func (rt *Router) Patch(path string, handler Handler) {
  rt.Mux.Patch(path, func(w http.ResponseWriter, r *http.Request) {
    rt.handler(handler, w, r)
  })
}

// Delete запрос на удаление данных
func (rt *Router) Delete(path string, handler Handler) {
  rt.Mux.Delete(path, func(w http.ResponseWriter, r *http.Request) {
    rt.handler(handler, w, r)
  })
}

// NotFound обрабатывает 404 статус
func (rt *Router) NotFound(handler Handler) {
  rt.Mux.NotFound(func(w http.ResponseWriter, r *http.Request) {
    rt.handler(handler, w, r)
  })
}

// MethodNotAllowed обрабатывает 405 статус
func (rt *Router) MethodNotAllowed(handler Handler) {
  rt.Mux.MethodNotAllowed(func(w http.ResponseWriter, r *http.Request) {
    rt.handler(handler, w, r)
  })
}
{% endcapture %}
{% include component/code.html lang='main.go' content=code %}

С остальными методами будет поинтересней.

### Метод Use

Метод **Use** добавляет промежуточное программное обеспечение **(middleware)**. Он пересекается с конструкцией **NextHandler http.Handler**, с которой мы столкнулись при создании структуры контекста:

{% capture code %}
type Ctx struct {
  ...
  NextHandler http.Handler
  ...
}
{% endcapture %}
{% include component/code.html lang='context.go' content=code %}

Ниже приведу код метода **Use**, а затем вернемся к **NextHandler**:

{% capture code %}
// Use добавляет промежуточное программное обеспечение
func (rt *Router) Use(middlewares ...Handler) {
  // Перебираем все полученные middleware
  for _, middleware := range middlewares {
    // Обязательно создаем отдельную переменную, хранящую текущий middleware
    // Это обусловлено особенностью самого языка golang, так как если не сохранить
    // текущую итерацию, то получать в обработчике будем всегда самую последнюю.
    currentMiddleware := middleware

    // Тут вызываем метод Use Chi роутера
    rt.Mux.Use(func(next http.Handler) http.Handler {
      return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        // Добавляем в текущий контекст следующий обработчик в цепочке middleware
        ctx := r.Context()
        ctx = WithNextHandler(ctx, next)

        // Вызываем метод handler, разобранный ранее в который передаем на этот раз
        // вместо обработчика роута, обработчик middleware.
        // Далее передаем ответ (w) и запрос (r), обратите внимание что r передается по особенному,
        // здесь мы переопределяем контекст в запросе роутера.
        rt.handler(currentMiddleware, w, r.WithContext(ctx))
      })
    })
  }
}
{% endcapture %}
{% include component/code.html lang='context.go' content=code %}

### Next Handler

Теперь давайте разберемся что же такое **WithNextHandler**, для этого в корне проекта создадим файл **next_handler.go**:

{% capture code %}
touch next_handler.go
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

Со следующим содержимым:

{% capture code %}
package chix

import (
  "context"
  "net/http"
)

// Тип key ключ контекста
type key string

// Значение ключа
const nextKey key = "next"

// WithNextHandler добавление следующего обработчика в цепочке middleware в контекст
func WithNextHandler(ctx context.Context, next http.Handler) context.Context {
  return context.WithValue(ctx, nextKey, next)
}

// NextHandler получение следующего обработчика в цепочке middleware из контекста
func NextHandler(ctx context.Context) http.Handler {
  val := ctx.Value(nextKey)

  next, ok := val.(http.Handler)
  if !ok {
    return nil
  }

  return next
}

{% endcapture %}
{% include component/code.html lang='next_handler.go' content=code %}

При регистрации **middleware** через метод **Use** мы добавляем в контекст через функцию **WithNextHandler** следующий обработчик и возвращаем получившийся контекст с добавленными данными.

При создании нового контекста **NewCtx(w, r)** мы передаем текущий контекст в функцию **NextHandler** для того, чтобы получить следующий в цепочке **middleware** обработчик и сохраняем его в свойстве **NextHandler** у структуры **Ctx**.

{% capture code %}
func NewCtx(w http.ResponseWriter, r *http.Request) *Ctx {
  return &Ctx{
    ...
    // Cохраняем следующий в цепочке **middleware** обработчик в свойстве **NextHandler**
    NextHandler: NextHandler(r.Context()),
    ...
  }
}
{% endcapture %}
{% include component/code.html lang='context.go' content=code %}

Чтобы полученная информация улеглась в голове, напишем тестовый **middleware**, чтобы на практике увидеть, то что мы описали.

В каталоге **_example** создадим файл **middleware**:

{% capture code %}
touch _example/middleware.go
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

Со следующим содержимым:

{% capture code %}
package main

import (
  "github.com/eliofery/go-chix"
)

// Example пример реализации middleware
// Мне нравится создавать middleware именно таким способом, а не напрямую например:
// func Example(ctx *chix.Ctx) error {}
// Так как в middleware при желании можно передать необходимые параметры например:
// Example(foo string, bar int) chix.Handler {}
// Затем внутри middleware ими воспользоваться.
func Example() chix.Handler {
  return func(ctx *chix.Ctx) error {
    // Некая логика
    // Если произошла ошибка, то возвращаем ошибку
    // и цепочка middleware будет прервана.
    if false {
      return errors.New("некая ошибка")
    }

    // При успешной логике вызываем следующий обработчик
    // в цепочке middleware
    return ctx.Next()
  }
}
{% endcapture %}
{% include component/code.html lang='middleware.go' content=code %}

Вы могли заметить, что метод **ctx.Next()** нам неизвестен, так как мы его еще не описывали. Самое время исправить этот момент, откроем файл **context.go** и добавим метод **Next**.

{% capture code %}
// Next обработка следующего обработчика
func (ctx *Ctx) Next() error {
  // ServeHTTP стандартный метод интерфейса http.Handler
  // Произойдет вызов следующего обработчика
  ctx.NextHandler.ServeHTTP(ctx.ResponseWriter, ctx.Request)

  return nil
}

{% endcapture %}
{% include component/code.html lang='context.go' content=code %}

Метод **Next** позволяет вызвать сохраненный **ctx.NextHandler** благодаря чему происходит обработка следующего **middleware** в цепочке.

С методом **Use** который регистрирует промежуточные программные обеспечения **(middleware)** покончено. Было не просто понадобится какое-то время, чтобы разобраться со всем, что здесь произошло, а я перехожу к реализации следующего метода.


### Метод With

Метод With используется для добавления middleware к группе маршрутов, что облегчает добавление одних и тех же middleware к нескольким маршрутам.

{% capture code %}
// With добавляет встроенное промежуточное программное обеспечение для обработчика конечной точки
func (rt *Router) With(middlewares ...Handler) *Router {
  // Так как стандартный метод With роутера Chi принимает множество обработчиков func(http.Handler) http.Handler
  // Необходимо смоделировать этот тип данных.
  var handlers []func(http.Handler) http.Handler

  // Далее код аналогичен тому, что мы прописывали в методе Use за исключением того,
  // что мы сохраняем обработчик в массиве, который создали выше
  for _, middleware := range middlewares {
    currentMiddleware := middleware

    handler := func(next http.Handler) http.Handler {
      return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        ctx := r.Context()
        ctx = WithNextHandler(ctx, next)

        rt.handler(currentMiddleware, w, r.WithContext(ctx))
      })
    }

    handlers = append(handlers, handler)
  }

  // Создаем новый роутер используя переданные middlewares
  return &Router{
    Mux: rt.Mux.With(handlers...).(*chi.Mux),
  }
}
{% endcapture %}
{% include component/code.html lang='router.go' content=code %}

Опишем пример использования метода **With**, для этого откроем файл **_example/main.go** и добавим:

{% capture code %}
route.With(Example()).Route("/group", func(r *chix.Router) {
  r.Get("/route1", func(ctx *chix.Ctx) error { return nil })
  r.Get("/route2", func(ctx *chix.Ctx) error { return nil })
})
{% endcapture %}
{% include component/code.html lang='main.go' content=code %}

Мы еще не определили метод **Route** давайте этим и займемся.

### Метод Route

Метод **Route** создает вложенность роутеров.

{% capture code %}
// Route создает вложенность роутеров
func (rt *Router) Route(pattern string, fn func(r *Router)) *Router {
  // Создаем дочерний роутер
  subRouter := &Router{
    Mux: chi.NewRouter(),
  }

  // Передаем его внутрь атрибута fn
  fn(subRouter)

  // Mount добавляет вложенность роутеров друг в друга.
  // В данном случае мы вкладываем внутрь родительского роута rt дочерний роут subRouter.
  // Мы еще не описывали метод Mount, займемся этим позже.
  rt.Mount(pattern, subRouter)

  return subRouter
}
{% endcapture %}
{% include component/code.html lang='main.go' content=code %}

После добавления метода **Route** текстовый редактор должен перестать ругаться на пример описанный ваше в файле **main.go**.

### Метод Mount

Метод Mount добавляет вложенность роутеров друг в друга.

{% capture code %}
// Mount добавляет вложенность роутеров
func (rt *Router) Mount(pattern string, router *Router) {
  // Здесь мы так же создаем обертку для того, чтобы использовать нашу структуру роутера
  // вместо Chi роутера.
  rt.Mux.Mount(pattern, http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
    // Запускаем наш роут
    router.Mux.ServeHTTP(w, r)
  }))
}
{% endcapture %}
{% include component/code.html lang='main.go' content=code %}

### Метод Group

Метод **Group** позволяет группировать роутеры.

{% capture code %}
// Group группирует роутеры
func (rt *Router) Group(fn func(r *Router)) *Router {
  // Создаем роут с привязкой middlewares
  // Возможно можно было ограничиться лишь переменной rt, например: fn(rt),
  // но я не уверен, что это сработает, так как сам Chi использует подход
  // с созданием роута через метод With.
	im := rt.With()

	if fn != nil {
		fn(im)
	}

	return im
}
{% endcapture %}
{% include component/code.html lang='router.go' content=code %}

Львиная часть методов позади осталось всего нечего.

### Метод ServeHTTP

Метод ServeHTTP возвращает весь пул роутеров.

{% capture code %}
// ServeHTTP возвращает весь пул роутеров
func (rt *Router) ServeHTTP() http.HandlerFunc {
  // Здесь ни чего особенного просто возвращаем стандартный http.HandlerFunc
  return rt.Mux.ServeHTTP
}
{% endcapture %}
{% include component/code.html lang='router.go' content=code %}

### Метод Listen

Метод Listen запускает сервер. Является локомотивом нашего роутера без которого ни чего не заработает.

{% capture code %}
// Listen запускает сервер
// Реализация: https://github.com/go-chi/chi/blob/master/_examples/graceful/main.go
func (rt *Router) Listen(addr string) error {
  // Создаем сервер
  server := &http.Server{
    Addr:    addr,
    Handler: rt.ServeHTTP(),
  }

  // Подписываемся на сигналы операционной системы, в данном случе на сигнал os.Interrupt,
  // который вызывается ОС при нажатии на клавиши Ctrl + C.
  ctx, cancel := signal.NotifyContext(context.Background(), os.Interrupt)
  defer cancel()

  // Создаем канал для ошибок
  ch := make(chan error, 1)

  // Запускаем сервер в горутине
  go func() {
    if err := server.ListenAndServe(); err != nil {
      // Если возникла ошибка при запуске сервера отправляем ошибку в канал
      if !errors.Is(err, http.ErrServerClosed) {
        fmt.Printf("Не удалось запустить сервер: %s", err.Error())
        ch <- ctx.Err()
      }
    }
    close(ch)
  }()

  // Слушаем каналы
  select {
  // При ошибке запуска сервера
  case err := <-ch:
    panic(err)
  // При ошибки завершения работы сервера
  case <-ctx.Done():
    // Создаем таймер в течении 10 сек. сервер должен завершить свою работу при
    // Нажатии на Ctrl + C
    timeoutCtx, done := context.WithTimeout(context.Background(), time.Second*10)
    defer done()

    // Ловим завершение работы с сервером
    go func() {
      <-timeoutCtx.Done()
      if errors.Is(timeoutCtx.Err(), context.DeadlineExceeded) {
        fmt.Printf("Время корректного завершения работы истекло. Принудительный выход: %s", timeoutCtx.Err().Error())
      }
    }()

    // Завершаем работу с сервером
    if err := server.Shutdown(timeoutCtx); err != nil {
      fmt.Printf("Не удалось остановить сервер: %s", err.Error())
    }
  }

  return nil
}
{% endcapture %}
{% include component/code.html lang='router.go' content=code %}

<h2 id="middleware"><span class="attention">Использование</span> сторонних middleware</h2>

Мы разобрали как в рамках нашего роутера создается **middleware**. Но бывают моменты когда необходимо использовать готовые **middleware** под роутер **Chi** и тут возникает нюанс. Который разберем в данном разделе на примере **middleware Cors**.

Скачаем пакет **github.com/go-chi/cors**:

{% capture code %}
go get github.com/go-chi/cors
{% endcapture %}
{% include component/code.html lang='router.go' content=code %}

В каталоге **_example** создадим файл **cors.go** со следующим содержимым:

{% capture code %}
package main

import (
	"github.com/eliofery/go-chix"
	"github.com/go-chi/cors"
)

const defaultCorsMaxAge = 3600 // 1 час

// Cors настройки межсайтового взаимодействия
// Пример: https://github.com/go-chi/cors?tab=readme-ov-file#usage
func Cors() chix.Handler {
  // Используем обработчик нашего роутера
  return func(ctx *chix.Ctx) error {
    // Создаем Cors обработчик сохраняя его в переменной
    corsHandler := cors.Handler(cors.Options{
      AllowedOrigins:   []string{"http://localhost:3000"},
      AllowedMethods:   []string{"GET", "POST", "PUT", "PATCH", "DELETE", "OPTIONS"},
      AllowedHeaders:   []string{"Origin", "Accept", "Authorization", "Content-Type", "X-CSRF-Token"},
      ExposedHeaders:   []string{"Link", "Content-Length", "Access-Control-Allow-Origin"},
      AllowCredentials: true,
      MaxAge:           defaultCorsMaxAge,
    })

    // Передаем в cors обработчик следующий обработчик и выполняем его.
    // Далее если внутри логики cors возникнет ошибка corsHandler сам прервет цепочку middleware.
    // Нам нет надобности вызывать ctx.Next()
    corsHandler(ctx.NextHandler).ServeHTTP(ctx.ResponseWriter, ctx.Request)

    return nil
  }
}
{% endcapture %}
{% include component/code.html lang='router.go' content=code %}

Вот таким нехитрым образом мы подружили стандартный **Chi middleware** с нашим роутером.

<h2 id="end"><span class="attention">Подведем</span> итоги</h2>

В этой статье мы создали свою обертку над **Chi** роутером благодаря которой наш опыт использования этого маршрутизатора будет похож на использование привычных фреймворков на подобии **gin** и **fiber**.

### Ссылки на проект

- [Исходный код](https://github.com/eliofery/chix-router){:target="_blank" rel="nofollow"}
- [Фреймворк на базе Chix роутера](https://github.com/eliofery/go-chix){:target="_blank" rel="nofollow"}
