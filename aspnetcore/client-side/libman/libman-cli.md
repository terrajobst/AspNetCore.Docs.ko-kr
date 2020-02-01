---
title: ASP.NET Core와 함께를 사용 합니다.
author: scottaddie
description: ASP.NET Core 프로젝트에서를 사용 하는 방법에 대해 알아봅니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: client-side/libman/libman-cli
ms.openlocfilehash: 02d88d09805bd23a86ef924766373245fec7ff52
ms.sourcegitcommit: 0b0e485a8a6dfcc65a7a58b365622b3839f4d624
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76928357"
---
# <a name="use-the-libman-cli-with-aspnet-core"></a><span data-ttu-id="6c906-103">ASP.NET Core와 함께를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-103">Use the LibMan CLI with ASP.NET Core</span></span>

<span data-ttu-id="6c906-104">작성자: [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="6c906-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="6c906-105">이 기능을 지 원하는 플랫폼 간 도구는 .NET [Core에서 지원](xref:client-side/libman/index) 되는 모든 플랫폼에서 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-105">The [LibMan](xref:client-side/libman/index) CLI is a cross-platform tool that's supported everywhere .NET Core is supported.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6c906-106">전제 조건</span><span class="sxs-lookup"><span data-stu-id="6c906-106">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="6c906-107">설치</span><span class="sxs-lookup"><span data-stu-id="6c906-107">Installation</span></span>

<span data-ttu-id="6c906-108">을 설치 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-108">To install the LibMan CLI:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

<span data-ttu-id="6c906-109">[.Net Core 전역 도구](/dotnet/core/tools/global-tools#install-a-global-tool) 는 [Microsoft web.config](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) 패키지에서 설치 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-109">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet package.</span></span>

<span data-ttu-id="6c906-110">특정 NuGet 패키지 원본에서 패키지를 설치 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-110">To install the LibMan CLI from a specific NuGet package source:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

<span data-ttu-id="6c906-111">위의 예제에서 .NET Core 글로벌 도구는 로컬 Windows 컴퓨터의 *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* 파일에서 설치 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-111">In the preceding example, a .NET Core Global Tool is installed from the local Windows machine's *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* file.</span></span>

## <a name="usage"></a><span data-ttu-id="6c906-112">용도</span><span class="sxs-lookup"><span data-stu-id="6c906-112">Usage</span></span>

<span data-ttu-id="6c906-113">CLI를 성공적으로 설치한 후에는 다음 명령을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-113">After successful installation of the CLI, the following command can be used:</span></span>

```console
libman
```

<span data-ttu-id="6c906-114">설치 된 CLI 버전을 보려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-114">To view the installed CLI version:</span></span>

```console
libman --version
```

<span data-ttu-id="6c906-115">사용 가능한 CLI 명령을 보려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-115">To view the available CLI commands:</span></span>

```console
libman --help
```

<span data-ttu-id="6c906-116">위의 명령은 다음과 유사한 출력을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-116">The preceding command displays output similar to the following:</span></span>

```console
 1.0.163+g45474d37ed

Usage: libman [options] [command]

Options:
  --help|-h  Show help information
  --version  Show version information

Commands:
  cache      List or clean libman cache contents
  clean      Deletes all library files defined in libman.json from the project
  init       Create a new libman.json
  install    Add a library definition to the libman.json file, and download the 
             library to the specified location
  restore    Downloads all files from provider and saves them to specified 
             destination
  uninstall  Deletes all files for the specified library from their specified 
             destination, then removes the specified library definition from 
             libman.json
  update     Updates the specified library

Use "libman [command] --help" for more information about a command.
```

<span data-ttu-id="6c906-117">다음 섹션에서는 사용 가능한 CLI 명령에 대해 간략하게 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-117">The following sections outline the available CLI commands.</span></span>

## <a name="initialize-libman-in-the-project"></a><span data-ttu-id="6c906-118">프로젝트에서이를 초기화 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-118">Initialize LibMan in the project</span></span>

<span data-ttu-id="6c906-119">`libman init` *명령은 파일 (* 없는 경우)을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-119">The `libman init` command creates a *libman.json* file if one doesn't exist.</span></span> <span data-ttu-id="6c906-120">기본 항목 템플릿 콘텐츠를 사용 하 여 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-120">The file is created with the default item template content.</span></span>

### <a name="synopsis"></a><span data-ttu-id="6c906-121">개요</span><span class="sxs-lookup"><span data-stu-id="6c906-121">Synopsis</span></span>

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a><span data-ttu-id="6c906-122">Options</span><span class="sxs-lookup"><span data-stu-id="6c906-122">Options</span></span>

<span data-ttu-id="6c906-123">다음 옵션은 `libman init` 명령에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-123">The following options are available for the `libman init` command:</span></span>

* `-d|--default-destination <PATH>`

  <span data-ttu-id="6c906-124">현재 폴더에 상대적인 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-124">A path relative to the current folder.</span></span> <span data-ttu-id="6c906-125">라이브러리 파일은 라이브러리에 대 한 `destination` 속성이 정의 되지 않은 *경우에는*이 위치에 설치 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-125">Library files are installed in this location if no `destination` property is defined for a library in *libman.json*.</span></span> <span data-ttu-id="6c906-126">`<PATH>` 값은 해당 `defaultDestination` 속성에 기록 *됩니다.*</span><span class="sxs-lookup"><span data-stu-id="6c906-126">The `<PATH>` value is written to the `defaultDestination` property of *libman.json*.</span></span>

* `-p|--default-provider <PROVIDER>`

  <span data-ttu-id="6c906-127">지정 된 라이브러리에 대해 정의 된 공급자가 없는 경우 사용할 공급자입니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-127">The provider to use if no provider is defined for a given library.</span></span> <span data-ttu-id="6c906-128">`<PROVIDER>` 값은 해당 `defaultProvider` 속성에 기록 *됩니다.*</span><span class="sxs-lookup"><span data-stu-id="6c906-128">The `<PROVIDER>` value is written to the `defaultProvider` property of *libman.json*.</span></span> <span data-ttu-id="6c906-129">`<PROVIDER>`을 다음 값 중 하나로 대체하세요.</span><span class="sxs-lookup"><span data-stu-id="6c906-129">Replace `<PROVIDER>` with one of the following values:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="6c906-130">예</span><span class="sxs-lookup"><span data-stu-id="6c906-130">Examples</span></span>

<span data-ttu-id="6c906-131">ASP.NET Core 프로젝트에서이 파일을 만들려면 다음을 수행 *합니다* .</span><span class="sxs-lookup"><span data-stu-id="6c906-131">To create a *libman.json* file in an ASP.NET Core project:</span></span>

* <span data-ttu-id="6c906-132">프로젝트 루트로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-132">Navigate to the project root.</span></span>
* <span data-ttu-id="6c906-133">명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-133">Run the following command:</span></span>

  ```console
  libman init
  ```

* <span data-ttu-id="6c906-134">기본 공급자의 이름을 입력 하거나 `Enter`를 눌러 기본 CDNJS 공급자를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-134">Type the name of the default provider, or press `Enter` to use the default CDNJS provider.</span></span> <span data-ttu-id="6c906-135">유효한 값은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-135">Valid values include:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![liman init 명령-기본 공급자](_static/libman-init-provider.png)

<span data-ttu-id="6c906-137">다음 콘텐츠를 사용 하 여 프로젝트 루트 *에 파일을 추가 합니다.*</span><span class="sxs-lookup"><span data-stu-id="6c906-137">A *libman.json* file is added to the project root with the following content:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a><span data-ttu-id="6c906-138">라이브러리 파일 추가하기</span><span class="sxs-lookup"><span data-stu-id="6c906-138">Add library files</span></span>

<span data-ttu-id="6c906-139">`libman install` 명령은 라이브러리 파일을 다운로드 하 여 프로젝트에 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-139">The `libman install` command downloads and installs library files into the project.</span></span> <span data-ttu-id="6c906-140">파일이 없는 경우 추가 됩니다 *.*</span><span class="sxs-lookup"><span data-stu-id="6c906-140">A *libman.json* file is added if one doesn't exist.</span></span> <span data-ttu-id="6c906-141">라이브러리 파일에 대 한 구성 세부 정보를 저장 하도록 *라이브러리 파일을* 수정 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-141">The *libman.json* file is modified to store configuration details for the library files.</span></span>

### <a name="synopsis"></a><span data-ttu-id="6c906-142">개요</span><span class="sxs-lookup"><span data-stu-id="6c906-142">Synopsis</span></span>

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="6c906-143">인수</span><span class="sxs-lookup"><span data-stu-id="6c906-143">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="6c906-144">설치할 라이브러리의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-144">The name of the library to install.</span></span> <span data-ttu-id="6c906-145">이 이름에는 버전 번호 표기법 (예: `@1.2.0`)이 포함 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-145">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="6c906-146">Options</span><span class="sxs-lookup"><span data-stu-id="6c906-146">Options</span></span>

<span data-ttu-id="6c906-147">다음 옵션은 `libman install` 명령에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-147">The following options are available for the `libman install` command:</span></span>

* `-d|--destination <PATH>`

  <span data-ttu-id="6c906-148">라이브러리를 설치할 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-148">The location to install the library.</span></span> <span data-ttu-id="6c906-149">지정 하지 않으면 기본 위치가 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-149">If not specified, the default location is used.</span></span> <span data-ttu-id="6c906-150">`defaultDestination` 속성이 지정 되지 않은 *경우에는*이 옵션이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-150">If no `defaultDestination` property is specified in *libman.json*, this option is required.</span></span>

* `--files <FILE>`

  <span data-ttu-id="6c906-151">라이브러리에서 설치할 파일의 이름을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-151">Specify the name of the file to install from the library.</span></span> <span data-ttu-id="6c906-152">지정 하지 않으면 라이브러리의 모든 파일이 설치 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-152">If not specified, all files from the library are installed.</span></span> <span data-ttu-id="6c906-153">설치할 파일 마다 하나의 `--files` 옵션을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-153">Provide one `--files` option per file to be installed.</span></span> <span data-ttu-id="6c906-154">상대 경로도 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-154">Relative paths are supported too.</span></span> <span data-ttu-id="6c906-155">예를 들어 `--files dist/browser/signalr.js`을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="6c906-155">For example: `--files dist/browser/signalr.js`.</span></span>

* `-p|--provider <PROVIDER>`

  <span data-ttu-id="6c906-156">라이브러리 획득에 사용할 공급자의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-156">The name of the provider to use for the library acquisition.</span></span> <span data-ttu-id="6c906-157">`<PROVIDER>`을 다음 값 중 하나로 대체하세요.</span><span class="sxs-lookup"><span data-stu-id="6c906-157">Replace `<PROVIDER>` with one of the following values:</span></span>
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  <span data-ttu-id="6c906-158">`defaultProvider` 지정 하지 *않으면이 속성을 사용 합니다.*</span><span class="sxs-lookup"><span data-stu-id="6c906-158">If not specified, the `defaultProvider` property in *libman.json* is used.</span></span> <span data-ttu-id="6c906-159">`defaultProvider` 속성이 지정 되지 않은 *경우에는*이 옵션이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-159">If no `defaultProvider` property is specified in *libman.json*, this option is required.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="6c906-160">예</span><span class="sxs-lookup"><span data-stu-id="6c906-160">Examples</span></span>

<span data-ttu-id="6c906-161">다음을 고려 하십시오 *. json* 파일:</span><span class="sxs-lookup"><span data-stu-id="6c906-161">Consider the following *libman.json* file:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

<span data-ttu-id="6c906-162">JQuery 버전 3.2.1 *jquery* 파일을 CDNJS 공급자를 사용 하는 *wwwroot/scripts/jQuery* 폴더에 설치 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-162">To install the jQuery version 3.2.1 *jquery.min.js* file to the *wwwroot/scripts/jquery* folder using the CDNJS provider:</span></span>

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

<span data-ttu-id="6c906-163">이 *파일은* 다음과 유사 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-163">The *libman.json* file resembles the following:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    }
  ]
}
```

<span data-ttu-id="6c906-164">파일 시스템 공급자를 사용 하 여 *C:\\temp\\contosoCalendar\\* 에서 *node.js* 및 *calendar .css* 파일을 설치 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-164">To install the *calendar.js* and *calendar.css* files from *C:\\temp\\contosoCalendar\\* using the file system provider:</span></span>

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

<span data-ttu-id="6c906-165">다음은 두 가지 이유로 표시 되는 메시지입니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-165">The following prompt appears for two reasons:</span></span>

* <span data-ttu-id="6c906-166">이 파일에는 `defaultDestination` 속성이 포함 되어 있지 않습니다 *.*</span><span class="sxs-lookup"><span data-stu-id="6c906-166">The *libman.json* file doesn't contain a `defaultDestination` property.</span></span>
* <span data-ttu-id="6c906-167">`libman install` 명령에는 `-d|--destination` 옵션이 포함 되어 있지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-167">The `libman install` command doesn't contain the `-d|--destination` option.</span></span>

![\man 설치 명령-대상](_static/libman-install-destination.png)

<span data-ttu-id="6c906-169">기본 대상을 승인한 후 *에는 다음과* 같은 경우에 해당 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-169">After accepting the default destination, the *libman.json* file resembles the following:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    },
    {
      "library": "C:\\temp\\contosoCalendar\\",
      "provider": "filesystem",
      "destination": "wwwroot/lib/contosoCalendar",
      "files": [
        "calendar.js",
        "calendar.css"
      ]
    }
  ]
}
```

