---
title: ASP.NET Core Blazor 인증 및 권한 부여
author: guardrex
description: Blazor 인증 및 권한 부여 시나리오에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/29/2019
uid: security/blazor/index
ms.openlocfilehash: 8714acbeb6e8a00992a601030811b24f53426b82
ms.sourcegitcommit: 8b36f75b8931ae3f656e2a8e63572080adc78513
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70310518"
---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a><span data-ttu-id="6f25e-103">ASP.NET Core Blazor 인증 및 권한 부여</span><span class="sxs-lookup"><span data-stu-id="6f25e-103">ASP.NET Core Blazor authentication and authorization</span></span>

<span data-ttu-id="6f25e-104">작성자: [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="6f25e-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="6f25e-105">ASP.NET Core는 Blazor 앱의 보안 구성 및 관리를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-105">ASP.NET Core supports the configuration and management of security in Blazor apps.</span></span>

<span data-ttu-id="6f25e-106">Blazor 서버 쪽 앱과 클라이언트 쪽 앱의 보안 시나리오가 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-106">Security scenarios differ between Blazor server-side and client-side apps.</span></span> <span data-ttu-id="6f25e-107">Blazor 서버 쪽 앱은 서버에서 실행되기 때문에 권한 부여 확인을 통해 다음을 결정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-107">Because Blazor server-side apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="6f25e-108">사용자에게 표시되는 UI 옵션(예: 사용자가 사용할 수 있는 메뉴 항목)</span><span class="sxs-lookup"><span data-stu-id="6f25e-108">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="6f25e-109">앱 영역과 구성 요소의 액세스 규칙</span><span class="sxs-lookup"><span data-stu-id="6f25e-109">Access rules for areas of the app and components.</span></span>

<span data-ttu-id="6f25e-110">Blazor 클라이언트 쪽 앱은 클라이언트에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-110">Blazor client-side apps run on the client.</span></span> <span data-ttu-id="6f25e-111">권한 부여는 표시할 UI 옵션을 결정하는 ‘용도로만’ 사용됩니다. </span><span class="sxs-lookup"><span data-stu-id="6f25e-111">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="6f25e-112">사용자가 클라이언트 쪽 확인을 수정하거나 무시할 수 있기 때문에, Blazor 클라이언트 쪽 앱은 권한 부여 액세스 규칙을 적용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-112">Since client-side checks can be modified or bypassed by a user, a Blazor client-side app can't enforce authorization access rules.</span></span>

## <a name="authentication"></a><span data-ttu-id="6f25e-113">인증</span><span class="sxs-lookup"><span data-stu-id="6f25e-113">Authentication</span></span>

<span data-ttu-id="6f25e-114">Blazor는 기존 ASP.NET Core 인증 메커니즘을 사용하여 사용자 ID를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-114">Blazor uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="6f25e-115">정확한 메커니즘은 Blazor 앱을 호스트하는 방법, 즉 서버 쪽인지 또는 클라이언트 쪽인지에 따라 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-115">The exact mechanism depends on how the Blazor app is hosted, server-side or client-side.</span></span>

### <a name="blazor-server-side-authentication"></a><span data-ttu-id="6f25e-116">Blazor 서버 쪽 인증</span><span class="sxs-lookup"><span data-stu-id="6f25e-116">Blazor server-side authentication</span></span>

<span data-ttu-id="6f25e-117">Blazor 서버 쪽 앱은 SignalR을 사용하여 생성된 실시간 연결을 통해 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-117">Blazor server-side apps operate over a real-time connection that's created using SignalR.</span></span> <span data-ttu-id="6f25e-118">[SignalR 기반 앱의 인증](xref:signalr/authn-and-authz)은 연결 시 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-118">[Authentication in SignalR-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="6f25e-119">인증은 쿠키 또는 다른 전달자 토큰을 기반으로 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-119">Authentication can be based on a cookie or some other bearer token.</span></span>

<span data-ttu-id="6f25e-120">프로젝트를 만들 때 Blazor 서버 쪽 프로젝트 템플릿에서 자동으로 인증을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-120">The Blazor server-side project template can set up authentication for you when the project is created.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6f25e-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6f25e-121">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6f25e-122"><xref:blazor/get-started> 문서의 Visual Studio 지침에 따라 인증 메커니즘이 있는 새 Blazor 서버 쪽 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-122">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor server-side project with an authentication mechanism.</span></span>

<span data-ttu-id="6f25e-123">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **Blazor 서버 앱** 템플릿을 선택한 후 **인증**에서 **변경**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-123">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="6f25e-124">다른 ASP.NET Core 프로젝트에 사용할 수 있는 것과 동일한 인증 메커니즘 세트를 제공하는 대화 상자가 열립니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-124">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="6f25e-125">**인증 없음**</span><span class="sxs-lookup"><span data-stu-id="6f25e-125">**No Authentication**</span></span>
* <span data-ttu-id="6f25e-126">**개별 사용자 계정** &ndash; 사용자 계정은 다음과 같이 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-126">**Individual User Accounts** &ndash; User accounts can be stored:</span></span>
  * <span data-ttu-id="6f25e-127">ASP.NET Core의 [ID](xref:security/authentication/identity) 시스템을 사용하여 앱에 저장</span><span class="sxs-lookup"><span data-stu-id="6f25e-127">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="6f25e-128">[Azure AD B2C](xref:security/authentication/azure-ad-b2c) 사용</span><span class="sxs-lookup"><span data-stu-id="6f25e-128">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="6f25e-129">**회사 또는 학교 계정**</span><span class="sxs-lookup"><span data-stu-id="6f25e-129">**Work or School Accounts**</span></span>
* <span data-ttu-id="6f25e-130">**Windows 인증**</span><span class="sxs-lookup"><span data-stu-id="6f25e-130">**Windows Authentication**</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="6f25e-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6f25e-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6f25e-132"><xref:blazor/get-started> 문서의 Visual Studio Code 지침에 따라 인증 메커니즘이 있는 새 Blazor 서버 쪽 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-132">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor server-side project with an authentication mechanism:</span></span>

```console
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="6f25e-133">허용되는 인증 값(`{AUTHENTICATION}`)은 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-133">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="6f25e-134">인증 메커니즘</span><span class="sxs-lookup"><span data-stu-id="6f25e-134">Authentication mechanism</span></span>                                                                 | <span data-ttu-id="6f25e-135">`{AUTHENTICATION}` 값</span><span class="sxs-lookup"><span data-stu-id="6f25e-135">`{AUTHENTICATION}` value</span></span> |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| <span data-ttu-id="6f25e-136">인증 없음</span><span class="sxs-lookup"><span data-stu-id="6f25e-136">No Authentication</span></span>                                                                        | `None`                   |
| <span data-ttu-id="6f25e-137">개인</span><span class="sxs-lookup"><span data-stu-id="6f25e-137">Individual</span></span><br><span data-ttu-id="6f25e-138">ASP.NET Core ID를 사용하여 앱에 저장된 사용자</span><span class="sxs-lookup"><span data-stu-id="6f25e-138">Users stored in the app with ASP.NET Core Identity.</span></span>                        | `Individual`             |
| <span data-ttu-id="6f25e-139">개인</span><span class="sxs-lookup"><span data-stu-id="6f25e-139">Individual</span></span><br><span data-ttu-id="6f25e-140">[Azure AD B2C](xref:security/authentication/azure-ad-b2c)에 저장된 사용자</span><span class="sxs-lookup"><span data-stu-id="6f25e-140">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span> | `IndividualB2C`          |
| <span data-ttu-id="6f25e-141">회사 또는 학교 계정</span><span class="sxs-lookup"><span data-stu-id="6f25e-141">Work or School Accounts</span></span><br><span data-ttu-id="6f25e-142">단일 테넌트에 대한 조직 인증</span><span class="sxs-lookup"><span data-stu-id="6f25e-142">Organizational authentication for a single tenant.</span></span>            | `SingleOrg`              |
| <span data-ttu-id="6f25e-143">회사 또는 학교 계정</span><span class="sxs-lookup"><span data-stu-id="6f25e-143">Work or School Accounts</span></span><br><span data-ttu-id="6f25e-144">여러 테넌트에 대한 조직 인증</span><span class="sxs-lookup"><span data-stu-id="6f25e-144">Organizational authentication for multiple tenants.</span></span>           | `MultiOrg`               |
| <span data-ttu-id="6f25e-145">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="6f25e-145">Windows Authentication</span></span>                                                                   | `Windows`                |

<span data-ttu-id="6f25e-146">이 명령은 `{APP NAME}` 자리 표시자에 제공된 값으로 이름을 지정하여 폴더를 만들고, 폴더 이름을 앱 이름으로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-146">The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name.</span></span> <span data-ttu-id="6f25e-147">자세한 내용은 .NET Core 가이드의 [dotnet new](/dotnet/core/tools/dotnet-new) 명령을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6f25e-147">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

<!--

# [Visual Studio for Mac](#tab/visual-studio-mac)

1. Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.

1.

1.

-->

<!--
# [.NET Core CLI](#tab/netcore-cli/)

Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor server-side project with an authentication mechanism:

```console
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.

| Authentication mechanism                                                                 | `{AUTHENTICATION}` value |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| No Authentication                                                                        | `None`                   |
| Individual<br>Users stored in the app with ASP.NET Core Identity.                        | `Individual`             |
| Individual<br>Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c). | `IndividualB2C`          |
| Work or School Accounts<br>Organizational authentication for a single tenant.            | `SingleOrg`              |
| Work or School Accounts<br>Organizational authentication for multiple tenants.           | `MultiOrg`               |
| Windows Authentication                                                                   | `Windows`                |

The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name. For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.

-->

---

### <a name="blazor-client-side-authentication"></a><span data-ttu-id="6f25e-148">Blazor 클라이언트 쪽 인증</span><span class="sxs-lookup"><span data-stu-id="6f25e-148">Blazor client-side authentication</span></span>

<span data-ttu-id="6f25e-149">Blazor 클라이언트 쪽 앱에서는 사용자가 클라이언트 쪽 코드를 모두 수정할 수 있기 때문에 인증 확인을 무시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-149">In Blazor client-side apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="6f25e-150">JavaScript SPA 프레임워크 또는 모든 운영 체제의 네이티브 앱을 포함하여 모든 클라이언트 쪽 앱 기술에는 동일하게 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-150">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="6f25e-151">Blazor 클라이언트 쪽 앱에 대한 사용자 지정 `AuthenticationStateProvider` 서비스 구현은 다음 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-151">Implementation of a custom `AuthenticationStateProvider` service for Blazor client-side apps is covered in the following sections.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="6f25e-152">AuthenticationStateProvider 서비스</span><span class="sxs-lookup"><span data-stu-id="6f25e-152">AuthenticationStateProvider service</span></span>

<span data-ttu-id="6f25e-153">Blazor 서버 쪽 앱에는 ASP.NET Core의 `HttpContext.User`에서 인증 상태 데이터를 가져오는 `AuthenticationStateProvider` 서비스가 기본 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-153">Blazor server-side apps include a built-in `AuthenticationStateProvider` service that obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="6f25e-154">이런 방법으로 인증 상태가 기존의 ASP.NET Core 서버 쪽 인증 메커니즘과 통합됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-154">This is how authentication state integrates with existing ASP.NET Core server-side authentication mechanisms.</span></span>

<span data-ttu-id="6f25e-155">`AuthenticationStateProvider`는 `AuthorizeView` 구성 요소와 `CascadingAuthenticationState` 구성 요소가 인증 상태를 가져오는 데 사용하는 기본 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-155">`AuthenticationStateProvider` is the underlying service used by the `AuthorizeView` component and `CascadingAuthenticationState` component to get the authentication state.</span></span>

<span data-ttu-id="6f25e-156">일반적으로 `AuthenticationStateProvider`를 직접 사용하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-156">You don't typically use `AuthenticationStateProvider` directly.</span></span> <span data-ttu-id="6f25e-157">[AuthorizeView 구성 요소](#authorizeview-component) 또는 이 문서의 뒷부분에서 설명하는 [Task<AuthenticationState> ](#expose-the-authentication-state-as-a-cascading-parameter) 접근 방식을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-157">Use the [AuthorizeView component](#authorizeview-component) or [Task<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="6f25e-158">`AuthenticationStateProvider`를 직접 사용하는 경우의 주요 단점은 기본 인증 상태 데이터가 변경될 때 구성 요소가 자동으로 알림을 받지 못하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-158">The main drawback to using `AuthenticationStateProvider` directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="6f25e-159">`AuthenticationStateProvider` 서비스는 다음 예제와 같이 현재 사용자의 <xref:System.Security.Claims.ClaimsPrincipal> 데이터를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-159">The `AuthenticationStateProvider` service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

```cshtml
@page "/"
@inject AuthenticationStateProvider AuthenticationStateProvider

<button @onclick="@LogUsername">Write user info to console</button>

@code {
    private async Task LogUsername()
    {
        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            Console.WriteLine($"{user.Identity.Name} is authenticated.");
        }
        else
        {
            Console.WriteLine("The user is NOT authenticated.");
        }
    }
}
```

<span data-ttu-id="6f25e-160">`user.Identity.IsAuthenticated`가 `true`이면, 사용자가 <xref:System.Security.Claims.ClaimsPrincipal>이므로 클레임을 열거하고 역할의 멤버 자격을 평가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-160">If `user.Identity.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="6f25e-161">DI(종속성 주입) 및 서비스에 대한 자세한 내용은 <xref:blazor/dependency-injection> 및 <xref:fundamentals/dependency-injection>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6f25e-161">For more information on dependency injection (DI) and services, see <xref:blazor/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="6f25e-162">사용자 지정 AuthenticationStateProvider 구현</span><span class="sxs-lookup"><span data-stu-id="6f25e-162">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="6f25e-163">Blazor 클라이언트 쪽 앱을 빌드하는 경우 또는 앱의 사양에 사용자 지정 공급자가 필요한 경우, 공급자를 구현하고 `GetAuthenticationStateAsync`를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-163">If you're building a Blazor client-side app or if your app's specification absolutely requires a custom provider, implement a provider and override `GetAuthenticationStateAsync`:</span></span>

```csharp
class CustomAuthStateProvider : AuthenticationStateProvider
{
    public override Task<AuthenticationState> GetAuthenticationStateAsync()
    {
        var identity = new ClaimsIdentity(new[]
        {
            new Claim(ClaimTypes.Name, "mrfibuli"),
        }, "Fake authentication type");

        var user = new ClaimsPrincipal(identity);

        return Task.FromResult(new AuthenticationState(user));
    }
}
```

<span data-ttu-id="6f25e-164">`CustomAuthStateProvider` 서비스는 `Startup.ConfigureServices`에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-164">The `CustomAuthStateProvider` service is registered in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
}
```

<span data-ttu-id="6f25e-165">`CustomAuthStateProvider`를 사용하면 모든 사용자가 사용자 이름 `mrfibuli`로 인증됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-165">Using the `CustomAuthStateProvider`, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="6f25e-166">인증 상태를 연계 매개 변수로 공개</span><span class="sxs-lookup"><span data-stu-id="6f25e-166">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="6f25e-167">사용자가 실행한 작업을 수행하는 경우와 같이 절차적 논리에 인증 상태 데이터가 필요한 경우 `Task<AuthenticationState>` 형식의 연계 매개 변수를 정의하여 인증 상태 데이터를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-167">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<AuthenticationState>`:</span></span>

```cshtml
@page "/"

<button @onclick="@LogUsername">Log username</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task LogUsername()
    {
        var authState = await authenticationStateTask;
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            Console.WriteLine($"{user.Identity.Name} is authenticated.");
        }
        else
        {
            Console.WriteLine("The user is NOT authenticated.");
        }
    }
}
```

<span data-ttu-id="6f25e-168">`user.Identity.IsAuthenticated`가 `true`이면, 클레임을 열거하고 역할의 멤버 자격을 평가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-168">If `user.Identity.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="6f25e-169">`AuthorizeRouteView` 및 `CascadingAuthenticationState` 구성 요소를 사용하여 `Task<AuthenticationState>` 연계 매개 변수를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-169">Set up the `Task<AuthenticationState>` cascading parameter using the `AuthorizeRouteView` and `CascadingAuthenticationState` components:</span></span>

```cshtml
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <CascadingAuthenticationState>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </CascadingAuthenticationState>
    </NotFound>
</Router>
```

## <a name="authorization"></a><span data-ttu-id="6f25e-170">권한 부여</span><span class="sxs-lookup"><span data-stu-id="6f25e-170">Authorization</span></span>

<span data-ttu-id="6f25e-171">사용자가 인증되면, 사용자가 수행할 수 있는 작업을 제어하기 위해 ‘권한 부여’ 규칙이 적용됩니다. </span><span class="sxs-lookup"><span data-stu-id="6f25e-171">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="6f25e-172">일반적으로 다음 여부에 따라 액세스가 허용 또는 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-172">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="6f25e-173">사용자가 인증(로그인)되었는지 여부</span><span class="sxs-lookup"><span data-stu-id="6f25e-173">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="6f25e-174">사용자가 ‘역할’에 속하는지 여부 </span><span class="sxs-lookup"><span data-stu-id="6f25e-174">A user is in a *role*.</span></span>
* <span data-ttu-id="6f25e-175">사용자에게 ‘클레임’이 있는지 여부 </span><span class="sxs-lookup"><span data-stu-id="6f25e-175">A user has a *claim*.</span></span>
* <span data-ttu-id="6f25e-176">‘정책’이 충족되었는지 여부 </span><span class="sxs-lookup"><span data-stu-id="6f25e-176">A *policy* is satisfied.</span></span>

<span data-ttu-id="6f25e-177">이러한 각 개념은 ASP.NET Core MVC 또는 Razor Pages 앱에서와 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-177">Each of these concepts is the same as in an ASP.NET Core MVC or Razor Pages app.</span></span> <span data-ttu-id="6f25e-178">ASP.NET Core 보안에 대한 자세한 내용은 [ASP.NET Core 보안 및 ID](xref:security/index)의 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6f25e-178">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and Identity](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="6f25e-179">AuthorizeView 구성 요소</span><span class="sxs-lookup"><span data-stu-id="6f25e-179">AuthorizeView component</span></span>

<span data-ttu-id="6f25e-180">`AuthorizeView` 구성 요소는 사용자에게 볼 수 있는 권한이 있는지 여부에 따라 선택적으로 UI를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-180">The `AuthorizeView` component selectively displays UI depending on whether the user is authorized to see it.</span></span> <span data-ttu-id="6f25e-181">이 접근 방식은 사용자에게 데이터를 ‘표시’하기만 하면 되고 절차적 논리에 사용자 ID를 사용할 필요가 없는 경우에 유용합니다. </span><span class="sxs-lookup"><span data-stu-id="6f25e-181">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="6f25e-182">이 구성 요소는 로그인한 사용자 정보에 액세스하는 데 사용할 수 있는 `AuthenticationState` 형식의 `context` 변수를 공개합니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-182">The component exposes a `context` variable of type `AuthenticationState`, which you can use to access information about the signed-in user:</span></span>

```cshtml
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="6f25e-183">사용자가 인증되지 않은 경우에 표시할 다른 콘텐츠를 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-183">You can also supply different content for display if the user isn't authenticated:</span></span>

```cshtml
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <NotAuthorized>
        <h1>Authentication Failure!</h1>
        <p>You're not signed in.</p>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="6f25e-184">`<Authorized>` 및 `<NotAuthorized>`의 콘텐츠에는 다른 대화형 구성 요소와 같은 임의 항목이 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-184">The content of `<Authorized>` and `<NotAuthorized>` can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="6f25e-185">UI 옵션이나 액세스를 제어하는 역할 또는 정책과 같은 권한 부여 조건은 [권한 부여](#authorization) 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-185">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="6f25e-186">권한 부여 조건을 지정하지 않으면 `AuthorizeView`는 기본 정책을 사용하고 다음과 같이 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-186">If authorization conditions aren't specified, `AuthorizeView` uses a default policy and treats:</span></span>

* <span data-ttu-id="6f25e-187">인증(로그인)된 사용자를 권한 있는 사용자로 처리</span><span class="sxs-lookup"><span data-stu-id="6f25e-187">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="6f25e-188">인증되지 않은(로그아웃된) 사용자를 권한 없는 사용자로 처리</span><span class="sxs-lookup"><span data-stu-id="6f25e-188">Unauthenticated (signed-out) users as unauthorized.</span></span>

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="6f25e-189">역할 기반 및 정책 기반 권한 부여</span><span class="sxs-lookup"><span data-stu-id="6f25e-189">Role-based and policy-based authorization</span></span>

<span data-ttu-id="6f25e-190">`AuthorizeView` 구성 요소는 ‘역할 기반’ 또는 ‘정책 기반’ 권한 부여를 지원합니다.  </span><span class="sxs-lookup"><span data-stu-id="6f25e-190">The `AuthorizeView` component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="6f25e-191">역할 기반 권한 부여의 경우 `Roles` 매개 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-191">For role-based authorization, use the `Roles` parameter:</span></span>

```cshtml
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="6f25e-192">자세한 내용은 <xref:security/authorization/roles>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6f25e-192">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="6f25e-193">정책 기반 권한 부여의 경우 `Policy` 매개 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-193">For policy-based authorization, use the `Policy` parameter:</span></span>

```cshtml
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="6f25e-194">클레임 기반 권한 부여는 정책 기반 권한 부여의 특별한 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-194">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="6f25e-195">예를 들어 사용자에게 특정 클레임이 있어야 하는 정책을 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-195">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="6f25e-196">자세한 내용은 <xref:security/authorization/policies>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6f25e-196">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="6f25e-197">이러한 API는 Blazor 서버 쪽 앱이나 Blazor 클라이언트 쪽 앱에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-197">These APIs can be used in either Blazor server-side or Blazor client-side apps.</span></span>

<span data-ttu-id="6f25e-198">`Roles` 또는 `Policy`를 지정하지 않으면 `AuthorizeView`는 기본 정책을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-198">If neither `Roles` nor `Policy` is specified, `AuthorizeView` uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="6f25e-199">비동기 인증 중에 표시되는 콘텐츠</span><span class="sxs-lookup"><span data-stu-id="6f25e-199">Content displayed during asynchronous authentication</span></span>

<span data-ttu-id="6f25e-200">Blazor에서는 인증 상태를 ‘비동기적으로’ 확인할 수 있습니다. </span><span class="sxs-lookup"><span data-stu-id="6f25e-200">Blazor allows for authentication state to be determined *asynchronously*.</span></span> <span data-ttu-id="6f25e-201">이 접근 방식의 주요 시나리오는 Blazor 클라이언트 쪽 앱이 외부 엔드포인트에 인증 요청을 하는 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-201">The primary scenario for this approach is in Blazor client-side apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="6f25e-202">인증이 진행되는 동안 `AuthorizeView`는 기본적으로 아무 콘텐츠도 표시하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-202">While authentication is in progress, `AuthorizeView` displays no content by default.</span></span> <span data-ttu-id="6f25e-203">인증 중에 콘텐츠를 표시하려면 `<Authorizing>` 요소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-203">To display content while authentication occurs, use the `<Authorizing>` element:</span></span>

```cshtml
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <Authorizing>
        <h1>Authentication in progress</h1>
        <p>You can only see this content while authentication is in progress.</p>
    </Authorizing>
</AuthorizeView>
```

<span data-ttu-id="6f25e-204">이 접근 방식은 일반적으로 Blazor 서버 쪽 앱에 적용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-204">This approach isn't normally applicable to Blazor server-side apps.</span></span> <span data-ttu-id="6f25e-205">Blazor 서버 쪽 앱은 상태가 설정되는 즉시 인증 상태를 알 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-205">Blazor server-side apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="6f25e-206">Blazor 서버 쪽 앱의 `AuthorizeView` 구성 요소에 `Authorizing` 콘텐츠를 제공할 수는 있지만, 이 콘텐츠는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-206">`Authorizing` content can be provided in a Blazor server-side app's `AuthorizeView` component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="6f25e-207">[Authorize] 특성</span><span class="sxs-lookup"><span data-stu-id="6f25e-207">[Authorize] attribute</span></span>

<span data-ttu-id="6f25e-208">앱이 MVC 컨트롤러 또는 Razor 페이지에서 `[Authorize]`를 사용할 수 있는 것과 마찬가지로, Razor 구성 요소에서 `[Authorize]`를 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-208">Just like an app can use `[Authorize]` with an MVC controller or Razor page, `[Authorize]` can also be used with Razor Components:</span></span>

```cshtml
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> <span data-ttu-id="6f25e-209">Blazor 라우터를 통해 연결된 `@page` 구성 요소에서만 `[Authorize]`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-209">Only use `[Authorize]` on `@page` components reached via the Blazor Router.</span></span> <span data-ttu-id="6f25e-210">권한 부여는 라우팅의 일부로만 수행되고, 페이지에 렌더링된 자식 구성 요소에 대해서는 수행되지 ‘않습니다’. </span><span class="sxs-lookup"><span data-stu-id="6f25e-210">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="6f25e-211">페이지 내의 특정 파트 표시 권한을 부여하려면 `AuthorizeView`를 대신 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-211">To authorize the display of specific parts within a page, use `AuthorizeView` instead.</span></span>

<span data-ttu-id="6f25e-212">구성 요소를 컴파일하기 위해 해당 구성 요소 또는 *_Imports.razor* 파일에 `@using Microsoft.AspNetCore.Authorization`을 추가해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-212">You may need to add `@using Microsoft.AspNetCore.Authorization` either to the component or to the *_Imports.razor* file in order for the component to compile.</span></span>

<span data-ttu-id="6f25e-213">`[Authorize]` 특성은 역할 기반 또는 정책 기반 권한 부여도 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-213">The `[Authorize]` attribute also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="6f25e-214">역할 기반 권한 부여의 경우 `Roles` 매개 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-214">For role-based authorization, use the `Roles` parameter:</span></span>

```cshtml
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="6f25e-215">정책 기반 권한 부여의 경우 `Policy` 매개 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-215">For policy-based authorization, use the `Policy` parameter:</span></span>

```cshtml
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="6f25e-216">`Roles` 또는 `Policy`를 지정하지 않으면 `[Authorize]`는 기본적으로 다음과 같이 처리하는 기본 정책을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-216">If neither `Roles` nor `Policy` is specified, `[Authorize]` uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="6f25e-217">인증(로그인)된 사용자를 권한 있는 사용자로 처리</span><span class="sxs-lookup"><span data-stu-id="6f25e-217">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="6f25e-218">인증되지 않은(로그아웃된) 사용자를 권한 없는 사용자로 처리</span><span class="sxs-lookup"><span data-stu-id="6f25e-218">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="6f25e-219">Router 구성 요소를 사용하여 권한 없는 콘텐츠 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="6f25e-219">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="6f25e-220">`AuthorizeRouteView` 구성 요소와 함께 `Router` 구성 요소를 사용하면 다음과 같은 경우 앱이 사용자 지정 콘텐츠를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-220">The `Router` component, in conjunction with the `AuthorizeRouteView` component, allows the app to specify custom content if:</span></span>

* <span data-ttu-id="6f25e-221">콘텐츠를 찾을 수 없는 경우</span><span class="sxs-lookup"><span data-stu-id="6f25e-221">Content isn't found.</span></span>
* <span data-ttu-id="6f25e-222">사용자가 구성 요소에 적용된 `[Authorize]` 조건을 충족하지 못하는 경우.</span><span class="sxs-lookup"><span data-stu-id="6f25e-222">The user fails an `[Authorize]` condition applied to the component.</span></span> <span data-ttu-id="6f25e-223">`[Authorize]` 특성은 [[Authorize] 특성](#authorize-attribute) 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-223">The `[Authorize]` attribute is covered in the [[Authorize] attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="6f25e-224">비동기 인증이 진행 중인 경우</span><span class="sxs-lookup"><span data-stu-id="6f25e-224">Asynchronous authentication is in progress.</span></span>

<span data-ttu-id="6f25e-225">기본 Blazor 서버 쪽 프로젝트 템플릿에서 *App.razor* 파일은 사용자 지정 콘텐츠를 설정하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-225">In the default Blazor server-side project template, the *App.razor* file demonstrates how to set custom content:</span></span>

```cshtml
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)">
            <NotAuthorized>
                <h1>Sorry</h1>
                <p>You're not authorized to reach this page.</p>
                <p>You may need to log in as a different user.</p>
            </NotAuthorized>
            <Authorizing>
                <h1>Authentication in progress</h1>
                <p>Only visible while authentication is in progress.</p>
            </Authorizing>
        </AuthorizeRouteView>
    </Found>
    <NotFound>
        <CascadingAuthenticationState>
            <LayoutView Layout="@typeof(MainLayout)">
                <h1>Sorry</h1>
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </CascadingAuthenticationState>
    </NotFound>
