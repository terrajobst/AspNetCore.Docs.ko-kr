---
title: Blazor WebAssembly를 활용해 ASP.NET Core SignalR 사용
author: guardrex
description: Blazor WebAssembly를 활용해 ASP.NET Core SignalR을 사용하는 채팅 앱을 만듭니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/31/2020
no-loc:
- Blazor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: d3605c0823e9ec3ce34fb781da66a7470aa00622
ms.sourcegitcommit: 0e21d4f8111743bcb205a2ae0f8e57910c3e8c25
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77034146"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a>Blazor WebAssembly를 활용해 ASP.NET Core SignalR 사용

작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

이 자습서에서는 Blazor WebAssembly를 활용하여 SignalR을 사용하는 실시간 앱을 빌드하는 방법에 대한 기본 사항을 설명합니다. 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * Blazor WebAssembly 호스팅 앱 프로젝트 만들기
> * SignalR 클라이언트 라이브러리 추가
> * SignalR 허브 추가
> * SignalR 서비스 및 SignalR 허브에 대한 엔드포인트 추가
> * 채팅을 위한 Razor 구성 요소 코드 추가

이 모든 과정을 마치면 채팅 앱을 실행할 수 있습니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>사전 요구 사항

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a>호스팅된 Blazor WebAssembly 앱 프로젝트 만들기

[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) 템플릿을 설치합니다. [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) 패키지에는 미리 보기 버전이 포함되어 있으며 Blazor WebAssembly는 미리 보기로 제공됩니다. 명령 셸에서 다음 명령을 실행합니다.

```dotnetcli
dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.2.0-preview1.20073.1
```

선택한 도구의 지침을 따르세요.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 새 프로젝트를 만듭니다.

1. **Blazor 앱**을 선택하고 **다음**을 선택합니다.

1. **프로젝트 이름** 필드에 "BlazorSignalRApp"을 입력합니다. **위치** 항목이 올바른지 확인하거나 프로젝트의 위치를 제공합니다. **만들기**를 선택합니다.

1. **Blazor WebAssembly 앱** 템플릿을 선택합니다.

1. **고급**에서 **ASP.NET Core 호스팅** 확인란을 선택합니다.

1. **만들기**를 선택합니다.

> [!NOTE]
> Visual Studio를 새 버전으로 업그레이드했거나 새 버전을 설치했는데 Blazor WebAssembly 템플릿이 VS UI에 표시되지 않는 경우, 앞서 살펴본 `dotnet new` 명령을 사용하여 템플릿을 다시 설치합니다.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. 명령 셸에서 다음 명령을 실행합니다.

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. Visual Studio Code에서 앱의 프로젝트 폴더를 엽니다.

1. 앱을 빌드하고 디버그할 자산을 추가하기 위한 대화 상자가 나타나면 **예**를 선택합니다. Visual Studio Code는 생성된 *launch.json* 및 *tasks.json* 파일로 *.vscode* 폴더를 자동으로 추가합니다.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

1. 명령 셸에서 다음 명령을 실행합니다.

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. Mac용 Visual Studio에서 프로젝트 폴더로 이동하고 프로젝트의 솔루션 파일( *.sln*)을 열어 프로젝트를 엽니다.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

명령 셸에서 다음 명령을 실행합니다.

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a>SignalR 클라이언트 라이브러리 추가

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. **솔루션 탐색기**에서 **BlazorSignalRApp.Client** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.

1. **NuGet 패키지 관리** 대화 상자에서 **패키지 원본**이 *nuget.org*로 설정되어 있는지 확인합니다.

1. **찾아보기**를 선택한 상태에서 검색 상자에 "Microsoft.AspNetCore.SignalR.Client"를 입력합니다.

1. 검색 결과에서 `Microsoft.AspNetCore.SignalR.Client` 패키지를 선택하고 **설치**를 선택합니다.

1. **변경 내용 미리 보기** 대화 상자가 표시되면 **확인**을 선택합니다.

1. **라이선스 승인** 대화 상자가 나타나면 사용 조건에 동의하는 경우 **동의함**을 선택합니다.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

**통합 터미널**(도구 모음에서 **보기** > **터미널**)에서 다음 명령을 실행합니다.

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

1. **솔루션** 사이드바에서 **BlazorSignalRApp.Client** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.

1. **NuGet 패키지 관리** 대화 상자에서 원본 드롭다운이 *nuget.org*로 설정되어 있는지 확인합니다.

1. **찾아보기**를 선택한 상태에서 검색 상자에 "Microsoft.AspNetCore.SignalR.Client"를 입력합니다.

1. 검색 결과에서 `Microsoft.AspNetCore.SignalR.Client` 패키지 옆의 확인란을 선택하고 **패키지 추가**를 선택합니다.

1. **라이선스 승인** 대화 상자가 나타나면 사용 조건에 동의하는 경우 **동의함**을 선택합니다.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

명령 셸에서 다음 명령을 실행합니다.

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a>SignalR 허브 추가

