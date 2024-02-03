---
layout: post
title: Создание сервера Golang с gRPC и Rest API используя Swagger
date: 2024-02-01 07:00 +0600
description: В этой статье затронем такие темы как <b>gRPC</b>, <b>Rest API</b>, <b>Swagger</b>, <b>Protobuf</b>, <b>валидация данных</b> и создадим веб сервер используя эти технологии.
image: cover.jpg
alt: Основное изображение
category: backend
tags: [golang, gRPC, rest, swagger, protobuf, validate]
source:
published: true
sitemap: true
excerpt_separator: "<!--more-->"
---

{{ page.description }}

<!--more-->

## <span class="attention">Оглавление</span> статьи

1. [Вводная часть](#intro)
2. [Подготовительные работы](#start)
3. [Протокол Protobuf](#protobuf)
4. [Фреймворк gRPC](#grpc)
5. [Плагин gRPC-Gateway](#gateway)
6. [Описываем proto файлы](#proto)
7. [Генерируем код](#generate)
8. [Валидация данных](#validate)
9. [OpenAPI (Swagger)](#swagger)
10. [Плагин Buf](#buf)
11. [Запускаем gRPC и REST сервер](#server)
12. [Подведем итоги](#end)

<h2 id="intro"><span class="attention">Вводная</span> часть</h2>

В сфере веб-разработки существует несколько стратегий взаимодействия с клиентами. Одни из самых распространенных это **gRPC** и **REST API**. В нашей статье мы сосредоточимся на создании сервера на **Golang**, который способен обслуживать оба этих метода взаимодействия с клиентами. Кроме того, мы рассмотрим использование **Swagger** для создания документации и проведения тестирования нашего **API**.

<h2 id="start"><span class="attention">Подготовительные</span> работы</h2>

Создадим отправную точку для нашего проекта:

{% capture code %}
# Создание модуля проекта
go mod init github.com/eliofery/golang-grpc

# Создание исполняемого файла
mkdir -p cmd/grpc && touch cmd/grpc/main.go

# Создание файла настроек редактора
touch .editorconfig

# Создание файла .gitignore
touch .gitignore

# Создание файла настроек для пакета modd
touch modd.conf

# Создание файла команд проекта
touch Makefile
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

### Файл main.go

Содержимое файла **main.go**:

{% capture code %}
package main

func main() {
  // code
}
{% endcapture %}
{% include component/code.html lang='main.go' content=code %}

### Файл .editorconfig

Содержимое файла **.editorconfig**:

{% capture code %}
root = true

[*]
charset = utf-8
indent_style = space
indent_size = 4
end_of_line = lf
trim_trailing_whitespace = true
insert_final_newline = true

[*.{yml,yaml}]
indent_size = 2

[*.md]
trim_trailing_whitespace = false
{% endcapture %}
{% include component/code.html lang='.editorconfig' content=code %}

### Файл .gitignore

Содержимое файла **.gitignore**:

{% capture code %}
.idea
bin
{% endcapture %}
{% include component/code.html lang='.gitignore' content=code %}

### Файл Makefile

Содержимое файла **Makefile**:

{% capture code %}
# Автоматическая сборка проекта при изменении файлов
watch:
  modd

# Сборка проекта
build:
  go build -o bin/grpc cmd/grpc/main.go
{% endcapture %}
{% include component/code.html lang='Makefile' content=code %}

### Файл modd.conf

**Modd** позволяет автоматически компилировать **Golang** проект при изменении файлов, более подробно об этой возможности была [статья]({{ site.baseurl }}/blog/2024-01-21-avtomaticheskaya-kompilyaciya-golang-proekta-pri-izmenenii-fajlov-backend.html){:target="_blank"} ранее.

Содержимое файла **modd.conf**:

{% capture code %}
# https://github.com/cortesi/modd
# go install github.com/cortesi/modd/cmd/modd@latest

# Проверка всех тестов при первой запуске
**/*.go {
  prep: go test @dirmods
}

# Автоматическая компиляция проекта при изменении файлов
# Исключает все тестовые файлы *_test.go
**/*.go !**/*_test.go {
  prep: go build -o ./bin/grpc ./cmd/grpc/main.go
  daemon +sigterm: ./bin/grpc
}
{% endcapture %}
{% include component/code.html lang='modd.conf' content=code %}

<h2 id="protobuf"><span class="attention">Протокол</span> Protobuf</h2>

Протокол буферизации сообщений **Protobuf** представляет собой мощный инструмент для сериализации структурированных данных, который широко используется в различных областях разработки программного обеспечения. Созданный в **Google**, **Protobuf** обеспечивает эффективную передачу данных между приложениями, облегчая процесс коммуникации и сокращая размер передаваемых сообщений.

Ознакомиться с данным протоколом можно на [официальном сайте](https://protobuf.dev/getting-started/gotutorial){:target="_blank" rel="nofollow"} документации **Protobuf**.

Для использования **Protobuf** необходимо его установить, инструкция по установке доступна на [официальном сайте](https://grpc.io/docs/protoc-installation){:target="_blank" rel="nofollow"} **gRPC** документации.

Устанавливать **Protobuf** я буду на **Linux** дистрибутив **PopOS!**, поэтому приведенные примеры будут касаться среды **Linux**. Если вы пользуетесь **Windows** или **Mac**, то поищите примеры установки самостоятельно, например в выше изложенной документации. Кстати в блоге есть [серия статей]({{ site.baseurl }}/blog/2023-08-19-linux-dlya-nachinayushhih-chast-1-ustanovka-linux.html){:target="_blank"} про работу с дистрибутивом **PopOS!** с уклоном в веб разработку.

{% capture code %}
// Стандартный вариант установки
// Плюсы: ни чего дополнительно устанавливать не надо
// Минусы: устанавливается устаревшая версия
sudo apt update
sudo apt install -y protobuf-compiler

// Вариант установки через Snap пакет
// Плюсы: актуальная версия
// Минусы: нужно установить snap
sudo apt update
sudo apt install snapd
sudo snap install protobuf --classic

// Вариант со скачиванием бинарного файла
// Скачиваете последнюю версию protobuf с официального репозитория
// https://github.com/protocolbuffers/protobuf/releases
// И добавьте путь до бинарного файла protobuf в переменные среды,
// либо разместите бинарный файл в каталоге /usr/local/bin
// Плюсы: актуальная версия
// Минусы: сложность установки

// Для проверки работоспособности вводим команду
protoc --version
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

При успешной установке должна отобразиться текущая версия **protobuf**, например: **libprotoc 3.14.0**.

<h2 id="grpc"><span class="attention">Фреймворк</span> gRPC</h2>

**gRPC** — это современная высокопроизводительная платформа от компании **Google**, основанная на протоколе удаленного вызова процедур **RPC**. **gRPC** использует **Protocol Buffers (protobuf)** и **HTTP2**.

Выше мы провели подготовительные действия для работы с **Protobuf** настала пора установить плагины **gRPC** и **protobuf**. Более подробное описание по установке доступно на [официальном сайте](https://grpc.io/docs/languages/go/quickstart){:target="_blank" rel="nofollow"}.

Вводим в терминале следующие команды:

{% capture code %}
go get google.golang.org/protobuf/cmd/protoc-gen-go
go get google.golang.org/grpc/cmd/protoc-gen-go-grpc

go install google.golang.org/protobuf/cmd/protoc-gen-go
go install google.golang.org/grpc/cmd/protoc-gen-go-grpc
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

**protoc-gen-go** - это плагин для компилятора **Protobuf** файлов с расширением **protoc**, который генерирует **Golang** код на основе определений сообщений и сервисов, написанных на языке **protobuf**. Позволяет создавать эффективный и легко читаемый код на **Golang** для работы с данными, определенными в файлах **Protobuf**.

**protoc-gen-go-grpc** - это плагин также является частью инструментария **Protobuf** и предназначен для генерации кода на **Golang** для реализации **gRPC** серверов и клиентов. Он расширяет функциональность **protoc-gen-go**, добавляя возможность генерации кода для обработки **RPC** (удаленных процедурного вызова) с использованием протокола **gRPC**.

<h2 id="gateway"><span class="attention">Плагин</span> gRPC-Gateway</h2>

На данный момент **фронтенд** напрямую не умеет взаимодействовать с **gRPC** сервером, но его можно подружить благодаря **gRPC-Gateway** плагину. **gRPC-Gateway** считывает определение службы **gRPC** и создает обратный прокси-сервер, который преобразует **Rest API** в **gRPC**.

Более подробно ознакомиться с этим плагином можно на его [официальном сайте](https://grpc-ecosystem.github.io/grpc-gateway){:target="_blank" rel="nofollow"}.

Для установки **gRPC-Gateway** вводим в терминале следующие команды:

{% capture code %}
go get github.com/grpc-ecosystem/grpc-gateway/v2/protoc-gen-grpc-gateway

go install github.com/grpc-ecosystem/grpc-gateway/v2/protoc-gen-grpc-gateway
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

**protoc-gen-grpc-gateway** - это инструмент позволяет генерировать код на **Golang** для обработки **HTTP** запросов к **gRPC** серверу. Он автоматически создает обработчики **HTTP**, которые преобразуют входящие запросы в вызовы **RPC** и обратно, что облегчает создание **Rest API** на основе существующего **gRPC API**. Это удобно для клиентов, которые предпочитают взаимодействовать с сервером через **HTTP**, а также для интеграции с существующими веб-инфраструктурами.

<h2 id="proto"><span class="attention">Описываем</span> proto файлы</h2>

После подготовительных работ, связанных с установкой необходимых пакетов и плагинов настала пора приступить к созданию и описанию **proto** файлов.

В корне проекта создадим следующие каталоги:

{% capture code %}
mkdir -p api/microservice/v1
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

В каталоге **api** будет храниться описание нашего **Api** в виде **proto** файлов. Каталог **microservice** название нашего сервиса, можете придумать свое название и **v1** версия нашего **Api**.

Внутри каталога **v1** создадим два файла:

{% capture code %}
touch api/microservice/v1/microservice.proto
touch api/microservice/v1/example.proto
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

В файле **microservice.proto** будут храниться "ручки", **rpc** правила взаимодействия клиент-сервера.

В файле **example.proto** описываются данные которые должны быть получены и отправлены при запросе, ответе.

Для каждой созданной "ручки" в файле **microservice.proto** необходимо будет создавать отдельный файл описывающий данные запроса и ответа, по аналогии с файлом **example.proto**. Можно было бы все описать в файле **microservice.proto**, но для наглядности и удобства лучше разделить **rpc** от **message**. Главное не забывать импортировать файлы с описанием запроса и ответа в файл **microservice.proto**, например **import "microservice/v1/example.proto";**.

Опишем содержимое файла **example.proto**.

{% capture code %}
// Указываем используемую версию нотации protobuf
syntax = "proto3";

// Имя нашего пакета по аналогии с .go файлами
// В названии пакета дублируем путь до самого файла proto, исключая название родительской директории (api)
// и изменяя слэш (/) точкой (.), пример:
// microservice/v1 -> microservice.v1
package microservice.v1;

// Файл proto это всего лишь описательная нотация, некий чертеж на основе которого
// необходимо сформировать программный код.
// Этой строкой указывается путь до каталога где будут размещены сгенерированные **Golang** файлы.
// Обратите внимание, что путь начинается с название нашего пакета, того самого которое мы задали при выполнении
// команды go mod init github.com/eliofery/golang-grpc, далее идут названия самих каталогов
option go_package = "github.com/eliofery/golang-grpc/pkg/microservice/v1";

// Запрос
// Описываем структуру данных которые планируем получить от клиента.
// Для понимания что к чему относится важно добавлять окончание Request к названию структуры.
message ExampleRequest {
  int32 number = 1;
}

// Ответ
// Описываем структуру данных которые планируем отправить клиенту.
// Для понимания что к чему относится важно добавлять окончание Response к названию структуры.
message ExampleResponse {
  string result = 1;
}
{% endcapture %}
{% include component/code.html lang='example.proto' content=code %}

Опишем содержимое файла **microservice.proto**.

{% capture code %}
syntax = "proto3";

package microservice.v1;

option go_package = "github.com/eliofery/golang-grpc/pkg/microservice/v1";

// Импортируем annotations.proto который необходим, чтобы описать rpc в rest нотации,
// для того, чтобы в дальнейшем можно было создать rest на основе rpc
import "google/api/annotations.proto";

// В файле example.proto мы создали пользовательские message ExampleRequest и ExampleResponse.
// Существуют подобные структуры которые Google описал за нас, ниже можно увидеть импорт
// часто используемых protobuf нотаций.

// empty.proto используется если необходимо получить или вернуть ни чего, например:
// rpc Example(google.protobuf.Empty) returns (google.protobuf.Empty)
// import "google/protobuf/empty.proto";

// timestamp.proto описывает тип данных, связанный с временем
// google.protobuf.Timestamp
// import "google/protobuf/timestamp.proto";

// Импортируем наш пользовательский message, созданный выше
import "microservice/v1/example.proto";

// Описываем наш api сервис
service Microservice {
  // Создаем "ручку", которая принимает данные описанные в ExampleRequest
  // и возвращает данные описанные в ExampleResponse
  rpc Example(ExampleRequest) returns (ExampleResponse) {
    // Это описание для Rest API, которое доступно благодаря annotations.proto
    option (google.api.http) = {
      // Ожидается POST запрос по адресу /example
      post: "/v1/example"
      // Тело запроса, может быть любым
      body: "*"
    };
  }
}
{% endcapture %}
{% include component/code.html lang='microservice.proto' content=code %}

Можно заметить, что **import "google/api/annotations.proto";** подсвечивается красным цветом в **IDE**. Так как редактор не может найти этот файл. Чтобы это исправить необходимо скачать нужный нам **annotations.proto** из [репозитория Google](https://github.com/googleapis/googleapis/tree/master/google/api){:target="_blank" rel="nofollow"}, а вместе с ним еще и **http.proto**, так как **annotations.proto** использует его внутри своего описания.

Чтобы скачать эти файлы, откроем **Makefile** и добавим в него следующее содержимое:

{% capture code %}
# Скачивание proto google
proto-google:
  curl https://raw.githubusercontent.com/googleapis/googleapis/974ad5bdfc9ba768db16b3eda2850aadd8c10a2c/google/api/annotations.proto --create-dirs -o api/google/api/annotations.proto
  curl https://raw.githubusercontent.com/googleapis/googleapis/974ad5bdfc9ba768db16b3eda2850aadd8c10a2c/google/api/http.proto --create-dirs -o api/google/api/http.proto
{% endcapture %}
{% include component/code.html lang='Makefile' content=code %}

Данные файлы находятся в [репозитории Google](https://github.com/googleapis/googleapis/tree/master/google/api){:target="_blank" rel="nofollow"}.

Выполним в терминале команду:

{% capture code %}
make proto-google
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

Внутри каталога **api** будет создан каталог **google/api** с двумя файлами **annotations.proto** и **http.proto**.

Я использую **IDE Goland** если оно продолжает подсвечивать красным цветом нотацию **import "google/api/annotations.proto";** нажмите горячие клавиши <kbd>Ctrl + Alt + S</kbd>. Откроется раздел настроек, перейдите в раздел **Languages & Frameworks** далее в **Protocol Buffers** и в разделе **Imports Paths** измените путь так чтобы **IDE** смотрело в каталог **api**, например: **home/eliofery/www/github/golang-grpc/api**.

Теперь **IDE** должен успешно подхватить все файлы **proto** и мы можем приступить к генерации кода.

<h2 id="generate"><span class="attention">Генерируем</span> код</h2>

Вспомним что в каждом нашем **proto** файле мы прописывали конструкцию **option go_package = "github.com/eliofery/golang-grpc/pkg/microservice/v1";**. Это путь куда будет генерироваться программный код нашего **Api** из описания **proto** файлов.

Для того чтобы сгенерировать программный код, откроем файл **Makefile** и добавим в него следующие команды:

{% capture code %}
# Название пакета
PACKAGE=$(shell awk 'NR==1 {print $$2}' go.mod)

# Генерация кода
generate:
  protoc -Iapi \
    --go_opt=module=$(PACKAGE) --go_out=. \
    --go-grpc_opt=module=$(PACKAGE) --go-grpc_out=. \
    --grpc-gateway_opt=module=$(PACKAGE) --grpc-gateway_out=. \
    api/microservice/v1/*.proto
{% endcapture %}
{% include component/code.html lang='Makefile' content=code %}

Рассмотрим подробнее, что здесь происходит.

**PACKAGE=$(shell awk 'NR==1 {print \$$2}' go.mod)** - данная строчка при запуске любой команды в **Makefile** создаст переменную в которую положит результат выполнения команды **shell awk 'NR==1 {print \$$2}' go.mod**. Данная команда прочитает файл **go.mod** и возьмет из него название модуля, в данном случае у меня это будет **github.com/eliofery/golang-grpc**. Данное значение пригодится при генерировании **proto** файлов в **go** код.

**protoc -Iapi** - запускаем плагин **protoc** который генерирует нотацию **proto** в программный код, **-Iapi** указываем путь до родительского каталога где хранятся наши **proto** файлы. Так же можно было бы прописать **\--proto_path=api** более наглядный вид. Если появится еще какой-нибудь каталог с **proto** файлами, его так же можно добавить, например: **-Iapi -Ifoobar**.

**\--go_opt=module=$(PACKAGE) \--go_out=.** - сгенерирует файлы **microservice.pb.go**, **example.pb.go** и разместит их по пути указанном в **option go_package = "github.com/eliofery/golang-grpc/pkg/microservice/v1";**. Часть **PACKAGE**, которая равно **github.com/eliofery/golang-grpc** будет откинута из **option go_package** останется только цепочка каталогов куда будет размещен сгенерированный файл, в нашем случе это каталог **pkg/microservice/v1** в корне проекта. Каталоги будут автоматически созданы при их отсутствии.

**\--go-grpc_opt=module=$(PACKAGE) \--go-grpc_out=.** - по аналогии с **\--go_opt** будет создан файл **microservice_grpc.pb.go**.

**\--grpc-gateway_opt=module=$(PACKAGE) \--grpc-gateway_out=.** - по аналогии с **\--go_opt** будет создан файл **microservice.pb.gw.go**.

**api/microservice/v1/*.proto** - указываем путь до файлов **proto** которые будут генерироваться в код.

Все сгенерированные файлы нужны будут в дальнейшем для написания логики веб сервера.

Выполним в терминале команду:

{% capture code %}
make generate
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

Как видим создалась следующая структура:

{% capture code %}
pkg/
└── microservice
    └── v1
        ├── example.pb.go
        ├── microservice_grpc.pb.go
        ├── microservice.pb.go
        └── microservice.pb.gw.go
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

<h2 id="validate"><span class="attention">Валидация</span> данных</h2>

При получении данных от клиента необходимо их провалидировать, в целях безопасности, да и в целом хороший тон получать то, что ожидаешь.

Для этого воспользуемся готовой **proto** нотацией [библиотеки protovalidate](https://github.com/bufbuild/protovalidate){:target="_blank" rel="nofollow"}. Скачаем ее уже знакомым нам способом, как это делали с **Google** **proto** файлами.

Добавим в **Makefile** новое правило:

{% capture code %}
# Скачивание proto validate
proto-validate:
  curl https://raw.githubusercontent.com/bufbuild/protovalidate/main/proto/protovalidate/buf/validate/validate.proto --create-dirs -o api/buf/validate/validate.proto
  curl https://raw.githubusercontent.com/bufbuild/protovalidate/main/proto/protovalidate/buf/validate/expression.proto --create-dirs -o api/buf/validate/expression.proto
  curl https://raw.githubusercontent.com/bufbuild/protovalidate/main/proto/protovalidate/buf/validate/priv/private.proto --create-dirs -o api/buf/validate/priv/private.proto
{% endcapture %}
{% include component/code.html lang='Makefile' content=code %}

Выполним в терминале команду:

{% capture code %}
make proto-validate
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

Внутри каталога **api** будет создан каталог **buf/validate** с файлами **validate.proto**, **expression.proto** и **priv/private.proto**.

С примерами использования **protovalidate** можно ознакомиться на [этой странице](https://github.com/bufbuild/protovalidate/tree/main/examples){:target="_blank" rel="nofollow"}.

Откроем файл **example.proto** и добавим в структуру сообщения, импорт **validate.proto** и два поля с описанием валидации:

{% capture code %}
import "buf/validate/validate.proto";

message ExampleRequest {
  int32 number = 1;
  string email = 2 [(buf.validate.field).string.email = true];
  string password = 3 [(buf.validate.field).string = {min_len: 8, max_len: 20}];
}
{% endcapture %}
{% include component/code.html lang='example.proto' content=code %}

Описание валидации происходит добавлением конструкции **[(buf.validate.field)]**, далее через цепочку значений указывается тип **(.string)** и правило валидации **(.email)**. При вводе **IDE** подсказывает доступные варианты, можно лишний раз не лезть в документацию или исходный код.

После описания **proto** файла напишем тестовый код который продемонстрирует работу валидатора. Но перед этим установим еще один пакет, который будет валидировать данные:

{% capture code %}
go get github.com/bufbuild/protovalidate-go
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

Откроем файл **cmd/grpc/main.go** и добавим в него следующее содержимое:

{% capture code %}
package main

import (
  "fmt"
  "github.com/bufbuild/protovalidate-go"
  pb "github.com/eliofery/golang-grpc/pkg/microservice/v1"
)

func main() {
  req := pb.ExampleRequest{
    Number: 42,
    Email: "test@mail.example",
    Password: "password",
  }

  v, err := protovalidate.New()
  if err != nil {
    fmt.Println("не удалось инициализировать валидатор:", err)
  }

  if err = v.Validate(&req); err != nil {
    fmt.Println("проверка не удалась:", err)
  } else {
    fmt.Println("проверка прошла успешно")
  }
}
{% endcapture %}
{% include component/code.html lang='main.go' content=code %}

Вызовем команду в терминале **make generate && make build**, запустим скомпилированный файл **bin/grpc** и получим сообщение **проверка прошла успешно**. При не корректном **email** либо **password** выведется примерно следующее сообщение об ошибке: **проверка не удалась: validation error: - email: value must be a valid email address [string.email]**.

<h2 id="swagger"><span class="attention">OpenAPI</span> (Swagger)</h2>

**OpenAPI (бывший Swagger)** — инструмент для разработки веб-сервисов, который позволяет описывать, создавать, консолидировать и визуализировать **API**. С его помощью разработчики могут создавать и поддерживать актуальную документацию к своим **API**, а также тестировать и отлаживать их.

Для установки **Openapiv2** вводим в терминале следующие команды:

{% capture code %}
go get github.com/grpc-ecosystem/grpc-gateway/v2/protoc-gen-openapiv2

go install github.com/grpc-ecosystem/grpc-gateway/v2/protoc-gen-openapiv2
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

**protoc-gen-openapiv2** - это инструмент генерирует файлы **OpenAPI** (ранее известные как **Swagger**) на основе определений **gRPC** сервисов. **OpenAPI** - это спецификация для описания **Rest API**, которая облегчает документирование и взаимодействие с **API**. Используя **protoc-gen-openapiv2**, разработчики могут автоматически создавать файлы **OpenAPI** для своих **gRPC** сервисов, что позволяет легко создавать документацию и выполнять различные операции тестирования и проверки соответствия.

Для генерации откроем файл **Makefile** и отредактируем содержимое команды **generate** следующим образом:

{% capture code %}
# Генерация кода
generate:
  mkdir -p pkg/microservice/v1
  protoc -Iapi \
    --go_opt=module=$(PACKAGE) --go_out=. \
    --go-grpc_opt=module=$(PACKAGE) --go-grpc_out=. \
    --grpc-gateway_opt=module=$(PACKAGE) --grpc-gateway_out=. \
    --openapiv2_out=allow_merge=true:./pkg/microservice/v1 \
    api/microservice/v1/*.proto
{% endcapture %}
{% include component/code.html lang='Makefile' content=code %}

И так, первым делом мы создаем каталог **pkg/microservice/v1**, в котором будут размещены все сгенерированные файлы. К их числу прибавится файл **apidocs.swagger.json**, содержащий схему нашего **API** на основе **gRPC** сервиса **Microservice**. Команда **allow_merge=true** означает, что все файлы **proto** будут объеденины в один общий файл **apidocs.swagger.json** и размещены по пути **./pkg/microservice/v1**.

Чтобы посмотреть сгенерированную **Swagger** схему в графическом, удобном виде нужно будет установить некоторые **Docker** образы. Для этого у вас должен быть установлен сам **Docker**. В блоге была [статья по установке Docker]({{ site.baseurl }}/blog/2023-08-21-linux-dlya-nachinayushhih-chast-3-soft-dlya-povsednevnyh-zadach-linux.html#docker){:target="_blank"} в среде **Linux**.

В корне проекта создадим файл **docker-compose.yml** c следующим содержимым:

{% capture code %}
version: "3.9"

services:
  swagger-editor:
    image: swaggerapi/swagger-editor
    container_name: "swagger-editor-container"
    ports:
      - "8085:8080"

  swagger-ui:
    image: swaggerapi/swagger-ui
    container_name: "swagger-ui-container"
    ports:
      - "8086:8080"
  volumes:
    - ./pkg/microservice/v1/apidocs.swagger.json:/v1.swagger.json
    - ./pkg/microservice/v2/apidocs.swagger.json:/v2.swagger.json
  environment:
    SWAGGER_JSON: /v1.swagger.json
    URLS: "[
      { url: 'v1.swagger.json', name: 'API Version 1.0'},
      { url: 'v2.swagger.json', name: 'API Version 2.0'},
    ]"
{% endcapture %}
{% include component/code.html lang='docker-compose.yml' content=code %}

В сервисе **swagger-ui** добавили версию **API 2.0** с заделом на будущее, позже мы его создадим. Параметр **SWAGGER_JSON** используется для выбора, прописанного в нем **API** при загрузке интерфейса **Swagger** в браузере. Параметр **URLS** позволяет переключаться в графическом интерфейсе браузера, между доступными версиями **API**.

Запустим прописанные в файле **docker-compose.yml** сервисы:

{% capture code %}
docker compose up -d
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

Будут подняты два виртуальных сервера по ссылкам **http://localhost:8085** и **http://localhost:8086**.

На порту **8085** будет располагаться редактор **Swagger** схемы куда можно будет скопировать содержимое файла **apidocs.swagger.json** и редактировать его в реальном времени.

На порту **8086** будет располагаться сама наша документация, прописанная в файле **apidocs.swagger.json**, где мы можем ознакомиться с ней в наглядном виде.

В целом автоматическая генерация **API** посредством **Swagger** на основании **protobuf** файлов готова, но можно ее сделать еще более информативной. Чтобы этого достичь нужно скачать **proto** файлы пакета **protoc-gen-openapiv2**, по аналогии с файлами **Google** и **Validate**.

Откроем файл **Makefile** и добавим в него новую команду:

{% capture code %}
# Скачивание proto openapiv2
proto-openapiv2:
  curl https://raw.githubusercontent.com/grpc-ecosystem/grpc-gateway/main/protoc-gen-openapiv2/options/annotations.proto --create-dirs -o api/protoc-gen-openapiv2/options/annotations.proto
  curl https://raw.githubusercontent.com/grpc-ecosystem/grpc-gateway/main/protoc-gen-openapiv2/options/openapiv2.proto --create-dirs -o api/protoc-gen-openapiv2/options/openapiv2.proto
{% endcapture %}
{% include component/code.html lang='Makefile' content=code %}

Выполним команду:

{% capture code %}
make proto-openapiv2
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

Создастся каталог **api/protoc-gen-openapiv2** со всему нужными нам **proto** файлами для детального описания **Swagger** документации. Откроем скаченный файл **openapiv2.proto**, в нем в комментариях отображены примеры того как можно описывать наше **API**. Я не буду застрять на этом внимание так как примеры довольно интуитивно понятны. Приступим сразу к реализации.

Откроем файл **microservice.proto** хранящий **rpc** нотации и добавим туда примеры из файла **openapiv2.proto**:

{% capture code %}
syntax = "proto3";

package microservice.v1;

option go_package = "github.com/eliofery/golang-grpc/pkg/microservice/v1";

// Импорт нотации openapiv2
import "protoc-gen-openapiv2/options/annotations.proto";

import "google/api/annotations.proto";
//import "google/protobuf/empty.proto";
//import "google/protobuf/timestamp.proto";

import "microservice/v1/example.proto";

// Основное описание API
option (grpc.gateway.protoc_gen_openapiv2.options.openapiv2_swagger) = {
  info: {
    title: "Echo API";
    version: "1.0";
    description: "";
    contact: {
      name: "gRPC-Gateway project";
      url: "https://github.com/grpc-ecosystem/grpc-gateway";
      email: "none@example.com";
    };
    license: {
      name: "BSD 3-Clause License";
      url: "https://github.com/grpc-ecosystem/grpc-gateway/blob/main/LICENSE";
    };
  };
  schemes: HTTPS;
  consumes: "application/json";
  produces: "application/json";
};

service Microservice {
  rpc Example(ExampleRequest) returns (ExampleResponse) {
    option (google.api.http) = {
      post: "/v1/example"
      body: "*"
    };

    // Описание для POST запроса /v1/example
    option (grpc.gateway.protoc_gen_openapiv2.options.openapiv2_operation) = {
      summary: "Get a message.";
      operation_id: "getMessage";
      tags: "echo";
      responses: {
        key: "201"
        value: {
          description: "OK Success";
        }
      }
    };
  }
}
{% endcapture %}
{% include component/code.html lang='microservice.proto' content=code %}

Я не буду подробно описывать каждую конструкцию так как при просмотре **API** в браузере все становится понятно и вы легко сможете разобраться в каких местах документации выводится тот или иной текст и изменить его под свои нужды. В файле **openapiv2.proto** так же имеются ссылки на документацию, где все эти правила досконально расписаны, загляните туда, чтобы ознакомиться с дополнительными возможностями.

Отредактируем так же файл **example.proto**, хранящий сообщения запроса и ответа, пример так же был взят из файла **openapiv2.proto**:

{% capture code %}
syntax = "proto3";

package microservice.v1;

option go_package = "github.com/eliofery/golang-grpc/pkg/microservice/v1";

// Импорт нотации openapiv2
import "protoc-gen-openapiv2/options/annotations.proto";
import "buf/validate/validate.proto";

message ExampleRequest {
    // Детальное описание запроса ExampleRequest в Swagger
    option (grpc.gateway.protoc_gen_openapiv2.options.openapiv2_schema) = {
        json_schema: {
            title: "A bit of everything"
            description: "Intentionaly complicated message type to cover many features of Protobuf."
            required: ["number", "email", "password"]
        }
        external_docs: {
            url: "https://github.com/grpc-ecosystem/grpc-gateway";
            description: "Find out more about ABitOfEverything";
        }
        example: "{\"number\": \"538\",\"email\": \"test@mail.org\",\"password\": \"123456\"}"
    };

    // Описываем детально поля
    int32 number = 1 [(grpc.gateway.protoc_gen_openapiv2.options.openapiv2_field) = {
        description: "The number of the simple message."
    }];
    string email = 2 [
        (buf.validate.field).string.email = true,
        (grpc.gateway.protoc_gen_openapiv2.options.openapiv2_field) = {
            description: "The email of the simple message."
        }
    ];
    string password = 3 [
        (buf.validate.field).string = {min_len: 8, max_len: 20},
        (grpc.gateway.protoc_gen_openapiv2.options.openapiv2_field) = {
            description: "The password of the simple message."
        }
    ];
}

message ExampleResponse {
    string result = 1;
}
{% endcapture %}
{% include component/code.html lang='example.proto' content=code %}

Вот таким довольно интересным способом можно подробно описать весь **API**.

<h2 id="buf"><span class="attention">Плагин</span> Buf</h2>

Перед тем как речь пойдем о **Buf** подредактируем файл **Makefile**.

На данный момент, чтобы полностью собрать наш проект необходимо выполнить поочередно команды в терминале:

{% capture code %}
make google-proto
make proto-validate
make proto-openapiv2
make generate
docker compose down && docker compose up -d
make build
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

Что не совсем удобно. Хорошо было бы собирать и приводить весь проект в исходное состояние одной командой. Для достижения этой цели отредактируем файл **Makefile** следующим образом:

{% capture code %}
# Название пакета
PACKAGE=$(shell awk 'NR==1 {print $$2}' go.mod)

# Автоматическая сборка проекта при изменении файлов
watch:
  modd

# Сборка проекта
build:
  make clean
  make proto-google && make proto-validate && make proto-openapiv2
  make generate
  docker compose down && docker compose up -d
  make bin

# Создание бинарника
bin:
  go build -o bin/grpc cmd/grpc/main.go

# Скачивание proto google
google-proto:
  curl https://raw.githubusercontent.com/googleapis/googleapis/974ad5bdfc9ba768db16b3eda2850aadd8c10a2c/google/api/annotations.proto --create-dirs -o api/google/api/annotations.proto
  curl https://raw.githubusercontent.com/googleapis/googleapis/974ad5bdfc9ba768db16b3eda2850aadd8c10a2c/google/api/http.proto --create-dirs -o api/google/api/http.proto

# Скачивание proto validate
proto-validate:
  curl https://raw.githubusercontent.com/bufbuild/protovalidate/main/proto/protovalidate/buf/validate/validate.proto --create-dirs -o api/buf/validate/validate.proto
  curl https://raw.githubusercontent.com/bufbuild/protovalidate/main/proto/protovalidate/buf/validate/expression.proto --create-dirs -o api/buf/validate/expression.proto
  curl https://raw.githubusercontent.com/bufbuild/protovalidate/main/proto/protovalidate/buf/validate/priv/private.proto --create-dirs -o api/buf/validate/priv/private.proto

# Скачивание proto openapiv2
proto-openapiv2:
  curl https://raw.githubusercontent.com/grpc-ecosystem/grpc-gateway/main/protoc-gen-openapiv2/options/annotations.proto --create-dirs -o api/protoc-gen-openapiv2/options/annotations.proto
  curl https://raw.githubusercontent.com/grpc-ecosystem/grpc-gateway/main/protoc-gen-openapiv2/options/openapiv2.proto --create-dirs -o api/protoc-gen-openapiv2/options/openapiv2.proto

# Генерация кода
generate:
  mkdir -p pkg/microservice/v1
  protoc -Iapi \
    --go_opt=module=$(PACKAGE) --go_out=. \
    --go-grpc_opt=module=$(PACKAGE) --go-grpc_out=. \
    --grpc-gateway_opt=module=$(PACKAGE) --grpc-gateway_out=. \
    --openapiv2_out=allow_merge=true:./pkg/microservice/v1 \
    api/microservice/v1/*.proto

# Очистка проекта
clean:
  rm -rf bin
  rm -rf pkg/microservice
  rm -rf api/buf
  rm -rf api/google
  rm -rf api/protoc-gen-openapiv2
{% endcapture %}
{% include component/code.html lang='Makefile' content=code %}

Теперь сборка проекта происходит командой **make build**, а возврат в исходное состояние командой **make clean**.

**Плагин Buf** - позволяет на основе заданных правил в конфигурационном файле **buf.gen.yml** генерировать **proto** файлы в программный **Golang** код. По сути он выполняет туже самую работу которую мы только, что описывали в файле **Makefile** для генерации кода, но делает это в более удобной и наглядной форме. Предоставляя при этом различные дополнительные фишки, которые мы не будем касаться в рамках этой статьи.

Подробная официальная документация по установке **Buf** доступна по [следующей ссылке](https://buf.build/docs/installation){:target="_blank" rel="nofollow"}. Я же воспользуюсь командами:

{% capture code %}
go install github.com/bufbuild/buf/cmd/buf
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

Выше было упомянуто, что плагин **Buf** генерирует **Golang** по прописанным правилам в конфигурационном файле **buf.gen.yml**. Более подробное объяснение вы можете найти в [официальной документации](https://buf.build/docs/generate/tutorial){:target="_blank" rel="nofollow"}.

И так, создадим в каталоге **api** файл **buf.gen.yml** со следующим содержимым:

{% capture code %}
# Версия нотации Buf
version: v1

# Перечень запускаемых плагинов
plugins:
  # Настройки плагина protoc-gen-go
  # Аналог записи в Makefile: --go_opt=module=$(PACKAGE) --go_out=.
  # name - название плагина
  # out - путь до каталога куда буду сохранены сгенерированные файлы
  # opt - перечисление опций данного плагина
  - name: go
    out: ../pkg
    opt:
      - paths=source_relative

  # Настройки плагина protoc-gen-go-grpc
  # Аналог записи в Makefile: --go-grpc_opt=module=$(PACKAGE) --go-grpc_out=.
  - name: go-grpc
    out: ../pkg
    opt:
      - paths=source_relative

  # Настройки плагина protoc-gen-grpc-gateway
  # Аналог записи в Makefile: --grpc-gateway_opt=module=$(PACKAGE) --grpc-gateway_out=.
  - name: grpc-gateway
    out: ../pkg
    opt:
      - paths=source_relative
{% endcapture %}
{% include component/code.html lang='buf.gen.yml' content=code %}

Если взглянуть на описание команды **generate** в файле **Makefile**, то там так же было добавлено правило **--openapiv2_out=allow_merge=true:./pkg/microservice/v1**. Оно кардинально отличается от правил описанных выше **\--go_opt, \--go-grpc_opt и т.д.** Дело в том, что почему-то разработчики плагина **protoc-gen-openapiv2** не добавили правило **\--openapiv2_opt** из-за чего пришлось хардкодить конечный путь генерации файла **Swagger** документации.

Мы бы могли в файле **buf.gen.yml** ниже остальных описаний так же добавить **openapiv2**, но это не было бы гибким решением при условии, что у нашего **API** когда-нибудь появится еще одна версия, например **V2**, а затем **V3**. Лучшем решением, было бы иметь возможность использовать правило **\--openapiv2_opt**, но как говорилось ранее нам его не завезли.

В общем, возможно тот пример который я вам приведу покажется кастыльным, но лучшего решения я пока не придумал. Для того чтобы иметь возможность генерировать разные файлы **apidocs.swagger.json** для каждой версии **API**, создадим внутри каталога **api/microservice/v1** еще один файл **buf.gen.yml** и пропишем в него:

{% capture code %}
version: v1

plugins:
  - name: openapiv2
    out: ../pkg/microservice/v1
    opt:
      - allow_merge=true
{% endcapture %}
{% include component/code.html lang='buf.gen.yml' content=code %}

Иными словами мы вынесли настройки плагина **openapiv2** в отдельный файл под конкретною версию **API**. Теперь в будущем если у нас появится новая версия апи **microservice/v2** мы сможем внутри файла **buf.gen.yml** для каждой версии **API** прописать свои настройки.

Для более наглядного примера продублируем каталог **microservice/v1** и назовем его **microservice/v2**. Файл **microservice/v2/example.proto** переименуем в **user.proto** со следующим содержимым:

{% capture code %}
syntax = "proto3";

package microservice.v2;

option go_package = "github.com/eliofery/golang-grpc/pkg/microservice/v2";

message UserRequest {
  string name = 1;
}

message UserResponse {
  int32 age = 1;
}
{% endcapture %}
{% include component/code.html lang='user.proto' content=code %}

А содержимое файла **microservice/v2/microservice.proto** изменим на:

{% capture code %}
syntax = "proto3";

package microservice.v2;

option go_package = "github.com/eliofery/golang-grpc/pkg/microservice/v2";

import "google/api/annotations.proto";
import "microservice/v2/user.proto";

service Microservice {
  rpc User(UserRequest) returns (UserResponse) {
    option (google.api.http) = {
      get: "/v2/user"
    };
  }
}
{% endcapture %}
{% include component/code.html lang='microservice.proto' content=code %}

Здесь я уже не стал добавлять **Swagger** нотацию описания документации, так как это не тема данного раздела, но если хотите, можете сделать это самостоятельно.

На данный момент мы имеем две версии **API V1** и **V2**. Опишем в файле **Makefile** вызов плагина **Buf** для генерации **proto** в **go**.

{% capture code %}
# Сборка проекта через Buf
buf:
  make clean
  make proto-google && make proto-validate && make proto-openapiv2
  make generate-buf
  docker compose down && docker compose up -d
  make bin

# Генерация кода через Buf
V1=microservice/v1
V2=microservice/v2
generate-buf:
  cd ./api \
  && buf generate --template buf.gen.yml --path $(V1) \
    && buf generate --template $(V1)/buf.gen.yml --path $(V1) \
  && buf generate --template buf.gen.yml --path $(V2) \
    && buf generate --template $(V2)/buf.gen.yml --path $(V2)
{% endcapture %}
{% include component/code.html lang='Makefile' content=code %}

Мы создаем отдельные переменные для каждой версии **API**, хранящие путь к **proto** файлам. Можно относиться к ним как к **$(PACKAGE)** в команде **generate**. Далее мы переходим в каталог **api** и внутри него генерируем сперва файл **buf.gen.yml** затем генерируем файл **microservice/v1/buf.gen.yml**. Аналогичным образом проделываем это для **API V2**.

Далее в файле **docker-compose.yml** в параметр **volumes** и **URLS** необходимо добавить новые версии **API**, чтобы они подхватились **Swagger** документацией.

{% capture code %}
swagger-ui:
  image: swaggerapi/swagger-ui
  container_name: "swagger-ui-container"
  ports:
    - "8086:8080"
  volumes:
    - ./pkg/microservice/v1/apidocs.swagger.json:/v1.swagger.json
    - ./pkg/microservice/v2/apidocs.swagger.json:/v2.swagger.json
  environment:
    SWAGGER_JSON: /v1.swagger.json
    URLS: "[
      {url: 'v1.swagger.json', name: 'API Version 1.0'},
      {url: 'v2.swagger.json', name: 'API Version 2.0'},
    ]"
{% endcapture %}
{% include component/code.html lang='docker-compose.yml' content=code %}

Выполним команду **make buf** в терминале, для того чтобы заново скачать и сгенерировать все файлы, а так же перезапустить виртуальный сервер **Swagger**. Теперь если перейти по ссылке [localhost:8086](http://localhost:8086/){:target="_blank" rel="nofollow"} в правом верхнем углу отобразится выпадающее меню с выбором доступных версий **API**.

<h2 id="server"><span class="attention">Запускаем</span> gRPC и REST сервер</h2>

Перед тем как начать писать код запуска **gRPC** и **REST** серверов приведем наши зависимости в порядок выполнив команду:

{% capture code %}
go mod tidy
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

Теперь файлы **go.mod** и **go.sum** стали заметно чище.

Откроем файл **cmd/grpc/main.go**, пример с валидацией данных нам не понадобится поэтому я полностью перепишу логику в данном файле.

{% capture code %}
package main

import (
  "context"
  "fmt"
  pb "github.com/eliofery/golang-grpc/pkg/microservice/v1"
  "github.com/grpc-ecosystem/grpc-gateway/v2/runtime"
  "google.golang.org/grpc"
  "google.golang.org/grpc/reflection"
  "net"
  "net/http"
)

// Создаем структуру, которая будет реализовывать rpc ручки, прописанные в файле microservice.proto.
// При генерации кода был создан файл microservice_grpc.pb.go, где описан MicroserviceServer interface который содержит,
// необходимые для реализации методы. Так же имеется UnimplementedMicroserviceServer struct, которая содержит реализацию
// методов, прописанных в MicroserviceServer interface. Описание методов в UnimplementedMicroserviceServer struct носит
// информационный характер в котором говорится что метод еще не реализован. Это нужно для того, чтобы сервер не паниковал,
// если какой-либо метод не реализован.
// И того, если нужно чтобы все методы строго были реализованы используйте MicroserviceServer interface иначе
// UnimplementedMicroserviceServer struct.
// Я буду использовать UnimplementedMicroserviceServer, чтобы не ждать когда логика всех методов будет реализована.
type MicroserviceServer struct {
  //pb.MicroserviceServer
  pb.UnimplementedMicroserviceServer
}

// Описываем реализацию метода Example, который является нашей rpc ручкой в файле microservice.proto.
// Откроем файл microservice_grpc.pb.go, найдем MicroserviceServer interface и скопируем все содержимое метода
// Example(context.Context, *ExampleRequest) (*ExampleResponse, error).
// Далее создадим реализацию метода Example для структуры MicroserviceServer, описанной выше.
func (s *MicroserviceServer) Example(ctx context.Context, req *pb.ExampleRequest) (*pb.ExampleResponse, error) {
  fmt.Println("Example rpc")

  // Получаем данные запроса от клиента
  data := pb.ExampleRequest{
    Number:   req.GetNumber(),
    Email:    req.GetEmail(),
    Password: req.GetPassword(),
  }

  // Некая бизнес логика
  _ = data

  // Возвращаем данные ответа от сервера
  return &pb.ExampleResponse{
    Result: "success",
  }, nil
}

func main() {
  // Создаем неблокирующий канал с буфером равным 2.
  // Сделано это, для того чтобы запустить сервер gRPC и REST в отдельных друг от друга горутинах.
  // И чтобы горутины не блокировали друг друга, а работали параллельно.
  ch := make(chan error, 2)

  // gRPC сервер
  go func(ch chan error) {
    // Создаем tcp соединение на порту 50051
    fmt.Println("gRPC server start :50051")
    listen, err := net.Listen("tcp", ":50051")
    if err != nil {
      ch <- err
    }

    // Создаем gRPC сервер
    grpcServer := grpc.NewServer()

    // Получаем все методы gRPC сервера. Нужно для того, чтобы программы для работы с API
    // на подобии Postman, без дополнительного импорта proto файлов знали какие методы доступны.
    reflection.Register(grpcServer)

    // Данная функция была сгенерирована в файле microservice_grpc.pb.go.
    // Она подвязывает к gRPC серверу структуру которая реализовывает логику rpc ручек.
    // Иначе говоря Handler (обработчики) по аналогии как это происходит в REST реализации.
    pb.RegisterMicroserviceServer(grpcServer, &MicroserviceServer{})

    // Запускаем gRPC сервер
    if err = grpcServer.Serve(listen); err != nil {
      ch <- err
    }
  }(ch)

  // REST сервер
  go func() {
    // Создаем мультиплексер реализованный в grpc-gateway.
    // Как говорилось ранее grpc-gateway это обратный прокси сервер, который переводит REST в GRPC.
    mux := runtime.NewServeMux()

    // Данная функция была сгенерирована в файле microservice_grpc.pb.go.
    // Она подвязывает к gRPC серверу структуру которая реализовывает логику rpc ручек.
    // Тоже самое что и pb.RegisterMicroserviceServer, но для REST.
    err := pb.RegisterMicroserviceHandlerServer(context.Background(), mux, &MicroserviceServer{})
    if err != nil {
      ch <- err
    }

    // Создаем сервер на порту 8080 и передаем ему мультиплексер
    server := http.Server{
      Addr:    ":8080",
      Handler: mux,
    }

    // Запускаем REST сервер
    fmt.Println("REST server start :8080")
    if err = server.ListenAndServe(); err != nil {
      ch <- err
    }
  }()

  // Слушаем каналы, если какой либо канал вернет ошибку, завершим работу сервера.
  for i := 0; i < 2; i++ {
    if err := <-ch; err != nil {
      panic(err)
    }
  }
}
{% endcapture %}
{% include component/code.html lang='main.go' content=code %}

<h2 id="end"><span class="attention">Подведем</span> итоги</h2>

В этой статье мы разобрали базовые, основополагающие моменты в реализации **gRPC** сервера. Научились описывать **Protobuf** файлы, генерировать их в программный код стандартным способом и через плагин **Buf**, делать **gRPC** сервер совместимым с **REST API**, валидировать данные, разделять **API** на разные версии, реализовывать **Swagger** документацию и наконец запускать сервера **gRPC** и **REST**.

### Ссылки на проект

- [Исходный код](#golang-grpc){:target="_blank" rel="nofollow"}
