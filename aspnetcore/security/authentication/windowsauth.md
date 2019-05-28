---
title: ASP.NET Core에서 Windows 인증을 구성 합니다.
author: scottaddie
description: ASP.NET core에서 IIS Express, IIS 및 HTTP.sys를 사용 하 여 Windows 인증을 구성 하는 방법에 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 04/03/2019
uid: security/authentication/windowsauth
ms.openlocfilehash: 46c9e054b6e9228a709f93c6b73772708f6c6eb0
ms.sourcegitcommit: b8ed594ab9f47fa32510574f3e1b210cff000967
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66251221"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a>ASP.NET Core에서 Windows 인증을 구성 합니다.

하 여 [Scott Addie](https://twitter.com/Scott_Addie) 고 [Luke Latham](https://github.com/guardrex)

[Windows 인증](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) 사용 하 여 호스팅되는 ASP.NET Core 앱에 대해 구성할 수 있습니다 [IIS](xref:host-and-deploy/iis/index) 하거나 [HTTP.sys](xref:fundamentals/servers/httpsys)합니다.

Windows 인증은 ASP.NET Core 앱의 사용자를 인증 하는 운영 체제에서 사용 합니다. 서버는 Windows 계정 또는 Active Directory 도메인 id를 사용 하 여 사용자를 식별 하는 회사 네트워크에서 실행 될 때 Windows 인증을 사용할 수 있습니다. Windows 인증은 인트라넷 환경 사용자, 클라이언트 앱 및 웹 서버는 동일한 Windows 도메인에 속해야 하는 위치에 가장 적합 합니다.

## <a name="enable-windows-authentication-in-an-aspnet-core-app"></a>ASP.NET Core 앱에서 Windows 인증을 사용 하도록 설정

합니다 **웹 응용 프로그램** Windows 인증을 지원 하도록 Visual Studio 또는.NET Core CLI를 통해 사용할 수 있는 템플릿을 구성할 수 있습니다.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="use-the-windows-authentication-app-template-for-a-new-project"></a>새 프로젝트를 위해 Windows 인증 앱 템플릿 사용

Visual Studio에서 다음을 수행합니다.

1. 새 프로젝트를 만듭니다.
1. **새 ASP.NET Core 웹 응용 프로그램**을 선택합니다. **새로 만들기**를 선택합니다.
1. 에 이름을 제공 합니다 **프로젝트 이름** 필드입니다. 확인 합니다 **위치** 항목이 올바른 또는 프로젝트의 위치를 지정 합니다. **만들기**를 선택합니다.
1. 선택 **변경** 아래에서 **인증**합니다.
1. 에 **인증 변경** 창에서 **Windows 인증**합니다. **확인**을 선택합니다.
1. **웹 애플리케이션**을 선택합니다.
1. **만들기**를 선택합니다.

앱을 실행합니다. 렌더링 된 앱의 사용자 인터페이스에 사용자 이름이 표시 됩니다.

### <a name="manual-configuration-for-an-existing-project"></a>기존 프로젝트에 대 한 수동 구성

프로젝트의 속성을 사용 하면 Windows 인증을 사용 하 여 익명 인증을 사용 하지 않도록 설정:

1. Visual Studio에서 프로젝트를 마우스 오른쪽 단추로 클릭 **솔루션 탐색기** 선택한 **속성**합니다.
1. **디버그** 탭을 선택합니다.
1. 에 대 한 확인란의 선택을 취소 **익명 인증 사용**합니다.
1. 에 대 한 확인란을 선택 **Windows 인증 사용**합니다.

속성에서 구성할 수 있습니다 또는 합니다 `iisSettings` 의 노드는 *launchSettings.json* 파일:

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

사용 된 **Windows 인증** 앱 템플릿.

실행 합니다 [새 dotnet](/dotnet/core/tools/dotnet-new) 명령에 `webapp` 인수 (ASP.NET Core 웹 앱) 및 `--auth Windows` 전환:

```console
dotnet new webapp --auth Windows
```

---

기존 프로젝트를 수정 하는 경우 프로젝트 파일에 대 한 패키지 참조를 포함 되어 있는지 확인 합니다 [Microsoft.AspNetCore.App 메타 패키지](xref:fundamentals/metapackage-app) **하거나** 는 [ Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet 패키지.

## <a name="enable-windows-authentication-with-iis"></a>IIS 사용 하 여 Windows 인증을 사용 하도록 설정

IIS에서 사용 하 여 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) ASP.NET Core 앱을 호스트 하 합니다. Windows 인증을 통해 IIS에 대해 구성 된 합니다 *web.config* 파일입니다. 다음 섹션은 표시 하는 방법.

* 로컬 제공할 *web.config* 앱을 배포할 때 서버에서 Windows 인증을 활성화 하는 파일입니다.
* IIS 관리자를 사용 하 여 구성 하는 *web.config* 서버에 이미 배포 된 ASP.NET Core 앱의 파일입니다.

### <a name="iis-configuration"></a>IIS 구성

이미 않았다면 IIS을 사용 하 여 ASP.NET Core 앱을 호스트할 수 있습니다. 자세한 내용은 <xref:host-and-deploy/iis/index>을 참조하세요.

Windows 인증을 위해 IIS 역할 서비스를 사용 하도록 설정 합니다. 자세한 내용은 [IIS 역할 서비스 (2 단계 참조)에서 Windows 인증 사용](xref:host-and-deploy/iis/index#iis-configuration)합니다.

[IIS 통합 미들웨어](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) 기본적으로 자동으로 요청을 인증 하도록 구성 됩니다. 자세한 내용은 참조 하세요. [IIS 사용 하 여 Windows에서 ASP.NET Core 호스팅. IIS 옵션 (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options)합니다.

ASP.NET Core 모듈은 기본적으로 앱에 Windows 인증 토큰을 전달 하도록 구성 됩니다. 자세한 내용은 참조 하세요. [ASP.NET Core 모듈 구성 참조: AspNetCore 요소의 특성](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)합니다.

### <a name="create-a-new-iis-site"></a>새 IIS 사이트 만들기

이름 및 폴더를 지정 하 고 새 응용 프로그램 풀을 만들 수 있도록 합니다.

### <a name="enable-windows-authentication-for-the-app-in-iis"></a>IIS에서 앱에 대 한 Windows 인증을 사용 하도록 설정

사용 하 여 **하거나** 다음 방법 중:

* [앱을 게시 하기 전에 개발 쪽 구성](#development-side-configuration-with-a-local-webconfig-file) (*권장*)
* [앱을 게시 한 후 서버 쪽 구성](#server-side-configuration-with-the-iis-manager)

#### <a name="development-side-configuration-with-a-local-webconfig-file"></a>로컬 web.config 파일을 사용 하 여 개발 쪽 구성

다음 단계를 수행 **하기 전에** 있습니다 [게시 하 고 프로젝트를 배포](#publish-and-deploy-your-project-to-the-iis-site-folder)합니다.

다음을 추가 합니다 *web.config* 프로젝트 루트에 파일:

[!code-xml[](windowsauth/sample_snapshot/web_2.config)]

프로젝트 SDK를 통해 게시 되는 경우 (없이 `<IsTransformWebConfigDisabled>` 속성이로 설정 `true` 프로젝트 파일에서), 게시 된 *web.config* 파일에는 `<location><system.webServer><security><authentication>` 섹션입니다. 에 대 한 자세한 합니다 `<IsTransformWebConfigDisabled>` 속성 참조 <xref:host-and-deploy/iis/index#webconfig-file>합니다.

#### <a name="server-side-configuration-with-the-iis-manager"></a>IIS 관리자를 사용 하 여 서버 쪽 구성

다음 단계를 수행 **한 후** 있습니다 [게시 하 고 프로젝트를 배포](#publish-and-deploy-your-project-to-the-iis-site-folder)합니다.

1. IIS 관리자에서 IIS 사이트를 선택 합니다 **사이트** 노드의 합니다 **연결** 사이드바 합니다.
1. 두 번 클릭 **Authentication** 에 **IIS** 영역입니다.
1. 선택 **익명 인증**합니다. 선택 **사용 안 함** 에 **작업** 사이드바 합니다.
1. **Windows 인증**을 선택합니다. 선택 **사용 하도록 설정** 에 **작업** 보충 합니다.

이러한 조치로, IIS 관리자 앱의 수정 *web.config* 파일입니다. A `<system.webServer><security><authentication>` 에 대 한 업데이트 된 설정으로 노드가 추가 됩니다 `anonymousAuthentication` 고 `windowsAuthentication`:

[!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

`<system.webServer>` 섹션에 추가 합니다 *web.config* IIS 관리자에 의해 파일이 앱의 외부 `<location>` 섹션에서는 앱을 게시할 때.NET Core SDK에 의해 추가 합니다. 섹션 외부의 추가 되기 때문에 합니다 `<location>` 노드를 설정에서 상속 됩니다 [하위 앱](xref:host-and-deploy/iis/index#sub-applications) 현재 앱. 상속을 방지 하려면 추가 이동 `<security>` 섹션 내에 `<location><system.webServer>` SDK에서 제공 하는 섹션입니다.

앱에만 적용 IIS 구성을 추가 하려면 IIS 관리자를 사용 하면 *web.config* 서버의 파일입니다. 앱의 후속 배포 하는 경우 서버에서 설정을 덮어쓸 수 있습니다 서버의 복사본 *web.config* 프로젝트의 바뀝니다 *web.config* 파일입니다. 사용 하 여 **하거나** 설정을 관리 하려면 다음 방법 중:

* 설정을 다시 설정 하려면 IIS 관리자를 사용 합니다 *web.config* 배포에서 파일을 덮어쓸지 후 파일입니다.
* 추가 된 *web.config 파일* 설정 사용 하 여 로컬 앱. 자세한 내용은 참조는 [개발 쪽 구성](#development-side-configuration-with-a-local-webconfig-file) 섹션입니다.

### <a name="publish-and-deploy-your-project-to-the-iis-site-folder"></a>게시 하 고 IIS 사이트 폴더를 프로젝트 배포

Visual Studio 또는.NET Core CLI를 사용 하 여, 게시 및 대상 폴더에 앱을 배포 합니다.

게시 및 배포에는 IIS를 사용 하 여 호스트에 대 한 자세한 내용은 다음 항목을 참조:

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>

Windows 인증이 작동을 확인 하려면 앱을 시작 합니다.

## <a name="enable-windows-authentication-with-httpsys"></a>HTTP.sys 사용 하 여 Windows 인증을 사용 하도록 설정

Kestrel은 Windows 인증을 지원 하지 않지만 사용할 수 있습니다 [HTTP.sys](xref:fundamentals/servers/httpsys) Windows에서 자체 호스팅된 시나리오를 지원 합니다. 다음 예제에서는 Windows 인증을 사용 하 여 HTTP.sys를 사용 하도록 앱의 웹 호스트를 구성 합니다.

[!code-csharp[](windowsauth/sample_snapshot/Program.cs?highlight=9-14)]

> [!NOTE]
> HTTP.sys는 Kerberos 인증 프로토콜을 사용하여 커널 모드 인증에 위임합니다. 사용자 모드 인증은 Kerberos 및 HTTP.sys로 지원되지 않습니다. 머신 계정은 Active Directory에서 가져온 Kerberos 토큰/티켓의 암호를 해독하는 데 사용되고 사용자를 인증하는 서버에 클라이언트에 의해 전달되어야 합니다. 앱의 사용자가 아닌 호스트에 대해 SPN(서비스 사용자 이름)을 등록합니다.

> [!NOTE]
> HTTP.sys는 Nano Server 버전 1709 이상에서 지원 되지 않습니다. Nano Server를 사용 하 여 Windows 인증 및 HTTP.sys를 사용 하려면 사용 된 [Server Core (microsoft/windowsservercore) 컨테이너](https://hub.docker.com/r/microsoft/windowsservercore/)합니다. Server Core에 대 한 자세한 내용은 참조 하세요. [Windows Server의 Server Core 설치 옵션 이란?](/windows-server/administration/server-core/what-is-server-core)합니다.

## <a name="work-with-windows-authentication"></a>Windows 인증 사용

익명 액세스 구성 상태는 방식을 결정 합니다 `[Authorize]` 및 `[AllowAnonymous]` 특성은 앱에서 사용 합니다. 다음 두 섹션에는 익명 액세스는 허용 되지 않는 및 허용 된 구성 상태를 처리 하는 방법을 설명 합니다.

### <a name="disallow-anonymous-access"></a>익명 액세스 허용 안 함

Windows 인증을 사용 하 고 익명 액세스가 비활성화 되어는 `[Authorize]` 고 `[AllowAnonymous]` 특성은 효과가 없습니다. IIS 사이트 (또는 HTTP.sys)를 익명 액세스를 허용 하도록 구성 하는 경우 요청에 하지 앱에 도달 합니다. 이러한 이유로 `[AllowAnonymous]` 특성 적용 되지 않습니다.

### <a name="allow-anonymous-access"></a>익명 액세스 허용

Windows 인증 및 익명 액세스를 모두 설정 된 경우 사용 합니다 `[Authorize]` 및 `[AllowAnonymous]` 특성입니다. `[Authorize]` 특성을 사용 하면 Windows 인증 필요 실제로 앱의 부분을 보호할 수 있습니다. 합니다 `[AllowAnonymous]` 재정의 특성 `[Authorize]` 특성 익명 액세스를 허용 하는 앱 내에서 사용 합니다. 참조 [단순 권한 부여](xref:security/authorization/simple) 자세한 특성 사용.

ASP.NET Core에서 2.x의 경우는 `[Authorize]` 특성 추가 구성이 필요 *Startup.cs* Windows 인증에 대 한 익명 요청 수입니다. 권장된 구성에 사용 중인 웹 서버에 따라 약간씩 달라 집니다.

> [!NOTE]
> 기본적으로 권한 부여를 페이지에 액세스할 수 없는 사용자는 빈 HTTP 403 응답으로 표시 됩니다. 합니다 [StatusCodePages 미들웨어](xref:fundamentals/error-handling#usestatuscodepages) "액세스 거부" 더 나은 환경을 제공 하기 위해 구성할 수 있습니다.

#### <a name="iis"></a>IIS

IIS를 사용 하는 경우 다음을 추가 하 여 `ConfigureServices` 메서드:

```csharp
// IISDefaults requires the following import:
// using Microsoft.AspNetCore.Server.IISIntegration;
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

#### <a name="httpsys"></a>HTTP.sys

HTTP.sys를 사용 하는 경우 다음을 추가 하 여 `ConfigureServices` 메서드:

```csharp
// HttpSysDefaults requires the following import:
// using Microsoft.AspNetCore.Server.HttpSys;
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

### <a name="impersonation"></a>가장

Asp.net 가장을 구현 하지 않습니다. 앱 풀 또는 프로세스 id를 사용 하 여 모든 요청에 대해 앱의 id를 사용 하 여 앱이 실행 됩니다. 사용 하 여 명시적으로 사용자를 대신 하 여 작업을 수행 해야 할 경우 [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) 에 [터미널 인라인 미들웨어](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) 에서 `Startup.Configure`합니다. 이 컨텍스트에서 단일 작업을 실행 하 고 컨텍스트를 닫습니다.

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

`RunImpersonated` 비동기 작업을 지원 하지 않으며 복잡 한 시나리오에 사용할 수 없습니다. 예를 들어 전체 요청 또는 미들웨어 체인 래핑 지원 없거나 것이 좋습니다.

### <a name="claims-transformations"></a>클레임 변환

IIS에서 프로세스 모드를 사용 하 여 호스팅하는 경우 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> 사용자를 초기화 하기 위해 내부적으로 호출 되지 않습니다. 따라서 모든 인증 후에 클레임을 변환하는 데 사용되는 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 구현은 기본적으로 활성화되지 않습니다. In process를 호스트 하는 경우 클레임 변환을 활성화 하는 코드 예제 및 자세한 내용은 참조 하세요. <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>합니다.
