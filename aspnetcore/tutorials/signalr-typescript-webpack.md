---
title: TypeScript 및 WebPack과 함께 ASP.NET Core SignalR 사용
author: ssougnez
description: 이 자습서에서는 해당 클라이언트가 TypeScript로 작성된 ASP.NET Core SignalR 웹앱을 번들링 및 빌드하도록 WebPack을 구성합니다.
ms.author: bradyg
ms.custom: mvc
ms.date: 04/23/2019
uid: tutorials/signalr-typescript-webpack
ms.openlocfilehash: 8bebdd9102f93d2b2a8bf142db1053def9d001a0
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64884608"
---
# <a name="use-aspnet-core-signalr-with-typescript-and-webpack"></a>TypeScript 및 WebPack과 함께 ASP.NET Core SignalR 사용

작성자: [Sébastien Sougnez](https://twitter.com/ssougnez) 및 [Scott Addie](https://twitter.com/Scott_Addie)

[WebPack](https://webpack.js.org/)을 사용하여 개발자가 웹앱의 클라이언트 쪽 리소스를 번들링 및 빌드할 수 있습니다. 본 자습서에서는 클라이언트가 [TypeScript](https://www.typescriptlang.org/)로 작성된 ASP.NET Core SignalR 웹앱에서 WebPack을 사용하는 방법을 보여줍니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * ASP.NET Core SignalR 앱 스캐폴드
> * SignalR TypeScript 클라이언트 구성
> * WebPack을 사용하여 빌드 파이프라인 구성
> * SignalR 서버 구성
> * 클라이언트 및 서버 간 통신 활성화

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>전제 조건

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2017 버전 15.9 이상](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017)(**ASP.NET 및 웹 개발** 워크로드 필요)
* [.NET Core SDK 2.2 이상](https://www.microsoft.com/net/download/all)
* [Node.js](https://www.npmjs.com/) ([npm](https://nodejs.org/) 포함)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [.NET Core SDK 2.2 이상](https://www.microsoft.com/net/download/all)
* [Visual Studio Code용 C# 버전 1.17.1 이상](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [Node.js](https://www.npmjs.com/) ([npm](https://nodejs.org/) 포함)

---

## <a name="create-the-aspnet-core-web-app"></a>ASP.NET Core 웹앱 만들기

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

*PATH* 환경 변수에서 npm을 찾도록 Visual Studio를 구성합니다. 기본적으로 Visual Studio는 설치 디렉터리에 있는 npm의 버전을 사용합니다. Visual Studio에서 다음 지침을 따릅니다.

1. **도구** > **옵션** > **프로젝트 및 솔루션** > **웹 패키지 관리** > **외부 웹 도구**로 이동합니다.
1. 목록에서 *$(PATH)* 항목을 선택합니다. 위쪽 화살표를 클릭하여 이 항목을 목록의 두 번째 위치로 이동합니다.

    ![Visual Studio 구성](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

Visual Studio 구성이 완료되었습니다. 이제 프로젝트를 만들어야 합니다.

1. **파일** > **새로 만들기** > **프로젝트** 메뉴 옵션을 사용하고 **ASP.NET Core 웹 애플리케이션** 템플릿을 선택합니다.
1. 프로젝트의 이름을 *SignalRWebPack*으로 지정하고 **확인**을 선택합니다.
1. 대상 프레임워크 드롭다운에서 *.NET Core*를 선택하고, 프레임워크 선택기 드롭다운에서 *ASP.NET Core 2.2*를 선택합니다. **빈** 템플릿을 선택하고, **확인**을 선택합니다.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

**통합 터미널**에서 다음 명령을 실행합니다.

```console
dotnet new web -o SignalRWebPack
```

.NET Core를 대상으로 하는 빈 ASP.NET Core 웹앱이 *SignalRWebPack* 디렉터리에 생성됩니다.

---

## <a name="configure-webpack-and-typescript"></a>WebPack 및 TypeScript 구성

다음 단계에서는 TypeScript에서 JavaScript로 변환 및 클라이언트 쪽 리소스의 번들링을 구성합니다.

1. 프로젝트 루트에서 다음 명령을 실행하여 *package.json* 파일을 생성합니다.

    ```console
    npm init -y
    ```

1. 강조 표시된 속성을 *package.json* 파일에 추가합니다.

    [!code-json[package.json](signalr-typescript-webpack/sample/snippets/package1.json?highlight=4)]

    `private` 속성을 `true`로 설정하면 다음 단계에서 패키지 설치 경고가 나타나지 않습니다.

1. 필요한 npm 패키지를 설치합니다. 프로젝트 루트에서 다음 명령을 실행합니다.

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    참고할 몇몇 명령 세부 정보:

    * 버전 번호는 각 패키지 이름에 대해 `@` 부호 뒤에 옵니다. npm은 해당 특정 패키지 버전을 설치합니다.
    * `-E` 옵션은 [유의적 버전](https://semver.org/) 범위 연산자를 *package.json*에 쓰는 npm의 기본 동작을 비활성화합니다. 예를 들어 `"webpack": "^4.29.3"` 대신 `"webpack": "4.29.3"`을 사용합니다. 이 옵션은 최신 패키지 버전으로 의도하지 않은 업그레이드를 방지합니다.

    자세한 내용은 [npm-install](https://docs.npmjs.com/cli/install) 문서를 참조하세요.

1. *package.json* 파일의 `scripts` 속성을 다음 코드 조각으로 바꿉니다.

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    스크립트에 대한 간략한 설명:

    * `build`: 개발 모드에서 클라이언트 쪽 리소스를 번들링하고 파일 변경을 감시합니다. 파일 감시자는 프로젝트 파일이 변경될 때마다 번들이 다시 생성되도록 합니다. `mode` 옵션은 프로덕션 최적화(예: 트리 셰이킹 및 축소)를 비활성화합니다. 개발 시에는 `build`만 사용합니다.
    * `release`: 프로덕션 모드에서 클라이언트 쪽 리소스를 번들링합니다.
    * `publish`: 프로덕션 모드에서 `release` 스크립트를 실행하여 클라이언트 쪽 리소스를 번들링합니다. 이는 .NET Core CLI의 [publish](/dotnet/core/tools/dotnet-publish) 명령을 호출하여 앱을 게시합니다.

1. 프로젝트 루트에 다음 내용을 포함한 *webpack.config.js*라는 파일을 생성합니다.

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/webpack.config.js)]

    앞의 파일은 WebPack 컴파일을 구성합니다. 참고할 일부 구성 세부 정보:

    * `output` 속성은 *dist*의 기본값을 재정의합니다. 번들은 *wwwroot* 디렉터리에 대신 내보내집니다.
    * `resolve.extensions` 배열은 SignalR 클라이언트 JavaScript를 가져오기 위한 *.js*를 포함합니다.

1. 프로젝트 루트에 새 *src* 디렉터리를 생성합니다. 그 목적은 프로젝트의 클라이언트 쪽 자산을 저장하는 것입니다.

1. 다음 내용을 포함한 *src/index.html*을 생성합니다.

    [!code-html[index.html](signalr-typescript-webpack/sample/src/index.html)]

    앞의 HTML은 홈 페이지의 상용구 마크업을 정의합니다.

1. 새 *src/css* 디렉터리를 생성합니다. 그 목적은 프로젝트의 *.css* 파일을 저장하는 것입니다.

1. 다음 내용을 포함한 *src/css/main.css*를 생성합니다.

    [!code-css[main.css](signalr-typescript-webpack/sample/src/css/main.css)]

    앞의 *main.css* 파일은 앱의 스타일을 설정합니다.

1. 다음 내용을 포함한 *src/tsconfig.json*을 생성합니다.

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/src/tsconfig.json)]

    앞의 코드는 [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5 호환 JavaScript를 생성하도록 TypeScript 컴파일러를 구성합니다.

1. 다음 내용을 포함한 *src/index.ts*를 생성합니다.

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    이 TypeScript는 DOM 요소 참조를 조회하여 두 가지 이벤트 핸들러를 연결합니다.

    * `keyup`: 이 이벤트는 사용자가 `tbMessage`로 식별되는 텍스트 상자에 내용을 입력할 때 발생합니다. `send` 함수는 사용자가 **Enter** 키를 누를 때 호출됩니다.
    * `click`: 이 이벤트는 사용자가 **보내기** 단추를 클릭할 때 발생합니다. `send` 함수가 호출됩니다.

## <a name="configure-the-aspnet-core-app"></a>ASP.NET Core 앱 구성

1. `Startup.Configure` 메서드에 제공된 코드는 *Hello World!* 를 표시합니다. `app.Run` 메서드를 [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 및 [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)에 대한 호출로 바꿉니다.

    [!code-csharp[Startup](signalr-typescript-webpack/sample/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    이 코드는 사용자가 전체 URL을 입력하는지 아니면 웹앱의 루트 URL만 입력하는지 관계없이 서버가 *index.html* 파일을 찾아서 제공할 수 있도록 합니다.

1. `Startup.ConfigureServices` 메서드에서 [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_)을 호출합니다. 이는 SignalR 서비스를 프로젝트에 추가합니다.

    [!code-csharp[Startup](signalr-typescript-webpack/sample/Startup.cs?name=snippet_AddSignalR)]

1. */hub* 경로를 `ChatHub` 허브에 매핑합니다. `Startup.Configure` 메서드의 끝에 다음 줄을 추가합니다.

    [!code-csharp[Startup](signalr-typescript-webpack/sample/Startup.cs?name=snippet_UseSignalR)]

1. 프로젝트 루트에 *Hubs*라는 새 디렉터리를 생성합니다. 그 목적은 다음 단계에서 생성되는 SignalR 허브를 저장하는 것입니다.

1. 다음 내용을 포함한 허브 *Hubs/ChatHub.cs*를 생성합니다.

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. `ChatHub` 참조를 확인하기 위해 *Startup.cs* 파일의 맨 위에 다음 코드를 추가합니다.

    [!code-csharp[Startup](signalr-typescript-webpack/sample/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a>클라이언트 및 서버 통신 활성화

앱은 현재 메시지를 보낼 간단한 양식을 표시합니다. 그런 시도를 할 때 아무 일도 일어나지 않습니다. 서버는 특정 경로를 수신 대기하지만 보낸 메시지를 사용하여 아무 일도 하지 않습니다.

1. 프로젝트 루트에서 다음 명령을 실행합니다.

    ```console
    npm install @aspnet/signalr
    ```

    앞의 명령은 클라이언트가 서버에 메시지를 보낼 수 있도록 하는 [SignalR TypeScript 클라이언트](https://www.npmjs.com/package/@aspnet/signalr)를 설치합니다.

1. 강조 표시된 코드를 *src/index.ts* 파일에 추가합니다.

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    앞의 코드는 서버에서 오는 메시지의 수신을 지원합니다. `HubConnectionBuilder` 클래스는 서버 연결을 구성하기 위한 새 빌더를 생성합니다. `withUrl` 함수는 허브 URL을 구성합니다.

    SignalR은 클라이언트 및 서버 간 메시지 교환을 활성화합니다. 각 메시지는 특정 이름을 가집니다. 예를 들어 메시지 영역에 새 메시지를 표시하는 로직을 실행하는 `messageReceived`라는 이름을 가진 메시지가 있을 수 있습니다. 특정 메시지 수신 대기는 `on` 함수를 통해 수행할 수 있습니다. 임의 개수의 메시지 이름을 수신 대기할 수 있습니다. 또한 수신 메시지의 작성자 이름 및 내용 등을 메시지에 파라미터로 전달할 수도 있습니다. 클라이언트가 메시지를 수신한 후 `innerHTML` 특성의 작성자 이름 및 메시지 내용을 사용하여 새 `div` 요소가 생성됩니다. 이 요소가 주 `div` 요소에 추가되어 메시지를 표시합니다.

1. 이제 클라이언트가 메시지를 수신할 수 있으므로, 메시지를 보내도록 구성합니다. 강조 표시된 코드를 *src/index.ts* 파일에 추가합니다.

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/src/index.ts?highlight=34-35)]

    WebSockets 연결을 통해 메시지를 보내면 `send` 메서드를 호출해야 합니다. 이 메서드의 첫 번째 매개 변수는 메시지의 이름입니다. 메시지의 데이터는 다른 매개 변수를 통해 지정합니다. 이 예제에서는 `newMessage`로 식별되는 메시지가 서버로 전송됩니다. 메시지는 사용자 이름 및 텍스트 상자에 입력된 사용자 입력으로 구성됩니다. 보내기가 작동하면 텍스트 상자 값이 지워집니다.

1. 강조 표시된 메서드를 `ChatHub` 클래스에 추가합니다.

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/Hubs/ChatHub.cs?highlight=8-11)]

    앞의 코드는 서버가 메시지를 수신한 후 수신된 메시지를 모든 연결된 사용자에게 브로드캐스트합니다. 모든 메시지를 수신하기 위해 일반 `on` 메서드를 포함할 필요는 없습니다. 메시지 이름 뒤에 명명한 메서드로 충분합니다.

    이 예에서 TypeScript 클라이언트는 `newMessage`로 식별된 메시지를 보냅니다. C# `NewMessage` 메서드에는 클라이언트가 보낸 데이터가 필요합니다. [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all)에 대한 [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) 메서드를 호출합니다. 수신된 메시지를 허브에 연결된 모든 클라이언트에 보냅니다.

## <a name="test-the-app"></a>앱 테스트

다음 단계를 사용하여 앱이 작동하는지 확인합니다.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. WebPack을 *release* 모드로 실행합니다. **패키지 관리자 콘솔** 창을 사용하여 프로젝트 루트에서 다음 명령을 실행합니다. 프로젝트 루트가 아닌 경우 명령을 입력하기 전에 `cd SignalRWebPack`을 입력합니다.

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. **디버그** > **디버그하지 않고 시작**을 선택하여 디버거를 연결하지 않고 브라우저에서 앱을 시작합니다. *wwwroot/index.html* 파일은 `http://localhost:<port_number>`에 제공됩니다.

1. 다른 브라우저 인스턴스(임의 브라우저)를 엽니다. URL을 주소 표시줄에 붙여넣습니다.

1. 브라우저를 선택하고 **메시지** 텍스트 상자에 내용을 입력하고 **보내기** 단추를 클릭합니다. 고유한 사용자 이름과 메시지는 두 페이지 모두에 즉시 표시됩니다.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. 프로젝트 루트에서 다음 명령을 실행하여 WebPack을 *release* 모드로 실행합니다.

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. 프로젝트 루트에서 다음 명령을 실행하여 앱을 빌드하고 실행합니다.

    ```console
    dotnet run
    ```

    웹 서버가 앱을 시작하고 로컬 호스트에서 사용할 수 있도록 만듭니다.

1. `http://localhost:<port_number>`에 대해 브라우저를 엽니다. 그러면 *wwwroot/index.html* 파일이 제공됩니다. 주소 표시줄에서 URL을 복사합니다.

1. 다른 브라우저 인스턴스(임의 브라우저)를 엽니다. URL을 주소 표시줄에 붙여넣습니다.

1. 브라우저를 선택하고 **메시지** 텍스트 상자에 내용을 입력하고 **보내기** 단추를 클릭합니다. 고유한 사용자 이름과 메시지는 두 페이지 모두에 즉시 표시됩니다.

---

![두 브라우저 창 모두에 표시된 메시지](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

## <a name="additional-resources"></a>추가 자료

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
