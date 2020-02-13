---
title: ASP.NET Core의 구성
author: guardrex
description: 구성 API를 사용하여 ASP.NET Core 앱을 구성하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2020
uid: fundamentals/configuration/index
ms.openlocfilehash: d0ef670aa0ac4960318f86ea7888b9eab71f17fd
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77171892"
---
# <a name="configuration-in-aspnet-core"></a>ASP.NET Core의 구성

[Luke Latham](https://github.com/guardrex)으로

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core의 앱 구성은 ‘구성 공급자’가 설정한 키-값 쌍을 기반으로 합니다.  구성 공급자는 다양한 구성 소스에서 구성 데이터를 키-값 쌍으로 읽어 들입니다.

* Azure Key Vault
* Azure App Configuration
* 명령줄 인수
* 사용자 지정 공급자(설치 또는 생성된)
* 디렉터리 파일
* 환경 변수
* 메모리 내 .NET 개체
* 설정 파일

일반적인 구성 공급자 시나리오에 대한 구성 패키지([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/))는 프레임워크에 의해 암시적으로 포함됩니다.

샘플 앱에서 <xref:Microsoft.Extensions.Configuration> 네임스페이스를 사용하는 다음 코드 예제:

```csharp
using Microsoft.Extensions.Configuration;
```

‘옵션 패턴’은 이 항목에 설명된 구성 개념의 확장입니다.  옵션은 클래스를 사용하여 관련 설정 그룹을 나타냅니다. 자세한 내용은 <xref:fundamentals/configuration/options>를 참조하세요.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="host-versus-app-configuration"></a>호스트 대 앱 구성

앱을 구성하고 시작하기 전에 *호스트*를 구성하고 시작합니다. 호스트는 앱 시작 및 수명 관리를 담당합니다. 앱과 호스트 모두 이 항목에서 설명하는 구성 관리자를 사용하여 구성합니다. 호스트 구성 키-값 쌍은 앱의 구성에도 포함됩니다. 호스트를 빌드할 때 구성 공급자를 사용하는 방법과 구성 소스가 호스트 구성에 미치는 영향에 대한 자세한 내용은 <xref:fundamentals/index#host>를 참조하세요.

## <a name="other-configuration"></a>기타 구성

이 항목에서는 *앱 구성*에 관련된 내용만 다룹니다. ASP.NET Core 앱을 실행하고 호스팅하는 다른 요소는 이 항목에서 다루지 않는 구성 파일을 사용하여 구성됩니다.

* *launch.json*/*launchSettings.json*은 다음에 설명된 개발 환경에 대한 도구 구성 파일입니다.
  * <xref:fundamentals/environments#development> 문서 내
  * 개발 시나리오에서 ASP.NET Core 앱을 구성하는 데 해당 파일이 사용되는 설명서 집합 전체
* *web.config*는 다음 항목에 설명된 서버 구성 파일입니다.
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

이전 버전의 ASP.NET에서 앱 구성을 마이그레이션하는 방법에 대한 자세한 정보는 <xref:migration/proper-to-2x/index#store-configurations>를 참조하세요.

## <a name="default-configuration"></a>기본 구성

호스트를 빌드할 때 ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) 템플릿을 기반으로 하는 웹앱은 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>를 호출합니다. `CreateDefaultBuilder`는 다음 순서로 앱에 대한 기본 구성을 제공합니다.

다음은 [제네릭 호스트](xref:fundamentals/host/generic-host)를 사용하는 앱에 적용됩니다. [웹 호스트](xref:fundamentals/host/web-host)를 사용하는 경우 기본 구성에 대한 자세한 내용은 [이 항목의 ASP.NET Core 2.2 버전](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2)을 참조하세요.

* 호스트 구성은 다음에 의해 제공됩니다.
  * [환경 변수 구성 공급자](#environment-variables-configuration-provider)를 사용하여 `DOTNET_`를 접두사로 사용하는 환경 변수(예: `DOTNET_ENVIRONMENT`) 구성 키-값 쌍이 로드되면 접두사(`DOTNET_`)는 제거됩니다.
  * [명령줄 구성 공급자](#command-line-configuration-provider)를 사용하는 명령줄 인수
* 웹 호스트 기본 구성이 설정됩니다(`ConfigureWebHostDefaults`).
  * Kestrel은 웹 서버로 사용되며 앱의 구성 공급자를 사용하여 구성됩니다.
  * 호스트 필터링 미들웨어를 추가합니다.
  * `ASPNETCORE_FORWARDEDHEADERS_ENABLED` 환경 변수가 `true`로 설정된 경우 전달된 헤더 미들웨어를 추가합니다.
  * IIS 통합을 사용하도록 설정합니다.
* 앱 구성은 다음에 의해 제공됩니다.
  * [파일 구성 공급자](#file-configuration-provider)를 사용하는 *appsettings.json*
  * [파일 구성 공급자](#file-configuration-provider)를 사용하는 *appsettings.{Environment}.json*
  * 앱이 항목 어셈블리를 사용하여 `Development` 환경에서 실행되는 경우 [Secret Manager](xref:security/app-secrets)입니다.
  * [환경 변수 구성 공급 기업](#environment-variables-configuration-provider)을 사용하는 환경 변수
  * [명령줄 구성 공급자](#command-line-configuration-provider)를 사용하는 명령줄 인수

## <a name="security"></a>보안

중요한 구성 데이터를 보호하려면 다음 방법을 채택합니다.

* 구성 공급자 코드 또는 일반 텍스트 구성 파일에 암호 또는 기타 중요한 데이터를 절대 저장하지 마세요.
* 개발 또는 테스트 환경에서 프로덕션 비밀을 사용하지 마세요.
* 의도치 않게 소스 코드 리포지토리에 커밋되는 일이 없도록 프로젝트 외부에서 비밀을 지정하세요.

자세한 내용은 다음 항목을 참조하세요.

* <xref:fundamentals/environments>
* <xref:security/app-secrets> &ndash; 환경 변수를 사용하여 중요한 데이터를 저장하는 방법에 대한 조언을 포함합니다. 비밀 관리자는 파일 구성 공급자를 사용하여 사용자 비밀을 로컬 시스템의 JSON 파일에 저장합니다. 파일 구성 공급자에 대해서는 이 항목의 뒷부분에서 설명합니다.

[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)가 ASP.NET Core 앱에 대한 앱 비밀을 안전하게 저장합니다. 자세한 내용은 <xref:security/key-vault-configuration>를 참조하세요.

## <a name="hierarchical-configuration-data"></a>계층적 구성 데이터

구성 API에서는 구성 키에 구분 기호를 사용해 계층적 데이터를 평면화하여 계층적 구성 데이터를 유지 관리할 수 있습니다.

다음 JSON 파일에서는 두 섹션으로 이루어진 구조적 계층에 네 개의 키가 있습니다.

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  }
}
```

파일을 구성으로 읽어 들이면 구성 소스의 원래 계층적 데이터 구조를 유지하기 위해 고유 키가 생성됩니다. 콜론(`:`)을 사용해 섹션과 키를 평면화하여 원래 구조를 유지합니다.

* section0:key0
* section0:key1
* section1:key0
* section1:key1

구성 데이터에서는 <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> 및 <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> 메서드를 사용하여 섹션과 섹션의 자식을 격리할 수 있습니다. 이러한 메서드에 대해서는 나중에 [GetSection, GetChildren 및 Exists](#getsection-getchildren-and-exists)에서 설명합니다.

## <a name="conventions"></a>규칙

### <a name="sources-and-providers"></a>소스 및 공급자

앱 시작 시 구성 공급자에서 지정한 순서로 구성 소스를 읽습니다.

변경 검색을 구현하는 구성 공급자는 기본 설정 파일이 변경되면 구성을 다시 로드할 수 있습니다. 예를 들어 파일 구성 공급자(이 항목의 뒷부분에서 설명됨) 및 [Azure Key Vault 구성 공급자](xref:security/key-vault-configuration)는 변경 검색을 구현합니다.

<xref:Microsoft.Extensions.Configuration.IConfiguration>은 앱의 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 컨테이너에서 사용할 수 있습니다. <xref:Microsoft.Extensions.Configuration.IConfiguration>을 Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> 또는 MVC <xref:Microsoft.AspNetCore.Mvc.Controller>에 삽입하여 클래스에 대한 구성을 가져올 수 있습니다.

다음 예제에서는 `_config` 필드가 구성 값에 액세스하는 데 사용됩니다.

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }
}
```

```csharp
public class HomeController : Controller
{
    private readonly IConfiguration _config;

    public HomeController(IConfiguration config)
    {
        _config = config;
    }
}
```

구성 공급자는 호스트에서 설정될 때 DI가 제공되지 않으므로 DI를 활용할 수 없습니다.

### <a name="keys"></a>키

구성키는 다음 규칙을 따릅니다.

* 키는 대/소문자를 구분하지 않습니다. 예를 들어 `ConnectionString`과 `connectionstring`은 동일한 키로 처리됩니다.
* 같은 키의 값을 같거나 다른 여러 구성 공급자에서 설정하는 경우 키에 설정된 마지막 값이 사용되는 값입니다.
* 계층적 키
  * 구성 API 내에서는 콜론 구분 기호(`:`)가 모든 플랫폼에 적용됩니다.
  * 환경 변수에서는 콜론 구분 기호가 일부 플랫폼에 적용되지 않을 수 있습니다. 두 개의 밑줄(`__`)은 모든 플랫폼에서 지원되며 콜론으로 자동 변환됩니다.
  * Azure Key Vault에서 계층적 키는 `--`(두 개의 대시)를 구분 기호로 사용합니다. 비밀을 앱의 구성으로 로드할 때 대시를 콜론으로 바꾸는 코드를 작성하세요.
* <xref:Microsoft.Extensions.Configuration.ConfigurationBinder>는 구성 키에 배열 인덱스를 사용하여 배열을 개체에 바인딩하는 것을 지원합니다. 배열 바인딩에 대해서는 [클래스에 배열 바인딩](#bind-an-array-to-a-class) 섹션에서 설명합니다.

### <a name="values"></a>값

구성 값은 다음 규칙을 따릅니다.

* 값은 문자열입니다.
* Null 값은 구성에 저장하거나 개체에 바인딩할 수 없습니다.

## <a name="providers"></a>공급자

다음 표에서는 ASP.NET Core 앱에서 사용할 수 있는 구성 공급자를 보여 줍니다.

| 공급자 | 다음에서 구성 제공&hellip; |
| -------- | ----------------------------------- |
| [Azure Key Vault 구성 공급자](xref:security/key-vault-configuration)(‘보안’ 항목)  | Azure Key Vault |
| [Azure App Configuration 공급자](/azure/azure-app-configuration/quickstart-aspnet-core-app)(Azure 설명서) | Azure App Configuration |
| [명령줄 구성 공급자](#command-line-configuration-provider) | 명령줄 매개 변수 |
| [사용자 지정 구성 공급자](#custom-configuration-provider) | 사용자 지정 소스 |
| [환경 변수 구성 공급자](#environment-variables-configuration-provider) | 환경 변수 |
| [파일 구성 공급자](#file-configuration-provider) | 파일(INI, JSON, XML) |
| [파일별 키 구성 공급자](#key-per-file-configuration-provider) | 디렉터리 파일 |
| [메모리 구성 공급자](#memory-configuration-provider) | 메모리 내 컬렉션 |
| [사용자 비밀(비밀 관리자)](xref:security/app-secrets)(‘보안’ 항목)  | 사용자 프로필 디렉터리의 파일 |

시작 시 구성 공급자에서 지정한 순서로 구성 소스를 읽습니다. 이 항목의 구성 공급자는 코드에서 정렬하는 순서가 아니라 사전 순으로 설명되어 있습니다. 앱에 필요한 기본 구성 소스에 대한 우선 순위에 맞게 구성 공급자를 코드에 정렬하세요.

구성 공급자의 일반적인 순서는 다음과 같습니다.

1. 파일(*appsettings.json*, *appsettings.{Environment}.json*, 여기서 `{Environment}`는 앱의 현재 호스팅 환경)
1. [Azure Key Vault](xref:security/key-vault-configuration)
1. [사용자 비밀(비밀 관리자)](xref:security/app-secrets)(개발 환경에서만)
1. 환경 변수
1. 명령줄 인수

일반적인 방식은 명령줄 구성 공급자를 일련의 공급자에서 마지막에 배치하는 것이므로, 다른 공급자에서 설정한 구성을 명령줄 인수로 재정의할 수 있습니다.

`CreateDefaultBuilder`로 새 호스트 작성기가 초기화될 때 이전 공급자 시퀀스가 사용됩니다. 자세한 내용은 [기본 구성](#default-configuration) 섹션을 참조하세요.

## <a name="configure-the-host-builder-with-configurehostconfiguration"></a>ConfigureHostConfiguration을 사용하여 호스트 작성기 구성

호스트 작성기를 구성하려면 <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>을 호출하고 구성을 제공합니다. `ConfigureHostConfiguration`은 나중에 빌드 프로세스에서 사용하기 위해 <xref:Microsoft.Extensions.Hosting.IHostEnvironment>를 초기화하는 데 사용됩니다. `ConfigureHostConfiguration` 항목은 부가적 결과와 함께 여러 번 호출할 수 있습니다.

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureHostConfiguration(config =>
        {
            var dict = new Dictionary<string, string>
            {
                {"MemoryCollectionKey1", "value1"},
                {"MemoryCollectionKey2", "value2"}
            };

            config.AddInMemoryCollection(dict);
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

## <a name="configureappconfiguration"></a>ConfigureAppConfiguration

호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출하여 `CreateDefaultBuilder`에 의해 자동으로 추가된 구성 공급자 외에도 앱의 구성 공급자를 지정합니다.

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a>명령줄 인수를 사용하여 이전 구성 재정의

명령줄 인수를 사용하여 재정의할 수 있는 앱 구성을 제공하려면 마지막으로 `AddCommandLine`을 호출합니다.

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a>CreateDefaultBuilder에 의해 추가된 공급자 제거

`CreateDefaultBuilder`에 의해 추가된 공급자를 제거하려면 먼저 [IConfigurationBuilder](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources)에서 [Clear](/dotnet/api/system.collections.generic.icollection-1.clear)를 호출합니다.

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a>앱 시작 중 구성 사용

`Startup.ConfigureServices`를 포함하여 `ConfigureAppConfiguration`에서 앱에 제공되는 구성은 앱을 시작하는 동안 사용할 수 있습니다. 자세한 내용은 [시작하는 동안 구성에 액세스](#access-configuration-during-startup) 섹션을 참조하세요.

## <a name="command-line-configuration-provider"></a>명령줄 구성 공급자

<xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider>은 런타임 시 명령줄 인수 키-값 쌍에서 구성을 로드합니다.

명령줄 구성을 활성화하기 위해 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 확장 메서드가 호출됩니다.

`CreateDefaultBuilder(string [])`가 호출되면 `AddCommandLine`도 자동으로 호출됩니다. 자세한 내용은 [기본 구성](#default-configuration) 섹션을 참조하세요.

`CreateDefaultBuilder`는 다음 항목도 로드합니다.

* *appsettings.json* 및 *appsettings.{Environment}.json* 파일에서 선택적 구성
* [사용자 비밀(비밀 관리자)](xref:security/app-secrets)(개발 환경에서)
* 환경 변수.

`CreateDefaultBuilder`는 명령줄 구성 공급자를 마지막에 추가합니다. 따라서 런타임에 전달된 명령줄 인수가 다른 공급자에서 설정한 구성을 재정의합니다.

`CreateDefaultBuilder`는 호스트를 생성할 때 작동합니다. 따라서 `CreateDefaultBuilder`에서 활성화한 명령줄 구성이 호스트 생성 방식에 영향을 줄 수 있습니다.

ASP.NET Core 템플릿을 기반으로 하는 앱의 경우 `AddCommandLine`은 `CreateDefaultBuilder`에서 이미 호출되었습니다. 추가 구성 공급자를 추가하고 명령줄 인수를 사용하여 해당 공급자의 구성을 재정의하는 기능을 유지하려면 `ConfigureAppConfiguration`에서 앱의 추가 공급자를 호출하고 마지막으로 `AddCommandLine`을 호출합니다.

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

**예제**

샘플 앱은 정적 편의 메서드 `CreateDefaultBuilder`를 활용하여 호스트를 빌드하며, <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 호출도 포함합니다.

1. 프로젝트의 디렉터리에서 명령 프롬프트를 엽니다.
1. `dotnet run` 명령에 명령줄 인수를 제공합니다(`dotnet run CommandLineKey=CommandLineValue`).
1. 앱이 실행되면 브라우저를 열어 `http://localhost:5000`의 앱으로 이동합니다.
1. `dotnet run`에 제공한 구성 명령줄 인수에 대한 키-값 쌍이 출력에 포함되어 있는지 확인합니다.

### <a name="arguments"></a>인수

값은 등호(`=`) 다음에 와야 합니다. 또는 값이 공백 다음에 오는 경우 키에 접두사(`--` 또는 `/`)가 있어야 합니다. 등호를 사용하는 경우 값이 필요하지 않습니다(예: `CommandLineKey=`).

| 키 접두사               | 예제                                                |
| ------------------------ | ------------------------------------------------------ |
| 접두사 없음                | `CommandLineKey1=value1`                               |
| 대시 2개(`--`)        | `--CommandLineKey2=value2`, `--CommandLineKey2 value2` |
| 슬래시(`/`)      | `/CommandLineKey3=value3`, `/CommandLineKey3 value3`   |

같은 명령 내에서 등호를 사용하는 명령줄 인수 키-값 쌍을 공백을 사용하는 키-값 쌍과 함께 사용하지 마세요.

명령 예:

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a>스위치 매핑

스위치 매핑은 키 이름 교체 논리를 지원합니다. <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>를 사용하여 구성을 수동으로 빌드하는 경우에는 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 메서드에 대체 스위치를 포함하는 사전을 제공하세요.

스위치 매핑 사전을 사용하면 명령줄 인수를 통해 제공된 키와 일치하는 키에 대해 사전을 검사합니다. 사전에서 명령줄 키가 발견되면 사전 값(대체 키)이 다시 전달되어 앱 구성의 키-값 쌍이 설정됩니다. 단일 대시(`-`) 접두사가 붙은 명령줄 키에는 스위치 매핑이 필수입니다.

스위치 매핑 사전 키 규칙:

* 스위치는 단일 대시(`-`) 또는 이중 대시(`--`)로 시작해야 합니다.
* 스위치 매핑 사전이 중복 키를 포함하면 안 됩니다.

스위치 매핑 사전을 만듭니다. 다음 예에서는 두 개의 스위치 매핑이 만들어집니다.

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

호스트가 빌드될 때 스위치 매핑 사전과 함께 `AddCommandLine`을 호출합니다.

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

스위치 매핑을 사용하는 앱의 경우 `CreateDefaultBuilder`에 대한 호출은 인수를 전달하지 않아야 합니다. `CreateDefaultBuilder` 메서드의 `AddCommandLine` 호출에는 매핑된 스위치가 포함되지 않으며 `CreateDefaultBuilder`에 스위치 매핑 사전을 전달할 방법은 없습니다. 해결 방법으로 `CreateDefaultBuilder`에 인수를 전달하지 않고 대신 `ConfigurationBuilder` 메서드의 `AddCommandLine` 메서드에서 인수와 스위치 매핑 사전을 모두 처리하도록 할 수 있습니다.

생성된 스위치 매핑 사전은 다음 표의 데이터를 포함합니다.

| Key       | 값             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

앱을 시작할 때 스위치 매핑된 키가 사용되는 경우 구성은 사전에서 제공하는 키의 구성 값을 수신합니다.

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

앞의 명령을 실행한 후 구성에는 다음 표에 표시된 값이 포함됩니다.

| Key               | 값    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a>환경 변수 구성 공급자

<xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider>는 런타임에 환경 변수 키-값 쌍에서 구성을 로드합니다.

환경 변수 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 확장 메서드를 호출합니다.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

[Azure App Service](https://azure.microsoft.com/services/app-service/)를 사용하면 Azure Portal에서 환경 변수를 설정할 수 있으므로 환경 변수 구성 공급자를 사용한 앱 구성을 재정의할 수 있습니다. 자세한 내용은 [Azure 앱: Azure Portal을 사용하여 앱 구성 재정의](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal) 편을 참조하세요.

새 호스트 작성기가 [제네릭 호스트](xref:fundamentals/host/generic-host)로 초기화되고 `CreateDefaultBuilder`가 호출될 때 `AddEnvironmentVariables`는 [호스트 구성](#host-versus-app-configuration)에 대해 접두사가 `DOTNET_`인 환경 변수를 로드하는 데 사용됩니다. 자세한 내용은 [기본 구성](#default-configuration) 섹션을 참조하세요.

`CreateDefaultBuilder`는 다음 항목도 로드합니다.

* 접두사가 없는 `AddEnvironmentVariables`의 호출을 통한 접두사가 없는 환경 변수의 앱 구성
* *appsettings.json* 및 *appsettings.{Environment}.json* 파일에서 선택적 구성
* [사용자 비밀(비밀 관리자)](xref:security/app-secrets)(개발 환경에서)
* 명령줄 인수.

사용자 비밀 및 *appsettings* 파일을 통해 구성을 설정한 후 환경 변수 구성 공급자를 호출합니다. 이 위치에서 공급자를 호출하면 런타임에 환경 변수를 읽어 들여 사용자 비밀 및 *appsettings* 파일로 설정한 구성을 재정의할 수 있습니다.

추가 환경 변수에서 앱 구성을 제공하려면 `ConfigureAppConfiguration`에서 앱의 추가 공급자를 호출하고 접두사가 있는 `AddEnvironmentVariables`를 호출합니다.

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

지정된 접두사를 포함하는 환경 변수에서 다른 공급자의 값을 재정의할 수 있도록 하려면 `AddEnvironmentVariables`를 마지막으로 호출합니다.

**예제**

샘플 앱은 정적 편의 메서드 `CreateDefaultBuilder`를 활용하여 호스트를 빌드하며, `AddEnvironmentVariables` 호출도 포함합니다.

1. 샘플 앱을 실행합니다. 브라우저를 열어 `http://localhost:5000`의 앱으로 이동합니다.
1. 환경 변수 `ENVIRONMENT`에 대한 키-값 쌍이 출력에 포함되는지 확인합니다. 이 값은 앱이 실행 중인 환경을 나타내며, 로컬에서 실행 중이면 일반적으로 `Development` 입니다.

앱은 렌더링하는 환경 변수 목록을 짧게 유지하기 위해 환경 변수를 필터링합니다. 샘플 앱의 *Pages/Index.cshtml.cs* 파일을 참조하세요.

앱에서 사용할 수 있는 모든 환경 변수를 표시하려면 *Pages/Index.cshtml.cs*에서 `FilteredConfiguration`을 다음으로 변경합니다.

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a>접두사

`AddEnvironmentVariables` 메서드에 접두사를 제공하면 앱의 구성에 로드된 환경 변수가 필터링됩니다. 예를 들어 환경 변수를 `CUSTOM_` 접두사로 필터링하려면 구성 공급자에 이 접두사를 제공합니다.

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

구성 키-값 쌍이 생성되면 접두사는 제거됩니다.

호스트 작성기를 만들 때 환경 변수에서 호스트 구성을 제공합니다. 환경 변수에 사용되는 접두사에 대한 자세한 내용은 [기본 구성](#default-configuration) 섹션을 참조하세요.

**연결 문자열 접두사**

구성 API에는 앱 환경에 대한 Azure 연결 문자열 구성과 관련된 네 개의 연결 문자열 환경 변수에 대한 특별한 처리 규칙이 있습니다. 즉, `AddEnvironmentVariables`에 접두사를 제공하지 않으면 표에 표시된 접두사가 붙은 환경 변수가 앱으로 로드됩니다.

| 연결 문자열 접두사 | 공급자 |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | 사용자 지정 공급자 |
| `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |

표에 표시된 네 개 접두사 중 하나가 붙은 환경 변수가 검색되어 구성으로 로드되면 다음과 같이 됩니다.

* 환경 변수 접두사를 제거하고 구성 키 섹션(`ConnectionStrings`)을 추가하여 구성 키가 생성됩니다.
* 데이터베이스 연결 제공자(지정된 공급자가 없는 `CUSTOMCONNSTR_` 제외)를 나타내는 새 구성 키-값 쌍이 생성됩니다.

| 환경 변수 키 | 변환된 구성 키 | 공급자 구성 항목                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | 구성 항목이 생성되지 않습니다.                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | 키: `ConnectionStrings:{KEY}_ProviderName`:<br>값: `MySql.Data.MySqlClient` |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | 키: `ConnectionStrings:{KEY}_ProviderName`:<br>값: `System.Data.SqlClient`  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | 키: `ConnectionStrings:{KEY}_ProviderName`:<br>값: `System.Data.SqlClient`  |

**예제**

서버에 사용자 지정 연결 문자열 환경 변수가 만들어집니다.

* 이름 &ndash; `CUSTOMCONNSTR_ReleaseDB`
* 값 &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`

`IConfiguration`이 삽입되고 `_config`라는 필드에 할당된 경우 값을 읽습니다.

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a>파일 구성 공급자

<xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>는 파일 시스템에서 구성을 로드하기 위한 기본 클래스입니다. 다음 구성 공급자는 특정 파일 형식 전용입니다.

* [INI 구성 공급자](#ini-configuration-provider)
* [JSON 구성 공급자](#json-configuration-provider)
* [XML 구성 공급자](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a>INI 구성 공급자

<xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider>는 런타임에 INI 파일 키-값 쌍에서 구성을 로드합니다.

INI 파일 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> 확장 메서드를 호출합니다.

콜론은 INI 파일 구성에서 콜론을 섹션 구분 기호로 사용할 수 있습니다.

오버로드에서는 다음을 지정할 수 있습니다.

* 파일이 선택 사항인지 여부
* 파일이 변경되면 구성을 다시 로드하는지 여부
* 파일에 액세스하는 데 사용되는 <xref:Microsoft.Extensions.FileProviders.IFileProvider>

호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출하여 앱의 구성을 지정합니다.

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

INI 구성 파일의 일반적인 예:

```ini
[section0]
key0=value
key1=value

[section1]
subsection:key=value

[section2:subsection0]
key=value

[section2:subsection1]
key=value
```

이전 구성 파일은 `value`와 함께 다음 키를 로드합니다.

* section0:key0
* section0:key1
* section1:subsection:key
* section2:subsection0:key
* section2:subsection1:key

### <a name="json-configuration-provider"></a>JSON 구성 공급자

<xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider>는 런타임에 JSON 파일 키-값 쌍에서 구성을 로드합니다.

JSON 파일 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 확장 메서드를 호출합니다.

오버로드에서는 다음을 지정할 수 있습니다.

* 파일이 선택 사항인지 여부
* 파일이 변경되면 구성을 다시 로드하는지 여부
* 파일에 액세스하는 데 사용되는 <xref:Microsoft.Extensions.FileProviders.IFileProvider>

`CreateDefaultBuilder`를 사용하여 새 호스트 작성기를 초기화할 때 `AddJsonFile`이 자동으로 두 번 호출됩니다. 이 메서드는 호출되면 다음에서 구성을 로드합니다.

* *appsettings.json* &ndash; 이 파일을 먼저 읽습니다. 파일의 환경 버전이 *appsettings.json* 파일에서 제공한 값을 재정의할 수 있습니다.
* *appsettings.{Environment}.json* &ndash; 파일의 환경 버전은 [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)을 기반으로 로드됩니다.

자세한 내용은 [기본 구성](#default-configuration) 섹션을 참조하세요.

`CreateDefaultBuilder`는 다음 항목도 로드합니다.

* 환경 변수.
* [사용자 비밀(비밀 관리자)](xref:security/app-secrets)(개발 환경에서)
* 명령줄 인수.

JSON 구성 공급자를 먼저 설정합니다. 따라서 사용자 비밀, 환경 변수 및 명령줄 인수가 *appsettings* 파일에서 설정한 구성을 재정의할 수 있습니다.

*appsettings.json* 및 *appsettings.{Environment}.json* 이외의 파일에 대한 앱 구성을 지정하도록 호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출합니다.

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

**예제**

샘플 앱은 정적 편의 메서드 `CreateDefaultBuilder`를 활용하여 호스트를 빌드하며, `AddJsonFile` 두 번 호출도 포함합니다.

* `AddJsonFile`에 대한 첫 번째 호출은 *appsettings.json*에서 구성을 로드합니다.

  [!code-json[](index/samples/3.x/ConfigurationSample/appsettings.json)]

* `AddJsonFile`에 대한 두 번째 호출은 *appsettings.{Environment}.json*에서 구성을 로드합니다. 샘플 앱의 *appsettings.Development.json*의 경우 다음 파일이 로드됩니다.

  [!code-json[](index/samples/3.x/ConfigurationSample/appsettings.Development.json)]

1. 샘플 앱을 실행합니다. 브라우저를 열어 `http://localhost:5000`의 앱으로 이동합니다.
1. 출력에는 앱 환경을 기반으로 하는 구성에 대한 키 값 쌍이 포함되어 있습니다. 개발 환경에서 앱을 실행할 때 `Logging:LogLevel:Default` 키의 로그 수준은 `Debug`입니다.
1. 프로덕션 환경에서 샘플 앱을 다시 실행합니다.
   1. *Properties/launchSettings.json* 파일을 엽니다.
   1. `ConfigurationSample` 프로필에서 `ASPNETCORE_ENVIRONMENT` 환경 변수의 값을 `Production`으로 변경합니다.
   1. 파일을 저장하고 명령 셸에서 `dotnet run`를 사용하여 앱을 실행합니다.
1. *appsettings.Development.json*의 설정에서 더 이상 *appsettings.json*의 설정을 재정의하지 않습니다. `Logging:LogLevel:Default` 키의 로그 수준은 `Information`입니다.

### <a name="xml-configuration-provider"></a>XML 구성 공급자

<xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider>는 런타임에 XML 파일 키-값 쌍에서 구성을 로드합니다.

XML 파일 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> 확장 메서드를 호출합니다.

오버로드에서는 다음을 지정할 수 있습니다.

* 파일이 선택 사항인지 여부
* 파일이 변경되면 구성을 다시 로드하는지 여부
* 파일에 액세스하는 데 사용되는 <xref:Microsoft.Extensions.FileProviders.IFileProvider>

구성 키-값 쌍이 생성되면 구성 파일의 루트 노드는 무시됩니다. 파일에 DTD(문서 종류 정의)나 네임스페이스는 지정하지 마세요.

호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출하여 앱의 구성을 지정합니다.

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

XML 구성 파일에서는 반복 섹션에 고유 요소 이름을 사용할 수 있습니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section0>
    <key0>value</key0>
    <key1>value</key1>
  </section0>
  <section1>
    <key0>value</key0>
    <key1>value</key1>
  </section1>
</configuration>
```

이전 구성 파일은 `value`와 함께 다음 키를 로드합니다.

* section0:key0
* section0:key1
* section1:key0
* section1:key1

같은 요소 이름을 사용하는 반복 요소는 다음과 같이 `name` 특성을 사용하여 요소를 구분하면 작동합니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section name="section0">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
  <section name="section1">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
</configuration>
```

이전 구성 파일은 `value`와 함께 다음 키를 로드합니다.

* section:section0:key:key0
* section:section0:key:key1
* section:section1:key:key0
* section:section1:key:key1

특성을 사용하여 값을 제공할 수 있습니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

이전 구성 파일은 `value`와 함께 다음 키를 로드합니다.

* key:attribute
* section:key:attribute

## <a name="key-per-file-configuration-provider"></a>파일별 키 구성 공급자

<xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider>는 디렉터리의 파일을 구성 키-값 쌍으로 사용합니다. 키는 파일 이름이고, 값은 파일의 콘텐츠를 포함합니다. 파일별 키 구성 공급자는 Docker 호스팅 시나리오에서 사용됩니다.

파일별 키 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 확장 메서드를 호출합니다. 파일의 `directoryPath`는 절대 경로여야 합니다.

오버로드에서는 다음을 지정할 수 있습니다.

* 소스를 구성하는 `Action<KeyPerFileConfigurationSource>` 대리자
* 디렉터리가 선택 사항인지 여부와 디렉터리의 경로

두 개의 밑줄(`__`)은 파일 이름에서 구성 키 구분 기호로 사용됩니다. 예를 들어, 파일 이름 `Logging__LogLevel__System`은 구성 키 `Logging:LogLevel:System`을 생성합니다.

호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출하여 앱의 구성을 지정합니다.

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a>메모리 구성 공급자

<xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider>는 메모리 내 컬렉션을 구성 키-값 쌍으로 사용합니다.

메모리 내 컬렉션 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 확장 메서드를 호출합니다.

`IEnumerable<KeyValuePair<String,String>>`을 사용하여 구성 공급자를 초기화할 수 있습니다.

호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출하여 앱의 구성을 지정합니다.

다음 예제에서는 구성 사전이 만들어집니다.

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

사전은 `AddInMemoryCollection`을 호출하여 구성을 제공하는 데 사용됩니다.

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a>GetValue

[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)는 지정된 키를 사용하여 구성에서 하나의 값을 추출하고 해당 값을 지정된 비컬렉션 형식으로 변환합니다. 오버로드는 기본값을 허용합니다.

다음 예제가 하는 일:

* `NumberKey` 키를 사용하여 구성에서 문자열 값을 추출합니다. 구성 키에서 `NumberKey`를 찾을 수 없으면 `99`의 기본값이 사용됩니다.
* 값을 `int` 형식으로 입력합니다.
* 페이지에 사용할 값을 `NumberConfig` 속성에 저장합니다.

```csharp
public class IndexModel : PageModel
{
    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    public int NumberConfig { get; private set; }

    public void OnGet()
    {
        NumberConfig = _config.GetValue<int>("NumberKey", 99);
    }
}
```

## <a name="getsection-getchildren-and-exists"></a>GetSection, GetChildren 및 Exists

이어지는 예제에서는 다음 JSON 파일을 고려하세요. 네 개의 키가 두 개 섹션에 있고, 두 섹션 중 하나에는 하위 섹션 쌍이 포함되어 있습니다.

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  },
  "section2": {
    "subsection0" : {
      "key0": "value",
      "key1": "value"
    },
    "subsection1" : {
      "key0": "value",
      "key1": "value"
    }
  }
}
```

파일을 구성으로 읽어 들이면 구성 값을 저장하기 위해 다음과 같은 고유한 계층 키가 생성됩니다.

* section0:key0
* section0:key1
* section1:key0
* section1:key1
* section2:subsection0:key0
* section2:subsection0:key1
* section2:subsection1:key0
* section2:subsection1:key1

### <a name="getsection"></a>GetSection

[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*)은 지정된 하위 섹션 키를 사용하여 구성 하위 섹션을 추출합니다.

`section1`의 키-값 쌍만 포함하는 <xref:Microsoft.Extensions.Configuration.IConfigurationSection>을 반환하려면 `GetSection`을 호출하고 섹션 이름을 제공합니다.

```csharp
var configSection = _config.GetSection("section1");
```

`configSection`에는 값이 포함되지 않고 키 및 경로만 포함됩니다.

마찬가지로 `section2:subsection0`의 키 값을 가져오려면 `GetSection`을 호출하고 섹션 경로를 제공합니다.

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

`GetSection`은 `null`을 반환하지 않습니다. 일치하는 섹션을 찾을 수 없으면 빈 `IConfigurationSection`이 반환됩니다.

`GetSection`이 일치하는 섹션을 반환할 때 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value>는 채워지지 않습니다. 섹션이 존재하면 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> 및 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path>가 반환됩니다.

### <a name="getchildren"></a>GetChildren

`section2`에서 [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*)을 호출하면 다음을 포함하는 `IEnumerable<IConfigurationSection>`이 반환됩니다.

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a>있음

[ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*)를 사용하면 구성 섹션이 있는지 확인할 수 있습니다.

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

예제 데이터의 경우 구성 데이터에 `section2:subsection2` 섹션이 없으므로 `sectionExists`는 `false`입니다.

## <a name="bind-to-a-class"></a>클래스에 바인딩

‘옵션 패턴’을 사용하여 관련 설정 그룹을 나타내는 클래스에 구성을 바인딩할 수 있습니다.  자세한 내용은 <xref:fundamentals/configuration/options>를 참조하세요.

구성 값이 문자열로 반환되지만, <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>를 호출하면 [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) 개체를 생성할 수 있습니다. 바인더는 제공된 형식의 모든 공용 읽기/쓰기 속성에 값을 바인딩합니다. 필드가 바인딩되지 **않았습니다**.

샘플 앱은 `Starship` 모델(*Models/Starship.cs*)을 포함합니다.

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

샘플 앱이 JSON 구성 공급자를 사용하여 구성을 로드하는 경우 *starship.json* 파일의 `starship` 섹션에서 구성을 생성합니다.

[!code-json[](index/samples/3.x/ConfigurationSample/starship.json)]

다음 구성 키-값 쌍이 생성됩니다.

| Key                   | 값                                             |
| --------------------- | ------------------------------------------------- |
| starship:name         | USS Enterprise                                    |
| starship:registry     | NCC-1701                                          |
| starship:class        | Constitution                                      |
| starship:length       | 304.8                                             |
| starship:commissioned | False                                             |
| trademark             | Paramount Pictures Corp. https://www.paramount.com |

샘플 앱은 `starship` 키를 사용하여 `GetSection`을 호출합니다. `starship` 키-값 쌍은 격리됩니다. `Bind` 메서드는 `Starship` 클래스의 인스턴스를 전달하는 하위 섹션에서 호출됩니다. 인스턴스 값을 바인딩한 후 인스턴스는 렌더링할 속성에 할당됩니다.

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

## <a name="bind-to-an-object-graph"></a>개체 그래프에 바인딩

<xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>는 전체 POCO 개체 그래프를 바인딩할 수 있습니다. 단순 개체 바인딩과 마찬가지로 공용 읽기/쓰기 속성만 바인딩되었습니다.

다음 샘플은 개체 그래프에`Metadata` 및 `Actors` 클래스가 포함된 `TvShow` 모델(*Models/TvShow.cs*)을 포함합니다.

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

샘플 앱의 *tvshow.xml* 파일에는 다음 구성 데이터가 포함됩니다.

[!code-xml[](index/samples/3.x/ConfigurationSample/tvshow.xml)]

`Bind` 메서드를 사용하여 전체 `TvShow` 개체 그래프에 구성을 바인딩합니다. 바인딩된 인스턴스는 렌더링할 속성에 할당됩니다.

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)는 지정된 형식을 바인딩하고 반환합니다. `Get<T>`가 `Bind`보다 사용하기 더 간편합니다. 다음 코드에서는 앞의 예제에 `Get<T>`를 사용하여 바인딩된 인스턴스가 렌더링에 사용되는 속성에 바로 할당될 수 있도록 하는 방법을 보여 줍니다.

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a>클래스에 배열 바인딩

다음 샘플 앱은 이 섹션에서 설명하는 개념을 보여 줍니다. 

<xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>는 구성 키에 배열 인덱스를 사용하여 배열을 개체에 바인딩하는 것을 지원합니다. 숫자 키 세그먼트(`:0:`, `:1:`, &hellip; `:{n}:`)를 노출하는 모든 배열 형식은 POCO 클래스 배열에 배열 바인딩할 수 있습니다.

> [!NOTE]
> 바인딩은 규칙에 따라 제공됩니다. 배열 바인딩을 구현하기 위해 사용자 지정 구성 공급자가 필요하지는 않습니다.

**메모리 내 배열 처리**

다음 표에 표시된 구성 키 및 값을 사용하세요.

| Key             | 값  |
| :-------------: | :----: |
| array:entries:0 | value0 |
| array:entries:1 | value1 |
| array:entries:2 | value2 |
| array:entries:4 | value4 |
| array:entries:5 | value5 |

메모리 구성 공급자를 사용하는 샘플 앱에서는 다음과 같은 키 및 값이 로드됩니다.

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

배열은 인덱스 &num;3에 대한 값을 건너뜁니다. 구성 바인더는 null 값을 바인딩하거나 바인딩된 개체에 null 항목을 만들 수 없으며, 이 점은 이 배열을 개체에 바인딩한 결과를 설명할 때 명확해집니다.

샘플 앱에서는 POCO 클래스를 사용하여 바인딩된 구성 데이터를 저장할 수 있습니다.

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

구성 데이터는 개체에 바인딩됩니다.

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 구문도 사용할 수 있으며, 이 경우 코드가 더 간결해집니다.

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

바인딩된 개체 즉, `ArrayExample`의 인스턴스는 구성에서 배열 데이터를 받습니다.

| `ArrayExample.Entries` 인덱스 | `ArrayExample.Entries` 값 |
| :--------------------------: | :--------------------------: |
| 0                            | value0                       |
| 1                            | value1                       |
| 2                            | value2                       |
| 3                            | value4                       |
| 4                            | value5                       |

바인딩된 개체의 인덱스 &num;3은 `array:4` 구성 키에 대한 구성 데이터와 `value4`의 값을 포함합니다. 배열이 포함된 구성 데이터를 바인딩할 때 구성 키의 배열 인덱스는 개체를 만들 때 구성 데이터를 반복하는 데에만 사용됩니다. 구성 데이터에 null 값을 유지할 수 없으며, 구성 키의 배열에서 하나 이상의 인덱스를 건너뛰더라도 바인딩된 개체에 null 값 항목이 생성되지 않습니다.

인덱스 &num;3에 대한 누락된 구성 항목은 `ArrayExample` 인스턴스에 바인딩하기 전에 구성에서 올바른 키-값 쌍을 생성하는 모든 구성 공급자에서 제공할 수 있습니다. 샘플에 누락된 키-값 쌍이 있는 추가 JSON 구성 공급자가 포함된 경우 `ArrayExample.Entries`는 전체 구성 배열과 일치합니다.

*missing_value.json*:

```json
{
  "array:entries:3": "value3"
}
```

`ConfigureAppConfiguration`의 경우

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

표에 표시된 키-값 쌍이 구성으로 로드됩니다.

| Key             | 값  |
| :-------------: | :----: |
| array:entries:3 | value3 |

JSON 구성 공급자에 인덱스 &num;3에 대한 항목이 포함된 후 `ArrayExample` 클래스 인스턴스를 바인딩하는 경우 `ArrayExample.Entries` 배열에 이 값이 포함됩니다.

| `ArrayExample.Entries` 인덱스 | `ArrayExample.Entries` 값 |
| :--------------------------: | :--------------------------: |
| 0                            | value0                       |
| 1                            | value1                       |
| 2                            | value2                       |
| 3                            | value3                       |
| 4                            | value4                       |
| 5                            | value5                       |

**JSON 배열 처리**

JSON 파일에 배열이 포함된 경우 0부터 시작하는 섹션 인덱스를 사용하여 배열 요소에 대한 구성 키가 생성됩니다. 다음 구성 파일에서 `subsection`은 배열입니다.

[!code-json[](index/samples/3.x/ConfigurationSample/json_array.json)]

JSON 구성 공급자는 구성 데이터를 다음 키-값 쌍으로 읽습니다.

| Key                     | 값  |
| ----------------------- | :----: |
| json_array:key          | valueA |
| json_array:subsection:0 | valueB |
| json_array:subsection:1 | valueC |
| json_array:subsection:2 | valueD |

샘플 앱에서는 다음 POCO 클래스를 사용하여 구성 키-값 쌍을 바인딩할 수 있습니다.

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

바인딩 후 `JsonArrayExample.Key`는 `valueA` 값을 포함합니다. 하위 섹션 값은 POCO 배열 속성 `Subsection`에 저장됩니다.

| `JsonArrayExample.Subsection` 인덱스 | `JsonArrayExample.Subsection` 값 |
| :---------------------------------: | :---------------------------------: |
| 0                                   | valueB                              |
| 1                                   | valueC                              |
| 2                                   | valueD                              |

## <a name="custom-configuration-provider"></a>사용자 지정 구성 공급자

샘플 앱에서는 [EF(Entity Framework)](/ef/core/)를 사용하여 데이터베이스에서 구성 키-값 쌍을 읽는 기본 구성 공급자를 만드는 방법을 보여 줍니다.

이 공급자의 특징은 다음과 같습니다.

* 데모용으로 EF 메모리 내 데이터베이스를 사용합니다. 연결 문자열이 필요한 데이터베이스를 사용하려면 보조 `ConfigurationBuilder`를 구현하여 다른 구성 공급자에서 연결 문자열을 제공하세요.
* 이 공급자는 시작 시 데이터베이스 테이블을 구성으로 읽어 들입니다. 이 공급자는 키별 기준으로 데이터베이스를 쿼리하지 않습니다.
* 변경 시 다시 로드가 구현되지 않으므로 앱 시작 후 데이터베이스를 업데이트해도 앱 구성에 영향을 주지 않습니다.

데이터베이스에 구성 값을 저장하는 `EFConfigurationValue` 엔터티를 정의합니다.

*Models/EFConfigurationValue.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

구성된 값을 저장 및 액세스하는 `EFConfigurationContext`를 추가합니다.

*EFConfigurationProvider/EFConfigurationContext.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<xref:Microsoft.Extensions.Configuration.IConfigurationSource>를 구현하는 클래스를 만듭니다.

*EFConfigurationProvider/EFConfigurationSource.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<xref:Microsoft.Extensions.Configuration.ConfigurationProvider>에서 상속하여 사용자 지정 구성 공급자를 만듭니다. 구성 공급자는 비어 있는 데이터베이스를 초기화합니다. [구성 키는 대/소문자를](#keys)구분하지 않으므로 데이터베이스를 초기화하는 데 사용되는 사전은 대/소문자를 구분하지 않는 비교자([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase))를 사용하여 생성됩니다.

*EFConfigurationProvider/EFConfigurationProvider.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

`AddEFConfiguration` 확장 메서드를 사용하여 구성 소스를 `ConfigurationBuilder`에 추가할 수 있습니다.

*Extensions/EntityFrameworkExtensions.cs*:

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

다음 코드는 *Program.cs*에서 사용자 지정 `EFConfigurationProvider`를 사용하는 방법을 보여 줍니다.

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a>시작하는 동안 구성에 액세스

`Startup.ConfigureServices`의 구성 값에 액세스하려면 `IConfiguration`을 `Startup` 생성자에 삽입합니다. `Startup.Configure`의 구성에 액세스하려면 `IConfiguration`을 메서드에 직접 삽입하거나 생성자의 인스턴스를 사용합니다.

```csharp
public class Startup
{
    private readonly IConfiguration _config;

    public Startup(IConfiguration config)
    {
        _config = config;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        var value = _config["key"];
    }

    public void Configure(IApplicationBuilder app, IConfiguration config)
    {
        var value = config["key"];
    }
}
```

시작 편의 메서드를 사용하여 구성에 액세스하는 방법의 예는 [앱 시작: 편리한 메서드](xref:fundamentals/startup#convenience-methods) 편을 참조하세요.

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a>Razor Pages 페이지 또는 MVC 뷰에서 구성에 액세스

Razor Pages 페이지나 MVC 뷰에서 구성 설정에 액세스하려면 [Microsoft.Extensions.Configuration 네임스페이스](xref:Microsoft.Extensions.Configuration)에 [using 지시문](xref:mvc/views/razor#using)([C# 참조: using 지시문](/dotnet/csharp/language-reference/keywords/using-directive))을 추가하고 <xref:Microsoft.Extensions.Configuration.IConfiguration>을 페이지 또는 뷰로 삽입합니다.

Razor 페이지에서:

```cshtml
@page
@model IndexModel
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index Page</title>
</head>
<body>
    <h1>Access configuration in a Razor Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

MVC 뷰에서:

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index View</title>
</head>
<body>
    <h1>Access configuration in an MVC view</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

## <a name="add-configuration-from-an-external-assembly"></a>외부 어셈블리의 구성 추가

<xref:Microsoft.AspNetCore.Hosting.IHostingStartup> 구현은 시작 시 앱의 `Startup` 클래스 외부에 있는 외부 어셈블리에서 앱에 향상된 기능을 추가할 수 있습니다. 자세한 내용은 <xref:fundamentals/configuration/platform-specific-configuration>를 참조하세요.

## <a name="additional-resources"></a>추가 자료

* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core의 앱 구성은 ‘구성 공급자’가 설정한 키-값 쌍을 기반으로 합니다.  구성 공급자는 다양한 구성 소스에서 구성 데이터를 키-값 쌍으로 읽어 들입니다.

* Azure Key Vault
* Azure App Configuration
* 명령줄 인수
* 사용자 지정 공급자(설치 또는 생성된)
* 디렉터리 파일
* 환경 변수
* 메모리 내 .NET 개체
* 설정 파일

일반적인 구성 공급자 시나리오에 대한 구성 패키지([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/))는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 포함됩니다.

샘플 앱에서 <xref:Microsoft.Extensions.Configuration> 네임스페이스를 사용하는 다음 코드 예제:

```csharp
using Microsoft.Extensions.Configuration;
```

‘옵션 패턴’은 이 항목에 설명된 구성 개념의 확장입니다.  옵션은 클래스를 사용하여 관련 설정 그룹을 나타냅니다. 자세한 내용은 <xref:fundamentals/configuration/options>를 참조하세요.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="host-versus-app-configuration"></a>호스트 대 앱 구성

앱을 구성하고 시작하기 전에 *호스트*를 구성하고 시작합니다. 호스트는 앱 시작 및 수명 관리를 담당합니다. 앱과 호스트 모두 이 항목에서 설명하는 구성 관리자를 사용하여 구성합니다. 호스트 구성 키-값 쌍은 앱의 구성에도 포함됩니다. 호스트를 빌드할 때 구성 공급자를 사용하는 방법과 구성 소스가 호스트 구성에 미치는 영향에 대한 자세한 내용은 <xref:fundamentals/index#host>를 참조하세요.

## <a name="other-configuration"></a>기타 구성

이 항목에서는 *앱 구성*에 관련된 내용만 다룹니다. ASP.NET Core 앱을 실행하고 호스팅하는 다른 요소는 이 항목에서 다루지 않는 구성 파일을 사용하여 구성됩니다.

* *launch.json*/*launchSettings.json*은 다음에 설명된 개발 환경에 대한 도구 구성 파일입니다.
  * <xref:fundamentals/environments#development> 문서 내
  * 개발 시나리오에서 ASP.NET Core 앱을 구성하는 데 해당 파일이 사용되는 설명서 집합 전체
* *web.config*는 다음 항목에 설명된 서버 구성 파일입니다.
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

이전 버전의 ASP.NET에서 앱 구성을 마이그레이션하는 방법에 대한 자세한 정보는 <xref:migration/proper-to-2x/index#store-configurations>를 참조하세요.

## <a name="default-configuration"></a>기본 구성

호스트를 빌드할 때 ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) 템플릿을 기반으로 하는 웹앱은 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>를 호출합니다. `CreateDefaultBuilder`는 다음 순서로 앱에 대한 기본 구성을 제공합니다.

