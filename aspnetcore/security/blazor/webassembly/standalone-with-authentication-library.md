---
title: 인증 라이브러리를 사용 하 여 ASP.NET Core Blazor Weasembomoma 독립 실행형 앱 보호
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: f9cc2884dcd94c729c45a056ae4327a2c75d34be
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083592"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="6e5d3-102">인증 라이브러리를 사용 하 여 ASP.NET Core Blazor Weasembomoma 독립 실행형 앱 보호</span><span class="sxs-lookup"><span data-stu-id="6e5d3-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="6e5d3-103">[Javier Calvarro e](https://github.com/javiercn) 및 [Luke latham 문자](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6e5d3-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="6e5d3-104">`Microsoft.AspNetCore.Components.WebAssembly.Authentication` 라이브러리를 사용 하는 Blazor Weasembomom독립형 앱을 만들려면 명령 셸에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="6e5d3-104">To create a Blazor WebAssembly standalone app that uses `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="6e5d3-105">출력 위치를 지정 하려면 프로젝트 폴더를 만드는 경우 (예: `-o BlazorSample`) 경로를 사용 하 여 명령에 출력 옵션을 포함 합니다 (예:).</span><span class="sxs-lookup"><span data-stu-id="6e5d3-105">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="6e5d3-106">또한 폴더 이름은 프로젝트 이름의 일부가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6e5d3-106">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="6e5d3-107">Visual Studio에서 [Blazor Weasembomapp을 만듭니다](xref:blazor/get-started).</span><span class="sxs-lookup"><span data-stu-id="6e5d3-107">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="6e5d3-108">**사용자 계정을 앱에 저장** 옵션을 사용 하 여 **개별 사용자 계정** 에 대 한 **인증** 을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="6e5d3-108">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="6e5d3-109">인증 패키지</span><span class="sxs-lookup"><span data-stu-id="6e5d3-109">Authentication package</span></span>

<span data-ttu-id="6e5d3-110">개별 사용자 계정을 사용 하도록 앱을 만들 때 앱은 앱의 프로젝트 파일에서 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 패키지에 대 한 패키지 참조를 자동으로 받습니다.</span><span class="sxs-lookup"><span data-stu-id="6e5d3-110">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="6e5d3-111">패키지는 앱이 사용자를 인증 하 고 토큰을 가져와서 보호 된 Api를 호출할 수 있도록 지 원하는 기본 형식 집합을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="6e5d3-111">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="6e5d3-112">앱에 인증을 추가 하는 경우 앱의 프로젝트 파일에 패키지를 수동으로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="6e5d3-112">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="6e5d3-113">이전 패키지 참조의 `{VERSION}`를 <xref:blazor/get-started> 문서에 표시 된 `Microsoft.AspNetCore.Blazor.Templates` 패키지의 버전으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="6e5d3-113">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="6e5d3-114">인증 서비스 지원</span><span class="sxs-lookup"><span data-stu-id="6e5d3-114">Authentication service support</span></span>

<span data-ttu-id="6e5d3-115">사용자 인증에 대 한 지원은 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 패키지에서 제공 하는 `AddOidcAuthentication` 확장 메서드를 사용 하 여 서비스 컨테이너에 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6e5d3-115">Support for authenticating users is registered in the service container with the `AddOidcAuthentication` extension method provided by the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="6e5d3-116">이 메서드는 앱이 IP (Id 공급자)와 상호 작용 하는 데 필요한 모든 서비스를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="6e5d3-116">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="6e5d3-117">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="6e5d3-117">*Program.cs*:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    options.ProviderOptions.Authority = "{AUTHORITY}";
    options.ProviderOptions.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="6e5d3-118">독립형 앱에 대 한 인증 지원은 OIDC (Open ID Connect)를 사용 하 여 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6e5d3-118">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="6e5d3-119">`AddOidcAuthentication` 메서드는 OIDC를 사용 하 여 앱을 인증 하는 데 필요한 매개 변수를 구성 하는 콜백을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="6e5d3-119">The `AddOidcAuthentication` method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="6e5d3-120">앱을 구성 하는 데 필요한 값은 Google, Microsoft 또는 기타 OIDC 규격 공급자와 같은 IP에서 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6e5d3-120">The values required for configuring the app can be obtained from the IP, such as Google, Microsoft, or other OIDC-compliant provider.</span></span> <span data-ttu-id="6e5d3-121">앱을 등록 하면 일반적으로 온라인 포털에서 발생 하는 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="6e5d3-121">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="index-page"></a><span data-ttu-id="6e5d3-122">인덱스 페이지</span><span class="sxs-lookup"><span data-stu-id="6e5d3-122">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

## <a name="app-component"></a><span data-ttu-id="6e5d3-123">앱 구성 요소</span><span class="sxs-lookup"><span data-stu-id="6e5d3-123">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="6e5d3-124">RedirectToLogin 구성 요소</span><span class="sxs-lookup"><span data-stu-id="6e5d3-124">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="6e5d3-125">LoginDisplay 구성 요소</span><span class="sxs-lookup"><span data-stu-id="6e5d3-125">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="6e5d3-126">인증 구성 요소</span><span class="sxs-lookup"><span data-stu-id="6e5d3-126">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]
