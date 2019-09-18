---
title: ASP.NET Core에서 기능을 사용 하 여 CLI (명령줄 인터페이스)를 사용 합니다.
author: scottaddie
description: ASP.NET Core 프로젝트에서 기능 CLI (명령줄 인터페이스)를 사용 하는 방법에 대해 알아봅니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/30/2018
uid: client-side/libman/libman-cli
ms.openlocfilehash: cf61bab2f0c3fc33d293968b8ac380cb56958d29
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080628"
---
# <a name="use-the-libman-command-line-interface-cli-with-aspnet-core"></a>ASP.NET Core에서 기능을 사용 하 여 CLI (명령줄 인터페이스)를 사용 합니다.

작성자: [Scott Addie](https://twitter.com/Scott_Addie)

이 기능을 지 원하는 플랫폼 간 도구는 .NET [Core에서 지원](xref:client-side/libman/index) 되는 모든 플랫폼에서 지원 됩니다.

## <a name="prerequisites"></a>필수 구성 요소

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a>설치

을 설치 하려면 다음을 수행 합니다.

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

[.Net Core 전역 도구](/dotnet/core/tools/global-tools#install-a-global-tool) 는 [Microsoft web.config](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) 패키지에서 설치 됩니다.

특정 NuGet 패키지 원본에서 패키지를 설치 하려면 다음을 수행 합니다.

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

위의 예제에서 .NET Core 글로벌 도구는 로컬 Windows 컴퓨터의 *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* 파일에서 설치 됩니다.

## <a name="usage"></a>사용법

CLI를 성공적으로 설치한 후에는 다음 명령을 사용할 수 있습니다.

```console
libman
```

설치 된 CLI 버전을 보려면 다음을 수행 합니다.

```console
libman --version
```

사용 가능한 CLI 명령을 보려면 다음을 수행 합니다.

```console
libman --help
```

위의 명령은 다음과 유사한 출력을 표시 합니다.

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

다음 섹션에서는 사용 가능한 CLI 명령에 대해 간략하게 설명합니다.

## <a name="initialize-libman-in-the-project"></a>프로젝트에서이를 초기화 합니다.

이 명령은 파일 (없는 경우)을 *만듭니다.* `libman init` 기본 항목 템플릿 콘텐츠를 사용 하 여 파일을 만듭니다.

### <a name="synopsis"></a>개요

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a>옵션

다음 옵션은 `libman init` 명령에 사용할 수 있습니다.

* `-d|--default-destination <PATH>`

  현재 폴더에 상대적인 경로입니다. 라이브러리 파일은 라이브러리에 대 한 속성이 정의 `destination` 되어 있지 않은 *경우에는*이 위치에 설치 됩니다. 값 `<PATH>` 은이 `defaultDestination` 속성에 기록 *됩니다.*

* `-p|--default-provider <PROVIDER>`

  지정 된 라이브러리에 대해 정의 된 공급자가 없는 경우 사용할 공급자입니다. 값 `<PROVIDER>` 은이 `defaultProvider` 속성에 기록 *됩니다.* 을 `<PROVIDER>` 다음 값 중 하나로 바꿉니다.

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>예

ASP.NET Core 프로젝트에서이 파일을 만들려면 다음을 수행 *합니다* .

* 프로젝트 루트로 이동 합니다.
* 다음 명령을 실행합니다.

  ```console
  libman init
  ```

* 기본 공급자의 이름을 입력 하거나 키를 눌러 `Enter` 기본 cdnjs 공급자를 사용 합니다. 유효한 값은 다음과 같습니다.

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![liman init 명령-기본 공급자](_static/libman-init-provider.png)

다음 콘텐츠를 사용 하 여 프로젝트 루트 *에 파일을 추가 합니다.*

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a>라이브러리 파일 추가하기

이 `libman install` 명령은 라이브러리 파일을 다운로드 하 여 프로젝트에 설치 합니다. 파일이 없는 경우 추가 됩니다 *.* 라이브러리 파일에 대 한 구성 세부 정보를 저장 하도록 *라이브러리 파일을* 수정 합니다.

### <a name="synopsis"></a>개요

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a>인수

`LIBRARY`

설치할 라이브러리의 이름입니다. 이 이름에는 버전 번호 표기법 (예: `@1.2.0`)이 포함 될 수 있습니다.

### <a name="options"></a>옵션

다음 옵션은 `libman install` 명령에 사용할 수 있습니다.

* `-d|--destination <PATH>`

  라이브러리를 설치할 위치입니다. 지정 하지 않으면 기본 위치가 사용 됩니다. 지정 된 `defaultDestination` 속성이 없습니다 *. json*에는이 옵션이 필요 합니다.

* `--files <FILE>`

  라이브러리에서 설치할 파일의 이름을 지정 합니다. 지정 하지 않으면 라이브러리의 모든 파일이 설치 됩니다. 설치할 파일 `--files` 마다 하나의 옵션을 제공 합니다. 상대 경로도 지원 됩니다. 예를 들어 `--files dist/browser/signalr.js`을 참조하십시오.

* `-p|--provider <PROVIDER>`

  라이브러리 획득에 사용할 공급자의 이름입니다. 을 `<PROVIDER>` 다음 값 중 하나로 바꿉니다.
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  지정 `defaultProvider` 하지 않은 경우에 *는이 속성* 을 사용 합니다. 지정 된 `defaultProvider` 속성이 없습니다 *. json*에는이 옵션이 필요 합니다.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>예

다음을 고려 하십시오 *. json* 파일:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

JQuery 버전 3.2.1 *jquery* 파일을 CDNJS 공급자를 사용 하는 *wwwroot/scripts/jQuery* 폴더에 설치 하려면 다음을 수행 합니다.

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

이 *파일은* 다음과 유사 합니다.

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

파일 시스템 공급자를 사용 하 여 *C\\: temp\\contosoCalendar\\*  에서 *node.js* 및 *calendar .css* 파일을 설치 하려면:

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

다음은 두 가지 이유로 표시 되는 메시지입니다.

* 이 파일에는 `defaultDestination` 속성이 포함 되어 있지 않습니다.
* 명령 `libman install` 에 `-d|--destination` 옵션이 포함 되어 있지 않습니다.

![\man 설치 명령-대상](_static/libman-install-destination.png)

기본 대상을 승인한 후 *에는 다음과* 같은 경우에 해당 합니다.

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

이 명령은 라이브러리 *파일을 설치 합니다.* `libman restore` 다음 규칙이 적용됩니다.

* 프로젝트 루트에 없습니다 *. json* 파일이 없으면 오류가 반환 됩니다.
* 라이브러리에서 공급자를 지정 하 `defaultProvider` 는 경우에는 라이브러리의 속성이 무시 됩니다.
* 라이브러리에서 대상을 지정 하 `defaultDestination` 는 경우에는 라이브러리 의 속성이 무시 됩니다.

### <a name="synopsis"></a>개요

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a>변수

다음 옵션은 `libman restore` 명령에 사용할 수 있습니다.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>예

라이브러리 파일을 복원 하려면 다음을 수행 합니다. *json*:

```console
libman restore
```

## <a name="delete-library-files"></a>라이브러리 파일 삭제하기

`libman clean` 명령을 통해 이전에 라이브러리 파일을 제거 합니다. 이 작업이 삭제 된 후 비어 있게 되는 폴더입니다. 라이브러리 파일의 연결 된 구성 `libraries` 은 제거 되지 않습니다 *.*

### <a name="synopsis"></a>개요

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a>변수

다음 옵션은 `libman clean` 명령에 사용할 수 있습니다.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>예

라이브러리 파일을 삭제 하려면 다음을 수행 합니다.

```console
libman clean
```

## <a name="uninstall-library-files"></a>라이브러리 파일 제거하기

다음 `libman uninstall` 명령을 수행 합니다.

* 지정 된 라이브러리와 연결 된 모든 파일을 *라이브러리의 대상에서 삭제 합니다.*
* 연결 된 라이브러리 구성을 제거 합니다 *.*

다음과 같은 경우 오류가 발생 합니다.

* 프로젝트 루트에 없습니다 *. json* 파일이 없습니다.
* 지정 된 라이브러리가 없습니다.

이름이 같은 라이브러리가 둘 이상 설치 되어 있으면 하나를 선택 하 라는 메시지가 표시 됩니다.

### <a name="synopsis"></a>개요

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a>인수

`LIBRARY`

제거할 라이브러리의 이름입니다. 이 이름에는 버전 번호 표기법 (예: `@1.2.0`)이 포함 될 수 있습니다.

### <a name="options"></a>변수

다음 옵션은 `libman uninstall` 명령에 사용할 수 있습니다.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>예

다음을 고려 하십시오 *. json* 파일:

[!code-json[](samples/LibManSample/libman.json)]

* JQuery를 제거 하려면 다음 명령 중 하나를 수행 합니다.

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* `filesystem` 공급자를 통해 설치 된 lodash 파일을 제거 하려면 다음을 수행 합니다.

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a>라이브러리 버전 업데이트

이 `libman update` 명령은 지정 된 버전에 라이브러리를 통해 설치 된 라이브러리를 업데이트 합니다.

다음과 같은 경우 오류가 발생 합니다.

* 프로젝트 루트에 없습니다 *. json* 파일이 없습니다.
* 지정 된 라이브러리가 없습니다.

이름이 같은 라이브러리가 둘 이상 설치 되어 있으면 하나를 선택 하 라는 메시지가 표시 됩니다.

### <a name="synopsis"></a>개요

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a>인수

`LIBRARY`

업데이트할 라이브러리의 이름입니다.

### <a name="options"></a>옵션

다음 옵션은 `libman update` 명령에 사용할 수 있습니다.

* `-pre`

  라이브러리의 최신 시험판 버전을 가져옵니다.

* `--to <VERSION>`

  라이브러리의 특정 버전을 가져옵니다.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>예

* JQuery를 최신 버전으로 업데이트 하려면 다음을 수행 합니다.

  ```console
  libman update jquery
  ```

* JQuery를 버전 3.3.1로 업데이트 하려면 다음을 수행 합니다.

  ```console
  libman update jquery --to 3.3.1
  ```

* JQuery를 최신 시험판 버전으로 업데이트 하려면 다음을 수행 합니다.

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a>라이브러리 캐시 관리

`libman cache` 명령은 라이브러리 캐시를 관리 합니다. 공급자 `filesystem` 는 라이브러리 캐시를 사용 하지 않습니다.

### <a name="synopsis"></a>개요

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a>인수

`PROVIDER`

`clean` 명령 에서만 사용 됩니다. 정리할 공급자 캐시를 지정 합니다. 유효한 값은 다음과 같습니다.

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a>옵션

다음 옵션은 `libman cache` 명령에 사용할 수 있습니다.

* `--files`

  캐시 된 파일의 이름을 나열 합니다.

* `--libraries`

  캐시 된 라이브러리의 이름을 나열 합니다.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>예

* 공급자 당 캐시 된 라이브러리의 이름을 보려면 다음 명령 중 하나를 사용 합니다.

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

* 공급자 당 캐시 된 라이브러리 파일의 이름을 보려면 다음을 수행 합니다.

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

  위의 출력은 jQuery 버전 3.2.1 및 3.3.1가 CDNJS 공급자 아래에 캐시 됨을 보여 줍니다.

* CDNJS 공급자에 대 한 라이브러리 캐시를 비우려면:

  ```console
  libman cache clean cdnjs
  ```

  Cdnjs 공급자 캐시 `libman cache list` 를 비운 후 명령은 다음을 표시 합니다.

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

* 지원 되는 모든 공급자에 대 한 캐시를 비우려면:

  ```console
  libman cache clean
  ```

  모든 공급자 캐시를 `libman cache list` 비운 후 명령은 다음을 표시 합니다.

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a>추가 자료

* [글로벌 도구 설치](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [LibMan GitHub 리포지토리](https://github.com/aspnet/LibraryManager)
