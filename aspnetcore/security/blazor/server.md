---
title: 보안 ASP.NET Blazor 핵심 서버 앱
author: guardrex
description: Server 앱에 대한 Blazor 보안 위협을 완화하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/server
ms.openlocfilehash: bd03f811d0425fdfdb7bbbc24fea5481b49b8ed3
ms.sourcegitcommit: 9675db7bf4b67ae269f9226b6f6f439b5cce4603
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80626018"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="52785-103">보안 ASP.NET 코어 블레이저 서버 앱</span><span class="sxs-lookup"><span data-stu-id="52785-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="52785-104">[하비에르 칼바로 넬슨](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="52785-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="52785-105">Blazor Server 앱은 서버와 클라이언트가 수명이 긴 관계를 유지하는 *상태 저장* 데이터 처리 모델을 채택합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-105">Blazor Server apps adopt a *stateful* data processing model, where the server and client maintain a long-lived relationship.</span></span> <span data-ttu-id="52785-106">영구 상태는 잠재적으로 수명이 긴 연결을 포괄할 수 있는 [회로에](xref:blazor/state-management)의해 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="52785-106">The persistent state is maintained by a [circuit](xref:blazor/state-management), which can span connections that are also potentially long-lived.</span></span>

<span data-ttu-id="52785-107">사용자가 Blazor Server 사이트를 방문하면 서버가 서버 메모리에 회로를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="52785-107">When a user visits a Blazor Server site, the server creates a circuit in the server's memory.</span></span> <span data-ttu-id="52785-108">회로는 사용자가 UI에서 단추를 선택할 때와 같이 렌더링할 콘텐츠를 브라우저에 나타내고 이벤트에 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-108">The circuit indicates to the browser what content to render and responds to events, such as when the user selects a button in the UI.</span></span> <span data-ttu-id="52785-109">이러한 작업을 수행하기 위해 회로는 사용자의 브라우저에서 JavaScript 함수를 호출하고 서버의 .NET 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-109">To perform these actions, a circuit invokes JavaScript functions in the user's browser and .NET methods on the server.</span></span> <span data-ttu-id="52785-110">이 양방향 자바 스크립트 기반 상호 작용은 [자바 스크립트 인터옵 (JS interop)이라고합니다.](xref:blazor/call-javascript-from-dotnet)</span><span class="sxs-lookup"><span data-stu-id="52785-110">This two-way JavaScript-based interaction is referred to as [JavaScript interop (JS interop)](xref:blazor/call-javascript-from-dotnet).</span></span>

<span data-ttu-id="52785-111">JS 인터옵은 인터넷을 통해 발생하고 클라이언트는 원격 브라우저를 사용하기 때문에 Blazor Server 앱은 대부분의 웹 앱 보안 문제를 공유합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-111">Because JS interop occurs over the Internet and the client uses a remote browser, Blazor Server apps share most web app security concerns.</span></span> <span data-ttu-id="52785-112">이 항목에서는 Blazor Server 앱에 대한 일반적인 위협에 대해 설명하고 인터넷 연결 앱에 중점을 둔 위협 완화 지침을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-112">This topic describes common threats to Blazor Server apps and provides threat mitigation guidance focused on Internet-facing apps.</span></span>

<span data-ttu-id="52785-113">내부 회사 네트워크 또는 인트라넷과 같이 제한된 환경에서는 다음과 같은 완화 지침 중 일부가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-113">In constrained environments, such as inside corporate networks or intranets, some of the mitigation guidance either:</span></span>

* <span data-ttu-id="52785-114">제한된 환경에는 적용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-114">Doesn't apply in the constrained environment.</span></span>
* <span data-ttu-id="52785-115">제한된 환경에서 는 보안 위험이 낮기 때문에 구현할 가치가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-115">Isn't worth the cost to implement because the security risk is low in a constrained environment.</span></span>

## <a name="blazor-server-project-template"></a><span data-ttu-id="52785-116">블레이저 서버 프로젝트 템플릿</span><span class="sxs-lookup"><span data-stu-id="52785-116">Blazor Server project template</span></span>

<span data-ttu-id="52785-117">Blazor Server 프로젝트 템플릿은 프로젝트를 만들 때 인증을 위해 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-117">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="52785-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="52785-118">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="52785-119"><xref:blazor/get-started> 문서의 Visual Studio 지침에 따라 인증 메커니즘이 있는 새 Blazor 서버 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="52785-119">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="52785-120">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **Blazor 서버 앱** 템플릿을 선택한 후 **인증**에서 **변경**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-120">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="52785-121">다른 ASP.NET Core 프로젝트에 사용할 수 있는 것과 동일한 인증 메커니즘 세트를 제공하는 대화 상자가 열립니다.</span><span class="sxs-lookup"><span data-stu-id="52785-121">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="52785-122">**인증 없음**</span><span class="sxs-lookup"><span data-stu-id="52785-122">**No Authentication**</span></span>
* <span data-ttu-id="52785-123">**개별 사용자 계정** &ndash; 사용자 계정은 다음과 같이 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-123">**Individual User Accounts** &ndash; User accounts can be stored:</span></span>
  * <span data-ttu-id="52785-124">ASP.NET Core의 [ID](xref:security/authentication/identity) 시스템을 사용하여 앱에 저장</span><span class="sxs-lookup"><span data-stu-id="52785-124">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="52785-125">[Azure AD B2C](xref:security/authentication/azure-ad-b2c) 사용</span><span class="sxs-lookup"><span data-stu-id="52785-125">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="52785-126">**직장 또는 학교 계정**</span><span class="sxs-lookup"><span data-stu-id="52785-126">**Work or School Accounts**</span></span>
* <span data-ttu-id="52785-127">**Windows 인증**</span><span class="sxs-lookup"><span data-stu-id="52785-127">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="52785-128">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="52785-128">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="52785-129"><xref:blazor/get-started> 문서의 Visual Studio Code 지침에 따라 인증 메커니즘이 있는 새 Blazor 서버 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="52785-129">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="52785-130">허용되는 인증 값(`{AUTHENTICATION}`)은 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-130">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="52785-131">인증 메커니즘</span><span class="sxs-lookup"><span data-stu-id="52785-131">Authentication mechanism</span></span>                                                                 | <span data-ttu-id="52785-132">`{AUTHENTICATION}` 값</span><span class="sxs-lookup"><span data-stu-id="52785-132">`{AUTHENTICATION}` value</span></span> |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| <span data-ttu-id="52785-133">인증 없음</span><span class="sxs-lookup"><span data-stu-id="52785-133">No Authentication</span></span>                                                                        | `None`                   |
| <span data-ttu-id="52785-134">개인</span><span class="sxs-lookup"><span data-stu-id="52785-134">Individual</span></span><br><span data-ttu-id="52785-135">ASP.NET Core ID를 사용하여 앱에 저장된 사용자</span><span class="sxs-lookup"><span data-stu-id="52785-135">Users stored in the app with ASP.NET Core Identity.</span></span>                        | `Individual`             |
| <span data-ttu-id="52785-136">개인</span><span class="sxs-lookup"><span data-stu-id="52785-136">Individual</span></span><br><span data-ttu-id="52785-137">[Azure AD B2C](xref:security/authentication/azure-ad-b2c)에 저장된 사용자</span><span class="sxs-lookup"><span data-stu-id="52785-137">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span> | `IndividualB2C`          |
| <span data-ttu-id="52785-138">회사 또는 학교 계정</span><span class="sxs-lookup"><span data-stu-id="52785-138">Work or School Accounts</span></span><br><span data-ttu-id="52785-139">단일 테넌트에 대한 조직 인증</span><span class="sxs-lookup"><span data-stu-id="52785-139">Organizational authentication for a single tenant.</span></span>            | `SingleOrg`              |
| <span data-ttu-id="52785-140">회사 또는 학교 계정</span><span class="sxs-lookup"><span data-stu-id="52785-140">Work or School Accounts</span></span><br><span data-ttu-id="52785-141">여러 테넌트에 대한 조직 인증</span><span class="sxs-lookup"><span data-stu-id="52785-141">Organizational authentication for multiple tenants.</span></span>           | `MultiOrg`               |
| <span data-ttu-id="52785-142">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="52785-142">Windows Authentication</span></span>                                                                   | `Windows`                |

<span data-ttu-id="52785-143">이 명령은 `{APP NAME}` 자리 표시자에 제공된 값으로 이름을 지정하여 폴더를 만들고, 폴더 이름을 앱 이름으로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-143">The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name.</span></span> <span data-ttu-id="52785-144">자세한 내용은 .NET Core 가이드의 [dotnet new](/dotnet/core/tools/dotnet-new) 명령을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52785-144">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="52785-145">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="52785-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="52785-146">이 문서에서 Mac 지침에 <xref:blazor/get-started> 대한 Visual Studio를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="52785-146">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="52785-147">새 **Blazor 서버 앱 구성** 단계에서 **인증** 드롭다운에서 개별 **인증(인앱)을** 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-147">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="52785-148">응용 프로그램은 ASP.NET 코어 ID와 응용 프로그램에 저장된 개별 사용자를 위해 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="52785-148">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="52785-149">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="52785-149">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="52785-150"><xref:blazor/get-started> 인증 메커니즘을 사용하여 새 Blazor Server 프로젝트를 만들려면 문서의 .NET Core CLI 지침을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="52785-150">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="52785-151">허용되는 인증 값(`{AUTHENTICATION}`)은 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-151">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="52785-152">인증 메커니즘</span><span class="sxs-lookup"><span data-stu-id="52785-152">Authentication mechanism</span></span>                                                                 | <span data-ttu-id="52785-153">`{AUTHENTICATION}` 값</span><span class="sxs-lookup"><span data-stu-id="52785-153">`{AUTHENTICATION}` value</span></span> |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| <span data-ttu-id="52785-154">인증 없음</span><span class="sxs-lookup"><span data-stu-id="52785-154">No Authentication</span></span>                                                                        | `None`                   |
| <span data-ttu-id="52785-155">개인</span><span class="sxs-lookup"><span data-stu-id="52785-155">Individual</span></span><br><span data-ttu-id="52785-156">ASP.NET Core ID를 사용하여 앱에 저장된 사용자</span><span class="sxs-lookup"><span data-stu-id="52785-156">Users stored in the app with ASP.NET Core Identity.</span></span>                        | `Individual`             |
| <span data-ttu-id="52785-157">개인</span><span class="sxs-lookup"><span data-stu-id="52785-157">Individual</span></span><br><span data-ttu-id="52785-158">[Azure AD B2C](xref:security/authentication/azure-ad-b2c)에 저장된 사용자</span><span class="sxs-lookup"><span data-stu-id="52785-158">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span> | `IndividualB2C`          |
| <span data-ttu-id="52785-159">회사 또는 학교 계정</span><span class="sxs-lookup"><span data-stu-id="52785-159">Work or School Accounts</span></span><br><span data-ttu-id="52785-160">단일 테넌트에 대한 조직 인증</span><span class="sxs-lookup"><span data-stu-id="52785-160">Organizational authentication for a single tenant.</span></span>            | `SingleOrg`              |
| <span data-ttu-id="52785-161">회사 또는 학교 계정</span><span class="sxs-lookup"><span data-stu-id="52785-161">Work or School Accounts</span></span><br><span data-ttu-id="52785-162">여러 테넌트에 대한 조직 인증</span><span class="sxs-lookup"><span data-stu-id="52785-162">Organizational authentication for multiple tenants.</span></span>           | `MultiOrg`               |
| <span data-ttu-id="52785-163">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="52785-163">Windows Authentication</span></span>                                                                   | `Windows`                |

<span data-ttu-id="52785-164">이 명령은 `{APP NAME}` 자리 표시자에 제공된 값으로 이름을 지정하여 폴더를 만들고, 폴더 이름을 앱 이름으로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-164">The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name.</span></span> <span data-ttu-id="52785-165">자세한 내용은 .NET Core 가이드의 [dotnet new](/dotnet/core/tools/dotnet-new) 명령을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52785-165">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="pass-tokens-to-a-blazor-server-app"></a><span data-ttu-id="52785-166">블레이저 서버 앱에 토큰 전달</span><span class="sxs-lookup"><span data-stu-id="52785-166">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="52785-167">일반 Razor 페이지 또는 MVC 앱과 마찬가지로 Blazor Server 앱을 인증합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-167">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="52785-168">토큰을 프로비전하고 인증 쿠키에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-168">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="52785-169">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="52785-169">For example:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = "code";
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
    options.Scope.Add("{SCOPE}");
    options.Resource = "{RESOURCE}";
});
```

<span data-ttu-id="52785-170">전체 `Startup.ConfigureServices` 예제를 포함한 샘플 코드의 경우 [서버 측 Blazor 응용 프로그램에 대한 Passing 토큰을](https://github.com/javiercn/blazor-server-aad-sample)참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="52785-170">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="52785-171">액세스 및 새로 고침 토큰을 사용하여 초기 앱 상태로 전달할 클래스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-171">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="52785-172">Blazor 앱 내에서 DI에서 토큰을 해결하는 데 사용할 수 있는 **범위가 되는** 토큰 공급자 서비스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-172">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from DI:</span></span>

```csharp
using System;
using System.Security.Claims;
using System.Threading.Tasks;

