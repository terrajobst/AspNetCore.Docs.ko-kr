---
title: ASP.NET Core Blazor에서 web API 호출
author: guardrex
description: CORS (크로스-원본 자원 공유) 요청 만들기를 포함 하 여 JSON 도우미를 사용 하 여 Blazor 앱에서 web API를 호출 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 06/25/2019
uid: blazor/call-web-api
ms.openlocfilehash: 1a13f9f1f9e660b39a1df584e49198c4bbb61533
ms.sourcegitcommit: 47cc13ab90913af9a2887cef0896bb4e9aba4dd5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/26/2019
ms.locfileid: "68948193"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a>ASP.NET Core Blazor에서 web API 호출

By [Luke Latham](https://github.com/guardrex) 및 [Daniel Roth](https://github.com/danroth27)

Blazor 클라이언트 쪽 앱은 미리 구성 `HttpClient` 된 서비스를 사용 하 여 web api를 호출 합니다. Blazor JSON 도우미를 사용 하거나를 <xref:System.Net.Http.HttpRequestMessage>사용 하 여 JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) 옵션을 포함할 수 있는 요청을 작성 합니다.

Blazor 서버 쪽 앱은 일반적으로를 사용 <xref:System.Net.Http.HttpClient> 하 여 <xref:System.Net.Http.IHttpClientFactory>만든 인스턴스를 사용 하 여 web api를 호출 합니다. 자세한 내용은 <xref:fundamentals/http-requests>을 참조하세요.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

Blazor 클라이언트 쪽 예제는 샘플 앱에서 다음 구성 요소를 참조 하세요.

* Web API 호출 (*Pages/CallWebAPI*)
* HTTP 요청 테스터 (*구성 요소/HTTPRequestTester. razor*)

## <a name="httpclient-and-json-helpers"></a>HttpClient 및 JSON 도우미

