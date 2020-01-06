---
title: ASP.NET Core 개발 시 앱 비밀의 안전한 저장
author: rick-anderson
description: ASP.NET Core 앱을 개발 하는 동안 중요 한 정보를 앱 비밀으로 저장 하 고 검색 하는 방법을 알아봅니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
uid: security/app-secrets
ms.openlocfilehash: 9b36ae64fbe277cd81ed22ba7b21b0a035082dbd
ms.sourcegitcommit: c815a9465e7b1bab44ce1643ec345b33e6cf1598
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/02/2020
ms.locfileid: "75606794"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="cf773-103">ASP.NET Core 개발 시 앱 비밀의 안전한 저장</span><span class="sxs-lookup"><span data-stu-id="cf773-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

<span data-ttu-id="cf773-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT), [김 Roth](https://github.com/danroth27), 및 [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="cf773-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="cf773-105">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples)([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cf773-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="cf773-106">이 문서에서는 개발 컴퓨터에서 ASP.NET Core 앱을 개발 하는 동안 중요 한 데이터를 저장 하 고 검색 하는 기술을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-106">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="cf773-107">소스 코드에 패스워드나 다른 민감한 데이터를 저장하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="cf773-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="cf773-108">프로덕션용 비밀들은 개발 또는 테스트에서 사용하면 안됩니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="cf773-109">암호는 앱과 함께 배포 해서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="cf773-110">대신 환경 변수, Azure Key Vault 등의 제어 된 방법을 통해 프로덕션 환경에서 암호를 사용할 수 있습니다. [Azure Key Vault 구성 공급자](xref:security/key-vault-configuration)를 사용 하 여 Azure 테스트 및 프로덕션 암호를 저장 하 고 보호할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-110">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="cf773-111">환경 변수</span><span class="sxs-lookup"><span data-stu-id="cf773-111">Environment variables</span></span>

<span data-ttu-id="cf773-112">환경 변수는 로컬 구성 파일 또는 코드에서의 앱 비밀의 저장을 방지하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-112">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="cf773-113">환경 변수는 이전에 지정한 모든 구성 원본의 구성 값을 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-113">Environment variables override configuration values for all previously specified configuration sources.</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="cf773-114">`Startup` 생성자에서 <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables%2A>를 호출 하 여 환경 변수 값에 대 한 읽기를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-114">Configure the reading of environment variable values by calling <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables%2A> in the `Startup` constructor:</span></span>

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=8)]

::: moniker-end

<span data-ttu-id="cf773-115">**개별 사용자 계정** 보안이 활성화된 ASP.NET Core 웹 앱을 고려해보세요.</span><span class="sxs-lookup"><span data-stu-id="cf773-115">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="cf773-116">기본 데이터베이스 연결 문자열이 프로젝트의 *appsettings.json* 파일의 `DefaultConnection` 키에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-116">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="cf773-117">기본 연결 문자열은 사용자 모드에서 실행되고 암호가 필요없는 LocalDB용입니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-117">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="cf773-118">앱을 배포하는 동안 `DefaultConnection` 키 값은 환경 변수의 값으로 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-118">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="cf773-119">환경 변수는 민감한 자격 증명을 사용하여 전체 연결 문자열을 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-119">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="cf773-120">환경 변수는 일반적으로 암호화되지 않은 일반 텍스트로 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-120">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="cf773-121">컴퓨터 또는 프로세스가 손상된 경우 환경 변수는 신뢰할 수 없는 당사자가 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-121">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="cf773-122">사용자의 비밀 정보가 유출되지 않도록 추가 조치가 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-122">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="cf773-123">비밀 관리자</span><span class="sxs-lookup"><span data-stu-id="cf773-123">Secret Manager</span></span>