public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="52785-173">에서 `Startup.ConfigureServices`다음에 대한 서비스 추가</span><span class="sxs-lookup"><span data-stu-id="52785-173">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="52785-174">*_Host.cshtml* 파일에서 응용 프로그램에 `InitialApplicationState` 매개 변수로 만들고 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="52785-174">In the *_Host.cshtml* file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<app>
    <component type="typeof(App)" param-InitialState="tokens" 
        render-mode="ServerPrerendered" />
</app>
```

<span data-ttu-id="52785-175">`App` 구성*요소(App.razor)에서*서비스를 해결하고 매개 변수의 데이터로 초기화합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-175">In the `App` component (*App.razor*), resolve the service and initialize it with the data from the parameter:</span></span>

```razor
@inject TokenProvider TokensProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokensProvider.AccessToken = InitialState.AccessToken;
        TokensProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

<span data-ttu-id="52785-176">보안 API 요청을 하는 서비스에서 토큰 공급자를 삽입 하고 토큰을 검색하여 API를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-176">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

```csharp
public class WeatherForecastService
{
    private readonly TokenProvider _store;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        Client = clientFactory.CreateClient();
        _store = tokenProvider;
    }

    public HttpClient Client { get; }

    public async Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        var token = _store.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await Client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

## <a name="resource-exhaustion"></a><span data-ttu-id="52785-177">자원 고갈</span><span class="sxs-lookup"><span data-stu-id="52785-177">Resource exhaustion</span></span>

<span data-ttu-id="52785-178">클라이언트가 서버와 상호 작용하여 서버가 과도한 리소스를 소비하게 할 때 리소스 가 소진될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-178">Resource exhaustion can occur when a client interacts with the server and causes the server to consume excessive resources.</span></span> <span data-ttu-id="52785-179">과도한 리소스 소비는 주로 다음과 같은 영향을 미칩니다.</span><span class="sxs-lookup"><span data-stu-id="52785-179">Excessive resource consumption primarily affects:</span></span>

* [<span data-ttu-id="52785-180">Cpu</span><span class="sxs-lookup"><span data-stu-id="52785-180">CPU</span></span>](#cpu)
* [<span data-ttu-id="52785-181">메모리</span><span class="sxs-lookup"><span data-stu-id="52785-181">Memory</span></span>](#memory)
* [<span data-ttu-id="52785-182">클라이언트 연결</span><span class="sxs-lookup"><span data-stu-id="52785-182">Client connections</span></span>](#client-connections)

<span data-ttu-id="52785-183">DoS(서비스 거부) 공격은 일반적으로 앱 또는 서버의 리소스를 소진하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-183">Denial of service (DoS) attacks usually seek to exhaust an app or server's resources.</span></span> <span data-ttu-id="52785-184">그러나 리소스 소모가 반드시 시스템에 대한 공격의 결과인 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="52785-184">However, resource exhaustion isn't necessarily the result of an attack on the system.</span></span> <span data-ttu-id="52785-185">예를 들어 사용자 수요가 많기 때문에 유한한 리소스가 소진될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-185">For example, finite resources can be exhausted due to high user demand.</span></span> <span data-ttu-id="52785-186">DoS는 [서비스 거부(DoS) 공격](#denial-of-service-dos-attacks) 섹션에서 자세히 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="52785-186">DoS is covered further in the [Denial of service (DoS) attacks](#denial-of-service-dos-attacks) section.</span></span>

<span data-ttu-id="52785-187">데이터베이스 및 파일 핸들(파일을 읽고 쓰는 데 사용됨)과 같은 Blazor 프레임워크 외부의 리소스도 리소스 소모가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-187">Resources external to the Blazor framework, such as databases and file handles (used to read and write files), may also experience resource exhaustion.</span></span> <span data-ttu-id="52785-188">자세한 내용은 <xref:performance/performance-best-practices>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52785-188">For more information, see <xref:performance/performance-best-practices>.</span></span>

### <a name="cpu"></a><span data-ttu-id="52785-189">CPU</span><span class="sxs-lookup"><span data-stu-id="52785-189">CPU</span></span>

<span data-ttu-id="52785-190">하나 이상의 클라이언트가 서버가 집중적인 CPU 작업을 수행하도록 강제할 때 CPU 소모가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-190">CPU exhaustion can occur when one or more clients force the server to perform intensive CPU work.</span></span>

<span data-ttu-id="52785-191">예를 *들어, 피보나치 숫자를*계산하는 Blazor Server 앱을 생각해 보십시오.</span><span class="sxs-lookup"><span data-stu-id="52785-191">For example, consider a Blazor Server app that calculates a *Fibonnacci number*.</span></span> <span data-ttu-id="52785-192">피본나치 번호는 피본아치 시퀀스에서 생성되며, 시퀀스의 각 숫자는 앞의 두 숫자의 합계입니다.</span><span class="sxs-lookup"><span data-stu-id="52785-192">A Fibonnacci number is produced from a Fibonnacci sequence, where each number in the sequence is the sum of the two preceding numbers.</span></span> <span data-ttu-id="52785-193">답변에 도달하는 데 필요한 작업량은 시퀀스의 길이와 초기 값의 크기에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="52785-193">The amount of work required to reach the answer depends on the length of the sequence and the size of the initial value.</span></span> <span data-ttu-id="52785-194">앱이 클라이언트의 요청에 제한을 두지 않으면 CPU 집약적인 계산이 CPU의 시간을 지배하고 다른 작업의 성능을 저하시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-194">If the app doesn't place limits on a client's request, the CPU-intensive calculations may dominate the CPU's time and diminish the performance of other tasks.</span></span> <span data-ttu-id="52785-195">과도한 리소스 소비는 가용성에 영향을 미치는 보안 문제입니다.</span><span class="sxs-lookup"><span data-stu-id="52785-195">Excessive resource consumption is a security concern impacting availability.</span></span>

<span data-ttu-id="52785-196">CPU 소모는 모든 공용 앱에 대한 관심사입니다.</span><span class="sxs-lookup"><span data-stu-id="52785-196">CPU exhaustion is a concern for all public-facing apps.</span></span> <span data-ttu-id="52785-197">일반 웹 앱에서는 요청 및 연결이 보호 장치로 시간 시간이 부족하지만 Blazor Server 앱은 동일한 보호 장치를 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-197">In regular web apps, requests and connections time out as a safeguard, but Blazor Server apps don't provide the same safeguards.</span></span> <span data-ttu-id="52785-198">Blazor Server 앱에는 CPU집약적인 작업을 수행하기 전에 적절한 검사와 제한이 포함되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-198">Blazor Server apps must include appropriate checks and limits before performing potentially CPU-intensive work.</span></span>

### <a name="memory"></a><span data-ttu-id="52785-199">메모리</span><span class="sxs-lookup"><span data-stu-id="52785-199">Memory</span></span>

<span data-ttu-id="52785-200">하나 이상의 클라이언트가 서버에 많은 양의 메모리를 사용하도록 강제할 때 메모리 소모가 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-200">Memory exhaustion can occur when one or more clients force the server to consume a large amount of memory.</span></span>

<span data-ttu-id="52785-201">예를 들어 항목 목록을 허용하고 표시하는 구성 요소가 있는 Blazor-server 쪽 앱을 생각해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-201">For example, consider a Blazor-server side app with a component that accepts and displays a list of items.</span></span> <span data-ttu-id="52785-202">Blazor 앱에서 허용되는 항목 수 또는 클라이언트로 다시 렌더링되는 항목 수에 제한을 두지 않는 경우 메모리 집약적인 처리 및 렌더링이 서버의 성능이 저하될 수 있는 시점까지 서버의 메모리를 지배할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-202">If the Blazor app doesn't place limits on the number of items allowed or the number of items rendered back to the client, the memory-intensive processing and rendering may dominate the server's memory to the point where performance of the server suffers.</span></span> <span data-ttu-id="52785-203">서버가 충돌한 것으로 보이는 지점까지 충돌하거나 속도가 느려질 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-203">The server may crash or slow to the point that it appears to have crashed.</span></span>

<span data-ttu-id="52785-204">서버의 잠재적인 메모리 소모 시나리오와 관련된 항목 목록을 유지 관리하고 표시하기 위한 다음 시나리오를 고려하십시오.</span><span class="sxs-lookup"><span data-stu-id="52785-204">Consider the following scenario for maintaining and displaying a list of items that pertain to a potential memory exhaustion scenario on the server:</span></span>

* <span data-ttu-id="52785-205">`List<MyItem>` 속성 또는 필드의 항목은 서버의 메모리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-205">The items in a `List<MyItem>` property or field use the server's memory.</span></span> <span data-ttu-id="52785-206">앱에서 항목 목록이 무한하게 증가하도록 허용하면 서버의 메모리가 부족할 위험이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-206">If the app allows the list of items to grow unbounded, there's a risk of the server running out of memory.</span></span> <span data-ttu-id="52785-207">메모리가 부족하면 현재 세션이 종료(충돌)되고 해당 서버 인스턴스의 모든 동시 세션이 메모리 부족 예외를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-207">Running out of memory causes the current session to end (crash) and all of the concurrent sessions in that server instance receive an out-of-memory exception.</span></span> <span data-ttu-id="52785-208">이 시나리오가 발생하지 않도록 하려면 앱에서 동시 사용자에 대한 항목 제한을 부과하는 데이터 구조를 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-208">To prevent this scenario from occurring, the app must use a data structure that imposes an item limit on concurrent users.</span></span>
* <span data-ttu-id="52785-209">페이징 스키마가 렌더링에 사용되지 않으면 서버는 UI에 표시되지 않는 개체에 대해 추가 메모리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-209">If a paging scheme isn't used for rendering, the server uses additional memory for objects that aren't visible in the UI.</span></span> <span data-ttu-id="52785-210">항목 수에 제한이 없으면 메모리 요구로 사용 가능한 서버 메모리가 소모될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-210">Without a limit on the number of items, memory demands may exhaust the available server memory.</span></span> <span data-ttu-id="52785-211">이 시나리오를 방지하려면 다음 방법 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-211">To prevent this scenario, use one of the following approaches:</span></span>
  * <span data-ttu-id="52785-212">렌더링 할 때 페이지 가을 목록을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-212">Use paginated lists when rendering.</span></span>
  * <span data-ttu-id="52785-213">처음 100~1,000개의 항목만 표시하고 사용자가 검색 조건을 입력하여 표시된 항목 이외의 항목을 찾아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-213">Only display the first 100 to 1,000 items and require the user to enter search criteria to find items beyond the items displayed.</span></span>
  * <span data-ttu-id="52785-214">고급 렌더링 시나리오의 경우 *가상화를*지원하는 목록 또는 그리드를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-214">For a more advanced rendering scenario, implement lists or grids that support *virtualization*.</span></span> <span data-ttu-id="52785-215">가상화를 사용하면 사용자에게 현재 표시되는 항목의 하위 집합만 나열합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-215">Using virtualization, lists only render a subset of items currently visible to the user.</span></span> <span data-ttu-id="52785-216">사용자가 UI에서 스크롤 막대와 상호 작용하면 구성 요소는 표시에 필요한 항목만 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-216">When the user interacts with the scrollbar in the UI, the component renders only those items required for display.</span></span> <span data-ttu-id="52785-217">현재 디스플레이에 필요하지 않은 항목은 보조 저장소에 보관할 수 있으며 이는 이상적인 방법입니다.</span><span class="sxs-lookup"><span data-stu-id="52785-217">The items that aren't currently required for display can be held in secondary storage, which is the ideal approach.</span></span> <span data-ttu-id="52785-218">표시되지 않은 항목은 메모리에 보관할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-218">Undisplayed items can also be held in memory, which is less ideal.</span></span>

<span data-ttu-id="52785-219">Blazor Server 앱은 WPF, Windows Forms 또는 Blazor WebAssembly와 같은 상태 저장 앱에 대한 다른 UI 프레임워크와 유사한 프로그래밍 모델을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-219">Blazor Server apps offer a similar programming model to other UI frameworks for stateful apps, such as WPF, Windows Forms, or Blazor WebAssembly.</span></span> <span data-ttu-id="52785-220">주요 차이점은 여러 UI 프레임워크에서 앱에서 사용하는 메모리가 클라이언트에 속하며 개별 클라이언트에만 영향을 미친다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="52785-220">The main difference is that in several of the UI frameworks the memory consumed by the app belongs to the client and only affects that individual client.</span></span> <span data-ttu-id="52785-221">예를 들어 Blazor WebAssembly 앱은 클라이언트에서 전적으로 실행되며 클라이언트 메모리 리소스만 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-221">For example, a Blazor WebAssembly app runs entirely on the client and only uses client memory resources.</span></span> <span data-ttu-id="52785-222">Blazor Server 시나리오에서 앱에서 사용하는 메모리는 서버에 속하며 서버 인스턴스의 클라이언트 간에 공유됩니다.</span><span class="sxs-lookup"><span data-stu-id="52785-222">In the Blazor Server scenario, the memory consumed by the app belongs to the server and is shared among clients on the server instance.</span></span>

<span data-ttu-id="52785-223">서버 측 메모리 요구는 모든 Blazor Server 앱에서 고려해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-223">Server-side memory demands are a consideration for all Blazor Server apps.</span></span> <span data-ttu-id="52785-224">그러나 대부분의 웹 앱은 상태 비수상태이며 요청을 처리하는 동안 사용된 메모리는 응답이 반환될 때 해제됩니다.</span><span class="sxs-lookup"><span data-stu-id="52785-224">However, most web apps are stateless, and the memory used while processing a request is released when the response is returned.</span></span> <span data-ttu-id="52785-225">일반적으로 클라이언트가 클라이언트 연결을 유지하는 다른 서버 쪽 앱과 같이 언바운드 양의 메모리를 할당하도록 허용하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="52785-225">As a general recommendation, don't permit clients to allocate an unbound amount of memory as in any other server-side app that persists client connections.</span></span> <span data-ttu-id="52785-226">Blazor Server 앱에서 사용하는 메모리는 단일 요청보다 더 긴 시간 동안 유지됩니다.</span><span class="sxs-lookup"><span data-stu-id="52785-226">The memory consumed by a Blazor Server app persists for a longer time than a single request.</span></span>

> [!NOTE]
> <span data-ttu-id="52785-227">개발 중에 프로파일러를 사용하거나 캡처한 추적을 사용하여 클라이언트의 메모리 요구를 평가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-227">During development, a profiler can be used or a trace captured to assess memory demands of clients.</span></span> <span data-ttu-id="52785-228">프로파일러 또는 추적은 특정 클라이언트에 할당된 메모리를 캡처하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-228">A profiler or trace won't capture the memory allocated to a specific client.</span></span> <span data-ttu-id="52785-229">개발 중에 특정 클라이언트의 메모리 사용을 캡처하려면 덤프를 캡처하고 사용자의 회로에 루팅된 모든 개체의 메모리 수요를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-229">To capture the memory use of a specific client during development, capture a dump and examine the memory demand of all the objects rooted at a user's circuit.</span></span>

### <a name="client-connections"></a><span data-ttu-id="52785-230">클라이언트 연결</span><span class="sxs-lookup"><span data-stu-id="52785-230">Client connections</span></span>

<span data-ttu-id="52785-231">하나 이상의 클라이언트가 서버에 너무 많은 동시 연결을 열어 다른 클라이언트가 새 연결을 설정하지 못하도록 하면 연결이 끊어지기도 합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-231">Connection exhaustion can occur when one or more clients open too many concurrent connections to the server, preventing other clients from establishing new connections.</span></span>

<span data-ttu-id="52785-232">Blazor 클라이언트는 세션당 단일 연결을 설정하고 브라우저 창이 열려 있는 동안 연결을 열어 두십시오.</span><span class="sxs-lookup"><span data-stu-id="52785-232">Blazor clients establish a single connection per session and keep the connection open for as long as the browser window is open.</span></span> <span data-ttu-id="52785-233">모든 연결을 유지 관리하는 서버에 대한 요구는 Blazor 앱에만 국한되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-233">The demands on the server of maintaining all of the connections isn't specific to Blazor apps.</span></span> <span data-ttu-id="52785-234">Blazor Server 앱의 연결의 지속적인 특성과 상태 저장 특성을 고려할 때 연결 고갈은 앱 가용성에 더 큰 위험이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-234">Given the persistent nature of the connections and the stateful nature of Blazor Server apps, connection exhaustion is a greater risk to availability of the app.</span></span>

<span data-ttu-id="52785-235">기본적으로 Blazor Server 앱의 사용자당 연결 수에는 제한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-235">By default, there's no limit on the number of connections per user for a Blazor Server app.</span></span> <span data-ttu-id="52785-236">앱에 연결 제한이 필요한 경우 다음 방법 중 하나 이상을 수행하십시오.</span><span class="sxs-lookup"><span data-stu-id="52785-236">If the app requires a connection limit, take one or more of the following approaches:</span></span>

* <span data-ttu-id="52785-237">권한이 없는 사용자가 앱에 연결하는 기능을 자연스럽게 제한하는 인증이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-237">Require authentication, which naturally limits the ability of unauthorized users to connect to the app.</span></span> <span data-ttu-id="52785-238">이 시나리오가 효과적이려면 사용자가 새 사용자를 즉시 프로비전할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-238">For this scenario to be effective, users must be prevented from provisioning new users at will.</span></span>
* <span data-ttu-id="52785-239">사용자당 연결 수를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-239">Limit the number of connections per user.</span></span> <span data-ttu-id="52785-240">연결 제한은 다음 방법을 통해 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-240">Limiting connections can be accomplished via the following approaches.</span></span> <span data-ttu-id="52785-241">합법적인 사용자가 앱에 액세스할 수 있도록 주의하십시오(예: 클라이언트의 IP 주소에 따라 연결 제한이 설정된 경우).</span><span class="sxs-lookup"><span data-stu-id="52785-241">Exercise care to allow legitimate users to access the app (for example, when a connection limit is established based on the client's IP address).</span></span>
  * <span data-ttu-id="52785-242">앱 수준에서:</span><span class="sxs-lookup"><span data-stu-id="52785-242">At the app level:</span></span>
    * <span data-ttu-id="52785-243">끝점 라우팅 확장성.</span><span class="sxs-lookup"><span data-stu-id="52785-243">Endpoint routing extensibility.</span></span>
    * <span data-ttu-id="52785-244">앱에 연결하고 사용자당 활성 세션을 추적하려면 인증이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-244">Require authentication to connect to the app and keep track of the active sessions per user.</span></span>
    * <span data-ttu-id="52785-245">한도에 도달하면 새 세션을 거부합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-245">Reject new sessions upon reaching a limit.</span></span>
    * <span data-ttu-id="52785-246">프록시 WebSocket 클라이언트에서 앱으로의 연결을 다중화하는 [Azure SignalR 서비스와](/azure/azure-signalr/signalr-overview) 같은 프록시를 사용하여 앱에 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-246">Proxy WebSocket connections to an app through the use of a proxy, such as the [Azure SignalR Service](/azure/azure-signalr/signalr-overview) that multiplexes connections from clients to an app.</span></span> <span data-ttu-id="52785-247">이렇게 하면 단일 클라이언트가 설정할 수 있는 것보다 더 큰 연결 용량을 가진 앱이 제공되어 클라이언트가 서버에 대한 연결을 모두 소모하지 못하게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="52785-247">This provides an app with greater connection capacity than a single client can establish, preventing a client from exhausting the connections to the server.</span></span>
  * <span data-ttu-id="52785-248">서버 수준에서: 앱 앞에 있는 프록시/게이트웨이를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-248">At the server level: Use a proxy/gateway in front of the app.</span></span> <span data-ttu-id="52785-249">예를 들어 [Azure Front Door를](/azure/frontdoor/front-door-overview) 사용하면 앱에 대한 웹 트래픽의 전역 라우팅을 정의, 관리 및 모니터링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-249">For example, [Azure Front Door](/azure/frontdoor/front-door-overview) enables you to define, manage, and monitor the global routing of web traffic to an app.</span></span>

## <a name="denial-of-service-dos-attacks"></a><span data-ttu-id="52785-250">서비스 거부(DoS) 공격</span><span class="sxs-lookup"><span data-stu-id="52785-250">Denial of service (DoS) attacks</span></span>

<span data-ttu-id="52785-251">DoS(서비스 거부) 공격에는 클라이언트가 하나 이상의 리소스를 소모하여 앱을 사용할 수 없게 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="52785-251">Denial of service (DoS) attacks involve a client causing the server to exhaust one or more of its resources making the app unavailable.</span></span> <span data-ttu-id="52785-252">Blazor Server 앱에는 몇 가지 기본 제한이 포함되어 있으며 DoS 공격으로부터 보호하기 위해 다른 ASP.NET 코어 및 SignalR 제한을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-252">Blazor Server apps include some default limits and rely on other ASP.NET Core and SignalR limits to protect against DoS attacks:</span></span>

| <span data-ttu-id="52785-253">블레이저 서버 앱 제한</span><span class="sxs-lookup"><span data-stu-id="52785-253">Blazor Server app limit</span></span>                            | <span data-ttu-id="52785-254">Description</span><span class="sxs-lookup"><span data-stu-id="52785-254">Description</span></span> | <span data-ttu-id="52785-255">기본값</span><span class="sxs-lookup"><span data-stu-id="52785-255">Default</span></span> |
| ------------------------------------------------------- | ----------- | ------- |
| `CircuitOptions.DisconnectedCircuitMaxRetained`         | <span data-ttu-id="52785-256">지정된 서버가 한 번에 메모리에 보유하는 연결이 끊긴 회로의 최대 수입니다.</span><span class="sxs-lookup"><span data-stu-id="52785-256">Maximum number of disconnected circuits that a given server holds in memory at a time.</span></span> | <span data-ttu-id="52785-257">100</span><span class="sxs-lookup"><span data-stu-id="52785-257">100</span></span> |
| `CircuitOptions.DisconnectedCircuitRetentionPeriod`     | <span data-ttu-id="52785-258">연결이 끊긴 회로가 분리되기 전에 메모리에 보관되는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="52785-258">Maximum amount of time a disconnected circuit is held in memory before being torn down.</span></span> | <span data-ttu-id="52785-259">3분</span><span class="sxs-lookup"><span data-stu-id="52785-259">3 minutes</span></span> |
| `CircuitOptions.JSInteropDefaultCallTimeout`            | <span data-ttu-id="52785-260">비동기 JavaScript 함수 호출을 타이밍 아웃하기 전에 서버가 대기하는 최대 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="52785-260">Maximum amount of time the server waits before timing out an asynchronous JavaScript function invocation.</span></span> | <span data-ttu-id="52785-261">1분</span><span class="sxs-lookup"><span data-stu-id="52785-261">1 minute</span></span> |
| `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches` | <span data-ttu-id="52785-262">승인되지 않은 최대 렌더 일괄 처리 수는 서버가 강력한 재연결을 지원하기 위해 지정된 시간에 회로당 메모리에 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-262">Maximum number of unacknowledged render batches the server keeps in memory per circuit at a given time to support robust reconnection.</span></span> <span data-ttu-id="52785-263">제한에 도달하면 서버는 클라이언트에서 하나 이상의 일괄 처리가 승인될 때까지 새 렌더 일괄 처리를 생성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-263">After reaching the limit, the server stops producing new render batches until one or more batches have been acknowledged by the client.</span></span> | <span data-ttu-id="52785-264">10</span><span class="sxs-lookup"><span data-stu-id="52785-264">10</span></span> |


| <span data-ttu-id="52785-265">시그널R 및 ASP.NET 코어 한계</span><span class="sxs-lookup"><span data-stu-id="52785-265">SignalR and ASP.NET Core limit</span></span>             | <span data-ttu-id="52785-266">Description</span><span class="sxs-lookup"><span data-stu-id="52785-266">Description</span></span> | <span data-ttu-id="52785-267">기본값</span><span class="sxs-lookup"><span data-stu-id="52785-267">Default</span></span> |
| ------------------------------------------ | ----------- | ------- |
| `CircuitOptions.MaximumReceiveMessageSize` | <span data-ttu-id="52785-268">개별 메시지에 대한 메시지 크기입니다.</span><span class="sxs-lookup"><span data-stu-id="52785-268">Message size for an individual message.</span></span> | <span data-ttu-id="52785-269">32KB</span><span class="sxs-lookup"><span data-stu-id="52785-269">32 KB</span></span> |

## <a name="interactions-with-the-browser-client"></a><span data-ttu-id="52785-270">브라우저와의 상호 작용(클라이언트)</span><span class="sxs-lookup"><span data-stu-id="52785-270">Interactions with the browser (client)</span></span>

<span data-ttu-id="52785-271">클라이언트는 JS interop 이벤트 디스패치 및 렌더링 완료를 통해 서버와 상호 작용합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-271">A client interacts with the server through JS interop event dispatching and render completion.</span></span> <span data-ttu-id="52785-272">JS 인터옵 통신은 자바 스크립트와 .NET 사이에 두 가지 방법을 간다 :</span><span class="sxs-lookup"><span data-stu-id="52785-272">JS interop communication goes both ways between JavaScript and .NET:</span></span>

* <span data-ttu-id="52785-273">브라우저 이벤트는 클라이언트에서 서버로 비동기 방식으로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="52785-273">Browser events are dispatched from the client to the server in an asynchronous fashion.</span></span>
* <span data-ttu-id="52785-274">서버는 필요에 따라 UI를 비동기적으로 다시 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-274">The server responds asynchronously rerendering the UI as necessary.</span></span>

### <a name="javascript-functions-invoked-from-net"></a><span data-ttu-id="52785-275">.NET에서 호출된 자바스크립트 함수</span><span class="sxs-lookup"><span data-stu-id="52785-275">JavaScript functions invoked from .NET</span></span>

<span data-ttu-id="52785-276">.NET 메서드에서 자바스크립트로 의 호출하는 경우:</span><span class="sxs-lookup"><span data-stu-id="52785-276">For calls from .NET methods to JavaScript:</span></span>

* <span data-ttu-id="52785-277">모든 호출에는 구성 가능한 시간 시간이 있으며, 그 <xref:System.OperationCanceledException> 후에는 실패하여 호출자에게 a를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-277">All invocations have a configurable timeout after which they fail, returning a <xref:System.OperationCanceledException> to the caller.</span></span>
  * <span data-ttu-id="52785-278">1분의 호출()에`CircuitOptions.JSInteropDefaultCallTimeout`대한 기본 시간 시간이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-278">There's a default timeout for the calls (`CircuitOptions.JSInteropDefaultCallTimeout`) of one minute.</span></span> <span data-ttu-id="52785-279">이 제한을 구성하려면 <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="52785-279">To configure this limit, see <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>.</span></span>
  * <span data-ttu-id="52785-280">호출별로 취소를 제어하기 위해 취소 토큰을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-280">A cancellation token can be provided to control the cancellation on a per-call basis.</span></span> <span data-ttu-id="52785-281">가능한 경우 기본 호출 시간 시간에 의존하고 취소 토큰이 제공되는 경우 클라이언트에 대한 모든 호출을 시간 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-281">Rely on the default call timeout where possible and time-bound any call to the client if a cancellation token is provided.</span></span>
* <span data-ttu-id="52785-282">자바 스크립트 호출의 결과를 신뢰할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-282">The result of a JavaScript call can't be trusted.</span></span> <span data-ttu-id="52785-283">브라우저에서 실행 중인 앱 클라이언트는 Blazor 호출할 JavaScript 함수를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-283">The Blazor app client running in the browser searches for the JavaScript function to invoke.</span></span> <span data-ttu-id="52785-284">함수가 호출되고 결과 또는 오류가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="52785-284">The function is invoked, and either the result or an error is produced.</span></span> <span data-ttu-id="52785-285">악의적인 클라이언트는 다음을 시도할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-285">A malicious client can attempt to:</span></span>
  * <span data-ttu-id="52785-286">자바 스크립트 함수에서 오류를 반환하여 응용 프로그램에 문제가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-286">Cause an issue in the app by returning an error from the JavaScript function.</span></span>
  * <span data-ttu-id="52785-287">JavaScript 함수에서 예기치 않은 결과를 반환하여 서버에서 의도하지 않은 동작을 유도합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-287">Induce an unintended behavior on the server by returning an unexpected result from the JavaScript function.</span></span>

<span data-ttu-id="52785-288">위의 시나리오에 대비하기 위해 다음 예방 조치를 취하십시오.</span><span class="sxs-lookup"><span data-stu-id="52785-288">Take the following precautions to guard against the preceding scenarios:</span></span>

* <span data-ttu-id="52785-289">호출 중에 발생할 수 있는 오류를 고려하기 위해 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) 문 내에서 JS interop 호출을 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-289">Wrap JS interop calls within [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements to account for errors that might occur during the invocations.</span></span> <span data-ttu-id="52785-290">자세한 내용은 <xref:blazor/handle-errors#javascript-interop>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52785-290">For more information, see <xref:blazor/handle-errors#javascript-interop>.</span></span>
* <span data-ttu-id="52785-291">작업을 수행하기 전에 오류 메시지를 포함하여 JS interop 호출에서 반환된 데이터의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-291">Validate data returned from JS interop invocations, including error messages, before taking any action.</span></span>

### <a name="net-methods-invoked-from-the-browser"></a><span data-ttu-id="52785-292">브라우저에서 호출된 .NET 메서드</span><span class="sxs-lookup"><span data-stu-id="52785-292">.NET methods invoked from the browser</span></span>

<span data-ttu-id="52785-293">JavaScript에서 .NET 메서드로 의 호출을 신뢰하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="52785-293">Don't trust calls from JavaScript to .NET methods.</span></span> <span data-ttu-id="52785-294">.NET 메서드가 JavaScript에 노출되면 .NET 메서드가 호출되는 방식을 고려하십시오.</span><span class="sxs-lookup"><span data-stu-id="52785-294">When a .NET method is exposed to JavaScript, consider how the .NET method is invoked:</span></span>

* <span data-ttu-id="52785-295">앱에 대한 공용 끝점과 마찬가지로 JavaScript에 노출된 모든 .NET 메서드를 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-295">Treat any .NET method exposed to JavaScript as you would a public endpoint to the app.</span></span>
  * <span data-ttu-id="52785-296">입력의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-296">Validate input.</span></span>
    * <span data-ttu-id="52785-297">값이 예상 범위 내에 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-297">Ensure that values are within expected ranges.</span></span>
    * <span data-ttu-id="52785-298">사용자에게 요청된 작업을 수행할 수 있는 권한이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-298">Ensure that the user has permission to perform the action requested.</span></span>
  * <span data-ttu-id="52785-299">.NET 메서드 호출의 일부로 과도한 양의 리소스를 할당하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="52785-299">Don't allocate an excessive quantity of resources as part of the .NET method invocation.</span></span> <span data-ttu-id="52785-300">예를 들어, CPU 및 메모리 사용에 대한 검사 및 배치 제한을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-300">For example, perform checks and place limits on CPU and memory use.</span></span>
  * <span data-ttu-id="52785-301">정적 및 인스턴스 메서드가 JavaScript 클라이언트에 노출될 수 있음을 고려하십시오.</span><span class="sxs-lookup"><span data-stu-id="52785-301">Take into account that static and instance methods can be exposed to JavaScript clients.</span></span> <span data-ttu-id="52785-302">디자인에서 적절한 제약 조건으로 상태를 공유해야 하는 경우가 아니면 세션 간에 상태를 공유하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="52785-302">Avoid sharing state across sessions unless the design calls for sharing state with appropriate constraints.</span></span>
    * <span data-ttu-id="52785-303">예를 들어 DI(종속성 주입)를 통해 원래 생성된 개체를 통해 `DotNetReference` 노출된 메서드의 경우 개체를 범위 가 있는 개체로 등록해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-303">For instance methods exposed through `DotNetReference` objects that are originally created through dependency injection (DI), the objects should be registered as scoped objects.</span></span> <span data-ttu-id="52785-304">이는 서버 앱이 Blazor 사용하는 모든 DI 서비스에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="52785-304">This applies to any DI service that the Blazor Server app uses.</span></span>
    * <span data-ttu-id="52785-305">정적 메서드의 경우 앱이 서버 인스턴스의 모든 사용자에 대해 상태 별 디자인을 명시적으로 공유하지 않는 한 클라이언트에 범위를 지정할 수 없는 상태를 설정하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="52785-305">For static methods, avoid establishing state that can't be scoped to the client unless the app is explicitly sharing state by-design across all users on a server instance.</span></span>
  * <span data-ttu-id="52785-306">사용자가 제공한 데이터를 매개 변수로 JavaScript 호출로 전달하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="52785-306">Avoid passing user-supplied data in parameters to JavaScript calls.</span></span> <span data-ttu-id="52785-307">매개 변수로 데이터를 전달하는 것이 절대적으로 필요한 경우 JavaScript 코드가 [XSS(교차 사이트 스크립팅)](#cross-site-scripting-xss) 취약점을 도입하지 않고 데이터를 전달하는 것을 처리해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-307">If passing data in parameters is absolutely required, ensure that the JavaScript code handles passing the data without introducing [Cross-site scripting (XSS)](#cross-site-scripting-xss) vulnerabilities.</span></span> <span data-ttu-id="52785-308">예를 들어 요소의 속성을 설정하여 사용자가 제공한 데이터를 DOM(문서 `innerHTML` 개체 모델)에 쓰지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="52785-308">For example, don't write user-supplied data to the Document Object Model (DOM) by setting the `innerHTML` property of an element.</span></span> <span data-ttu-id="52785-309">[CSP(콘텐츠 보안 정책)를](https://developer.mozilla.org/docs/Web/HTTP/CSP) `eval` 사용하여 안전하지 않은 JavaScript 프리미티브를 사용하지 않도록 설정하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-309">Consider using [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) to disable `eval` and other unsafe JavaScript primitives.</span></span>
* <span data-ttu-id="52785-310">프레임워크의 디스패치 구현 위에 .NET 호출의 사용자 지정 디스패치를 구현하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="52785-310">Avoid implementing custom dispatching of .NET invocations on top of the framework's dispatching implementation.</span></span> <span data-ttu-id="52785-311">.NET 메서드를 브라우저에 노출하는 것은 일반적인 Blazor 개발에권장되지 않는 고급 시나리오입니다.</span><span class="sxs-lookup"><span data-stu-id="52785-311">Exposing .NET methods to the browser is an advanced scenario, not recommended for general Blazor development.</span></span>

### <a name="events"></a><span data-ttu-id="52785-312">이벤트</span><span class="sxs-lookup"><span data-stu-id="52785-312">Events</span></span>

<span data-ttu-id="52785-313">이벤트는 Blazor 서버 앱에 대한 진입점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-313">Events provide an entry point to a Blazor Server app.</span></span> <span data-ttu-id="52785-314">웹 앱의 엔드포인트 보호에 대한 동일한 규칙이 Blazor Server 앱의 이벤트 처리에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="52785-314">The same rules for safeguarding endpoints in web apps apply to event handling in Blazor Server apps.</span></span> <span data-ttu-id="52785-315">악의적인 클라이언트는 이벤트의 페이로드로 전송하려는 모든 데이터를 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-315">A malicious client can send any data it wishes to send as the payload for an event.</span></span>

<span data-ttu-id="52785-316">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="52785-316">For example:</span></span>

* <span data-ttu-id="52785-317">에 `<select>` 대한 변경 이벤트는 앱이 클라이언트에 제공하는 옵션 내에 없는 값을 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-317">A change event for a `<select>` could send a value that isn't within the options that the app presented to the client.</span></span>
* <span data-ttu-id="52785-318">A는 `<input>` 클라이언트 측 유효성 검사를 우회하여 서버에 텍스트 데이터를 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-318">An `<input>` could send any text data to the server, bypassing client-side validation.</span></span>

<span data-ttu-id="52785-319">앱은 앱이 처리하는 모든 이벤트에 대한 데이터의 유효성을 검사해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-319">The app must validate the data for any event that the app handles.</span></span> <span data-ttu-id="52785-320">Blazor 프레임워크는 [구성 요소가](xref:blazor/forms-validation) 기본 유효성 검사를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-320">The Blazor framework [forms components](xref:blazor/forms-validation) perform basic validations.</span></span> <span data-ttu-id="52785-321">앱에서 사용자 지정 양식 구성 요소를 사용하는 경우 이벤트 데이터의 유효성을 검사하기 위해 사용자 지정 코드를 작성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-321">If the app uses custom forms components, custom code must be written to validate event data as appropriate.</span></span>

Blazor<span data-ttu-id="52785-322">서버 이벤트는 비동기이므로 앱이 새 렌더링을 생성하여 반응할 시간이 있기 전에 여러 이벤트를 서버로 디스패치할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-322"> Server events are asynchronous, so multiple events can be dispatched to the server before the app has time to react by producing a new render.</span></span> <span data-ttu-id="52785-323">이는 고려해야 할 몇 가지 보안 의미가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-323">This has some security implications to consider.</span></span> <span data-ttu-id="52785-324">앱에서 클라이언트 작업을 제한하는 것은 이벤트 처리기 내에서 수행되어야 하며 현재 렌더링된 뷰 상태에 의존하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-324">Limiting client actions in the app must be performed inside event handlers and not depend on the current rendered view state.</span></span>

<span data-ttu-id="52785-325">사용자가 카운터를 최대 3배까지 증분할 수 있도록 해야 하는 카운터 구성 요소를 고려해 보십시오.</span><span class="sxs-lookup"><span data-stu-id="52785-325">Consider a counter component that should allow a user to increment a counter a maximum of three times.</span></span> <span data-ttu-id="52785-326">카운터를 증분하는 버튼은 다음 의 값을 `count`기준으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-326">The button to increment the counter is conditionally based on the value of `count`:</span></span>

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        count++;
    }
}
```

