---
title: ASP.NET Core Blazor에서 web API 호출
author: guardrex
description: CORS (크로스-원본 자원 공유) 요청 만들기를 포함 하 여 JSON 도우미를 사용 하 여 Blazor 앱에서 web API를 호출 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: blazor/call-web-api
ms.openlocfilehash: 2e79c81dc2371ef5b00f9251be47433a141c8ab7
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71207145"
---
# <a name="call-a-web-api-from-aspnet-core-blazor"></a><span data-ttu-id="6d2d9-103">ASP.NET Core Blazor에서 web API 호출</span><span class="sxs-lookup"><span data-stu-id="6d2d9-103">Call a web API from ASP.NET Core Blazor</span></span>

<span data-ttu-id="6d2d9-104">By [Luke Latham](https://github.com/guardrex) 및 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="6d2d9-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="6d2d9-105">Blazor weasembomapps는 미리 구성 `HttpClient` 된 서비스를 사용 하 여 web api를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-105">Blazor WebAssembly apps call web APIs using a preconfigured `HttpClient` service.</span></span> <span data-ttu-id="6d2d9-106">Blazor JSON 도우미를 사용 하거나를 <xref:System.Net.Http.HttpRequestMessage>사용 하 여 JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) 옵션을 포함할 수 있는 요청을 작성 합니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-106">Compose requests, which can include JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) options, using Blazor JSON helpers or with <xref:System.Net.Http.HttpRequestMessage>.</span></span>

<span data-ttu-id="6d2d9-107">Blazor 서버 앱은 일반적으로을 <xref:System.Net.Http.HttpClient> 사용 하 여 <xref:System.Net.Http.IHttpClientFactory>만든 인스턴스를 사용 하 여 web api를 호출 합니다</span><span class="sxs-lookup"><span data-stu-id="6d2d9-107">Blazor Server apps call web APIs using <xref:System.Net.Http.HttpClient> instances typically created using <xref:System.Net.Http.IHttpClientFactory>.</span></span> <span data-ttu-id="6d2d9-108">자세한 내용은 <xref:fundamentals/http-requests>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-108">For more information, see <xref:fundamentals/http-requests>.</span></span>

<span data-ttu-id="6d2d9-109">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6d2d9-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="6d2d9-110">Blazor Weasemboma 예제는 샘플 앱에서 다음 구성 요소를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-110">For Blazor WebAssembly examples, see the following components in the sample app:</span></span>

* <span data-ttu-id="6d2d9-111">Web API 호출 (*Pages/CallWebAPI*)</span><span class="sxs-lookup"><span data-stu-id="6d2d9-111">Call Web API (*Pages/CallWebAPI.razor*)</span></span>
* <span data-ttu-id="6d2d9-112">HTTP 요청 테스터 (*구성 요소/HTTPRequestTester. razor*)</span><span class="sxs-lookup"><span data-stu-id="6d2d9-112">HTTP Request Tester (*Components/HTTPRequestTester.razor*)</span></span>

## <a name="httpclient-and-json-helpers"></a><span data-ttu-id="6d2d9-113">HttpClient 및 JSON 도우미</span><span class="sxs-lookup"><span data-stu-id="6d2d9-113">HttpClient and JSON helpers</span></span>

<span data-ttu-id="6d2d9-114">Blazor Weasembomapps에서 [Httpclient](xref:fundamentals/http-requests) 는 다시 원본 서버로 요청을 만들기 위해 미리 구성 된 서비스로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-114">In Blazor WebAssembly apps, [HttpClient](xref:fundamentals/http-requests) is available as a preconfigured service for making requests back to the origin server.</span></span> <span data-ttu-id="6d2d9-115">JSON 도우미 `HttpClient` 를 사용 하려면에 대 `Microsoft.AspNetCore.Blazor.HttpClient`한 패키지 참조를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-115">To use `HttpClient` JSON helpers, add a package reference to `Microsoft.AspNetCore.Blazor.HttpClient`.</span></span> <span data-ttu-id="6d2d9-116">`HttpClient`및 JSON 도우미는 타사 웹 API 끝점을 호출 하는 데에도 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-116">`HttpClient` and JSON helpers are also used to call third-party web API endpoints.</span></span> <span data-ttu-id="6d2d9-117">`HttpClient`는 브라우저 [인출 API](https://developer.mozilla.org/docs/Web/API/Fetch_API) 를 사용 하 여 구현 되며 동일한 원본 정책의 적용을 포함 하 여 해당 제한 사항이 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-117">`HttpClient` is implemented using the browser [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) and is subject to its limitations, including enforcement of the same origin policy.</span></span>

<span data-ttu-id="6d2d9-118">클라이언트의 기본 주소가 원래 서버의 주소로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-118">The client's base address is set to the originating server's address.</span></span> <span data-ttu-id="6d2d9-119">지시문을 `HttpClient` 사용 하 여 `@inject` 인스턴스를 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-119">Inject an `HttpClient` instance using the `@inject` directive:</span></span>

```cshtml
@using System.Net.Http
@inject HttpClient Http
```

<span data-ttu-id="6d2d9-120">다음 예에서 Todo 웹 API는 만들기, 읽기, 업데이트 및 삭제 (CRUD) 작업을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-120">In the following examples, a Todo web API processes create, read, update, and delete (CRUD) operations.</span></span> <span data-ttu-id="6d2d9-121">예제는를 저장 하는 `TodoItem` 클래스를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-121">The examples are based on a `TodoItem` class that stores the:</span></span>

* <span data-ttu-id="6d2d9-122">Id (`Id`, `long`) &ndash; 항목의 고유 id입니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-122">ID (`Id`, `long`) &ndash; Unique ID of the item.</span></span>
* <span data-ttu-id="6d2d9-123">항목의`Name`이름 `string`( &ndash; ,) 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-123">Name (`Name`, `string`) &ndash; Name of the item.</span></span>
* <span data-ttu-id="6d2d9-124">상태 (`IsComplete`, `bool`) &ndash; 는 Todo 항목이 완료 되었는지 여부를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-124">Status (`IsComplete`, `bool`) &ndash; Indication if the Todo item is finished.</span></span>

```csharp
private class TodoItem
{
    public long Id { get; set; }
    public string Name { get; set; }
    public bool IsComplete { get; set; }
}
```

<span data-ttu-id="6d2d9-125">JSON 도우미 메서드는 URI (다음 예제의 웹 API)에 요청을 보내고 응답을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-125">JSON helper methods send requests to a URI (a web API in the following examples) and process the response:</span></span>

* <span data-ttu-id="6d2d9-126">`GetJsonAsync`&ndash; HTTP GET 요청을 보내고 JSON 응답 본문을 구문 분석 하 여 개체를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-126">`GetJsonAsync` &ndash; Sends an HTTP GET request and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="6d2d9-127">다음 코드에서는 `_todoItems` 구성 요소에 의해 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-127">In the following code, the `_todoItems` are displayed by the component.</span></span> <span data-ttu-id="6d2d9-128">이 `GetTodoItems` 메서드는 구성 요소의 렌더링 ([oninitializedasync](xref:blazor/components#lifecycle-methods))이 완료 될 때 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-128">The `GetTodoItems` method is triggered when the component is finished rendering ([OnInitializedAsync](xref:blazor/components#lifecycle-methods)).</span></span> <span data-ttu-id="6d2d9-129">전체 예제는 샘플 앱을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-129">See the sample app for a complete example.</span></span>

  ```cshtml
  @using System.Net.Http
  @inject HttpClient Http

  @code {
      private TodoItem[] _todoItems;

      protected override async Task OnInitializedAsync() => 
          _todoItems = await Http.GetJsonAsync<TodoItem[]>("api/todo");
  }
  ```

* <span data-ttu-id="6d2d9-130">`PostJsonAsync`&ndash; Json 인코딩 콘텐츠를 포함 하 여 HTTP POST 요청을 보내고 json 응답 본문을 구문 분석 하 여 개체를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-130">`PostJsonAsync` &ndash; Sends an HTTP POST request, including JSON-encoded content, and parses the JSON response body to create an object.</span></span>

  <span data-ttu-id="6d2d9-131">다음 코드 `_newItemName` 에서는 구성 요소의 바인딩된 요소에 의해 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-131">In the following code, `_newItemName` is provided by a bound element of the component.</span></span> <span data-ttu-id="6d2d9-132">메서드 `AddItem` 는 요소를 `<button>` 선택 하 여 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-132">The `AddItem` method is triggered by selecting a `<button>` element.</span></span> <span data-ttu-id="6d2d9-133">전체 예제는 샘플 앱을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-133">See the sample app for a complete example.</span></span>

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

* <span data-ttu-id="6d2d9-134">`PutJsonAsync`&ndash; JSON으로 인코딩된 콘텐츠를 포함 하 여 HTTP PUT 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-134">`PutJsonAsync` &ndash; Sends an HTTP PUT request, including JSON-encoded content.</span></span>

  <span data-ttu-id="6d2d9-135">다음 코드 `_editItem` 에서 및 `IsCompleted` 에 대 한 `Name` 값은 구성 요소의 바인딩된 요소에 의해 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-135">In the following code, `_editItem` values for `Name` and `IsCompleted` are provided by bound elements of the component.</span></span> <span data-ttu-id="6d2d9-136">항목은 UI의 다른 부분에서 항목을 선택 하 고 `EditItem` 가 호출 될 때 설정 됩니다.`Id`</span><span class="sxs-lookup"><span data-stu-id="6d2d9-136">The item's `Id` is set when the item is selected in another part of the UI and `EditItem` is called.</span></span> <span data-ttu-id="6d2d9-137">메서드 `SaveItem` 는 저장 `<button>` 요소를 선택 하 여 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-137">The `SaveItem` method is triggered by selecting the Save `<button>` element.</span></span> <span data-ttu-id="6d2d9-138">전체 예제는 샘플 앱을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-138">See the sample app for a complete example.</span></span>

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

<span data-ttu-id="6d2d9-139"><xref:System.Net.Http>HTTP 요청을 보내고 HTTP 응답을 받기 위한 추가 확장 메서드가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-139"><xref:System.Net.Http> includes additional extension methods for sending HTTP requests and receiving HTTP responses.</span></span> <span data-ttu-id="6d2d9-140">[DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) 는 HTTP DELETE 요청을 web API로 보내는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-140">[HttpClient.DeleteAsync](xref:System.Net.Http.HttpClient.DeleteAsync*) is used to send an HTTP DELETE request to a web API.</span></span>

<span data-ttu-id="6d2d9-141">다음 코드에서 Delete `<button>` 요소는 메서드를 `DeleteItem` 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-141">In the following code, the Delete `<button>` element calls the `DeleteItem` method.</span></span> <span data-ttu-id="6d2d9-142">바인딩된 `<input>` 요소는 삭제할 항목 `id` 의를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-142">The bound `<input>` element supplies the `id` of the item to delete.</span></span> <span data-ttu-id="6d2d9-143">전체 예제는 샘플 앱을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-143">See the sample app for a complete example.</span></span>

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

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="6d2d9-144">CORS (원본 간 리소스 공유)</span><span class="sxs-lookup"><span data-stu-id="6d2d9-144">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="6d2d9-145">브라우저 보안은 웹 페이지에서 웹 페이지를 제공 하는 도메인에 대 한 요청을 수행 하지 못하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-145">Browser security prevents a webpage from making requests to a different domain than the one that served the webpage.</span></span> <span data-ttu-id="6d2d9-146">이러한 제한을 *동일한 원본 정책*이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-146">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="6d2d9-147">동일한 원본 정책은 악의적인 사이트에서 다른 사이트의 중요 한 데이터를 읽지 못하도록 방지 합니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-147">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="6d2d9-148">브라우저에서 원본을 다른 끝점으로 요청 하려면 *끝점이* [CORS (원본 간 리소스 공유)](https://www.w3.org/TR/cors/)를 사용 하도록 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-148">To make requests from the browser to an endpoint with a different origin, the *endpoint* must enable [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/).</span></span>

<span data-ttu-id="6d2d9-149">샘플 앱에서는 Web API 호출 구성 요소 (*Pages/CallWebAPI*)에서 CORS를 사용 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-149">The sample app demonstrates the use of CORS in the Call Web API component (*Pages/CallWebAPI.razor*).</span></span>

<span data-ttu-id="6d2d9-150">다른 사이트에서 CORS (크로스-원본 자원 공유) 요청을 앱으로 만들 수 있도록 하려면를 <xref:security/cors>참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-150">To allow other sites to make cross-origin resource sharing (CORS) requests to your app, see <xref:security/cors>.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="6d2d9-151">Fetch API 요청 옵션이 포함 된 HttpClient 및 HttpRequestMessage</span><span class="sxs-lookup"><span data-stu-id="6d2d9-151">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="6d2d9-152">Blazor weambmbomembomommboma에서 실행 되는 경우 [httpclient](xref:fundamentals/http-requests) 및 <xref:System.Net.Http.HttpRequestMessage> 를 사용 하 여 요청을 사용자 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-152">When running on WebAssembly in a Blazor WebAssembly app, use [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> to customize requests.</span></span> <span data-ttu-id="6d2d9-153">예를 들어 요청 URI, HTTP 메서드 및 원하는 요청 헤더를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-153">For example, you can specify the request URI, HTTP method, and any desired request headers.</span></span>

<span data-ttu-id="6d2d9-154">요청에 대 한 속성을 `WebAssemblyHttpMessageHandler.FetchArgs` 사용 하 여 기본 JavaScript [인출 API](https://developer.mozilla.org/docs/Web/API/Fetch_API) 에 요청 옵션을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-154">Supply request options to the underlying JavaScript [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) using the `WebAssemblyHttpMessageHandler.FetchArgs` property on the request.</span></span> <span data-ttu-id="6d2d9-155">다음 예제 `credentials` 와 같이 속성은 다음 값 중 하나로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-155">As shown in the following example, the `credentials` property is set to any of the following values:</span></span>

* <span data-ttu-id="6d2d9-156">`FetchCredentialsOption.Include`("포함") &ndash; 원본 간 요청에 대 한 자격 증명 (예: 쿠키 또는 HTTP 인증 헤더)을 전송 하도록 브라우저에 조언 합니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-156">`FetchCredentialsOption.Include` ("include") &ndash; Advises the browser to send credentials (such as cookies or HTTP authentication headers) even for cross-origin requests.</span></span> <span data-ttu-id="6d2d9-157">CORS 정책이 자격 증명을 허용 하도록 구성 된 경우에만 허용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-157">Only allowed when the CORS policy is configured to allow credentials.</span></span>
* <span data-ttu-id="6d2d9-158">`FetchCredentialsOption.Omit`("생략") &ndash; 브라우저에서 쿠키 또는 HTTP 인증 헤더와 같은 자격 증명을 보내지 않도록 조언 합니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-158">`FetchCredentialsOption.Omit` ("omit") &ndash; Advises the browser never to send credentials (such as cookies or HTTP auth headers).</span></span>
* <span data-ttu-id="6d2d9-159">`FetchCredentialsOption.SameOrigin`("동일 원본") &ndash; 대상 URL이 호출 하는 응용 프로그램과 동일한 원본에 있는 경우에만 쿠키 또는 HTTP 인증 헤더와 같은 자격 증명을 보내도록 브라우저에 조언 합니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-159">`FetchCredentialsOption.SameOrigin` ("same-origin") &ndash; Advises the browser to send credentials (such as cookies or HTTP auth headers) only if the target URL is on the same origin as the calling application.</span></span>

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

<span data-ttu-id="6d2d9-160">Fetch API 옵션에 대 한 자세한 내용은 MDN [웹 문서를 참조 하세요. WindowOrWorkerGlobalScope ():P arameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters)를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-160">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="6d2d9-161">Cors 요청에 대 한 자격 증명 (권한 부여 쿠키/헤더) `Authorization` 을 보낼 때 cors 정책에서 헤더를 허용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-161">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="6d2d9-162">다음 정책에는에 대 한 구성이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-162">The following policy includes configuration for:</span></span>

* <span data-ttu-id="6d2d9-163">요청 원본 (`http://localhost:5000`, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="6d2d9-163">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="6d2d9-164">모든 메서드 (verb).</span><span class="sxs-lookup"><span data-stu-id="6d2d9-164">Any method (verb).</span></span>
* <span data-ttu-id="6d2d9-165">`Content-Type`및 `Authorization` 헤더.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-165">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="6d2d9-166">사용자 지정 헤더 (예: `x-custom-header`)를 허용 하려면를 호출할 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>때 헤더를 나열 합니다.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-166">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="6d2d9-167">클라이언트 쪽 JavaScript 코드에 의해 설정 된 자격`credentials` 증명 (속성 `include`은로 설정).</span><span class="sxs-lookup"><span data-stu-id="6d2d9-167">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="6d2d9-168">자세한 내용은 및 샘플 응용 <xref:security/cors> 프로그램의 HTTP 요청 테스터 구성 요소 (*Components/httprequesttester. razor*)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="6d2d9-168">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6d2d9-169">추가 자료</span><span class="sxs-lookup"><span data-stu-id="6d2d9-169">Additional resources</span></span>

* <xref:fundamentals/http-requests>
* [<span data-ttu-id="6d2d9-170">W3C의 CORS (원본 간 리소스 공유)</span><span class="sxs-lookup"><span data-stu-id="6d2d9-170">Cross Origin Resource Sharing (CORS) at W3C</span></span>](https://www.w3.org/TR/cors/)
