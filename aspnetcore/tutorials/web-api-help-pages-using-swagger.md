---
title: Swagger/OpenAPI를 사용한 ASP.NET Core Web API 도움말 페이지
author: rsuter
description: 이 자습서에서는 Swagger를 추가하여 Web API 앱에 대한 설명서 및 도움말 페이지를 생성하는 연습을 제공합니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 09/20/2018
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: d7a6ed158dcb464bb80c83773ed7d455b25ce44b
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64887728"
---
# <a name="aspnet-core-web-api-help-pages-with-swagger--openapi"></a>Swagger/OpenAPI를 사용한 ASP.NET Core 웹 API 도움말 페이지

작성자: [Christoph Nienaber](https://twitter.com/zuckerthoben) 및 [Rico Suter](http://rsuter.com)

Web API를 사용할 때 여러 메서드를 이해하는 것이 개발자에게 어려울 수 있습니다. [Swagger](https://swagger.io/)([OpenAPI](https://www.openapis.org/)라고도 함)는 Web API에 대한 유용한 설명서 및 도움말 페이지를 생성하는 문제를 해결합니다. 대화형 설명서, 클라이언트 SDK 생성 및 API 검색 기능과 같은 이점을 제공합니다.

이 아티클에서는 [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) 및 [NSwag](https://github.com/RSuter/NSwag) .NET Swagger 구현을 보여 줍니다.

* **Swashbuckle.AspNetCore**는 ASP.NET Core Web API에 대한 Swagger 문서를 생성하기 위한 오픈 소스 프로젝트입니다.

* **NSwag**는 Swagger 문서를 생성하고 [Swagger UI](https://swagger.io/swagger-ui/) 또는 [ReDoc](https://github.com/Rebilly/ReDoc)를 ASP.NET Core Web API에 통합하기 위한 또 다른 오픈 소스 프로젝트입니다. 또한 NSwag는 API용 C# 및 TypeScript 클라이언트 코드를 생성하는 방법을 제공합니다.

## <a name="what-is-swagger--openapi"></a>Swagger/OpenAPI란?

Swagger는 [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) API를 설명하는 언어 중립적 사양입니다. Swagger 프로젝트는 [OpenAPI 이니셔티브](https://www.openapis.org/)에 기부되었으며, 현재는 OpenAPI라고 합니다. 두 이름은 동일한 의미로 사용되지만 OpenAPI를 사용하는 것이 좋습니다. 컴퓨터와 사람 모두 구현(소스 코드, 네트워크 액세스, 설명서)에 직접 액세스하지 않고도 서비스의 기능을 이해할 수 있습니다. 단일 목표는 분리된 서비스를 연결하는 데 필요한 작업량을 최소화하는 것입니다. 또 다른 목표는 서비스를 정확하게 문서화하는 데 필요한 시간을 줄이는 것입니다.

## <a name="swagger-specification-swaggerjson"></a>Swagger 사양(swagger.json)

Swagger 흐름의 핵심은 Swagger 사양(기본적으로 *swagger.json*이라는 이름의 문서)입니다. 서비스에 따라 Swagger 도구 체인(또는 타사 구현 도구)이 생성됩니다. API의 기능과 HTTP를 사용하여 액세스하는 방법에 대해 설명합니다. Swagger UI를 구동하고, 도구 체인에서 검색 및 클라이언트 코드 생성을 활성화하는 데 사용됩니다. 다음은 간단히 축약된 Swagger 사양의 예입니다.

```json
{
   "swagger": "2.0",
   "info": {
       "version": "v1",
       "title": "API V1"
   },
   "basePath": "/",
   "paths": {
       "/api/Todo": {
           "get": {
               "tags": [
                   "Todo"
               ],
               "operationId": "ApiTodoGet",
               "consumes": [],
               "produces": [
                   "text/plain",
                   "application/json",
                   "text/json"
               ],
               "responses": {
                   "200": {
                       "description": "Success",
                       "schema": {
                           "type": "array",
                           "items": {
                               "$ref": "#/definitions/TodoItem"
                           }
                       }
                   }
                }
           },
           "post": {
               ...
           }
       },
       "/api/Todo/{id}": {
           "get": {
               ...
           },
           "put": {
               ...
           },
           "delete": {
               ...
   },
   "definitions": {
       "TodoItem": {
           "type": "object",
            "properties": {
                "id": {
                    "format": "int64",
                    "type": "integer"
                },
                "name": {
                    "type": "string"
                },
                "isComplete": {
                    "default": false,
                    "type": "boolean"
                }
            }
       }
   },
   "securityDefinitions": {}
}
```

## <a name="swagger-ui"></a>Swagger UI

[Swagger UI](https://swagger.io/swagger-ui/)는 생성된 Swagger 사양을 사용하여 서비스에 대한 정보를 제공하는 웹 기반 UI를 제공합니다. Swashbuckle과 NSwag에는 모두 임베디드 버전의 Swagger UI가 포함되어 있어, 미들웨어 등록 호출을 사용하여 ASP.NET Core 앱에서 호스팅할 수 있습니다. 웹 UI는 다음과 같습니다.

![Swagger UI](web-api-help-pages-using-swagger/_static/swagger-ui.png)

컨트롤러의 각 공용 작업 메서드는 UI에서 테스트할 수 있습니다. 메서드 이름을 클릭하여 섹션을 확장합니다. 필요한 매개 변수를 모두 추가하고 **사용해 보기**를 클릭합니다.

![예제 Swagger GET 테스트](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> 스크린샷에 사용된 Swagger UI 버전은 버전 2입니다. 버전 3 예제는 [Petstore 예제](http://petstore.swagger.io/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Swashbuckle 시작](xref:tutorials/get-started-with-swashbuckle)
* [NSwag 시작](xref:tutorials/get-started-with-nswag)