</Router>
```

<span data-ttu-id="6f25e-226">`<NotFound>`, `<NotAuthorized>` 및 `<Authorizing>`의 콘텐츠에는 다른 대화형 구성 요소와 같은 임의 항목이 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-226">The content of `<NotFound>`, `<NotAuthorized>`, and `<Authorizing>` can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="6f25e-227">`<NotAuthorized>`를 지정하지 않으면, `<AuthorizeRouteView>`는 다음 대체 메시지를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-227">If `<NotAuthorized>` isn't specified, the `<AuthorizeRouteView>` uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="6f25e-228">인증 상태 변경 알림</span><span class="sxs-lookup"><span data-stu-id="6f25e-228">Notification about authentication state changes</span></span>

<span data-ttu-id="6f25e-229">예를 들어 사용자가 로그아웃했거나 다른 사용자가 해당 역할을 변경하여 기본 인증 상태 데이터가 변경되었음을 앱이 확인하면, 사용자 지정 `AuthenticationStateProvider`는 필요에 따라 `AuthenticationStateProvider` 기본 클래스에서 `NotifyAuthenticationStateChanged` 메서드를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-229">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a custom `AuthenticationStateProvider` can optionally invoke the method `NotifyAuthenticationStateChanged` on the `AuthenticationStateProvider` base class.</span></span> <span data-ttu-id="6f25e-230">그러면 인증 상태 데이터의 소비자(예: `AuthorizeView`)가 새 데이터를 사용하여 다시 렌더링하라는 알림을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-230">This notifies consumers of the authentication state data (for example, `AuthorizeView`) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="6f25e-231">절차적 논리</span><span class="sxs-lookup"><span data-stu-id="6f25e-231">Procedural logic</span></span>

<span data-ttu-id="6f25e-232">앱이 절차적 논리의 일부로 권한 부여 규칙을 확인해야 하는 경우, `Task<AuthenticationState>` 형식의 연계 매개 변수를 사용하여 사용자의 <xref:System.Security.Claims.ClaimsPrincipal>을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-232">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<AuthenticationState>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="6f25e-233">`IAuthorizationService` 등의 다른 서비스와 `Task<AuthenticationState>`를 결합하여 정책을 평가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-233">`Task<AuthenticationState>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