## <a name="restore-library-files"></a><span data-ttu-id="6c906-170">라이브러리 파일 복원하기</span><span class="sxs-lookup"><span data-stu-id="6c906-170">Restore library files</span></span>

<span data-ttu-id="6c906-171">`libman restore` 명령은 라이브러리 파일을 설치 *합니다.*</span><span class="sxs-lookup"><span data-stu-id="6c906-171">The `libman restore` command installs library files defined in *libman.json*.</span></span> <span data-ttu-id="6c906-172">이 때 적용되는 규칙은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-172">The following rules apply:</span></span>

* <span data-ttu-id="6c906-173">프로젝트 루트에 없습니다 *. json* 파일이 없으면 오류가 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-173">If no *libman.json* file exists in the project root, an error is returned.</span></span>
* <span data-ttu-id="6c906-174">라이브러리에서 공급자를 지정 하는 *경우에는* 라이브러리의 `defaultProvider` 속성이 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-174">If a library specifies a provider, the `defaultProvider` property in *libman.json* is ignored.</span></span>
* <span data-ttu-id="6c906-175">라이브러리에서 대상을 지정 하는 *경우에는* 라이브러리의 `defaultDestination` 속성이 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-175">If a library specifies a destination, the `defaultDestination` property in *libman.json* is ignored.</span></span>

