---
title: ASP.NET Core 개발 시 앱 비밀의 안전한 저장
author: rick-anderson
description: ASP.NET Core 앱을 개발 하는 동안 중요 한 정보를 앱 비밀으로 저장 하 고 검색 하는 방법을 알아봅니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
uid: security/app-secrets
ms.openlocfilehash: c3f165164f3c95e8c0aab773f3731429ae224bd9
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654693"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a>ASP.NET Core 개발 시 앱 비밀의 안전한 저장

[Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27)및 [Scott addie](https://github.com/scottaddie)

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

이 문서에서는 개발 컴퓨터에서 ASP.NET Core 앱을 개발 하는 동안 중요 한 데이터를 저장 하 고 검색 하는 기술을 설명 합니다. 소스 코드에 패스워드나 다른 민감한 데이터를 저장하지 마세요. 프로덕션용 비밀들은 개발 또는 테스트에서 사용하면 안됩니다. 암호는 앱과 함께 배포 해서는 안 됩니다. 대신 환경 변수, Azure Key Vault 등의 제어 된 방법을 통해 프로덕션 환경에서 암호를 사용할 수 있습니다. [Azure Key Vault 구성 공급자](xref:security/key-vault-configuration)를 사용 하 여 Azure 테스트 및 프로덕션 암호를 저장 하 고 보호할 수 있습니다.

## <a name="environment-variables"></a>환경 변수

환경 변수는 로컬 구성 파일 또는 코드에서의 앱 비밀의 저장을 방지하는 데 사용됩니다. 환경 변수는 이전에 지정한 모든 구성 원본의 구성 값을 재정의합니다.

::: moniker range="<= aspnetcore-1.1"

`Startup` 생성자에서 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables%2A>를 호출 하 여 환경 변수 값에 대 한 읽기를 구성 합니다.

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=8)]

::: moniker-end

**개별 사용자 계정** 보안이 사용 되는 ASP.NET Core 웹 앱을 고려 합니다. 기본 데이터베이스 연결 문자열은 키 `DefaultConnection`를 사용 하 여 프로젝트의 *appsettings* 파일에 포함 됩니다. 기본 연결 문자열은 사용자 모드에서 실행되고 암호가 필요없는 LocalDB용입니다. 앱을 배포 하는 동안 `DefaultConnection` 키 값을 환경 변수의 값으로 재정의할 수 있습니다. 환경 변수는 민감한 자격 증명을 사용하여 전체 연결 문자열을 저장할 수 있습니다.

> [!WARNING]
> 환경 변수는 일반적으로 암호화되지 않은 일반 텍스트로 저장됩니다. 컴퓨터 또는 프로세스가 손상된 경우 환경 변수는 신뢰할 수 없는 당사자가 액세스할 수 있습니다. 사용자의 비밀 정보가 유출되지 않도록 추가 조치가 필요할 수 있습니다.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>비밀 관리자

암호 관리자 도구는 ASP.NET Core 프로젝트를 개발 하는 동안 중요 한 데이터를 저장 합니다. 이 컨텍스트에서 중요 한 데이터 조각은 앱 암호입니다. 앱 암호는 프로젝트 트리와는 별도의 위치에 저장 됩니다. 앱 암호는 특정 프로젝트에 연결 되거나 여러 프로젝트에서 공유 됩니다. 앱 비밀이 소스 제어에 체크 인 되지 않습니다.

> [!WARNING]
> 비밀 관리자 도구는 저장 된 비밀을 암호화 하지 않으며 신뢰할 수 있는 저장소로 처리 되어서는 안 됩니다. 개발 목적 으로만 사용 됩니다. 키와 값은 사용자 프로필 디렉터리의 JSON 구성 파일에 저장 됩니다.

## <a name="how-the-secret-manager-tool-works"></a>비밀 관리자 도구의 작동 원리

비밀 관리자 도구는 값이 저장 되는 위치 및 방법과 같은 구현 세부 정보를 추출 합니다. 이러한 구현 세부 정보를 몰라도 도구를 사용할 수 있습니다. 값은 로컬 컴퓨터의 시스템 보호 사용자 프로필 폴더에 있는 JSON 구성 파일에 저장 됩니다.

# <a name="windows"></a>[Windows](#tab/windows)

파일 시스템 경로:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

파일 시스템 경로:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

위의 파일 경로에서 `<user_secrets_id>`를 *.csproj* 파일에 지정 된 `UserSecretsId` 값으로 바꿉니다.

암호 관리자 도구를 사용 하 여 저장 된 데이터의 위치 또는 형식에 따라 달라 지는 코드를 작성 하지 마세요. 이러한 구현 세부 정보는 변경 될 수 있습니다. 예를 들어 비밀 값은 암호화 되지 않지만 나중에 있을 수 있습니다.

::: moniker range="<= aspnetcore-2.0"

## <a name="install-the-secret-manager-tool"></a>암호 관리자 도구 설치

암호 관리자 도구는 .NET Core SDK 2.1.300 이상에서 .NET Core CLI와 함께 제공 됩니다. 2\.1.300 이전 .NET Core SDK 버전의 경우 도구 설치가 필요 합니다.

