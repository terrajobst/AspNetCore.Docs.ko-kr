---
title: ASP.NET Core Blazor에서 web API를 호출 합니다.
author: guardrex
description: CORS (크로스-원본 자원 공유) 요청 만들기를 포함 하 여 JSON 도우미를 사용 하 여 Blazor 앱에서 web API를 호출 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
uid: blazor/call-web-api
ms.openlocfilehash: f1929b48275a36552f061a64823267df0f3acabc
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74943916"
---
# <a name="call-a-web-api-from-aspnet-core-opno-locblazor"></a><span data-ttu-id="817a0-103">ASP.NET Core Blazor에서 web API를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="817a0-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27)및 [Juan De la Cruz](https://github.com/juandelacruz23)</span><span class="sxs-lookup"><span data-stu-id="817a0-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Juan De la Cruz](https://github.com/juandelacruz23)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="817a0-105">[Blazor Weasembomapps](xref:blazor/hosting-models#blazor-webassembly) 는 미리 구성 된 `HttpClient` 서비스를 사용 하 여 web api를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-105">[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="817a0-106">Blazor JSON 도우미 또는 <xref:System.Net.Http.HttpRequestMessage>를 사용 하 여 JavaScript [FETCH API](https://developer.mozilla.org/docs/Web/API/Fetch_API) 옵션을 포함할 수 있는 요청을 작성 합니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span>

