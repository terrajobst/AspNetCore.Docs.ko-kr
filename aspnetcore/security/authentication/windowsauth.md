---
title: ASP.NET Core에서 Windows 인증을 구성 합니다.
author: scottaddie
description: ASP.NET Core에서 HTTP.sys 및 IIS에 대 한 Windows 인증을 구성 하는 방법에 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 06/12/2019
uid: security/authentication/windowsauth
ms.openlocfilehash: 93f833adff95f25d570947cd1a9035d652f522c2
ms.sourcegitcommit: 335a88c1b6e7f0caa8a3a27db57c56664d676d34
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/12/2019
ms.locfileid: "67034956"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a>ASP.NET Core에서 Windows 인증을 구성 합니다.

하 여 [Scott Addie](https://twitter.com/Scott_Addie) 고 [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-3.0"

사용 하 여 호스팅되는 ASP.NET Core 앱에 대 한 Windows 인증 (라고도: Negotiate, Kerberos 또는 NTLM 인증)를 구성할 수 있습니다 [IIS](xref:host-and-deploy/iis/index)하십시오 [Kestrel](xref:fundamentals/servers/kestrel), 또는 [HTTP.sys](xref:fundamentals/servers/httpsys) .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

사용 하 여 호스팅되는 ASP.NET Core 앱에 대 한 Windows 인증 (라고도: Negotiate, Kerberos 또는 NTLM 인증)를 구성할 수 있습니다 [IIS](xref:host-and-deploy/iis/index) 하거나 [HTTP.sys](xref:fundamentals/servers/httpsys)합니다.

::: moniker-end

Windows 인증은 ASP.NET Core 앱의 사용자를 인증 하는 운영 체제에서 사용 합니다. 서버는 Windows 계정 또는 Active Directory 도메인 id를 사용 하 여 사용자를 식별 하는 회사 네트워크에서 실행 될 때 Windows 인증을 사용할 수 있습니다. Windows 인증은 인트라넷 환경 사용자, 클라이언트 앱 및 웹 서버는 동일한 Windows 도메인에 속해야 하는 위치에 가장 적합 합니다.

> [!NOTE]
> HTTP/2를 사용 하 여 Windows 인증이 지원 되지 않습니다. HTTP/2 응답에서 인증 질문을 보낼 수 있습니다 하지만 인증 하기 전에 클라이언트 HTTP/1.1로 다운 그레이드 해야 합니다.

## <a name="iisiis-express"></a>IIS/IIS Express

인증 서비스를 호출 하 여 추가 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> 네임 스페이스)에서 `Startup.ConfigureServices`:

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

### <a name="launch-settings-debugger"></a>시작 설정 (디버거)