> [!TIP]
> 명령 셸에서 `dotnet --version`를 실행 하 여 설치 된 .NET Core SDK 버전 번호를 확인 합니다.

사용 중인 .NET Core SDK에 도구가 포함 되어 있으면 경고가 표시 됩니다.

```console
The tool 'Microsoft.Extensions.SecretManager.Tools' is now included in the .NET Core SDK. Information on resolving this warning is available at (https://aka.ms/dotnetclitools-in-box).
```

ASP.NET Core 프로젝트에 [Microsoft.extensions.secretmanager.tools](https://www.nuget.org/packages/Microsoft.Extensions.SecretManager.Tools/) NuGet 패키지를 설치 합니다. 다음은 그 예입니다.

[!code-xml[](app-secrets/samples/1.x/UserSecrets/UserSecrets.csproj?name=snippet_CsprojFile&highlight=15-16)]

명령 셸에서 다음 명령을 실행 하 여 도구 설치의 유효성을 검사 합니다.

```dotnetcli
dotnet user-secrets -h
```

비밀 관리자 도구는 예제 사용법, 옵션 및 명령 도움말을 표시 합니다.

```console
Usage: dotnet user-secrets [options] [command]

Options:
  -?|-h|--help                        Show help information
  --version                           Show version information
  -v|--verbose                        Show verbose output
  -p|--project <PROJECT>              Path to project. Defaults to searching the current directory.
  -c|--configuration <CONFIGURATION>  The project configuration to use. Defaults to 'Debug'.
  --id                                The user secret ID to use.

Commands:
  clear   Deletes all the application secrets
  list    Lists all the application secrets
  remove  Removes the specified user secret
  set     Sets the user secret to the specified value

Use "dotnet user-secrets [command] --help" for more information about a command.
```

> [!NOTE]
> *.Csproj* 파일의 `DotNetCliToolReference` 요소에 정의 된 도구를 실행 하려면 *.csproj* 파일과 동일한 디렉터리에 있어야 합니다.

::: moniker-end

## <a name="enable-secret-storage"></a>비밀 저장소 사용

비밀 관리자 도구는 사용자 프로필에 저장 된 프로젝트별 구성 설정에 대해 작동 합니다.

::: moniker range=">= aspnetcore-3.0"

비밀 관리자 도구 .NET Core SDK 3.0.100 이상에 `init` 명령을 포함 합니다. 사용자 암호를 사용 하려면 프로젝트 디렉터리에서 다음 명령을 실행 합니다.

```dotnetcli
dotnet user-secrets init
```

이전 명령은 *.csproj* 파일의 `PropertyGroup`에 `UserSecretsId` 요소를 추가 합니다. 기본적으로 `UserSecretsId`의 내부 텍스트는 GUID입니다. 내부 텍스트는 임의로 이지만 프로젝트에 고유 합니다.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

사용자 암호를 사용 하려면 *.csproj* 파일의 `PropertyGroup` 내에서 `UserSecretsId` 요소를 정의 합니다. `UserSecretsId`의 내부 텍스트는 임의로 이지만 프로젝트에 고유 합니다. 개발자는 일반적으로 `UserSecretsId`에 대 한 GUID를 생성 합니다.

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](app-secrets/samples/1.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

::: moniker-end

> [!TIP]
> Visual Studio의 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 상황에 맞는 메뉴에서 **사용자 암호 관리** 를 선택 합니다. 이 제스처는 GUID로 채워진 `UserSecretsId` 요소를 *.csproj* 파일에 추가 합니다.

## <a name="set-a-secret"></a>비밀 설정

키와 해당 값으로 구성 된 앱 암호를 정의 합니다. 암호는 프로젝트의 `UserSecretsId` 값과 연결 됩니다. 예를 들어 *.csproj* 파일이 있는 디렉터리에서 다음 명령을 실행 합니다.

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

앞의 예제에서 콜론은 `Movies`이 `ServiceApiKey` 속성을 가진 개체 리터럴이어야 함을 나타냅니다.

암호 관리자 도구는 다른 디렉터리 에서도 사용할 수 있습니다. `--project` 옵션을 사용 하 여 *.csproj* 파일이 있는 파일 시스템 경로를 제공 합니다. 다음은 그 예입니다.

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Visual Studio의 JSON 구조 평면화

Visual Studio의 **사용자 비밀 관리** 제스처는 텍스트 편집기에서 *비밀. json* 파일을 엽니다. *비밀. json* 의 내용을 저장할 키-값 쌍으로 바꿉니다. 다음은 그 예입니다.

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

JSON 구조는 `dotnet user-secrets remove` 또는 `dotnet user-secrets set`를 통해 수정 된 후에 결합 됩니다. 예를 들어 `dotnet user-secrets remove "Movies:ConnectionString"`를 실행 하면 `Movies` 개체 리터럴이 축소 됩니다. 수정 된 파일은 다음과 유사 합니다.

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>여러 비밀 설정

암호 일괄 처리는 `set` 명령으로 JSON을 파이프 하 여 설정할 수 있습니다. 다음 예제에서는 *입력. json* 파일의 내용이 `set` 명령으로 파이프 됩니다.

# <a name="windows"></a>[Windows](#tab/windows)

명령 셸을 열고 다음 명령을 실행 합니다.

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

명령 셸을 열고 다음 명령을 실행 합니다.

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>비밀 액세스

[ASP.NET Core 구성 API](xref:fundamentals/configuration/index) 는 암호 관리자 암호에 대 한 액세스를 제공 합니다.

::: moniker range=">= aspnetcore-2.0 <= aspnetcore-2.2"

프로젝트가 .NET Framework 대상으로 지정 되는 경우에는 [Microsoft 확장명이](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) . n e t.

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

ASP.NET Core 2.0 이상에서는 프로젝트가 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>를 호출 하 여 미리 구성 된 기본값을 사용 하 여 호스트의 새 인스턴스를 초기화 하는 경우 개발 모드에서 사용자 암호 구성 소스가 자동으로 추가 됩니다. <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>될 때 `CreateDefaultBuilder` <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>를 호출 합니다.

::: moniker-end

::: moniker range=">= aspnetcore-2.0 <= aspnetcore-2.2"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

`CreateDefaultBuilder`를 호출 하지 않은 경우 `Startup` 생성자에서 <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>를 호출 하 여 사용자 암호 구성 소스를 명시적으로 추가 합니다. 다음 예제와 같이 개발 환경에서 앱이 실행 되는 경우에만 `AddUserSecrets`를 호출 합니다.

::: moniker-end

::: moniker range=">= aspnetcore-2.0 <= aspnetcore-2.2"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[Microsoft 확장명이 구성](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) 된 NuGet 패키지를 설치 합니다.

`Startup` 생성자에서 <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>에 대 한 호출을 사용 하 여 사용자 비밀 구성 소스를 추가 합니다.

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

`Configuration` API를 통해 사용자 암호를 검색할 수 있습니다.

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=26)]

