---
title: Visual Studio에서 ASP.NET Core와 LibMan 사용하기
author: scottaddie
description: Visual Studio로 ASP.NET Core 프로젝트에서 LibMan을 사용하는 방법을 알아봅니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
uid: client-side/libman/libman-vs
ms.openlocfilehash: ebfb405516d968bf5d5b8cff956a9892457027f2
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813469"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a>Visual Studio에서 ASP.NET Core와 LibMan 사용하기

작성자: [Scott Addie](https://twitter.com/Scott_Addie)

Visual Studio는 ASP.NET Core 프로젝트에서 [LibMan](xref:client-side/libman/index)에 대한 다음과 같은 기본 제공 기능을 지원합니다.

* 빌드 시 LibMan 복원 작업을 구성 및 실행하기 위한 지원.
* LibMan 복원 및 정리 작업을 실행하기 위한 메뉴 항목.
* 라이브러리를 검색하고 프로젝트에 파일을 추가하기 위한 검색 대화 상자.
* LibMan 매니페스트 파일인 *libman.json*&mdash;에 대한 편집 지원.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(다운로드 방법)](xref:index#how-to-download-a-sample)

## <a name="prerequisites"></a>필수 구성 요소

* **ASP.NET 및 웹 개발** 워크로드가 있는 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)

## <a name="add-library-files"></a>라이브러리 파일 추가하기

두 가지 방법으로 ASP.NET Core 프로젝트에 라이브러리 파일을 추가할 수 있습니다.