Blazor 클라이언트 쪽 응용 프로그램에서 [Httpclient](xref:fundamentals/http-requests) 는 다시 원본 서버로 요청을 만들기 위해 미리 구성 된 서비스로 사용할 수 있습니다. `HttpClient`및 JSON 도우미는 타사 웹 API 끝점을 호출 하는 데에도 사용 됩니다. `HttpClient`는 브라우저 [인출 API](https://developer.mozilla.org/docs/Web/API/Fetch_API) 를 사용 하 여 구현 되며 동일한 원본 정책의 적용을 포함 하 여 해당 제한 사항이 적용 됩니다.

클라이언트의 기본 주소가 원래 서버의 주소로 설정 됩니다. 지시문을 `HttpClient` 사용 하 여 `@inject` 인스턴스를 삽입 합니다.

```cshtml
@using System.Net.Http
@inject HttpClient Http
```

다음 예에서 Todo 웹 API는 만들기, 읽기, 업데이트 및 삭제 (CRUD) 작업을 처리 합니다. 예제는를 저장 하는 `TodoItem` 클래스를 기반으로 합니다.

* Id (`Id`, `long`) &ndash; 항목의 고유 id입니다.
* 항목의`Name`이름 `string`( &ndash; ,) 이름입니다.
* 상태 (`IsComplete`, `bool`) &ndash; 는 Todo 항목이 완료 되었는지 여부를 나타냅니다.

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

JSON 도우미 메서드는 URI (다음 예제의 웹 API)에 요청을 보내고 응답을 처리 합니다.

* `GetJsonAsync`&ndash; HTTP GET 요청을 보내고 JSON 응답 본문을 구문 분석 하 여 개체를 만듭니다.

  다음 코드에서는 `_todoItems` 구성 요소에 의해 표시 됩니다. 이 `GetTodoItems` 메서드는 구성 요소의 렌더링 ([OnInitAsync](xref:blazor/components#lifecycle-methods))이 완료 될 때 트리거됩니다. 전체 예제는 샘플 앱을 참조 하세요.

  ```cshtml
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitAsync() => 
          _todoItems = await Http.GetJsonAsync<TodoItem[]>("api/todo");
  }
  ```

* `PostJsonAsync`&ndash; Json 인코딩 콘텐츠를 포함 하 여 HTTP POST 요청을 보내고 json 응답 본문을 구문 분석 하 여 개체를 만듭니다.

  다음 코드 `_newItemName` 에서는 구성 요소의 바인딩된 요소에 의해 제공 됩니다. 메서드 `AddItem` 는 요소를 `<button>` 선택 하 여 트리거됩니다. 전체 예제는 샘플 앱을 참조 하세요.

  ```cshtml
  @using System.Net.Http
  @inject HttpClient Http

  <input @bind="_newItemName" placeholder="New Todo Item" />
  <button @onclick="@AddItem">Add</button>

  @code {
      private string _newItemName;

      private async Task AddItem()
      {
          var addItem = new TodoItem { Name = _newItemName, IsComplete = false };
          await Http.PostJsonAsync("api/todo", addItem);
      }
  }
  ```

* `PutJsonAsync`&ndash; JSON으로 인코딩된 콘텐츠를 포함 하 여 HTTP PUT 요청을 보냅니다.

  다음 코드 `_editItem` 에서 및 `IsCompleted` 에 대 한 `Name` 값은 구성 요소의 바인딩된 요소에 의해 제공 됩니다. 항목은 UI의 다른 부분에서 항목을 선택 하 고 `EditItem` 가 호출 될 때 설정 됩니다.`Id` 메서드 `SaveItem` 는 저장 `<button>` 요소를 선택 하 여 트리거됩니다. 전체 예제는 샘플 앱을 참조 하세요.

  ```cshtml
  @using System.Net.Http
  @inject HttpClient Http

  <input type="checkbox" @bind="_editItem.IsComplete" />
  <input @bind="_editItem.Name" />
  <button @onclick="@SaveItem">Save</button>

  @code {
      private TodoItem _editItem = new TodoItem();

      private void EditItem(long id)
      {
          var editItem = _todoItems.Single(i => i.Id == id);
          _editItem = new TodoItem { Id = editItem.Id, Name = editItem.Name, 
              IsComplete = editItem.IsComplete };
      }

      private async Task SaveItem() =>
          await Http.PutJsonAsync($"api/todo/{_editItem.Id}, _editItem);
  }
  ```

<xref:System.Net.Http>HTTP 요청을 보내고 HTTP 응답을 받기 위한 추가 확장 메서드가 포함 되어 있습니다. [DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) 는 HTTP DELETE 요청을 web API로 보내는 데 사용 됩니다.

다음 코드에서 Delete `<button>` 요소는 메서드를 `DeleteItem` 호출 합니다. 바인딩된 `<input>` 요소는 삭제할 항목 `id` 의를 제공 합니다. 전체 예제는 샘플 앱을 참조 하세요.

```cshtml
@using System.Net.Http
@inject HttpClient Http

<input @bind="_id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long _id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/todo/{_id}");
}
```

## <a name="cross-origin-resource-sharing-cors"></a>CORS (원본 간 리소스 공유)

브라우저 보안은 웹 페이지에서 웹 페이지를 제공 하는 도메인에 대 한 요청을 수행 하지 못하도록 합니다. 이러한 제한을 *동일한 원본 정책*이라고 합니다. 동일한 원본 정책은 악의적인 사이트에서 다른 사이트의 중요 한 데이터를 읽지 못하도록 방지 합니다. 브라우저에서 원본을 다른 끝점으로 요청 하려면 *끝점이* [CORS (원본 간 리소스 공유)](https://www.w3.org/TR/cors/)를 사용 하도록 설정 해야 합니다.

샘플 앱에서는 Web API 호출 구성 요소 (*Pages/CallWebAPI*)에서 CORS를 사용 하는 방법을 보여 줍니다.

다른 사이트에서 CORS (크로스-원본 자원 공유) 요청을 앱으로 만들 수 있도록 하려면를 <xref:security/cors>참조 하세요.

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>Fetch API 요청 옵션이 포함 된 HttpClient 및 HttpRequestMessage

Blazor 클라이언트 쪽 앱에서 webassembly을 실행 하는 경우 [httpclient](xref:fundamentals/http-requests) 및 <xref:System.Net.Http.HttpRequestMessage> 를 사용 하 여 요청을 사용자 지정 합니다. 예를 들어 요청 URI, HTTP 메서드 및 원하는 요청 헤더를 지정할 수 있습니다.

요청에 대 한 속성을 `WebAssemblyHttpMessageHandler.FetchArgs` 사용 하 여 기본 JavaScript [인출 API](https://developer.mozilla.org/docs/Web/API/Fetch_API) 에 요청 옵션을 제공 합니다. 다음 예제 `credentials` 와 같이 속성은 다음 값 중 하나로 설정 됩니다.

* `FetchCredentialsOption.Include`("포함") &ndash; 원본 간 요청에 대 한 자격 증명 (예: 쿠키 또는 HTTP 인증 헤더)을 전송 하도록 브라우저에 조언 합니다. CORS 정책이 자격 증명을 허용 하도록 구성 된 경우에만 허용 됩니다.
* `FetchCredentialsOption.Omit`("생략") &ndash; 브라우저에서 쿠키 또는 HTTP 인증 헤더와 같은 자격 증명을 보내지 않도록 조언 합니다.
* `FetchCredentialsOption.SameOrigin`("동일 원본") &ndash; 대상 URL이 호출 하는 응용 프로그램과 동일한 원본에 있는 경우에만 쿠키 또는 HTTP 인증 헤더와 같은 자격 증명을 보내도록 브라우저에 조언 합니다.

```cshtml
@using System.Net.Http
@using System.Net.Http.Headers
@inject HttpClient Http

@code {
    private async Task PostRequest()
    {
        Http.DefaultRequestHeaders.Authorization =
            new AuthenticationHeaderValue("Bearer", "{OAUTH TOKEN}");

        var requestMessage = new HttpRequestMessage()
        {
            Method = new HttpMethod("POST"),
            RequestUri = new Uri("https://localhost:10000/api/todo"),
            Content = 
                new StringContent(
                    @"{""name"":""A New Todo Item"",""isComplete"":false}")
        };

        requestMessage.Content.Headers.ContentType = 
            new System.Net.Http.Headers.MediaTypeHeaderValue(
                "application/json");

        requestMessage.Content.Headers.TryAddWithoutValidation(
            "x-custom-header", "value");
        
        requestMessage.Properties[WebAssemblyHttpMessageHandler.FetchArgs] = new
        { 
            credentials = FetchCredentialsOption.Include
        };

        var response = await Http.SendAsync(requestMessage);
        var responseStatusCode = response.StatusCode;
        var responseBody = await response.Content.ReadAsStringAsync();
    }
}
```

Fetch API 옵션에 대 한 자세한 내용은 MDN [웹 문서를 참조 하세요. WindowOrWorkerGlobalScope ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)를 가져옵니다.

Cors 요청에 대 한 자격 증명 (권한 부여 쿠키/헤더) `Authorization` 을 보낼 때 cors 정책에서 헤더를 허용 해야 합니다.

다음 정책에는에 대 한 구성이 포함 되어 있습니다.

* 요청 원본 (`http://localhost:5000`, `https://localhost:5001`).
* 모든 메서드 (verb).
* `Content-Type`및 `Authorization` 헤더. 사용자 지정 헤더 (예: `x-custom-header`)를 허용 하려면를 호출할 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>때 헤더를 나열 합니다.
* 클라이언트 쪽 JavaScript 코드에 의해 설정 된 자격`credentials` 증명 (속성 `include`은로 설정).

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

자세한 내용은 및 샘플 응용 <xref:security/cors> 프로그램의 HTTP 요청 테스터 구성 요소 (*Components/httprequesttester. razor*)를 참조 하세요.

## <a name="additional-resources"></a>추가 자료

* <xref:fundamentals/http-requests>
* [W3C의 CORS (원본 간 리소스 공유)](https://www.w3.org/TR/cors/)