시작 설정에 대 한 구성에만 적용 합니다 *Properties/launchSettings.json* IIS Express에 대 한 파일을 IIS에 대 한 Windows 인증을 구성 하지 않습니다. 서버 구성에 설명 되어는 [IIS](#iis) 섹션입니다.

합니다 **웹 응용 프로그램** Visual Studio 또는.NET Core CLI를 통해 사용할 수 있는 템플릿을 업데이트 하는 Windows 인증을 지원 하도록 구성할 수 있습니다 합니다 *Properties/launchSettings.json* 파일 자동으로 합니다.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

**새 프로젝트**

1. 새 프로젝트를 만듭니다.
1. **새 ASP.NET Core 웹 응용 프로그램**을 선택합니다. **새로 만들기**를 선택합니다.
1. 에 이름을 제공 합니다 **프로젝트 이름** 필드입니다. 확인 합니다 **위치** 항목이 올바른 또는 프로젝트의 위치를 지정 합니다. **만들기**를 선택합니다.
1. 선택 **변경** 아래에서 **인증**합니다.
1. 에 **인증 변경** 창에서 **Windows 인증**합니다. **확인**을 선택합니다.
1. **웹 애플리케이션**을 선택합니다.
1. **만들기**를 선택합니다.

앱을 실행합니다. 렌더링 된 앱의 사용자 인터페이스에 사용자 이름이 표시 됩니다.

**기존 프로젝트**

Windows 인증을 사용 하도록 설정 하 고 익명 인증을 사용 하지 않도록 설정 하는 프로젝트의 속성:

1. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.
1. **디버그** 탭을 선택합니다.
1. 에 대 한 확인란의 선택을 취소 **익명 인증 사용**합니다.
1. 에 대 한 확인란을 선택 **Windows 인증 사용**합니다.
1. 저장 하 고 속성 페이지를 닫습니다.

속성에서 구성할 수 있습니다 또는 합니다 `iisSettings` 의 노드는 *launchSettings.json* 파일:

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[Visual Studio Code/.NET Core CLI](#tab/visual-studio-code+netcore-cli)

**새 프로젝트**

실행 합니다 [새 dotnet](/dotnet/core/tools/dotnet-new) 명령에 `webapp` 인수 (ASP.NET Core 웹 앱) 및 `--auth Windows` 전환:

```console
dotnet new webapp --auth Windows
```

**기존 프로젝트**

업데이트를 `iisSettings` 의 노드는 *launchSettings.json* 파일:

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

---

기존 프로젝트를 수정 하는 경우 프로젝트 파일에 대 한 패키지 참조를 포함 되어 있는지 확인 합니다 [Microsoft.AspNetCore.App 메타 패키지](xref:fundamentals/metapackage-app) **하거나** 는 [ Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet 패키지.

### <a name="iis"></a>IIS

IIS에서 사용 하 여 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module) ASP.NET Core 앱을 호스트 하 합니다. Windows 인증을 통해 IIS에 대해 구성 된 합니다 *web.config* 파일입니다. 다음 섹션은 표시 하는 방법.

* 로컬 제공할 *web.config* 앱을 배포할 때 서버에서 Windows 인증을 활성화 하는 파일입니다.
* IIS 관리자를 사용 하 여 구성 하는 *web.config* 서버에 이미 배포 된 ASP.NET Core 앱의 파일입니다.

이미 않았다면 IIS을 사용 하 여 ASP.NET Core 앱을 호스트할 수 있습니다. 자세한 내용은 <xref:host-and-deploy/iis/index>을 참조하세요.

Windows 인증을 위해 IIS 역할 서비스를 사용 하도록 설정 합니다. 자세한 내용은 [IIS 역할 서비스 (2 단계 참조)에서 Windows 인증 사용](xref:host-and-deploy/iis/index#iis-configuration)합니다.

[IIS 통합 미들웨어](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) 기본적으로 자동으로 요청을 인증 하도록 구성 됩니다. 자세한 내용은 참조 하세요. [IIS 사용 하 여 Windows에서 ASP.NET Core 호스팅. IIS 옵션 (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options)합니다.

ASP.NET Core 모듈은 기본적으로 앱에 Windows 인증 토큰을 전달 하도록 구성 됩니다. 자세한 내용은 참조 하세요. [ASP.NET Core 모듈 구성 참조: AspNetCore 요소의 특성](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)합니다.

사용 하 여 **하거나** 다음 방법 중:

* **게시 하 고 프로젝트를 배포 하기 전에** 다음을 추가 합니다 *web.config* 프로젝트 루트에 파일:

  [!code-xml[](windowsauth/sample_snapshot/web_2.config)]

  .NET Core SDK에 의해 프로젝트를 게시 하는 경우 (없이 `<IsTransformWebConfigDisabled>` 속성이로 설정 `true` 프로젝트 파일에서), 게시 된 *web.config* 파일에는 `<location><system.webServer><security><authentication>` 섹션입니다. 에 대 한 자세한 합니다 `<IsTransformWebConfigDisabled>` 속성 참조 <xref:host-and-deploy/iis/index#webconfig-file>합니다.

* **게시 및 프로젝트를 배포한 후** IIS 관리자를 사용 하 여 서버 쪽 구성 작업을 수행 합니다.

  1. IIS 관리자에서 IIS 사이트를 선택 합니다 **사이트** 노드의 합니다 **연결** 사이드바 합니다.
  1. 두 번 클릭 **Authentication** 에 **IIS** 영역입니다.
  1. 선택 **익명 인증**합니다. 선택 **사용 안 함** 에 **작업** 사이드바 합니다.
  1. **Windows 인증**을 선택합니다. 선택 **사용 하도록 설정** 에 **작업** 보충 합니다.

  이러한 조치로, IIS 관리자 앱의 수정 *web.config* 파일입니다. A `<system.webServer><security><authentication>` 에 대 한 업데이트 된 설정으로 노드가 추가 됩니다 `anonymousAuthentication` 고 `windowsAuthentication`:

  [!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

  `<system.webServer>` 섹션에 추가 합니다 *web.config* IIS 관리자에 의해 파일이 앱의 외부 `<location>` 섹션에서는 앱을 게시할 때.NET Core SDK에 의해 추가 합니다. 섹션 외부의 추가 되기 때문에 합니다 `<location>` 노드를 설정에서 상속 됩니다 [하위 앱](xref:host-and-deploy/iis/index#sub-applications) 현재 앱. 상속을 방지 하려면 추가 이동 `<security>` 섹션 내에 `<location><system.webServer>` .NET Core SDK를 제공 하는 섹션입니다.

  앱에만 적용 IIS 구성을 추가 하려면 IIS 관리자를 사용 하면 *web.config* 서버의 파일입니다. 앱의 후속 배포 하는 경우 서버에서 설정을 덮어쓸 수 있습니다 서버의 복사본 *web.config* 프로젝트의 바뀝니다 *web.config* 파일입니다. 사용 하 여 **하거나** 설정을 관리 하려면 다음 방법 중:

  * 설정을 다시 설정 하려면 IIS 관리자를 사용 합니다 *web.config* 배포에서 파일을 덮어쓸지 후 파일입니다.
  * 추가 된 *web.config 파일* 설정 사용 하 여 로컬 앱.

::: moniker range=">= aspnetcore-3.0"

## <a name="kestrel"></a>Kestrel

 합니다 [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) NuGet 패키지 사용 될 수 있습니다 [Kestrel](xref:fundamentals/servers/kestrel) Negotiate, Kerberos 및 NTLM을 사용 하 여 Windows, Linux 및 macOS에서 Windows 인증을 지원 하도록 합니다.

> [!WARNING]
> 연결에 대 한 요청에서 자격 증명을 유지할 수 있습니다. *협상 프록시 Kestrel 사용 하 여 1:1 연결 선호도 (영구 연결)를 유지 하지 않는 인증 프록시를 사용 하 여 사용 하면 안 됩니다.* 즉, Negotiate 인증을 IIS 뒤에 있는 Kestrel을 사용 하 여 사용할 해야 [ancm (ASP.NET Core 모듈은) out of process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)합니다.

 인증 서비스를 호출 하 여 추가 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (`Microsoft.AspNetCore.Authentication.Negotiate` 네임 스페이스) 및 `AddNegotitate` (`Microsoft.AspNetCore.Authentication.Negotiate` 네임 스페이스)에서 `Startup.ConfigureServices`:

 ```csharp
services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
    .AddNegotiate();
```

호출 하 여 인증 미들웨어를 추가할 <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> 에서 `Startup.Configure`:

 ```csharp
app.UseAuthentication();

app.UseMvc();
```

미들웨어에 대 한 자세한 내용은 참조 하세요. <xref:fundamentals/middleware/index>합니다.

익명 요청 허용 됩니다. 사용 하 여 [ASP.NET Core 권한 부여](xref:security/authorization/introduction) 인증에 대 한 익명 요청 수입니다.

### <a name="windows-environment-configuration"></a>Windows 환경 구성

합니다 [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) 사용자 모드 인증을 수행 하는 구성 요소입니다. 서비스 사용자 이름 (Spn) 컴퓨터 계정이 아닌 서비스를 실행 하는 사용자 계정에 추가 되어야 합니다. 실행 `setspn -S HTTP/mysrevername.mydomain.com myuser` 관리 명령 셸에서 합니다.

### <a name="linux-and-macos-environment-configuration"></a>Linux 및 macOS 환경 구성

Linux 또는 macOS 컴퓨터를 Windows 도메인 가입에 대 한 지침은 합니다 [Kerberos Windows 인증을 사용 하 여 SQL Server를 Azure Data Studio 연결](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) 문서. 지침을 도메인에 Linux 컴퓨터에 대 한 컴퓨터 계정을 만듭니다. 해당 컴퓨터 계정에 Spn은 추가 해야 합니다.

> [!NOTE]
> 지침에 따라 하는 경우는 [Azure Data Studio Kerberos Windows 인증을 사용 하 여 SQL Server에 연결](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) 문서를 교체 `python-software-properties` 사용 하 여 `python3-software-properties` 필요한 경우.

제공 하는 데 필요한 추가 단계는 Linux 또는 macOS 컴퓨터에 도메인에 가입 되 면을 [keytab 파일](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) Spn을 사용 하 여:

* 도메인 컨트롤러에서 컴퓨터 계정에 새 웹 서비스 Spn을 추가 합니다.
  * `setspn -S HTTP/mywebservice.mydomain.com mymachine`
  * `setspn -S HTTP/mywebservice@MYDOMAIN.COM mymachine`
* 사용 하 여 [ktpass](/windows-server/administration/windows-commands/ktpass) keytab 파일을 생성 하려면:
  * `ktpass -princ HTTP/mywebservice.mydomain.com@MYDOMAIN.COM -pass myKeyTabFilePassword -mapuser MYDOMAIN\mymachine$ -pType KRB5_NT_PRINCIPAL -out c:\temp\mymachine.HTTP.keytab -crypto AES256-SHA1`
  * 표시 된 대로 대문자로에서 일부 필드를 지정 되어야 합니다.
* Linux 또는 macOS 컴퓨터에 키 파일을 복사 합니다.
* 환경 변수를 통해 keytab 파일을 선택 합니다. `export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`
* 호출 `klist` 현재 사용 가능한 Spn을 표시 합니다.

> [!NOTE]
> Keytab 파일을 도메인 액세스 자격 증명을 포함 하 고 적절 하 게 보호 되어야 합니다.

::: moniker-end

## <a name="httpsys"></a>HTTP.sys

[HTTP.sys](xref:fundamentals/servers/httpsys) 협상, NTLM 또는 기본 인증을 사용 하 여 커널 모드 Windows 인증을 지원 합니다.

인증 서비스를 호출 하 여 추가 <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> 네임 스페이스)에서 `Startup.ConfigureServices`:

```csharp
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

Windows 인증을 사용 하 여 HTTP.sys를 사용 하도록 앱의 웹 호스트 구성 (*Program.cs*). <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 에 <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> 네임 스페이스입니다.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_GenericHost.cs?highlight=13-19)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_WebHost.cs?highlight=9-15)]

::: moniker-end

> [!NOTE]
> HTTP.sys는 Kerberos 인증 프로토콜을 사용하여 커널 모드 인증에 위임합니다. 사용자 모드 인증은 Kerberos 및 HTTP.sys로 지원되지 않습니다. 머신 계정은 Active Directory에서 가져온 Kerberos 토큰/티켓의 암호를 해독하는 데 사용되고 사용자를 인증하는 서버에 클라이언트에 의해 전달되어야 합니다. 앱의 사용자가 아닌 호스트에 대해 SPN(서비스 사용자 이름)을 등록합니다.

> [!NOTE]
> HTTP.sys는 Nano Server 버전 1709 이상에서 지원 되지 않습니다. Nano Server를 사용 하 여 Windows 인증 및 HTTP.sys를 사용 하려면 사용 된 [Server Core (microsoft/windowsservercore) 컨테이너](https://hub.docker.com/r/microsoft/windowsservercore/)합니다. Server Core에 대 한 자세한 내용은 참조 하세요. [Windows Server의 Server Core 설치 옵션 이란?](/windows-server/administration/server-core/what-is-server-core)합니다.

## <a name="authorize-users"></a>사용자 권한 부여

익명 액세스 구성 상태는 방식을 결정 합니다 `[Authorize]` 및 `[AllowAnonymous]` 특성은 앱에서 사용 합니다. 다음 두 섹션에는 익명 액세스는 허용 되지 않는 및 허용 된 구성 상태를 처리 하는 방법을 설명 합니다.

### <a name="disallow-anonymous-access"></a>익명 액세스 허용 안 함

Windows 인증을 사용 하 고 익명 액세스가 비활성화 되어는 `[Authorize]` 고 `[AllowAnonymous]` 특성은 효과가 없습니다. IIS 사이트를 익명 액세스를 허용 하지 않도록 구성 된 경우 요청에 하지 앱에 도달 합니다. 이러한 이유로 `[AllowAnonymous]` 특성 적용 되지 않습니다.

### <a name="allow-anonymous-access"></a>익명 액세스 허용

Windows 인증 및 익명 액세스를 모두 설정 된 경우 사용 합니다 `[Authorize]` 및 `[AllowAnonymous]` 특성입니다. `[Authorize]` 특성을 사용 하면 앱의 인증을 요구 하는 끝점을 보호할 수 있습니다. `[AllowAnonymous]` 재정의 특성의 `[Authorize]` 익명 액세스를 허용 하는 앱의 특성입니다. 특성 사용 세부 정보를 참조 하세요. <xref:security/authorization/simple>합니다.

> [!NOTE]
> 기본적으로 권한 부여를 페이지에 액세스할 수 없는 사용자는 빈 HTTP 403 응답으로 표시 됩니다. 합니다 [StatusCodePages 미들웨어](xref:fundamentals/error-handling#usestatuscodepages) "액세스 거부" 더 나은 환경을 제공 하기 위해 구성할 수 있습니다.

## <a name="impersonation"></a>가장

Asp.net 가장을 구현 하지 않습니다. 앱 풀 또는 프로세스 id를 사용 하 여 모든 요청에 대해 앱의 id를 사용 하 여 앱이 실행 됩니다. 앱 사용자를 대신 하 여 작업을 수행 해야 하는 경우 사용 하 여 [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) 에 [터미널 인라인 미들웨어](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) 에서 `Startup.Configure`합니다. 이 컨텍스트에서 단일 작업을 실행 하 고 컨텍스트를 닫습니다.

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

`RunImpersonated` 비동기 작업을 지원 하지 않으며 복잡 한 시나리오에 사용할 수 없습니다. 예를 들어 전체 요청 또는 미들웨어 체인 래핑 지원 없거나 것이 좋습니다.

::: moniker range=">= aspnetcore-3.0"

동안 합니다 [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) 패키지를 사용 하면 Windows에서 인증, Linux 및 macOS의 경우 가장은 Windows 에서만 지원 됩니다.

::: moniker-end

## <a name="claims-transformations"></a>클레임 변환

IIS에서 프로세스 모드를 사용 하 여 호스팅하는 경우 <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> 사용자를 초기화 하기 위해 내부적으로 호출 되지 않습니다. 따라서 모든 인증 후에 클레임을 변환하는 데 사용되는 <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> 구현은 기본적으로 활성화되지 않습니다. In process를 호스트 하는 경우 클레임 변환을 활성화 하는 코드 예제 및 자세한 내용은 참조 하세요. <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>합니다.

## <a name="additional-resources"></a>추가 자료

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>