<span data-ttu-id="52785-327">클라이언트는 프레임워크가 이 구성 요소의 새 렌더링을 생성하기 전에 하나 이상의 증분 이벤트를 디스패치할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-327">A client can dispatch one or more increment events before the framework produces a new render of this component.</span></span> <span data-ttu-id="52785-328">결과적으로 사용자가 단추를 `count` UI에서 충분히 빠르게 제거하지 않기 때문에 사용자가 세 번 *이상* 증분할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-328">The result is that the `count` can be incremented *over three times* by the user because the button isn't removed by the UI quickly enough.</span></span> <span data-ttu-id="52785-329">세 `count` 증분의 제한을 달성하는 올바른 방법은 다음 예제에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-329">The correct way to achieve the limit of three `count` increments is shown in the following example:</span></span>

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        if (count < 3)
        {
            count++;
        }
    }
}
```

<span data-ttu-id="52785-330">처리기 `if (count < 3) { ... }` 내부에 검사를 추가하면 증분 `count` 결정은 현재 앱 상태를 기반으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-330">By adding the `if (count < 3) { ... }` check inside the handler, the decision to increment `count` is based on the current app state.</span></span> <span data-ttu-id="52785-331">결정은 이전 예제에서와 같이 UI의 상태를 기반으로 하지 않습니다., 일시적으로 부실 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-331">The decision isn't based on the state of the UI as it was in the previous example, which might be temporarily stale.</span></span>

### <a name="guard-against-multiple-dispatches"></a><span data-ttu-id="52785-332">여러 디스패치에 대한 보호</span><span class="sxs-lookup"><span data-stu-id="52785-332">Guard against multiple dispatches</span></span>

<span data-ttu-id="52785-333">이벤트 콜백이 외부 서비스 또는 데이터베이스에서 데이터를 가져오는 등 비동기적으로 장기 실행 작업을 호출하는 경우 guard를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-333">If an event callback invokes a long running operation asynchronously, such as fetching data from an external service or database, consider using a guard.</span></span> <span data-ttu-id="52785-334">가드는 시각적 피드백을 통해 작업이 진행되는 동안 사용자가 여러 작업을 큐에 대기하지 못하도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-334">The guard can prevent the user from queueing up multiple operations while the operation is in progress with visual feedback.</span></span> <span data-ttu-id="52785-335">다음 구성 요소 `isLoading` `true` 코드는 서버에서 데이터를 가져오는 동안으로 `GetForecastAsync` 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="52785-335">The following component code sets `isLoading` to `true` while `GetForecastAsync` obtains data from the server.</span></span> <span data-ttu-id="52785-336">`isLoading` 동안, `true`버튼은 UI에서 비활성화됩니다 .</span><span class="sxs-lookup"><span data-stu-id="52785-336">While `isLoading` is `true`, the button is disabled in the UI:</span></span>

```razor
@page "/fetchdata"
@using BlazorServerSample.Data
@inject WeatherForecastService ForecastService

