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
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-the-authentication-library"></a>인증 라이브러리를 Blazor 통해 ASP.NET 핵심 웹어셈블리 독립 실행형 앱 보호

[하비에르 칼바로 넬슨과](https://github.com/javiercn) [루크 라담](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

*Azure Active Directory(AAD) 및 Azure Active Directory B2C(AAD B2C)의 경우 이 항목의 지침을 따르지 마십시오. 이 목회 자 노드의 AAD 및 AAD B2C 항목을 참조하십시오.*

라이브러리를 Blazor 사용하는 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` WebAssembly 독립 실행형 앱을 만들려면 명령 셸에서 다음 명령을 실행합니다.

```dotnetcli
dotnet new blazorwasm -au Individual
```

프로젝트 폴더가 없는 경우 만드는 출력 위치를 지정하려면 경로가 있는 명령에 출력 옵션을 포함합니다(예: `-o BlazorSample`). 폴더 이름도 프로젝트 이름의 일부가 됩니다.

비주얼 스튜디오에서 [ Blazor 웹 어셈블리 응용 프로그램을 만듭니다.](xref:blazor/get-started) 스토어 사용자 계정 **인앱** 옵션을 사용하여 개별 사용자 **계정으로** **인증을** 설정합니다.

## <a name="authentication-package"></a>인증 패키지

개별 사용자 계정을 사용하도록 앱을 만들면 앱은 앱의 프로젝트 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 파일에 있는 패키지에 대한 패키지 참조를 자동으로 받습니다. 이 패키지는 앱이 사용자를 인증하고 보호된 API를 호출하는 토큰을 얻는 데 도움이 되는 기본 요소 집합을 제공합니다.

앱에 인증을 추가하는 경우 앱의 프로젝트 파일에 패키지를 수동으로 추가합니다.

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

앞의 패키지 참조에서 문서에 표시된 `{VERSION}` `Microsoft.AspNetCore.Blazor.Templates` 패키지 버전으로 <xref:blazor/get-started> 바꿉습니다.

## <a name="authentication-service-support"></a>인증 서비스 지원

사용자 인증에 대한 지원은 패키지에서 제공하는 `AddOidcAuthentication` 확장 방법으로 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 서비스 컨테이너에 등록됩니다. 이 메서드는 앱이 IP(ID 공급자)와 상호 작용하는 데 필요한 모든 서비스를 설정합니다.

*Program.cs*:

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    options.ProviderOptions.Authority = "{AUTHORITY}";
    options.ProviderOptions.ClientId = "{CLIENT ID}";
});
```

독립 실행형 앱에 대한 인증 지원은 개방형 ID 연결(OIDC)을 사용하여 제공됩니다. 이 `AddOidcAuthentication` 메서드는 OIDC를 사용하여 앱을 인증하는 데 필요한 매개 변수를 구성하는 콜백을 허용합니다. 응용 프로그램을 구성하는 데 필요한 값은 OIDC 호환 IP에서 얻을 수 있습니다. 일반적으로 온라인 포털에서 발생하는 앱을 등록할 때 값을 가져옵니다.

## <a name="access-token-scopes"></a>토큰 범위에 액세스

WebAssembly 템플릿은 Blazor 보안 API에 대한 액세스 토큰을 요청하도록 앱을 자동으로 구성하지 않습니다. 로그인 흐름의 일부로 토큰을 프로비전하려면 `OidcProviderOptions`다음의 기본 토큰 범위에 범위를 추가합니다.

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> Azure 포털이 범위 URI를 제공하고 API에서 *401 무단* 응답을 받을 때 **앱이 처리되지 않은 예외를 throw하는** 경우 구성표 및 호스트를 포함하지 않는 범위 URI를 사용해 보십시오. 예를 들어 Azure 포털은 다음 범위 URI 형식 중 하나를 제공할 수 있습니다.
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> 스키마 및 호스트 없이 범위 URI를 제공합니다.
>
> ```csharp
> options.ProviderOptions.DefaultScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

자세한 내용은 <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>을 참조하세요.

## <a name="imports-file"></a>파일 가져오기

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>인덱스 페이지

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a>앱 구성 요소

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>리디렉션토로그인 구성 요소

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>로그인표시 구성 요소

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a>인증 구성 요소

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>추가 리소스

* [추가 액세스 토큰 요청](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
