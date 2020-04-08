---
title: dotnet aspnet-codegenerator 명령
author: rick-anderson
description: dotnet aspnet codegenerator 명령은 ASP.NET Core 프로젝트를 스캐폴딩합니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/04/2019
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: 1043a578f66d5bb57f4a81e9fe21afa5e3c37cb8
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78649857"
---
# <a name="dotnet-aspnet-codegenerator"></a>dotnet aspnet-codegenerator

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

`dotnet aspnet-codegenerator` - ASP.NET Core 스캐폴딩 엔진을 실행합니다. `dotnet aspnet-codegenerator`는 명령줄에서 스캐폴딩하는 경우에만 필요하며, Visual Studio에서 스캐폴딩을 사용하는 경우에는 필요하지 않습니다.

이 문서는 [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) 이상에 적용됩니다.

## <a name="installing-aspnet-codegenerator"></a>aspnet-codegenerator 설치

`dotnet-aspnet-codegenerator`는 설치가 필요한 [전역 도구](/dotnet/core/tools/global-tools)입니다. 다음 명령은 `dotnet-aspnet-codegenerator` 도구의 안정적인 최신 버전을 설치합니다.

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

다음 명령은 `dotnet-aspnet-codegenerator`를 설치된 .NET Core SDK에서 사용 가능한 안정적인 최신 버전으로 업데이트합니다.

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a>개요

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a>설명

`dotnet aspnet-codegenerator` 전역 명령은 ASP.NET Core 코드 생성기 및 스캐폴딩 엔진을 실행합니다.

## <a name="arguments"></a>인수

`generator`

실행할 코드 생성기입니다. 다음 생성기를 사용할 수 있습니다.

| Generator | 작업 |
| ----------------- | ------------ | 
| area      | [영역 스캐폴딩](/aspnet/core/mvc/controllers/areas) |
  컨트롤러| [컨트롤러 스캐폴딩](/aspnet/core/tutorials/first-mvc-app/adding-model) |
  ID  | [Identity 스캐폴딩](/aspnet/core/security/authentication/scaffold-identity) |
  razorpage | [Razor 페이지 스캐폴딩](/aspnet/core/tutorials/razor-pages/model) |
  보기      | [보기 스캐폴딩](/aspnet/core/mvc/views/overview) |

## <a name="options"></a>옵션

`-n|--nuget-package-dir`

NuGet 패키지 디렉터리를 지정합니다.

`-c|--configuration {Debug|Release}`

빌드 구성을 정의합니다. 기본값은 `Debug`입니다.

`-tfm|--target-framework`

사용할 대상 [프레임워크](/dotnet/standard/frameworks)입니다. 예를 들어 `net46`과 같은 형식입니다.

`-b|--build-base-path`

빌드 기본 경로입니다.

`-h|--help`

명령에 대한 간단한 도움말을 출력합니다.

`--no-build`

실행하기 전에 프로젝트를 빌드하지 않습니다. 또한 `--no-restore` 플래그를 암시적으로 설정합니다.

`-p|--project <PATH>`

실행할 프로젝트 파일의 경로를 지정합니다(폴더 이름 또는 전체 경로). 지정하지 않으면 현재 디렉터리로 기본 설정됩니다.

## <a name="generator-options"></a>생성기 옵션

다음 섹션에서는 지원되는 생성기에 사용 가능한 옵션에서 설명합니다.

* 영역
* 컨트롤러
* ID  
* Razorpage
* 보기

<a name="area"></a>

### <a name="area-options"></a>Area 옵션

이 도구는 컨트롤러와 보기가 포함된 ASP.NET Core 웹 프로젝트에 사용할 수 있습니다. Razor Pages 앱에는 사용할 수 없습니다.

사용법: `dotnet aspnet-codegenerator area AreaNameToGenerate`

앞의 명령은 다음 폴더를 생성합니다.

* *영역*
  * *AreaNameToGenerate*
    * *컨트롤러*
    * *Data*
    * *Models*
    * *뷰*

<a name="ctl"></a>

### <a name="controller-options"></a>Controller 옵션

다음 표는 `aspnet-codegenerator` `controller` 및 `razorpage`의 옵션 목록을 보여줍니다.

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

다음 표는 `aspnet-codegenerator controller`에 고유한 옵션 목록을 보여줍니다.

| 옵션               | 설명|
| ----------------- | ------------ |
| --controllerName 또는 -name | 컨트롤러의 이름입니다. |
| --useAsyncActions 또는 -async | 비동기 컨트롤러 작업을 생성합니다. |
| --noViews 또는 -nv | 보기를 생성하지 **않습니다**. |
| --restWithNoViews 또는 -api  | REST 스타일 API를 사용하여 컨트롤러를 생성합니다. `noViews` 옵션이 지정된 것으로 간주되며 모든 보기 관련된 옵션이 무시됩니다. |
| --readWriteActions 또는 -actions | 모델 없이 읽기/쓰기 동작이 포함된 컨트롤러를 생성합니다. |

`-h` 스위치를 사용하여 `aspnet-codegenerator controller` 명령에 대한 도움말을 확인합니다.

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

[의 예제는 ](/aspnet/core/tutorials/razor-pages/model)동영상 모델 스캐폴드`dotnet aspnet-codegenerator controller`를 참조하세요.

### <a name="razorpage"></a>Razorpage

<a name="rp"></a>

Razor Pages는 새 페이지 이름 및 사용할 템플릿을 지정하여 개별적으로 스캐폴딩할 수 있습니다. 지원되는 템플릿은 다음과 같습니다.

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

예를 들어 다음 명령은 Edit 템플릿을 사용하여 *MyEdit.cshtml* 및 *MyEdit.cshtml.cs*를 생성합니다.

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

일반적으로 템플릿 및 생성된 파일 이름은 지정되지 않으며 다음 템플릿이 생성됩니다.

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

다음 표는 `aspnet-codegenerator` `razorpage` 및 `controller`의 옵션 목록을 보여줍니다.

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

다음 표는 `aspnet-codegenerator razorpage`에 고유한 옵션 목록을 보여줍니다.

| 옵션               | 설명|
| ----------------- | ------------ |
|   --namespaceName 또는 -namespace | 생성된 PageModel에 사용할 네임스페이스의 이름입니다. |
| --partialView 또는 -partial | 부분 보기를 생성합니다. 이 옵션이 지정되면 레이아웃 옵션 -l 및 -udl은 무시됩니다. |
| --noPageModel 또는 -npm | 빈 템플릿에 대한 PageModel 클래스를 생성하지 않도록 전환합니다. |

`-h` 스위치를 사용하여 `aspnet-codegenerator razorpage` 명령에 대한 도움말을 확인합니다.

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

[의 예제는 ](/aspnet/core/tutorials/razor-pages/model)동영상 모델 스캐폴드`dotnet aspnet-codegenerator razorpage`를 참조하세요.

### <a name="identity"></a>ID

[Identity 스캐폴딩](/aspnet/core/security/authentication/scaffold-identity) 참조