<button disabled="@isLoading" @onclick="UpdateForecasts">Update</button>

@code {
    private bool isLoading;
    private WeatherForecast[] forecasts;

    private async Task UpdateForecasts()
    {
        if (!isLoading)
        {
            isLoading = true;
            forecasts = await ForecastService.GetForecastAsync(DateTime.Now);
            isLoading = false;
        }
    }
}
```

<span data-ttu-id="52785-337">배경 작업이 패턴과 비동기적으로 실행되는 경우 앞의 예제에서 보여 `async` - `await` 준 가드 패턴이 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-337">The guard pattern demonstrated in the preceding example works if the background operation is executed asynchronously with the `async`-`await` pattern.</span></span>

### <a name="cancel-early-and-avoid-use-after-dispose"></a><span data-ttu-id="52785-338">조기 취소 및 폐기 후 사용 방지</span><span class="sxs-lookup"><span data-stu-id="52785-338">Cancel early and avoid use-after-dispose</span></span>

<span data-ttu-id="52785-339">[여러 디스패치 섹션에 대해 Guard에](#guard-against-multiple-dispatches) 설명된 대로 가드를 <xref:System.Threading.CancellationToken> 사용하는 것 외에도 구성 요소를 삭제할 때 장기 실행 작업을 취소하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-339">In addition to using a guard as described in the [Guard against multiple dispatches](#guard-against-multiple-dispatches) section, consider using a <xref:System.Threading.CancellationToken> to cancel long-running operations when the component is disposed.</span></span> <span data-ttu-id="52785-340">이 방법은 구성 요소에서 *사후 배치를* 사용하지 않아도 되는 추가이점입니다.</span><span class="sxs-lookup"><span data-stu-id="52785-340">This approach has the added benefit of avoiding *use-after-dispose* in components:</span></span>

```razor
@implements IDisposable

