---
title: ASP.NET Core Blazor에서 웹 API 호출
author: guardrex
description: CORS(원본 간 리소스 공유) 요청 만들기를 포함하여 JSON 도우미를 사용하여 Blazor 앱에서 웹 API를 호출하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/22/2020
no-loc:
- Blazor
- SignalR
uid: blazor/call-web-api
ms.openlocfilehash: e6996f0e6731b05038d0a9329152b8afd5f6796d
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78647733"
---
# <a name="call-a-web-api-from-aspnet-core-opno-locblazor"></a>ASP.NET Core Blazor에서 웹 API 호출

작성자: [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27) 및 [Juan De la Cruz](https://github.com/juandelacruz23)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) 앱은 미리 구성된 `HttpClient` 서비스를 사용하여 웹 API를 호출합니다. Blazor JSON 도우미 또는 <xref:System.Net.Http.HttpRequestMessage>를 사용하여 JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) 옵션을 포함할 수 있는 요청을 작성합니다. Blazor WebAssembly 앱의 `HttpClient` 서비스는 원본 서버에 대해 다시 요청을 수행하는 데 중점을 둡니다. 이 항목의 지침은 Blazor WebAssembly 앱과만 관련이 있습니다.

[Blazor 서버](xref:blazor/hosting-models#blazor-server) 앱은 일반적으로 <xref:System.Net.Http.IHttpClientFactory>를 사용하여 만드는 <xref:System.Net.Http.HttpClient> 인스턴스를 사용하여 웹 API를 호출합니다. 이 항목의 지침은 Blazor 서버 앱과는 관련이 없습니다. Blazor 서버 앱을 개발하는 경우 <xref:fundamentals/http-requests>의 지침을 따르세요.

[샘플 코드 보기 또는 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)([다운로드 방법](xref:index#how-to-download-a-sample)) &ndash; *BlazorWebAssemblySample* 앱을 선택합니다.

*BlazorWebAssemblySample* 샘플 앱에서 다음 구성 요소를 참조하세요.

* 웹 API(*Pages/CallWebAPI.razor*) 호출
* HTTP 요청 테스터(*Components/HTTPRequestTester.razor*)

## <a name="packages"></a>패키지

프로젝트 파일의 *실험적*[Microsoft.AspNetCore.Blazor.HttpClient](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.HttpClient/) NuGet 패키지를 참조합니다. `Microsoft.AspNetCore.Blazor.HttpClient`는 `HttpClient` 및 [System.Text.Json](https://www.nuget.org/packages/System.Text.Json/)을 기준으로 합니다.

안정적인 API를 사용하려면 [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/)/[Json.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm)을 사용하는 [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) 패키지를 사용합니다. `Microsoft.AspNet.WebApi.Client`에서 안정적인 API를 사용하는 경우 이 항목에서 설명하는 JSON 도우미가 제공되지 않습니다. 이 도우미는 실험적 `Microsoft.AspNetCore.Blazor.HttpClient` 패키지에만 국한됩니다.

## <a name="httpclient-and-json-helpers"></a>HttpClient 및 JSON 도우미

Blazor WebAssembly 앱에서 [HttpClient](xref:fundamentals/http-requests)는 원본 서버에 대해 다시 요청을 수행하기 위해 미리 구성된 서비스로 사용할 수 있습니다.

Blazor 서버 앱은 기본적으로 `HttpClient` 서비스를 포함하지 않습니다. [HttpClient 팩터리 인프라](xref:fundamentals/http-requests)를 사용하여 앱에 `HttpClient`를 제공합니다.

`HttpClient` 및 JSON 도우미는 타사 웹 API 엔드포인트를 호출하는 데에도 사용됩니다. `HttpClient`는 브라우저 [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)를 사용하여 구현되며 동일한 원본 정책 적용을 비롯한 제한 사항이 적용됩니다.

클라이언트의 기본 주소가 원래 서버의 주소로 설정됩니다. `@inject` 지시문을 사용하여 `HttpClient` 인스턴스를 주입합니다.

```razor
@using System.Net.Http
@inject HttpClient Http
```

다음 예제에서 Todo 웹 API는 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업을 처리합니다. 예제는 다음을 저장하는 `TodoItem` 클래스를 기준으로 합니다.

* ID(`Id`, `long`) &ndash; 항목의 고유한 ID입니다.
* 이름(`Name`, `string`) &ndash; 항목의 이름입니다.
* 상태(`IsComplete`, `bool`) &ndash;) Todo 항목이 완료되었는지 여부를 나타냅니다.

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

JSON 도우미 메서드는 URI(다음 예제의 웹 API)에 요청을 보내고 응답을 처리합니다.

* `GetJsonAsync` &ndash; HTTP GET 요청을 보내고 JSON 응답 본문을 구문 분석하여 개체를 만듭니다.

  다음 코드에서 `_todoItems`는 구성 요소에 의해 표시됩니다. `GetTodoItems` 메서드는 구성 요소가 렌더링을 완료할 때 트리거됩니다([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)). 전체 예제는 샘플 앱을 참조하세요.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* `PostJsonAsync` &ndash; JSON 인코딩 콘텐츠를 포함하여 HTTP POST 요청을 보내고 JSON 응답 본문을 구문 분석하여 개체를 만듭니다.

  다음 코드에서 구성 요소의 바인딩된 요소는 `_newItemName`을 제공합니다. `AddItem` 메서드는 `<button>` 요소를 선택하여 트리거됩니다. 전체 예제는 샘플 앱을 참조하세요.

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  <input @bind="_newItemName" placeholder="New Todo Item" />
  <button @onclick="@AddItem">Add</button>

  @code {
      private string _newItemName;

      private async Task AddItem()
      {
          var addItem = new TodoItem { Name = _newItemName, IsComplete = false };
          await Http.PostJsonAsync("api/TodoItems", addItem);
      }
  }
  ```

* `PutJsonAsync` &ndash; JSON 인코딩 콘텐츠를 포함하여 HTTP PUT 요청을 보냅니다.

  다음 코드에서 구성 요소의 바인딩된 요소는 `Name` 및 `IsCompleted`에 대한 `_editItem` 값을 제공합니다. 항목의 `Id`는 UI의 다른 부분에서 해당 항목을 선택하고 `EditItem`을 호출하면 설정됩니다. `SaveItem` 메서드는 Save `<button>` 요소를 선택하여 트리거됩니다. 전체 예제는 샘플 앱을 참조하세요.

  ```razor
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
          await Http.PutJsonAsync($"api/TodoItems/{_editItem.Id}, _editItem);
  }
  ```

<xref:System.Net.Http>는 HTTP 요청을 보내고 HTTP 응답을 받기 위한 추가 확장 메서드를 포함합니다. [HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*)는 웹 API에 HTTP DELETE 요청을 보내는 데 사용됩니다.

다음 코드에서 Delete `<button>` 요소는 `DeleteItem` 메서드를 호출합니다. 바인딩된 `<input>` 요소는 삭제할 항목의 `id`를 제공합니다. 전체 예제는 샘플 앱을 참조하세요.

```razor
@using System.Net.Http
@inject HttpClient Http

<input @bind="_id" />
<button @onclick="@DeleteItem">Delete</button>

@code {
    private long _id;

    private async Task DeleteItem() =>
        await Http.DeleteAsync($"api/TodoItems/{_id}");
}
```

## <a name="cross-origin-resource-sharing-cors"></a>CORS(원본 간 리소스 공유)

브라우저 보안 때문에 웹 페이지에서 해당 웹 페이지를 제공한 도메인이 아닌 다른 도메인에 요청을 수행할 수는 없습니다. 이러한 제한 사항을 *동일 원본 정책*이라고 합니다. 동일 원본 정책은 악성 사이트에서 다른 사이트의 중요한 데이터를 읽지 못하도록 차단합니다. 브라우저에서 원본이 다른 엔드포인트로의 요청을 만들려면 *엔드포인트*가 [CORS(원본 간 리소스 공유)](https://www.w3.org/TR/cors/)를 사용하도록 설정해야 합니다.

[Blazor WebAssembly 샘플 앱(BlazorWebAssemblySample)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)은 웹 API 호출 구성 요소(*Pages/CallWebAPI.razor*)에서 CORS를 사용하는 방법을 보여 줍니다.

다른 사이트에서 앱에 대해 CORS(원본 간 리소스 공유) 요청을 수행하도록 허용하려면 <xref:security/cors>를 참조하세요.

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>Fetch API 요청 옵션이 포함된 HttpClient 및 HttpRequestMessage

Blazor WebAssembly 앱의 WebAssembly에서 실행될 경우 [HttpClient](xref:fundamentals/http-requests) 및 <xref:System.Net.Http.HttpRequestMessage>를 사용하여 요청을 사용자 지정합니다. 예를 들어, 요청 URI, HTTP 메서드 및 원하는 요청 헤더를 지정할 수 있습니다.

```razor
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
            RequestUri = new Uri("https://localhost:10000/api/TodoItems"),
            Content = 
                new StringContent(
                    @"{""name"":""A New Todo Item"",""isComplete"":false}")
        };

        requestMessage.Content.Headers.ContentType = 
            new System.Net.Http.Headers.MediaTypeHeaderValue(
                "application/json");

        requestMessage.Content.Headers.TryAddWithoutValidation(
            "x-custom-header", "value");

        var response = await Http.SendAsync(requestMessage);
        var responseStatusCode = response.StatusCode;
        var responseBody = await response.Content.ReadAsStringAsync();
    }
}
```

Fetch API 옵션에 대한 자세한 내용은 [MDN 웹 설명서: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)를 참조하세요.

CORS 요청에 대한 자격 증명(권한 부여 쿠키/헤더)을 보낼 때 CORS 정책에서 `Authorization` 헤더를 허용해야 합니다.

다음 정책에는 해당 구성이 포함되어 있습니다.

* 요청 원본(`http://localhost:5000`, `https://localhost:5001`).
* 임의 메서드(동사)
* `Content-Type` 및 `Authorization` 헤더. 사용자 지정 헤더(예: `x-custom-header`)를 허용하려면 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>를 호출할 때 헤더를 나열합니다.
* 클라이언트 쪽 JavaScript 코드에 의해 설정된 자격 증명(`credentials` 속성이 `include`로 설정됨).

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

자세한 내용은 <xref:security/cors> 및 샘플 앱의 HTTP 요청 테스터 구성 요소(*Components/HTTPRequestTester.razor*)를 참조하세요.

## <a name="additional-resources"></a>추가 자료

* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [Kestrel HTTPS 엔드포인트 구성](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [W3C의 CORS(원본 간 리소스 공유)](https://www.w3.org/TR/cors/)