```cshtml
@inject IAuthorizationService AuthorizationService

<button @onclick="@DoSomething">Do something important</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task DoSomething()
    {
        var user = (await authenticationStateTask).User;

        if (user.Identity.IsAuthenticated)
        {
            // Perform an action only available to authenticated (signed-in) users.
        }

        if (user.IsInRole("admin"))
        {
            // Perform an action only available to users in the 'admin' role.
        }

        if ((await AuthorizationService.AuthorizeAsync(user, "content-editor"))
            .Succeeded)
        {
            // Perform an action only available to users satisfying the 
            // 'content-editor' policy.
        }
    }
}
```

## <a name="authorization-in-blazor-client-side-apps"></a><span data-ttu-id="6f25e-234">Blazor 클라이언트 쪽 앱의 권한 부여</span><span class="sxs-lookup"><span data-stu-id="6f25e-234">Authorization in Blazor client-side apps</span></span>

<span data-ttu-id="6f25e-235">Blazor 클라이언트 쪽 앱에서는 사용자가 클라이언트 쪽 코드를 모두 수정할 수 있기 때문에 권한 부여 확인을 무시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-235">In Blazor client-side apps, authorization checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="6f25e-236">JavaScript SPA 프레임워크 또는 모든 운영 체제의 네이티브 앱을 포함하여 모든 클라이언트 쪽 앱 기술에는 동일하게 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-236">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="6f25e-237">**항상 클라이언트 쪽 앱을 통해 액세스한 API 엔드포인트 내에서 서버의 권한 부여 확인을 수행합니다.**</span><span class="sxs-lookup"><span data-stu-id="6f25e-237">**Always perform authorization checks on the server within any API endpoints accessed by your client-side app.**</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="6f25e-238">오류 문제 해결</span><span class="sxs-lookup"><span data-stu-id="6f25e-238">Troubleshoot errors</span></span>