### <a name="synopsis"></a><span data-ttu-id="6c906-176">개요</span><span class="sxs-lookup"><span data-stu-id="6c906-176">Synopsis</span></span>

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a><span data-ttu-id="6c906-177">Options</span><span class="sxs-lookup"><span data-stu-id="6c906-177">Options</span></span>

<span data-ttu-id="6c906-178">다음 옵션은 `libman restore` 명령에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-178">The following options are available for the `libman restore` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="6c906-179">예</span><span class="sxs-lookup"><span data-stu-id="6c906-179">Examples</span></span>

<span data-ttu-id="6c906-180">라이브러리 파일을 복원 하려면 다음을 수행 합니다. *json*:</span><span class="sxs-lookup"><span data-stu-id="6c906-180">To restore the library files defined in *libman.json*:</span></span>

```console
libman restore
```

## <a name="delete-library-files"></a><span data-ttu-id="6c906-181">라이브러리 파일 삭제하기</span><span class="sxs-lookup"><span data-stu-id="6c906-181">Delete library files</span></span>

<span data-ttu-id="6c906-182">`libman clean` 명령은 해당 라이브러리 파일을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-182">The `libman clean` command deletes library files previously restored via LibMan.</span></span> <span data-ttu-id="6c906-183">이 작업이 삭제 된 후 비어 있게 되는 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-183">Folders that become empty after this operation are deleted.</span></span> <span data-ttu-id="6c906-184">라이브러리 파일의 연결 된 구성은 라이브러리 파일의 `libraries` 속성에 있습니다. *json* 은 제거 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-184">The library files' associated configurations in the `libraries` property of *libman.json* aren't removed.</span></span>