<span data-ttu-id="817a0-107">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps는 일반적으로 <xref:System.Net.Http.IHttpClientFactory>를 사용 하 여 만든 <xref:System.Net.Http.HttpClient> 인스턴스를 사용 하 여 web api를 호출 합니다</span><span class="sxs-lookup"><span data-stu-id="817a0-107">[Blazor Server](xref:blazor/hosting-models#blazor-server) apps call web APIs using <xref:System.Net.Http.HttpClient> instances typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="817a0-108">자세한 내용은 <xref:fundamentals/http-requests>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="817a0-108">For more information, see <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="817a0-109">[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample)) &ndash; *BlazorWebAssemblySample* 앱을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample)) &ndash; Select the *BlazorWebAssemblySample* app.</span></span>

<span data-ttu-id="817a0-110">*BlazorWebAssemblySample* 샘플 앱에서 다음 구성 요소를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="817a0-110">See the following components in the *BlazorWebAssemblySample* sample app:</span></span>

* <span data-ttu-id="817a0-111">Web API 호출 (*Pages/CallWebAPI*)</span><span class="sxs-lookup"><span data-stu-id="817a0-111">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="817a0-112">HTTP 요청 테스터 (*구성 요소/HTTPRequestTester. razor*)</span><span class="sxs-lookup"><span data-stu-id="817a0-112">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="packages"></a><span data-ttu-id="817a0-113">패키지</span><span class="sxs-lookup"><span data-stu-id="817a0-113">Packages</span></span>

<span data-ttu-id="817a0-114">프로젝트 파일의 *실험적* [Microsoft.AspNetCore.Blazor.HttpClient](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.HttpClient/) NuGet 패키지를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-114">Reference the *experimental* [Microsoft.AspNetCore.Blazor.HttpClient](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.HttpClient/) NuGet package in the project file.</span></span> <span data-ttu-id="817a0-115">`Microsoft.AspNetCore.Blazor.HttpClient`은 `HttpClient` 및 [system.object](https://www.nuget.org/packages/System.Text.Json/)를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-115">`Microsoft.AspNetCore.Blazor.HttpClient` is based on `HttpClient` and [System.Text.Json](https://www.nuget.org/packages/System.Text.Json/).</span></span>

<span data-ttu-id="817a0-116">안정적인 API를 사용 하려면 [newtonsoft.json](https://www.nuget.org/packages/Newtonsoft.Json/)/[Json.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm)를 사용 하는 [WebApi](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) 패키지를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-116">To use a stable API, use the [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/) package, which uses [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/)/[Json.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span> <span data-ttu-id="817a0-117">`Microsoft.AspNet.WebApi.Client`에서 안정적인 API를 사용 하는 경우이 항목에서 설명 하는 JSON 도우미를 제공 하지 않습니다 .이는 실험적 `Microsoft.AspNetCore.Blazor.HttpClient` 패키지에 고유한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-117">Using the stable API in `Microsoft.AspNet.WebApi.Client` doesn't provide the JSON helpers described in this topic, which are unique to the experimental `Microsoft.AspNetCore.Blazor.HttpClient` package.</span></span>

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="817a0-118">HttpClient 및 JSON 도우미</span><span class="sxs-lookup"><span data-stu-id="817a0-118">HttpClient and JSON helpers</span></span>

<span data-ttu-id="817a0-119">Blazor Weasembomapp에서 [Httpclient](xref:fundamentals/http-requests) 는 다시 원본 서버로 요청을 만들기 위해 미리 구성 된 서비스로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-119">In a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span>

<span data-ttu-id="817a0-120">Blazor Server 앱은 기본적으로 `HttpClient` 서비스를 포함 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-120">A Blazor Server app doesn't include an `HttpClient` service by default.</span></span> <span data-ttu-id="817a0-121">[Httpclient factory 인프라](xref:fundamentals/http-requests)를 사용 하 여 앱에 대 한 `HttpClient`를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-121">Provide an `HttpClient` to the app using the [HttpClient factory infrastructure](xref:fundamentals/http-requests).</span></span>

<span data-ttu-id="817a0-122">타사 웹 API 끝점을 호출 하는 데에도 `HttpClient` 및 JSON 도우미가 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-122">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="817a0-123">`HttpClient`는 브라우저 [인출 API](https://developer.mozilla.org/docs/Web/API/Fetch_API) 를 사용 하 여 구현 되며 동일한 원본 정책의 적용을 포함 하 여 해당 제한 사항이 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-123">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="817a0-124">클라이언트의 기본 주소가 원래 서버의 주소로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-124">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="817a0-125">`@inject` 지시어를 사용 하 여 `HttpClient` 인스턴스를 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-125">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```razor
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="817a0-126">다음 예에서 Todo 웹 API는 만들기, 읽기, 업데이트 및 삭제 (CRUD) 작업을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-126">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="817a0-127">예제는를 저장 하는 `TodoItem` 클래스를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-127">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="817a0-128">ID (`Id`, `long`) &ndash; 항목의 고유 ID입니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-128">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="817a0-129">항목의 이름 (`Name`, `string`) &ndash; 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-129">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="817a0-130">상태 (`IsComplete`, `bool`&ndash;)는 Todo 항목이 완료 되었는지 여부를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-130">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="817a0-131">JSON 도우미 메서드는 URI (다음 예제의 웹 API)에 요청을 보내고 응답을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-131">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="817a0-132">`GetJsonAsync` &ndash;는 HTTP GET 요청을 보내고 JSON 응답 본문을 구문 분석 하 여 개체를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-132">`GetJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="817a0-133">다음 코드에서 `_todoItems`는 구성 요소에 의해 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-133">In the following code, the `_todoItems` are displayed by the component.</span></span> <span data-ttu-id="817a0-134">`GetTodoItems` 메서드는 구성 요소의 렌더링 ([Oninitializedasync](xref:blazor/lifecycle#component-initialization-methods))이 완료 될 때 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-134">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/lifecycle#component-initialization-methods)).</span></span> <span data-ttu-id="817a0-135">전체 예제는 샘플 앱을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="817a0-135">See the sample app for a complete example.</span></span>

  ```razor
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetJsonAsync<TodoItem[]>("api/TodoItems");
  }
  ```

* <span data-ttu-id="817a0-136">`PostJsonAsync` &ndash;는 JSON으로 인코딩된 콘텐츠를 포함 하 여 HTTP POST 요청을 보내고 JSON 응답 본문을 구문 분석 하 여 개체를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-136">`PostJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="817a0-137">다음 코드에서 `_newItemName`는 구성 요소의 바인딩된 요소에 의해 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-137">In the following code, `_newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="817a0-138">`AddItem` 메서드는 `<button>` 요소를 선택 하 여 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-138">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="817a0-139">전체 예제는 샘플 앱을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="817a0-139">See the sample app for a complete example.</span></span>

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

* <span data-ttu-id="817a0-140">`PutJsonAsync` &ndash;는 JSON으로 인코딩된 콘텐츠를 포함 하 여 HTTP PUT 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-140">`PutJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="817a0-141">다음 코드에서 `Name` 및 `IsCompleted`에 대 한 `_editItem` 값은 구성 요소의 바인딩된 요소에 의해 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-141">In the following code, `_editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="817a0-142">항목의 `Id`은 UI의 다른 부분에서 항목을 선택 하 고 `EditItem`를 호출할 때 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-142">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="817a0-143">`SaveItem` 메서드는 Save `<button>` 요소를 선택 하 여 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-143">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="817a0-144">전체 예제는 샘플 앱을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="817a0-144">See the sample app for a complete example.</span></span>

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

<span data-ttu-id="817a0-145"><xref:System.Net.Http>에는 HTTP 요청을 보내고 HTTP 응답을 받기 위한 추가 확장 메서드가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-145"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="817a0-146">[DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) 는 HTTP DELETE 요청을 web API로 보내는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-146">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="817a0-147">다음 코드에서 Delete `<button>` 요소는 `DeleteItem` 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-147">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="817a0-148">바인딩된 `<input>` 요소는 삭제할 항목의 `id`를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-148">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="817a0-149">전체 예제는 샘플 앱을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="817a0-149">See the sample app for a complete example.</span></span>

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

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="817a0-150">CORS (원본 간 리소스 공유)</span><span class="sxs-lookup"><span data-stu-id="817a0-150">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="817a0-151">브라우저 보안은 웹 페이지에서 웹 페이지를 제공 하는 도메인에 대 한 요청을 수행 하지 못하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-151">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="817a0-152">이러한 제한을 *동일한 원본 정책*이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-152">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="817a0-153">동일한 원본 정책은 악의적인 사이트에서 다른 사이트의 중요 한 데이터를 읽지 못하도록 방지 합니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-153">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="817a0-154">브라우저에서 원본을 다른 끝점으로 요청 하려면 *끝점이* [CORS (원본 간 리소스 공유)](https://www.w3.org/TR/cors/)를 사용 하도록 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-154">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="817a0-155">[Blazor Weasembomsample 앱 (BlazorWebAssemblySample)](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) 은 CALL Web API 구성 요소 (*Pages/CALLWEBAPI*)에서 CORS를 사용 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-155">The [Blazor WebAssembly sample app (BlazorWebAssemblySample)](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="817a0-156">다른 사이트에서 CORS (크로스-원본 자원 공유) 요청을 앱에 만들도록 허용 하려면 <xref:security/cors>를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="817a0-156">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="817a0-157">Fetch API 요청 옵션이 포함 된 HttpClient 및 HttpRequestMessage</span><span class="sxs-lookup"><span data-stu-id="817a0-157">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="817a0-158">Weasembmbomapp에서 Blazor 하 여 실행 하는 경우 [Httpclient](xref:fundamentals/http-requests) 및 <xref:System.Net.Http.HttpRequestMessage>를 사용 하 여 요청을 사용자 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-158">When running on WebAssembly in a Blazor WebAssembly app, use [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> to customize requests.</span></span> <span data-ttu-id="817a0-159">예를 들어 요청 URI, HTTP 메서드 및 원하는 요청 헤더를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-159">For example, you can specify the request URI, HTTP method, and any desired request headers.</span></span>

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

<span data-ttu-id="817a0-160">Fetch API 옵션에 대 한 자세한 내용은 [MDN 웹 문서: WindowOrWorkerGlobalScope ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="817a0-160">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="817a0-161">CORS 요청에 대 한 자격 증명 (권한 부여 쿠키/헤더)을 보낼 때 CORS 정책에서 `Authorization` 헤더를 허용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-161">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="817a0-162">다음 정책에는에 대 한 구성이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-162">The following policy includes configuration for:</span></span>

* <span data-ttu-id="817a0-163">요청 원본 (`http://localhost:5000`, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="817a0-163">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="817a0-164">모든 메서드 (verb).</span><span class="sxs-lookup"><span data-stu-id="817a0-164">Any method (verb).</span></span>
* <span data-ttu-id="817a0-165">`Content-Type` 및 `Authorization` 헤더.</span><span class="sxs-lookup"><span data-stu-id="817a0-165">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="817a0-166">사용자 지정 헤더 (예: `x-custom-header`)를 허용 하려면 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>를 호출할 때 헤더를 나열 합니다.</span><span class="sxs-lookup"><span data-stu-id="817a0-166">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="817a0-167">클라이언트 쪽 JavaScript 코드에 의해 설정 된 자격 증명 (`credentials` 속성이 `include`로 설정).</span><span class="sxs-lookup"><span data-stu-id="817a0-167">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="817a0-168">자세한 내용은 <xref:security/cors> 및 샘플 앱의 HTTP 요청 테스터 구성 요소 (*Components/HTTPRequestTester*)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="817a0-168">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="817a0-169">추가 자료</span><span class="sxs-lookup"><span data-stu-id="817a0-169">Additional resources</span></span>

* <xref:fundamentals/http-requests>
* <xref:security/enforcing-ssl>
* [<span data-ttu-id="817a0-170">Kestrel HTTPS 끝점 구성</span><span class="sxs-lookup"><span data-stu-id="817a0-170">Kestrel HTTPS endpoint configuration</span></span>](xref:fundamentals/servers/kestrel#endpoint-configuration)
* [<span data-ttu-id="817a0-171">W3C의 CORS (원본 간 리소스 공유)</span><span class="sxs-lookup"><span data-stu-id="817a0-171">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
