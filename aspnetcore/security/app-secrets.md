---
title: ASP.NET Core 개발 시 앱 암호의 안전한 저장  
author: rick-anderson
description: 저장 하 고 ASP.NET Core 앱을 개발 하는 동안 앱 암호 '로 중요 한 정보를 검색 하는 방법에 알아봅니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 03/13/2019
uid: security/app-secrets
ms.openlocfilehash: 18313f8284e81d196cbe786f494a607ee97a299f
ms.sourcegitcommit: 3e9e1f6d572947e15347e818f769e27dea56b648
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2019
ms.locfileid: "58750980"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a>ASP.NET Core 개발 시 앱 비밀의 안전한 저장

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT), [김 Roth](https://github.com/danroth27), 및 [Scott Addie](https://github.com/scottaddie)

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/app-secrets/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

이 문서는 ASP.NET Core 앱을 개발할 때 민감한 데이터를 저장하거나 검색하는 기술들을 설명합니다. 소스 코드에 패스워드나 다른 민감한 데이터를 저장하지 마세요. 프로덕션용 비밀들은 개발 또는 테스트에서 사용하면 안됩니다. [Azure Key Vault 구성 제공자](xref:security/key-vault-configuration)로 Azure 테스트 및 프로덕션 암호를 저장하고 보호할 수 있습니다.

## <a name="environment-variables"></a>환경 변수

환경 변수는 로컬 구성 파일 또는 코드에서의 앱 비밀의 저장을 방지하는 데 사용됩니다. 환경 변수는 이전에 지정한 모든 구성 원본의 구성 값을 재정의합니다.

::: moniker range="<= aspnetcore-1.1"

호출 하 여 환경 변수 값을 읽는 구성 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> 에 `Startup` 생성자:

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=8)]

::: moniker-end

**개별 사용자 계정** 보안이 활성화된 ASP.NET Core 웹 앱을 고려해보세요. 기본 데이터베이스 연결 문자열이 프로젝트의 *appsettings.json* 파일의 `DefaultConnection` 키에 포함되어 있습니다. 기본 연결 문자열은 사용자 모드에서 실행되고 암호가 필요없는 LocalDB용입니다. 앱을 배포하는 동안 `DefaultConnection` 키 값은 환경 변수의 값으로 재정의할 수 있습니다. 환경 변수는 민감한 자격 증명을 사용하여 전체 연결 문자열을 저장할 수 있습니다.

> [!WARNING]
> 환경 변수는 일반적으로 암호화되지 않은 일반 텍스트로 저장됩니다. 컴퓨터 또는 프로세스가 손상된 경우 환경 변수는 신뢰할 수 없는 당사자가 액세스할 수 있습니다. 사용자의 비밀 정보가 유출되지 않도록 추가 조치가 필요할 수 있습니다.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>암호 관리자

암호 관리자 도구는 ASP.NET Core 프로젝트를 개발 하는 동안 중요 한 데이터를 저장합니다. 이 컨텍스트에서 중요 한 데이터의 일부는 앱 암호를 사용 합니다. 앱 암호는 별도 프로젝트 트리 위치에 저장 됩니다. 앱 암호를 특정 프로젝트와 연결 된 또는 여러 프로젝트 간에 공유 됩니다. 앱 암호를 소스 제어에 체크 인 되지 않습니다.

> [!WARNING]
> 암호 관리자 도구는 저장 된 비밀을 암호화 하지 않습니다 하 고 신뢰할 수 있는 저장소로 간주 해서는 안 됩니다. 개발 목적 으로만입니다. 키와 값은 사용자 프로필 디렉터리에는 JSON 구성 파일에 저장 됩니다.

## <a name="how-the-secret-manager-tool-works"></a>암호 관리자 도구 작동 원리

암호 관리자 도구를 같은 값을 저장 하는 위치와 방법을 구현 세부 정보를 추상화 합니다. 이러한 구현 세부 정보를 알 필요 없이 도구를 사용할 수 있습니다. 값은 로컬 컴퓨터의 시스템 보호 된 사용자 프로필 폴더에 JSON 구성 파일에 저장 됩니다.

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

파일 시스템 경로:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macostablinuxmacos"></a>[Linux / macOS](#tab/linux+macos)

파일 시스템 경로:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

앞의 파일 경로, 대체 `<user_secrets_id>` 사용 하 여 합니다 `UserSecretsId` 에 지정 된 값을 *.csproj* 파일입니다.

위치 또는 암호 관리자 도구를 사용 하 여 저장 된 데이터의 형식에 의존 하는 코드를 작성 하지 마십시오. 이러한 구현 세부 정보를 변경할 수 있습니다. 예를 들어, 비밀 값을 암호화 되지 않습니다 하지만 나중에 사용할 수 없습니다.