::: moniker-end

## <a name="map-secrets-to-a-poco"></a>POCO에 비밀 매핑

전체 개체 리터럴을 POCO (속성이 있는 간단한 .NET 클래스)에 매핑하면 관련 된 속성을 집계할 때 유용 합니다.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

위의 비밀을 POCO에 매핑하려면 `Configuration` API의 [개체 그래프 바인딩](xref:fundamentals/configuration/index#bind-to-an-object-graph) 기능을 사용 합니다. 다음 코드는 사용자 지정 `MovieSettings` POCO에 바인딩하고 `ServiceApiKey` 속성 값에 액세스 합니다.

::: moniker range=">= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

::: moniker-end

::: moniker range="= aspnetcore-1.0"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

::: moniker-end

`Movies:ConnectionString` 및 `Movies:ServiceApiKey` 암호는 `MovieSettings`의 해당 속성에 매핑됩니다.

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>암호를 사용 하 여 문자열 대체

암호를 일반 텍스트로 저장 하는 것은 안전 하지 않습니다. 예를 들어 *appsettings* 에 저장 된 데이터베이스 연결 문자열은 지정 된 사용자에 대 한 암호를 포함할 수 있습니다.

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

보다 안전한 방법은 암호를 비밀로 저장 하는 것입니다. 다음은 그 예입니다.

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

*Appsettings*의 연결 문자열에서 `Password` 키-값 쌍을 제거 합니다. 다음은 그 예입니다.

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<xref:System.Data.SqlClient.SqlConnectionStringBuilder> 개체의 <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> 속성에 대해 암호의 값을 설정 하 여 연결 문자열을 완료할 수 있습니다.

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=26-29)]

::: moniker-end

## <a name="list-the-secrets"></a>암호 나열

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

*.Csproj* 파일이 있는 디렉터리에서 다음 명령을 실행 합니다.

```dotnetcli
dotnet user-secrets list
```

다음 출력이 표시됩니다.

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

앞의 예제에서 키 이름의 콜론은 *비밀이*포함 된 개체 계층 구조를 나타냅니다.

## <a name="remove-a-single-secret"></a>단일 비밀 제거

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

*.Csproj* 파일이 있는 디렉터리에서 다음 명령을 실행 합니다.

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

`MoviesConnectionString` 키와 연결 된 키-값 쌍을 제거 하기 위해 앱의 *비밀. json* 파일이 수정 되었습니다.

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

`dotnet user-secrets list`를 실행 하면 다음과 같은 메시지가 표시 됩니다.

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>모든 비밀 제거

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

*.Csproj* 파일이 있는 디렉터리에서 다음 명령을 실행 합니다.

```dotnetcli
dotnet user-secrets clear
```

앱에 대 한 모든 사용자 비밀이 *비밀. json* 파일에서 삭제 되었습니다.

```json
{}
```

`dotnet user-secrets list`를 실행 하면 다음과 같은 메시지가 표시 됩니다.

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>추가 리소스

* IIS에서 암호 관리자에 액세스 하는 방법에 대 한 자세한 내용은 [이 문제](https://github.com/dotnet/AspNetCore.Docs/issues/16328) 를 참조 하세요.
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>
