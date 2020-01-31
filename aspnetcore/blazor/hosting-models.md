---
title: ASP.NET Core Blazor 호스팅 모델
author: guardrex
description: Blazor Weasembmbsemboma 및 Blazor Server 호스팅 모델을 이해 합니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-models
ms.openlocfilehash: 145f385fd6c5d04510a4ac15a41b879591ab5caa
ms.sourcegitcommit: c81ef12a1b6e6ac838e5e07042717cf492e6635b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76885519"
---
# <a name="aspnet-core-blazor-hosting-models"></a>ASP.NET Core Blazor 호스팅 모델

[Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor는 [WeasembmbBlazor](https://webassembly.org/)(*Blazor server*) ASP.NET Core의 서버 쪽에서 브라우저에서 클라이언트 쪽을 실행 하도록 디자인 된웹 프레임 워크입니다. 호스팅 모델에 관계 없이 앱 및 구성 요소 모델은 *동일*합니다.

이 문서에서 설명 하는 호스팅 모델에 대 한 프로젝트를 만들려면 <xref:blazor/get-started>를 참조 하세요.

## <a name="blazor-webassembly"></a>Blazor WebAssembly

Blazor에 대 한 주 호스팅 모델이 브라우저에서 클라이언트 쪽을 제대로 실행 하 고 있습니다. Blazor 앱, 해당 앱의 종속성 및 .NET 런타임이 브라우저에 다운로드됩니다. 해당 앱은 브라우저 UI 스레드에서 직접 실행됩니다. UI 업데이트 및 이벤트 처리는 동일한 프로세스 내에서 발생 합니다. 응용 프로그램의 자산은 정적 콘텐츠를 클라이언트에 제공할 수 있는 웹 서버 또는 서비스에 정적 파일로 배포 됩니다.

![Blazor Weasemboma: Blazor 앱은 브라우저 내부의 UI 스레드에서 실행 됩니다.](hosting-models/_static/blazor-webassembly.png)

클라이언트 쪽 호스팅 모델을 사용 하 여 Blazor 앱을 만들려면 **Blazor Weasembomapp** 템플릿 ([dotnet new blazorwasm](/dotnet/core/tools/dotnet-new))을 사용 합니다.

**Blazor WebAssembly 앱** 템플릿을 선택한 후에는 **ASP.NET Core hosted** 확인란을 선택 하 여 ASP.NET Core 백 엔드를 사용 하도록 앱을 구성 하는 옵션이 있습니다 ([dotnet new blazorwasm--호스팅](/dotnet/core/tools/dotnet-new)). ASP.NET Core 앱은 Blazor 앱을 클라이언트에 제공 합니다. Blazor Weasembomapps는 web API 호출 또는 [SignalR](xref:signalr/introduction)를 사용 하 여 네트워크를 통해 서버와 상호 작용할 수 있습니다.

템플릿에는를 처리 하는 `blazor.webassembly.js` 스크립트가 포함 되어 있습니다.

* .NET 런타임, 앱 및 앱의 종속성 다운로드
* 앱을 실행 하기 위한 런타임 초기화

Blazor Weasembom호스팅 모델은 몇 가지 이점을 제공 합니다.

* .NET 서버 쪽 종속성이 없습니다. 클라이언트에 다운로드 한 후 앱이 완전히 작동 합니다.
* 클라이언트 리소스 및 기능은 완전히 활용 됩니다.
* 작업은 서버에서 클라이언트로 오프 로드 됩니다.
* ASP.NET Core 웹 서버는 앱을 호스트 하는 데 필요 하지 않습니다. 서버를 사용 하지 않는 배포 시나리오를 사용할 수 있습니다. 예를 들어 CDN에서 앱을 제공할 수 있습니다.

Blazor Weasembom단점이는 다음과 같습니다.

* 앱은 브라우저의 기능으로 제한 됩니다.
* 가능 클라이언트 하드웨어 및 소프트웨어 (예: Weasembomsupport)는 필수입니다.
* 다운로드 크기가 크고 앱을 로드 하는 데 시간이 오래 걸립니다.
* .NET 런타임 및 도구 지원의 완성도는 낮아집니다. 예를 들어 [.NET Standard](/dotnet/standard/net-standard) 지원 및 디버깅에는 제한 사항이 있습니다.

## <a name="blazor-server"></a>Blazor 서버

Blazor 서버 호스팅 모델을 사용 하면 앱이 ASP.NET Core 앱 내에서 서버에 실행 됩니다. UI 업데이트, 이벤트 처리 및 JavaScript 호출은 [SignalR](xref:signalr/introduction) 연결을 통해 처리됩니다.

![브라우저는 SignalR 연결을 통해 서버의 앱 (ASP.NET Core 앱 내에서 호스트)과 상호 작용 합니다.](hosting-models/_static/blazor-server.png)

Blazor 서버 호스팅 모델을 사용 하 여 Blazor 앱을 만들려면 ASP.NET Core **Blazor Server 앱** 템플릿 ([dotnet new blazorserver](/dotnet/core/tools/dotnet-new))을 사용 합니다. ASP.NET Core 앱은 Blazor Server 앱을 호스팅하고 클라이언트가 연결 하는 SignalR 끝점을 만듭니다.

ASP.NET Core 앱은 추가할 앱의 `Startup` 클래스를 참조 합니다.

* 서버 쪽 서비스.
* 요청 처리 파이프라인에 대 한 앱입니다.

`blazor.server.js` 스크립트&dagger; 클라이언트 연결을 설정 합니다. 필요에 따라 앱 상태를 유지 하 고 복원 하는 것은 앱의 책임입니다 (예: 네트워크 연결이 끊어진 경우).

Blazor 서버 호스팅 모델은 몇 가지 이점을 제공 합니다.

* 다운로드 크기는 Blazor Weasembomapp 보다 훨씬 더 작기 때문에 앱이 훨씬 더 빠르게 로드 됩니다.
* 앱은 .NET Core와 호환 되는 Api의 사용을 포함 하 여 서버 기능을 최대한 활용 합니다.
* 서버의 .NET Core는 앱을 실행 하는 데 사용 되므로 디버깅과 같은 기존 .NET 도구는 정상적으로 작동 합니다.
* 씬 클라이언트가 지원 됩니다. 예를 들어 Blazor Server 앱은 리소스 제한 장치에서 Weasembmbmbsemboma를 지원 하지 않는 브라우저에서 작동 합니다.
* 앱의 구성 요소 코드C# 를 포함 하 여 앱의 .net/code 베이스가 클라이언트에 제공 되지 않습니다.

Blazor 서버 호스팅을 단점이 있습니다.

* 일반적으로 더 높은 대기 시간이 있습니다. 모든 사용자 상호 작용에는 네트워크 홉이 포함 됩니다.
* 오프 라인은 지원 되지 않습니다. 클라이언트 연결에 실패 하면 앱 작동이 중지 됩니다.
* 여러 사용자가 있는 앱의 경우에는 확장성이 어렵습니다. 서버는 여러 클라이언트 연결을 관리 하 고 클라이언트 상태를 처리 해야 합니다.
* 앱을 제공 하려면 ASP.NET Core 서버가 필요 합니다. 서버를 사용 하지 않는 배포 시나리오 (예: CDN에서 앱 제공)를 사용할 수 없습니다.

&dagger;`blazor.server.js` 스크립트는 ASP.NET Core 공유 프레임 워크의 포함 리소스에서 제공 됩니다.

### <a name="comparison-to-server-rendered-ui"></a>서버에서 렌더링 된 UI 비교

Blazor 서버 앱을 이해 하는 한 가지 방법은 Razor 뷰나 Razor Pages를 사용 하 여 ASP.NET Core 앱에서 UI를 렌더링 하는 일반적인 모델과의 차이점을 이해 하는 것입니다. 두 모델 모두 Razor 언어를 사용 하 여 HTML 콘텐츠를 설명 하지만 태그가 렌더링 되는 방식에는 크게 차이가 있습니다.

Razor 페이지 또는 뷰가 렌더링 되 면 Razor 코드의 모든 줄은 텍스트 형식으로 HTML을 내보냅니다. 렌더링 후 서버는 생성 된 상태를 포함 하 여 페이지 또는 뷰 인스턴스를 삭제 합니다. 서버 유효성 검사에 실패 하 고 유효성 검사 요약이 표시 되는 경우와 같은 페이지에 대 한 다른 요청이 발생 하는 경우

* 전체 페이지는 HTML 텍스트로 다시 돌아갑니다.
* 페이지가 클라이언트로 전송 됩니다.

Blazor 앱은 *구성 요소*라는 UI의 재사용 가능한 요소로 구성 됩니다. 구성 요소에 C# 는 코드, 태그 및 기타 구성 요소가 포함 됩니다. 구성 요소가 렌더링 되 면 Blazor은 HTML 또는 XML 문서 개체 모델 (DOM)와 유사한 포함 된 구성 요소의 그래프를 생성 합니다. 이 그래프에는 속성 및 필드에 유지 되는 구성 요소 상태가 포함 됩니다. Blazor는 구성 요소 그래프를 평가 하 여 태그의 이진 표현을 생성 합니다. 이진 형식은 다음과 같을 수 있습니다.

* 렌더링 중에 HTML 텍스트로 바뀝니다.
* 정기적으로 렌더링 하는 동안 태그를 효율적으로 업데이트 하는 데 사용 됩니다.

Blazor의 UI 업데이트는 다음에 의해 트리거됩니다.

* 사용자 상호 작용 (예: 단추 선택)
* 타이머와 같은 앱 트리거입니다.

그래프가 계산 되며 UI *diff* (차이)가 계산 됩니다. 이러한 차이는 클라이언트에서 UI를 업데이트 하는 데 필요한 최소한의 DOM 편집 집합입니다. Diff는 클라이언트에 이진 형식으로 전송 되 고 브라우저에서 적용 됩니다.

사용자가 클라이언트에서 다른 구성 요소를 탐색 한 후에 구성 요소가 삭제 됩니다. 사용자가 구성 요소와 상호 작용 하는 동안에는 구성 요소의 상태 (서비스, 리소스)가 서버 메모리에 보관 되어야 합니다. 서버에서 많은 구성 요소의 상태를 동시에 유지 관리할 수 있기 때문에 메모리 소모는 해결 해야 하는 문제입니다. 서버 메모리를 최적으로 사용 하기 위해 Blazor Server 앱을 제작 하는 방법에 대 한 지침은 <xref:security/blazor/server>를 참조 하세요.

### <a name="integrate-razor-components-into-razor-pages-and-mvc-apps"></a>Razor Pages 및 MVC 앱에 Razor 구성 요소 통합

#### <a name="use-components-in-pages-and-views"></a>페이지 및 뷰에서 구성 요소 사용

기존 Razor Pages 또는 MVC 앱은 Razor 구성 요소를 페이지 및 뷰에 통합할 수 있습니다.

1. 앱의 레이아웃 파일 ( *_Layout cshtml*)에서:

   * `<head>` 요소에 다음 `<base>` 태그를 추가 합니다.

     ```html
     <base href="~/" />
     ```

     앞의 예제에서 `href` 값 ( *앱 기본 경로*)은 앱이 루트 URL 경로 (`/`)에 있는 것으로 가정 합니다. 앱이 하위 응용 프로그램 인 경우 <xref:host-and-deploy/blazor/index#app-base-path> 문서의 *앱 기준 경로* 섹션에 있는 지침을 따르세요.

     *_Layout cshtml* 파일은 Razor Pages 앱의 *Pages/SHARED* 폴더 또는 MVC 앱의 *Views/shared* 폴더에 있습니다.

   * 닫는 `</body>` 태그 내에 *blazor* 스크립트에 대 한 `<script>` 태그를 추가 합니다.

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     프레임 워크는 *blazor* 스크립트를 앱에 추가 합니다. 앱에 스크립트를 수동으로 추가할 필요는 없습니다.

1. 다음 콘텐츠를 사용 하 여 프로젝트의 루트 폴더에 *_Imports razor* 파일을 추가 합니다 (마지막 네임 스페이스 `MyAppNamespace`를 앱의 네임 스페이스로 변경).

   ```csharp
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. `Startup.ConfigureServices`에서 Blazor 서버 서비스를 추가 합니다.

   ```csharp
   services.AddServerSideBlazor();
   ```

1. `Startup.Configure`에서 `app.UseEndpoints`에 Blazor Hub 끝점을 추가 합니다.

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. 모든 페이지 또는 보기에 구성 요소를 통합 합니다. 자세한 내용은 <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps> 문서의 *Razor Pages 및 MVC 앱에 구성 요소 통합* 섹션을 참조 하세요.

#### <a name="use-routable-components-in-a-razor-pages-app"></a>Razor Pages 앱에서 라우팅 가능한 구성 요소 사용

Razor Pages 앱에서 라우팅할 수 있는 Razor 구성 요소를 지원 하려면 다음을 수행 합니다.

1. [페이지 및 뷰에서 구성 요소 사용](#use-components-in-pages-and-views) 섹션의 지침을 따릅니다.

1. 다음 콘텐츠를 사용 하 여 프로젝트의 루트에 *응용 프로그램 razor* 파일을 추가 합니다.

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. 다음 내용을 사용 하 여 *Pages* 폴더에 *_Host cshtml* 파일을 추가 합니다.

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   구성 요소는 해당 레이아웃에 대해 공유 *_Layout cshtml* 파일을 사용 합니다.

1. _Host에 대 한 우선 순위가 낮은 경로를 `Startup.Configure`의 끝점 구성에 추가 *합니다* .

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. 라우팅할 수 있는 구성 요소를 앱에 추가 합니다. 예를 들면 다음과 같습니다.:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   사용자 지정 폴더를 사용 하 여 앱의 구성 요소를 저장 하는 경우 폴더를 나타내는 네임 스페이스를 *Pages/_ViewImports cshtml* 파일에 추가 합니다. 자세한 내용은 <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>를 참조하세요.

#### <a name="use-routable-components-in-an-mvc-app"></a>MVC 앱에서 라우팅 가능한 구성 요소 사용

MVC 앱에서 라우팅할 수 있는 Razor 구성 요소를 지원 하려면 다음을 수행 합니다.

1. [페이지 및 뷰에서 구성 요소 사용](#use-components-in-pages-and-views) 섹션의 지침을 따릅니다.

1. 다음 콘텐츠를 사용 하 여 프로젝트의 루트에 *응용 프로그램 razor* 파일을 추가 합니다.

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. 다음 내용을 사용 하 여 *Views/Home* 폴더에 _Host을 추가 *합니다* .

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   구성 요소는 해당 레이아웃에 대해 공유 *_Layout cshtml* 파일을 사용 합니다.

1. 홈 컨트롤러에 작업을 추가 합니다.

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. `Startup.Configure`의 끝점 구성에 *_Host. cshtml* 뷰를 반환 하는 컨트롤러 작업에 우선 순위가 낮은 경로를 추가 합니다.

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. *페이지* 폴더를 만들고 라우팅할 수 있는 구성 요소를 앱에 추가 합니다. 예를 들면 다음과 같습니다.:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

   사용자 지정 폴더를 사용 하 여 앱의 구성 요소를 저장 하는 경우 폴더를 나타내는 네임 스페이스를 *Views/_ViewImports cshtml* 파일에 추가 합니다. 자세한 내용은 <xref:blazor/components#integrate-components-into-razor-pages-and-mvc-apps>를 참조하세요.

### <a name="circuits"></a>배선

Blazor 서버 앱은 [ASP.NET Core SignalR](xref:signalr/introduction)위에 빌드됩니다. 각 클라이언트는 *회로*라는 하나 이상의 SignalR 연결을 통해 서버와 통신 합니다. 회로는 일시적인 네트워크 중단을 허용할 수 있는 SignalR 연결에 대 한 Blazor의 추상화입니다. Blazor client에서 SignalR 연결의 연결이 끊어지면 새 SignalR 연결을 사용 하 여 서버에 다시 연결 하려고 시도 합니다.

Blazor 서버 앱에 연결 된 각 브라우저 화면 (브라우저 탭 또는 iframe)은 SignalR 연결을 사용 합니다. 이는 서버에서 렌더링 되는 일반적인 앱에 비해 또 다른 중요 한 차이점입니다. 서버에서 렌더링 하는 응용 프로그램에서는 여러 브라우저 화면에서 동일한 앱을 여는 것은 일반적으로 서버에서 추가 리소스 요구 사항으로 변환 되지 않습니다. Blazor 서버 앱에서 각 브라우저 화면에는 서버에서 관리 해야 하는 별도의 회로와 개별 구성 요소 상태 인스턴스가 필요 합니다.

Blazor는 브라우저 탭을 닫거나 외부 URL로 이동 하는 *정상적인* 종료를 고려 합니다. 정상적인 종료 시 회로 및 연결 된 리소스가 즉시 해제 됩니다. 클라이언트는 네트워크 중단으로 인해 안정적이 지 않은 방식으로 연결을 끊을 수도 있습니다. Blazor 서버는 클라이언트가 다시 연결할 수 있도록 구성 가능한 간격 동안 연결이 끊어진 회로를 저장 합니다. 자세한 내용은 [동일한 서버에](#reconnection-to-the-same-server) 다시 연결 섹션을 참조 하세요.

### <a name="ui-latency"></a>UI 대기 시간

UI 대기 시간은 시작 된 작업에서 UI를 업데이트 하는 데 걸리는 시간입니다. UI 대기 시간에 대 한 값이 작을수록 앱이 사용자에 게 반응 하는 데 필수적입니다. Blazor 서버 앱에서 각 작업은 서버로 전송 되 고 처리 되며 UI diff가 다시 전송 됩니다. 따라서 UI 대기 시간은 네트워크 대기 시간 및 작업 처리의 서버 대기 시간 합계입니다.

개인 회사 네트워크로 제한 된 lob (기간 업무) 앱의 경우 네트워크 대기 시간으로 인 한 대기 시간에 대 한 사용자의 영향은 일반적으로 imperceptible입니다. 인터넷을 통해 배포 된 앱의 경우 특히 사용자가 지리적으로 광범위 하 게 분산 된 경우 대기 시간이 달라질 수 있습니다.

메모리 사용은 앱 대기 시간에도 영향을 주지 않습니다. 메모리 사용이 증가 하면 가비지 수집 또는 페이징 메모리가 디스크에 자주 발생 하므로 앱 성능이 저하 되 고 결과적으로 UI 대기 시간이 증가 합니다. 자세한 내용은 <xref:security/blazor/server>를 참조하세요.

Blazor 서버 앱은 네트워크 대기 시간 및 메모리 사용을 줄여 UI 대기 시간을 최소화 하도록 최적화 되어야 합니다. 네트워크 대기 시간을 측정 하는 방법에 대 한 자세한 내용은 <xref:host-and-deploy/blazor/server#measure-network-latency>를 참조 하세요. SignalR 및 Blazor에 대 한 자세한 내용은 다음을 참조 하세요.

* <xref:host-and-deploy/blazor/server>
* <xref:security/blazor/server>

### <a name="connection-to-the-server"></a>서버에 대 한 연결

Blazor 서버 앱은 서버에 대 한 활성 SignalR 연결이 필요 합니다. 연결이 끊어지면 앱이 서버에 다시 연결을 시도 합니다. 클라이언트의 상태가 아직 메모리에 있으면 클라이언트 세션이 상태 손실 없이 다시 시작 됩니다.

#### <a name="reconnection-to-the-same-server"></a>동일한 서버에 다시 연결

Blazor 서버 앱은 서버에서 UI 상태를 설정 하는 첫 번째 클라이언트 요청에 대 한 응답으로 prerenders. 클라이언트에서 SignalR 연결을 만들려고 하면 클라이언트는 동일한 서버에 다시 연결 해야 합니다. 두 개 이상의 백 엔드 서버를 사용 하는 Blazor Server 앱은 SignalR 연결에 대 한 *고정 세션* 을 구현 해야 합니다.

Blazor 서버 앱에 [Azure SignalR Service](/azure/azure-signalr)를 사용하는 것이 좋습니다. 이 서비스를 사용하면 Blazor 서버 앱을 다수의 동시 SignalR 연결로 확장할 수 있습니다. 고정 세션은 서비스의 `ServerStickyMode` 옵션 또는 구성 값을 `Required`로 설정 하 여 Azure SignalR 서비스에 대해 사용 하도록 설정 됩니다. 자세한 내용은 <xref:host-and-deploy/blazor/server#signalr-configuration>를 참조하세요.

IIS를 사용하는 경우 애플리케이션 요청 라우팅을 사용하여 고정 세션을 사용할 수 있습니다. 자세한 내용은 [애플리케이션 요청 라우팅을 사용하여 HTTP 부하 분산](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)을 참조하세요.

#### <a name="reflect-the-connection-state-in-the-ui"></a>UI의 연결 상태를 반영 합니다.

클라이언트에서 연결이 끊어진 것을 감지 하면 클라이언트에서 다시 연결을 시도 하는 동안 기본 UI가 표시 됩니다. 다시 연결이 실패 하는 경우 사용자에 게 다시 시도 하는 옵션이 제공 됩니다.

UI를 사용자 지정 하려면 _Host의 `<body>`에 `components-reconnect-modal` `id`를 사용 하 여 요소를 정의 *합니다. cshtml* Razor 페이지:

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

다음 표에서는 `components-reconnect-modal` 요소에 적용 된 CSS 클래스에 대해 설명 합니다.

| CSS 클래스                       | &hellip;를 나타냅니다. |
| ------------------------------- | ------------------------- |
| `components-reconnect-show`     | 연결이 끊어졌습니다. 클라이언트에서 다시 연결 하려고 합니다. 모달을 표시 합니다. |
| `components-reconnect-hide`     | 서버에 대 한 활성 연결이 다시 설정 됩니다. 모달을 숨깁니다. |
| `components-reconnect-failed`   | 네트워크 오류로 인해 다시 연결 하지 못했습니다. 다시 연결을 시도 하려면 `window.Blazor.reconnect()`를 호출 합니다. |
| `components-reconnect-rejected` | 다시 연결이 거부 되었습니다. 서버에 도달 했지만 연결이 거부 되었으며 서버에서 사용자의 상태가 손실 됩니다. 앱을 다시 로드 하려면 `location.reload()`를 호출 합니다. 이 연결 상태는 다음과 같은 경우에 발생할 수 있습니다.<ul><li>서버 쪽 회로의 작동이 중단 됩니다.</li><li>서버에서 사용자의 상태를 삭제 하기에 충분 한 길이의 클라이언트 연결이 끊겼습니다. 사용자가 상호 작용 하는 구성 요소의 인스턴스가 삭제 됩니다.</li><li>서버가 다시 시작 되거나 앱의 작업자 프로세스가 재활용 됩니다.</li></ul> |

### <a name="stateful-reconnection-after-prerendering"></a>렌더링 후 상태 저장 다시 연결

서버에 대 한 클라이언트 연결이 설정 되기 전에 서버에서 UI를 미리 렌더링 하도록 Blazor 서버 앱이 기본적으로 설정 되어 있습니다. 이는 _Host에 설정 되어 *있습니다. cshtml* Razor 페이지:

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

구성 요소가 있는지 여부를 구성 하 `RenderMode` 다음을 수행 합니다.

* 는 페이지에 미리 렌더링 된 됩니다.
* 는 페이지에서 정적 HTML로 렌더링 되거나 사용자 에이전트에서 Blazor 앱을 부트스트랩 하는 데 필요한 정보가 포함 되어 있습니다.

| `RenderMode`        | 설명 |
| ------------------- | ----------- |
| `ServerPrerendered` | 구성 요소를 정적 HTML로 렌더링 하 고 Blazor Server 앱에 대 한 마커를 포함 합니다. 사용자 에이전트가 시작 되 면이 마커는 Blazor 앱을 부트스트랩 하는 데 사용 됩니다. |
| `Server`            | Blazor Server 앱에 대 한 마커를 렌더링 합니다. 구성 요소의 출력은 포함 되지 않습니다. 사용자 에이전트가 시작 되 면이 마커는 Blazor 앱을 부트스트랩 하는 데 사용 됩니다. |
| `Static`            | 구성 요소를 정적 HTML로 렌더링 합니다. |

정적 HTML 페이지에서 서버 구성 요소를 렌더링 하는 것은 지원 되지 않습니다.

`RenderMode` `ServerPrerendered`되 면 구성 요소는 초기에 페이지의 일부로 정적으로 렌더링 됩니다. 브라우저에서 서버로 다시 연결 하면 구성 요소가 *다시*렌더링 되 고 구성 요소가 대화형으로 설정 됩니다. 구성 요소를 초기화 하는 [Oninitialized {Async}](xref:blazor/lifecycle#component-initialization-methods) 주기 방법이 있는 경우 메서드는 *두 번*실행 됩니다.

* 구성 요소가 정적으로 미리 렌더링 된 된 경우
* 서버 연결이 설정 된 후

이로 인해 구성 요소가 최종적으로 렌더링 될 때 UI에 표시 되는 데이터가 크게 변경 될 수 있습니다.

Blazor Server 앱에서 이중 렌더링 시나리오를 방지 하려면 다음을 수행 합니다.

* 렌더링 중에 상태를 캐시 하 고 앱이 다시 시작 된 후 상태를 검색 하는 데 사용할 수 있는 식별자를 전달 합니다.
* 렌더링 중에 식별자를 사용 하 여 구성 요소 상태를 저장 합니다.
* 렌더링 후 식별자를 사용 하 여 캐시 된 상태를 검색 합니다.

다음 코드는 이중 렌더링을 방지 하는 템플릿 기반 Blazor 서버 앱에서 업데이트 된 `WeatherForecastService`를 보여 줍니다.

```csharp
public class WeatherForecastService
{
    private static readonly string[] _summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = _summaries[rng.Next(_summaries.Length)]
            }).ToArray();
        });
    }
}
```

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a>Razor 페이지 및 뷰에서 상태 저장 대화형 구성 요소 렌더링

상태 저장 대화형 구성 요소는 Razor 페이지 또는 보기에 추가할 수 있습니다.

페이지 또는 뷰가 렌더링 되는 경우:

* 구성 요소가 페이지 또는 뷰와 미리 렌더링 된 됩니다.
* 렌더링에 사용 되는 초기 구성 요소 상태가 손실 됩니다.
* 새 구성 요소 상태는 SignalR 연결이 설정 될 때 생성 됩니다.

다음 Razor 페이지는 `Counter` 구성 요소를 렌더링 합니다.

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a>Razor 페이지 및 뷰에서 비 대화형 구성 요소 렌더링

다음 Razor 페이지에서 `Counter` 구성 요소는 폼을 사용 하 여 지정 된 초기 값을 사용 하 여 정적으로 렌더링 됩니다.

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

`MyComponent` 정적으로 렌더링 되므로 구성 요소는 대화형이 될 수 없습니다.

### <a name="detect-when-the-app-is-prerendering"></a>앱이 사전 렌더링 되는 경우 검색

[!INCLUDE[](~/includes/blazor-prerendering.md)]

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a>Blazor Server 앱에 대 한 SignalR 클라이언트 구성

경우에 따라 Blazor Server 앱에서 사용 하는 SignalR 클라이언트를 구성 해야 합니다. 예를 들어 SignalR 클라이언트에 대 한 로깅을 구성 하 여 연결 문제를 진단할 수 있습니다.

*Pages/_Host* 파일에서 SignalR 클라이언트를 구성 하려면 다음을 수행 합니다.

* `blazor.server.js` 스크립트의 `<script>` 태그에 `autostart="false"` 특성을 추가 합니다.
* `Blazor.start`를 호출 하 고 SignalR 빌더를 지정 하는 구성 개체를 전달 합니다.

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```

## <a name="additional-resources"></a>추가 자료

* <xref:blazor/get-started>
* <xref:signalr/introduction>