### <a name="synopsis"></a><span data-ttu-id="6c906-185">개요</span><span class="sxs-lookup"><span data-stu-id="6c906-185">Synopsis</span></span>

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a><span data-ttu-id="6c906-186">Options</span><span class="sxs-lookup"><span data-stu-id="6c906-186">Options</span></span>

<span data-ttu-id="6c906-187">다음 옵션은 `libman clean` 명령에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-187">The following options are available for the `libman clean` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="6c906-188">예</span><span class="sxs-lookup"><span data-stu-id="6c906-188">Examples</span></span>

<span data-ttu-id="6c906-189">라이브러리 파일을 삭제 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-189">To delete library files installed via LibMan:</span></span>

```console
libman clean
```

## <a name="uninstall-library-files"></a><span data-ttu-id="6c906-190">라이브러리 파일 제거하기</span><span class="sxs-lookup"><span data-stu-id="6c906-190">Uninstall library files</span></span>

<span data-ttu-id="6c906-191">`libman uninstall` 명령은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-191">The `libman uninstall` command:</span></span>

* <span data-ttu-id="6c906-192">지정 된 라이브러리와 연결 된 모든 파일을 *라이브러리의 대상에서 삭제 합니다.*</span><span class="sxs-lookup"><span data-stu-id="6c906-192">Deletes all files associated with the specified library from the destination in *libman.json*.</span></span>
* <span data-ttu-id="6c906-193">연결 된 라이브러리 구성을 제거 합니다 *.*</span><span class="sxs-lookup"><span data-stu-id="6c906-193">Removes the associated library configuration from *libman.json*.</span></span>