<span data-ttu-id="cf773-124">암호 관리자 도구는 ASP.NET Core 프로젝트를 개발 하는 동안 중요 한 데이터를 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-124">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="cf773-125">이 컨텍스트에서 중요 한 데이터 조각은 앱 암호입니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-125">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="cf773-126">앱 암호는 프로젝트 트리와는 별도의 위치에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-126">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="cf773-127">앱 암호는 특정 프로젝트에 연결 되거나 여러 프로젝트에서 공유 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-127">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="cf773-128">앱 비밀이 소스 제어에 체크 인 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-128">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="cf773-129">비밀 관리자 도구는 저장 된 비밀을 암호화 하지 않으며 신뢰할 수 있는 저장소로 처리 되어서는 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-129">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="cf773-130">개발 목적 으로만 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-130">It's for development purposes only.</span></span> <span data-ttu-id="cf773-131">키와 값은 사용자 프로필 디렉터리의 JSON 구성 파일에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-131">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="cf773-132">비밀 관리자 도구의 작동 원리</span><span class="sxs-lookup"><span data-stu-id="cf773-132">How the Secret Manager tool works</span></span>

<span data-ttu-id="cf773-133">비밀 관리자 도구는 값이 저장 되는 위치 및 방법과 같은 구현 세부 정보를 추출 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-133">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="cf773-134">이러한 구현 세부 정보를 몰라도 도구를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-134">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="cf773-135">값은 로컬 컴퓨터의 시스템 보호 사용자 프로필 폴더에 있는 JSON 구성 파일에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-135">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="cf773-136">Windows</span><span class="sxs-lookup"><span data-stu-id="cf773-136">Windows</span></span>](#tab/windows)

<span data-ttu-id="cf773-137">파일 시스템 경로:</span><span class="sxs-lookup"><span data-stu-id="cf773-137">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macostablinuxmacos"></a>[<span data-ttu-id="cf773-138">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="cf773-138">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="cf773-139">파일 시스템 경로:</span><span class="sxs-lookup"><span data-stu-id="cf773-139">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="cf773-140">위의 파일 경로에서 `<user_secrets_id>`를 *.csproj* 파일에 지정 된 `UserSecretsId` 값으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-140">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="cf773-141">암호 관리자 도구를 사용 하 여 저장 된 데이터의 위치 또는 형식에 따라 달라 지는 코드를 작성 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="cf773-141">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="cf773-142">이러한 구현 세부 정보는 변경 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-142">These implementation details may change.</span></span> <span data-ttu-id="cf773-143">예를 들어 비밀 값은 암호화 되지 않지만 나중에 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-143">For example, the secret values aren't encrypted, but could be in the future.</span></span>

::: moniker range="<= aspnetcore-2.0"

## <a name="install-the-secret-manager-tool"></a><span data-ttu-id="cf773-144">암호 관리자 도구 설치</span><span class="sxs-lookup"><span data-stu-id="cf773-144">Install the Secret Manager tool</span></span>

<span data-ttu-id="cf773-145">암호 관리자 도구는 .NET Core SDK 2.1.300 이상에서 .NET Core CLI와 함께 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-145">The Secret Manager tool is bundled with the .NET Core CLI in .NET Core SDK 2.1.300 or later.</span></span> <span data-ttu-id="cf773-146">2\.1.300 이전 .NET Core SDK 버전의 경우 도구 설치가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-146">For .NET Core SDK versions before 2.1.300, tool installation is necessary.</span></span>

> [!TIP]
> <span data-ttu-id="cf773-147">명령 셸에서 `dotnet --version`를 실행 하 여 설치 된 .NET Core SDK 버전 번호를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-147">Run `dotnet --version` from a command shell to see the installed .NET Core SDK version number.</span></span>

<span data-ttu-id="cf773-148">사용 중인 .NET Core SDK에 도구가 포함 되어 있으면 경고가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-148">A warning is displayed if the .NET Core SDK being used includes the tool:</span></span>

```console
The tool 'Microsoft.Extensions.SecretManager.Tools' is now included in the .NET Core SDK. Information on resolving this warning is available at (https://aka.ms/dotnetclitools-in-box).
```

