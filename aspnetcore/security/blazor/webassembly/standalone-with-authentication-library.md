---
title: 인증 라이브러리를 Blazor 통해 ASP.NET 핵심 웹어셈블리 독립 실행형 앱 보호
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 893fff10df37e1c2be549604f4cb83cd20049108
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977043"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="6cf67-102">인증 라이브러리를 Blazor 통해 ASP.NET 핵심 웹어셈블리 독립 실행형 앱 보호</span><span class="sxs-lookup"><span data-stu-id="6cf67-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="6cf67-103">[하비에르 칼바로 넬슨과](https://github.com/javiercn) [루크 라담](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6cf67-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="6cf67-104">*Azure Active Directory(AAD) 및 Azure Active Directory B2C(AAD B2C)의 경우 이 항목의 지침을 따르지 마십시오. 이 목회 자 노드의 AAD 및 AAD B2C 항목을 참조하십시오.*</span><span class="sxs-lookup"><span data-stu-id="6cf67-104">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="6cf67-105">라이브러리를 Blazor 사용하는 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` WebAssembly 독립 실행형 앱을 만들려면 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf67-105">To create a Blazor WebAssembly standalone app that uses `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="6cf67-106">프로젝트 폴더가 없는 경우 만드는 출력 위치를 지정하려면 경로가 있는 명령에 출력 옵션을 포함합니다(예: `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="6cf67-106">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="6cf67-107">폴더 이름도 프로젝트 이름의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6cf67-107">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="6cf67-108">비주얼 스튜디오에서 [ Blazor 웹 어셈블리 응용 프로그램을 만듭니다.](xref:blazor/get-started)</span><span class="sxs-lookup"><span data-stu-id="6cf67-108">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="6cf67-109">스토어 사용자 계정 **인앱** 옵션을 사용하여 개별 사용자 **계정으로** **인증을** 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf67-109">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="6cf67-110">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="6cf67-110">Authentication package</span></span>

<span data-ttu-id="6cf67-111">개별 사용자 계정을 사용하도록 앱을 만들면 앱은 앱의 프로젝트 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 파일에 있는 패키지에 대한 패키지 참조를 자동으로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="6cf67-111">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="6cf67-112">이 패키지는 앱이 사용자를 인증하고 보호된 API를 호출하는 토큰을 얻는 데 도움이 되는 기본 요소 집합을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf67-112">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="6cf67-113">앱에 인증을 추가하는 경우 앱의 프로젝트 파일에 패키지를 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf67-113">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="6cf67-114">앞의 패키지 참조에서 문서에 표시된 `{VERSION}` `Microsoft.AspNetCore.Blazor.Templates` 패키지 버전으로 <xref:blazor/get-started> 바꿉습니다.</span><span class="sxs-lookup"><span data-stu-id="6cf67-114">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="6cf67-115">인증 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="6cf67-115">Authentication service support</span></span>

<span data-ttu-id="6cf67-116">사용자 인증에 대한 지원은 패키지에서 제공하는 `AddOidcAuthentication` 확장 방법으로 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 서비스 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="6cf67-116">Support for authenticating users is registered in the service container with the `AddOidcAuthentication` extension method provided by the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="6cf67-117">이 메서드는 앱이 IP(ID 공급자)와 상호 작용하는 데 필요한 모든 서비스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf67-117">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="6cf67-118">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="6cf67-118">*Program.cs*:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    options.ProviderOptions.Authority = "{AUTHORITY}";
    options.ProviderOptions.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="6cf67-119">독립 실행형 앱에 대한 인증 지원은 개방형 ID 연결(OIDC)을 사용하여 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="6cf67-119">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="6cf67-120">이 `AddOidcAuthentication` 메서드는 OIDC를 사용하여 앱을 인증하는 데 필요한 매개 변수를 구성하는 콜백을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf67-120">The `AddOidcAuthentication` method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="6cf67-121">응용 프로그램을 구성하는 데 필요한 값은 OIDC 호환 IP에서 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6cf67-121">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="6cf67-122">일반적으로 온라인 포털에서 발생하는 앱을 등록할 때 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="6cf67-122">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="6cf67-123">토큰 범위에 액세스</span><span class="sxs-lookup"><span data-stu-id="6cf67-123">Access token scopes</span></span>

<span data-ttu-id="6cf67-124">WebAssembly 템플릿은 Blazor 보안 API에 대한 액세스 토큰을 요청하도록 앱을 자동으로 구성하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6cf67-124">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="6cf67-125">로그인 흐름의 일부로 토큰을 프로비전하려면 `OidcProviderOptions`다음의 기본 토큰 범위에 범위를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf67-125">To provision a token as part of the sign-in flow, add the scope to the default token scopes of the `OidcProviderOptions`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="6cf67-126">Azure 포털이 범위 URI를 제공하고 API에서 *401 무단* 응답을 받을 때 **앱이 처리되지 않은 예외를 throw하는** 경우 구성표 및 호스트를 포함하지 않는 범위 URI를 사용해 보십시오.</span><span class="sxs-lookup"><span data-stu-id="6cf67-126">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="6cf67-127">예를 들어 Azure 포털은 다음 범위 URI 형식 중 하나를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6cf67-127">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="6cf67-128">스키마 및 호스트 없이 범위 URI를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="6cf67-128">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="6cf67-129">자세한 내용은 <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="6cf67-129">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

## <a name="imports-file"></a><span data-ttu-id="6cf67-130">파일 가져오기</span><span class="sxs-lookup"><span data-stu-id="6cf67-130">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="6cf67-131">인덱스 페이지</span><span class="sxs-lookup"><span data-stu-id="6cf67-131">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="6cf67-132">앱 구성 요소</span><span class="sxs-lookup"><span data-stu-id="6cf67-132">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="6cf67-133">리디렉션토로그인 구성 요소</span><span class="sxs-lookup"><span data-stu-id="6cf67-133">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="6cf67-134">로그인표시 구성 요소</span><span class="sxs-lookup"><span data-stu-id="6cf67-134">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="6cf67-135">인증 구성 요소</span><span class="sxs-lookup"><span data-stu-id="6cf67-135">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="6cf67-136">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="6cf67-136">Additional resources</span></span>

* [<span data-ttu-id="6cf67-137">추가 액세스 토큰 요청</span><span class="sxs-lookup"><span data-stu-id="6cf67-137">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
