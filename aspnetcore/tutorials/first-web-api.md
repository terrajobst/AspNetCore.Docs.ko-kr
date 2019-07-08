---
title: '자습서: ASP.NET Core를 사용하여 웹 API 만들기'
author: rick-anderson
description: ASP.NET Core를 사용하여 웹 API를 빌드하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 06/23/2019
uid: tutorials/first-web-api
ms.openlocfilehash: a53f7019c1079296f073e743ddbf9d90fc5abad3
ms.sourcegitcommit: d6e51c60439f03a8992bda70cc982ddb15d3f100
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555871"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a>자습서: ASP.NET Core를 사용하여 웹 API 만들기

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Mike Wasson](https://github.com/mikewasson)

이 자습서에서는 ASP.NET Core를 사용하여 웹 API를 빌드하는 작업의 기본 사항을 설명합니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * 웹 API 프로젝트를 만듭니다.
> * 모델 클래스 추가
> * 데이터베이스 컨텍스트 만들기
> * 데이터베이스 컨텍스트 등록
> * 컨트롤러 추가
> * CRUD 메서드 추가
> * 라우팅 및 URL 경로 구성
> * 반환 값 지정
> * Postman을 사용하여 웹 API 호출
> * jQuery를 사용하여 웹 API를 호출합니다.

작업을 완료하면 웹 API는 관계형 데이터베이스에 저장된 "할 일" 항목을 관리할 수 있게 됩니다.

## <a name="overview"></a>개요

이 자습서에서는 다음 API를 만듭니다.

|API | 설명 | 요청 본문 | 응답 본문 |
|--- | ---- | ---- | ---- |
|GET /api/todo | 할 일 항목 모두 가져오기 | 없음 | 할 일 항목의 배열|
|GET /api/todo/{id} | ID로 항목 가져오기 | 없음 | 할 일 항목|
|POST /api/todo | 새 항목 추가 | 할 일 항목 | 할 일 항목 |
|PUT /api/todo/{id} | 기존 항목 업데이트 &nbsp; | 할 일 항목 | 없음 |
|DELETE /api/todo/{id} &nbsp; &nbsp; | 항목 삭제 &nbsp; &nbsp; | 없음 | 없음|

다음 다이어그램에서는 앱의 디자인을 보여줍니다.

![클라이언트는 왼쪽에 상자로 표시됩니다. 클라이언트는 요청을 제출하고 오른쪽에 그려진 상자인 애플리케이션에서 응답을 받습니다. 애플리케이션 상자 내에서 3개의 상자는 컨트롤러, 모델 및 데이터 액세스 계층을 나타냅니다. 요청은 애플리케이션의 컨트롤러로 들어오고 읽기/쓰기 작업은 컨트롤러와 데이터 액세스 계층 간에 발생합니다. 모델은 직렬화되며 응답에서 클라이언트에 반환됩니다.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a>전제 조건

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a>웹 프로젝트 만들기

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* **파일** 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다.
* **ASP.NET Core 웹 애플리케이션** 템플릿을 선택하고 **다음**을 클릭합니다.
* 프로젝트 이름을 *TodoApi*로 지정하고 **만들기**를 클릭합니다.
* **새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **.NET Core** 및 **ASP.NET Core 2.2**가 선택되었는지 확인합니다. **API** 템플릿을 선택하고 **만들기**를 클릭합니다. **Docker 지원 사용**을 선택하지 **마세요**.

![VS 새 프로젝트 대화 상자](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.
* 디렉터리(`cd`)를 프로젝트 폴더를 포함하는 폴더로 변경합니다.
* 다음 명령을 실행합니다.

   ```console
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  이러한 명령은 새 웹 API 프로젝트를 만들고 새 프로젝트 폴더에서 Visual Studio Code의 새 인스턴스를 엽니다.

* 프로젝트에 필수 자산을 추가하려는지 묻는 대화 상자가 나타나면 **예**를 선택합니다.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* **파일** > **새 솔루션**을 선택합니다.

  ![macOS 새 솔루션](first-web-api-mac/_static/sln.png)

* **.NET Core App** > **ASP.NET Core Web API** > **다음**을 선택합니다.

  ![macOS 새 프로젝트 대화 상자](first-web-api-mac/_static/1.png)
  
* **새 ASP.NET Core Web API 구성** 대화 상자에서 * *.NET Core 2.2*라는 기본 **대상 프레임워크**를 수락합니다.

* **프로젝트 이름**으로 *TodoApi*를 입력한 다음, **만들기**를 선택합니다.

  ![구성 대화 상자](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a>API 테스트

프로젝트 템플릿은 `values` API를 만듭니다. 브라우저에서 `Get` 메서드를 호출하여 앱을 테스트합니다.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Ctrl+F5 키를 눌러 앱을 실행합니다. Visual Studio가 브라우저를 시작하고 `https://localhost:<port>/api/values`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다.

IIS Express 인증서를 신뢰해야 하는지 묻는 대화 상자가 표시되면 **예**를 선택합니다. 다음으로, **보안 경고** 대화 상자가 나타나면 **예**를 선택합니다.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Ctrl+F5 키를 눌러 앱을 실행합니다. 브라우저에서 다음 URL [https://localhost:5001/api/values](https://localhost:5001/api/values)로 이동합니다.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

**실행** > **디버깅 시작**을 선택하여 앱을 시작합니다. Mac용 Visual Studio가 브라우저를 시작하고 `https://localhost:<port>`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다. HTTP 404(찾을 수 없음) 오류가 반환됩니다. `/api/values`를 URL에 추가합니다(URL을 `https://localhost:<port>/api/values`로 변경).

---

다음 JSON이 반환됩니다.

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a>모델 클래스 추가

*모델*은 앱에서 관리하는 데이터를 나타내는 일련의 클래스입니다. 이 앱에 대한 모델은 단일 `TodoItem` 클래스입니다.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다. **추가** > **새 폴더**를 선택합니다. 폴더 이름을 *Models*로 지정합니다.

* *Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스**를 선택합니다. 클래스 이름을 *TodoItem*으로 지정하고 **추가**를 선택합니다.

* 템플릿 코드를 다음 코드로 바꿉니다.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* *Models* 폴더를 추가합니다.

* 다음 코드를 사용하여 *Models* 폴더에 `TodoItem` 클래스를 추가합니다.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 프로젝트를 마우스 오른쪽 단추로 클릭합니다. **추가** > **새 폴더**를 선택합니다. 폴더 이름을 *Models*로 지정합니다.

  ![새 폴더](first-web-api-mac/_static/folder.png)

* *모델* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 파일** > **일반** > **빈 클래스**를 선택합니다.

* 클래스 이름을 *TodoItem*으로 지정한 다음, **새로 만들기**를 클릭합니다.

* 템플릿 코드를 다음 코드로 바꿉니다.

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

`Id` 속성은 관계형 데이터베이스에서 고유 키로 작동합니다.

모델 클래스는 프로젝트의 어디로든 이동할 수 있지만 규칙에 따라 *Models* 폴더를 사용합니다.

## <a name="add-a-database-context"></a>데이터베이스 컨텍스트 추가

*데이터베이스 컨텍스트*는 데이터 모델에 맞게 Entity Framework 기능을 조정하는 주 클래스입니다. `Microsoft.EntityFrameworkCore.DbContext` 클래스에서 파생시키는 방식으로 이 클래스를 만듭니다.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* *Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스**를 선택합니다. 클래스 이름을 *TodoContext*로 지정하고 **추가**를 클릭합니다.

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

* `TodoContext` 클래스를 *Models* 폴더에 추가합니다.

---

* 템플릿 코드를 다음 코드로 바꿉니다.

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a>데이터베이스 컨텍스트 등록

ASP.NET Core에서는 DB 컨텍스트와 같은 서비스를 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 컨테이너에 등록해야 합니다. 컨테이너는 컨트롤러에 서비스를 제공합니다.

*Startup.cs*를 다음 강조 표시된 코드로 업데이트합니다.

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

위의 코드는:

* 사용되지 않는 `using` 선언을 제거합니다.
* DI 컨테이너에 데이터베이스 컨텍스트를 추가합니다.
* 데이터베이스 컨텍스트가 메모리 내 데이터베이스를 사용하도록 지정합니다.

## <a name="add-a-controller"></a>컨트롤러 추가

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* *Controllers* 폴더를 마우스 오른쪽 단추로 클릭합니다.
* **추가** > **새 항목**을 선택합니다.
* **새 항목 추가** 대화 상자에서 **API 컨트롤러 클래스** 템플릿을 선택합니다.
* 클래스 이름을 *TodoController*로 지정하고 **추가**를 선택합니다.

  ![검색 상자의 컨트롤러 및 웹 API 컨트롤러가 선택된 새 항목 추가 대화 상자](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

* *Controllers* 폴더에 `TodoController`라는 클래스를 만듭니다.

---

* 템플릿 코드를 다음 코드로 바꿉니다.

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

위의 코드는:

* 메서드 없이 API 컨트롤러 클래스를 정의합니다.
* [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 특성을 사용하여 클래스를 데코레이팅합니다. 이 특성은 컨트롤러가 웹 API 요청에 응답함을 나타냅니다. 특성을 사용하도록 설정하는 특정 동작에 대한 정보는 <xref:web-api/index>를 참조하세요.
* DI를 사용하여 데이터베이스 컨텍스트(`TodoContext`)를 컨트롤러에 삽입합니다. 컨트롤러의 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드 각각에서 데이터베이스 컨텍스트를 사용합니다.
* 데이터베이스가 비어 있는 경우 데이터베이스에 `Item1`이라는 항목을 추가합니다. 이 코드는 생성자에 위치하므로 새 HTTP 요청이 발생할 때마다 실행됩니다. 모든 항목을 삭제하면 생성자는 다음에 API가 호출될 경우 `Item1`을 다시 만듭니다. 따라서 실제로 작동되는 경우 삭제가 작동하지 않는 것처럼 보일 수 있습니다.

## <a name="add-get-methods"></a>GET 메서드 추가

할 일 항목을 가져오는 API를 제공하려면 다음 메서드를 `TodoController` 클래스에 추가합니다.

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

다음과 같은 메서드는 두 개의 GET 엔드포인트를 구현합니다.

* `GET /api/todo`
* `GET /api/todo/{id}`

브라우저에서 두 개의 엔드포인트를 호출하여 앱을 테스트합니다. 예:

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

`GetTodoItems`를 호출하여 다음 HTTP 응답이 생성됩니다.

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a>라우팅 및 URL 경로

[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 특성은 HTTP GET 요청에 응답하는 메서드를 나타냅니다. 각 방법에 대한 URL 경로는 다음과 같이 구성됩니다.

* 컨트롤러의 `Route` 특성에서 템플릿 문자열로 시작합니다.

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* `[controller]`를 컨트롤러의 이름으로 바꿉니다. 일반적으로 컨트롤러 클래스 이름에서 "Controller" 접미사를 뺀 이름입니다. 이 샘플의 경우 컨트롤러 클래스 이름은 **Todo**Controller이므로 컨트롤러 이름은 "todo"입니다. ASP.NET Core [라우팅](xref:mvc/controllers/routing)은 대/소문자를 구분하지 않습니다.
* `[HttpGet]` 특성에 경로 템플릿(예: `[HttpGet("products")]`)이 있는 경우 경로에 추가합니다. 이 샘플은 템플릿을 사용하지 않습니다. 자세한 내용은 [Http[동사] 특성을 사용한 특성 라우팅](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)을 참조하세요.

다음 `GetTodoItem` 메서드에서 `"{id}"`는 할 일 항목의 고유 식별자에 대한 자리 표시자 변수입니다. `GetTodoItem`가 호출되면 URL의 `"{id}"` 값을 `id` 매개 변수의 메서드에 제공합니다.

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a>반환 값

`GetTodoItems` 및 `GetTodoItem` 메서드의 반환 형식은 [ActionResult\<T> 형식](xref:web-api/action-return-types#actionresultt-type)입니다. ASP.NET Core는 자동으로 [JSON](https://www.json.org/)에 개체를 직렬화하고 JSON을 응답 메시지의 본문에 기록합니다. 이 반환 형식의 응답 코드는 200이며 처리되지 않은 예외가 없다고 가정합니다. 처리되지 않은 예외는 5xx 오류로 변환됩니다.

`ActionResult` 반환 형식은 다양한 HTTP 상태 코드를 나타낼 수 있습니다. 예를 들어 `GetTodoItem`은 두 가지 상태 값을 반환할 수 있습니다.

* 요청된 ID와 일치하는 항목이 없는 경우 메서드가 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) 오류 코드를 반환합니다.
* 그렇지 않으면 메서드가 JSON 응답 본문에서 200을 반환합니다. `item`을 반환하면 HTTP 200 응답이 발생합니다.

## <a name="test-the-gettodoitems-method"></a>GetTodoItems 메서드 테스트

이 자습서에서는 Postman을 사용하여 웹 API를 테스트합니다.

* [Postman](https://www.getpostman.com/downloads/) 설치
* 웹앱을 시작합니다.
* Postman을 시작합니다.
* **SSL 인증서 확인** 사용 안 함
  
  * **파일 > 설정**(**일반* 탭)에서 **SSL 인증서 확인**을 사용하지 않습니다.
    > [!WARNING]
    > 컨트롤러를 테스트한 후에 SSL 인증서 확인을 다시 사용하도록 설정합니다.

* 새 요청을 만듭니다.
  * HTTP 메서드를 **GET**으로 설정합니다.
  * 요청 URL을 `https://localhost:<port>/api/todo`로 설정합니다. 예를 들어, `https://localhost:5001/api/todo`을 입력합니다.
* Postman에서 **두 개의 창 보기**를 설정합니다.
* **보내기**를 선택합니다.

![Get 요청이 있는 Postman](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a>메서드 만들기 추가

다음 `PostTodoItem` 메서드를 추가합니다.

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

이전 코드는 [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 특성으로 표시되는 HTTP POST 메서드입니다. 이 메서드는 HTTP 요청 본문에서 할 일 항목 값을 가져옵니다.

`CreatedAtAction` 메서드는 다음 작업을 수행합니다.

* 성공 시 HTTP 201 상태 코드를 반환합니다. HTTP 201은 서버에서 새 리소스를 만드는 HTTP POST 메서드의 표준 응답입니다.
* `Location` 헤더를 응답에 추가합니다. `Location` 헤더는 새로 만들어진 할 일 항목의 URI를 지정합니다. 자세한 내용은 [10.2.2 201 생성됨](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)을 참조하세요.
* `Location` 헤더의 URI를 만들려면 `GetTodoItem` 작업을 참조합니다. C# `nameof` 키워드는 `CreatedAtAction` 호출에서 작업 이름의 하드 코딩을 방지하는 데 사용됩니다.

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a>PostTodoItem 메서드 테스트

* 프로젝트를 빌드합니다.
* Postman에서 HTTP 메서드를 `POST`로 설정합니다.
* **본문** 탭을 선택합니다.
* **원시** 라디오 단추를 선택합니다.
* 유형을 **JSON(application/json)** 으로 설정합니다.
* 요청 본문에서 할 일 항목에 대한 JSON을 입력합니다.

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* **보내기**를 선택합니다.

  ![생성 요청이 있는 Postman](first-web-api/_static/create.png)

  405 메서드가 허용되지 않음 오류가 발생할 경우 `PostTodoItem` 메서드를 추가한 후에 프로젝트를 컴파일하지 않아서 발생한 결과일 수 있습니다.

### <a name="test-the-location-header-uri"></a>위치 헤더 URI 테스트

* **응답** 창에서 **헤더** 탭을 선택합니다.
* **위치** 헤더 값을 복사합니다.

  ![Postman 콘솔의 헤더 탭](first-web-api/_static/pmc2.png)

* 메서드를 GET으로 설정합니다.
* URI(예: `https://localhost:5001/api/Todo/2`) 붙여넣기
* **보내기**를 선택합니다.

## <a name="add-a-puttodoitem-method"></a>PutTodoItem 메서드 추가

다음 `PutTodoItem` 메서드를 추가합니다.

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

HTTP PUT을 사용하는 것을 제외하고 `PutTodoItem`는 `PostTodoItem`와 비슷합니다. 응답은 [204(콘텐츠 없음)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다. HTTP 사양에 따라 PUT 요청의 경우 클라이언트는 변경 내용만이 아니라 전체 업데이트된 엔터티를 보내야 합니다. 부분 업데이트를 지원하려면 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)를 사용합니다.

`PutTodoItem`을 호출하는 중 오류가 발생하면 `GET`을 호출하여 데이터베이스에 항목이 있는지 확인합니다.

### <a name="test-the-puttodoitem-method"></a>PutTodoItem 메서드 테스트

이 샘플은 앱이 시작될 때마다 초기화되어야 하는 메모리 내 데이터베이스를 사용합니다. PUT 호출을 실행하기 전에 데이터베이스에 항목이 있어야 합니다. GET을 호출하여 PUT 호출을 실행하기 전에 데이터베이스에 항목이 있는지 확인합니다.

ID = 1인 할 일 항목을 업데이트하고 해당 이름을 "feed fish"로 설정합니다.

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

다음 이미지는 Postman 업데이트를 보여줍니다.

![204(콘텐츠 없음) 응답을 보여주는 Postman 콘솔](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a>DeleteTodoItem 메서드 추가

다음 `DeleteTodoItem` 메서드를 추가합니다.

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

`DeleteTodoItem` 응답은 [204(콘텐츠 없음)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다.

### <a name="test-the-deletetodoitem-method"></a>DeleteTodoItem 메서드 테스트

Postman을 사용하여 할 일 항목을 삭제합니다.

* 메서드를 `DELETE`로 설정합니다.
* 예를 들어 삭제할 개체의 URI를 `https://localhost:5001/api/todo/1`로 설정합니다.
* **보내기** 선택

샘플 앱을 사용하면 모든 항목을 삭제할 수 있습니다. 하지만 마지막 항목이 삭제되면 다음에 API를 호출하는 경우 모델 클래스 생성자에서 새로운 항목이 생성됩니다.

## <a name="call-the-api-with-jquery"></a>jQuery를 사용하여 API 호출

이 섹션에서는 jQuery를 사용하여 웹 API를 호출하는 HTML 페이지가 추가되었습니다. jQuery는 요청을 시작하고 API 응답의 세부 정보로 페이지를 업데이트합니다.

다음 강조 표시된 코드로 *Startup.cs*를 업데이트하여 앱이 [정적 파일을 제공](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)하고 [기본 파일 매핑을 사용](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)하도록 구성합니다.

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

::: moniker range=">= aspnetcore-2.2"
프로젝트 디렉터리에서 *wwwroot* 폴더를 만듭니다.
::: moniker-end

*index.html*이라는 HTML 파일을 *wwwroot* 디렉터리에 추가합니다. 다음 표시로 콘텐츠를 바꿉니다.

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

*site.js*라는 JavaScript 파일을 *wwwroot* 디렉터리에 추가합니다. 다음 코드로 콘텐츠를 바꿉니다.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

HTML 페이지를 로컬에서 테스트하려면 ASP.NET Core 프로젝트의 시작 설정을 변경해야 할 수 있습니다.

* *Properties\launchSettings.json*을 엽니다.
* `launchUrl` 속성을 제거하여 앱이 *index.html*&mdash; 프로젝트의 기본 파일에서 열리도록 합니다.

여러 가지 방법으로 jQuery를 가져올 수 있습니다. 위의 코드 조각에서 라이브러리는 CDN에서 로드됩니다.

이 샘플은 API의 모든 CRUD 메서드를 호출합니다. API 호출에 대한 설명은 다음과 같습니다.

### <a name="get-a-list-of-to-do-items"></a>할 일 항목의 목록 가져오기

jQuery [ajax](https://api.jquery.com/jquery.ajax/) 함수는 할 일 항목의 배열을 나타내는 JSON을 반환하는 API에 `GET` 요청을 보냅니다. 요청이 성공하면 `success` 콜백 함수가 호출됩니다. 콜백에서 DOM은 할 일 정보로 업데이트됩니다.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a>할 일 항목 추가

[ajax](https://api.jquery.com/jquery.ajax/) 함수는 `POST` 요청 본문에서 할 일 항목을 사용하여 요청을 보냅니다. `accepts` 및 `contentType` 옵션은 수신 및 전송되는 미디어 형식을 지정하기 위해 `application/json`으로 설정됩니다. [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)를 사용하여 할 일 항목을 JSON으로 변환합니다. API가 성공적인 상태 코드를 반환하면 `getData` 함수가 호출되어 HTML 테이블을 업데이트합니다.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a>할 일 항목 업데이트

할 일 항목을 업데이트하는 작업은 추가하는 작업과 비슷합니다. `url`은 항목의 고유 식별자를 추가하도록 변경되고 `type`은 `PUT`입니다.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a>할 일 항목 삭제

할 일 항목을 삭제하려면 AJAX 호출에서 `type`을 `DELETE`로 설정하고 URL에서 항목의 고유 식별자를 지정하면 됩니다.

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

## <a name="additional-resources"></a>추가 자료

[이 자습서에서 샘플 코드 보기 또는 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples) [다운로드하는 방법](xref:index#how-to-download-a-sample)을 참조하세요.

자세한 내용은 다음 리소스를 참조하세요.

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/index>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [이 자습서의 YouTube 버전](https://www.youtube.com/watch?v=TTkhEyGBfAk)

## <a name="next-steps"></a>다음 단계

본 자습서에서는 다음 작업에 관한 방법을 학습했습니다.

> [!div class="checklist"]
> * 웹 API 프로젝트 만들기
> * 모델 클래스 추가
> * 데이터베이스 컨텍스트 만들기
> * 데이터베이스 컨텍스트 등록
> * 컨트롤러 추가
> * CRUD 메서드 추가
> * 라우팅 및 URL 경로 구성
> * 반환 값 지정
> * Postman을 사용하여 웹 API 호출
> * jQuery를 사용하여 웹 API 호출

API 도움말 페이지를 생성하는 방법을 알아보려면 다음 자습서로 계속 진행합니다.

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>
