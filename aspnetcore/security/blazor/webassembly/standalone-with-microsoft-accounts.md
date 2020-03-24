---
title: Microsoft 계정을 사용 하 여 ASP.NET Core Blazor Weasembomamboma 독립 실행형 앱 보호
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: be73bec971f96bd64afc735a1ea750d47c7bc383
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80219261"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a>Microsoft 계정을 사용 하 여 ASP.NET Core Blazor Weasembomamboma 독립 실행형 앱 보호

[Javier Calvarro e](https://github.com/javiercn) 및 [Luke latham 문자](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

인증을 위해 [AAD (Azure Active Directory)에서 Microsoft 계정을](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) 사용 하는 Blazor Weasembom독립형 앱을 만들려면 다음을 수행 합니다.

1. [AAD 테 넌 트 및 웹 응용 프로그램 만들기](/azure/active-directory/develop/v2-overview)

   Azure Portal의 **Azure Active Directory** > **앱 등록** 영역에서 AAD 앱을 등록 합니다.

   1\. 앱에 대 한 **이름** (예: **Blazor Client AAD**)을 제공 합니다.<br>
   2\. **지원 되는 계정 유형**에서 **조직 디렉터리의 계정**을 선택 합니다.<br>
   3\. **리디렉션 uri** 드롭다운을 **웹**으로 설정 된 상태로 두고 `https://localhost:5001/authentication/login-callback`의 리디렉션 uri를 제공 합니다.<br>
   4\. **권한을** 사용 하지 않도록 설정 > **관리자 동의 하도록 요구에 게 openid connect 및 offline_access 권한 부여 확인란을** 선택 합니다.<br>
   5\. **등록**을 선택합니다.

   **웹** > **인증** > **플랫폼 구성** 에서 다음을 수행 합니다.

   1\. `https://localhost:5001/authentication/login-callback`의 **리디렉션 URI** 가 있는지 확인 합니다.<br>
   2\. **암시적 권한 부여**의 경우 **액세스 토큰** 및 **ID 토큰**에 대 한 확인란을 선택 합니다.<br>
   3\. 앱에 대 한 나머지 기본값은이 환경에 사용할 수 있습니다.<br>
   4\. **저장** 단추를 선택합니다.

   응용 프로그램 ID (클라이언트 ID)를 기록 합니다 (예: `11111111-1111-1111-1111-111111111111`).

1. 다음 명령에서 자리 표시자를 앞에서 기록한 정보로 바꾸고 명령 셸에서 명령을 실행 합니다.

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   출력 위치를 지정 하려면 프로젝트 폴더를 만드는 경우 (예: `-o BlazorSample`) 경로를 사용 하 여 명령에 출력 옵션을 포함 합니다 (예:). 또한 폴더 이름은 프로젝트 이름의 일부가 됩니다.

앱을 만든 후 다음을 수행할 수 있습니다.

* Microsoft 계정을 사용 하 여 앱에 로그인 합니다.
* 앱을 올바르게 구성 하는 경우 독립 실행형 Blazor 앱과 동일한 방식으로 Microsoft Api에 대 한 액세스 토큰을 요청 합니다. 자세한 내용은 [빠른 시작: 웹 api를 노출 하도록 응용 프로그램 구성](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)을 참조 하세요.

## <a name="authentication-package"></a>인증 패키지

회사 또는 학교 계정 (`SingleOrg`)을 사용 하도록 앱을 만들 때 앱은 [Microsoft 인증 라이브러리](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`)에 대 한 패키지 참조를 자동으로 받습니다. 패키지는 앱이 사용자를 인증 하 고 토큰을 가져와서 보호 된 Api를 호출할 수 있도록 지 원하는 기본 형식 집합을 제공 합니다.

앱에 인증을 추가 하는 경우 앱의 프로젝트 파일에 패키지를 수동으로 추가 합니다.

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

이전 패키지 참조의 `{VERSION}`를 <xref:blazor/get-started> 문서에 표시 된 `Microsoft.AspNetCore.Blazor.Templates` 패키지의 버전으로 바꿉니다.

`Microsoft.Authentication.WebAssembly.Msal` 패키지는 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 패키지를 앱에 전이적으로 추가 합니다.

## <a name="authentication-service-support"></a>인증 서비스 지원

사용자 인증에 대 한 지원은 `Microsoft.Authentication.WebAssembly.Msal` 패키지에서 제공 하는 `AddMsalAuthentication` 확장 메서드를 사용 하 여 서비스 컨테이너에 등록 됩니다. 이 메서드는 앱이 IP (Id 공급자)와 상호 작용 하는 데 필요한 모든 서비스를 설정 합니다.

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

`AddMsalAuthentication` 메서드는 콜백을 허용 하 여 앱을 인증 하는 데 필요한 매개 변수를 구성 합니다. 앱을 구성 하는 데 필요한 값은 앱을 등록할 때 Microsoft 계정 구성에서 가져올 수 있습니다.

## <a name="index-page"></a>인덱스 페이지

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a>앱 구성 요소

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>RedirectToLogin 구성 요소

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>LoginDisplay 구성 요소

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a>인증 구성 요소

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>추가 리소스

* [빠른 시작: Microsoft id 플랫폼을 사용 하 여 응용 프로그램 등록](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [빠른 시작: 웹 Api를 노출 하도록 응용 프로그램 구성](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
