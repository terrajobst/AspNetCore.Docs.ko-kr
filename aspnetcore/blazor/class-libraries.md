---
title: ASP.NET Core Razor 구성 요소 클래스 라이브러리
author: guardrex
description: 외부 구성 요소 라이브러리의 구성 요소를 Blazor 앱에 포함하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/class-libraries
ms.openlocfilehash: f2cc57638922bd1f6ab036adb2ed37209d14c5b0
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218768"
---
# <a name="aspnet-core-razor-components-class-libraries"></a>ASP.NET Core Razor 구성 요소 클래스 라이브러리

작성자: [Simon Timms](https://github.com/stimms)

프로젝트 간에 [RCL](xref:razor-pages/ui-class)(Razor 클래스 라이브러리)의 구성 요소를 공유할 수 있습니다. 다음 위치에서 *Razor 구성 요소 클래스 라이브러리*를 포함할 수 있습니다.

* 솔루션의 다른 프로젝트
* NuGet 패키지
* 참조된 .NET 라이브러리

구성 요소가 일반적인 .NET 형식인 것처럼, RCL에서 제공하는 구성 요소는 일반적인 .NET 어셈블리입니다.

## <a name="create-an-rcl"></a>RCL 만들기

<xref:blazor/get-started> 문서의 지침에 따라 사용자 환경을 Blazor에 맞게 구성합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 새 프로젝트를 만듭니다.
1. **Razor 클래스 라이브러리**를 선택합니다. **새로 만들기**를 선택합니다.
1. **새 Razor 클래스 라이브러리 만들기** 대화 상자에서 **만들기**를 선택합니다.
1. **프로젝트 이름** 필드에 프로젝트 이름을 제공하거나 기본 프로젝트 이름을 수락합니다. 이 항목의 예제에서는 프로젝트 이름으로 `MyComponentLib1`을 사용합니다. **만들기**를 선택합니다.
1. 솔루션에 RCL을 추가합니다.
   1. 솔루션을 마우스 오른쪽 단추로 클릭합니다. **추가** > **기존 프로젝트**를 선택합니다.
   1. RCL의 프로젝트 파일로 이동합니다.
   1. RCL의 프로젝트 파일( *.csproj*)을 선택합니다.
1. 앱에서 RCL 참조를 추가합니다.
   1. 앱 프로젝트를 마우스 오른쪽 단추로 클릭합니다. **추가** > **참조**를 선택합니다.
   1. RCL 프로젝트를 선택합니다. **확인**을 선택합니다.

> [!NOTE]
> 템플릿에서 RCL을 생성할 때 **페이지 및 뷰 지원** 확인란을 선택한 경우 다음 내용을 포함하는 *_Imports.razor* 파일을 생성된 프로젝트 루트에 추가하여 Razor 구성 요소 작성을 사용하도록 설정합니다.
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> 생성된 프로젝트 루트에 파일을 수동으로 추가합니다.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

1. 명령 셸에서 **dotnet new** 명령을 통해 `razorclasslib`Razor 클래스 라이브러리[ 템플릿(](/dotnet/core/tools/dotnet-new))을 사용합니다. 다음 예제에서는 이름이 `MyComponentLib1`인 RCL을 만듭니다. 명령을 실행하면 `MyComponentLib1`을 포함하는 폴더가 자동으로 생성됩니다.

   ```dotnetcli
   dotnet new razorclasslib -o MyComponentLib1
   ```

   > [!NOTE]
   > 템플릿에서 RCL을 생성할 때 `-s|--support-pages-and-views` 스위치를 사용한 경우 다음 내용을 포함하는 *_Imports.razor* 파일을 생성된 프로젝트 루트에 추가하여 Razor 구성 요소 작성을 사용하도록 설정합니다.
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > 생성된 프로젝트 루트에 파일을 수동으로 추가합니다.

1. 기존 프로젝트에 라이브러리를 추가하려면 명령 셸에서 [dotnet add reference](/dotnet/core/tools/dotnet-add-reference) 명령을 사용합니다. 다음 예제에서는 앱에 RCL을 추가합니다. 라이브러리 경로를 사용하여 앱의 프로젝트 폴더에서 다음 명령을 실행합니다.

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a>라이브러리 구성 요소 사용

다른 프로젝트의 라이브러리에서 정의된 구성 요소를 사용하려면 다음 방법의 하나를 사용합니다.

* 네임스페이스를 포함하는 전체 형식 이름을 사용합니다.
* Razor [\@using](xref:mvc/views/razor#using) 지시문을 사용합니다. 개별 구성 요소는 이름으로 추가할 수 있습니다.

다음 예제에서 `MyComponentLib1`은 `SalesReport` 구성 요소가 포함된 구성 요소 라이브러리입니다.

네임스페이스를 포함하는 전체 형식 이름을 사용하여 `SalesReport` 구성 요소를 참조할 수 있습니다.

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<MyComponentLib1.SalesReport />
```

`@using` 지시문을 사용하여 라이브러리를 범위로 가져온 경우 구성 요소를 참조할 수도 있습니다.

```razor
@using MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<SalesReport />
```

최상위 `@using MyComponentLib1`_Import.razor*파일에* 지시문을 포함하여 전체 프로젝트에서 라이브러리 구성 요소를 사용할 수 있게 합니다. 임의 수준의 *_Import.razor* 파일에 지시문을 추가하여 폴더 내의 단일 페이지 또는 페이지 집합에 네임스페이스를 적용합니다.

## <a name="create-a-razor-components-class-library-with-static-assets"></a>정적 자산을 사용하여 Razor 구성 요소 클래스 라이브러리 만들기

RCL에는 정적 자산이 포함될 수 있습니다. 정적 자산은 라이브러리를 사용하는 모든 앱에서 제공됩니다. 자세한 내용은 <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>을 참조하세요.

## <a name="build-pack-and-ship-to-nuget"></a>빌드, 패키지 및 NuGet에 제공

구성 요소 라이브러리는 표준 .NET 라이브러리이기 때문에 패키지하여 NuGet에 제공하는 과정이 라이브러리를 패키지하여 NuGet에 제공하는 과정과 다르지 않습니다. 명령 셸에서 [dotnet pack](/dotnet/core/tools/dotnet-pack) 명령을 사용하여 패키지합니다.

```dotnetcli
dotnet pack
```

명령 셸에서 [dotnet nuget push](/dotnet/core/tools/dotnet-nuget-push) 명령을 사용하여 패키지를 NuGet에 업로드합니다.

## <a name="additional-resources"></a>추가 자료

* <xref:razor-pages/ui-class>
* [라이브러리에 XML 링커 구성 파일 추가](xref:host-and-deploy/blazor/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