<span data-ttu-id="6f25e-239">일반적인 오류:</span><span class="sxs-lookup"><span data-stu-id="6f25e-239">Common errors:</span></span>

* <span data-ttu-id="6f25e-240">**권한을 부여하려면 Task\<AuthenticationState> 형식의 연계 매개 변수가 필요합니다. CascadingAuthenticationState를 사용하여 이 매개 변수를 제공하는 것이 좋습니다.**</span><span class="sxs-lookup"><span data-stu-id="6f25e-240">**Authorization requires a cascading parameter of type Task\<AuthenticationState>. Consider using CascadingAuthenticationState to supply this.**</span></span>

* <span data-ttu-id="6f25e-241">**`authenticationStateTask`에 대해 `null` 값을 받았습니다.**</span><span class="sxs-lookup"><span data-stu-id="6f25e-241">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="6f25e-242">인증을 사용할 수 있는 Blazor 서버 쪽 템플릿으로 프로젝트를 만들지 않았을 가능성이 큽니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-242">It's likely that the project wasn't created using a Blazor server-side template with authentication enabled.</span></span> <span data-ttu-id="6f25e-243">*App.razor* 등의 UI 트리 일부를 `<CascadingAuthenticationState>`로 다음과 같이 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-243">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in *App.razor* as follows:</span></span>

```cshtml
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="6f25e-244">`CascadingAuthenticationState`는 기본 `AuthenticationStateProvider` DI 서비스로부터 받은 `Task<AuthenticationState>` 연계 매개 변수를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="6f25e-244">The `CascadingAuthenticationState` supplies the `Task<AuthenticationState>` cascading parameter, which in turn it receives from the underlying `AuthenticationStateProvider` DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6f25e-245">추가 자료</span><span class="sxs-lookup"><span data-stu-id="6f25e-245">Additional resources</span></span>

* <xref:security/index>
* <xref:security/blazor/server-side>
* <xref:security/authentication/windowsauth>
