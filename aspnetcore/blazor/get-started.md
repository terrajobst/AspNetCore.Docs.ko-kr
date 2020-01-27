---
title: ASP.NET Core Blazor 시작하기
author: guardrex
description: 선택한 도구를 사용 하 여 Blazor 앱을 빌드하여 Blazor를 시작 합니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 642881b5400a70a99f6e7e262d2a2f1038389ce7
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76726853"
---
# <a name="get-started-with-aspnet-core-opno-locblazor"></a>ASP.NET Core Blazor 시작하기

작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor시작:

1. [.Net Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)를 설치 합니다.

1. 필요에 따라 [Blazor Weasembomomtemplate](xref:blazor/hosting-models#blazor-webassembly) 을 설치 합니다.
   * [.Net Core 3.1 이상 (미리 보기) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)를 설치 합니다.
   * 명령 셸에서 다음 명령을 실행 합니다. [AspNetCoreBlazor입니다. 템플릿](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) 패키지에는 미리 보기 버전이 있지만 Blazor Weasembmbveris는 미리 보기로 제공 됩니다.

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview4.19579.2
   ```

1. 선택한 도구에 대 한 지침을 따르세요.

   # <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

   1 \. **ASP.NET 및 웹 개발** 워크 로드를 사용 하 여 [Visual Studio 2019 버전 16.4](https://visualstudio.microsoft.com/vs/preview/) 이상을 설치 합니다.

   2 \. 새 프로젝트를 만듭니다.

   3\. **Blazor 앱**을 선택 합니다. **새로 만들기**를 선택합니다.

   4 \. **프로젝트 이름** 필드에 프로젝트 이름을 제공하거나 기본 프로젝트 이름을 수락합니다. **위치** 항목이 올바른지 확인 하거나 프로젝트의 위치를 제공 합니다. **만들기**를 선택합니다.

   5\. Blazor weasembomexperience의 경우 **Blazor Weasembomapp** 템플릿을 선택 합니다. Blazor 서버 환경에 대해 **Blazor Server 앱** 템플릿을 선택 합니다. **만들기**를 선택합니다. 두 Blazor 호스팅 모델에 대 한 자세한 내용은 *Blazor Server* 및 *Blazor* 를 <xref:blazor/hosting-models>참조 하세요.

   6\. **Ctrl**+**F5**를 눌러 앱을 실행합니다.

   > [!NOTE]
   > ASP.NET Core Blazor (Preview 6 이전 버전)의 이전 미리 보기 릴리스에 대 한 Blazor Visual Studio 확장을 설치한 경우 확장을 제거할 수 있습니다. 이제 명령 셸에서 Blazor 템플릿을 설치 하면 Visual Studio에서 템플릿을 노출 하기에 충분 합니다.

   # <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

   1 \. [Visual Studio Code](https://code.visualstudio.com/)를 설치합니다.

   2 \. [ C# Visual Studio Code 확장에 대 한](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)최신 버전을 설치 합니다.

   3\. Blazor Weasembomexperience의 경우 명령 셸에서 다음 명령을 실행 합니다.

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      Blazor 서버 환경의 경우 명령 셸에서 다음 명령을 실행 합니다.

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      두 Blazor 호스팅 모델에 대 한 자세한 내용은 *Blazor Server* 및 *Blazor* 를 <xref:blazor/hosting-models>참조 하세요.

   4 \. Visual Studio Code에서 *WebApplication1* 폴더를 엽니다.

   5\. Blazor Server 프로젝트의 경우 IDE는 프로젝트를 빌드 및 디버그 하기 위해 자산을 추가 하도록 요청 합니다. **Yes**를 선택합니다.

   6\. Blazor Server 앱을 사용 하는 경우 Visual Studio Code 디버거를 사용 하 여 앱을 실행 합니다. Blazor Weasembomapp을 사용 하는 경우 앱의 프로젝트 폴더에서 `dotnet run`를 실행 합니다.

   7 \. 브라우저에서 `https://localhost:5001`로 이동합니다.

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

   1 \. [Mac용 Visual Studio](https://visualstudio.microsoft.com/vs/mac/)를 설치 합니다.

   2 \. **파일** > **새 솔루션** 을 선택 하거나 **새 프로젝트**를 만듭니다.

   3\. 사이드바에서 **.Net Core** > **앱**을 선택 합니다.

   4 \. **Blazor Server 앱** 템플릿을 선택 합니다. 현재 Mac용 Visual Studio에서는 Blazor 서버 템플릿만 사용할 수 있습니다. Blazor Weasembomexperience의 경우 **.NET Core CLI** 탭의 지침을 따르세요. Blazor 서버 템플릿을 선택한 후 **다음**을 선택 합니다. 두 Blazor 호스팅 모델에 대 한 자세한 내용은 *Blazor Server* 및 *Blazor* 를 <xref:blazor/hosting-models>참조 하세요.

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   5\. **대상 프레임 워크** 를 **.net Core 3.1** 로 설정 하 고 **다음**을 선택 합니다.

   6\. **프로젝트 이름** 필드에서 앱 이름을 `WebApplication1`로 합니다. **만들기**를 선택합니다.

   7 \. **디버깅 하지 않고** 실행 > 실행을 선택 하 여 *디버거 없이*앱 **을 실행 합니다** . **디버깅 시작** 을 사용 하 여 앱을 실행 하 고 *디버거를 사용*하 여 앱을 실행 합니다.

   개발 인증서를 신뢰 하 라는 메시지가 표시 되 면 인증서를 신뢰 하 고 계속 합니다.

   # <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

   Blazor Weasembomexperience의 경우 명령 셸에서 다음 명령을 실행 합니다.

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Blazor 서버 환경의 경우 명령 셸에서 다음 명령을 실행 합니다.

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   두 Blazor 호스팅 모델에 대 한 자세한 내용은 *Blazor Server* 및 *Blazor* 를 <xref:blazor/hosting-models>참조 하세요.

   브라우저에서 `https://localhost:5001`로 이동합니다.

   ---

세로 막대의 탭에서 여러 페이지를 사용할 수 있습니다.

* 홈
* 카운터
* 데이터 페치

카운터 페이지에서 **Click me** 단추를 선택하여 페이지 새로 고침 없이 카운터를 증분합니다. 웹 페이지에서 카운터를 증가 시키려면 JavaScript를 작성 해야 하지만를 사용할 C#수 Blazor.

*Pages/Counter.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

맨 위에 있는 `@page` 지시문에 지정 된 대로 브라우저의 `/counter`에 대 한 요청은 `Counter` 구성 요소가 해당 내용을 렌더링 합니다. 구성 요소는 렌더링 트리의 메모리 내 표현으로 렌더링 되어 유연 하 고 효율적인 방식으로 UI를 업데이트 하는 데 사용할 수 있습니다.

**[클릭** ] 단추를 선택할 때마다:

* `onclick` 이벤트가 발생 합니다.
* `IncrementCount` 메서드가 호출됩니다.
* `currentCount` 증가 합니다.
* 구성 요소가 다시 렌더링 됩니다.

런타임은 새 콘텐츠를 이전 콘텐츠와 비교 하 고 변경 된 콘텐츠만 문서 개체 모델 (DOM)에 적용 합니다.

HTML 구문을 사용 하 여 구성 요소를 다른 구성 요소에 추가 합니다. 예를 들어 `Index` 구성 요소에 `<Counter />` 요소를 추가 하 여 앱의 홈페이지에 `Counter` 구성 요소를 추가 합니다.

*Pages/Index.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

앱을 실행합니다. 홈페이지에는 `Counter` 구성 요소에서 제공 하는 고유한 카운터가 있습니다.

구성 요소 매개 변수는 자식 구성 요소에 대 한 속성을 설정할 수 있도록 하는 특성 또는 [자식 콘텐츠](xref:blazor/components#child-content)를 사용 하 여 지정 됩니다. `Counter` 구성 요소에 매개 변수를 추가 하려면 구성 요소의 `@code` 블록을 업데이트 합니다.

* `[Parameter]` 특성을 사용 하 여 `IncrementAmount`에 대 한 public 속성을 추가 합니다.
* `currentCount` 값을 늘릴 때 `IncrementAmount`를 사용하도록 `IncrementCount` 메서드를 변경합니다.

*Pages/Counter.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

특성을 사용 하 여 `Index` 구성 요소의 `<Counter>` 요소에 `IncrementAmount`를 지정 합니다.

*Pages/Index.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

앱을 실행합니다. `Index` 구성 요소에는 **클릭** 단추를 선택할 때마다 10 씩 증가 하는 고유한 카운터가 있습니다. `/counter`의 `Counter` 구성 요소 ( *. razor*)는 1 씩 계속 증가 합니다.

## <a name="next-steps"></a>다음 단계

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>추가 자료

* <xref:blazor/templates>
* <xref:signalr/introduction>