::: moniker range="<= aspnetcore-2.0"

## <a name="install-the-secret-manager-tool"></a>암호 관리자 도구를 설치 합니다.

암호 관리자 도구는.NET Core SDK 2.1.300에서에서.NET Core CLI와 함께 번들로 묶은 이상. .NET Core SDK 2.1.300 이전 버전의 경우 도구 설치가 필요 합니다.

> [!TIP]
> 실행 `dotnet --version` 명령 셸을 설치 된.NET Core SDK 버전 번호를 확인 합니다.

사용 중인.NET Core SDK 도구를 포함 하는 경우 경고가 표시 됩니다.

```console
The tool 'Microsoft.Extensions.SecretManager.Tools' is now included in the .NET Core SDK. Information on resolving this warning is available at (https://aka.ms/dotnetclitools-in-box).
```

설치 합니다 [Microsoft.Extensions.SecretManager.Tools](https://www.nuget.org/packages/Microsoft.Extensions.SecretManager.Tools/) ASP.NET Core 프로젝트에 NuGet 패키지. 예를 들어:

[!code-xml[](app-secrets/samples/1.x/UserSecrets/UserSecrets.csproj?name=snippet_CsprojFile&highlight=15-16)]

도구 설치를 확인 하려면 명령 셸에서 다음 명령을 실행 합니다.

```console
dotnet user-secrets -h
```

암호 관리자 도구 샘플 사용법, 옵션 및 명령 도움말을 표시합니다.

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
> 동일한 디렉터리에 있어야 합니다 *.csproj* 파일에 정의 된 도구를 실행 하는 *.csproj* 파일의 `DotNetCliToolReference` 요소입니다.

::: moniker-end

## <a name="enable-secret-storage"></a>비밀 저장소를 사용 하도록 설정

암호 관리자 도구는 사용자 프로필에 저장 된 프로젝트 관련 구성 설정에서 작동 합니다.

::: moniker range=">= aspnetcore-3.0"

암호 관리자 도구에는 `init` .NET Core SDK 3.0.100 명령을 이상. 사용자 암호를 사용 하려면 프로젝트 디렉터리에서 다음 명령을 실행 합니다.

```console
dotnet user-secrets init
```

앞의 명령을 추가 `UserSecretsId` 내의 요소를 `PropertyGroup` 의 합니다 *.csproj* 파일입니다. 기본적으로의 내부 텍스트 `UserSecretsId` 는 GUID입니다. 내부 텍스트는 있지만 프로젝트에 고유 합니다.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

사용자 암호를 사용 하려면 정의 `UserSecretsId` 내의 요소를 `PropertyGroup` 의 합니다 *.csproj* 파일입니다. 내부 텍스트 `UserSecretsId` 는 임의적 이지만 프로젝트에 고유 합니다. 개발자는 일반적으로 GUID를 생성할는 `UserSecretsId`합니다.

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](app-secrets/samples/1.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

::: moniker-end

> [!TIP]
> Visual Studio에서 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 선택 **사용자 암호 관리** 상황에 맞는 메뉴입니다. 이 제스처 추가 `UserSecretsId` 에 채워진 GUID를 사용 하 여 요소를 *.csproj* 파일.

## <a name="set-a-secret"></a>암호를 설정 합니다.

키 및 해당 값으로 구성 된 앱 암호를 정의 합니다. 암호는 프로젝트와 연결 된 `UserSecretsId` 값입니다. 예를 들어 있는 디렉터리에서 다음 명령을 실행 합니다 *.csproj* 파일이 있습니다.

```console
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

앞의 예제에서 콜론 나타냅니다 `Movies` 는 개체를 사용 하 여 리터럴는 `ServiceApiKey` 속성입니다.

암호 관리자 도구는 너무 다른 디렉터리에서 사용할 수 있습니다. 사용 합니다 `--project` 는 파일 시스템 경로 제공 하는 옵션을 *.csproj* 파일이 합니다. 예를 들어:

```console
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Visual Studio에서 평면화 된 JSON 구조

Visual Studio **사용자 암호 관리** 열립니다 제스처를 *secrets.json* 파일을 텍스트 편집기에서. 내용을 바꿉니다 *secrets.json* 저장할 키-값 쌍을 사용 하 여 합니다. 예를 들어:

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

JSON 구조를 통해 수정 후 결합 됩니다 `dotnet user-secrets remove` 또는 `dotnet user-secrets set`합니다. 예를 들어, 실행 중인 `dotnet user-secrets remove "Movies:ConnectionString"` 축소는 `Movies` 개체 리터럴. 수정된 된 파일에는 다음과 같습니다.

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>여러 암호를 설정 합니다.

JSON에 파이프 하 여 비밀의 일괄 처리를 설정할 수 있습니다는 `set` 명령입니다. 다음 예제에서는 *input.json* 파일의 내용으로 파이프 되는 `set` 명령.

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

명령 셸을 열고 다음 명령을 실행 합니다.

  ```console
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macostablinuxmacos"></a>[Linux / macOS](#tab/linux+macos)

명령 셸을 열고 다음 명령을 실행 합니다.

  ```console
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>비밀에 액세스

합니다 [ASP.NET Core 구성 API](xref:fundamentals/configuration/index) 암호 관리자 암호에 대 한 액세스를 제공 합니다.

::: moniker range=">= aspnetcore-2.0 <= aspnetcore-2.2"

프로젝트가.NET Framework를 대상으로 하는 경우 설치 합니다 [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet 패키지.

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

ASP.NET Core 2.0 이상에서는 사용자 비밀 구성 소스는 자동으로 추가 개발 모드에서 프로젝트를 호출 하는 경우 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> 미리 구성 된 기본값을 사용 하 여 호스트의 새 인스턴스를 초기화 합니다. `CreateDefaultBuilder` 호출 <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets*> 때 합니다 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> 는 <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

때 `CreateDefaultBuilder` 되지 호출 하 여 사용자 비밀 구성 소스를 명시적으로 추가 호출 <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets*> 에 `Startup` 생성자입니다. 호출 `AddUserSecrets` 만 앱에서에서 실행 될 때 개발 환경에서는 다음 예제에서와 같이:

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

설치 합니다 [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet 패키지.

호출 하 여 사용자 비밀 구성 소스를 추가 <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets*> 에 `Startup` 생성자:

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

사용자 암호를 통해 검색할 수는 `Configuration` API:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=26)]

::: moniker-end

## <a name="map-secrets-to-a-poco"></a>POCO에 비밀을 맵

전체 개체 리터럴에 POCO (속성을 사용 하 여 간단한.NET 클래스)에 매핑하는 관련된 속성을 집계 유용 합니다.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

이전 암호를 POCO에 매핑할을 사용 합니다 `Configuration` API [graph 바인딩 개체](xref:fundamentals/configuration/index#bind-to-an-object-graph) 기능입니다. 다음 코드를 사용자 지정 바인딩할 `MovieSettings` POCO 및 액세스는 `ServiceApiKey` 속성 값:

::: moniker range=">= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

::: moniker-end

::: moniker range="= aspnetcore-1.0"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

::: moniker-end

합니다 `Movies:ConnectionString` 하 고 `Movies:ServiceApiKey` 비밀의 각 속성에 매핑됩니다. `MovieSettings`:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>암호를 사용 하 여 문자열 대체

암호를 일반 텍스트로 저장 안전 하지 않습니다. 데이터베이스 연결 문자열에 저장 하는 예를 들어 *appsettings.json* 지정된 된 사용자에 대 한 암호를 포함할 수 있습니다.

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

보다 안전한 방법은 암호로 암호를 저장 하는 것입니다. 예를 들어:

```console
dotnet user-secrets set "DbPassword" "pass123"
```

제거 된 `Password` 연결 문자열에서 키-값 쌍 *appsettings.json*합니다. 예를 들어:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

암호의 값에 설정할 수는 <xref:System.Data.SqlClient.SqlConnectionStringBuilder> 개체의 <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password*> 연결 문자열을 완료 하는 속성:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=26-29)]

