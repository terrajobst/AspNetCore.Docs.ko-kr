---
title: ASP.NET Core Blazor 인증 및 권한 부여
author: guardrex
description: Blazor 인증 및 권한 부여 시나리오에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/02/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/index
ms.openlocfilehash: c7b3788b5737073100e7fa449fd6bb4a83c0043a
ms.sourcegitcommit: 235623b6e5a5d1841139c82a11ac2b4b3f31a7a9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114889"
---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a><span data-ttu-id="a5eab-103">ASP.NET Core Blazor 인증 및 권한 부여</span><span class="sxs-lookup"><span data-stu-id="a5eab-103">ASP.NET Core Blazor authentication and authorization</span></span>

<span data-ttu-id="a5eab-104">작성자: [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="a5eab-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="a5eab-105">ASP.NET Core는 Blazor 앱의 보안 구성 및 관리를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-105">ASP.NET Core supports the configuration and management of security in Blazor apps.</span></span>

<span data-ttu-id="a5eab-106">Blazor 서버와 Blazor WebAssembly 앱의 보안 시나리오는 서로 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-106">Security scenarios differ between Blazor Server and Blazor WebAssembly apps.</span></span> <span data-ttu-id="a5eab-107">Blazor 서버 앱은 서버에서 실행되기 때문에 권한 부여 확인을 통해 다음을 결정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-107">Because Blazor Server apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="a5eab-108">사용자에게 표시되는 UI 옵션(예: 사용자가 사용할 수 있는 메뉴 항목)</span><span class="sxs-lookup"><span data-stu-id="a5eab-108">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="a5eab-109">앱 영역과 구성 요소의 액세스 규칙</span><span class="sxs-lookup"><span data-stu-id="a5eab-109">Access rules for areas of the app and components.</span></span>

<span data-ttu-id="a5eab-110">Blazor WebAssembly 앱은 클라이언트에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-110">Blazor WebAssembly apps run on the client.</span></span> <span data-ttu-id="a5eab-111">권한 부여는 표시할 UI 옵션을 결정하는 ‘용도로만’ 사용됩니다. </span><span class="sxs-lookup"><span data-stu-id="a5eab-111">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="a5eab-112">사용자가 클라이언트 쪽 확인을 수정하거나 무시할 수 있기 때문에, Blazor WebAssembly 앱은 권한 부여 액세스 규칙을 적용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-112">Since client-side checks can be modified or bypassed by a user, a Blazor WebAssembly app can't enforce authorization access rules.</span></span>

## <a name="authentication"></a><span data-ttu-id="a5eab-113">인증</span><span class="sxs-lookup"><span data-stu-id="a5eab-113">Authentication</span></span>

<span data-ttu-id="a5eab-114">Blazor는 기존 ASP.NET Core 인증 메커니즘을 사용하여 사용자 ID를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-114">Blazor uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="a5eab-115">정확한 메커니즘은 Blazor 앱을 호스트하는 방법, 즉 Blazor 서버인지 또는 Blazor WebAssembly인지에 따라 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-115">The exact mechanism depends on how the Blazor app is hosted, Blazor Server or Blazor WebAssembly.</span></span>

### <a name="blazor-server-authentication"></a><span data-ttu-id="a5eab-116">Blazor 서버 인증</span><span class="sxs-lookup"><span data-stu-id="a5eab-116">Blazor Server authentication</span></span>

<span data-ttu-id="a5eab-117">Blazor 서버 앱은 SignalR을 사용하여 생성된 실시간 연결을 통해 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-117">Blazor Server apps operate over a real-time connection that's created using SignalR.</span></span> <span data-ttu-id="a5eab-118">[SignalR 기반 앱의 인증](xref:signalr/authn-and-authz)은 연결 시 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-118">[Authentication in SignalR-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="a5eab-119">인증은 쿠키 또는 다른 전달자 토큰을 기반으로 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-119">Authentication can be based on a cookie or some other bearer token.</span></span>

<span data-ttu-id="a5eab-120">프로젝트를 만들 때 Blazor 서버 프로젝트 템플릿에서 자동으로 인증을 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-120">The Blazor Server project template can set up authentication for you when the project is created.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a5eab-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a5eab-121">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a5eab-122"><xref:blazor/get-started> 문서의 Visual Studio 지침에 따라 인증 메커니즘이 있는 새 Blazor 서버 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-122">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="a5eab-123">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **Blazor 서버 앱** 템플릿을 선택한 후 **인증**에서 **변경**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-123">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="a5eab-124">다른 ASP.NET Core 프로젝트에 사용할 수 있는 것과 동일한 인증 메커니즘 세트를 제공하는 대화 상자가 열립니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-124">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="a5eab-125">**인증 없음**</span><span class="sxs-lookup"><span data-stu-id="a5eab-125">**No Authentication**</span></span>
* <span data-ttu-id="a5eab-126">**개별 사용자 계정** &ndash; 사용자 계정은 다음과 같이 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-126">**Individual User Accounts** &ndash; User accounts can be stored:</span></span>
  * <span data-ttu-id="a5eab-127">ASP.NET Core의 [ID](xref:security/authentication/identity) 시스템을 사용하여 앱에 저장</span><span class="sxs-lookup"><span data-stu-id="a5eab-127">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="a5eab-128">[Azure AD B2C](xref:security/authentication/azure-ad-b2c) 사용</span><span class="sxs-lookup"><span data-stu-id="a5eab-128">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="a5eab-129">**회사 또는 학교 계정**</span><span class="sxs-lookup"><span data-stu-id="a5eab-129">**Work or School Accounts**</span></span>
* <span data-ttu-id="a5eab-130">**Windows 인증**</span><span class="sxs-lookup"><span data-stu-id="a5eab-130">**Windows Authentication**</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="a5eab-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a5eab-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a5eab-132"><xref:blazor/get-started> 문서의 Visual Studio Code 지침에 따라 인증 메커니즘이 있는 새 Blazor 서버 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-132">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="a5eab-133">허용되는 인증 값(`{AUTHENTICATION}`)은 다음 표에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-133">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="a5eab-134">인증 메커니즘</span><span class="sxs-lookup"><span data-stu-id="a5eab-134">Authentication mechanism</span></span>                                                                 | <span data-ttu-id="a5eab-135">`{AUTHENTICATION}` 값</span><span class="sxs-lookup"><span data-stu-id="a5eab-135">`{AUTHENTICATION}` value</span></span> |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| <span data-ttu-id="a5eab-136">인증 없음</span><span class="sxs-lookup"><span data-stu-id="a5eab-136">No Authentication</span></span>                                                                        | `None`                   |
| <span data-ttu-id="a5eab-137">개인</span><span class="sxs-lookup"><span data-stu-id="a5eab-137">Individual</span></span><br><span data-ttu-id="a5eab-138">ASP.NET Core ID를 사용하여 앱에 저장된 사용자</span><span class="sxs-lookup"><span data-stu-id="a5eab-138">Users stored in the app with ASP.NET Core Identity.</span></span>                        | `Individual`             |
| <span data-ttu-id="a5eab-139">개인</span><span class="sxs-lookup"><span data-stu-id="a5eab-139">Individual</span></span><br><span data-ttu-id="a5eab-140">[Azure AD B2C](xref:security/authentication/azure-ad-b2c)에 저장된 사용자</span><span class="sxs-lookup"><span data-stu-id="a5eab-140">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span> | `IndividualB2C`          |
| <span data-ttu-id="a5eab-141">회사 또는 학교 계정</span><span class="sxs-lookup"><span data-stu-id="a5eab-141">Work or School Accounts</span></span><br><span data-ttu-id="a5eab-142">단일 테넌트에 대한 조직 인증</span><span class="sxs-lookup"><span data-stu-id="a5eab-142">Organizational authentication for a single tenant.</span></span>            | `SingleOrg`              |
| <span data-ttu-id="a5eab-143">회사 또는 학교 계정</span><span class="sxs-lookup"><span data-stu-id="a5eab-143">Work or School Accounts</span></span><br><span data-ttu-id="a5eab-144">여러 테넌트에 대한 조직 인증</span><span class="sxs-lookup"><span data-stu-id="a5eab-144">Organizational authentication for multiple tenants.</span></span>           | `MultiOrg`               |
| <span data-ttu-id="a5eab-145">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="a5eab-145">Windows Authentication</span></span>                                                                   | `Windows`                |

<span data-ttu-id="a5eab-146">이 명령은 `{APP NAME}` 자리 표시자에 제공된 값으로 이름을 지정하여 폴더를 만들고, 폴더 이름을 앱 이름으로 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-146">The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name.</span></span> <span data-ttu-id="a5eab-147">자세한 내용은 .NET Core 가이드의 [dotnet new](/dotnet/core/tools/dotnet-new) 명령을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a5eab-147">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

<!--

# [Visual Studio for Mac](#tab/visual-studio-mac)

1. Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.

1.

1.

-->

<!--
# [.NET Core CLI](#tab/netcore-cli/)

Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:

```dotnetcli
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

### <a name="opno-locblazor-webassembly-authentication"></a>Blazor<span data-ttu-id="a5eab-148"> WebAssembly 인증</span><span class="sxs-lookup"><span data-stu-id="a5eab-148"> WebAssembly authentication</span></span>

<span data-ttu-id="a5eab-149">Blazor WebAssembly 앱에서는 사용자가 클라이언트 쪽 코드를 모두 수정할 수 있기 때문에 인증 확인을 무시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-149">In Blazor WebAssembly apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="a5eab-150">JavaScript SPA 프레임워크 또는 모든 운영 체제의 네이티브 앱을 포함하여 모든 클라이언트 쪽 앱 기술에는 동일하게 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-150">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="a5eab-151">[Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/)에 대한 패키지 참조를 앱의 프로젝트 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-151">Add a package reference for [Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) to the app's project file.</span></span>

<span data-ttu-id="a5eab-152">Blazor WebAssembly 앱에 대한 사용자 지정 `AuthenticationStateProvider` 서비스 구현은 다음 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-152">Implementation of a custom `AuthenticationStateProvider` service for Blazor WebAssembly apps is covered in the following sections.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="a5eab-153">AuthenticationStateProvider 서비스</span><span class="sxs-lookup"><span data-stu-id="a5eab-153">AuthenticationStateProvider service</span></span>

Blazor<span data-ttu-id="a5eab-154"> 서버 앱에는 ASP.NET Core의 `HttpContext.User`에서 인증 상태 데이터를 가져오는 `AuthenticationStateProvider` 서비스가 기본 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-154"> Server apps include a built-in `AuthenticationStateProvider` service that obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="a5eab-155">이런 방법으로 인증 상태가 기존의 ASP.NET Core 서버 쪽 인증 메커니즘과 통합됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-155">This is how authentication state integrates with existing ASP.NET Core server-side authentication mechanisms.</span></span>

<span data-ttu-id="a5eab-156">`AuthenticationStateProvider`는 `AuthorizeView` 구성 요소와 `CascadingAuthenticationState` 구성 요소가 인증 상태를 가져오는 데 사용하는 기본 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-156">`AuthenticationStateProvider` is the underlying service used by the `AuthorizeView` component and `CascadingAuthenticationState` component to get the authentication state.</span></span>

<span data-ttu-id="a5eab-157">일반적으로 `AuthenticationStateProvider`를 직접 사용하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-157">You don't typically use `AuthenticationStateProvider` directly.</span></span> <span data-ttu-id="a5eab-158">[AuthorizeView 구성 요소](#authorizeview-component) 또는 이 문서의 뒷부분에서 설명하는 [Task<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) 접근 방식을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-158">Use the [AuthorizeView component](#authorizeview-component) or [Task<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="a5eab-159">`AuthenticationStateProvider`를 직접 사용하는 경우의 주요 단점은 기본 인증 상태 데이터가 변경될 때 구성 요소가 자동으로 알림을 받지 못하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-159">The main drawback to using `AuthenticationStateProvider` directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="a5eab-160">`AuthenticationStateProvider` 서비스는 다음 예제와 같이 현재 사용자의 <xref:System.Security.Claims.ClaimsPrincipal> 데이터를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-160">The `AuthenticationStateProvider` service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.Authorization
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

<span data-ttu-id="a5eab-161">`user.Identity.IsAuthenticated`가 `true`이면, 사용자가 <xref:System.Security.Claims.ClaimsPrincipal>이므로 클레임을 열거하고 역할의 멤버 자격을 평가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-161">If `user.Identity.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="a5eab-162">DI(종속성 주입) 및 서비스에 대한 자세한 내용은 <xref:blazor/dependency-injection> 및 <xref:fundamentals/dependency-injection>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a5eab-162">For more information on dependency injection (DI) and services, see <xref:blazor/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="a5eab-163">사용자 지정 AuthenticationStateProvider 구현</span><span class="sxs-lookup"><span data-stu-id="a5eab-163">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="a5eab-164">Blazor WebAssembly 앱을 빌드하는 경우 또는 앱의 사양에 사용자 지정 공급자가 필요한 경우, 공급자를 구현하고 `GetAuthenticationStateAsync`를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-164">If you're building a Blazor WebAssembly app or if your app's specification absolutely requires a custom provider, implement a provider and override `GetAuthenticationStateAsync`:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Authorization;

namespace BlazorSample.Services
{
    public class CustomAuthStateProvider : AuthenticationStateProvider
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
}
```

<span data-ttu-id="a5eab-165">Blazor WebAssembly 앱에서 `CustomAuthStateProvider` 서비스는 *Program.cs*의 `Main`에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-165">In a Blazor WebAssembly app, the `CustomAuthStateProvider` service is registered in `Main` of *Program.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Blazor.Hosting;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.Extensions.DependencyInjection;
using BlazorSample.Services;

public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddScoped<AuthenticationStateProvider, 
            CustomAuthStateProvider>();
        builder.RootComponents.Add<App>("app");

        await builder.Build().RunAsync();
    }
}
```

<span data-ttu-id="a5eab-166">`CustomAuthStateProvider`를 사용하면 모든 사용자가 사용자 이름 `mrfibuli`로 인증됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-166">Using the `CustomAuthStateProvider`, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="a5eab-167">인증 상태를 연계 매개 변수로 공개</span><span class="sxs-lookup"><span data-stu-id="a5eab-167">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="a5eab-168">사용자가 실행한 작업을 수행하는 경우와 같이 절차적 논리에 인증 상태 데이터가 필요한 경우 `Task<AuthenticationState>` 형식의 연계 매개 변수를 정의하여 인증 상태 데이터를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-168">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<AuthenticationState>`:</span></span>

```razor
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

> [!NOTE]
> <span data-ttu-id="a5eab-169">Blazor WebAssembly 앱 구성 요소에서 `Microsoft.AspNetCore.Components.Authorization` 네임스페이스(`@using Microsoft.AspNetCore.Components.Authorization`)를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-169">In a Blazor WebAssembly app component, add the `Microsoft.AspNetCore.Components.Authorization` namespace (`@using Microsoft.AspNetCore.Components.Authorization`).</span></span>

<span data-ttu-id="a5eab-170">`user.Identity.IsAuthenticated`가 `true`이면, 클레임을 열거하고 역할의 멤버 자격을 평가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-170">If `user.Identity.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="a5eab-171">`AuthorizeRouteView`, `CascadingAuthenticationState` 구성 요소 및 *App.razor* 파일을 사용하여 `Task<AuthenticationState>` 연계 매개 변수를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-171">Set up the `Task<AuthenticationState>` cascading parameter using the `AuthorizeRouteView` and `CascadingAuthenticationState` components in the *App.razor* file:</span></span>

```razor
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

## <a name="authorization"></a><span data-ttu-id="a5eab-172">권한 부여</span><span class="sxs-lookup"><span data-stu-id="a5eab-172">Authorization</span></span>

<span data-ttu-id="a5eab-173">사용자가 인증되면, 사용자가 수행할 수 있는 작업을 제어하기 위해 ‘권한 부여’ 규칙이 적용됩니다. </span><span class="sxs-lookup"><span data-stu-id="a5eab-173">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="a5eab-174">일반적으로 다음 여부에 따라 액세스가 허용 또는 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-174">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="a5eab-175">사용자가 인증(로그인)되었는지 여부</span><span class="sxs-lookup"><span data-stu-id="a5eab-175">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="a5eab-176">사용자가 ‘역할’에 속하는지 여부 </span><span class="sxs-lookup"><span data-stu-id="a5eab-176">A user is in a *role*.</span></span>
* <span data-ttu-id="a5eab-177">사용자에게 ‘클레임’이 있는지 여부 </span><span class="sxs-lookup"><span data-stu-id="a5eab-177">A user has a *claim*.</span></span>
* <span data-ttu-id="a5eab-178">‘정책’이 충족되었는지 여부 </span><span class="sxs-lookup"><span data-stu-id="a5eab-178">A *policy* is satisfied.</span></span>

<span data-ttu-id="a5eab-179">이러한 각 개념은 ASP.NET Core MVC 또는 Razor Pages 앱에서와 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-179">Each of these concepts is the same as in an ASP.NET Core MVC or Razor Pages app.</span></span> <span data-ttu-id="a5eab-180">ASP.NET Core 보안에 대한 자세한 내용은 [ASP.NET Core 보안 및 ID](xref:security/index)의 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a5eab-180">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and Identity](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="a5eab-181">AuthorizeView 구성 요소</span><span class="sxs-lookup"><span data-stu-id="a5eab-181">AuthorizeView component</span></span>

<span data-ttu-id="a5eab-182">`AuthorizeView` 구성 요소는 사용자에게 볼 수 있는 권한이 있는지 여부에 따라 선택적으로 UI를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-182">The `AuthorizeView` component selectively displays UI depending on whether the user is authorized to see it.</span></span> <span data-ttu-id="a5eab-183">이 접근 방식은 사용자에게 데이터를 ‘표시’하기만 하면 되고 절차적 논리에 사용자 ID를 사용할 필요가 없는 경우에 유용합니다. </span><span class="sxs-lookup"><span data-stu-id="a5eab-183">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="a5eab-184">이 구성 요소는 로그인한 사용자 정보에 액세스하는 데 사용할 수 있는 `AuthenticationState` 형식의 `context` 변수를 공개합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-184">The component exposes a `context` variable of type `AuthenticationState`, which you can use to access information about the signed-in user:</span></span>

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="a5eab-185">사용자가 인증되지 않은 경우에 표시할 다른 콘텐츠를 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-185">You can also supply different content for display if the user isn't authenticated:</span></span>

```razor
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

<span data-ttu-id="a5eab-186">`<Authorized>` 및 `<NotAuthorized>` 태그의 콘텐츠에는 다른 대화형 구성 요소와 같은 임의 항목이 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-186">The content of `<Authorized>` and `<NotAuthorized>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="a5eab-187">UI 옵션이나 액세스를 제어하는 역할 또는 정책과 같은 권한 부여 조건은 [권한 부여](#authorization) 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-187">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="a5eab-188">권한 부여 조건을 지정하지 않으면 `AuthorizeView`는 기본 정책을 사용하고 다음과 같이 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-188">If authorization conditions aren't specified, `AuthorizeView` uses a default policy and treats:</span></span>

* <span data-ttu-id="a5eab-189">인증(로그인)된 사용자를 권한 있는 사용자로 처리</span><span class="sxs-lookup"><span data-stu-id="a5eab-189">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="a5eab-190">인증되지 않은(로그아웃된) 사용자를 권한 없는 사용자로 처리</span><span class="sxs-lookup"><span data-stu-id="a5eab-190">Unauthenticated (signed-out) users as unauthorized.</span></span>

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="a5eab-191">역할 기반 및 정책 기반 권한 부여</span><span class="sxs-lookup"><span data-stu-id="a5eab-191">Role-based and policy-based authorization</span></span>

<span data-ttu-id="a5eab-192">`AuthorizeView` 구성 요소는 ‘역할 기반’ 또는 ‘정책 기반’ 권한 부여를 지원합니다.  </span><span class="sxs-lookup"><span data-stu-id="a5eab-192">The `AuthorizeView` component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="a5eab-193">역할 기반 권한 부여의 경우 `Roles` 매개 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-193">For role-based authorization, use the `Roles` parameter:</span></span>

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="a5eab-194">자세한 내용은 <xref:security/authorization/roles>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a5eab-194">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="a5eab-195">정책 기반 권한 부여의 경우 `Policy` 매개 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-195">For policy-based authorization, use the `Policy` parameter:</span></span>

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="a5eab-196">클레임 기반 권한 부여는 정책 기반 권한 부여의 특별한 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-196">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="a5eab-197">예를 들어 사용자에게 특정 클레임이 있어야 하는 정책을 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-197">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="a5eab-198">자세한 내용은 <xref:security/authorization/policies>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a5eab-198">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="a5eab-199">이러한 API는 Blazor 서버나 Blazor WebAssembly 앱에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-199">These APIs can be used in either Blazor Server or Blazor WebAssembly apps.</span></span>

<span data-ttu-id="a5eab-200">`Roles` 또는 `Policy`를 지정하지 않으면 `AuthorizeView`는 기본 정책을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-200">If neither `Roles` nor `Policy` is specified, `AuthorizeView` uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="a5eab-201">비동기 인증 중에 표시되는 콘텐츠</span><span class="sxs-lookup"><span data-stu-id="a5eab-201">Content displayed during asynchronous authentication</span></span>

Blazor<span data-ttu-id="a5eab-202">에서는 인증 상태를 *비동기적으로* 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-202"> allows for authentication state to be determined *asynchronously*.</span></span> <span data-ttu-id="a5eab-203">이 접근 방식의 주요 시나리오는 Blazor WebAssembly 앱이 외부 엔드포인트에 인증 요청을 하는 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-203">The primary scenario for this approach is in Blazor WebAssembly apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="a5eab-204">인증이 진행되는 동안 `AuthorizeView`는 기본적으로 아무 콘텐츠도 표시하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-204">While authentication is in progress, `AuthorizeView` displays no content by default.</span></span> <span data-ttu-id="a5eab-205">인증 중에 콘텐츠를 표시하려면 `<Authorizing>` 요소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-205">To display content while authentication occurs, use the `<Authorizing>` element:</span></span>

```razor
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

<span data-ttu-id="a5eab-206">이 접근 방식은 일반적으로 Blazor 서버 앱에 적용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-206">This approach isn't normally applicable to Blazor Server apps.</span></span> Blazor<span data-ttu-id="a5eab-207"> 서버 앱은 상태가 설정되는 즉시 인증 상태를 알 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-207"> Server apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="a5eab-208">Blazor 서버 앱의 `AuthorizeView` 구성 요소에 `Authorizing` 콘텐츠를 제공할 수는 있지만, 이 콘텐츠는 표시되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-208">`Authorizing` content can be provided in a Blazor Server app's `AuthorizeView` component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="a5eab-209">[Authorize] 특성</span><span class="sxs-lookup"><span data-stu-id="a5eab-209">[Authorize] attribute</span></span>

<span data-ttu-id="a5eab-210">`[Authorize]` 특성은 Razor 구성 요소에서 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-210">The `[Authorize]` attribute can be used in Razor components:</span></span>

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!NOTE]
> <span data-ttu-id="a5eab-211">Blazor WebAssembly 앱 구성 요소에서 `Microsoft.AspNetCore.Authorization` 네임스페이스(`@using Microsoft.AspNetCore.Authorization`)를 이 섹션의 예제에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-211">In a Blazor WebAssembly app component, add the `Microsoft.AspNetCore.Authorization` namespace (`@using Microsoft.AspNetCore.Authorization`) to the examples in this section.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a5eab-212">Blazor 라우터를 통해 연결된 `@page` 구성 요소에서만 `[Authorize]`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-212">Only use `[Authorize]` on `@page` components reached via the Blazor Router.</span></span> <span data-ttu-id="a5eab-213">권한 부여는 라우팅의 일부로만 수행되고, 페이지에 렌더링된 자식 구성 요소에 대해서는 수행되지 ‘않습니다’. </span><span class="sxs-lookup"><span data-stu-id="a5eab-213">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="a5eab-214">페이지 내의 특정 파트 표시 권한을 부여하려면 `AuthorizeView`를 대신 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-214">To authorize the display of specific parts within a page, use `AuthorizeView` instead.</span></span>

<span data-ttu-id="a5eab-215">`[Authorize]` 특성은 역할 기반 또는 정책 기반 권한 부여도 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-215">The `[Authorize]` attribute also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="a5eab-216">역할 기반 권한 부여의 경우 `Roles` 매개 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-216">For role-based authorization, use the `Roles` parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="a5eab-217">정책 기반 권한 부여의 경우 `Policy` 매개 변수를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-217">For policy-based authorization, use the `Policy` parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="a5eab-218">`Roles` 또는 `Policy`를 지정하지 않으면 `[Authorize]`는 기본적으로 다음과 같이 처리하는 기본 정책을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-218">If neither `Roles` nor `Policy` is specified, `[Authorize]` uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="a5eab-219">인증(로그인)된 사용자를 권한 있는 사용자로 처리</span><span class="sxs-lookup"><span data-stu-id="a5eab-219">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="a5eab-220">인증되지 않은(로그아웃된) 사용자를 권한 없는 사용자로 처리</span><span class="sxs-lookup"><span data-stu-id="a5eab-220">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="a5eab-221">Router 구성 요소를 사용하여 권한 없는 콘텐츠 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="a5eab-221">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="a5eab-222">`AuthorizeRouteView` 구성 요소와 함께 `Router` 구성 요소를 사용하면 다음과 같은 경우 앱이 사용자 지정 콘텐츠를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-222">The `Router` component, in conjunction with the `AuthorizeRouteView` component, allows the app to specify custom content if:</span></span>

* <span data-ttu-id="a5eab-223">콘텐츠를 찾을 수 없는 경우</span><span class="sxs-lookup"><span data-stu-id="a5eab-223">Content isn't found.</span></span>
* <span data-ttu-id="a5eab-224">사용자가 구성 요소에 적용된 `[Authorize]` 조건을 충족하지 못하는 경우.</span><span class="sxs-lookup"><span data-stu-id="a5eab-224">The user fails an `[Authorize]` condition applied to the component.</span></span> <span data-ttu-id="a5eab-225">`[Authorize]` 특성은 [`[Authorize]` 속성](#authorize-attribute) 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-225">The `[Authorize]` attribute is covered in the [`[Authorize]` attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="a5eab-226">비동기 인증이 진행 중인 경우</span><span class="sxs-lookup"><span data-stu-id="a5eab-226">Asynchronous authentication is in progress.</span></span>

<span data-ttu-id="a5eab-227">기본 Blazor 서버 프로젝트 템플릿에서 *App.razor* 파일은 사용자 지정 콘텐츠를 설정하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-227">In the default Blazor Server project template, the *App.razor* file demonstrates how to set custom content:</span></span>

```razor
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

<span data-ttu-id="a5eab-228">`<NotFound>`, `<NotAuthorized>` 및 `<Authorizing>` 태그의 콘텐츠에는 다른 대화형 구성 요소와 같은 임의 항목이 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-228">The content of `<NotFound>`, `<NotAuthorized>`, and `<Authorizing>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="a5eab-229">`<NotAuthorized>` 요소를 지정하지 않으면 `AuthorizeRouteView`는 다음 대체 메시지를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-229">If the `<NotAuthorized>` element isn't specified, the `AuthorizeRouteView` uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="a5eab-230">인증 상태 변경 알림</span><span class="sxs-lookup"><span data-stu-id="a5eab-230">Notification about authentication state changes</span></span>

<span data-ttu-id="a5eab-231">예를 들어 사용자가 로그아웃했거나 다른 사용자가 해당 역할을 변경하여 기본 인증 상태 데이터가 변경되었음을 앱이 확인하면, 사용자 지정 `AuthenticationStateProvider`는 필요에 따라 `AuthenticationStateProvider` 기본 클래스에서 `NotifyAuthenticationStateChanged` 메서드를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-231">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a custom `AuthenticationStateProvider` can optionally invoke the method `NotifyAuthenticationStateChanged` on the `AuthenticationStateProvider` base class.</span></span> <span data-ttu-id="a5eab-232">그러면 인증 상태 데이터의 소비자(예: `AuthorizeView`)가 새 데이터를 사용하여 다시 렌더링하라는 알림을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-232">This notifies consumers of the authentication state data (for example, `AuthorizeView`) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="a5eab-233">절차적 논리</span><span class="sxs-lookup"><span data-stu-id="a5eab-233">Procedural logic</span></span>

<span data-ttu-id="a5eab-234">앱이 절차적 논리의 일부로 권한 부여 규칙을 확인해야 하는 경우, `Task<AuthenticationState>` 형식의 연계 매개 변수를 사용하여 사용자의 <xref:System.Security.Claims.ClaimsPrincipal>을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-234">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<AuthenticationState>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="a5eab-235">`IAuthorizationService` 등의 다른 서비스와 `Task<AuthenticationState>`를 결합하여 정책을 평가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-235">`Task<AuthenticationState>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

```razor
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

> [!NOTE]
> <span data-ttu-id="a5eab-236">Blazor WebAssembly 앱 구성 요소에서 `Microsoft.AspNetCore.Authorization` 및 `Microsoft.AspNetCore.Components.Authorization` 네임스페이스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-236">In a Blazor WebAssembly app component, add the `Microsoft.AspNetCore.Authorization` and `Microsoft.AspNetCore.Components.Authorization` namespaces:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```

## <a name="authorization-in-opno-locblazor-webassembly-apps"></a><span data-ttu-id="a5eab-237">Blazor WebAssembly 앱의 권한 부여</span><span class="sxs-lookup"><span data-stu-id="a5eab-237">Authorization in Blazor WebAssembly apps</span></span>

<span data-ttu-id="a5eab-238">Blazor WebAssembly 앱에서는 사용자가 클라이언트 쪽 코드를 모두 수정할 수 있기 때문에 권한 부여 확인을 무시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-238">In Blazor WebAssembly apps, authorization checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="a5eab-239">JavaScript SPA 프레임워크 또는 모든 운영 체제의 네이티브 앱을 포함하여 모든 클라이언트 쪽 앱 기술에는 동일하게 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-239">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="a5eab-240">**항상 클라이언트 쪽 앱을 통해 액세스한 API 엔드포인트 내에서 서버의 권한 부여 확인을 수행합니다.**</span><span class="sxs-lookup"><span data-stu-id="a5eab-240">**Always perform authorization checks on the server within any API endpoints accessed by your client-side app.**</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="a5eab-241">오류 문제 해결</span><span class="sxs-lookup"><span data-stu-id="a5eab-241">Troubleshoot errors</span></span>

<span data-ttu-id="a5eab-242">일반적인 오류:</span><span class="sxs-lookup"><span data-stu-id="a5eab-242">Common errors:</span></span>

* <span data-ttu-id="a5eab-243">**권한을 부여하려면 Task\<AuthenticationState> 형식의 연계 매개 변수가 필요합니다. CascadingAuthenticationState를 사용하여 이 매개 변수를 제공하는 것이 좋습니다.**</span><span class="sxs-lookup"><span data-stu-id="a5eab-243">**Authorization requires a cascading parameter of type Task\<AuthenticationState>. Consider using CascadingAuthenticationState to supply this.**</span></span>

* <span data-ttu-id="a5eab-244">**`authenticationStateTask`에 대해 `null` 값을 받았습니다.**</span><span class="sxs-lookup"><span data-stu-id="a5eab-244">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="a5eab-245">인증을 사용할 수 있는 Blazor 서버 템플릿으로 프로젝트를 만들지 않았을 가능성이 큽니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-245">It's likely that the project wasn't created using a Blazor Server template with authentication enabled.</span></span> <span data-ttu-id="a5eab-246">*App.razor* 등의 UI 트리 일부를 `<CascadingAuthenticationState>`로 다음과 같이 래핑합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-246">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in *App.razor* as follows:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="a5eab-247">`CascadingAuthenticationState`는 기본 `AuthenticationStateProvider` DI 서비스로부터 받은 `Task<AuthenticationState>` 연계 매개 변수를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="a5eab-247">The `CascadingAuthenticationState` supplies the `Task<AuthenticationState>` cascading parameter, which in turn it receives from the underlying `AuthenticationStateProvider` DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a5eab-248">추가 자료</span><span class="sxs-lookup"><span data-stu-id="a5eab-248">Additional resources</span></span>

* <xref:security/index>
* <xref:security/blazor/server>
* <xref:security/authentication/windowsauth>
* <span data-ttu-id="a5eab-249">[Awesome Blazor: 인증](https://github.com/AdrienTorris/awesome-blazor#authentication) 커뮤니티 샘플 링크</span><span class="sxs-lookup"><span data-stu-id="a5eab-249">[Awesome Blazor: Authentication](https://github.com/AdrienTorris/awesome-blazor#authentication) community sample links</span></span>
