---
title: ASP.NET Core Blazor 시작 하기
author: guardrex
description: 선택한 도구를 사용 하 여 Blazor 앱을 빌드하여 Blazor를 시작 하세요.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
uid: blazor/get-started
ms.openlocfilehash: b1d0b1a99bac202567e44ae11986c57ab5891e43
ms.sourcegitcommit: e5a74f882c14eaa0e5639ff082355e130559ba83
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71168107"
---
# <a name="get-started-with-aspnet-core-blazor"></a>ASP.NET Core Blazor 시작 하기

작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor 시작 하기:

1. 최신 [.Net Core 3.0 미리 보기 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) 릴리스를 설치 합니다.

1. 명령 셸에서 다음 명령을 실행 하 여 Blazor 템플릿을 설치 합니다.

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.0.0-preview9.19457.4
   ```

1. 선택한 도구에 대 한 지침을 따르세요.

   # <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

   1 \. **ASP.NET 및 웹 개발** 워크 로드를 사용 하 여 최신 [Visual Studio preview](https://visualstudio.com/vs/preview) 를 설치 합니다.

   2 \. 새 프로젝트를 만듭니다.

   3\. **Blazor App**을 선택 합니다. **새로 만들기**를 선택합니다.

   4 \. **프로젝트 이름** 필드에 프로젝트 이름을 제공하거나 기본 프로젝트 이름을 수락합니다. **위치** 항목이 올바른지 확인 하거나 프로젝트의 위치를 제공 합니다. **만들기**를 선택합니다.

   5\. Blazor weasembomexperience 환경에 대해 **Blazor Weasembomapp** 템플릿을 선택 합니다. Blazor 서버 환경의 경우 **Blazor Server 앱** 템플릿을 선택 합니다. **만들기**를 선택합니다. 두 Blazor 호스팅 모델에 대 한 자세한 내용은 *Blazor Server* 및 *Blazor Weasembomommbmbsembmbsembmbsembse.* <xref:blazor/hosting-models>

   6\. **F5** 키를 눌러 앱을 실행합니다.

   > [!NOTE]
   > ASP.NET Core Blazor (Preview 6 이전 버전)의 이전 미리 보기 릴리스에 대해 Blazor Visual Studio 확장을 설치한 경우 확장을 제거할 수 있습니다. 이제 명령 셸에서 Blazor 템플릿을 설치 하면 Visual Studio에서 템플릿을 노출 하기에 충분 합니다.

   # <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

   1 \. [Visual Studio Code](https://code.visualstudio.com/)를 설치합니다.

   2 \. [ C# Visual Studio Code 확장에 대 한](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)최신 버전을 설치 합니다.

   3\. Blazor Weasembomexperience 환경의 경우 명령 셸에서 다음 명령을 실행 합니다.

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      Blazor 서버 환경의 경우 명령 셸에서 다음 명령을 실행 합니다.

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      두 Blazor 호스팅 모델에 대 한 자세한 내용은 *Blazor Server* 및 *Blazor Weasembomommbmbsembmbsembmbsembse.* <xref:blazor/hosting-models>

   4 \. Visual Studio Code에서 *WebApplication1* 폴더를 엽니다.

   5\. Blazor 서버 프로젝트의 경우 IDE는 프로젝트를 빌드 및 디버그 하기 위해 자산을 추가 하도록 요청 합니다. **예**를 선택합니다.

   6\. Blazor 서버 앱을 사용 하는 경우 Visual Studio Code 디버거를 사용 하 여 앱을 실행 합니다. Blazor weasembomapp을 사용 하는 경우 `dotnet run` 앱의 프로젝트 폴더에서를 실행 합니다.

   7 \. 브라우저에서 `https://localhost:5001`로 이동합니다.

   <!--

   # [Visual Studio for Mac](#tab/visual-studio-mac)

   1\. Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/). Switch the [Update channel to Preview](/visualstudio/mac/install-preview).

   2\. Select **File** > **New Solution** or **New Project**.

   3\. In the sidebar, select **.NET Core** > **App**.

   4\. For a Blazor Server experience, select the **Blazor Server App** template. For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.

   5\. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.

   6\. In the **Project Name** field, enter `WebApplication1`. Select **Create**.

   7\. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

   -->

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

   두 Blazor 호스팅 모델에 대 한 자세한 내용은 *Blazor Server* 및 *Blazor Weasembomommbmbsembmbsembmbsembse.* <xref:blazor/hosting-models>

   브라우저에서 `https://localhost:5001`로 이동합니다.

   ---

세로 막대의 탭에서 여러 페이지를 사용할 수 있습니다.

* 홈
* 카운터
* 데이터 페치

카운터 페이지에서 **Click me** 단추를 선택하여 페이지 새로 고침 없이 카운터를 증분합니다. 웹 페이지에서 카운터를 증가 시키려면 JavaScript를 작성 해야 하지만 Razor 구성 요소는을 사용 하 C#여 더 나은 접근 방식을 제공 합니다.

*Pages/Counter.razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

맨 위에 `/counter` 있는지시문`@page` 에 지정 된 대로 브라우저에서에 대 한 요청은 구성요소가해당콘텐츠를렌더링하도록합니다.`Counter` 구성 요소는 렌더링 트리의 메모리 내 표현으로 렌더링 되어 유연 하 고 효율적인 방식으로 UI를 업데이트 하는 데 사용할 수 있습니다.

**[클릭** ] 단추를 선택할 때마다:

* `onclick` 이벤트가 발생 합니다.
* `IncrementCount` 메서드가 호출됩니다.
* `currentCount` 가 증가 합니다.
* 구성 요소가 다시 렌더링 됩니다.

런타임은 새 콘텐츠를 이전 콘텐츠와 비교 하 고 변경 된 콘텐츠만 문서 개체 모델 (DOM)에 적용 합니다.

HTML 구문을 사용 하 여 구성 요소를 다른 구성 요소에 추가 합니다. 예를 들어 `Counter` `Index` 구성 요소에 `<Counter />` 요소를 추가 하 여 응용 프로그램의 홈페이지에 구성 요소를 추가 합니다.

*Pages/Index.razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

앱을 실행합니다. 홈 페이지에는 `Counter` 구성 요소에서 제공 하는 고유한 카운터가 있습니다.

구성 요소 매개 변수는 자식 구성 요소에 대 한 속성을 설정할 수 있도록 하는 특성 또는 [자식 콘텐츠](xref:blazor/components#child-content)를 사용 하 여 지정 됩니다. `Counter` 구성 요소에 매개 변수를 추가 하려면 `@code` 구성 요소의 블록을 업데이트 합니다.

* 특성`[Parameter]` 을 사용 하 여 `IncrementAmount` 에 대 한 public 속성을 추가 합니다.
* `currentCount` 값을 늘릴 때 `IncrementAmount`를 사용하도록 `IncrementCount` 메서드를 변경합니다.

*Pages/Counter.razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

특성을 `IncrementAmount` 사용 하 `Index` 여 구성 `<Counter>` 요소의 요소에를 지정 합니다.

*Pages/Index.razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

앱을 실행합니다. 구성 요소에는 클릭 단추를 선택할 때마다 10 씩 증가 하는 고유한 카운터가 있습니다. `Index` 의 `Counter` 구성 요소 ( `/counter` *카운터. razor*)는 계속 해 서 1 씩 증가 합니다.

## <a name="next-steps"></a>다음 단계

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>추가 자료

* <xref:signalr/introduction>