...

@code {
    private readonly CancellationTokenSource TokenSource = 
        new CancellationTokenSource();

    private async Task UpdateForecasts()
    {
        ...

        forecasts = await ForecastService.GetForecastAsync(DateTime.Now, 
            TokenSource.Token);

        if (TokenSource.Token.IsCancellationRequested)
        {
           return;
        }

        ...
    }

    public void Dispose()
    {
        TokenSource.Cancel();
    }
}
```

### <a name="avoid-events-that-produce-large-amounts-of-data"></a><span data-ttu-id="52785-341">많은 양의 데이터를 생성하는 이벤트 방지</span><span class="sxs-lookup"><span data-stu-id="52785-341">Avoid events that produce large amounts of data</span></span>

<span data-ttu-id="52785-342">또는 와 `oninput` `onscroll`같은 일부 DOM 이벤트는 많은 양의 데이터를 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-342">Some DOM events, such as `oninput` or `onscroll`, can produce a large amount of data.</span></span> <span data-ttu-id="52785-343">서버 앱에서 Blazor 이러한 이벤트를 사용하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="52785-343">Avoid using these events in Blazor server apps.</span></span>

## <a name="additional-security-guidance"></a><span data-ttu-id="52785-344">추가 보안 지침</span><span class="sxs-lookup"><span data-stu-id="52785-344">Additional security guidance</span></span>

<span data-ttu-id="52785-345">ASP.NET 핵심 앱 보안 지침은 Blazor 서버 앱에 적용되며 다음 섹션에서 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="52785-345">The guidance for securing ASP.NET Core apps apply to Blazor Server apps and are covered in the following sections:</span></span>

* [<span data-ttu-id="52785-346">로깅 및 중요한 데이터</span><span class="sxs-lookup"><span data-stu-id="52785-346">Logging and sensitive data</span></span>](#logging-and-sensitive-data)
* [<span data-ttu-id="52785-347">HTTPS를 통해 전송 중 정보 보호</span><span class="sxs-lookup"><span data-stu-id="52785-347">Protect information in transit with HTTPS</span></span>](#protect-information-in-transit-with-https)
* <span data-ttu-id="52785-348">[크로스 사이트 스크립팅(XSS)](#cross-site-scripting-xss))</span><span class="sxs-lookup"><span data-stu-id="52785-348">[Cross-site scripting (XSS)](#cross-site-scripting-xss))</span></span>
* [<span data-ttu-id="52785-349">원산지 간 보호</span><span class="sxs-lookup"><span data-stu-id="52785-349">Cross-origin protection</span></span>](#cross-origin-protection)
* [<span data-ttu-id="52785-350">클릭 재킹</span><span class="sxs-lookup"><span data-stu-id="52785-350">Click-jacking</span></span>](#click-jacking)
* [<span data-ttu-id="52785-351">열기 리디렉션</span><span class="sxs-lookup"><span data-stu-id="52785-351">Open redirects</span></span>](#open-redirects)

### <a name="logging-and-sensitive-data"></a><span data-ttu-id="52785-352">로깅 및 중요한 데이터</span><span class="sxs-lookup"><span data-stu-id="52785-352">Logging and sensitive data</span></span>

<span data-ttu-id="52785-353">클라이언트와 서버 간의 JS 상호 작용은 인스턴스가 있는 <xref:Microsoft.Extensions.Logging.ILogger> 서버의 로그에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="52785-353">JS interop interactions between the client and server are recorded in the server's logs with <xref:Microsoft.Extensions.Logging.ILogger> instances.</span></span> Blazor<span data-ttu-id="52785-354">는 실제 이벤트 나 JS 인터옵 입력 및 출력과 같은 중요한 정보를 로깅하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-354"> avoids logging sensitive information, such as actual events or JS interop inputs and outputs.</span></span>

<span data-ttu-id="52785-355">서버에서 오류가 발생하면 프레임워크는 클라이언트에게 통보하고 세션을 중단합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-355">When an error occurs on the server, the framework notifies the client and tears down the session.</span></span> <span data-ttu-id="52785-356">기본적으로 클라이언트는 브라우저의 개발자 도구에서 볼 수 있는 일반 오류 메시지를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-356">By default, the client receives a generic error message that can be seen in the browser's developer tools.</span></span>

<span data-ttu-id="52785-357">클라이언트 측 오류는 호출 스택을 포함하지 않으며 오류의 원인에 대한 세부 정보를 제공하지 않지만 서버 로그에는 이러한 정보가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-357">The client-side error doesn't include the callstack and doesn't provide detail on the cause of the error, but server logs do contain such information.</span></span> <span data-ttu-id="52785-358">개발을 위해 자세한 오류를 활성화하여 클라이언트에서 중요한 오류 정보를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-358">For development purposes, sensitive error information can be made available to the client by enabling detailed errors.</span></span>

<span data-ttu-id="52785-359">다음과 같은 경우 자세한 오류를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-359">Enable detailed errors with:</span></span>

* <span data-ttu-id="52785-360">`CircuitOptions.DetailedErrors`.</span><span class="sxs-lookup"><span data-stu-id="52785-360">`CircuitOptions.DetailedErrors`.</span></span>
* <span data-ttu-id="52785-361">`DetailedErrors`구성 키를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-361">`DetailedErrors` configuration key.</span></span> <span data-ttu-id="52785-362">예를 들어 환경 `ASPNETCORE_DETAILEDERRORS` 변수를 `true`의 값으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-362">For example, set the `ASPNETCORE_DETAILEDERRORS` environment variable to a value of `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="52785-363">인터넷에서 클라이언트에 오류 정보를 노출하는 것은 항상 피해야 하는 보안 위험입니다.</span><span class="sxs-lookup"><span data-stu-id="52785-363">Exposing error information to clients on the Internet is a security risk that should always be avoided.</span></span>

