---
title: JavaScript 서비스를 사용 하 여 ASP.NET Core에서 단일 페이지 응용 프로그램 만들기
author: scottaddie
description: JavaScript 서비스를 사용 하 여 ASP.NET Core에서 지 원하는 SPA (단일 페이지 응용 프로그램)를 만드는 경우의 이점에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017
ms.date: 09/06/2019
uid: client-side/spa-services
ms.openlocfilehash: 52285999d7710cc3198836b9246596980cfc1666
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75355782"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a>JavaScript 서비스를 사용 하 여 ASP.NET Core에서 단일 페이지 응용 프로그램 만들기

작성자: [Scott Addie](https://github.com/scottaddie) 및 [Fiyaz Hasan](https://fiyazhasan.me/)

단일 페이지 응용 프로그램(SPA)은 내재된 풍부한 사용자 경험으로 인해서 인기 있는 웹 응용 프로그램 형식입니다. ASP.NET Core와 같은 서버 쪽 프레임 워크를 사용 하 여 클라이언트 쪽 SPA 프레임 워크 또는 라이브러리 (예: [각도](https://angular.io/) 또는 [반응](https://facebook.github.io/react/))를 통합 하는 것은 어려울 수 있습니다. JavaScript 서비스는 통합 프로세스의 마찰을 줄이기 위해 개발 되었습니다. 서로 다른 클라이언트 및 서버 기술 스택 간의 원활한 작업을 가능하게 해줍니다.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> 이 문서에서 설명 하는 기능은 ASP.NET Core 3.0에서 사용 되지 않습니다. 더 간단한 SPA 프레임 워크 통합 메커니즘은 [AspNetCore. SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet 패키지에서 사용할 수 있습니다. 자세한 내용은 [[알림] Obsoleting 및 AspNetCore 서비스](https://github.com/aspnet/AspNetCore/issues/12890)를 참조 하세요.

::: moniker-end

## <a name="what-is-javascript-services"></a>JavaScript Services 란?

JavaScript 서비스는 ASP.NET Core에 대 한 클라이언트 쪽 기술의 모음입니다. 이 기술의 목표는 ASP.NET Core를 SPA 구축 시 개발자가 선호하는 서버 측 플랫폼으로 자리매김하는 것입니다.

JavaScript 서비스는 두 개의 고유한 NuGet 패키지로 구성 됩니다.

* [Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)
* [Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)

이러한 패키지는 다음과 같은 시나리오에서 유용 합니다.

* 서버에서 JavaScript를 실행하는 경우.
* SPA 프레임워크나 라이브러리를 사용하는 경우.
* Webpack을 사용하여 클라이언트 측 자산을 빌드하는 경우.

이 문서는 대부분의 초점을 SpaServices 패키지의 사용 방법에 두고 있습니다.

## <a name="what-is-spaservices"></a>SpaServices란

SpaServices는 ASP.NET Core를 SPA 구축 시 개발자가 선호하는 서버 측 플랫폼으로 자리매김하기 위해서 개발되었습니다. SpaServices는 ASP.NET Core에서 SPAs를 개발 하는 데 필요 하지 않으며 특정 클라이언트 프레임 워크로 개발자를 잠그지 않습니다.

SpaServices는 다음과 같은 유용한 인프라를 제공합니다.

* [서버 측 사전 렌더링](#server-side-prerendering)
* [Webpack Dev 미들웨어](#webpack-dev-middleware)
* [실시간 모듈 교체](#hot-module-replacement)
* [라우팅 도우미](#routing-helpers)

종합적으로 이러한 인프라 구성 요소는 개발 워크플로와 런타임 환경 모두를 향상시킵니다. 이 구성 요소들은 개별적으로 사용할 수 있습니다.

## <a name="prerequisites-for-using-spaservices"></a>SpaServices 사용을 위한 전제 조건

SpaServices를 사용하려면 다음을 설치합니다.

* npm을 비롯한 [Node.js](https://nodejs.org/)(버전 6 이상)

  * 구성 요소가 설치되어 있으며 찾을 수 있는지 확인해보려면 명령줄에서 다음을 실행합니다.

    ```console
    node -v && npm -v
    ```

  * Azure 웹 사이트에 배포 하는 경우 서버 환경에서 node.js를 설치 하 고 사용할 수&mdash;아무런 조치가 필요 하지 않습니다.

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * Visual Studio 2017을 사용 하는 Windows에서는 **.Net Core 플랫폼 간 개발** 워크 로드를 선택 하 여 SDK를 설치 합니다.

* [Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet 패키지

## <a name="server-side-prerendering"></a>서버 측 사전 렌더링

유니버설(또는 동형) 응용 프로그램은 서버 및 클라이언트 양쪽 모두에서 실행할 수 있는 JavaScript 응용 프로그램입니다. Angular, React 및 기타 인기 있는 프레임워크는 이 응용 프로그램 개발 스타일을 위한 유니버설 플랫폼을 제공합니다. 그 개념은 우선 서버에서 Node.js를 통해서 프레임워크 구성 요소를 렌더링한 다음 이후의 실행은 클라이언트에 위임하는 것입니다.

SpaServices가 제공하는 ASP.NET Core [태그 도우미](xref:mvc/views/tag-helpers/intro)는 서버에서 JavaScript 함수를 호출하여 서버 측 사전 렌더링의 구현을 단순화합니다.

### <a name="server-side-prerendering-prerequisites"></a>서버 쪽 렌더링 전 필수 구성 요소

[Aspnet 렌더링](https://www.npmjs.com/package/aspnet-prerendering) npm 패키지를 설치 합니다.

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a>서버 쪽 렌더링 전 구성

태그 도우미는 프로젝트의 *_ViewImports.cshtml* 파일에서 네임스페이스를 등록하여 검색 가능합니다.

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

태그 도우미는 Razor 뷰 내부에서 HTML과 유사한 구문을 활용하여 저수준 API와 직접 통신하는 복잡함을 추상화합니다.

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a>asp-prerender-모듈 태그 도우미

위의 코드 예제에 사용된 `asp-prerender-module` 태그 도우미는 서버에서 Node.js를 통해서 *ClientApp/dist/main-server.js*를 실행합니다. 명확히 말하자면 *main-server.js* 파일은 [Webpack](https://webpack.github.io/) 빌드 프로세스의 TypeScript-JavaScript 소스 간 변환 작업의 결과물입니다. Webpack은 `main-server`의 진입점 별칭과 *ClientApp/boot-server.ts* 파일에서 시작되는 이 별칭에 대한 종속성 그래프의 순회를 정의합니다.

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

다음 Angular 예제에서 *ClientApp/boot-server.ts* 파일은 `aspnet-prerendering` npm 패키지의 `createServerRenderer` 함수 및 `RenderResult` 형식을 사용하여 Node.js를 통한 서버 렌더링을 구성합니다. 서버 측 렌더링에 사용될 HTML 마크업은 강력한 형식의 JavaScript `Promise` 개체로 래핑된 resolve 함수 호출로 전달됩니다. 이 `Promise` 개체가 중요한 이유는 DOM의 자리 표시자 요소에 삽입하기 위해 비동기적으로 HTML의 마크업을 페이지에 제공하기 때문입니다.

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a>asp-prerender-데이터 태그 도우미

`asp-prerender-module` 태그 도우미와 함께 `asp-prerender-data` 태그 도우미를 사용하면 Razor 뷰에서 서버 측 JavaScript로 컨텍스트 정보를 전달할 수 있습니다. 예를 들어 다음 태그는 사용자 데이터를 `main-server` 모듈로 전달합니다.

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

지정한 `UserName` 인수는 기본 제공 JSON 직렬 변환기를 이용하여 직렬화되고 `params.data` 개체에 저장됩니다. 다음의 Angular 예제는 이 데이터를 사용하여 `h1` 요소 내에 개인화된 인사말을 생성합니다.

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

태그 도우미에 전달 된 속성 이름은 **대/소문자** 표기를 사용 하 여 표시 됩니다. 이와는 달리 JavaScript에서는 동일한 속성 이름이 **camelCase**로 표현됩니다. 이 차이는 기본 JSON 직렬화 구성으로 인한 것입니다.

위의 코드 예제를 확장하기 위해 `resolve` 함수에 제공되는 `globals` 속성을 추가하여 데이터를 서버에서 뷰로 전달할 수 있습니다.

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

`globals` 개체 내에 정의된 `postList` 배열은 브라우저의 전역 `window` 개체에 연결됩니다. 이렇게 전역 범위로 변수를 끌어올리면 특히 동일한 데이터를 서버에서 한 번 그리고 클라이언트에서 다시 한 번 로딩하는 작업과 관련된 반복적인 노력을 줄일 수 있습니다.

![window 개체에 연결된 전역 postList 변수](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a>Webpack Dev 미들웨어

[Webpack Dev 미들웨어](https://webpack.js.org/guides/development/#using-webpack-dev-middleware)는 필요할 때마다 Webpack이 리소스를 빌드하는 간결한 개발 워크플로를 도입합니다. 이 미들웨어는 페이지가 브라우저에서 다시 로드될 때 클라이언트 측 리소스를 자동으로 컴파일하고 제공합니다. 다른 대안은 타사의 종속성이나 사용자 지정 코드가 변경될 때 프로젝트의 npm 빌드 스크립트를 사용해서 직접 Webpack을 호출하는 것입니다. 다음 예제는 *package.json* 파일의 npm 빌드 스크립트를 보여줍니다.

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a>Webpack Dev 미들웨어 필수 구성 요소

[Aspnet webpack](https://www.npmjs.com/package/aspnet-webpack) npm 패키지를 설치 합니다.

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a>Webpack Dev 미들웨어 구성

Webpack Dev 미들웨어는 *Startup.cs* 파일의 `Configure` 메서드에서 다음 코드를 통해서 HTTP 요청 파이프라인에 등록됩니다.

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

`UseWebpackDevMiddleware` 확장 메서드는 `UseStaticFiles` 확장 메서드를 통해서 [정적 파일 호스팅을 등록](xref:fundamentals/static-files)하기 전에 미리 호출해야 합니다. 보안 상의 이유로 앱이 개발 모드에서 실행될 때만 이 미들웨어를 등록해야 합니다.

*webpack.config.js* 파일의 `output.publicPath` 속성은 미들웨어에게 `dist` 폴더의 변경 사항을 모니터링하도록 지정합니다.

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a>실시간 모듈 교체

Webpack의 [실시간 모듈 교체(HMR)](https://webpack.js.org/concepts/hot-module-replacement/) 기능은 [Webpack Dev 미들웨어](#webpack-dev-middleware)가 진화한 것으로 생각하면 됩니다. HMR은 모든 동일한 이점을 제공할 뿐만 아니라 변경 사항을 컴파일 한 뒤 페이지의 내용을 자동으로 갱신하여 개발 워크플로우를 더욱 간소화합니다. SPA의 현재 메모리 내 상태와 디버깅 세션을 방해하지 않도록 이 기능과 브라우저 새로 고침을 혼동하지 마십시오. Webpack Dev 미들웨어 서비스와 브라우저 간에는 실시간 링크가 존재하며, 이 얘기는 변경 사항이 브라우저로 푸시된다는 뜻입니다.

### <a name="hot-module-replacement-prerequisites"></a>핫 모듈 교체 필수 조건

[Webpack-핫 미들웨어](https://www.npmjs.com/package/webpack-hot-middleware) npm 패키지를 설치 합니다.

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a>핫 모듈 교체 구성

`Configure` 메서드에서 MVC의 HTTP 요청 파이프라인에 HMR 구성 요소를 등록해야 합니다.

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

[Webpack Dev 미들웨어](#webpack-dev-middleware)의 경우와 마찬가지로`UseStaticFiles` 확장 메서드를 호출 하기 전에 `UseWebpackDevMiddleware` 확장 메서드를 먼저 호출해야 합니다. 보안 상의 이유로 앱이 개발 모드에서 실행될 때만 이 미들웨어를 등록해야 합니다.

*webpack.config.js* 파일에서는 `plugins` 배열이 비어 있더라도 이를 정의해야 합니다.

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

브라우저에서 앱을 로딩한 다음, 개발자 도구의 콘솔 탭에서 HMR의 활성화를 확인할 수 있습니다.

![실시간 모듈 교체 연결 메시지](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a>라우팅 도우미

대부분의 ASP.NET Core 기반 SPAs에서 클라이언트 쪽 라우팅은 서버 쪽 라우팅 외에도 필요한 경우가 많습니다. SPA 및 MVC 라우팅 시스템은 서로 간섭 없이 독립적으로 동작할 수 있습니다. 그러나 404 HTTP 응답 식별이라는 한 가지 까다로운 문제가 존재합니다.

확장자가 존재하지 않는 `/some/page`라는 경로가 사용되는 시나리오를 생각해보겠습니다. 요청이 서버 측 경로와는 패턴이 일치하지 않지만 클라이언트 측 경로와는 패턴이 일치한다고 가정합니다. 이제 일반적으로 서버에서 이미지 파일을 찾는 것으로 예상되는 `/images/user-512.png`에 대한 들어오는 요청을 생각해보겠습니다. 요청 된 리소스 경로가 서버 쪽 경로 또는 정적 파일과 일치 하지 않을 경우 일반적으로 404 HTTP 상태 코드를 반환 하&mdash;클라이언트 쪽 응용 프로그램에서이를 처리할 가능성이 거의 없습니다.

### <a name="routing-helpers-prerequisites"></a>라우팅 도우미 필수 조건

클라이언트 쪽 라우팅 npm 패키지를 설치 합니다. 예를 들어 Angular를 사용하는 경우입니다.

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a>라우팅 도우미 구성

`Configure` 메서드에서 `MapSpaFallbackRoute`라는 확장 메서드를 사용합니다.

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

경로는 구성 된 순서 대로 평가 됩니다. 따라서 앞의 코드 예제에서는 `default` 경로가 가장 먼저 패턴 일치에 사용됩니다.

## <a name="create-a-new-project"></a>새 프로젝트 만들기

JavaScript 서비스는 미리 구성 된 응용 프로그램 템플릿을 제공 합니다. SpaServices는 이러한 템플릿에서 각 프레임 워크 및 라이브러리 (예: 각도, 반응, Redux)와 함께 사용 됩니다.

이러한 템플릿들은 다음 명령을 실행하여 .NET Core CLI를 통해서 설치할 수 있습니다.

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

사용 가능한 SPA 템플릿 목록은 다음과 같습니다.

| 템플릿                                 | 짧은 이름 | Language | Tags        |
| ------------------------------------------| :--------: | :------: | :---------: |
| MVC ASP.NET Core with Angular             | angular    | [C#]     | Web/MVC/SPA |
| MVC ASP.NET Core with React.js            | react      | [C#]     | Web/MVC/SPA |
| MVC ASP.NET Core with React.js and Redux  | reactredux | [C#]     | Web/MVC/SPA |

SPA 템플릿 중 하나를 사용하여 새로운 프로젝트를 만들려면 [dotnet new](/dotnet/core/tools/dotnet-new) 명령 뒤에 템플릿의 **약식 이름**을 추가합니다. 다음 명령은 서버 측에 ASP.NET Core MVC가 구성된 Angular 응용 프로그램을 생성합니다.

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a>런타임 구성 모드 설정

다음과 같은 두 가지 기본 런타임 구성 모드가 존재합니다.

* **개발**:
  * 손쉬운 디버깅을 위해 소스 맵이 포함되어 있습니다.
  * 성능을 위한 클라이언트 측 코드를 최적화하지 않습니다.
* **프로덕션**:
  * 소스 맵을 제외합니다.
  * 묶음 및 축소를 통해 클라이언트 쪽 코드를 최적화 합니다.

ASP.NET Core는 `ASPNETCORE_ENVIRONMENT` 라는 환경 변수를 사용하여 구성 모드를 저장합니다. 자세한 내용은 [환경 설정](xref:fundamentals/environments#set-the-environment)을 참조 하세요.

### <a name="run-with-net-core-cli"></a>.NET Core CLI를 사용 하 여 실행

다음 명령을 프로젝트 루트에서 실행하여 필요한 NuGet 및 npm 패키지를 복원합니다.

```dotnetcli
dotnet restore && npm i
```

응용 프로그램을 빌드하고 실행합니다.

```dotnetcli
dotnet run
```

응용 프로그램은 [런타임 구성 모드](#set-the-runtime-configuration-mode)에 따라 localhost에서 시작됩니다. 브라우저에서 `http://localhost:5000`로 이동하면 방문 페이지가 표시됩니다.

### <a name="run-with-visual-studio-2017"></a>Visual Studio 2017에서 실행

[dotnet new](/dotnet/core/tools/dotnet-new) 명령으로 생성한 *.csproj* 파일을 엽니다. 필요한 NuGet 및 npm 패키지는 프로젝트가 열릴 때 자동으로 복원됩니다. 이 복원 프로세스는 최대 몇 분 정도 걸릴 수 있으며 완료되면 응용 프로그램을 실행할 준비가 완료됩니다. 녹색 실행 버튼을 클릭하거나 `Ctrl + F5` 키를 누르면 브라우저가 응용 프로그램의 방문 페이지를 엽니다. 응용 프로그램은 [런타임 구성 모드](#set-the-runtime-configuration-mode)에 따라 localhost에서 실행됩니다.

## <a name="test-the-app"></a>앱을 테스트합니다.

SpaServices 템플릿은 [Karma](https://karma-runner.github.io/1.0/index.html)와 [Jasmine](https://jasmine.github.io/)을 사용하여 클라이언트 측 테스트를 실행하도록 미리 구성되어 있습니다. Jasmine은 인기 있는 JavaScript 용 단위 테스트 프레임워크인 반면, Karma는 해당 테스트에 대한 테스트 러너입니다. Karma는 [Webpack Dev 미들웨어](#webpack-dev-middleware)와 함께 동작하도록 구성되어 있으므로 개발자는 변경 사항이 있을 때마다 매번 중단하고 테스트를 실행할 필요가 없습니다. 테스트 케이스에 대해 실행 중인 코드든 테스트 케이스 자체든 상관없이 테스트가 자동으로 실행됩니다.

Angular 응용 프로그램을 예로 들면, *counter.component.spec.ts* 파일에 `CounterComponent`에 대한 두 가지 Jasmine 테스트 케이스가 미리 제공됩니다.

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

*ClientApp* 디렉터리에서 명령 프롬프트를 엽니다. 명령을 실행합니다.

```console
npm test
```

이 스크립트는 *karma.conf.js* 파일에 정의된 설정을 읽는 Karma 테스트 러너를 실행합니다. *karma.conf.js*는 다른 설정들 중에서 `files` 배열을 통해서 실행될 테스트 파일을 식별합니다.

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a>앱 게시

Azure에 게시 하는 방법에 대 한 자세한 내용은 [이 GitHub 문제](https://github.com/aspnet/AspNetCore.Docs/issues/12474) 를 참조 하세요.

생성된 클라이언트 측 자산과 게시된 ASP.NET Core의 결과물을 배포 준비 패키지로 결합하는 일은 번거로운 작업일 수도 있습니다. 다행스럽게도 SpaServices는 `RunWebpack`이라는 이름의 사용자 지정 MSBuild 대상으로 전체 게시 프로세스를 조정합니다.

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

이 MSBuild 대상은 다음과 같은 작업을 책임집니다.

1. Npm 패키지를 복원 합니다.
1. 타사 클라이언트 쪽 자산의 프로덕션 등급 빌드를 만듭니다.
1. 사용자 지정 클라이언트 쪽 자산의 프로덕션 등급 빌드를 만듭니다.
1. Webpack에서 생성 된 자산을 게시 폴더에 복사 합니다.

다음을 실행하면 MSBuild 대상이 호출됩니다.

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a>추가 자료

* [Angular Docs](https://angular.io/docs)