<span data-ttu-id="6c906-194">다음과 같은 경우 오류가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-194">An error occurs when:</span></span>

* <span data-ttu-id="6c906-195">프로젝트 루트에 없습니다 *. json* 파일이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-195">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="6c906-196">지정 된 라이브러리가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-196">The specified library doesn't exist.</span></span>

<span data-ttu-id="6c906-197">이름이 같은 라이브러리가 둘 이상 설치 되어 있으면 하나를 선택 하 라는 메시지가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-197">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="6c906-198">개요</span><span class="sxs-lookup"><span data-stu-id="6c906-198">Synopsis</span></span>

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="6c906-199">인수</span><span class="sxs-lookup"><span data-stu-id="6c906-199">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="6c906-200">제거할 라이브러리의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-200">The name of the library to uninstall.</span></span> <span data-ttu-id="6c906-201">이 이름에는 버전 번호 표기법 (예: `@1.2.0`)이 포함 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-201">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="6c906-202">Options</span><span class="sxs-lookup"><span data-stu-id="6c906-202">Options</span></span>

<span data-ttu-id="6c906-203">다음 옵션은 `libman uninstall` 명령에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-203">The following options are available for the `libman uninstall` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="6c906-204">예</span><span class="sxs-lookup"><span data-stu-id="6c906-204">Examples</span></span>

<span data-ttu-id="6c906-205">다음을 고려 하십시오 *. json* 파일:</span><span class="sxs-lookup"><span data-stu-id="6c906-205">Consider the following *libman.json* file:</span></span>

[!code-json[](samples/LibManSample/libman.json)]