### <a name="protect-information-in-transit-with-https"></a><span data-ttu-id="52785-364">HTTPS를 통해 전송 중 정보 보호</span><span class="sxs-lookup"><span data-stu-id="52785-364">Protect information in transit with HTTPS</span></span>

Blazor<span data-ttu-id="52785-365">서버는 SignalR 클라이언트와 서버 간의 통신을 위해 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-365"> Server uses SignalR for communication between the client and the server.</span></span> Blazor<span data-ttu-id="52785-366">서버는 일반적으로 WebSockets인 협상하는 SignalR 전송을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-366"> Server normally uses the transport that SignalR negotiates, which is typically WebSockets.</span></span>

Blazor<span data-ttu-id="52785-367">서버는 서버와 클라이언트 간에 전송되는 데이터의 무결성과 기밀성을 보장하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-367"> Server doesn't ensure the integrity and confidentiality of the data sent between the server and the client.</span></span> <span data-ttu-id="52785-368">항상 HTTPS를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-368">Always use HTTPS.</span></span>

### <a name="cross-site-scripting-xss"></a><span data-ttu-id="52785-369">크로스 사이트 스크립팅(XSS)</span><span class="sxs-lookup"><span data-stu-id="52785-369">Cross-site scripting (XSS)</span></span>

<span data-ttu-id="52785-370">XSS(교차 사이트 스크립팅)를 사용하면 권한이 없는 당사자가 브라우저컨텍스트에서 임의로 논리를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-370">Cross-site scripting (XSS) allows an unauthorized party to execute arbitrary logic in the context of the browser.</span></span> <span data-ttu-id="52785-371">손상된 앱은 클라이언트에서 임의의 코드를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-371">A compromised app could potentially run arbitrary code on the client.</span></span> <span data-ttu-id="52785-372">이 취약점은 잠재적으로 서버에 대한 여러 가지 악의적인 작업을 수행하는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-372">The vulnerability could be used to potentially perform a number of malicious actions against the server:</span></span>

