---
title: Windows 서비스에서 ASP.NET Core 호스트
author: guardrex
description: Windows 서비스에서 ASP.NET Core 앱을 호스트하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
uid: host-and-deploy/windows-service
ms.openlocfilehash: 32226c06ba005b4a61c473d6584b2b762733dcbd
ms.sourcegitcommit: 3d082bd46e9e00a3297ea0314582b1ed2abfa830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2019
ms.locfileid: "72007303"
---
# <a name="host-aspnet-core-in-a-windows-service"></a>Windows 서비스에서 ASP.NET Core 호스트

작성자: [Luke Latham](https://github.com/guardrex) 및 [Tom Dykstra](https://github.com/tdykstra)

IIS를 사용하지 않고 Windows에서 ASP.NET Core 앱을 [Windows 서비스](/dotnet/framework/windows-services/introduction-to-windows-service-applications)로 호스트할 수 있습니다. Windows 서비스로 호스트되는 앱은 서버 다시 부팅 후 자동으로 시작됩니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>전제 조건

* [ASP.NET Core SDK 2.1 이상](https://dotnet.microsoft.com/download)
* [PowerShell 6.2 이상](https://github.com/PowerShell/PowerShell)

::: moniker range=">= aspnetcore-3.0"

## <a name="worker-service-template"></a>Worker Service 템플릿

ASP.NET Core Worker Service 템플릿은 장기간 실행되는 서비스 앱을 작성하기 위한 시작점을 제공합니다. 템플릿을 Windows Service 앱의 기반으로 사용하려면 다음을 수행합니다.

1. .NET Core 템플릿에서 Worker Service 앱을 만듭니다.
1. [앱 구성](#app-configuration) 섹션의 지침에 따라 Windows Service로 실행할 수 있도록 Worker Service 앱을 업데이트합니다.

[!INCLUDE[](~/includes/worker-template-instructions.md)]

---

::: moniker-end

## <a name="app-configuration"></a>앱 구성

::: moniker range=">= aspnetcore-3.0"

[Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices) 패키지에서 제공된 `IHostBuilder.UseWindowsService`는 호스트를 빌드할 때 호출됩니다. 앱이 Windows 서비스로 실행되는 경우 이 메서드는 다음과 같이 합니다.

* 호스트 수명을 `WindowsServiceLifetime`으로 설정합니다.
* [콘텐츠 루트](xref:fundamentals/index#content-root)를 설정합니다.
* 애플리케이션 이름을 기본 소스 이름으로 사용하여 이벤트 로그에 대한 로깅을 사용하도록 설정합니다.
  * *appsettings.Production.json* 파일에서 `Logging:LogLevel:Default` 키를 사용하여 로그 수준을 구성할 수 있습니다.
  * 관리자만 새 이벤트 소스를 만들 수 있습니다. 애플리케이션 이름을 사용하여 이벤트 소스를 만들 수 없는 경우 *Application* 소스에 경고가 기록되고 이벤트 로그가 사용하지 않도록 설정됩니다.

[!code-csharp[](windows-service/samples/3.x/AspNetCoreService/Program.cs?name=snippet_Program)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

앱에 [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) 및 [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog)에 대한 패키지 참조가 필요합니다.

서비스 외부에서 실행할 때 테스트 및 디버그하려면 앱이 서비스 또는 콘솔 앱으로 실행되고 있는지 확인하는 코드를 추가합니다. 디버거가 연결되었는지 또는 `--console` 스위치가 있는지 검사합니다. 두 조건 중 하나라도 true이면(앱이 서비스로 실행되지 않음) <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>을 호출합니다. 두 조건이 모두 false이면(앱이 서비스로 실행됨) 다음을 수행합니다.

* <xref:System.IO.Directory.SetCurrentDirectory*>를 호출하고 앱의 게시 위치에 대한 경로를 사용합니다. <xref:System.IO.Directory.GetCurrentDirectory*>를 호출하는 경우 Windows 서비스 앱이 *C:\\WINDOWS\\system32* 폴더를 반환하므로 경로를 얻기 위해 <xref:System.IO.Directory.GetCurrentDirectory*>를 호출하지는 마세요. 자세한 내용은 [현재 디렉터리 및 콘텐츠 루트](#current-directory-and-content-root) 섹션을 참조하세요. 이 단계는 `CreateWebHostBuilder`에서 앱이 구성되기 전에 수행됩니다.
* <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>를 호출하여 앱을 서비스로 실행합니다.

[명령줄 구성 공급자](xref:fundamentals/configuration/index#command-line-configuration-provider)에는 명령줄 인수의 이름-값 쌍이 필요하므로 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>가 인수를 수신하기 전에 `--console` 스위치가 인수에서 제거됩니다.

Windows 이벤트 로그에 기록하려면 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>에 EventLog 공급자를 추가합니다. *appsettings.Production.json* 파일에서 `Logging:LogLevel:Default` 키를 사용하여 로깅 수준을 설정합니다.

샘플 앱의 다음 예제에서는 앱 내에서 수명 이벤트를 처리하기 위해 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 대신 `RunAsCustomService`가 호출됩니다. 자세한 내용은 [시작 및 중지 이벤트 처리](#handle-starting-and-stopping-events) 섹션을 참조하세요.

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

::: moniker-end

## <a name="deployment-type"></a>배포 유형

배포 시나리오에 대한 자세한 내용은 [.NET Core 애플리케이션 배포](/dotnet/core/deploying/)를 참조하세요.

### <a name="framework-dependent-deployment-fdd"></a>FDD(프레임워크 종속 배포)

FDD(프레임워크 종속 배포)에서는 대상 시스템에 .NET Core의 공유 시스템 차원 버전이 있어야 합니다. 이 문서의 지침에 따라 FDD 시나리오가 채택된 경우 SDK는 *프레임워크 종속 실행 파일*이라는 실행 파일( *.exe*)을 생성합니다.

::: moniker range=">= aspnetcore-3.0"

다음 속성 요소를 프로젝트 파일에 추가합니다.

* `<OutputType>` &ndash; 앱의 출력 형식(실행 파일의 경우 `Exe`).
* `<LangVersion>` &ndash; C# 언어 버전(`latest` 또는 `preview`).

ASP.NET Core 앱을 게시할 때 일반적으로 생성되는 *web.config* 파일은 Windows 서비스 앱에 필요하지 않습니다. *web.config* 파일이 생성되지 않도록 하려면 `<IsTransformWebConfigDisabled>` 속성을 `true`로 설정합니다.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <OutputType>Exe</OutputType>
  <LangVersion>preview</LangVersion>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Windows [RID(런타임 식별자)](/dotnet/core/rid-catalog)([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier))는 대상 프레임워크를 포함합니다. 다음 예제에서는 RID가 `win7-x64`로 설정됩니다. `<SelfContained>` 속성은 `false`로 설정됩니다. 해당 속성은 SDK에 Windows용 실행 파일( *.exe*)과 공유 .NET Core Framework에 종속되는 앱을 생성하도록 지시합니다.

ASP.NET Core 앱을 게시할 때 일반적으로 생성되는 *web.config* 파일은 Windows 서비스 앱에 필요하지 않습니다. *web.config* 파일이 생성되지 않도록 하려면 `<IsTransformWebConfigDisabled>` 속성을 `true`로 설정합니다.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.1"

Windows [RID(런타임 식별자)](/dotnet/core/rid-catalog)([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier))는 대상 프레임워크를 포함합니다. 다음 예제에서는 RID가 `win7-x64`로 설정됩니다. `<SelfContained>` 속성은 `false`로 설정됩니다. 해당 속성은 SDK에 Windows용 실행 파일( *.exe*)과 공유 .NET Core Framework에 종속되는 앱을 생성하도록 지시합니다.

`<UseAppHost>` 속성은 `true`로 설정됩니다. 이 속성은 서비스에 FDD의 활성화 경로(실행 파일, *.exe*)를 제공합니다.

ASP.NET Core 앱을 게시할 때 일반적으로 생성되는 *web.config* 파일은 Windows 서비스 앱에 필요하지 않습니다. *web.config* 파일이 생성되지 않도록 하려면 `<IsTransformWebConfigDisabled>` 속성을 `true`로 설정합니다.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.1</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

### <a name="self-contained-deployment-scd"></a>SCD(자체 포함 배포)

SCD(자체 포함 배포)에서는 호스트 시스템에 공유 프레임워크가 없어도 됩니다. 런타임 및 앱의 종속성은 앱과 함께 배포됩니다.

Windows [RID(런타임 식별자)](/dotnet/core/rid-catalog)는 대상 프레임워크를 포함하는 `<PropertyGroup>`에 포함되어 있습니다.

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

여러 개의 RID를 게시하려면

* 세미콜론으로 구분된 목록으로 RID를 제공합니다.
* 속성 이름 [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers)(복수)를 사용합니다.

자세한 내용은 [.NET Core RID 카탈로그](/dotnet/core/rid-catalog)를 참조하세요.

::: moniker range="< aspnetcore-3.0"

`<SelfContained>` 속성이 `true`로 설정된 경우:

```xml
<SelfContained>true</SelfContained>
```

::: moniker-end

## <a name="service-user-account"></a>서비스 사용자 계정

서비스의 사용자 계정을 만드려면 관리 PowerShell 6 명령 셸에서 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet을 사용합니다.

Windows 10 2018년 10월 업데이트(버전 1809/빌드 10.0.17763) 이상의 경우:

```PowerShell
New-LocalUser -Name {NAME}
```

Windows 10 2018년 10월 업데이트(버전 1809/빌드 10.0.17763) 이전 버전 Windows OS의 경우:

```console
powershell -Command "New-LocalUser -Name {NAME}"
```

메시지가 표시되면 [강력한 암호](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)를 제공합니다.

만료 <xref:System.DateTime>과 함께 `-AccountExpires` 매개 변수를 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet에 제공한 경우가 아니면 계정이 만료되지 않습니다.

자세한 내용은 [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) 및 [서비스 사용자 계정](/windows/desktop/services/service-user-accounts)을 참조하세요.

Active Directory를 사용할 때 사용자를 관리하는 대체 방법은 관리 서비스 계정을 사용하는 것입니다. 자세한 내용은 [그룹 관리 서비스 계정 개요](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)를 참조하세요.

## <a name="log-on-as-a-service-rights"></a>서비스로 로그온 권한

서비스 사용자 계정에 대해 *서비스로 로그온* 권한을 설정하려면:

1. *secpol.msc*를 실행하여 로컬 보안 정책 편집기를 엽니다.
1. **로컬 정책** 노드를 확장하고 **사용자 권한 할당**을 선택합니다.
1. **서비스로 로그온** 정책을 엽니다.
1. **사용자 또는 그룹 추가**를 선택합니다.
1. 다음 방법 중 하나를 사용하여 개체 이름(사용자 계정)을 제공합니다.
   1. 개체 이름 필드에 사용자 계정(`{DOMAIN OR COMPUTER NAME\USER}`)을 입력하고 **확인**을 선택하여 정책에 사용자를 추가합니다.
   1. **고급**을 선택합니다. **지금 찾기**를 선택합니다. 목록에서 사용자 계정을 선택합니다. **확인**을 선택합니다. 다시 **확인**을 선택하여 정책에 사용자를 추가합니다.
1. **확인** 또는 **적용**을 선택하여 변경 내용을 적용합니다.

## <a name="create-and-manage-the-windows-service"></a>Windows 서비스 만들기 및 관리

### <a name="create-a-service"></a>서비스 만들기

PowerShell 명령을 사용하여 서비스를 등록합니다. 관리 PowerShell 6 명령 셸에서 다음 명령을 실행합니다.

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* `{EXE PATH}` &ndash; 호스트의 앱 폴더 경로(예: `d:\myservice`). 경로에 앱의 실행 파일은 포함하지 마세요. 후행 슬래시는 필요하지 않습니다.
* `{DOMAIN OR COMPUTER NAME\USER}` &ndash; 서비스 사용자 계정(예: `Contoso\ServiceUser`).
* `{NAME}` &ndash; 서비스 이름(예: `MyService`).
* `{EXE FILE PATH}` &ndash; 앱의 실행 파일 경로(예: `d:\myservice\myservice.exe`). 실행 파일 이름에 확장명을 포함하세요.
* `{DESCRIPTION}` &ndash; 서비스 설명(예: `My sample service`).
* `{DISPLAY NAME}` &ndash; 서비스 표시 이름(예: `My Service`).

### <a name="start-a-service"></a>서비스 시작

다음 PowerShell 6 명령을 사용하여 서비스를 시작합니다.

```powershell
Start-Service -Name {NAME}
```

명령은 서비스를 시작하는 데 몇 초 정도 걸립니다.

### <a name="determine-a-services-status"></a>서비스의 상태 확인

서비스의 상태를 확인하려면 다음 PowerShell 6 명령을 사용합니다.

```powershell
Get-Service -Name {NAME}
```

상태는 다음 값 중 하나로 보고됩니다.

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>서비스 중지

다음 PowerShell 6 명령을 사용하여 서비스를 중지합니다.

```powershell
Stop-Service -Name {NAME}
```

### <a name="remove-a-service"></a>서비스 제거

서비스를 중지하는 짧은 지연 후에 다음 PowerShell 6 명령을 사용하여 서비스를 제거합니다.

```powershell
Remove-Service -Name {NAME}
```

::: moniker range="< aspnetcore-3.0"

## <a name="handle-starting-and-stopping-events"></a>시작 및 중지 이벤트 처리

<xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> 및 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> 이벤트를 처리하려면:

1. `OnStarting`, `OnStarted` 및 `OnStopping` 메서드를 사용하여 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService>에서 파생되는 클래스를 만듭니다.

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. `CustomWebHostService`를 <xref:System.ServiceProcess.ServiceBase.Run*>에 전달하는 <xref:Microsoft.AspNetCore.Hosting.IWebHost>에 대한 확장 메서드를 만듭니다.

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. `Program.Main`에서 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 대신 `RunAsCustomService` 확장 메서드를 호출합니다.

   ```csharp
   host.RunAsCustomService();
   ```

   `Program.Main`에서 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>의 위치를 확인하려면 [배포 유형](#deployment-type) 섹션에 표시된 코드 샘플을 참조하세요.

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a>프록시 서버 및 부하 분산 장치 시나리오

인터넷 또는 회사 네트워크의 요청과 상호 작용하고 프록시 또는 부하 분산 장치 뒤에 있는 서비스에는 추가 구성이 필요합니다. 자세한 내용은 <xref:host-and-deploy/proxy-load-balancer>을 참조하세요.

## <a name="configure-endpoints"></a>엔드포인트 구성

기본적으로 ASP.NET Core는 `http://localhost:5000`으로 바인딩합니다. `ASPNETCORE_URLS` 환경 변수를 설정하여 URL과 포트를 구성합니다.

HTTPS 엔드포인트에 대한 지원을 포함하여 추가 URL 및 포트 구성 방법에 대한 자세한 내용은 다음 항목을 참조하세요.

* <xref:fundamentals/servers/kestrel#endpoint-configuration>(Kestrel)
* <xref:fundamentals/servers/httpsys#configure-windows-server>(HTTP.sys)

> [!NOTE]
> 서비스 엔드포인트를 보호하기 위해 ASP.NET Core HTTPS 개발 인증서 사용은 지원되지 않습니다.

## <a name="current-directory-and-content-root"></a>현재 디렉터리 및 콘텐츠 루트

Windows 서비스에 대해 <xref:System.IO.Directory.GetCurrentDirectory*>를 호출하여 반환된 현재 작업 디렉터리는 *C:\\WINDOWS\\system32* 폴더입니다. *system32* 폴더는 서비스의 파일(예: 설정 파일)을 저장을 저장하는 데 적절한 위치가 아닙니다. 다음 방법 중 하나를 사용하여 서비스의 자산 및 설정 파일을 유지 관리하고 액세스합니다.

::: moniker range=">= aspnetcore-3.0"

### <a name="use-contentrootpath-or-contentrootfileprovider"></a>ContentRootPath 또는 ContentRootFileProvider 사용

[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) 또는 <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider>를 사용하여 앱의 리소스를 찾습니다.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

### <a name="set-the-content-root-path-to-the-apps-folder"></a>콘텐츠 루트 경로를 앱 폴더로 설정

<xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*>는 서비스가 만들어질 때 `binPath` 인수에 제공된 것과 같은 경로입니다. `GetCurrentDirectory`를 호출하여 설정 파일의 경로를 만드는 대신, 앱의 [콘텐츠 루트](xref:fundamentals/index#content-root) 경로를 사용하여 <xref:System.IO.Directory.SetCurrentDirectory*>를 호출합니다.

`Program.Main`에서 서비스 실행 파일의 폴더 경로를 확인하고 이 경로를 사용하여 앱의 콘텐츠 루트를 설정합니다.

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

::: moniker-end

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>디스크의 적합한 위치에 서비스 파일 저장

파일이 포함된 폴더로 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>를 사용하는 경우 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>를 통해 절대 경로를 지정합니다.

## <a name="additional-resources"></a>추가 자료

::: moniker range=">= aspnetcore-3.0"

* [Kestrel 엔드포인트 구성](xref:fundamentals/servers/kestrel#endpoint-configuration)(HTTPS 구성 및 SNI 지원 포함)
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [Kestrel 엔드포인트 구성](xref:fundamentals/servers/kestrel#endpoint-configuration)(HTTPS 구성 및 SNI 지원 포함)
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