**BlazorSignalRApp.Server** 프로젝트에서 *Hubs*(복수형) 폴더를 만들고 다음 `ChatHub` 클래스(*Hubs/ChatHub.cs*)를 추가합니다.

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-signalr-services-and-an-endpoint-for-the-signalr-hub"></a>SignalR 서비스 및 SignalR 허브에 대한 엔드포인트 추가

1. **BlazorSignalRApp.Server** 프로젝트에서 *Startup.cs* 파일을 엽니다.

1. `ChatHub` 클래스에 대한 네임스페이스를 파일의 맨 위에 추가합니다.

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. `Startup.ConfigureServices`에 SignalR 서비스를 추가합니다.

   ```csharp
   services.AddSignalR();
   ```

1. 기본 컨트롤러 경로와 클라이언트 쪽 대체에 대한 엔드포인트 간의 `Startup.Configure`에서 허브에 대한 엔드포인트를 추가합니다.

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet&highlight=4)]

## <a name="add-razor-component-code-for-chat"></a>채팅을 위한 Razor 구성 요소 코드 추가

1. **BlazorSignalRApp.Client** 프로젝트에서 *Pages/Index.razor* 파일을 엽니다.

1. 태그를 다음 코드로 바꿉니다.

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a>앱 실행

1. 선택한 도구의 지침을 따르세요.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. **솔루션 탐색기**에서 **BlazorSignalRApp.Server** 프로젝트를 선택합니다. **CTRL+F5** 키를 눌러 디버깅 없이 앱을 실행합니다.

1. 주소 표시줄에서 URL을 복사하고, 다른 브라우저 인스턴스 또는 탭을 열고, 주소 표시줄에 URL을 붙여넣습니다.

1. 브라우저 중 하나를 선택하고, 이름 및 메시지를 입력하고, **보내기** 단추를 선택합니다. 이름과 메시지는 두 페이지 모두에 즉시 표시됩니다.

   ![SignalR Blazor WebAssembly 샘플 앱은 오가는 메시지를 표시하는 두 개의 브라우저 창에서 열립니다.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   인용: *스타 트렉 6: 미지의 세계* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. 도구 모음에서 **디버그** > **디버그깅하지 않고 실행**을 선택합니다.

1. 주소 표시줄에서 URL을 복사하고, 다른 브라우저 인스턴스 또는 탭을 열고, 주소 표시줄에 URL을 붙여넣습니다.

1. 브라우저 중 하나를 선택하고, 이름 및 메시지를 입력하고, **보내기** 단추를 선택합니다. 이름과 메시지는 두 페이지 모두에 즉시 표시됩니다.

   ![SignalR Blazor WebAssembly 샘플 앱은 오가는 메시지를 표시하는 두 개의 브라우저 창에서 열립니다.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   인용: *스타 트렉 6: 미지의 세계* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Mac용 Visual Studio](#tab/visual-studio-mac)

1. **솔루션** 사이드바에서 **BlazorSignalRApp.Server** 프로젝트를 선택합니다. 메뉴에서 **실행** > **디버깅하지 않고 시작**을 선택합니다.

1. 주소 표시줄에서 URL을 복사하고, 다른 브라우저 인스턴스 또는 탭을 열고, 주소 표시줄에 URL을 붙여넣습니다.

1. 브라우저 중 하나를 선택하고, 이름 및 메시지를 입력하고, **보내기** 단추를 선택합니다. 이름과 메시지는 두 페이지 모두에 즉시 표시됩니다.

   ![SignalR Blazor WebAssembly 샘플 앱은 오가는 메시지를 표시하는 두 개의 브라우저 창에서 열립니다.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   인용: *스타 트렉 6: 미지의 세계* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

1. 명령 셸에서 다음 명령을 실행합니다.

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. 주소 표시줄에서 URL을 복사하고, 다른 브라우저 인스턴스 또는 탭을 열고, 주소 표시줄에 URL을 붙여넣습니다.

1. 브라우저 중 하나를 선택하고, 이름 및 메시지를 입력하고, **보내기** 단추를 선택합니다. 이름과 메시지는 두 페이지 모두에 즉시 표시됩니다.

   ![SignalR Blazor WebAssembly 샘플 앱은 오가는 메시지를 표시하는 두 개의 브라우저 창에서 열립니다.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   인용: *스타 트렉 6: 미지의 세계* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

## <a name="next-steps"></a>다음 단계

본 자습서에서는 다음 작업에 관한 방법을 학습했습니다.

> [!div class="checklist"]
> * Blazor WebAssembly 호스팅 앱 프로젝트 만들기
> * SignalR 클라이언트 라이브러리 추가
> * SignalR 허브 추가
> * SignalR 서비스 및 SignalR 허브에 대한 엔드포인트 추가
> * 채팅을 위한 Razor 구성 요소 코드 추가

Blazor 앱 빌드에 대한 자세한 내용은 Blazor 설명서를 참조하세요.

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a>추가 자료

* <xref:signalr/introduction>