[웹호스트](xref:fundamentals/host/web-host)를 사용하는 앱에는 다음이 적용됩니다. [제네릭 호스트](xref:fundamentals/host/generic-host) 사용 시 기본 구성에 대한 자세한 내용은 [이 토픽의 최신 버전](xref:fundamentals/configuration/index)을 참조하세요.

* 호스트 구성은 다음에 의해 제공됩니다.
  * [환경 변수 구성 공급자](#environment-variables-configuration-provider)를 사용하여 `ASPNETCORE_`를 접두사로 사용하는 환경 변수(예: `ASPNETCORE_ENVIRONMENT`) 구성 키-값 쌍이 로드되면 접두사(`ASPNETCORE_`)는 제거됩니다.
  * [명령줄 구성 공급자](#command-line-configuration-provider)를 사용하는 명령줄 인수
* 앱 구성은 다음에 의해 제공됩니다.
  * [파일 구성 공급자](#file-configuration-provider)를 사용하는 *appsettings.json*
  * [파일 구성 공급자](#file-configuration-provider)를 사용하는 *appsettings.{Environment}.json*
  * 앱이 항목 어셈블리를 사용하여 `Development` 환경에서 실행되는 경우 [Secret Manager](xref:security/app-secrets)입니다.
  * [환경 변수 구성 공급 기업](#environment-variables-configuration-provider)을 사용하는 환경 변수
  * [명령줄 구성 공급자](#command-line-configuration-provider)를 사용하는 명령줄 인수

## <a name="security"></a>보안

중요한 구성 데이터를 보호하려면 다음 방법을 채택합니다.

* 구성 공급자 코드 또는 일반 텍스트 구성 파일에 암호 또는 기타 중요한 데이터를 절대 저장하지 마세요.
* 개발 또는 테스트 환경에서 프로덕션 비밀을 사용하지 마세요.
* 의도치 않게 소스 코드 리포지토리에 커밋되는 일이 없도록 프로젝트 외부에서 비밀을 지정하세요.

자세한 내용은 다음 항목을 참조하세요.

* <xref:fundamentals/environments>
* <xref:security/app-secrets> &ndash; 환경 변수를 사용하여 중요한 데이터를 저장하는 방법에 대한 조언을 포함합니다. 비밀 관리자는 파일 구성 공급자를 사용하여 사용자 비밀을 로컬 시스템의 JSON 파일에 저장합니다. 파일 구성 공급자에 대해서는 이 항목의 뒷부분에서 설명합니다.

[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)가 ASP.NET Core 앱에 대한 앱 비밀을 안전하게 저장합니다. 자세한 내용은 <xref:security/key-vault-configuration>를 참조하세요.

## <a name="hierarchical-configuration-data"></a>계층적 구성 데이터

구성 API에서는 구성 키에 구분 기호를 사용해 계층적 데이터를 평면화하여 계층적 구성 데이터를 유지 관리할 수 있습니다.

다음 JSON 파일에서는 두 섹션으로 이루어진 구조적 계층에 네 개의 키가 있습니다.

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  }
}
```

파일을 구성으로 읽어 들이면 구성 소스의 원래 계층적 데이터 구조를 유지하기 위해 고유 키가 생성됩니다. 콜론(`:`)을 사용해 섹션과 키를 평면화하여 원래 구조를 유지합니다.

* section0:key0
* section0:key1
* section1:key0
* section1:key1

구성 데이터에서는 <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> 및 <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> 메서드를 사용하여 섹션과 섹션의 자식을 격리할 수 있습니다. 이러한 메서드에 대해서는 나중에 [GetSection, GetChildren 및 Exists](#getsection-getchildren-and-exists)에서 설명합니다.

## <a name="conventions"></a>규칙

### <a name="sources-and-providers"></a>소스 및 공급자

앱 시작 시 구성 공급자에서 지정한 순서로 구성 소스를 읽습니다.

변경 검색을 구현하는 구성 공급자는 기본 설정 파일이 변경되면 구성을 다시 로드할 수 있습니다. 예를 들어 파일 구성 공급자(이 항목의 뒷부분에서 설명됨) 및 [Azure Key Vault 구성 공급자](xref:security/key-vault-configuration)는 변경 검색을 구현합니다.

<xref:Microsoft.Extensions.Configuration.IConfiguration>은 앱의 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 컨테이너에서 사용할 수 있습니다. <xref:Microsoft.Extensions.Configuration.IConfiguration>을 Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> 또는 MVC <xref:Microsoft.AspNetCore.Mvc.Controller>에 삽입하여 클래스에 대한 구성을 가져올 수 있습니다.

다음 예제에서는 `_config` 필드가 구성 값에 액세스하는 데 사용됩니다.

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }
}
```

```csharp
public class HomeController : Controller
{
    private readonly IConfiguration _config;

    public HomeController(IConfiguration config)
    {
        _config = config;
    }
}
```

구성 공급자는 호스트에서 설정될 때 DI가 제공되지 않으므로 DI를 활용할 수 없습니다.

### <a name="keys"></a>키

구성키는 다음 규칙을 따릅니다.

* 키는 대/소문자를 구분하지 않습니다. 예를 들어 `ConnectionString`과 `connectionstring`은 동일한 키로 처리됩니다.
* 같은 키의 값을 같거나 다른 여러 구성 공급자에서 설정하는 경우 키에 설정된 마지막 값이 사용되는 값입니다.
* 계층적 키
  * 구성 API 내에서는 콜론 구분 기호(`:`)가 모든 플랫폼에 적용됩니다.
  * 환경 변수에서는 콜론 구분 기호가 일부 플랫폼에 적용되지 않을 수 있습니다. 두 개의 밑줄(`__`)은 모든 플랫폼에서 지원되며 콜론으로 자동 변환됩니다.
  * Azure Key Vault에서 계층적 키는 `--`(두 개의 대시)를 구분 기호로 사용합니다. 비밀을 앱의 구성으로 로드할 때 대시를 콜론으로 바꾸는 코드를 작성하세요.
* <xref:Microsoft.Extensions.Configuration.ConfigurationBinder>는 구성 키에 배열 인덱스를 사용하여 배열을 개체에 바인딩하는 것을 지원합니다. 배열 바인딩에 대해서는 [클래스에 배열 바인딩](#bind-an-array-to-a-class) 섹션에서 설명합니다.

### <a name="values"></a>값

구성 값은 다음 규칙을 따릅니다.

* 값은 문자열입니다.
* Null 값은 구성에 저장하거나 개체에 바인딩할 수 없습니다.

## <a name="providers"></a>공급자

다음 표에서는 ASP.NET Core 앱에서 사용할 수 있는 구성 공급자를 보여 줍니다.

| 공급자 | 다음에서 구성 제공&hellip; |
| -------- | ----------------------------------- |
| [Azure Key Vault 구성 공급자](xref:security/key-vault-configuration)(‘보안’ 항목)  | Azure Key Vault |
| [Azure App Configuration 공급자](/azure/azure-app-configuration/quickstart-aspnet-core-app)(Azure 설명서) | Azure App Configuration |
| [명령줄 구성 공급자](#command-line-configuration-provider) | 명령줄 매개 변수 |
| [사용자 지정 구성 공급자](#custom-configuration-provider) | 사용자 지정 소스 |
| [환경 변수 구성 공급자](#environment-variables-configuration-provider) | 환경 변수 |
| [파일 구성 공급자](#file-configuration-provider) | 파일(INI, JSON, XML) |
| [파일별 키 구성 공급자](#key-per-file-configuration-provider) | 디렉터리 파일 |
| [메모리 구성 공급자](#memory-configuration-provider) | 메모리 내 컬렉션 |
| [사용자 비밀(비밀 관리자)](xref:security/app-secrets)(‘보안’ 항목)  | 사용자 프로필 디렉터리의 파일 |

시작 시 구성 공급자에서 지정한 순서로 구성 소스를 읽습니다. 이 항목의 구성 공급자는 코드에서 정렬하는 순서가 아니라 사전 순으로 설명되어 있습니다. 앱에 필요한 기본 구성 소스에 대한 우선 순위에 맞게 구성 공급자를 코드에 정렬하세요.

구성 공급자의 일반적인 순서는 다음과 같습니다.

1. 파일(*appsettings.json*, *appsettings.{Environment}.json*, 여기서 `{Environment}`는 앱의 현재 호스팅 환경)
1. [Azure Key Vault](xref:security/key-vault-configuration)
1. [사용자 비밀(비밀 관리자)](xref:security/app-secrets)(개발 환경에서만)
1. 환경 변수
1. 명령줄 인수

일반적인 방식은 명령줄 구성 공급자를 일련의 공급자에서 마지막에 배치하는 것이므로, 다른 공급자에서 설정한 구성을 명령줄 인수로 재정의할 수 있습니다.

`CreateDefaultBuilder`로 새 호스트 작성기가 초기화될 때 이전 공급자 시퀀스가 사용됩니다. 자세한 내용은 [기본 구성](#default-configuration) 섹션을 참조하세요.

## <a name="configure-the-host-builder-with-useconfiguration"></a>UseConfiguration을 사용하여 호스트 작성기 구성

호스트 작성기를 구성하려면 구성과 함께 호스트 작성기에서 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>을 호출합니다.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

    var config = new ConfigurationBuilder()
        .AddInMemoryCollection(dict)
        .Build();

    return WebHost.CreateDefaultBuilder(args)
        .UseConfiguration(config)
        .UseStartup<Startup>();
}
```

## <a name="configureappconfiguration"></a>ConfigureAppConfiguration

호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출하여 `CreateDefaultBuilder`에 의해 자동으로 추가된 구성 공급자 외에도 앱의 구성 공급자를 지정합니다.

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a>명령줄 인수를 사용하여 이전 구성 재정의

명령줄 인수를 사용하여 재정의할 수 있는 앱 구성을 제공하려면 마지막으로 `AddCommandLine`을 호출합니다.

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a>CreateDefaultBuilder에 의해 추가된 공급자 제거

`CreateDefaultBuilder`에 의해 추가된 공급자를 제거하려면 먼저 [IConfigurationBuilder](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources)에서 [Clear](/dotnet/api/system.collections.generic.icollection-1.clear)를 호출합니다.

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a>앱 시작 중 구성 사용

`Startup.ConfigureServices`를 포함하여 `ConfigureAppConfiguration`에서 앱에 제공되는 구성은 앱을 시작하는 동안 사용할 수 있습니다. 자세한 내용은 [시작하는 동안 구성에 액세스](#access-configuration-during-startup) 섹션을 참조하세요.

## <a name="command-line-configuration-provider"></a>명령줄 구성 공급자

<xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider>은 런타임 시 명령줄 인수 키-값 쌍에서 구성을 로드합니다.

명령줄 구성을 활성화하기 위해 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 확장 메서드가 호출됩니다.

`CreateDefaultBuilder(string [])`가 호출되면 `AddCommandLine`도 자동으로 호출됩니다. 자세한 내용은 [기본 구성](#default-configuration) 섹션을 참조하세요.

`CreateDefaultBuilder`는 다음 항목도 로드합니다.

* *appsettings.json* 및 *appsettings.{Environment}.json* 파일에서 선택적 구성
* [사용자 비밀(비밀 관리자)](xref:security/app-secrets)(개발 환경에서)
* 환경 변수.

`CreateDefaultBuilder`는 명령줄 구성 공급자를 마지막에 추가합니다. 따라서 런타임에 전달된 명령줄 인수가 다른 공급자에서 설정한 구성을 재정의합니다.

`CreateDefaultBuilder`는 호스트를 생성할 때 작동합니다. 따라서 `CreateDefaultBuilder`에서 활성화한 명령줄 구성이 호스트 생성 방식에 영향을 줄 수 있습니다.

ASP.NET Core 템플릿을 기반으로 하는 앱의 경우 `AddCommandLine`은 `CreateDefaultBuilder`에서 이미 호출되었습니다. 추가 구성 공급자를 추가하고 명령줄 인수를 사용하여 해당 공급자의 구성을 재정의하는 기능을 유지하려면 `ConfigureAppConfiguration`에서 앱의 추가 공급자를 호출하고 마지막으로 `AddCommandLine`을 호출합니다.

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

**예제**

샘플 앱은 정적 편의 메서드 `CreateDefaultBuilder`를 활용하여 호스트를 빌드하며, <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 호출도 포함합니다.

1. 프로젝트의 디렉터리에서 명령 프롬프트를 엽니다.
1. `dotnet run` 명령에 명령줄 인수를 제공합니다(`dotnet run CommandLineKey=CommandLineValue`).
1. 앱이 실행되면 브라우저를 열어 `http://localhost:5000`의 앱으로 이동합니다.
1. `dotnet run`에 제공한 구성 명령줄 인수에 대한 키-값 쌍이 출력에 포함되어 있는지 확인합니다.

### <a name="arguments"></a>인수

값은 등호(`=`) 다음에 와야 합니다. 또는 값이 공백 다음에 오는 경우 키에 접두사(`--` 또는 `/`)가 있어야 합니다. 등호를 사용하는 경우 값이 필요하지 않습니다(예: `CommandLineKey=`).

| 키 접두사               | 예제                                                |
| ------------------------ | ------------------------------------------------------ |
| 접두사 없음                | `CommandLineKey1=value1`                               |
| 대시 2개(`--`)        | `--CommandLineKey2=value2`, `--CommandLineKey2 value2` |
| 슬래시(`/`)      | `/CommandLineKey3=value3`, `/CommandLineKey3 value3`   |

같은 명령 내에서 등호를 사용하는 명령줄 인수 키-값 쌍을 공백을 사용하는 키-값 쌍과 함께 사용하지 마세요.

명령 예:

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a>스위치 매핑

스위치 매핑은 키 이름 교체 논리를 지원합니다. <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>를 사용하여 구성을 수동으로 빌드하는 경우에는 <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> 메서드에 대체 스위치를 포함하는 사전을 제공하세요.

스위치 매핑 사전을 사용하면 명령줄 인수를 통해 제공된 키와 일치하는 키에 대해 사전을 검사합니다. 사전에서 명령줄 키가 발견되면 사전 값(대체 키)이 다시 전달되어 앱 구성의 키-값 쌍이 설정됩니다. 단일 대시(`-`) 접두사가 붙은 명령줄 키에는 스위치 매핑이 필수입니다.

스위치 매핑 사전 키 규칙:

* 스위치는 단일 대시(`-`) 또는 이중 대시(`--`)로 시작해야 합니다.
* 스위치 매핑 사전이 중복 키를 포함하면 안 됩니다.

스위치 매핑 사전을 만듭니다. 다음 예에서는 두 개의 스위치 매핑이 만들어집니다.

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

호스트가 빌드될 때 스위치 매핑 사전과 함께 `AddCommandLine`을 호출합니다.

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

스위치 매핑을 사용하는 앱의 경우 `CreateDefaultBuilder`에 대한 호출은 인수를 전달하지 않아야 합니다. `CreateDefaultBuilder` 메서드의 `AddCommandLine` 호출에는 매핑된 스위치가 포함되지 않으며 `CreateDefaultBuilder`에 스위치 매핑 사전을 전달할 방법은 없습니다. 해결 방법으로 `CreateDefaultBuilder`에 인수를 전달하지 않고 대신 `ConfigurationBuilder` 메서드의 `AddCommandLine` 메서드에서 인수와 스위치 매핑 사전을 모두 처리하도록 할 수 있습니다.

생성된 스위치 매핑 사전은 다음 표의 데이터를 포함합니다.

| Key       | 값             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

앱을 시작할 때 스위치 매핑된 키가 사용되는 경우 구성은 사전에서 제공하는 키의 구성 값을 수신합니다.

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

앞의 명령을 실행한 후 구성에는 다음 표에 표시된 값이 포함됩니다.

| Key               | 값    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a>환경 변수 구성 공급자

<xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider>는 런타임에 환경 변수 키-값 쌍에서 구성을 로드합니다.

환경 변수 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 확장 메서드를 호출합니다.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

[Azure App Service](https://azure.microsoft.com/services/app-service/)를 사용하면 Azure Portal에서 환경 변수를 설정할 수 있으므로 환경 변수 구성 공급자를 사용한 앱 구성을 재정의할 수 있습니다. 자세한 내용은 [Azure 앱: Azure Portal을 사용하여 앱 구성 재정의](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal) 편을 참조하세요.

새 호스트 작성기가 [웹 호스트](xref:fundamentals/host/web-host)로 초기화되고 `CreateDefaultBuilder`가 호출될 때 `AddEnvironmentVariables`는 [호스트 구성](#host-versus-app-configuration)에 대해 접두사가 `ASPNETCORE_`인 환경 변수를 로드하는 데 사용됩니다. 자세한 내용은 [기본 구성](#default-configuration) 섹션을 참조하세요.

`CreateDefaultBuilder`는 다음 항목도 로드합니다.

* 접두사가 없는 `AddEnvironmentVariables`의 호출을 통한 접두사가 없는 환경 변수의 앱 구성
* *appsettings.json* 및 *appsettings.{Environment}.json* 파일에서 선택적 구성
* [사용자 비밀(비밀 관리자)](xref:security/app-secrets)(개발 환경에서)
* 명령줄 인수.

사용자 비밀 및 *appsettings* 파일을 통해 구성을 설정한 후 환경 변수 구성 공급자를 호출합니다. 이 위치에서 공급자를 호출하면 런타임에 환경 변수를 읽어 들여 사용자 비밀 및 *appsettings* 파일로 설정한 구성을 재정의할 수 있습니다.

추가 환경 변수에서 앱 구성을 제공하려면 `ConfigureAppConfiguration`에서 앱의 추가 공급자를 호출하고 접두사가 있는 `AddEnvironmentVariables`를 호출합니다.

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

지정된 접두사를 포함하는 환경 변수에서 다른 공급자의 값을 재정의할 수 있도록 하려면 `AddEnvironmentVariables`를 마지막으로 호출합니다.

**예제**

샘플 앱은 정적 편의 메서드 `CreateDefaultBuilder`를 활용하여 호스트를 빌드하며, `AddEnvironmentVariables` 호출도 포함합니다.

1. 샘플 앱을 실행합니다. 브라우저를 열어 `http://localhost:5000`의 앱으로 이동합니다.
1. 환경 변수 `ENVIRONMENT`에 대한 키-값 쌍이 출력에 포함되는지 확인합니다. 이 값은 앱이 실행 중인 환경을 나타내며, 로컬에서 실행 중이면 일반적으로 `Development` 입니다.

앱은 렌더링하는 환경 변수 목록을 짧게 유지하기 위해 환경 변수를 필터링합니다. 샘플 앱의 *Pages/Index.cshtml.cs* 파일을 참조하세요.

앱에서 사용할 수 있는 모든 환경 변수를 표시하려면 *Pages/Index.cshtml.cs*에서 `FilteredConfiguration`을 다음으로 변경합니다.

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a>접두사

`AddEnvironmentVariables` 메서드에 접두사를 제공하면 앱의 구성에 로드된 환경 변수가 필터링됩니다. 예를 들어 환경 변수를 `CUSTOM_` 접두사로 필터링하려면 구성 공급자에 이 접두사를 제공합니다.

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

구성 키-값 쌍이 생성되면 접두사는 제거됩니다.

호스트 작성기를 만들 때 환경 변수에서 호스트 구성을 제공합니다. 환경 변수에 사용되는 접두사에 대한 자세한 내용은 [기본 구성](#default-configuration) 섹션을 참조하세요.

**연결 문자열 접두사**

구성 API에는 앱 환경에 대한 Azure 연결 문자열 구성과 관련된 네 개의 연결 문자열 환경 변수에 대한 특별한 처리 규칙이 있습니다. 즉, `AddEnvironmentVariables`에 접두사를 제공하지 않으면 표에 표시된 접두사가 붙은 환경 변수가 앱으로 로드됩니다.

| 연결 문자열 접두사 | 공급자 |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | 사용자 지정 공급자 |
| `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |

표에 표시된 네 개 접두사 중 하나가 붙은 환경 변수가 검색되어 구성으로 로드되면 다음과 같이 됩니다.

* 환경 변수 접두사를 제거하고 구성 키 섹션(`ConnectionStrings`)을 추가하여 구성 키가 생성됩니다.
* 데이터베이스 연결 제공자(지정된 공급자가 없는 `CUSTOMCONNSTR_` 제외)를 나타내는 새 구성 키-값 쌍이 생성됩니다.

| 환경 변수 키 | 변환된 구성 키 | 공급자 구성 항목                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | 구성 항목이 생성되지 않습니다.                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | 키: `ConnectionStrings:{KEY}_ProviderName`:<br>값: `MySql.Data.MySqlClient` |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | 키: `ConnectionStrings:{KEY}_ProviderName`:<br>값: `System.Data.SqlClient`  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | 키: `ConnectionStrings:{KEY}_ProviderName`:<br>값: `System.Data.SqlClient`  |

**예제**

서버에 사용자 지정 연결 문자열 환경 변수가 만들어집니다.

* 이름 &ndash; `CUSTOMCONNSTR_ReleaseDB`
* 값 &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`

`IConfiguration`이 삽입되고 `_config`라는 필드에 할당된 경우 값을 읽습니다.

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a>파일 구성 공급자

<xref:Microsoft.Extensions.Configuration.FileConfigurationProvider>는 파일 시스템에서 구성을 로드하기 위한 기본 클래스입니다. 다음 구성 공급자는 특정 파일 형식 전용입니다.

* [INI 구성 공급자](#ini-configuration-provider)
* [JSON 구성 공급자](#json-configuration-provider)
* [XML 구성 공급자](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a>INI 구성 공급자

<xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider>는 런타임에 INI 파일 키-값 쌍에서 구성을 로드합니다.

INI 파일 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> 확장 메서드를 호출합니다.

콜론은 INI 파일 구성에서 콜론을 섹션 구분 기호로 사용할 수 있습니다.

오버로드에서는 다음을 지정할 수 있습니다.

* 파일이 선택 사항인지 여부
* 파일이 변경되면 구성을 다시 로드하는지 여부
* 파일에 액세스하는 데 사용되는 <xref:Microsoft.Extensions.FileProviders.IFileProvider>

호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출하여 앱의 구성을 지정합니다.

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

INI 구성 파일의 일반적인 예:

```ini
[section0]
key0=value
key1=value

[section1]
subsection:key=value

[section2:subsection0]
key=value

[section2:subsection1]
key=value
```

이전 구성 파일은 `value`와 함께 다음 키를 로드합니다.

* section0:key0
* section0:key1
* section1:subsection:key
* section2:subsection0:key
* section2:subsection1:key

### <a name="json-configuration-provider"></a>JSON 구성 공급자

<xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider>는 런타임에 JSON 파일 키-값 쌍에서 구성을 로드합니다.

JSON 파일 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> 확장 메서드를 호출합니다.

오버로드에서는 다음을 지정할 수 있습니다.

* 파일이 선택 사항인지 여부
* 파일이 변경되면 구성을 다시 로드하는지 여부
* 파일에 액세스하는 데 사용되는 <xref:Microsoft.Extensions.FileProviders.IFileProvider>

`CreateDefaultBuilder`를 사용하여 새 호스트 작성기를 초기화할 때 `AddJsonFile`이 자동으로 두 번 호출됩니다. 이 메서드는 호출되면 다음에서 구성을 로드합니다.

* *appsettings.json* &ndash; 이 파일을 먼저 읽습니다. 파일의 환경 버전이 *appsettings.json* 파일에서 제공한 값을 재정의할 수 있습니다.
* *appsettings.{Environment}.json* &ndash; 파일의 환경 버전은 [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*)을 기반으로 로드됩니다.

자세한 내용은 [기본 구성](#default-configuration) 섹션을 참조하세요.

`CreateDefaultBuilder`는 다음 항목도 로드합니다.

* 환경 변수.
* [사용자 비밀(비밀 관리자)](xref:security/app-secrets)(개발 환경에서)
* 명령줄 인수.

JSON 구성 공급자를 먼저 설정합니다. 따라서 사용자 비밀, 환경 변수 및 명령줄 인수가 *appsettings* 파일에서 설정한 구성을 재정의할 수 있습니다.

*appsettings.json* 및 *appsettings.{Environment}.json* 이외의 파일에 대한 앱 구성을 지정하도록 호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출합니다.

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

**예제**

샘플 앱은 정적 편의 메서드 `CreateDefaultBuilder`를 활용하여 호스트를 빌드하며, `AddJsonFile` 두 번 호출도 포함합니다.

* `AddJsonFile`에 대한 첫 번째 호출은 *appsettings.json*에서 구성을 로드합니다.

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* `AddJsonFile`에 대한 두 번째 호출은 *appsettings.{Environment}.json*에서 구성을 로드합니다. 샘플 앱의 *appsettings.Development.json*의 경우 다음 파일이 로드됩니다.

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. 샘플 앱을 실행합니다. 브라우저를 열어 `http://localhost:5000`의 앱으로 이동합니다.
1. 출력에는 앱 환경을 기반으로 하는 구성에 대한 키 값 쌍이 포함되어 있습니다. 개발 환경에서 앱을 실행할 때 `Logging:LogLevel:Default` 키의 로그 수준은 `Debug`입니다.
1. 프로덕션 환경에서 샘플 앱을 다시 실행합니다.
   1. *Properties/launchSettings.json* 파일을 엽니다.
   1. `ConfigurationSample` 프로필에서 `ASPNETCORE_ENVIRONMENT` 환경 변수의 값을 `Production`으로 변경합니다.
   1. 파일을 저장하고 명령 셸에서 `dotnet run`를 사용하여 앱을 실행합니다.
1. *appsettings.Development.json*의 설정에서 더 이상 *appsettings.json*의 설정을 재정의하지 않습니다. `Logging:LogLevel:Default` 키의 로그 수준은 `Warning`입니다.

### <a name="xml-configuration-provider"></a>XML 구성 공급자

<xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider>는 런타임에 XML 파일 키-값 쌍에서 구성을 로드합니다.

XML 파일 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> 확장 메서드를 호출합니다.

오버로드에서는 다음을 지정할 수 있습니다.

* 파일이 선택 사항인지 여부
* 파일이 변경되면 구성을 다시 로드하는지 여부
* 파일에 액세스하는 데 사용되는 <xref:Microsoft.Extensions.FileProviders.IFileProvider>

구성 키-값 쌍이 생성되면 구성 파일의 루트 노드는 무시됩니다. 파일에 DTD(문서 종류 정의)나 네임스페이스는 지정하지 마세요.

호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출하여 앱의 구성을 지정합니다.

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

XML 구성 파일에서는 반복 섹션에 고유 요소 이름을 사용할 수 있습니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section0>
    <key0>value</key0>
    <key1>value</key1>
  </section0>
  <section1>
    <key0>value</key0>
    <key1>value</key1>
  </section1>
</configuration>
```

이전 구성 파일은 `value`와 함께 다음 키를 로드합니다.

* section0:key0
* section0:key1
* section1:key0
* section1:key1

같은 요소 이름을 사용하는 반복 요소는 다음과 같이 `name` 특성을 사용하여 요소를 구분하면 작동합니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section name="section0">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
  <section name="section1">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
</configuration>
```

이전 구성 파일은 `value`와 함께 다음 키를 로드합니다.

* section:section0:key:key0
* section:section0:key:key1
* section:section1:key:key0
* section:section1:key:key1

특성을 사용하여 값을 제공할 수 있습니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

이전 구성 파일은 `value`와 함께 다음 키를 로드합니다.

* key:attribute
* section:key:attribute

## <a name="key-per-file-configuration-provider"></a>파일별 키 구성 공급자

<xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider>는 디렉터리의 파일을 구성 키-값 쌍으로 사용합니다. 키는 파일 이름이고, 값은 파일의 콘텐츠를 포함합니다. 파일별 키 구성 공급자는 Docker 호스팅 시나리오에서 사용됩니다.

파일별 키 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> 확장 메서드를 호출합니다. 파일의 `directoryPath`는 절대 경로여야 합니다.

오버로드에서는 다음을 지정할 수 있습니다.

* 소스를 구성하는 `Action<KeyPerFileConfigurationSource>` 대리자
* 디렉터리가 선택 사항인지 여부와 디렉터리의 경로

두 개의 밑줄(`__`)은 파일 이름에서 구성 키 구분 기호로 사용됩니다. 예를 들어, 파일 이름 `Logging__LogLevel__System`은 구성 키 `Logging:LogLevel:System`을 생성합니다.

호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출하여 앱의 구성을 지정합니다.

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a>메모리 구성 공급자

<xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider>는 메모리 내 컬렉션을 구성 키-값 쌍으로 사용합니다.

메모리 내 컬렉션 구성을 활성화하려면 <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> 인스턴스에서 <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> 확장 메서드를 호출합니다.

`IEnumerable<KeyValuePair<String,String>>`을 사용하여 구성 공급자를 초기화할 수 있습니다.

호스트를 빌드할 때 `ConfigureAppConfiguration`을 호출하여 앱의 구성을 지정합니다.

다음 예제에서는 구성 사전이 만들어집니다.

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

사전은 `AddInMemoryCollection`을 호출하여 구성을 제공하는 데 사용됩니다.

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a>GetValue

[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*)는 지정된 키를 사용하여 구성에서 하나의 값을 추출하고 해당 값을 지정된 비컬렉션 형식으로 변환합니다. 오버로드는 기본값을 허용합니다.

다음 예제가 하는 일:

* `NumberKey` 키를 사용하여 구성에서 문자열 값을 추출합니다. 구성 키에서 `NumberKey`를 찾을 수 없으면 `99`의 기본값이 사용됩니다.
* 값을 `int` 형식으로 입력합니다.
* 페이지에 사용할 값을 `NumberConfig` 속성에 저장합니다.

```csharp
public class IndexModel : PageModel
{
    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    public int NumberConfig { get; private set; }

    public void OnGet()
    {
        NumberConfig = _config.GetValue<int>("NumberKey", 99);
    }
}
```

## <a name="getsection-getchildren-and-exists"></a>GetSection, GetChildren 및 Exists

이어지는 예제에서는 다음 JSON 파일을 고려하세요. 네 개의 키가 두 개 섹션에 있고, 두 섹션 중 하나에는 하위 섹션 쌍이 포함되어 있습니다.

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  },
  "section2": {
    "subsection0" : {
      "key0": "value",
      "key1": "value"
    },
    "subsection1" : {
      "key0": "value",
      "key1": "value"
    }
  }
}
```

파일을 구성으로 읽어 들이면 구성 값을 저장하기 위해 다음과 같은 고유한 계층 키가 생성됩니다.

* section0:key0
* section0:key1
* section1:key0
* section1:key1
* section2:subsection0:key0
* section2:subsection0:key1
* section2:subsection1:key0
* section2:subsection1:key1

### <a name="getsection"></a>GetSection

[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*)은 지정된 하위 섹션 키를 사용하여 구성 하위 섹션을 추출합니다.

`section1`의 키-값 쌍만 포함하는 <xref:Microsoft.Extensions.Configuration.IConfigurationSection>을 반환하려면 `GetSection`을 호출하고 섹션 이름을 제공합니다.

```csharp
var configSection = _config.GetSection("section1");
```

`configSection`에는 값이 포함되지 않고 키 및 경로만 포함됩니다.

마찬가지로 `section2:subsection0`의 키 값을 가져오려면 `GetSection`을 호출하고 섹션 경로를 제공합니다.

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

`GetSection`은 `null`을 반환하지 않습니다. 일치하는 섹션을 찾을 수 없으면 빈 `IConfigurationSection`이 반환됩니다.

`GetSection`이 일치하는 섹션을 반환할 때 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value>는 채워지지 않습니다. 섹션이 존재하면 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> 및 <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path>가 반환됩니다.

### <a name="getchildren"></a>GetChildren

`section2`에서 [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*)을 호출하면 다음을 포함하는 `IEnumerable<IConfigurationSection>`이 반환됩니다.

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a>있음

[ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*)를 사용하면 구성 섹션이 있는지 확인할 수 있습니다.

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

예제 데이터의 경우 구성 데이터에 `section2:subsection2` 섹션이 없으므로 `sectionExists`는 `false`입니다.

## <a name="bind-to-a-class"></a>클래스에 바인딩

‘옵션 패턴’을 사용하여 관련 설정 그룹을 나타내는 클래스에 구성을 바인딩할 수 있습니다.  자세한 내용은 <xref:fundamentals/configuration/options>를 참조하세요.

구성 값이 문자열로 반환되지만, <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>를 호출하면 [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) 개체를 생성할 수 있습니다. 바인더는 제공된 형식의 모든 공용 읽기/쓰기 속성에 값을 바인딩합니다. 필드가 바인딩되지 **않았습니다**.

샘플 앱은 `Starship` 모델(*Models/Starship.cs*)을 포함합니다.

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

샘플 앱이 JSON 구성 공급자를 사용하여 구성을 로드하는 경우 *starship.json* 파일의 `starship` 섹션에서 구성을 생성합니다.

[!code-json[](index/samples/2.x/ConfigurationSample/starship.json)]

다음 구성 키-값 쌍이 생성됩니다.

| Key                   | 값                                             |
| --------------------- | ------------------------------------------------- |
| starship:name         | USS Enterprise                                    |
| starship:registry     | NCC-1701                                          |
| starship:class        | Constitution                                      |
| starship:length       | 304.8                                             |
| starship:commissioned | False                                             |
| trademark             | Paramount Pictures Corp. https://www.paramount.com |

샘플 앱은 `starship` 키를 사용하여 `GetSection`을 호출합니다. `starship` 키-값 쌍은 격리됩니다. `Bind` 메서드는 `Starship` 클래스의 인스턴스를 전달하는 하위 섹션에서 호출됩니다. 인스턴스 값을 바인딩한 후 인스턴스는 렌더링할 속성에 할당됩니다.

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

## <a name="bind-to-an-object-graph"></a>개체 그래프에 바인딩

<xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>는 전체 POCO 개체 그래프를 바인딩할 수 있습니다. 단순 개체 바인딩과 마찬가지로 공용 읽기/쓰기 속성만 바인딩되었습니다.

다음 샘플은 개체 그래프에`Metadata` 및 `Actors` 클래스가 포함된 `TvShow` 모델(*Models/TvShow.cs*)을 포함합니다.

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

샘플 앱의 *tvshow.xml* 파일에는 다음 구성 데이터가 포함됩니다.

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

`Bind` 메서드를 사용하여 전체 `TvShow` 개체 그래프에 구성을 바인딩합니다. 바인딩된 인스턴스는 렌더링할 속성에 할당됩니다.

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)는 지정된 형식을 바인딩하고 반환합니다. `Get<T>`가 `Bind`보다 사용하기 더 간편합니다. 다음 코드에서는 앞의 예제에 `Get<T>`를 사용하여 바인딩된 인스턴스가 렌더링에 사용되는 속성에 바로 할당될 수 있도록 하는 방법을 보여 줍니다.

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a>클래스에 배열 바인딩

다음 샘플 앱은 이 섹션에서 설명하는 개념을 보여 줍니다. 

<xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*>는 구성 키에 배열 인덱스를 사용하여 배열을 개체에 바인딩하는 것을 지원합니다. 숫자 키 세그먼트(`:0:`, `:1:`, &hellip; `:{n}:`)를 노출하는 모든 배열 형식은 POCO 클래스 배열에 배열 바인딩할 수 있습니다.

> [!NOTE]
> 바인딩은 규칙에 따라 제공됩니다. 배열 바인딩을 구현하기 위해 사용자 지정 구성 공급자가 필요하지는 않습니다.

**메모리 내 배열 처리**

다음 표에 표시된 구성 키 및 값을 사용하세요.

| Key             | 값  |
| :-------------: | :----: |
| array:entries:0 | value0 |
| array:entries:1 | value1 |
| array:entries:2 | value2 |
| array:entries:4 | value4 |
| array:entries:5 | value5 |

메모리 구성 공급자를 사용하는 샘플 앱에서는 다음과 같은 키 및 값이 로드됩니다.

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

배열은 인덱스 &num;3에 대한 값을 건너뜁니다. 구성 바인더는 null 값을 바인딩하거나 바인딩된 개체에 null 항목을 만들 수 없으며, 이 점은 이 배열을 개체에 바인딩한 결과를 설명할 때 명확해집니다.

샘플 앱에서는 POCO 클래스를 사용하여 바인딩된 구성 데이터를 저장할 수 있습니다.

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

구성 데이터는 개체에 바인딩됩니다.

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 구문도 사용할 수 있으며, 이 경우 코드가 더 간결해집니다.

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

바인딩된 개체 즉, `ArrayExample`의 인스턴스는 구성에서 배열 데이터를 받습니다.

| `ArrayExample.Entries` 인덱스 | `ArrayExample.Entries` 값 |
| :--------------------------: | :--------------------------: |
| 0                            | value0                       |
| 1                            | value1                       |
| 2                            | value2                       |
| 3                            | value4                       |
| 4                            | value5                       |

바인딩된 개체의 인덱스 &num;3은 `array:4` 구성 키에 대한 구성 데이터와 `value4`의 값을 포함합니다. 배열이 포함된 구성 데이터를 바인딩할 때 구성 키의 배열 인덱스는 개체를 만들 때 구성 데이터를 반복하는 데에만 사용됩니다. 구성 데이터에 null 값을 유지할 수 없으며, 구성 키의 배열에서 하나 이상의 인덱스를 건너뛰더라도 바인딩된 개체에 null 값 항목이 생성되지 않습니다.

인덱스 &num;3에 대한 누락된 구성 항목은 `ArrayExample` 인스턴스에 바인딩하기 전에 구성에서 올바른 키-값 쌍을 생성하는 모든 구성 공급자에서 제공할 수 있습니다. 샘플에 누락된 키-값 쌍이 있는 추가 JSON 구성 공급자가 포함된 경우 `ArrayExample.Entries`는 전체 구성 배열과 일치합니다.

*missing_value.json*:

```json
{
  "array:entries:3": "value3"
}
```

`ConfigureAppConfiguration`의 경우

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

표에 표시된 키-값 쌍이 구성으로 로드됩니다.

| Key             | 값  |
| :-------------: | :----: |
| array:entries:3 | value3 |

JSON 구성 공급자에 인덱스 &num;3에 대한 항목이 포함된 후 `ArrayExample` 클래스 인스턴스를 바인딩하는 경우 `ArrayExample.Entries` 배열에 이 값이 포함됩니다.

| `ArrayExample.Entries` 인덱스 | `ArrayExample.Entries` 값 |
| :--------------------------: | :--------------------------: |
| 0                            | value0                       |
| 1                            | value1                       |
| 2                            | value2                       |
| 3                            | value3                       |
| 4                            | value4                       |
| 5                            | value5                       |

**JSON 배열 처리**

JSON 파일에 배열이 포함된 경우 0부터 시작하는 섹션 인덱스를 사용하여 배열 요소에 대한 구성 키가 생성됩니다. 다음 구성 파일에서 `subsection`은 배열입니다.

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

JSON 구성 공급자는 구성 데이터를 다음 키-값 쌍으로 읽습니다.

| Key                     | 값  |
| ----------------------- | :----: |
| json_array:key          | valueA |
| json_array:subsection:0 | valueB |
| json_array:subsection:1 | valueC |
| json_array:subsection:2 | valueD |

샘플 앱에서는 다음 POCO 클래스를 사용하여 구성 키-값 쌍을 바인딩할 수 있습니다.

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

바인딩 후 `JsonArrayExample.Key`는 `valueA` 값을 포함합니다. 하위 섹션 값은 POCO 배열 속성 `Subsection`에 저장됩니다.

| `JsonArrayExample.Subsection` 인덱스 | `JsonArrayExample.Subsection` 값 |
| :---------------------------------: | :---------------------------------: |
| 0                                   | valueB                              |
| 1                                   | valueC                              |
| 2                                   | valueD                              |

## <a name="custom-configuration-provider"></a>사용자 지정 구성 공급자

샘플 앱에서는 [EF(Entity Framework)](/ef/core/)를 사용하여 데이터베이스에서 구성 키-값 쌍을 읽는 기본 구성 공급자를 만드는 방법을 보여 줍니다.

이 공급자의 특징은 다음과 같습니다.

* 데모용으로 EF 메모리 내 데이터베이스를 사용합니다. 연결 문자열이 필요한 데이터베이스를 사용하려면 보조 `ConfigurationBuilder`를 구현하여 다른 구성 공급자에서 연결 문자열을 제공하세요.
* 이 공급자는 시작 시 데이터베이스 테이블을 구성으로 읽어 들입니다. 이 공급자는 키별 기준으로 데이터베이스를 쿼리하지 않습니다.
* 변경 시 다시 로드가 구현되지 않으므로 앱 시작 후 데이터베이스를 업데이트해도 앱 구성에 영향을 주지 않습니다.

데이터베이스에 구성 값을 저장하는 `EFConfigurationValue` 엔터티를 정의합니다.

*Models/EFConfigurationValue.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

구성된 값을 저장 및 액세스하는 `EFConfigurationContext`를 추가합니다.

*EFConfigurationProvider/EFConfigurationContext.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<xref:Microsoft.Extensions.Configuration.IConfigurationSource>를 구현하는 클래스를 만듭니다.

*EFConfigurationProvider/EFConfigurationSource.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<xref:Microsoft.Extensions.Configuration.ConfigurationProvider>에서 상속하여 사용자 지정 구성 공급자를 만듭니다. 구성 공급자는 비어 있는 데이터베이스를 초기화합니다.

*EFConfigurationProvider/EFConfigurationProvider.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

`AddEFConfiguration` 확장 메서드를 사용하여 구성 소스를 `ConfigurationBuilder`에 추가할 수 있습니다.

*Extensions/EntityFrameworkExtensions.cs*:

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

다음 코드는 *Program.cs*에서 사용자 지정 `EFConfigurationProvider`를 사용하는 방법을 보여 줍니다.

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a>시작하는 동안 구성에 액세스

`Startup.ConfigureServices`의 구성 값에 액세스하려면 `IConfiguration`을 `Startup` 생성자에 삽입합니다. `Startup.Configure`의 구성에 액세스하려면 `IConfiguration`을 메서드에 직접 삽입하거나 생성자의 인스턴스를 사용합니다.

```csharp
public class Startup
{
    private readonly IConfiguration _config;

    public Startup(IConfiguration config)
    {
        _config = config;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        var value = _config["key"];
    }

    public void Configure(IApplicationBuilder app, IConfiguration config)
    {
        var value = config["key"];
    }
}
```

시작 편의 메서드를 사용하여 구성에 액세스하는 방법의 예는 [앱 시작: 편리한 메서드](xref:fundamentals/startup#convenience-methods) 편을 참조하세요.

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a>Razor Pages 페이지 또는 MVC 뷰에서 구성에 액세스

Razor Pages 페이지나 MVC 뷰에서 구성 설정에 액세스하려면 [Microsoft.Extensions.Configuration 네임스페이스](xref:Microsoft.Extensions.Configuration)에 [using 지시문](xref:mvc/views/razor#using)([C# 참조: using 지시문](/dotnet/csharp/language-reference/keywords/using-directive))을 추가하고 <xref:Microsoft.Extensions.Configuration.IConfiguration>을 페이지 또는 뷰로 삽입합니다.

Razor 페이지에서:

```cshtml
@page
@model IndexModel
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index Page</title>
</head>
<body>
    <h1>Access configuration in a Razor Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

MVC 뷰에서:

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index View</title>
</head>
<body>
    <h1>Access configuration in an MVC view</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

## <a name="add-configuration-from-an-external-assembly"></a>외부 어셈블리의 구성 추가

<xref:Microsoft.AspNetCore.Hosting.IHostingStartup> 구현은 시작 시 앱의 `Startup` 클래스 외부에 있는 외부 어셈블리에서 앱에 향상된 기능을 추가할 수 있습니다. 자세한 내용은 <xref:fundamentals/configuration/platform-specific-configuration>를 참조하세요.

## <a name="additional-resources"></a>추가 자료

* <xref:fundamentals/configuration/options>

::: moniker-end
