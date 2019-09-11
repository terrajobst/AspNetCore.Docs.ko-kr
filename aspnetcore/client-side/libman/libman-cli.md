---
title: LibMan 명령줄 인터페이스 (CLI)를 사용 하 여 ASP.NET Core를 사용 하 여
author: scottaddie
description: ASP.NET Core 프로젝트에서 LibMan 명령줄 인터페이스 (CLI)를 사용 하는 방법에 알아봅니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/30/2018
uid: client-side/libman/libman-cli
ms.openlocfilehash: 5667f79648a60b8fd9496f8041ef08891ab766af
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64894760"
---
# <a name="use-the-libman-command-line-interface-cli-with-aspnet-core"></a><span data-ttu-id="6e803-103">LibMan 명령줄 인터페이스 (CLI)를 사용 하 여 ASP.NET Core를 사용 하 여</span><span class="sxs-lookup"><span data-stu-id="6e803-103">Use the LibMan command-line interface (CLI) with ASP.NET Core</span></span>

<span data-ttu-id="6e803-104">작성자: [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="6e803-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="6e803-105">합니다 [LibMan](xref:client-side/libman/index) CLI는 모든.NET Core는 지원 범위에서 지원 되는 플랫폼 간 도구입니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-105">The [LibMan](xref:client-side/libman/index) CLI is a cross-platform tool that's supported everywhere .NET Core is supported.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6e803-106">전제 조건</span><span class="sxs-lookup"><span data-stu-id="6e803-106">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="6e803-107">설치</span><span class="sxs-lookup"><span data-stu-id="6e803-107">Installation</span></span>

<span data-ttu-id="6e803-108">LibMan CLI를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-108">To install the LibMan CLI:</span></span>

```console
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

<span data-ttu-id="6e803-109">A [.NET Core에 대 한 전역 도구](/dotnet/core/tools/global-tools#install-a-global-tool) 에서 설치 되는 [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet 패키지.</span><span class="sxs-lookup"><span data-stu-id="6e803-109">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet package.</span></span>

<span data-ttu-id="6e803-110">에 특정 NuGet 패키지 소스에서 LibMan CLI를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-110">To install the LibMan CLI from a specific NuGet package source:</span></span>

```console
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

<span data-ttu-id="6e803-111">앞의 예제에서 로컬 Windows 컴퓨터에서.NET Core 전역 도구 설치 *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-111">In the preceding example, a .NET Core Global Tool is installed from the local Windows machine's *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* file.</span></span>

## <a name="usage"></a><span data-ttu-id="6e803-112">사용</span><span class="sxs-lookup"><span data-stu-id="6e803-112">Usage</span></span>

<span data-ttu-id="6e803-113">CLI의 설치 후 다음 명령을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-113">After successful installation of the CLI, the following command can be used:</span></span>

```console
libman
```

<span data-ttu-id="6e803-114">설치 된 CLI 버전을 보려면:</span><span class="sxs-lookup"><span data-stu-id="6e803-114">To view the installed CLI version:</span></span>

```console
libman --version
```

<span data-ttu-id="6e803-115">사용 가능한 CLI 명령을 보려면</span><span class="sxs-lookup"><span data-stu-id="6e803-115">To view the available CLI commands:</span></span>

```console
libman --help
```

<span data-ttu-id="6e803-116">이전 명령에는 다음과 유사한 출력이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-116">The preceding command displays output similar to the following:</span></span>

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

<span data-ttu-id="6e803-117">다음 섹션에서는 사용 가능한 CLI 명령을 간략하게 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-117">The following sections outline the available CLI commands.</span></span>

## <a name="initialize-libman-in-the-project"></a><span data-ttu-id="6e803-118">LibMan 프로젝트 초기화</span><span class="sxs-lookup"><span data-stu-id="6e803-118">Initialize LibMan in the project</span></span>

<span data-ttu-id="6e803-119">합니다 `libman init` 명령은 만듭니다는 *libman.json* 없는 경우 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-119">The `libman init` command creates a *libman.json* file if one doesn't exist.</span></span> <span data-ttu-id="6e803-120">파일의 기본 항목 템플릿 콘텐츠를 사용 하 여 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-120">The file is created with the default item template content.</span></span>

### <a name="synopsis"></a><span data-ttu-id="6e803-121">개요</span><span class="sxs-lookup"><span data-stu-id="6e803-121">Synopsis</span></span>

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a><span data-ttu-id="6e803-122">옵션</span><span class="sxs-lookup"><span data-stu-id="6e803-122">Options</span></span>

<span data-ttu-id="6e803-123">다음 옵션을 사용할 수는 `libman init` 명령:</span><span class="sxs-lookup"><span data-stu-id="6e803-123">The following options are available for the `libman init` command:</span></span>

* `-d|--default-destination <PATH>`

  <span data-ttu-id="6e803-124">현재 폴더의 상대 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-124">A path relative to the current folder.</span></span> <span data-ttu-id="6e803-125">라이브러리 파일은이 위치에 설치 되지 않은 경우 `destination` 속성에서 라이브러리에 대해 정의 됩니다 *libman.json*합니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-125">Library files are installed in this location if no `destination` property is defined for a library in *libman.json*.</span></span> <span data-ttu-id="6e803-126">`<PATH>` 값에 기록 됩니다 합니다 `defaultDestination` 속성을 *libman.json*합니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-126">The `<PATH>` value is written to the `defaultDestination` property of *libman.json*.</span></span>

* `-p|--default-provider <PROVIDER>`

  <span data-ttu-id="6e803-127">공급자가 없습니다 지정 된 라이브러리에 대해 정의 된 경우 사용 하는 공급자입니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-127">The provider to use if no provider is defined for a given library.</span></span> <span data-ttu-id="6e803-128">`<PROVIDER>` 값에 기록 됩니다 합니다 `defaultProvider` 속성을 *libman.json*합니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-128">The `<PROVIDER>` value is written to the `defaultProvider` property of *libman.json*.</span></span> <span data-ttu-id="6e803-129">대체 `<PROVIDER>` 다음 값 중 하나를 사용 하 여:</span><span class="sxs-lookup"><span data-stu-id="6e803-129">Replace `<PROVIDER>` with one of the following values:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="6e803-130">예제</span><span class="sxs-lookup"><span data-stu-id="6e803-130">Examples</span></span>

<span data-ttu-id="6e803-131">만들려는 *libman.json* ASP.NET Core 프로젝트에서 파일:</span><span class="sxs-lookup"><span data-stu-id="6e803-131">To create a *libman.json* file in an ASP.NET Core project:</span></span>

* <span data-ttu-id="6e803-132">프로젝트 루트에 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-132">Navigate to the project root.</span></span>
* <span data-ttu-id="6e803-133">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-133">Run the following command:</span></span>

  ```console
  libman init
  ```

* <span data-ttu-id="6e803-134">키를 눌러 기본 공급자의 이름을 입력 `Enter` 기본 CDNJS 공급자를 사용 하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-134">Type the name of the default provider, or press `Enter` to use the default CDNJS provider.</span></span> <span data-ttu-id="6e803-135">유효한 값은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-135">Valid values include:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![libman init 명령-기본 공급자](_static/libman-init-provider.png)

<span data-ttu-id="6e803-137">A *libman.json* 파일이 다음과 같은 내용으로 프로젝트 루트에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-137">A *libman.json* file is added to the project root with the following content:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a><span data-ttu-id="6e803-138">라이브러리 파일 추가하기</span><span class="sxs-lookup"><span data-stu-id="6e803-138">Add library files</span></span>

<span data-ttu-id="6e803-139">`libman install` 명령을 다운로드 하 고 프로젝트에 라이브러리 파일을 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-139">The `libman install` command downloads and installs library files into the project.</span></span> <span data-ttu-id="6e803-140">A *libman.json* 파일이 없는 경우 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-140">A *libman.json* file is added if one doesn't exist.</span></span> <span data-ttu-id="6e803-141">합니다 *libman.json* 라이브러리 파일에 대 한 구성 세부 정보를 저장할 파일을 수정 합니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-141">The *libman.json* file is modified to store configuration details for the library files.</span></span>

### <a name="synopsis"></a><span data-ttu-id="6e803-142">개요</span><span class="sxs-lookup"><span data-stu-id="6e803-142">Synopsis</span></span>

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="6e803-143">인수</span><span class="sxs-lookup"><span data-stu-id="6e803-143">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="6e803-144">설치 하려면 라이브러리의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-144">The name of the library to install.</span></span> <span data-ttu-id="6e803-145">이 이름은 버전 번호 표기법 포함 될 수 있습니다 (예를 들어 `@1.2.0`).</span><span class="sxs-lookup"><span data-stu-id="6e803-145">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="6e803-146">옵션</span><span class="sxs-lookup"><span data-stu-id="6e803-146">Options</span></span>

<span data-ttu-id="6e803-147">다음 옵션을 사용할 수는 `libman install` 명령:</span><span class="sxs-lookup"><span data-stu-id="6e803-147">The following options are available for the `libman install` command:</span></span>

* `-d|--destination <PATH>`

  <span data-ttu-id="6e803-148">라이브러리를 설치할 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-148">The location to install the library.</span></span> <span data-ttu-id="6e803-149">지정 하지 않으면 기본 위치에 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-149">If not specified, the default location is used.</span></span> <span data-ttu-id="6e803-150">없으면 `defaultDestination` 속성에 지정 된 *libman.json*,이 옵션을 지정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-150">If no `defaultDestination` property is specified in *libman.json*, this option is required.</span></span>

* `--files <FILE>`

  <span data-ttu-id="6e803-151">라이브러리에서 설치 파일의 이름을 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-151">Specify the name of the file to install from the library.</span></span> <span data-ttu-id="6e803-152">지정 하지 않으면 라이브러리에서 모든 파일이 설치 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-152">If not specified, all files from the library are installed.</span></span> <span data-ttu-id="6e803-153">제공 `--files` 파일당 옵션이 설치 되어 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-153">Provide one `--files` option per file to be installed.</span></span> <span data-ttu-id="6e803-154">상대 경로 너무 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-154">Relative paths are supported too.</span></span> <span data-ttu-id="6e803-155">예: `--files dist/browser/signalr.js`</span><span class="sxs-lookup"><span data-stu-id="6e803-155">For example: `--files dist/browser/signalr.js`.</span></span>

* `-p|--provider <PROVIDER>`

  <span data-ttu-id="6e803-156">라이브러리 획득을 위해 사용 하 여 공급자의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-156">The name of the provider to use for the library acquisition.</span></span> <span data-ttu-id="6e803-157">대체 `<PROVIDER>` 다음 값 중 하나를 사용 하 여:</span><span class="sxs-lookup"><span data-stu-id="6e803-157">Replace `<PROVIDER>` with one of the following values:</span></span>
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  <span data-ttu-id="6e803-158">지정 하지 않으면 합니다 `defaultProvider` 속성에서 *libman.json* 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-158">If not specified, the `defaultProvider` property in *libman.json* is used.</span></span> <span data-ttu-id="6e803-159">없으면 `defaultProvider` 속성에 지정 된 *libman.json*,이 옵션을 지정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-159">If no `defaultProvider` property is specified in *libman.json*, this option is required.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="6e803-160">예제</span><span class="sxs-lookup"><span data-stu-id="6e803-160">Examples</span></span>

<span data-ttu-id="6e803-161">다음을 고려해 야 *libman.json* 파일:</span><span class="sxs-lookup"><span data-stu-id="6e803-161">Consider the following *libman.json* file:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

<span data-ttu-id="6e803-162">3\.2.1 jQuery 버전을 설치 하려면 *jquery.min.js* 파일을 합니다 *jquery스크립트/wwwroot/* CDNJS 공급자를 사용 하 여 폴더:</span><span class="sxs-lookup"><span data-stu-id="6e803-162">To install the jQuery version 3.2.1 *jquery.min.js* file to the *wwwroot/scripts/jquery* folder using the CDNJS provider:</span></span>

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

<span data-ttu-id="6e803-163">합니다 *libman.json* 파일에는 다음 예제와 유사 합니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-163">The *libman.json* file resembles the following:</span></span>

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

<span data-ttu-id="6e803-164">설치 하는 *calendar.js* 하 고 *calendar.css* 에서 파일을 *c:\\temp\\contosoCalendar\\*  파일 시스템을 사용 하 여 공급자:</span><span class="sxs-lookup"><span data-stu-id="6e803-164">To install the *calendar.js* and *calendar.css* files from *C:\\temp\\contosoCalendar\\* using the file system provider:</span></span>

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

<span data-ttu-id="6e803-165">두 가지 이유로 다음과 같은 메시지가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-165">The following prompt appears for two reasons:</span></span>

* <span data-ttu-id="6e803-166">합니다 *libman.json* 파일에 포함 하지 않습니다는 `defaultDestination` 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-166">The *libman.json* file doesn't contain a `defaultDestination` property.</span></span>
* <span data-ttu-id="6e803-167">합니다 `libman install` 명령을 포함 하지 않습니다는 `-d|--destination` 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-167">The `libman install` command doesn't contain the `-d|--destination` option.</span></span>

![libman 명령-설치 대상](_static/libman-install-destination.png)

<span data-ttu-id="6e803-169">기본 대상에 동의한 후 합니다 *libman.json* 파일에는 다음 예제와 유사 합니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-169">After accepting the default destination, the *libman.json* file resembles the following:</span></span>

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

## <a name="restore-library-files"></a><span data-ttu-id="6e803-170">라이브러리 파일 복원하기</span><span class="sxs-lookup"><span data-stu-id="6e803-170">Restore library files</span></span>

<span data-ttu-id="6e803-171">합니다 `libman restore` 명령에 정의 된 라이브러리 파일 설치 *libman.json*합니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-171">The `libman restore` command installs library files defined in *libman.json*.</span></span> <span data-ttu-id="6e803-172">이 때 적용되는 규칙은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-172">The following rules apply:</span></span>

* <span data-ttu-id="6e803-173">없으면 *libman.json* 파일이 있는 프로젝트 루트에서 오류가 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-173">If no *libman.json* file exists in the project root, an error is returned.</span></span>
* <span data-ttu-id="6e803-174">라이브러리에는 공급자를 지정 하는 경우는 `defaultProvider` 속성에서 *libman.json* 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-174">If a library specifies a provider, the `defaultProvider` property in *libman.json* is ignored.</span></span>
* <span data-ttu-id="6e803-175">라이브러리를 대상으로 지정 하는 경우는 `defaultDestination` 속성에서 *libman.json* 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-175">If a library specifies a destination, the `defaultDestination` property in *libman.json* is ignored.</span></span>

### <a name="synopsis"></a><span data-ttu-id="6e803-176">개요</span><span class="sxs-lookup"><span data-stu-id="6e803-176">Synopsis</span></span>

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a><span data-ttu-id="6e803-177">옵션</span><span class="sxs-lookup"><span data-stu-id="6e803-177">Options</span></span>

<span data-ttu-id="6e803-178">다음 옵션을 사용할 수는 `libman restore` 명령:</span><span class="sxs-lookup"><span data-stu-id="6e803-178">The following options are available for the `libman restore` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="6e803-179">예제</span><span class="sxs-lookup"><span data-stu-id="6e803-179">Examples</span></span>

<span data-ttu-id="6e803-180">에 정의 된 라이브러리 파일을 복원할 *libman.json*:</span><span class="sxs-lookup"><span data-stu-id="6e803-180">To restore the library files defined in *libman.json*:</span></span>

```console
libman restore
```

## <a name="delete-library-files"></a><span data-ttu-id="6e803-181">라이브러리 파일 삭제하기</span><span class="sxs-lookup"><span data-stu-id="6e803-181">Delete library files</span></span>

<span data-ttu-id="6e803-182">`libman clean` 명령은 LibMan 통해 이전에 복원 하는 라이브러리 파일을 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-182">The `libman clean` command deletes library files previously restored via LibMan.</span></span> <span data-ttu-id="6e803-183">이 작업 후 빈 할 폴더 삭제 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-183">Folders that become empty after this operation are deleted.</span></span> <span data-ttu-id="6e803-184">라이브러리 파일의 구성에 연결 합니다 `libraries` 속성을 *libman.json* 제거 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-184">The library files' associated configurations in the `libraries` property of *libman.json* aren't removed.</span></span>

### <a name="synopsis"></a><span data-ttu-id="6e803-185">개요</span><span class="sxs-lookup"><span data-stu-id="6e803-185">Synopsis</span></span>

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a><span data-ttu-id="6e803-186">옵션</span><span class="sxs-lookup"><span data-stu-id="6e803-186">Options</span></span>

<span data-ttu-id="6e803-187">다음 옵션을 사용할 수는 `libman clean` 명령:</span><span class="sxs-lookup"><span data-stu-id="6e803-187">The following options are available for the `libman clean` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="6e803-188">예제</span><span class="sxs-lookup"><span data-stu-id="6e803-188">Examples</span></span>

<span data-ttu-id="6e803-189">LibMan를 통해 설치 된 라이브러리 파일을 삭제:</span><span class="sxs-lookup"><span data-stu-id="6e803-189">To delete library files installed via LibMan:</span></span>

```console
libman clean
```

## <a name="uninstall-library-files"></a><span data-ttu-id="6e803-190">라이브러리 파일 제거하기</span><span class="sxs-lookup"><span data-stu-id="6e803-190">Uninstall library files</span></span>

<span data-ttu-id="6e803-191">`libman uninstall` 명령:</span><span class="sxs-lookup"><span data-stu-id="6e803-191">The `libman uninstall` command:</span></span>

* <span data-ttu-id="6e803-192">대상에서 지정 된 라이브러리와 연결 된 모든 파일을 삭제 *libman.json*합니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-192">Deletes all files associated with the specified library from the destination in *libman.json*.</span></span>
* <span data-ttu-id="6e803-193">연결 된 라이브러리 구성을 제거 *libman.json*합니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-193">Removes the associated library configuration from *libman.json*.</span></span>

<span data-ttu-id="6e803-194">오류가 발생 하는 경우:</span><span class="sxs-lookup"><span data-stu-id="6e803-194">An error occurs when:</span></span>

* <span data-ttu-id="6e803-195">아니오 *libman.json* 파일이 프로젝트 루트에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-195">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="6e803-196">지정된 된 라이브러리 존재 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-196">The specified library doesn't exist.</span></span>

<span data-ttu-id="6e803-197">동일한 이름 가진 둘 이상의 라이브러리가 설치 되므로 하나를 선택 하려면 묻는 메시지가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-197">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="6e803-198">개요</span><span class="sxs-lookup"><span data-stu-id="6e803-198">Synopsis</span></span>

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="6e803-199">인수</span><span class="sxs-lookup"><span data-stu-id="6e803-199">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="6e803-200">제거할 라이브러리의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-200">The name of the library to uninstall.</span></span> <span data-ttu-id="6e803-201">이 이름은 버전 번호 표기법 포함 될 수 있습니다 (예를 들어 `@1.2.0`).</span><span class="sxs-lookup"><span data-stu-id="6e803-201">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="6e803-202">옵션</span><span class="sxs-lookup"><span data-stu-id="6e803-202">Options</span></span>

<span data-ttu-id="6e803-203">다음 옵션을 사용할 수는 `libman uninstall` 명령:</span><span class="sxs-lookup"><span data-stu-id="6e803-203">The following options are available for the `libman uninstall` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="6e803-204">예제</span><span class="sxs-lookup"><span data-stu-id="6e803-204">Examples</span></span>

<span data-ttu-id="6e803-205">다음을 고려해 야 *libman.json* 파일:</span><span class="sxs-lookup"><span data-stu-id="6e803-205">Consider the following *libman.json* file:</span></span>

[!code-json[](samples/LibManSample/libman.json)]

* <span data-ttu-id="6e803-206">JQuery를 제거 하려면 다음 명령 중 하나 성공 합니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-206">To uninstall jQuery, either of the following commands succeed:</span></span>

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* <span data-ttu-id="6e803-207">통해 설치 및 Lodash 파일을 제거 하는 `filesystem` 공급자:</span><span class="sxs-lookup"><span data-stu-id="6e803-207">To uninstall the Lodash files installed via the `filesystem` provider:</span></span>

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a><span data-ttu-id="6e803-208">라이브러리 버전 업데이트</span><span class="sxs-lookup"><span data-stu-id="6e803-208">Update library version</span></span>

<span data-ttu-id="6e803-209">`libman update` 명령은 LibMan를 통해 지정된 된 버전에 설치 된 라이브러리를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-209">The `libman update` command updates a library installed via LibMan to the specified version.</span></span>

<span data-ttu-id="6e803-210">오류가 발생 하는 경우:</span><span class="sxs-lookup"><span data-stu-id="6e803-210">An error occurs when:</span></span>

* <span data-ttu-id="6e803-211">아니오 *libman.json* 파일이 프로젝트 루트에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-211">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="6e803-212">지정된 된 라이브러리 존재 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-212">The specified library doesn't exist.</span></span>

<span data-ttu-id="6e803-213">동일한 이름 가진 둘 이상의 라이브러리가 설치 되므로 하나를 선택 하려면 묻는 메시지가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-213">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="6e803-214">개요</span><span class="sxs-lookup"><span data-stu-id="6e803-214">Synopsis</span></span>

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="6e803-215">인수</span><span class="sxs-lookup"><span data-stu-id="6e803-215">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="6e803-216">업데이트 하려면 라이브러리의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-216">The name of the library to update.</span></span>

### <a name="options"></a><span data-ttu-id="6e803-217">옵션</span><span class="sxs-lookup"><span data-stu-id="6e803-217">Options</span></span>

<span data-ttu-id="6e803-218">다음 옵션을 사용할 수는 `libman update` 명령:</span><span class="sxs-lookup"><span data-stu-id="6e803-218">The following options are available for the `libman update` command:</span></span>

* `-pre`

  <span data-ttu-id="6e803-219">라이브러리의 최신 시험판 버전을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-219">Obtain the latest prerelease version of the library.</span></span>

* `--to <VERSION>`

  <span data-ttu-id="6e803-220">라이브러리의 특정 버전을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-220">Obtain a specific version of the library.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="6e803-221">예제</span><span class="sxs-lookup"><span data-stu-id="6e803-221">Examples</span></span>

* <span data-ttu-id="6e803-222">JQuery를 최신 버전으로 업데이트:</span><span class="sxs-lookup"><span data-stu-id="6e803-222">To update jQuery to the latest version:</span></span>

  ```console
  libman update jquery
  ```

* <span data-ttu-id="6e803-223">JQuery 버전 3.3.1 업데이트:</span><span class="sxs-lookup"><span data-stu-id="6e803-223">To update jQuery to version 3.3.1:</span></span>

  ```console
  libman update jquery --to 3.3.1
  ```

* <span data-ttu-id="6e803-224">JQuery를 최신 시험판 버전으로 업데이트:</span><span class="sxs-lookup"><span data-stu-id="6e803-224">To update jQuery to the latest prerelease version:</span></span>

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a><span data-ttu-id="6e803-225">라이브러리 캐시 관리</span><span class="sxs-lookup"><span data-stu-id="6e803-225">Manage library cache</span></span>

<span data-ttu-id="6e803-226">`libman cache` 명령 LibMan 라이브러리 캐시를 관리 합니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-226">The `libman cache` command manages the LibMan library cache.</span></span> <span data-ttu-id="6e803-227">`filesystem` 공급자 라이브러리 캐시를 사용 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-227">The `filesystem` provider doesn't use the library cache.</span></span>

### <a name="synopsis"></a><span data-ttu-id="6e803-228">개요</span><span class="sxs-lookup"><span data-stu-id="6e803-228">Synopsis</span></span>

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="6e803-229">인수</span><span class="sxs-lookup"><span data-stu-id="6e803-229">Arguments</span></span>

`PROVIDER`

<span data-ttu-id="6e803-230">에서만 사용할 수는 `clean` 명령입니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-230">Only used with the `clean` command.</span></span> <span data-ttu-id="6e803-231">공급자 캐시 정리를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-231">Specifies the provider cache to clean.</span></span> <span data-ttu-id="6e803-232">유효한 값은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-232">Valid values include:</span></span>

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a><span data-ttu-id="6e803-233">옵션</span><span class="sxs-lookup"><span data-stu-id="6e803-233">Options</span></span>

<span data-ttu-id="6e803-234">다음 옵션을 사용할 수는 `libman cache` 명령:</span><span class="sxs-lookup"><span data-stu-id="6e803-234">The following options are available for the `libman cache` command:</span></span>

* `--files`

  <span data-ttu-id="6e803-235">캐시 된 파일의 이름을 나열 합니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-235">List the names of files that are cached.</span></span>

* `--libraries`

  <span data-ttu-id="6e803-236">캐시 되는 라이브러리의 이름을 나열 합니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-236">List the names of libraries that are cached.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="6e803-237">예제</span><span class="sxs-lookup"><span data-stu-id="6e803-237">Examples</span></span>

* <span data-ttu-id="6e803-238">공급자별 캐시 된 라이브러리의 이름을 보려면, 다음 명령 중 하나를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-238">To view the names of cached libraries per provider, use one of the following commands:</span></span>

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  <span data-ttu-id="6e803-239">다음과 비슷한 출력이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-239">Output similar to the following is displayed:</span></span>

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

* <span data-ttu-id="6e803-240">공급자별 캐시 된 라이브러리 파일의 이름을 보려면:</span><span class="sxs-lookup"><span data-stu-id="6e803-240">To view the names of cached library files per provider:</span></span>

  ```console
  libman cache list --files
  ```

  <span data-ttu-id="6e803-241">다음과 비슷한 출력이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-241">Output similar to the following is displayed:</span></span>

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

  <span data-ttu-id="6e803-242">앞의 출력 표시 버전 3.2.1 및 3.3.1 CDNJS 공급자 아래에서 캐시 되는 jQuery를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-242">Notice the preceding output shows that jQuery versions 3.2.1 and 3.3.1 are cached under the CDNJS provider.</span></span>

* <span data-ttu-id="6e803-243">CDNJS 공급자에 대 한 라이브러리 캐시를 비우기:</span><span class="sxs-lookup"><span data-stu-id="6e803-243">To empty the library cache for the CDNJS provider:</span></span>

  ```console
  libman cache clean cdnjs
  ```

  <span data-ttu-id="6e803-244">CDNJS 공급자 캐시를 비운 후는 `libman cache list` 명령은 다음을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-244">After emptying the CDNJS provider cache, the `libman cache list` command displays the following:</span></span>

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

* <span data-ttu-id="6e803-245">모든 지원 되는 공급자에 대 한 캐시를 비우기:</span><span class="sxs-lookup"><span data-stu-id="6e803-245">To empty the cache for all supported providers:</span></span>

  ```console
  libman cache clean
  ```

  <span data-ttu-id="6e803-246">모든 공급자 캐시를 비운 후는 `libman cache list` 명령은 다음을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="6e803-246">After emptying all provider caches, the `libman cache list` command displays the following:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a><span data-ttu-id="6e803-247">추가 자료</span><span class="sxs-lookup"><span data-stu-id="6e803-247">Additional resources</span></span>

* [<span data-ttu-id="6e803-248">전역 도구 설치</span><span class="sxs-lookup"><span data-stu-id="6e803-248">Install a Global Tool</span></span>](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [<span data-ttu-id="6e803-249">LibMan GitHub 리포지토리</span><span class="sxs-lookup"><span data-stu-id="6e803-249">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