* <span data-ttu-id="6c906-206">JQuery를 제거 하려면 다음 명령 중 하나를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-206">To uninstall jQuery, either of the following commands succeed:</span></span>

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* <span data-ttu-id="6c906-207">`filesystem` 공급자를 통해 설치 된 Lodash 파일을 제거 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-207">To uninstall the Lodash files installed via the `filesystem` provider:</span></span>

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a><span data-ttu-id="6c906-208">라이브러리 버전 업데이트</span><span class="sxs-lookup"><span data-stu-id="6c906-208">Update library version</span></span>

<span data-ttu-id="6c906-209">`libman update` 명령은 지정 된 버전에 라이브러리를 통해 설치 된 라이브러리를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-209">The `libman update` command updates a library installed via LibMan to the specified version.</span></span>

<span data-ttu-id="6c906-210">다음과 같은 경우 오류가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-210">An error occurs when:</span></span>

* <span data-ttu-id="6c906-211">프로젝트 루트에 없습니다 *. json* 파일이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-211">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="6c906-212">지정 된 라이브러리가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-212">The specified library doesn't exist.</span></span>

<span data-ttu-id="6c906-213">이름이 같은 라이브러리가 둘 이상 설치 되어 있으면 하나를 선택 하 라는 메시지가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-213">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="6c906-214">개요</span><span class="sxs-lookup"><span data-stu-id="6c906-214">Synopsis</span></span>

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="6c906-215">인수</span><span class="sxs-lookup"><span data-stu-id="6c906-215">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="6c906-216">업데이트할 라이브러리의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-216">The name of the library to update.</span></span>

### <a name="options"></a><span data-ttu-id="6c906-217">Options</span><span class="sxs-lookup"><span data-stu-id="6c906-217">Options</span></span>

<span data-ttu-id="6c906-218">다음 옵션은 `libman update` 명령에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-218">The following options are available for the `libman update` command:</span></span>

* `-pre`

  <span data-ttu-id="6c906-219">라이브러리의 최신 시험판 버전을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-219">Obtain the latest prerelease version of the library.</span></span>

* `--to <VERSION>`

  <span data-ttu-id="6c906-220">라이브러리의 특정 버전을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-220">Obtain a specific version of the library.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="6c906-221">예</span><span class="sxs-lookup"><span data-stu-id="6c906-221">Examples</span></span>

* <span data-ttu-id="6c906-222">JQuery를 최신 버전으로 업데이트 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-222">To update jQuery to the latest version:</span></span>

  ```console
  libman update jquery
  ```

* <span data-ttu-id="6c906-223">JQuery를 버전 3.3.1로 업데이트 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-223">To update jQuery to version 3.3.1:</span></span>

  ```console
  libman update jquery --to 3.3.1
  ```

* <span data-ttu-id="6c906-224">JQuery를 최신 시험판 버전으로 업데이트 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-224">To update jQuery to the latest prerelease version:</span></span>

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a><span data-ttu-id="6c906-225">라이브러리 캐시 관리</span><span class="sxs-lookup"><span data-stu-id="6c906-225">Manage library cache</span></span>

<span data-ttu-id="6c906-226">`libman cache` 명령은 라이브러리 캐시를 관리 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-226">The `libman cache` command manages the LibMan library cache.</span></span> <span data-ttu-id="6c906-227">`filesystem` 공급자는 라이브러리 캐시를 사용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-227">The `filesystem` provider doesn't use the library cache.</span></span>

### <a name="synopsis"></a><span data-ttu-id="6c906-228">개요</span><span class="sxs-lookup"><span data-stu-id="6c906-228">Synopsis</span></span>

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="6c906-229">인수</span><span class="sxs-lookup"><span data-stu-id="6c906-229">Arguments</span></span>

`PROVIDER`

<span data-ttu-id="6c906-230">`clean` 명령 에서만 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-230">Only used with the `clean` command.</span></span> <span data-ttu-id="6c906-231">정리할 공급자 캐시를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-231">Specifies the provider cache to clean.</span></span> <span data-ttu-id="6c906-232">유효한 값은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-232">Valid values include:</span></span>

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a><span data-ttu-id="6c906-233">Options</span><span class="sxs-lookup"><span data-stu-id="6c906-233">Options</span></span>

<span data-ttu-id="6c906-234">다음 옵션은 `libman cache` 명령에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-234">The following options are available for the `libman cache` command:</span></span>

