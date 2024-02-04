---
layout: post
title: Кодогенерация Protobuf файлов используя плагин Buf
date: 2024-02-03 07:00 +0600
description: В этой статье научимся генерировать <b>Protobuf</b> файлы через плагин <b>Buf</b>. Настроим зависимости сторонних <b>proto</b> нотаций, такие как <b>googleapis</b>, <b>grpc-gateway</b> и <b>protovalidate</b>.
image: cover.jpg
alt: Основное изображение
category: backend
tags: [golang, gRPC, googleapis, grpc-gateway, protovalidate, protobuf, buf]
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
3. [Установка зависимостей](#deps)
4. [Структура api](#api)
5. [Генерируем код](#generate)
6. [Запускаем gRPC и REST сервер](#server)
7. [Подведем итоги](#end)

<h2 id="intro"><span class="attention">Вводная</span> часть</h2>

Данная статья является дополнением предыдущей статьи ["Создание сервера Golang с gRPC и Rest API используя Swagger"]({{ site.baseurl }}/blog/2024-02-01-sozdanie-servera-golang-s-grpc-i-rest-api-ispolzuya-swagger-backend.html){:target="_blank"}.

В прошлой статье зависимости проекта такие как **google/api/annotations.proto** и т.п. были скачены вручную, здесь же мы разберем как можно улучшить и автоматизировать данный процесс.

<h2 id="start"><span class="attention">Подготовительные</span> работы</h2>

Создадим отправную точку для нашего проекта:

{% capture code %}
# Создание модуля проекта
go mod init github.com/eliofery/golang-buf

# Создание файла команд проекта
touch Makefile
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

### Файл Makefile

Содержимое файла **Makefile**:

{% capture code %}
{% raw %}
# Путь до бинарных файлов
LOCAL_BIN=$(CURDIR)/bin

# Установка бинарных файлов различных пакетов.
# Плагины пакетов можно установить глобально выполнив команду go install [package].
# Но лучше сохранить их в каталоге проекта, зафиксировав используемые версии,
# чтобы не нарушить обратную совместимость при коллективной работе над проектом.
install-bin:
  GOBIN=$(LOCAL_BIN) go install github.com/bufbuild/buf/cmd/buf@v1.29.0
  GOBIN=$(LOCAL_BIN) go install google.golang.org/protobuf/cmd/protoc-gen-go@v1.28
  GOBIN=$(LOCAL_BIN) go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@v1.2
  GOBIN=$(LOCAL_BIN) go install github.com/grpc-ecosystem/grpc-gateway/v2/protoc-gen-grpc-gateway@v2.19.1
  GOBIN=$(LOCAL_BIN) go install github.com/grpc-ecosystem/grpc-gateway/v2/protoc-gen-openapiv2@v2.19.1

# Приводим проект в первоначальный вид
clean:
  rm -rf $(LOCAL_BIN)
{% endraw %}
{% endcapture %}
{% include component/code.html lang='Makefile' content=code %}

Выполним команду **make install-bin** для скачивания бинарных файлов. В корне проекта создастся каталог **bin** со скаченными бинарниками. Теперь, чтобы вызвать тот или иной плагин в терминале необходимо будет ввести команду **./bin/[имя файла] [опции]**.

<h2 id="deps"><span class="attention">Установка</span> зависимостей</h2>

Для кодогенерации **proto** файлов необходимо наличие **Protobuf** генератора. Подробная инструкция по его установке для различных ОС доступна на [официальном сайте](https://grpc.io/docs/protoc-installation){:target="_blank" rel="nofollow"} **gRPC** документации. Я буду производить установку в **Linux** среде [дистрибутива PopOS!]({{ site.baseurl }}/blog/2023-08-19-linux-dlya-nachinayushhih-chast-1-ustanovka-linux.html){:target="_blank"}.

{% capture code %}
// Вариант 1
// Стандартный вариант установки
// Плюсы: ни чего дополнительно устанавливать не надо
// Минусы: устанавливается устаревшая версия
sudo apt update
sudo apt install -y protobuf-compiler

// Вариант 2
// Вариант установки через Snap пакет
// Плюсы: актуальная версия
// Минусы: нужно установить snap
sudo apt update
sudo apt install snapd
sudo snap install protobuf --classic

// Вариант 3
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

В предыдущем разделе мы скачали бинарники необходимых нам пакетов. Теперь необходимо так же скачать сами пакеты. При установке пакетов задаем конкретные версии для сохранения совместимости.

{% capture code %}
go get google.golang.org/protobuf/cmd/protoc-gen-go@v1.28
go get google.golang.org/grpc/cmd/protoc-gen-go-grpc@v1.2
go get github.com/grpc-ecosystem/grpc-gateway/v2/protoc-gen-grpc-gateway@v2.19.1
go get github.com/grpc-ecosystem/grpc-gateway/v2/protoc-gen-openapiv2@v2.19.1
go get github.com/bufbuild/protovalidate-go@v0.6.0
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

<h2 id="api"><span class="attention">Структура</span> api</h2>

Официальная [документация Buf](https://buf.build/docs/introduction){:target="_blank" rel="nofollow"} предоставляет подробное описание того как должна выглядеть структура **proto** файлов и описание **buf** конфигураций. Советую ознакомиться:

- [Организация нескольких модулей](https://buf.build/docs/reference/workspaces){:target="_blank" rel="nofollow"}
- [Стайл гайд](https://buf.build/docs/best-practices/style-guide){:target="_blank" rel="nofollow"}
- [buf.yaml](https://buf.build/docs/configuration/v1/buf-yaml){:target="_blank" rel="nofollow"}
- [buf.gen.yaml](https://buf.build/docs/configuration/v1/buf-gen-yaml){:target="_blank" rel="nofollow"}
- [buf.work.yaml](https://buf.build/docs/configuration/v1/buf-work-yaml){:target="_blank" rel="nofollow"}

Следуя выше изложенной документации создадим в корне проекта файлы **buf.gen.yaml** и **buf.work.yaml**.

**buf.gen.yaml** - конфигурационный файл содержит правила для генерации кода из **proto** файлов.

{% capture code %}
version: v1

# Настройки используемых плагинов.
# Здесь мы перечисляем скаченные раннее бинарные файлы плагинов,
# указываем путь где размещать сгенерированные .go файлы
# и прописываем различные дополнительные опции.
plugins:
  # Генерирует файлы .pb.go
  - name: go
    path: ./bin/protoc-gen-go
    out: ./pkg
    opt:
    - paths=source_relative

  # Генерирует файлы _grpc.pb.go
  - name: go-grpc
    path: ./bin/protoc-gen-go-grpc
    out: ./pkg
    opt:
    - paths=source_relative

  # Генерирует файлы .pb.gw.go
  - name: grpc-gateway
    path: ./bin/protoc-gen-grpc-gateway
    out: ./pkg
    opt:
    - paths=source_relative

  # Генерирует файлы .swagger.json
  - name: openapiv2
    path: ./bin/protoc-gen-openapiv2
    out: ./pkg

  # Генерирует один общий файл .swagger.json
  - name: openapiv2
    path: ./bin/protoc-gen-openapiv2
    out: ./docs
    opt:
    - allow_merge=true
{% endcapture %}
{% include component/code.html lang='buf.gen.yaml' content=code %}

**buf.work.yaml** - конфигурационный файл содержит перечень директорий в которых хранятся **proto** файлы.

{% capture code %}
version: v1

# Перечисление директорий, хранящих .proto файлы
directories:
  - api
{% endcapture %}
{% include component/code.html lang='buf.gen.yaml' content=code %}

В корне проекта создадим каталог **api** в котором будут располагаться все пользовательские **proto** файлы. Внутри каталога **api** создадим файл **buf.yaml**.

**buf.yaml** - конфигурационный файл содержит различные настройки такие как линтер, зависимости, исключаемые из генерации **proto** файлы и много другое.

{% capture code %}
version: v1

# Зависимости проекта
deps:
  - buf.build/googleapis/googleapis
  - buf.build/grpc-ecosystem/grpc-gateway
  - buf.build/bufbuild/protovalidate

# Базовые настройки линтера для proto файлов
lint:
  use:
    - DEFAULT

# Не вникал в этот параметр, но оставил его, так как он автоматически прописывается
# при создании файла buf.yaml через команду ./bin/buf mod init.
breaking:
  use:
    - FILE
{% endcapture %}
{% include component/code.html lang='buf.yaml' content=code %}

После описания **buf.yaml** файла необходимо установить прописанные в нем зависимости, выполнив команду:

{% capture code %}
# api - название директории, где хранится файл buf.yaml.
# Если команда buf mod update вызывается из каталога, где находится файл buf.yaml, то имя директории можно не писать.
./bin/buf mod update api
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

После выполнения команды в **Buf cache** будут установлены прописанные зависимости и мы сможем воспользоваться их **proto** файлами. Далее мы на практике это рассмотрим. Так же рядом с файлом **buf.yaml** появится файл **buf.lock** который хранит именно те версии зависимостей, что мы установили. Это нужно опять же для обратной совместимости при командной работе.

И так, конфигурационные **buf** файлы настроены настала пора создать структуру наших **proto** файлов. Для этого в каталоге **api** создадим каталог, хранящий **proto** файлы. Я назову его **microservice**.

При развитии проекта бывает необходимость в кардинальном изменении **API**. В этом случае поможет версионирование **proto** файлов. Для этого внутри каталога **microservice** создадим каталог **v1**. В нем будут храниться **proto** файлы относящиеся к **API** первой версии. В каталоге **v1** создадим два файла **microservice_grpc.proto** и **user.proto**.

- **microservice_grpc.proto** - будет описывать **service**, хранящий **rpc** ручки.
- **user.proto** - будет описывать **message**, хранищий сущности запроса, ответа нашего **API** для пользователей.

Каждая сущность описывается в своем **proto** файле, **например**: product.proto, payment.proto, upload.proto и т.п.  
А файл **microservice_grpc.proto** их агрегирует.

Более подробное объяснение описания **proto** файлов было в прошлой статье, здесь я лишь коснусь кратко некоторых моментов.

### Файл microservice_grpc.proto

Опишем содержимое файла **microservice_grpc.proto**:

{% capture code %}
syntax = "proto3";

package microservice.v1;

option go_package = "github.com/eliofery/golang-fullstack/pkg/microservice/v1";

import "google/api/annotations.proto";
import "protoc-gen-openapiv2/options/annotations.proto";

import "microservice/v1/user.proto";

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

service MicroService {
  rpc User(UserRequest) returns (UserResponse) {
    option (google.api.http) = {
        post: "/v1/user"
        body: "*"
    };

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
{% include component/code.html lang='microservice_grpc.proto' content=code %}

В данном файле мы импортируем файлы **google/api/annotations.proto** и **protoc-gen-openapiv2/options/annotations.proto**. Мы можем воспользоваться ими благодаря тому, что установили зависимости, прописанные в файле **buf.yaml**, используя команду **./bin/buf mod update api**.

### Файл user.proto

Опишем содержимое файла **user.proto**:

{% capture code %}
syntax = "proto3";

package microservice.v1;

option go_package = "github.com/eliofery/golang-fullstack/pkg/microservice/v1";

import "protoc-gen-openapiv2/options/annotations.proto";
import "buf/validate/validate.proto";
import "google/protobuf/wrappers.proto";

message UserRequest {
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

  int32 number = 1;
  string email = 2 [
      (buf.validate.field).string.email = true
  ];
}

message UserResponse {
  google.protobuf.StringValue result = 1;
}
{% endcapture %}
{% include component/code.html lang='user.proto' content=code %}

В данном файле мы импортируем файлы **protoc-gen-openapiv2/options/annotations.proto** и **buf/validate/validate.proto**. Возможность использовать эти **proto** файлы так же доступно благодаря установки зависимостей прописанных в файле **buf.yaml**.

### Версия 2

Скопируем каталог **v1** со всеми файлами в ту же директорию и переименуем его в **v2**.

Содержимое файлов **microservice_grpc.proto** и **user.proto** будет следующим:

#### microservice_grpc.proto

{% capture code %}
syntax = "proto3";

package microservice.v2;

option go_package = "github.com/eliofery/golang-fullstack/pkg/microservice/v2";

import "google/api/annotations.proto";

import "microservice/v2/user.proto";

service MicroService {
  rpc User(UserRequest) returns (UserResponse) {
    option (google.api.http) = {
      post: "/v2/user"
      body: "*"
    };
  }
}
{% endcapture %}
{% include component/code.html lang='microservice_grpc.proto' content=code %}

#### user.proto

{% capture code %}
syntax = "proto3";

package microservice.v2;

option go_package = "github.com/eliofery/golang-fullstack/pkg/microservice/v2";

import "buf/validate/validate.proto";
import "google/protobuf/wrappers.proto";

message UserRequest {
    int32 number = 1;
    string email = 2 [
        (buf.validate.field).string.email = true
    ];
}

message UserResponse {
    google.protobuf.StringValue result = 1;
}
{% endcapture %}
{% include component/code.html lang='user.proto' content=code %}

<h2 id="generate"><span class="attention">Генерируем</span> код</h2>

Перед тем как сгенерировать код из **proto** файлов давайте проверим соответствие наших **proto** файлов правилам линтера. Для это воспользуемся командой:

{% capture code %}
buf lint
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

При возникновении ошибок линтер подскажет, что не так. В нашем случае ошибок быть не должно и мы можем приступить к генерации кода:

{% capture code %}
buf generate
{% endcapture %}
{% include component/code.html lang='bash' content=code %}

После выполнения команды в корне проекта появятся два каталога **docs**, хранящий **Swagger** файл с нашим **API** и **pkg** сгенерированные **Protobuf** файлы.

Ваш **IDE** может подсвечивать красным цветом сторонние зависимости **proto** файлов. Если вы пользуетесь **IDE Goland** то установите плагин **Buf for Protocol Buffers** для других редакторов ознакомьтесь с [этим обсуждением](https://github.com/bufbuild/buf/issues/493#issuecomment-947090008){:target="_blank" rel="nofollow"} возможно оно вам поможет.

<h2 id="server"><span class="attention">Запускаем</span> gRPC и REST сервер</h2>

В корне проекта создадим каталог **cmd/grpc_server** и внутри него файл **main.go** со следующим содержимым:

{% capture code %}
package main

import (
  "context"
  "fmt"
  pb "github.com/eliofery/golang-fullstack/pkg/microservice/v1"
  pbV2 "github.com/eliofery/golang-fullstack/pkg/microservice/v2"
  "github.com/golang/protobuf/ptypes/wrappers"
  "github.com/grpc-ecosystem/grpc-gateway/v2/runtime"
  "google.golang.org/grpc"
  "google.golang.org/grpc/reflection"
  "net"
  "net/http"
)

type MicroserviceServer struct {
  pb.UnimplementedMicroServiceServer
}

func (s *MicroserviceServer) User(ctx context.Context, req *pb.UserRequest) (*pb.UserResponse, error) {
  return &pb.UserResponse{
    Result: &wrappers.StringValue{Value: "success"},
  }, nil
}

type MicroserviceV2Server struct {
  pbV2.UnimplementedMicroServiceServer
}

func (s *MicroserviceV2Server) User(ctx context.Context, req *pbV2.UserRequest) (*pbV2.UserResponse, error) {
  return &pbV2.UserResponse{
    Result: &wrappers.StringValue{Value: "success v2"},
  }, nil
}

func main() {
  ch := make(chan error, 2)
  
  // gRPC server
  go func(ch chan error) {
    fmt.Println("gRPC server start :50051")
    listen, err := net.Listen("tcp", ":50051")
    if err != nil {
      ch <- err
    }
  
    grpcServer := grpc.NewServer()
    reflection.Register(grpcServer)
    
    pb.RegisterMicroServiceServer(grpcServer, &MicroserviceServer{})
    pbV2.RegisterMicroServiceServer(grpcServer, &MicroserviceV2Server{})
    
    if err = grpcServer.Serve(listen); err != nil {
      ch <- err
    }
  }(ch)
  
  // REST server
  go func() {
    mux := runtime.NewServeMux()

    err := pb.RegisterMicroServiceHandlerServer(context.Background(), mux, &MicroserviceServer{})
    if err != nil {
      ch <- err
    }

    err = pbV2.RegisterMicroServiceHandlerServer(context.Background(), mux, &MicroserviceV2Server{})
    if err != nil {
      ch <- err
    }
  
    server := http.Server{
      Addr:    ":8080",
      Handler: mux,
    }
    
    fmt.Println("REST server start :8080")
    if err = server.ListenAndServe(); err != nil {
      ch <- err
    }
  }()
  
  for i := 0; i < 2; i++ {
    if err := <-ch; err != nil {
      panic(err)
    }
  }
}
{% endcapture %}
{% include component/code.html lang='main.go' content=code %}

Мы уже разбирали этот код в [прошлой статье]({{ site.baseurl }}/blog/2024-02-01-sozdanie-servera-golang-s-grpc-i-rest-api-ispolzuya-swagger-backend.html){:target="_blank"}, здесь я лишь добавил использование **API** версии первой и второй.

Для этого нужно зарегистрировать каждую версию нашего **API** после чего оно станет доступно по заданному адресу и порту:

{% capture code %}
// pb - сокращенно от protobuf
import (
  pb "github.com/eliofery/golang-fullstack/pkg/microservice/v1"
  pbV2 "github.com/eliofery/golang-fullstack/pkg/microservice/v2"
)

type MicroserviceServer struct {
  pb.UnimplementedMicroServiceServer
}

type MicroserviceV2Server struct {
  pbV2.UnimplementedMicroServiceServer
}

pb.RegisterMicroServiceServer(grpcServer, &MicroserviceServer{})
pbV2.RegisterMicroServiceServer(grpcServer, &MicroserviceV2Server{})

pb.RegisterMicroServiceHandlerServer(context.Background(), mux, &MicroserviceServer{})
pbV2.RegisterMicroServiceHandlerServer(context.Background(), mux, &MicroserviceV2Server{})
{% endcapture %}
{% include component/code.html lang='main.go' content=code %}

<h2 id="end"><span class="attention">Подведем</span> итоги</h2>

В этой статье мы расширили свои знания в области генерации **proto** кода используя плагин **Buf**. Данный вариант считается передовым и рекомендуемым по сравнению с предыдущим, который мы разбирали в прошлой статье. Хотя он тоже довольно не плох. Какой из этих подходов выбирать решайте сами.

### Ссылки на проект

- [Исходный код](#golang-buf){:target="_blank" rel="nofollow"}
