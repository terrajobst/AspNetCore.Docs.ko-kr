---
title: 인증 라이브러리를 사용 하 여 ASP.NET Core Blazor Weasembomoma 독립 실행형 앱 보호
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: ea50d94835b044f9c3d6a0561868f081d32cb62a
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80219009"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-the-authentication-library"></a>인증 라이브러리를 사용 하 여 ASP.NET Core Blazor Weasembomoma 독립 실행형 앱 보호

[Javier Calvarro e](https://github.com/javiercn) 및 [Luke latham 문자](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

*AAD (Azure Active Directory) 및 Azure Active Directory B2C (AAD B2C)의 경우이 항목의 지침을 따르세요. 이 목차 노드의 AAD 및 AAD B2C 항목을 참조 하세요.*

`Microsoft.AspNetCore.Components.WebAssembly.Authentication` 라이브러리를 사용 하는 Blazor Weasembomom독립형 앱을 만들려면 명령 셸에서 다음 명령을 실행 합니다.

```dotnetcli
dotnet new blazorwasm -au Individual
```

출력 위치를 지정 하려면 프로젝트 폴더를 만드는 경우 (예: `-o BlazorSample`) 경로를 사용 하 여 명령에 출력 옵션을 포함 합니다 (예:). 또한 폴더 이름은 프로젝트 이름의 일부가 됩니다.

Visual Studio에서 [Blazor Weasembomapp을 만듭니다](xref:blazor/get-started). **사용자 계정을 앱에 저장** 옵션을 사용 하 여 **개별 사용자 계정** 에 대 한 **인증** 을 설정 합니다.

## <a name="authentication-package"></a>인증 패키지

개별 사용자 계정을 사용 하도록 앱을 만들 때 앱은 앱의 프로젝트 파일에서 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 패키지에 대 한 패키지 참조를 자동으로 받습니다. 패키지는 앱이 사용자를 인증 하 고 토큰을 가져와서 보호 된 Api를 호출할 수 있도록 지 원하는 기본 형식 집합을 제공 합니다.

앱에 인증을 추가 하는 경우 앱의 프로젝트 파일에 패키지를 수동으로 추가 합니다.

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

이전 패키지 참조의 `{VERSION}`를 <xref:blazor/get-started> 문서에 표시 된 `Microsoft.AspNetCore.Blazor.Templates` 패키지의 버전으로 바꿉니다.

## <a name="authentication-service-support"></a>인증 서비스 지원

사용자 인증에 대 한 지원은 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 패키지에서 제공 하는 `AddOidcAuthentication` 확장 메서드를 사용 하 여 서비스 컨테이너에 등록 됩니다. 이 메서드는 앱이 IP (Id 공급자)와 상호 작용 하는 데 필요한 모든 서비스를 설정 합니다.

*Program.cs*:

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    options.ProviderOptions.Authority = "{AUTHORITY}";
    options.ProviderOptions.ClientId = "{CLIENT ID}";
});
```

독립형 앱에 대 한 인증 지원은 OIDC (Open ID Connect)를 사용 하 여 제공 됩니다. `AddOidcAuthentication` 메서드는 OIDC를 사용 하 여 앱을 인증 하는 데 필요한 매개 변수를 구성 하는 콜백을 허용 합니다. 앱을 구성 하는 데 필요한 값은 OIDC 규격 IP에서 가져올 수 있습니다. 앱을 등록 하면 일반적으로 온라인 포털에서 발생 하는 값을 가져옵니다.

## <a name="index-page"></a>인덱스 페이지

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a>앱 구성 요소

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>RedirectToLogin 구성 요소

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>LoginDisplay 구성 요소

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a>인증 구성 요소

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]