::: moniker-end

## <a name="list-the-secrets"></a>암호 나열

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

디렉터리에서 다음 명령을 실행 합니다 *.csproj* 파일이 있습니다.

```console
dotnet user-secrets list
```

다음 출력이 표시됩니다.

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

키 이름에 콜론 앞의 예제에서 내에서 개체 계층 구조를 나타냅니다 *secrets.json*합니다.

## <a name="remove-a-single-secret"></a>단일 암호를 제거 합니다.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

디렉터리에서 다음 명령을 실행 합니다 *.csproj* 파일이 있습니다.

```console
dotnet user-secrets remove "Movies:ConnectionString"
```

앱의 *secrets.json* 와 연결 된 키-값 쌍을 제거 하려면 파일을 수정한는 `MoviesConnectionString` 키:

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

실행 `dotnet user-secrets list` 다음 메시지가 표시 됩니다.

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>모든 암호를 제거 합니다.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

디렉터리에서 다음 명령을 실행 합니다 *.csproj* 파일이 있습니다.

```console
dotnet user-secrets clear
```

앱에 대 한 모든 사용자 암호에서 삭제 합니다 *secrets.json* 파일:

```json
{}
```

실행 `dotnet user-secrets list` 다음 메시지가 표시 됩니다.

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>추가 자료

* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>
