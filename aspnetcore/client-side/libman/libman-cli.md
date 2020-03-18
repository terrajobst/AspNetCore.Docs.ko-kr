---
title: ASP.NET Core에서 LibMan CLI 사용
author: scottaddie
description: ASP.NET Core 프로젝트에서 LibMan CLI를 사용하는 방법을 알아봅니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: client-side/libman/libman-cli
ms.openlocfilehash: 02d88d09805bd23a86ef924766373245fec7ff52
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78649605"
---
# <a name="use-the-libman-cli-with-aspnet-core"></a><span data-ttu-id="cddef-103">ASP.NET Core에서 LibMan CLI 사용</span><span class="sxs-lookup"><span data-stu-id="cddef-103">Use the LibMan CLI with ASP.NET Core</span></span>

<span data-ttu-id="cddef-104">작성자: [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="cddef-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="cddef-105">[LibMan](xref:client-side/libman/index) CLI는 .NET Core가 지원되는 플랫폼 간 도구입니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-105">The [LibMan](xref:client-side/libman/index) CLI is a cross-platform tool that's supported everywhere .NET Core is supported.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="cddef-106">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="cddef-106">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="cddef-107">설치</span><span class="sxs-lookup"><span data-stu-id="cddef-107">Installation</span></span>

<span data-ttu-id="cddef-108">LibMan CLI를 설치하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-108">To install the LibMan CLI:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

<span data-ttu-id="cddef-109">[.NET Core 전역 도구](/dotnet/core/tools/global-tools#install-a-global-tool)가 [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet 패키지에서 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-109">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet package.</span></span>

<span data-ttu-id="cddef-110">특정 NuGet 패키지 원본에서 LibMan CLI를 설치하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-110">To install the LibMan CLI from a specific NuGet package source:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

<span data-ttu-id="cddef-111">위의 예제에서 .NET Core 전역 도구는 로컬 Windows 머신의 *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* 파일에서 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-111">In the preceding example, a .NET Core Global Tool is installed from the local Windows machine's *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* file.</span></span>

## <a name="usage"></a><span data-ttu-id="cddef-112">사용법</span><span class="sxs-lookup"><span data-stu-id="cddef-112">Usage</span></span>

<span data-ttu-id="cddef-113">CLI를 성공적으로 설치한 후에는 다음 명령을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-113">After successful installation of the CLI, the following command can be used:</span></span>

```console
libman
```

<span data-ttu-id="cddef-114">설치된 CLI 버전을 보려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-114">To view the installed CLI version:</span></span>

```console
libman --version
```

<span data-ttu-id="cddef-115">사용 가능한 CLI 명령을 보려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-115">To view the available CLI commands:</span></span>

```console
libman --help
```

<span data-ttu-id="cddef-116">위의 명령은 다음과 유사한 출력을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-116">The preceding command displays output similar to the following:</span></span>

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

<span data-ttu-id="cddef-117">다음 섹션에서는 사용 가능한 CLI 명령에 대해 간략하게 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-117">The following sections outline the available CLI commands.</span></span>

## <a name="initialize-libman-in-the-project"></a><span data-ttu-id="cddef-118">프로젝트에서 LibMan 초기화</span><span class="sxs-lookup"><span data-stu-id="cddef-118">Initialize LibMan in the project</span></span>

<span data-ttu-id="cddef-119">`libman init` 명령은 *libman.json* 파일이 없는 경우 새로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-119">The `libman init` command creates a *libman.json* file if one doesn't exist.</span></span> <span data-ttu-id="cddef-120">이 파일은 기본 항목 템플릿 콘텐츠를 사용하여 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-120">The file is created with the default item template content.</span></span>

### <a name="synopsis"></a><span data-ttu-id="cddef-121">개요</span><span class="sxs-lookup"><span data-stu-id="cddef-121">Synopsis</span></span>

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a><span data-ttu-id="cddef-122">옵션</span><span class="sxs-lookup"><span data-stu-id="cddef-122">Options</span></span>

<span data-ttu-id="cddef-123">다음 옵션은 `libman init` 명령에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-123">The following options are available for the `libman init` command:</span></span>

* `-d|--default-destination <PATH>`

  <span data-ttu-id="cddef-124">현재 폴더에 상대적인 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-124">A path relative to the current folder.</span></span> <span data-ttu-id="cddef-125">*libman.json*의 라이브러리에 대해 `destination` 속성이 정의되지 않으면 라이브러리 파일이 이 위치에 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-125">Library files are installed in this location if no `destination` property is defined for a library in *libman.json*.</span></span> <span data-ttu-id="cddef-126">`<PATH>` 값은 *libman.json*의 `defaultDestination` 속성에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-126">The `<PATH>` value is written to the `defaultDestination` property of *libman.json*.</span></span>

* `-p|--default-provider <PROVIDER>`

  <span data-ttu-id="cddef-127">지정된 라이브러리에 대해 공급자를 지정하지 않은 경우 사용할 공급자입니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-127">The provider to use if no provider is defined for a given library.</span></span> <span data-ttu-id="cddef-128">`<PROVIDER>` 값은 *libman.json*의 `defaultProvider` 속성에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-128">The `<PROVIDER>` value is written to the `defaultProvider` property of *libman.json*.</span></span> <span data-ttu-id="cddef-129">`<PROVIDER>`을 다음 값 중 하나로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-129">Replace `<PROVIDER>` with one of the following values:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="cddef-130">예</span><span class="sxs-lookup"><span data-stu-id="cddef-130">Examples</span></span>

<span data-ttu-id="cddef-131">ASP.NET Core 프로젝트에서 *libman.json* 파일을 만들려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-131">To create a *libman.json* file in an ASP.NET Core project:</span></span>

* <span data-ttu-id="cddef-132">프로젝트 루트로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-132">Navigate to the project root.</span></span>
* <span data-ttu-id="cddef-133">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-133">Run the following command:</span></span>

  ```console
  libman init
  ```

* <span data-ttu-id="cddef-134">기본 공급자의 이름을 입력하거나 `Enter` 키를 눌러 기본 CDNJS 공급자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-134">Type the name of the default provider, or press `Enter` to use the default CDNJS provider.</span></span> <span data-ttu-id="cddef-135">유효한 값은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-135">Valid values include:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![libman init 명령 - 기본 공급자](_static/libman-init-provider.png)

<span data-ttu-id="cddef-137">다음 콘텐츠를 사용하여 *libman.json* 파일이 프로젝트 루트에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-137">A *libman.json* file is added to the project root with the following content:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a><span data-ttu-id="cddef-138">라이브러리 파일 추가</span><span class="sxs-lookup"><span data-stu-id="cddef-138">Add library files</span></span>

<span data-ttu-id="cddef-139">`libman install` 명령은 라이브러리 파일을 다운로드하여 프로젝트에 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-139">The `libman install` command downloads and installs library files into the project.</span></span> <span data-ttu-id="cddef-140">*libman.json* 파일이 없는 경우 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-140">A *libman.json* file is added if one doesn't exist.</span></span> <span data-ttu-id="cddef-141">라이브러리 파일에 대한 구성 세부 정보를 저장하도록 *libman.json* 파일이 수정됩니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-141">The *libman.json* file is modified to store configuration details for the library files.</span></span>

### <a name="synopsis"></a><span data-ttu-id="cddef-142">개요</span><span class="sxs-lookup"><span data-stu-id="cddef-142">Synopsis</span></span>

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="cddef-143">인수</span><span class="sxs-lookup"><span data-stu-id="cddef-143">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="cddef-144">설치할 라이브러리의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-144">The name of the library to install.</span></span> <span data-ttu-id="cddef-145">이 이름에는 버전 번호 표기법(예: `@1.2.0`)이 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-145">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="cddef-146">옵션</span><span class="sxs-lookup"><span data-stu-id="cddef-146">Options</span></span>

<span data-ttu-id="cddef-147">다음 옵션은 `libman install` 명령에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-147">The following options are available for the `libman install` command:</span></span>

* `-d|--destination <PATH>`

  <span data-ttu-id="cddef-148">라이브러리를 설치할 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-148">The location to install the library.</span></span> <span data-ttu-id="cddef-149">이 값을 지정하지 않으면 기본 위치가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-149">If not specified, the default location is used.</span></span> <span data-ttu-id="cddef-150">*libman.json*에 `defaultDestination` 속성이 지정되지 않은 경우 이 옵션은 필수입니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-150">If no `defaultDestination` property is specified in *libman.json*, this option is required.</span></span>

* `--files <FILE>`

  <span data-ttu-id="cddef-151">라이브러리에서 설치할 파일의 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-151">Specify the name of the file to install from the library.</span></span> <span data-ttu-id="cddef-152">지정하지 않으면 라이브러리의 모든 파일이 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-152">If not specified, all files from the library are installed.</span></span> <span data-ttu-id="cddef-153">설치할 파일마다 하나의 `--files` 옵션을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-153">Provide one `--files` option per file to be installed.</span></span> <span data-ttu-id="cddef-154">상대 경로도 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-154">Relative paths are supported too.</span></span> <span data-ttu-id="cddef-155">예를 들어 `--files dist/browser/signalr.js`을 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="cddef-155">For example: `--files dist/browser/signalr.js`.</span></span>

* `-p|--provider <PROVIDER>`

  <span data-ttu-id="cddef-156">라이브러리 획득에 사용할 공급자의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-156">The name of the provider to use for the library acquisition.</span></span> <span data-ttu-id="cddef-157">`<PROVIDER>`을 다음 값 중 하나로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-157">Replace `<PROVIDER>` with one of the following values:</span></span>
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  <span data-ttu-id="cddef-158">지정하지 않으면 *libman.json*의 `defaultProvider` 속성이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-158">If not specified, the `defaultProvider` property in *libman.json* is used.</span></span> <span data-ttu-id="cddef-159">*libman.json*에 `defaultProvider` 속성이 지정되지 않은 경우 이 옵션은 필수입니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-159">If no `defaultProvider` property is specified in *libman.json*, this option is required.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="cddef-160">예</span><span class="sxs-lookup"><span data-stu-id="cddef-160">Examples</span></span>

<span data-ttu-id="cddef-161">다음 *libman.json* 파일을 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="cddef-161">Consider the following *libman.json* file:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

<span data-ttu-id="cddef-162">CDNJS 공급자를 사용하여 jQuery 버전 3.2.1 *jquery.min.js* 파일을 *wwwroot/scripts/jquery* 폴더에 설치하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-162">To install the jQuery version 3.2.1 *jquery.min.js* file to the *wwwroot/scripts/jquery* folder using the CDNJS provider:</span></span>

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

<span data-ttu-id="cddef-163">*libman.json* 파일은 다음과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-163">The *libman.json* file resembles the following:</span></span>

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

<span data-ttu-id="cddef-164">파일 시스템 공급자를 사용하여 *C:\\temp\\contosoCalendar\\* 에서 *calendar.js* 및 *calendar.css* 파일을 설치하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-164">To install the *calendar.js* and *calendar.css* files from *C:\\temp\\contosoCalendar\\* using the file system provider:</span></span>

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

<span data-ttu-id="cddef-165">다음과 같은 두 가지 이유로 다음 프롬프트가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-165">The following prompt appears for two reasons:</span></span>

* <span data-ttu-id="cddef-166">*libman.json* 파일에는 `defaultDestination` 속성이 포함되어 있지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-166">The *libman.json* file doesn't contain a `defaultDestination` property.</span></span>
* <span data-ttu-id="cddef-167">`libman install` 명령에는 `-d|--destination` 옵션이 포함되어 있지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-167">The `libman install` command doesn't contain the `-d|--destination` option.</span></span>

![libman install 명령 - 대상](_static/libman-install-destination.png)

<span data-ttu-id="cddef-169">기본 대상을 승인한 후에 *libman.json* 파일은 다음과 유사합니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-169">After accepting the default destination, the *libman.json* file resembles the following:</span></span>

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

## <a name="restore-library-files"></a><span data-ttu-id="cddef-170">라이브러리 파일 복원</span><span class="sxs-lookup"><span data-stu-id="cddef-170">Restore library files</span></span>

<span data-ttu-id="cddef-171">`libman restore` 명령은 *libman.json*에 정의된 라이브러리 파일을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-171">The `libman restore` command installs library files defined in *libman.json*.</span></span> <span data-ttu-id="cddef-172">이 때 적용되는 규칙은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-172">The following rules apply:</span></span>

* <span data-ttu-id="cddef-173">프로젝트 루트에 *libman.json* 파일이 없으면 오류가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-173">If no *libman.json* file exists in the project root, an error is returned.</span></span>
* <span data-ttu-id="cddef-174">라이브러리가 공급자를 지정하는 경우 *libman.json*의 `defaultProvider` 속성이 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-174">If a library specifies a provider, the `defaultProvider` property in *libman.json* is ignored.</span></span>
* <span data-ttu-id="cddef-175">라이브러리가 대상을 지정하는 경우 *libman.json*의 `defaultDestination` 속성이 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-175">If a library specifies a destination, the `defaultDestination` property in *libman.json* is ignored.</span></span>

### <a name="synopsis"></a><span data-ttu-id="cddef-176">개요</span><span class="sxs-lookup"><span data-stu-id="cddef-176">Synopsis</span></span>

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a><span data-ttu-id="cddef-177">옵션</span><span class="sxs-lookup"><span data-stu-id="cddef-177">Options</span></span>

<span data-ttu-id="cddef-178">다음 옵션은 `libman restore` 명령에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-178">The following options are available for the `libman restore` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="cddef-179">예</span><span class="sxs-lookup"><span data-stu-id="cddef-179">Examples</span></span>

<span data-ttu-id="cddef-180">*libman.json* 파일에 정의된 라이브러리 파일을 복원하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-180">To restore the library files defined in *libman.json*:</span></span>

```console
libman restore
```

## <a name="delete-library-files"></a><span data-ttu-id="cddef-181">라이브러리 폴더 삭제</span><span class="sxs-lookup"><span data-stu-id="cddef-181">Delete library files</span></span>

<span data-ttu-id="cddef-182">`libman clean` 명령은 LibMan을 통해 이전에 복원한 라이브러리 파일을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-182">The `libman clean` command deletes library files previously restored via LibMan.</span></span> <span data-ttu-id="cddef-183">이 작업 후 삭제된 후에 비어 있게 되는 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-183">Folders that become empty after this operation are deleted.</span></span> <span data-ttu-id="cddef-184">*libman.json*의 `libraries` 속성에 있는 라이브러리 파일의 연결된 구성은 제거되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-184">The library files' associated configurations in the `libraries` property of *libman.json* aren't removed.</span></span>

### <a name="synopsis"></a><span data-ttu-id="cddef-185">개요</span><span class="sxs-lookup"><span data-stu-id="cddef-185">Synopsis</span></span>

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a><span data-ttu-id="cddef-186">옵션</span><span class="sxs-lookup"><span data-stu-id="cddef-186">Options</span></span>

<span data-ttu-id="cddef-187">다음 옵션은 `libman clean` 명령에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-187">The following options are available for the `libman clean` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="cddef-188">예</span><span class="sxs-lookup"><span data-stu-id="cddef-188">Examples</span></span>

<span data-ttu-id="cddef-189">LibMan을 통해 설치된 라이브러리 파일을 삭제하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-189">To delete library files installed via LibMan:</span></span>

```console
libman clean
```

## <a name="uninstall-library-files"></a><span data-ttu-id="cddef-190">라이브러리 파일 제거</span><span class="sxs-lookup"><span data-stu-id="cddef-190">Uninstall library files</span></span>

<span data-ttu-id="cddef-191">`libman uninstall` 명령은 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-191">The `libman uninstall` command:</span></span>

* <span data-ttu-id="cddef-192">지정된 라이브러리와 연결된 모든 파일을 *libman.json*의 대상에서 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-192">Deletes all files associated with the specified library from the destination in *libman.json*.</span></span>
* <span data-ttu-id="cddef-193">연결된 라이브러리 구성을 *libman.json*에서 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-193">Removes the associated library configuration from *libman.json*.</span></span>

<span data-ttu-id="cddef-194">다음 경우에 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-194">An error occurs when:</span></span>

* <span data-ttu-id="cddef-195">프로젝트 루트에는 *libman.json* 파일이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-195">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="cddef-196">지정된 라이브러리가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-196">The specified library doesn't exist.</span></span>

<span data-ttu-id="cddef-197">이름이 같은 라이브러리가 2개 이상 설치되어 있으면 하나를 선택하라는 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-197">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="cddef-198">개요</span><span class="sxs-lookup"><span data-stu-id="cddef-198">Synopsis</span></span>

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="cddef-199">인수</span><span class="sxs-lookup"><span data-stu-id="cddef-199">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="cddef-200">제거할 라이브러리의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-200">The name of the library to uninstall.</span></span> <span data-ttu-id="cddef-201">이 이름에는 버전 번호 표기법(예: `@1.2.0`)이 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-201">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="cddef-202">옵션</span><span class="sxs-lookup"><span data-stu-id="cddef-202">Options</span></span>

<span data-ttu-id="cddef-203">다음 옵션은 `libman uninstall` 명령에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-203">The following options are available for the `libman uninstall` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="cddef-204">예</span><span class="sxs-lookup"><span data-stu-id="cddef-204">Examples</span></span>

<span data-ttu-id="cddef-205">다음 *libman.json* 파일을 고려하세요.</span><span class="sxs-lookup"><span data-stu-id="cddef-205">Consider the following *libman.json* file:</span></span>

[!code-json[](samples/LibManSample/libman.json)]

* <span data-ttu-id="cddef-206">jQuery를 제거하려면 다음 명령 중 하나를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-206">To uninstall jQuery, either of the following commands succeed:</span></span>

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* <span data-ttu-id="cddef-207">`filesystem` 공급자를 통해 설치된 Lodash 파일을 제거하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-207">To uninstall the Lodash files installed via the `filesystem` provider:</span></span>

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a><span data-ttu-id="cddef-208">라이브러리 버전 업데이트</span><span class="sxs-lookup"><span data-stu-id="cddef-208">Update library version</span></span>

<span data-ttu-id="cddef-209">`libman update` 명령은 LibMan을 통해 설치된 라이브러리를 지정된 버전으로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-209">The `libman update` command updates a library installed via LibMan to the specified version.</span></span>

<span data-ttu-id="cddef-210">다음 경우에 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-210">An error occurs when:</span></span>

* <span data-ttu-id="cddef-211">프로젝트 루트에는 *libman.json* 파일이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-211">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="cddef-212">지정된 라이브러리가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-212">The specified library doesn't exist.</span></span>

<span data-ttu-id="cddef-213">이름이 같은 라이브러리가 2개 이상 설치되어 있으면 하나를 선택하라는 메시지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-213">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="cddef-214">개요</span><span class="sxs-lookup"><span data-stu-id="cddef-214">Synopsis</span></span>

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="cddef-215">인수</span><span class="sxs-lookup"><span data-stu-id="cddef-215">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="cddef-216">업데이트할 라이브러리의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-216">The name of the library to update.</span></span>

### <a name="options"></a><span data-ttu-id="cddef-217">옵션</span><span class="sxs-lookup"><span data-stu-id="cddef-217">Options</span></span>

<span data-ttu-id="cddef-218">다음 옵션은 `libman update` 명령에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-218">The following options are available for the `libman update` command:</span></span>

* `-pre`

  <span data-ttu-id="cddef-219">라이브러리의 최신 시험판 버전을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-219">Obtain the latest prerelease version of the library.</span></span>

* `--to <VERSION>`

  <span data-ttu-id="cddef-220">라이브러리의 특정 버전을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-220">Obtain a specific version of the library.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="cddef-221">예</span><span class="sxs-lookup"><span data-stu-id="cddef-221">Examples</span></span>

* <span data-ttu-id="cddef-222">jQuery를 최신 버전으로 업데이트하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-222">To update jQuery to the latest version:</span></span>

  ```console
  libman update jquery
  ```

* <span data-ttu-id="cddef-223">jQuery를 버전 3.3.1로 업데이트하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-223">To update jQuery to version 3.3.1:</span></span>

  ```console
  libman update jquery --to 3.3.1
  ```

* <span data-ttu-id="cddef-224">jQuery를 최신 시험판 버전으로 업데이트하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-224">To update jQuery to the latest prerelease version:</span></span>

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a><span data-ttu-id="cddef-225">라이브러리 캐시 관리</span><span class="sxs-lookup"><span data-stu-id="cddef-225">Manage library cache</span></span>

<span data-ttu-id="cddef-226">`libman cache` 명령은 LibMan 라이브러리 캐시를 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-226">The `libman cache` command manages the LibMan library cache.</span></span> <span data-ttu-id="cddef-227">`filesystem` 공급자는 라이브러리 캐시를 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-227">The `filesystem` provider doesn't use the library cache.</span></span>

### <a name="synopsis"></a><span data-ttu-id="cddef-228">개요</span><span class="sxs-lookup"><span data-stu-id="cddef-228">Synopsis</span></span>

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="cddef-229">인수</span><span class="sxs-lookup"><span data-stu-id="cddef-229">Arguments</span></span>

`PROVIDER`

<span data-ttu-id="cddef-230">`clean` 명령에서만 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-230">Only used with the `clean` command.</span></span> <span data-ttu-id="cddef-231">정리할 공급자 캐시를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-231">Specifies the provider cache to clean.</span></span> <span data-ttu-id="cddef-232">유효한 값은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-232">Valid values include:</span></span>

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a><span data-ttu-id="cddef-233">옵션</span><span class="sxs-lookup"><span data-stu-id="cddef-233">Options</span></span>

<span data-ttu-id="cddef-234">다음 옵션은 `libman cache` 명령에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-234">The following options are available for the `libman cache` command:</span></span>

* `--files`

  <span data-ttu-id="cddef-235">캐시된 파일의 이름을 나열합니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-235">List the names of files that are cached.</span></span>

* `--libraries`

  <span data-ttu-id="cddef-236">캐시된 라이브러리의 이름을 나열합니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-236">List the names of libraries that are cached.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="cddef-237">예</span><span class="sxs-lookup"><span data-stu-id="cddef-237">Examples</span></span>

* <span data-ttu-id="cddef-238">공급자당 캐시된 라이브러리의 이름을 보려면 다음 명령 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-238">To view the names of cached libraries per provider, use one of the following commands:</span></span>

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  <span data-ttu-id="cddef-239">다음과 비슷한 출력이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-239">Output similar to the following is displayed:</span></span>

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

* <span data-ttu-id="cddef-240">공급자당 캐시된 라이브러리 파일의 이름을 보려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-240">To view the names of cached library files per provider:</span></span>

  ```console
  libman cache list --files
  ```

  <span data-ttu-id="cddef-241">다음과 비슷한 출력이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-241">Output similar to the following is displayed:</span></span>

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

  <span data-ttu-id="cddef-242">위의 출력은 jQuery 버전 3.2.1 및 3.3.1이 CDNJS 공급자 아래에 캐시됨을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-242">Notice the preceding output shows that jQuery versions 3.2.1 and 3.3.1 are cached under the CDNJS provider.</span></span>

* <span data-ttu-id="cddef-243">CDNJS 공급자에 대한 라이브러리 캐시를 비우려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-243">To empty the library cache for the CDNJS provider:</span></span>

  ```console
  libman cache clean cdnjs
  ```

  <span data-ttu-id="cddef-244">CDNJS 공급자 캐시를 비우면 `libman cache list` 명령은 다음을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-244">After emptying the CDNJS provider cache, the `libman cache list` command displays the following:</span></span>

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

* <span data-ttu-id="cddef-245">지원되는 모든 공급자에 대한 캐시를 비우려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-245">To empty the cache for all supported providers:</span></span>

  ```console
  libman cache clean
  ```

  <span data-ttu-id="cddef-246">모든 공급자 캐시를 비우면 `libman cache list` 명령은 다음을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="cddef-246">After emptying all provider caches, the `libman cache list` command displays the following:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a><span data-ttu-id="cddef-247">추가 자료</span><span class="sxs-lookup"><span data-stu-id="cddef-247">Additional resources</span></span>

* [<span data-ttu-id="cddef-248">전역 도구 설치</span><span class="sxs-lookup"><span data-stu-id="cddef-248">Install a Global Tool</span></span>](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [<span data-ttu-id="cddef-249">LibMan GitHub 리포지토리</span><span class="sxs-lookup"><span data-stu-id="cddef-249">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
