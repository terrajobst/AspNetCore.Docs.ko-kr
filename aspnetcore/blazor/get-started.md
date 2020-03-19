---
title: ASP.NET Core Blazor 시작하기
author: guardrex
description: 선택한 도구로 Blazor 앱을 빌드하여 Blazor를 시작합니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/10/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 89c7529d2b8ec97db731f7c7268e19937c398115
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083247"
---
# <a name="get-started-with-aspnet-core-blazor"></a>ASP.NET Core Blazor 시작

작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor를 시작합니다.

1. [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)를 설치합니다.

1. 필요에 따라 [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) 템플릿을 설치합니다.
   * [.NET Core 3.1.102 이상(미리 보기) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)를 설치합니다.
   * 명령 셸에서 다음 명령을 실행합니다. [Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) 패키지에는 미리 보기 버전이 포함되어 있으며 Blazor WebAssembly는 미리 보기로 제공됩니다.

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview2.20160.5
   ```

   > [!NOTE]
   > 3\.2 미리 보기 2 Blazor WebAssembly 템플릿을 사용하려면 .NET Core SDK 버전 3.1.102 이상이 **필요**합니다. 명령 셸에서 `dotnet --version`을 실행하여 설치된 .NET Core SDK 버전을 확인합니다.

1. 선택한 도구의 지침을 따르세요.

   # <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

   1\. **ASP.NET 및 웹 개발** 워크로드를 사용하여 [Visual Studio 2019 버전 16.4 이상](https://visualstudio.microsoft.com/vs/preview/)을 설치합니다.

   2\. 새 프로젝트를 만듭니다.

   3\. **Blazor 앱**을 선택합니다. **새로 만들기**를 선택합니다.

   4\. **프로젝트 이름** 필드에 프로젝트 이름을 제공하거나 기본 프로젝트 이름을 수락합니다. **위치** 항목이 올바른지 확인하거나 프로젝트의 위치를 제공합니다. **만들기**를 선택합니다.

   5\. Blazor WebAssembly 환경의 경우 **Blazor WebAssembly 앱** 템플릿을 선택합니다. Blazor 서버 환경의 경우 **Blazor 서버 앱** 템플릿을 선택합니다. **만들기**를 선택합니다. 두 가지 Blazor 호스팅 모델인 ‘Blazor 서버’와 *Blazor WebAssembly*에 대한 자세한 내용은 <xref:blazor/hosting-models>를 참조하세요.  Blazor WebAssembly 템플릿이 없으면 이전 단계로 돌아가서 템플릿을 다시 설치합니다.

   6\. **Ctrl**+**F5**를 눌러 앱을 실행합니다.

   > [!NOTE]
   > ASP.NET Core Blazor의 이전 미리 보기 릴리스(Preview 6 또는 이전 버전)용 Blazor Visual Studio 확장을 설치한 경우 확장을 제거할 수 있습니다. 이제 명령 셸에서 Blazor 템플릿을 설치하기만 하면 Visual Studio에 템플릿을 표시할 수 있습니다.

   # <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

   1\. [Visual Studio Code](https://code.visualstudio.com/)를 설치합니다.

   2\. 최신 [Visual Studio Code용 C# 확장](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)을 설치합니다.

   3\. Blazor WebAssembly 환경의 경우 명령 셸에서 다음 명령을 실행합니다.

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      Blazor 서버 환경의 경우 명령 셸에서 다음 명령을 실행합니다.

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      두 가지 Blazor 호스팅 모델인 ‘Blazor 서버’와 *Blazor WebAssembly*에 대한 자세한 내용은 <xref:blazor/hosting-models>를 참조하세요. 

   4\. Visual Studio Code에서 *WebApplication1* 폴더를 엽니다.

   5\. Blazor 서버 프로젝트의 경우 IDE에서 프로젝트를 빌드 및 디버그하기 위한 자산을 추가하도록 요청합니다. **Yes**를 선택합니다.

   6\. Blazor 서버 앱을 사용하는 경우 Visual Studio Code 디버거를 사용하여 앱을 실행합니다. Blazor WebAssembly 앱을 사용하는 경우 앱의 프로젝트 폴더에서 `dotnet run`을 실행합니다.

   7\. 브라우저에서 `https://localhost:5001`로 이동합니다.

   # <a name="visual-studio-for-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

   1\. [Mac용 Visual Studio](https://visualstudio.microsoft.com/vs/mac/)를 설치합니다.

   2\. **파일** > **새 솔루션**을 선택하거나 **새 프로젝트**를 만듭니다.

   3\. 사이드바에서 **.NET Core** > **앱**을 선택합니다.

   4\. **Blazor 서버 앱** 템플릿을 선택합니다. 지금은 Mac용 Visual Studio에서 Blazor 서버 템플릿만 사용할 수 있습니다. Blazor WebAssembly 환경의 경우 **.NET Core CLI** 탭의 지침을 따르세요. Blazor 서버 템플릿을 선택한 후 **다음**을 선택합니다. 두 가지 Blazor 호스팅 모델인 ‘Blazor 서버’와 *Blazor WebAssembly*에 대한 자세한 내용은 <xref:blazor/hosting-models>를 참조하세요. 

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   5\. **대상 프레임워크**를 **.NET Core 3.1**로 설정하고 **다음**을 선택합니다.

   6\. **프로젝트 이름** 필드에서 앱 이름을 `WebApplication1`로 지정합니다. **만들기**를 선택합니다.

   7\. **실행** > **디버깅하지 않고 실행**을 선택하여 ‘디버거 없이’ 앱을 실행합니다.  ‘디버거로’ 앱을 실행하려면 **디버깅 시작**을 사용하여 앱을 실행합니다. 

   개발 인증서를 신뢰하라는 메시지가 표시되면 인증서를 신뢰하고 계속합니다.

   # <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

   Blazor WebAssembly 환경의 경우 명령 셸에서 다음 명령을 실행합니다.

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Blazor 서버 환경의 경우 명령 셸에서 다음 명령을 실행합니다.

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   두 가지 Blazor 호스팅 모델인 ‘Blazor 서버’와 *Blazor WebAssembly*에 대한 자세한 내용은 <xref:blazor/hosting-models>를 참조하세요. 

   브라우저에서 `https://localhost:5001`로 이동합니다.

   ---

사이드바의 탭에서 여러 페이지를 사용할 수 있습니다.

* Home
* 카운터
* 데이터 가져오기

Counter 페이지에서 **Click me** 단추를 선택하여 페이지 새로 고침 없이 카운터를 증가시킵니다. 웹 페이지에서 카운터를 증가하려면 일반적으로 JavaScript를 작성해야 하지만, Blazor에서는 C#을 사용할 수 있습니다.

*Pages/Counter.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

맨 위에 있는 `@page` 지시문에 지정된 대로 브라우저에서 `/counter`를 요청하면 `Counter` 구성 요소가 해당 콘텐츠를 렌더링합니다. 구성 요소는 유연하고 효율적인 방법으로 UI를 업데이트하는 데 사용할 수 있는 렌더링 트리의 메모리 내 표시로 렌더링됩니다.

**Click me** 단추를 선택할 때마다 다음 작업이 수행됩니다.

* `onclick` 이벤트가 발생합니다.
* `IncrementCount` 메서드가 호출됩니다.
* `currentCount`가 증가합니다.
* 구성 요소가 다시 렌더링됩니다.

런타임은 이전 콘텐츠와 새 콘텐츠를 비교하고 변경된 콘텐츠만 DOM(문서 개체 모델)에 적용합니다.

HTML 구문을 사용하여 구성 요소를 다른 구성 요소에 추가합니다. 예를 들어 `Index` 구성 요소에 `<Counter />` 요소를 추가하여 앱의 홈페이지에 `Counter` 구성 요소를 추가합니다.

*Pages/Index.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

앱을 실행합니다. 홈페이지에는 `Counter` 구성 요소가 제공하는 고유한 카운터가 있습니다.

구성 요소 매개 변수는 자식 구성 요소에 속성을 설정하게 해주는 특성 또는 [자식 콘텐츠](xref:blazor/components#child-content)를 사용하여 지정합니다. `Counter` 구성 요소에 매개 변수를 추가하려면 구성 요소의 `@code` 블록을 업데이트합니다.

* `[Parameter]` 특성을 사용하여 `IncrementAmount`의 공용 속성을 추가합니다.
* `currentCount` 값을 증가시킬 때 `IncrementAmount`를 사용하도록 `IncrementCount` 메서드를 변경합니다.

*Pages/Counter.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

특성을 사용하여 `Index` 구성 요소의 `<Counter>` 요소에 `IncrementAmount`를 지정합니다.

*Pages/Index.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

앱을 실행합니다. `Index` 구성 요소에는 **Click me** 단추를 선택할 때마다 10씩 증가하는 고유한 카운터가 있습니다. `/counter`의 `Counter` 구성 요소(*Counter.razor*)는 계속 1씩 증가합니다.

## <a name="next-steps"></a>다음 단계

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>추가 자료

* <xref:blazor/templates>
* <xref:signalr/introduction>