* <span data-ttu-id="52785-373">가짜/잘못된 이벤트를 서버에 디스패치합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-373">Dispatch fake/invalid events to the server.</span></span>
* <span data-ttu-id="52785-374">디스패치 실패/잘못된 렌더링 완료.</span><span class="sxs-lookup"><span data-stu-id="52785-374">Dispatch fail/invalid render completions.</span></span>
* <span data-ttu-id="52785-375">렌더링 완료를 디스패치하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="52785-375">Avoid dispatching render completions.</span></span>
* <span data-ttu-id="52785-376">자바스크립트에서 .NET으로 인터옵 호출을 디스패치합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-376">Dispatch interop calls from JavaScript to .NET.</span></span>
* <span data-ttu-id="52785-377">.NET에서 자바스크립트로 인터옵 호출의 응답을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-377">Modify the response of interop calls from .NET to JavaScript.</span></span>
* <span data-ttu-id="52785-378">JS 인터롭 결과에 .NET을 디스패치하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="52785-378">Avoid dispatching .NET to JS interop results.</span></span>

<span data-ttu-id="52785-379">Server Blazor 프레임워크는 앞의 위협 중 일부로부터 보호하기 위한 조치를 취합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-379">The Blazor Server framework takes steps to protect against some of the preceding threats:</span></span>

* <span data-ttu-id="52785-380">클라이언트가 렌더 일괄 처리를 승인하지 않는 경우 새 UI 업데이트 생성을 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-380">Stops producing new UI updates if the client isn't acknowledging render batches.</span></span> <span data-ttu-id="52785-381">로 `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches`구성되었습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-381">Configured with `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches`.</span></span>
* <span data-ttu-id="52785-382">클라이언트로부터 응답을 받지 않고 1분 후에 .NET을 JavaScript 호출로 시간 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-382">Times out any .NET to JavaScript call after one minute without receiving a response from the client.</span></span> <span data-ttu-id="52785-383">로 `CircuitOptions.JSInteropDefaultCallTimeout`구성되었습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-383">Configured with `CircuitOptions.JSInteropDefaultCallTimeout`.</span></span>
* <span data-ttu-id="52785-384">JS interop 동안 브라우저에서 들어오는 모든 입력에 대한 기본 유효성 검사를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-384">Performs basic validation on all input coming from the browser during JS interop:</span></span>
  * <span data-ttu-id="52785-385">.NET 참조는 유효하며 .NET 메서드에서 예상하는 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="52785-385">.NET references are valid and of the type expected by the .NET method.</span></span>
  * <span data-ttu-id="52785-386">데이터가 잘못된 것이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="52785-386">The data isn't malformed.</span></span>
  * <span data-ttu-id="52785-387">메서드에 대 한 올바른 인수 수는 페이로드에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-387">The correct number of arguments for the method are present in the payload.</span></span>
  * <span data-ttu-id="52785-388">인수를 호출하기 전에 인수 또는 결과를 올바르게 역직렬화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-388">The arguments or result can be deserialized correctly before invoking the method.</span></span>
* <span data-ttu-id="52785-389">디스패치 이벤트에서 브라우저에서 들어오는 모든 입력에서 기본 유효성 검사를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-389">Performs basic validation in all input coming from the browser from dispatched events:</span></span>
  * <span data-ttu-id="52785-390">이벤트에 유효한 형식이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-390">The event has a valid type.</span></span>
  * <span data-ttu-id="52785-391">이벤트에 대한 데이터는 역직렬화될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-391">The data for the event can be deserialized.</span></span>
  * <span data-ttu-id="52785-392">이벤트와 연결된 이벤트 처리기가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-392">There's an event handler associated with the event.</span></span>

<span data-ttu-id="52785-393">프레임워크가 구현하는 보호 장치 외에도 위협으로부터 보호하고 적절한 조치를 취하기 위해 개발자가 앱을 코딩해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-393">In addition to the safeguards that the framework implements, the app must be coded by the developer to safeguard against threats and take appropriate actions:</span></span>

* <span data-ttu-id="52785-394">이벤트를 처리할 때 항상 데이터의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-394">Always validate data when handling events.</span></span>
* <span data-ttu-id="52785-395">잘못된 데이터를 수신할 때 적절한 조치를 취하십시오.</span><span class="sxs-lookup"><span data-stu-id="52785-395">Take appropriate action upon receiving invalid data:</span></span>
  * <span data-ttu-id="52785-396">데이터를 무시하고 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-396">Ignore the data and return.</span></span> <span data-ttu-id="52785-397">이렇게 하면 앱에서 요청을 계속 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-397">This allows the app to continue processing requests.</span></span>
  * <span data-ttu-id="52785-398">앱에서 입력이 불법이며 합법적인 클라이언트에서 생성할 수 없다고 판단되면 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-398">If the app determines that the input is illegitimate and couldn't be produced by legitimate client, throw an exception.</span></span> <span data-ttu-id="52785-399">예외를 throw하는 것은 회로를 찢어 세션을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-399">Throwing an exception tears down the circuit and ends the session.</span></span>
* <span data-ttu-id="52785-400">로그에 포함된 렌더링 일괄 처리 완료에서 제공하는 오류 메시지를 신뢰하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="52785-400">Don't trust the error message provided by render batch completions included in the logs.</span></span> <span data-ttu-id="52785-401">이 오류는 클라이언트에서 제공되며 클라이언트가 손상될 수 있기 때문에 일반적으로 신뢰할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-401">The error is provided by the client and can't generally be trusted, as the client might be compromised.</span></span>
* <span data-ttu-id="52785-402">JavaScript와 .NET 메서드 사이의 어느 방향으로든 JS interop 호출에 대한 입력을 신뢰하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="52785-402">Don't trust the input on JS interop calls in either direction between JavaScript and .NET methods.</span></span>
* <span data-ttu-id="52785-403">앱은 인수 또는 결과가 올바르게 역직렬화된 경우에도 인수 및 결과의 내용이 유효한지 유효성을 검사할 책임이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-403">The app is responsible for validating that the content of arguments and results are valid, even if the arguments or results are correctly deserialized.</span></span>

<span data-ttu-id="52785-404">XSS 취약점이 존재하려면 앱이 렌더링된 페이지에 사용자 입력을 통합해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-404">For a XSS vulnerability to exist, the app must incorporate user input in the rendered page.</span></span> Blazor<span data-ttu-id="52785-405">서버 구성 요소는 *.razor* 파일의 태그가 절차적 C# 논리로 변환되는 컴파일 타임 단계를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-405"> Server components execute a compile-time step where the markup in a *.razor* file is transformed into procedural C# logic.</span></span> <span data-ttu-id="52785-406">런타임시 C# 논리는 요소, 텍스트 및 자식 구성 요소를 설명하는 *렌더 트리를* 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-406">At runtime, the C# logic builds a *render tree* describing the elements, text, and child components.</span></span> <span data-ttu-id="52785-407">이것은 자바 스크립트 명령의 시퀀스를 통해 브라우저의 DOM에 적용됩니다 (또는 사전 렌더링의 경우 HTML로 직렬화됨).</span><span class="sxs-lookup"><span data-stu-id="52785-407">This is applied to the browser's DOM via a sequence of JavaScript instructions (or is serialized to HTML in the case of prerendering):</span></span>

* <span data-ttu-id="52785-408">일반 Razor 구문을 통해 렌더링된 사용자 `@someStringValue`입력(예: 텍스트만 쓸 수 있는 명령을 통해 Razor 구문이 DOM에 추가되기 때문에 XSS 취약점이 노출되지 않습니다).</span><span class="sxs-lookup"><span data-stu-id="52785-408">User input rendered via normal Razor syntax (for example, `@someStringValue`) doesn't expose a XSS vulnerability because the Razor syntax is added to the DOM via commands that can only write text.</span></span> <span data-ttu-id="52785-409">값에 HTML 태그가 포함되어 있더라도 값은 정적 텍스트로 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="52785-409">Even if the value includes HTML markup, the value is displayed as static text.</span></span> <span data-ttu-id="52785-410">사전 렌더링할 때 출력은 HTML 인코딩되어 콘텐츠를 정적 텍스트로 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-410">When prerendering, the output is HTML-encoded, which also displays the content as static text.</span></span>
* <span data-ttu-id="52785-411">스크립트 태그는 허용되지 않으며 앱의 구성 요소 렌더 트리에 포함되어서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="52785-411">Script tags aren't allowed and shouldn't be included in the app's component render tree.</span></span> <span data-ttu-id="52785-412">스크립트 태그가 구성 요소의 태그에 포함된 경우 컴파일 타임 오류가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="52785-412">If a script tag is included in a component's markup, a compile-time error is generated.</span></span>
* <span data-ttu-id="52785-413">구성 요소 작성자는 Razor를 사용하지 않고 C#의 구성 요소를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-413">Component authors can author components in C# without using Razor.</span></span> <span data-ttu-id="52785-414">구성 요소 작성자는 출력을 내보내는 경우 올바른 API를 사용할 책임이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-414">The component author is responsible for using the correct APIs when emitting output.</span></span> <span data-ttu-id="52785-415">예를 들어 `builder.AddContent(0, someUserSuppliedString)` XSS 취약점을 만들 수 있기 때문에 을 사용하지 *않습니다.* `builder.AddMarkupContent(0, someUserSuppliedString)`</span><span class="sxs-lookup"><span data-stu-id="52785-415">For example, use `builder.AddContent(0, someUserSuppliedString)` and *not* `builder.AddMarkupContent(0, someUserSuppliedString)`, as the latter could create a XSS vulnerability.</span></span>