1. [클라이언트 쪽 라이브러리 추가 대화 상자 사용하기](#use-the-add-client-side-library-dialog)
1. [직접 LibMan 매니페스트 파일 항목 구성하기](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a>클라이언트 쪽 라이브러리 추가 대화 상자 사용하기

클라이언트 쪽 라이브러리를 설치하려면 다음의 단계를 수행합니다.

* **솔루션 탐색기**에서 파일을 추가할 프로젝트 폴더를 마우스 오른쪽 버튼으로 클릭합니다. **추가** > **클라이언트 라이브러리 추가**를 선택합니다. **클라이언트 쪽 라이브러리 추가** 대화 상자가 나타납니다.

  ![클라이언트 쪽 라이브러리 추가 대화 상자](_static/add-library-dialog.png)

* **공급자** 드롭다운에서 라이브러리 공급자를 선택합니다. 기본 공급자는 CDNJS입니다.
* **라이브러리** 텍스트 상자에 가져올 라이브러리 이름을 입력합니다. IntelliSense가 입력한 텍스트로 시작하는 라이브러리의 목록을 제공해줍니다.
* IntelliSense 목록에서 라이브러리를 선택합니다. 라이브러리 이름에 접미사로 `@` 기호와 선택한 공급자에게 알려진 최신의 안정적인 버전이 추가되어 있는 것에 주의하시기 바랍니다.
* 포함할 파일을 결정합니다.
  * 라이브러리의 모든 파일을 포함시키려면 **모든 라이브러리 파일 포함** 라디오 단추를 선택합니다.
  * 라이브러리의 일부 파일만 포함시키려면 **특정 파일 선택** 라디오 단추를 선택합니다. 이 라디오 단추를 선택하면 파일 선택기 트리가 활성화됩니다. 다운로드 할 파일명의 왼쪽에 위치한 상자를 체크합니다.
* **대상 위치** 텍스트 상자에 파일을 저장할 프로젝트 폴더를 지정합니다. 각 라이브러리를 별도의 폴더에 저장하는 것을 권장합니다.

  제안되는 **대상 위치** 폴더는 대화 상자가 시작된 위치에 따라서 달라집니다.

  * 프로젝트 루트에서 시작된 경우:
    * *wwwroot*가 존재하면 *wwwroot/lib*가 사용됩니다.
    * *wwwroot*가 존재하지 않으면 *lib*가 사용됩니다.
  * 프로젝트 폴더에서 시작된 경우 해당 폴더 이름이 사용됩니다.

  폴더 제안 뒤에는 라이브러리의 이름이 붙습니다. 다음 표는 Razor Pages 프로젝트에 jQuery를 설치하는 경우의 폴더 제안을 보여줍니다.
  
  |시작 위치                           |제안되는 폴더      |
  |------------------------------------------|----------------------|
  |프로젝트 루트 (*wwwroot*가 존재할 경우)        |*jquery/wwwroot/lib/* |
  |프로젝트 루트 (*wwwroot*가 존재하지 않을 경우) |*lib/jquery/*         |
  |프로젝트의 *Pages* 폴더                 |*Pages/jquery/*       |

* *libman.json*의 구성에 따라 파일을 다운로드하려면 **설치** 버튼을 클릭합니다.
* **출력** 창의 **라이브러리 관리자** 피드에서 설치 세부 정보를 검토할 수 있습니다. 예를 들어 다음과 같습니다.

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

### <a name="manually-configure-libman-manifest-file-entries"></a>직접 LibMan 매니페스트 파일 항목 구성하기

Visual Studio의 모든 LibMan 작업은 프로젝트 루트의 LibMan 매니페스트(*libman.json*)의 내용을 기반으로 합니다. 직접 *libman.json*을 편집하여 프로젝트의 라이브러리 파일을 구성할 수 있습니다. *libman.json*이 저장되면 Visual Studio는 모든 라이브러리 파일을 복원합니다.

편집을 위해 *libman.json*을 열려면 다음과 같은 방법을 사용할 수 있습니다.

* **솔루션 탐색기**에서 *libman.json* 파일을 더블 클릭합니다.
* **솔루션 탐색기**에서 마우스 오른쪽 버튼으로 프로젝트를 클릭하고 **클라이언트 쪽 라이브러리 관리**를 선택합니다. **&#8224;**
* Visual studio의 **프로젝트** 메뉴에서 **클라이언트 쪽 라이브러리 관리**를 선택합니다. **&#8224;**

**&#8224;** 프로젝트 루트에 아직 *libman.json* 파일이 존재하지 않으면 기본 항목 템플릿 내용을 담은 파일이 만들어집니다.

Visual Studio는 색 지정, 서식 지정, IntelliSense 및 스키마 유효성 검사 같은 다양한 JSON 편집 지원을 제공합니다. LibMan 매니페스트의 JSON 스키마는 [https://json.schemastore.org/libman](https://json.schemastore.org/libman)에서 살펴볼 수 있습니다.

다음 매니페스트 파일을 사용하는 경우 LibMan은 `libraries` 속성에 정의된 각 구성에 따라 파일을 검색합니다. `libraries` 내에 정의된 개체 리터럴에 대한 설명은 다음과 같습니다.

* CDNJS 공급자에서 [jQuery](https://jquery.com/) 버전 3.3.1의 하위 집합이 검색됩니다. 하위 집합은 `files` 속성에 정의됩니다 (&mdash;*jquery.min.js*, *jquery.js* 및 *jquery.min.map*). 이러한 파일은 프로젝트의 *wwwroot/lib/jquery* 폴더에 배치됩니다.
* [부트스트랩](https://getbootstrap.com/) 버전 4.1.3 전체가 검색되어 *wwwroot/lib/bootstrap* 폴더에 배치됩니다. 개체 리터럴의 `provider` 속성은 `defaultProvider` 속성 값을 재정의합니다. LibMan은 unpkg 공급자에서 부트스트랩 파일을 검색합니다.
* [Lodash](https://lodash.com/)의 하위 집합은 조직 내 관리 기관의 승인을 받았습니다. *lodash.js* 및 *lodash.min.js* 파일은 로컬 파일 시스템의 *C:\\temp\\lodash\\* 에서 검색됩니다. 이러한 파일은 프로젝트의 *wwwroot/lib/lodash* 폴더에 복사됩니다.

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> LibMan은 각 공급자의 각 라이브러리에 대해 하나의 버전만 지원합니다. 지정된 공급자에 대해 동일한 라이브러리 이름을 가진 두 개의 라이브러리가 존재할 경우 *libman.json* 파일은 스키마 유효성 검증에 실패합니다.

## <a name="restore-library-files"></a>라이브러리 파일 복원하기

Visual Studio에서 라이브러리 파일을 복원하려면 프로젝트 루트에 유효한 *libman.json* 파일이 존재해야 합니다. 복원된 파일은 각 라이브러리에 대해 지정된 프로젝트의 위치에 배치됩니다.

ASP.NET Core 프로젝트에서 두 가지 방법으로 라이브러리 파일을 복원할 수 있습니다.

1. [빌드 시 파일 복원하기](#restore-files-during-build)
1. [직접 파일 복원하기](#restore-files-manually)

### <a name="restore-files-during-build"></a>빌드 시 파일 복원하기

LibMan은 빌드 프로세스의 일부로 정의된 라이브러리 파일을 복원할 수 있습니다. 기본적으로 *빌드 시 복원* 동작은 비활성화되어 있습니다.

빌드 시 복원 동작을 활성화시키고 테스트해 보려면:

* **솔루션 탐색기**에서 *libman.json*을 마우스 오른쪽 버튼으로 클릭하고 컨텍스트 메뉴에서 **빌드에 클라이언트 쪽 라이브러리 복원 사용**을 선택합니다.
* NuGet 패키지를 설치하라는 메시지가 나타나면 **예** 버튼을 클릭합니다. 그러면 [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet 패키지가 프로젝트에 추가됩니다.

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* 프로젝트를 빌드해서 LibMan 파일 복원이 발생하는지 확인합니다. `Microsoft.Web.LibraryManager.Build`  패키지는 프로젝트의 빌드 작업 중 LibMan을 실행하는 MSBuild 대상을 주입합니다.
* LibMan 활동 로그에 대한 **출력** 창의 **빌드** 피드를 검토합니다.

  ```console
  1>------ Build started: Project: LibManSample, Configuration: Debug Any CPU ------
  1>
  1>Restore operation started...
  1>Restoring library jquery@3.3.1...
  1>Restoring library bootstrap@4.1.3...
  1>
  1>2 libraries restored in 10.66 seconds
  1>LibManSample -> C:\LibManSample\bin\Debug\netcoreapp2.1\LibManSample.dll
  ========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
  ```

빌드 시 복원 동작을 활성화시키면 *libman.json*의 컨텍스트 메뉴에는 **빌드에 클라이언트 쪽 라이브러리 사용하지 않음** 옵션이 표시됩니다. 이 옵션을 선택하면 프로젝트 파일에서 `Microsoft.Web.LibraryManager.Build` 패키지 참조가 제거됩니다. 따라서 더 이상 클라이언트 쪽 라이브러리가 빌드 시마다 복원되지 않습니다.

빌드 시 복원 설정과 관계없이 *libman.json*의 컨텍스트 메뉴에서 언제든지 수동으로 복원할 수 있습니다. 자세한 내용은 [직접 파일 복원하기](#restore-files-manually)를 참고하시기 바랍니다.

### <a name="restore-files-manually"></a>직접 파일 복원하기

라이브러리 파일을 직접 복원하려면:

* 솔루션의 모든 프로젝트를 복원하는 경우:
  * **솔루션 탐색기**에서 솔루션 이름을 마우스 오른쪽 버튼으로 클릭합니다.
  * **클라이언트 라이브러리를 복원** 옵션을 선택합니다.
* 특정 프로젝트를 복원하는 경우:
  * **솔루션 탐색기**에서 *libman.json* 파일을 마우스 오른쪽 버튼으로 클릭합니다.
  * **클라이언트 라이브러리를 복원** 옵션을 선택합니다.

복원 작업이 실행되는 동안:

* Visual Studio 상태 표시줄의 작업 상태 센터(TSC) 아이콘이 애니메이션되고 *복원 작업이 시작됨*으로 표시됩니다. 이 아이콘을 클릭하면 알려진 백그라운드 작업을 나열하는 도구 설명이 열립니다.
* 메시지는 상태 표시줄 및 **출력** 창의 **라이브러리 관리자** 피드로 전송됩니다. 예를 들어 다음과 같습니다.

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

## <a name="delete-library-files"></a>라이브러리 파일 삭제하기

Visual Studio에서 이전에 복원한 라이브러리 파일을 삭제하는 *정리* 작업을 수행하려면:

* *솔루션 탐색기*에서 **libman.json** 파일을 마우스 오른쪽 버튼으로 클릭합니다.
* **청소** 옵션을 선택합니다.

정리 작업은 의도하지 않은 비-라이브러리 파일의 제거를 방지하기 위해서 전체 디렉터리를 삭제하지 않습니다. 이전 복원에 포함된 파일들만 제거합니다.

정리 작업이 실행되는 동안:

* Visual Studio 상태 표시줄의 TSC 아이콘이 애니메이션되고 *라이브러리 정리 작업이 시작됨*으로 표시됩니다. 이 아이콘을 클릭하면 알려진 백그라운드 작업을 나열하는 도구 설명이 열립니다.
* 상태 표시줄 메시지와 **라이브러리 관리자** 의 피드를 **출력** 창입니다. 예를 들어 다음과 같습니다.

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

정리 작업은 프로젝트에서 파일만 삭제합니다. 라이브러리 파일은 이후 복원 작업 시 빠른 검색을 위해 캐시에 남아 있습니다. 로컬 컴퓨터의 캐시에 저장된 라이브러리 파일을 관리하려면 [LibMan CLI](xref:client-side/libman/libman-cli)를 사용합니다.

## <a name="uninstall-library-files"></a>라이브러리 파일 제거하기

라이브러리 파일을 제거하려면:

* *libman.json*을 엽니다.
* 캐럿을 해당 `libraries` 개체 리터럴에 배치합니다.
* 왼쪽 여백에 표시되는 전구 아이콘을 클릭하고 **\<library_name>@\<library_version> 제거**를 선택합니다.

  ![라이브러리 제거 컨텍스트 메뉴 옵션](_static/uninstall-menu-option.png)

또는 직접 LibMan 매니페스트(*libman.json*)를 편집하고 저장할 수 있습니다. 파일을 저장하면 [복원 작업](#restore-library-files)이 실행됩니다. *libman.json*에 더 이상 정의되어 있지 않은 라이브러리 파일은 프로젝트에서 제거됩니다.

## <a name="update-library-version"></a>라이브러리 버전 업데이트

업데이트된 라이브러리 버전을 확인하려면:

* *libman.json*을 엽니다.
* 캐럿을 해당 `libraries` 개체 리터럴에 배치합니다.
* 왼쪽 여백에 표시되는 전구 아이콘을 클릭합니다. **업데이트 확인** 위에 마우스를 올려 놓습니다.

LibMan이 설치되어 있는 버전보다 새로운 라이브러리 버전을 확인합니다. 다음과 같은 결과가 발생할 수 있습니다.

* 이미 가장 최신 버전이 설치되어 있는 경우 **No updates found** 메시지가 표시됩니다.
* 안정적인 최신 버전이 아직 설치되지 않았으면 해당 버전이 표시됩니다.

  ![업데이트 확인 컨텍스트 메뉴 옵션](_static/update-menu-option.png)

* 설치되어 있는 버전보다 새로운 시험판 버전을 사용할 수 있는 경우 시험판 버전이 표시됩니다.

이전 라이브러리 버전으로 다운그레이드하려면 *libman.json* 파일을 직접 편집합니다. 파일이 저장되면 LibMan [복원 작업](#restore-library-files)에서 다음을 수행합니다.

* 이전 버전에서 중복 파일을 제거합니다.
* 새 버전에서 새로운 파일과 업데이트된 파일을 추가합니다.

## <a name="additional-resources"></a>추가 자료

* <xref:client-side/libman/libman-cli>
* [LibMan GitHub 리포지토리](https://github.com/aspnet/LibraryManager)
