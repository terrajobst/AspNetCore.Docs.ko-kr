---
title: ASP.NET Core Razor 구성 요소 클래스 라이브러리
author: guardrex
description: 외부 구성 요소 라이브러리의 Blazor apps에 구성 요소를 포함 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2019
uid: blazor/class-libraries
ms.openlocfilehash: 91c4ffb29649c162fc15e5cb70dc60144b53afea
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080658"
---
# <a name="aspnet-core-razor-components-class-libraries"></a>ASP.NET Core Razor 구성 요소 클래스 라이브러리

[Simon](https://github.com/stimms)

구성 요소는 프로젝트 간에 [RCL (Razor 클래스 라이브러리)](xref:razor-pages/ui-class) 에서 공유할 수 있습니다. *Razor 구성 요소 클래스 라이브러리* 는 다음에서 포함할 수 있습니다.

* 솔루션의 다른 프로젝트
* NuGet 패키지입니다.
* 참조 된 .NET 라이브러리입니다.

구성 요소가 일반적인 .NET 형식인 것 처럼 RCL에서 제공 하는 구성 요소는 일반적인 .NET 어셈블리입니다.

## <a name="create-an-rcl"></a>RCL 만들기

Blazor에 대 한 환경을 <xref:blazor/get-started> 구성 하려면 문서의 지침을 따르세요.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 새 프로젝트를 만듭니다.
1. **Razor 클래스 라이브러리**를 선택 합니다. **다음**을 선택합니다.
1. **새 Razor 클래스 라이브러리 만들기** 대화 상자에서 **만들기**를 선택 합니다.
1. **프로젝트 이름** 필드에 프로젝트 이름을 제공하거나 기본 프로젝트 이름을 수락합니다. 이 항목의 예제에서는 프로젝트 이름을 `MyComponentLib1`사용 합니다. **만들기**를 선택합니다.
1. 솔루션에 RCL을 추가 합니다.
   1. 솔루션을 마우스 오른쪽 단추로 클릭 합니다. **기존 프로젝트** **추가** > 를 선택 합니다.
   1. RCL의 프로젝트 파일로 이동 합니다.
   1. RCL의 프로젝트 파일 ( *.csproj*)을 선택 합니다.
1. 앱에서 RCL 참조를 추가 합니다.
   1. 앱 프로젝트를 마우스 오른쪽 단추로 클릭 합니다. **참조** **추가** > 를 선택 합니다.
   1. RCL 프로젝트를 선택 합니다. **확인**을 선택합니다.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

1. 명령 셸에서 [dotnet new](/dotnet/core/tools/dotnet-new) 명령과 함께 **Razor 클래스 라이브러리** 템플릿 (`razorclasslib`)을 사용 합니다. 다음 예에서는 라는 `MyComponentLib1`rcl을 만듭니다. 명령이 실행 될 때 `MyComponentLib1` 가 포함 된 폴더가 자동으로 생성 됩니다.

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

1. 기존 프로젝트에 라이브러리를 추가 하려면 명령 셸에서 [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) 명령을 사용 합니다. 다음 예제에서는 RCL이 앱에 추가 됩니다. 라이브러리에 대 한 경로를 사용 하 여 앱의 프로젝트 폴더에서 다음 명령을 실행 합니다.

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a>라이브러리 구성 요소 사용

다른 프로젝트의 라이브러리에 정의 된 구성 요소를 사용 하려면 다음 방법 중 하나를 사용 합니다.

* 전체 형식 이름에 네임 스페이스를 사용 합니다.
* Razor의 [ \@using](xref:mvc/views/razor#using) 지시문을 사용 합니다. 개별 구성 요소는 이름으로 추가할 수 있습니다.

다음 예제 `MyComponentLib1` 에서은 `SalesReport` 구성 요소를 포함 하는 구성 요소 라이브러리입니다.

네임 `SalesReport` 스페이스의 전체 형식 이름을 사용 하 여 구성 요소를 참조할 수 있습니다.

```cshtml
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

`@using` 지시문을 사용 하 여 라이브러리를 범위로 가져오는 경우 구성 요소를 참조할 수도 있습니다.

```cshtml
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

최상위 수준 *가져오기. razor* 파일에 지시문을포함하여전체프로젝트에서라이브러리의구성요소를사용할수있도록합니다.`@using MyComponentLib1` 모든 수준에서 *_Import. razor* 파일에 지시문을 추가 하 여 폴더 내의 단일 페이지 또는 페이지 집합에 네임 스페이스를 적용 합니다.

## <a name="build-pack-and-ship-to-nuget"></a>빌드, 팩 및 NuGet에 제공

구성 요소 라이브러리는 표준 .NET 라이브러리 이기 때문에 NuGet으로 패키징 및 전달 하는 것은 nuget에 라이브러리를 패키징 및 전달 하는 것과 다르지 않습니다. 패키지는 명령 셸에서 [dotnet pack](/dotnet/core/tools/dotnet-pack) 명령을 사용 하 여 수행 됩니다.

```dotnetcli
dotnet pack
```

명령 셸에서 [dotnet nuget publish](/dotnet/core/tools/dotnet-nuget-push) 명령을 사용 하 여 NuGet에 패키지를 업로드 합니다.

```dotnetcli
dotnet nuget publish
```

## <a name="create-a-razor-components-class-library-with-static-assets"></a>정적 자산을 사용 하 여 Razor 구성 요소 클래스 라이브러리 만들기

RCL에는 정적 자산이 포함 될 수 있습니다. 정적 자산은 라이브러리를 사용 하는 모든 앱에서 사용할 수 있습니다. 자세한 내용은 <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>을 참조하세요.

## <a name="additional-resources"></a>추가 자료

* <xref:razor-pages/ui-class>