<span data-ttu-id="52785-416">XSS 공격으로부터 보호하는 일환으로 [CSP(콘텐츠 보안 정책)와](https://developer.mozilla.org/docs/Web/HTTP/CSP)같은 XSS 완화를 구현하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-416">As part of protecting against XSS attacks, consider implementing XSS mitigations, such as [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP).</span></span>

<span data-ttu-id="52785-417">자세한 내용은 <xref:security/cross-site-scripting>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52785-417">For more information, see <xref:security/cross-site-scripting>.</span></span>

### <a name="cross-origin-protection"></a><span data-ttu-id="52785-418">원산지 간 보호</span><span class="sxs-lookup"><span data-stu-id="52785-418">Cross-origin protection</span></span>

<span data-ttu-id="52785-419">원본 간 공격에는 다른 원본의 클라이언트가 서버에 대해 작업을 수행하는 작업이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="52785-419">Cross-origin attacks involve a client from a different origin performing an action against the server.</span></span> <span data-ttu-id="52785-420">악의적인 작업은 일반적으로 GET 요청 또는 양식 POST(교차 사이트 요청 위조, CSRF)이지만 악의적인 WebSocket을 여는 것도 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-420">The malicious action is typically a GET request or a form POST (Cross-Site Request Forgery, CSRF), but opening a malicious WebSocket is also possible.</span></span> Blazor<span data-ttu-id="52785-421">서버 [앱은 허브 프로토콜을 SignalR 사용하는 다른 앱이 제공하는 것과 동일한 보증을 제공합니다.](xref:signalr/security)</span><span class="sxs-lookup"><span data-stu-id="52785-421"> Server apps offer [the same guarantees that any other SignalR app using the hub protocol offer](xref:signalr/security):</span></span>

* Blazor<span data-ttu-id="52785-422">서버 앱을 방지하기 위한 추가 조치를 취하지 않는 한 서버 앱은 원본 간 액세스가 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-422"> Server apps can be accessed cross-origin unless additional measures are taken to prevent it.</span></span> <span data-ttu-id="52785-423">원본 간 액세스를 사용하지 않도록 설정하려면 CORS 미들웨어를 파이프라인에 추가하고 `DisableCorsAttribute` Blazor 끝점 메타데이터에 추가하여 끝점에서 CORS를 사용하지 않도록 설정하거나 원본 간 리소스 [공유를 구성하여 SignalR ](xref:signalr/security#cross-origin-resource-sharing)허용된 원본 집합을 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-423">To disable cross-origin access, either disable CORS in the endpoint by adding the CORS middleware to the pipeline and adding the `DisableCorsAttribute` to the Blazor endpoint metadata or limit the set of allowed origins by [configuring SignalR for cross-origin resource sharing](xref:signalr/security#cross-origin-resource-sharing).</span></span>
* <span data-ttu-id="52785-424">CORS를 사용하도록 설정한 경우 CORS 구성에 따라 앱을 보호하기 위한 추가 단계가 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-424">If CORS is enabled, extra steps might be required to protect the app depending on the CORS configuration.</span></span> <span data-ttu-id="52785-425">CORS가 전역적으로 활성화된 경우 를 호출한 Blazor `DisableCorsAttribute` `hub.MapBlazorHub()`후 끝점 메타데이터에 메타데이터를 추가하여 서버 허브에 대해 CORS를 사용하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-425">If CORS is globally enabled, CORS can be disabled for the Blazor Server hub by adding the `DisableCorsAttribute` metadata to the endpoint metadata after calling `hub.MapBlazorHub()`.</span></span>

<span data-ttu-id="52785-426">자세한 내용은 <xref:security/anti-request-forgery>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52785-426">For more information, see <xref:security/anti-request-forgery>.</span></span>

### <a name="click-jacking"></a><span data-ttu-id="52785-427">클릭 재킹</span><span class="sxs-lookup"><span data-stu-id="52785-427">Click-jacking</span></span>

<span data-ttu-id="52785-428">클릭 재킹은 사용자가 공격을 `<iframe>` 받고 있는 사이트에서 작업을 수행하도록 속이도록 사용자를 속이기 위해 사이트를 다른 원본의 사이트 내부로 렌더링하는 것을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-428">Click-jacking involves rendering a site as an `<iframe>` inside a site from a different origin in order to trick the user into performing actions on the site under attack.</span></span>

<span data-ttu-id="52785-429">의 내부 렌더링에서 응용 프로그램을 보호 하려면 콘텐츠 보안 정책 `X-Frame-Options` [(CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) 및 헤더를 사용 합니다. `<iframe>`</span><span class="sxs-lookup"><span data-stu-id="52785-429">To protect an app from rendering inside of an `<iframe>`, use [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) and the `X-Frame-Options` header.</span></span> <span data-ttu-id="52785-430">자세한 내용은 [MDN 웹 문서: X-프레임 옵션](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options)을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="52785-430">For more information, see [MDN web docs: X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options).</span></span>

### <a name="open-redirects"></a><span data-ttu-id="52785-431">열기 리디렉션</span><span class="sxs-lookup"><span data-stu-id="52785-431">Open redirects</span></span>

<span data-ttu-id="52785-432">서버 Blazor 앱 세션이 시작되면 서버는 세션 시작의 일부로 전송된 URL의 기본 유효성 검사를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-432">When a Blazor Server app session starts, the server performs basic validation of the URLs sent as part of starting the session.</span></span> <span data-ttu-id="52785-433">프레임워크는 회로를 설정하기 전에 기본 URL이 현재 URL의 부모임을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-433">The framework checks that the base URL is a parent of the current URL before establishing the circuit.</span></span> <span data-ttu-id="52785-434">프레임워크에서 추가 검사가 수행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-434">No additional checks are performed by the framework.</span></span>

<span data-ttu-id="52785-435">사용자가 클라이언트에서 링크를 선택하면 링크의 URL이 서버로 전송되어 어떤 작업을 취할지 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-435">When a user selects a link on the client, the URL for the link is sent to the server, which determines what action to take.</span></span> <span data-ttu-id="52785-436">예를 들어 앱은 클라이언트 쪽 탐색을 수행하거나 브라우저에 새 위치로 이동하도록 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-436">For example, the app may perform a client-side navigation or indicate to the browser to go to the new location.</span></span>

<span data-ttu-id="52785-437">구성 요소는 `NavigationManager`을 사용하여 탐색 요청을 프로그래밍할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-437">Components can also trigger navigation requests programatically through the use of `NavigationManager`.</span></span> <span data-ttu-id="52785-438">이러한 시나리오에서 앱은 클라이언트 쪽 탐색을 수행하거나 브라우저에 새 위치로 이동하도록 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-438">In such scenarios, the app might perform a client-side navigation or indicate to the browser to go to the new location.</span></span>

<span data-ttu-id="52785-439">구성 요소는 다음을 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-439">Components must:</span></span>

* <span data-ttu-id="52785-440">탐색 호출 인수의 일부로 사용자 입력을 사용하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="52785-440">Avoid using user input as part of the navigation call arguments.</span></span>
* <span data-ttu-id="52785-441">인수의 유효성을 검사하여 앱에서 대상이 허용되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-441">Validate arguments to ensure that the target is allowed by the app.</span></span>

<span data-ttu-id="52785-442">그렇지 않으면 악의적인 사용자가 브라우저를 공격자 제어 사이트로 이동하도록 강제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-442">Otherwise, a malicious user can force the browser to go to an attacker-controlled site.</span></span> <span data-ttu-id="52785-443">이 시나리오에서 공격자는 `NavigationManager.Navigate` 메서드 호출의 일부로 일부 사용자 입력을 사용 하 여 응용 프로그램을 속여 합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-443">In this scenario, the attacker tricks the app into using some user input as part of the invocation of the `NavigationManager.Navigate` method.</span></span>

<span data-ttu-id="52785-444">이 조언은 앱의 일부로 링크를 렌더링할 때도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="52785-444">This advice also applies when rendering links as part of the app:</span></span>

* <span data-ttu-id="52785-445">가능하면 상대 링크를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-445">If possible, use relative links.</span></span>
* <span data-ttu-id="52785-446">절대 링크 대상을 페이지에 포함하기 전에 유효한지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-446">Validate that absolute link destinations are valid before including them in a page.</span></span>

<span data-ttu-id="52785-447">자세한 내용은 <xref:security/preventing-open-redirects>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="52785-447">For more information, see <xref:security/preventing-open-redirects>.</span></span>

## <a name="authentication-and-authorization"></a><span data-ttu-id="52785-448">인증 및 권한 부여</span><span class="sxs-lookup"><span data-stu-id="52785-448">Authentication and authorization</span></span>

<span data-ttu-id="52785-449">인증 및 권한 부여에 <xref:security/blazor/index>대한 지침은 을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="52785-449">For guidance on authentication and authorization, see <xref:security/blazor/index>.</span></span>

## <a name="security-checklist"></a><span data-ttu-id="52785-450">보안 검사 목록</span><span class="sxs-lookup"><span data-stu-id="52785-450">Security checklist</span></span>

<span data-ttu-id="52785-451">다음 보안 고려 사항 목록은 포괄적이지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-451">The following list of security considerations isn't comprehensive:</span></span>

* <span data-ttu-id="52785-452">이벤트에서 인수를 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-452">Validate arguments from events.</span></span>
* <span data-ttu-id="52785-453">JS interop 호출의 입력 및 결과를 검증합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-453">Validate inputs and results from JS interop calls.</span></span>
* <span data-ttu-id="52785-454">.NET에서 JS interop 호출에 대한 사용자 입력을 사용(또는 미리 확인)하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="52785-454">Avoid using (or validate beforehand) user input for .NET to JS interop calls.</span></span>
* <span data-ttu-id="52785-455">클라이언트가 언바운드 메모리 양을 할당하지 못하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-455">Prevent the client from allocating an unbound amount of memory.</span></span>
  * <span data-ttu-id="52785-456">구성 요소 내의 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="52785-456">Data within the component.</span></span>
  * <span data-ttu-id="52785-457">`DotNetObject`참조가 클라이언트에 반환되었습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-457">`DotNetObject` references returned to the client.</span></span>
* <span data-ttu-id="52785-458">여러 디스패치를 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-458">Guard against multiple dispatches.</span></span>
* <span data-ttu-id="52785-459">구성 요소가 삭제될 때 장기 실행 작업을 취소합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-459">Cancel long-running operations when the component is disposed.</span></span>
* <span data-ttu-id="52785-460">많은 양의 데이터를 생성하는 이벤트를 피합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-460">Avoid events that produce large amounts of data.</span></span>
* <span data-ttu-id="52785-461">불가피한 경우 먼저 허용된 원본 집합에 대해 URL에 대한 사용자 입력을 호출의 `NavigationManager.Navigate` 일부로 사용하지 말고 사용자 입력의 유효성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-461">Avoid using user input as part of calls to `NavigationManager.Navigate` and validate user input for URLs against a set of allowed origins first if unavoidable.</span></span>
* <span data-ttu-id="52785-462">UI의 상태에 따라 권한 부여를 결정하지 말고 구성 요소 상태에서만 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-462">Don't make authorization decisions based on the state of the UI but only from component state.</span></span>
* <span data-ttu-id="52785-463">XSS 공격으로부터 보호하기 위해 [CSP(콘텐츠 보안 정책)를](https://developer.mozilla.org/docs/Web/HTTP/CSP) 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="52785-463">Consider using [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) to protect against XSS attacks.</span></span>
* <span data-ttu-id="52785-464">CSP 및 [X-프레임 옵션을](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) 사용하여 클릭 재킹을 방지하십시오.</span><span class="sxs-lookup"><span data-stu-id="52785-464">Consider using CSP and [X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) to protect against click-jacking.</span></span>
* <span data-ttu-id="52785-465">CORS를 사용하도록 설정하거나 앱에 대해 Blazor CORS를 명시적으로 사용하지 않도록 설정할 때 CORS 설정이 적절한지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-465">Ensure CORS settings are appropriate when enabling CORS or explicitly disable CORS for Blazor apps.</span></span>
* <span data-ttu-id="52785-466">Blazor 앱의 서버 측 제한이 허용할 수 없는 수준의 위험 없이 허용 가능한 사용자 환경을 제공하는지 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="52785-466">Test to ensure that the server-side limits for the Blazor app provide an acceptable user experience without unacceptable levels of risk.</span></span>
