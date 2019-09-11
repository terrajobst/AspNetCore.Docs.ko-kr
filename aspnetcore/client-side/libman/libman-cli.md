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
# <a name="use-the-libman-command-line-interface-cli-with-aspnet-core"></a>LibMan 명령줄 인터페이스 (CLI)를 사용 하 여 ASP.NET Core를 사용 하 여

작성자: [Scott Addie](https://twitter.com/Scott_Addie)

합니다 [LibMan](xref:client-side/libman/index) CLI는 모든.NET Core는 지원 범위에서 지원 되는 플랫폼 간 도구입니다.

## <a name="prerequisites"></a>전제 조건

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a>설치

LibMan CLI를 설치 합니다.

```console
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

A [.NET Core에 대 한 전역 도구](/dotnet/core/tools/global-tools#install-a-global-tool) 에서 설치 되는 [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet 패키지.

에 특정 NuGet 패키지 소스에서 LibMan CLI를 설치 합니다.

```console
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

앞의 예제에서 로컬 Windows 컴퓨터에서.NET Core 전역 도구 설치 *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* 파일입니다.

## <a name="usage"></a>사용

CLI의 설치 후 다음 명령을 사용할 수 있습니다.

```console
libman
```

설치 된 CLI 버전을 보려면:

```console
libman --version
```

사용 가능한 CLI 명령을 보려면

```console
libman --help
```

이전 명령에는 다음과 유사한 출력이 표시 됩니다.

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

다음 섹션에서는 사용 가능한 CLI 명령을 간략하게 설명합니다.

## <a name="initialize-libman-in-the-project"></a>LibMan 프로젝트 초기화

합니다 `libman init` 명령은 만듭니다는 *libman.json* 없는 경우 파일입니다. 파일의 기본 항목 템플릿 콘텐츠를 사용 하 여 만들어집니다.

### <a name="synopsis"></a>개요

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a>옵션

다음 옵션을 사용할 수는 `libman init` 명령:

* `-d|--default-destination <PATH>`

  현재 폴더의 상대 경로입니다. 라이브러리 파일은이 위치에 설치 되지 않은 경우 `destination` 속성에서 라이브러리에 대해 정의 됩니다 *libman.json*합니다. `<PATH>` 값에 기록 됩니다 합니다 `defaultDestination` 속성을 *libman.json*합니다.

* `-p|--default-provider <PROVIDER>`

  공급자가 없습니다 지정 된 라이브러리에 대해 정의 된 경우 사용 하는 공급자입니다. `<PROVIDER>` 값에 기록 됩니다 합니다 `defaultProvider` 속성을 *libman.json*합니다. 대체 `<PROVIDER>` 다음 값 중 하나를 사용 하 여:

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>예제

만들려는 *libman.json* ASP.NET Core 프로젝트에서 파일:

* 프로젝트 루트에 이동 합니다.
* 다음 명령을 실행합니다.

  ```console
  libman init
  ```

* 키를 눌러 기본 공급자의 이름을 입력 `Enter` 기본 CDNJS 공급자를 사용 하도록 합니다. 유효한 값은 다음과 같습니다.

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![libman init 명령-기본 공급자](_static/libman-init-provider.png)

A *libman.json* 파일이 다음과 같은 내용으로 프로젝트 루트에 추가 됩니다.

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a>라이브러리 파일 추가하기

`libman install` 명령을 다운로드 하 고 프로젝트에 라이브러리 파일을 설치 합니다. A *libman.json* 파일이 없는 경우 추가 됩니다. 합니다 *libman.json* 라이브러리 파일에 대 한 구성 세부 정보를 저장할 파일을 수정 합니다.

### <a name="synopsis"></a>개요

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a>인수

`LIBRARY`

설치 하려면 라이브러리의 이름입니다. 이 이름은 버전 번호 표기법 포함 될 수 있습니다 (예를 들어 `@1.2.0`).

### <a name="options"></a>옵션

다음 옵션을 사용할 수는 `libman install` 명령:

* `-d|--destination <PATH>`

  라이브러리를 설치할 위치입니다. 지정 하지 않으면 기본 위치에 사용 됩니다. 없으면 `defaultDestination` 속성에 지정 된 *libman.json*,이 옵션을 지정 해야 합니다.

* `--files <FILE>`

  라이브러리에서 설치 파일의 이름을 지정 합니다. 지정 하지 않으면 라이브러리에서 모든 파일이 설치 됩니다. 제공 `--files` 파일당 옵션이 설치 되어 있어야 합니다. 상대 경로 너무 지원 됩니다. 예: `--files dist/browser/signalr.js`

* `-p|--provider <PROVIDER>`

  라이브러리 획득을 위해 사용 하 여 공급자의 이름입니다. 대체 `<PROVIDER>` 다음 값 중 하나를 사용 하 여:
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  지정 하지 않으면 합니다 `defaultProvider` 속성에서 *libman.json* 사용 됩니다. 없으면 `defaultProvider` 속성에 지정 된 *libman.json*,이 옵션을 지정 해야 합니다.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>예제

다음을 고려해 야 *libman.json* 파일:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

3\.2.1 jQuery 버전을 설치 하려면 *jquery.min.js* 파일을 합니다 *jquery스크립트/wwwroot/* CDNJS 공급자를 사용 하 여 폴더:

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

합니다 *libman.json* 파일에는 다음 예제와 유사 합니다.

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

설치 하는 *calendar.js* 하 고 *calendar.css* 에서 파일을 *c:\\temp\\contosoCalendar\\*  파일 시스템을 사용 하 여 공급자:

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

두 가지 이유로 다음과 같은 메시지가 표시 됩니다.

* 합니다 *libman.json* 파일에 포함 하지 않습니다는 `defaultDestination` 속성입니다.
* 합니다 `libman install` 명령을 포함 하지 않습니다는 `-d|--destination` 옵션입니다.

![libman 명령-설치 대상](_static/libman-install-destination.png)

기본 대상에 동의한 후 합니다 *libman.json* 파일에는 다음 예제와 유사 합니다.

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

## <a name="restore-library-files"></a>라이브러리 파일 복원하기

합니다 `libman restore` 명령에 정의 된 라이브러리 파일 설치 *libman.json*합니다. 이 때 적용되는 규칙은 다음과 같습니다.

* 없으면 *libman.json* 파일이 있는 프로젝트 루트에서 오류가 반환 됩니다.
* 라이브러리에는 공급자를 지정 하는 경우는 `defaultProvider` 속성에서 *libman.json* 무시 됩니다.
* 라이브러리를 대상으로 지정 하는 경우는 `defaultDestination` 속성에서 *libman.json* 무시 됩니다.

### <a name="synopsis"></a>개요

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a>옵션

다음 옵션을 사용할 수는 `libman restore` 명령:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>예제

에 정의 된 라이브러리 파일을 복원할 *libman.json*:

```console
libman restore
```

## <a name="delete-library-files"></a>라이브러리 파일 삭제하기

`libman clean` 명령은 LibMan 통해 이전에 복원 하는 라이브러리 파일을 삭제 합니다. 이 작업 후 빈 할 폴더 삭제 됩니다. 라이브러리 파일의 구성에 연결 합니다 `libraries` 속성을 *libman.json* 제거 되지 않습니다.

### <a name="synopsis"></a>개요

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a>옵션

다음 옵션을 사용할 수는 `libman clean` 명령:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>예제

LibMan를 통해 설치 된 라이브러리 파일을 삭제:

```console
libman clean
```

## <a name="uninstall-library-files"></a>라이브러리 파일 제거하기

`libman uninstall` 명령:

* 대상에서 지정 된 라이브러리와 연결 된 모든 파일을 삭제 *libman.json*합니다.
* 연결 된 라이브러리 구성을 제거 *libman.json*합니다.

오류가 발생 하는 경우:

* 아니오 *libman.json* 파일이 프로젝트 루트에 있습니다.
* 지정된 된 라이브러리 존재 하지 않습니다.

동일한 이름 가진 둘 이상의 라이브러리가 설치 되므로 하나를 선택 하려면 묻는 메시지가 나타납니다.

### <a name="synopsis"></a>개요

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a>인수

`LIBRARY`

제거할 라이브러리의 이름입니다. 이 이름은 버전 번호 표기법 포함 될 수 있습니다 (예를 들어 `@1.2.0`).

### <a name="options"></a>옵션

다음 옵션을 사용할 수는 `libman uninstall` 명령:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>예제

다음을 고려해 야 *libman.json* 파일:

[!code-json[](samples/LibManSample/libman.json)]

* JQuery를 제거 하려면 다음 명령 중 하나 성공 합니다.

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* 통해 설치 및 Lodash 파일을 제거 하는 `filesystem` 공급자:

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a>라이브러리 버전 업데이트

`libman update` 명령은 LibMan를 통해 지정된 된 버전에 설치 된 라이브러리를 업데이트 합니다.

오류가 발생 하는 경우:

* 아니오 *libman.json* 파일이 프로젝트 루트에 있습니다.
* 지정된 된 라이브러리 존재 하지 않습니다.

동일한 이름 가진 둘 이상의 라이브러리가 설치 되므로 하나를 선택 하려면 묻는 메시지가 나타납니다.

### <a name="synopsis"></a>개요

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a>인수

`LIBRARY`

업데이트 하려면 라이브러리의 이름입니다.

### <a name="options"></a>옵션

다음 옵션을 사용할 수는 `libman update` 명령:

* `-pre`

  라이브러리의 최신 시험판 버전을 가져옵니다.

* `--to <VERSION>`

  라이브러리의 특정 버전을 가져옵니다.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>예제

* JQuery를 최신 버전으로 업데이트:

  ```console
  libman update jquery
  ```

* JQuery 버전 3.3.1 업데이트:

  ```console
  libman update jquery --to 3.3.1
  ```

* JQuery를 최신 시험판 버전으로 업데이트:

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a>라이브러리 캐시 관리

`libman cache` 명령 LibMan 라이브러리 캐시를 관리 합니다. `filesystem` 공급자 라이브러리 캐시를 사용 하지 않습니다.

### <a name="synopsis"></a>개요

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a>인수

`PROVIDER`

에서만 사용할 수는 `clean` 명령입니다. 공급자 캐시 정리를 지정 합니다. 유효한 값은 다음과 같습니다.

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a>옵션

다음 옵션을 사용할 수는 `libman cache` 명령:

* `--files`

  캐시 된 파일의 이름을 나열 합니다.

* `--libraries`

  캐시 되는 라이브러리의 이름을 나열 합니다.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>예제

* 공급자별 캐시 된 라이브러리의 이름을 보려면, 다음 명령 중 하나를 사용 합니다.

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  다음과 비슷한 출력이 표시됩니다.

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

* 공급자별 캐시 된 라이브러리 파일의 이름을 보려면:

  ```console
  libman cache list --files
  ```

  다음과 비슷한 출력이 표시됩니다.

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

  앞의 출력 표시 버전 3.2.1 및 3.3.1 CDNJS 공급자 아래에서 캐시 되는 jQuery를 보여 줍니다.

* CDNJS 공급자에 대 한 라이브러리 캐시를 비우기:

  ```console
  libman cache clean cdnjs
  ```

  CDNJS 공급자 캐시를 비운 후는 `libman cache list` 명령은 다음을 표시 합니다.

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

* 모든 지원 되는 공급자에 대 한 캐시를 비우기:

  ```console
  libman cache clean
  ```

  모든 공급자 캐시를 비운 후는 `libman cache list` 명령은 다음을 표시 합니다.

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a>추가 자료

* [전역 도구 설치](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [LibMan GitHub 리포지토리](https://github.com/aspnet/LibraryManager)