<span data-ttu-id="cf773-149">ASP.NET Core 프로젝트에 [Microsoft.extensions.secretmanager.tools](https://www.nuget.org/packages/Microsoft.Extensions.SecretManager.Tools/) NuGet 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-149">Install the [Microsoft.Extensions.SecretManager.Tools](https://www.nuget.org/packages/Microsoft.Extensions.SecretManager.Tools/) NuGet package in your ASP.NET Core project.</span></span> <span data-ttu-id="cf773-150">예를 들면 다음과 같습니다.:</span><span class="sxs-lookup"><span data-stu-id="cf773-150">For example:</span></span>

[!code-xml[](app-secrets/samples/1.x/UserSecrets/UserSecrets.csproj?name=snippet_CsprojFile&highlight=15-16)]

<span data-ttu-id="cf773-151">명령 셸에서 다음 명령을 실행 하 여 도구 설치의 유효성을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-151">Execute the following command in a command shell to validate the tool installation:</span></span>

```dotnetcli
dotnet user-secrets -h
```

<span data-ttu-id="cf773-152">비밀 관리자 도구는 예제 사용법, 옵션 및 명령 도움말을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-152">The Secret Manager tool displays sample usage, options, and command help:</span></span>

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
> <span data-ttu-id="cf773-153">*.Csproj* 파일의 `DotNetCliToolReference` 요소에 정의 된 도구를 실행 하려면 *.csproj* 파일과 동일한 디렉터리에 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-153">You must be in the same directory as the *.csproj* file to run tools defined in the *.csproj* file's `DotNetCliToolReference` elements.</span></span>

::: moniker-end

## <a name="enable-secret-storage"></a><span data-ttu-id="cf773-154">비밀 저장소 사용</span><span class="sxs-lookup"><span data-stu-id="cf773-154">Enable secret storage</span></span>

<span data-ttu-id="cf773-155">비밀 관리자 도구는 사용자 프로필에 저장 된 프로젝트별 구성 설정에 대해 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-155">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cf773-156">비밀 관리자 도구 .NET Core SDK 3.0.100 이상에 `init` 명령을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-156">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="cf773-157">사용자 암호를 사용 하려면 프로젝트 디렉터리에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-157">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="cf773-158">이전 명령은 *.csproj* 파일의 `PropertyGroup`에 `UserSecretsId` 요소를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-158">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="cf773-159">기본적으로 `UserSecretsId`의 내부 텍스트는 GUID입니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-159">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="cf773-160">내부 텍스트는 임의로 이지만 프로젝트에 고유 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-160">The inner text is arbitrary, but is unique to the project.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="cf773-161">사용자 암호를 사용 하려면 *.csproj* 파일의 `PropertyGroup` 내에서 `UserSecretsId` 요소를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-161">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="cf773-162">`UserSecretsId`의 내부 텍스트는 임의로 이지만 프로젝트에 고유 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-162">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="cf773-163">개발자는 일반적으로 `UserSecretsId`에 대 한 GUID를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-163">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](app-secrets/samples/1.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

::: moniker-end

> [!TIP]
> <span data-ttu-id="cf773-164">Visual Studio의 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 상황에 맞는 메뉴에서 **사용자 암호 관리** 를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-164">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="cf773-165">이 제스처는 GUID로 채워진 `UserSecretsId` 요소를 *.csproj* 파일에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-165">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="cf773-166">비밀 설정</span><span class="sxs-lookup"><span data-stu-id="cf773-166">Set a secret</span></span>

<span data-ttu-id="cf773-167">키와 해당 값으로 구성 된 앱 암호를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-167">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="cf773-168">암호는 프로젝트의 `UserSecretsId` 값과 연결 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-168">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="cf773-169">예를 들어 *.csproj* 파일이 있는 디렉터리에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-169">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="cf773-170">앞의 예제에서 콜론은 `Movies`이 `ServiceApiKey` 속성을 가진 개체 리터럴이어야 함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-170">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="cf773-171">암호 관리자 도구는 다른 디렉터리 에서도 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-171">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="cf773-172">`--project` 옵션을 사용 하 여 *.csproj* 파일이 있는 파일 시스템 경로를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-172">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="cf773-173">예를 들면 다음과 같습니다.:</span><span class="sxs-lookup"><span data-stu-id="cf773-173">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="cf773-174">Visual Studio의 JSON 구조 평면화</span><span class="sxs-lookup"><span data-stu-id="cf773-174">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="cf773-175">Visual Studio의 **사용자 비밀 관리** 제스처는 텍스트 편집기에서 *비밀. json* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-175">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="cf773-176">*비밀. json* 의 내용을 저장할 키-값 쌍으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-176">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="cf773-177">예를 들면 다음과 같습니다.:</span><span class="sxs-lookup"><span data-stu-id="cf773-177">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="cf773-178">JSON 구조는 `dotnet user-secrets remove` 또는 `dotnet user-secrets set`를 통해 수정 된 후에 결합 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-178">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="cf773-179">예를 들어 `dotnet user-secrets remove "Movies:ConnectionString"`를 실행 하면 `Movies` 개체 리터럴이 축소 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-179">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="cf773-180">수정 된 파일은 다음과 유사 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-180">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="cf773-181">여러 비밀 설정</span><span class="sxs-lookup"><span data-stu-id="cf773-181">Set multiple secrets</span></span>

<span data-ttu-id="cf773-182">암호 일괄 처리는 `set` 명령으로 JSON을 파이프 하 여 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-182">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="cf773-183">다음 예제에서는 *입력. json* 파일의 내용이 `set` 명령으로 파이프 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-183">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="cf773-184">Windows</span><span class="sxs-lookup"><span data-stu-id="cf773-184">Windows</span></span>](#tab/windows)

<span data-ttu-id="cf773-185">명령 셸을 열고 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-185">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macostablinuxmacos"></a>[<span data-ttu-id="cf773-186">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="cf773-186">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="cf773-187">명령 셸을 열고 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-187">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="cf773-188">비밀 액세스</span><span class="sxs-lookup"><span data-stu-id="cf773-188">Access a secret</span></span>

<span data-ttu-id="cf773-189">[ASP.NET Core 구성 API](xref:fundamentals/configuration/index) 는 암호 관리자 암호에 대 한 액세스를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-189">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

::: moniker range=">= aspnetcore-2.0 <= aspnetcore-2.2"

<span data-ttu-id="cf773-190">프로젝트가 .NET Framework 대상으로 지정 되는 경우에는 [Microsoft 확장명이](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) . n e t.</span><span class="sxs-lookup"><span data-stu-id="cf773-190">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="cf773-191">ASP.NET Core 2.0 이상에서는 프로젝트가 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>를 호출 하 여 미리 구성 된 기본값을 사용 하 여 호스트의 새 인스턴스를 초기화 하는 경우 개발 모드에서 사용자 암호 구성 소스가 자동으로 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-191">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="cf773-192"><xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>될 때 `CreateDefaultBuilder` <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-192">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0 <= aspnetcore-2.2"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="cf773-193">`CreateDefaultBuilder`를 호출 하지 않은 경우 `Startup` 생성자에서 <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>를 호출 하 여 사용자 암호 구성 소스를 명시적으로 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-193">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="cf773-194">다음 예제와 같이 개발 환경에서 앱이 실행 되는 경우에만 `AddUserSecrets`를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-194">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0 <= aspnetcore-2.2"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="cf773-195">[Microsoft 확장명이 구성](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) 된 NuGet 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-195">Install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

<span data-ttu-id="cf773-196">`Startup` 생성자에서 <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>에 대 한 호출을 사용 하 여 사용자 비밀 구성 소스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-196">Add the user secrets configuration source with a call to <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor:</span></span>

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

<span data-ttu-id="cf773-197">`Configuration` API를 통해 사용자 암호를 검색할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-197">User secrets can be retrieved via the `Configuration` API:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=26)]

::: moniker-end

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="cf773-198">POCO에 비밀 매핑</span><span class="sxs-lookup"><span data-stu-id="cf773-198">Map secrets to a POCO</span></span>

<span data-ttu-id="cf773-199">전체 개체 리터럴을 POCO (속성이 있는 간단한 .NET 클래스)에 매핑하면 관련 된 속성을 집계할 때 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-199">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="cf773-200">위의 비밀을 POCO에 매핑하려면 `Configuration` API의 [개체 그래프 바인딩](xref:fundamentals/configuration/index#bind-to-an-object-graph) 기능을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-200">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="cf773-201">다음 코드는 사용자 지정 `MovieSettings` POCO에 바인딩하고 `ServiceApiKey` 속성 값에 액세스 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-201">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

::: moniker range=">= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

::: moniker-end

::: moniker range="= aspnetcore-1.0"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

::: moniker-end

<span data-ttu-id="cf773-202">`Movies:ConnectionString` 및 `Movies:ServiceApiKey` 암호는 `MovieSettings`의 해당 속성에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-202">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="cf773-203">암호를 사용 하 여 문자열 대체</span><span class="sxs-lookup"><span data-stu-id="cf773-203">String replacement with secrets</span></span>

<span data-ttu-id="cf773-204">암호를 일반 텍스트로 저장 하는 것은 안전 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-204">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="cf773-205">예를 들어 *appsettings* 에 저장 된 데이터베이스 연결 문자열은 지정 된 사용자에 대 한 암호를 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-205">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="cf773-206">보다 안전한 방법은 암호를 비밀로 저장 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-206">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="cf773-207">예를 들면 다음과 같습니다.:</span><span class="sxs-lookup"><span data-stu-id="cf773-207">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="cf773-208">*Appsettings*의 연결 문자열에서 `Password` 키-값 쌍을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-208">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="cf773-209">예를 들면 다음과 같습니다.:</span><span class="sxs-lookup"><span data-stu-id="cf773-209">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="cf773-210"><xref:System.Data.SqlClient.SqlConnectionStringBuilder> 개체의 <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> 속성에 대해 암호의 값을 설정 하 여 연결 문자열을 완료할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-210">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=26-29)]

::: moniker-end

## <a name="list-the-secrets"></a><span data-ttu-id="cf773-211">암호 나열</span><span class="sxs-lookup"><span data-stu-id="cf773-211">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="cf773-212">*.Csproj* 파일이 있는 디렉터리에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-212">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="cf773-213">다음 출력이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-213">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="cf773-214">앞의 예제에서 키 이름의 콜론은 *비밀이*포함 된 개체 계층 구조를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-214">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="cf773-215">단일 비밀 제거</span><span class="sxs-lookup"><span data-stu-id="cf773-215">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="cf773-216">*.Csproj* 파일이 있는 디렉터리에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-216">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="cf773-217">`MoviesConnectionString` 키와 연결 된 키-값 쌍을 제거 하기 위해 앱의 *비밀. json* 파일이 수정 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-217">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="cf773-218">`dotnet user-secrets list`를 실행 하면 다음과 같은 메시지가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-218">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="cf773-219">모든 비밀 제거</span><span class="sxs-lookup"><span data-stu-id="cf773-219">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="cf773-220">*.Csproj* 파일이 있는 디렉터리에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-220">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="cf773-221">앱에 대 한 모든 사용자 비밀이 *비밀. json* 파일에서 삭제 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-221">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="cf773-222">`dotnet user-secrets list`를 실행 하면 다음과 같은 메시지가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cf773-222">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="cf773-223">추가 자료</span><span class="sxs-lookup"><span data-stu-id="cf773-223">Additional resources</span></span>

* <span data-ttu-id="cf773-224">IIS에서 암호 관리자에 액세스 하는 방법에 대 한 자세한 내용은 [이 문제](https://github.com/aspnet/AspNetCore.Docs/issues/16328) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="cf773-224">See [this issue](https://github.com/aspnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>