* `--files`

  <span data-ttu-id="6c906-235">캐시 된 파일의 이름을 나열 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-235">List the names of files that are cached.</span></span>

* `--libraries`

  <span data-ttu-id="6c906-236">캐시 된 라이브러리의 이름을 나열 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-236">List the names of libraries that are cached.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="6c906-237">예</span><span class="sxs-lookup"><span data-stu-id="6c906-237">Examples</span></span>

* <span data-ttu-id="6c906-238">공급자 당 캐시 된 라이브러리의 이름을 보려면 다음 명령 중 하나를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-238">To view the names of cached libraries per provider, use one of the following commands:</span></span>

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  <span data-ttu-id="6c906-239">다음과 비슷한 출력이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-239">Output similar to the following is displayed:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      font-awesome
      jquery
      knockout
      lodash.js
      react
  ```

* <span data-ttu-id="6c906-240">공급자 당 캐시 된 라이브러리 파일의 이름을 보려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-240">To view the names of cached library files per provider:</span></span>

  ```console
  libman cache list --files
  ```

  <span data-ttu-id="6c906-241">다음과 비슷한 출력이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-241">Output similar to the following is displayed:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout:
          <list omitted for brevity>
      react:
          <list omitted for brevity>
      vue:
          <list omitted for brevity>
  cdnjs:
      font-awesome
          metadata.json
      jquery
          metadata.json
          3.2.1\core.js
          3.2.1\jquery.js
          3.2.1\jquery.min.js
          3.2.1\jquery.min.map
          3.2.1\jquery.slim.js
          3.2.1\jquery.slim.min.js
          3.2.1\jquery.slim.min.map
          3.3.1\core.js
          3.3.1\jquery.js
          3.3.1\jquery.min.js
          3.3.1\jquery.min.map
          3.3.1\jquery.slim.js
          3.3.1\jquery.slim.min.js
          3.3.1\jquery.slim.min.map
      knockout
          metadata.json
          3.4.2\knockout-debug.js
          3.4.2\knockout-min.js
      lodash.js
          metadata.json
          4.17.10\lodash.js
          4.17.10\lodash.min.js
      react
          metadata.json
  ```

  <span data-ttu-id="6c906-242">위의 출력은 jQuery 버전 3.2.1 및 3.3.1가 CDNJS 공급자 아래에 캐시 됨을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-242">Notice the preceding output shows that jQuery versions 3.2.1 and 3.3.1 are cached under the CDNJS provider.</span></span>

* <span data-ttu-id="6c906-243">CDNJS 공급자에 대 한 라이브러리 캐시를 비우려면:</span><span class="sxs-lookup"><span data-stu-id="6c906-243">To empty the library cache for the CDNJS provider:</span></span>

  ```console
  libman cache clean cdnjs
  ```

  <span data-ttu-id="6c906-244">CDNJS 공급자 캐시를 비운 후 `libman cache list` 명령은 다음을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-244">After emptying the CDNJS provider cache, the `libman cache list` command displays the following:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      (empty)
  ```

* <span data-ttu-id="6c906-245">지원 되는 모든 공급자에 대 한 캐시를 비우려면:</span><span class="sxs-lookup"><span data-stu-id="6c906-245">To empty the cache for all supported providers:</span></span>

  ```console
  libman cache clean
  ```

  <span data-ttu-id="6c906-246">모든 공급자 캐시를 비운 후 `libman cache list` 명령은 다음을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="6c906-246">After emptying all provider caches, the `libman cache list` command displays the following:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a><span data-ttu-id="6c906-247">추가 자료</span><span class="sxs-lookup"><span data-stu-id="6c906-247">Additional resources</span></span>

* [<span data-ttu-id="6c906-248">글로벌 도구 설치</span><span class="sxs-lookup"><span data-stu-id="6c906-248">Install a Global Tool</span></span>](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [<span data-ttu-id="6c906-249">LibMan GitHub 리포지토리</span><span class="sxs-lookup"><span data-stu-id="6c906-249">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
