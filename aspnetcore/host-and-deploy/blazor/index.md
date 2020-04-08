---
title: ASP.NET Core 호스트 및 배포 Blazor
author: guardrex
description: Blazor 앱을 호스트하고 배포하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/11/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/index
ms.openlocfilehash: ddf70da29a82d462422c1bdf74ff45b92bb10b56
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79434267"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a>ASP.NET Core Blazor 호스트 및 배포

작성자: [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) 및 [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="publish-the-app"></a>앱 게시

앱은 릴리스 구성으로 배포하기 위해 게시됩니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 탐색 모음에서 **빌드** >  **{APPLICATION} 게시**를 선택합니다.
1. *publish target*을 선택합니다. 로컬로 게시하려면 **폴더**를 선택합니다.
1. **폴더 선택** 필드에서 기본 위치를 그대로 사용하거나 다른 위치를 지정합니다. **게시** 단추를 선택합니다.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

[dotnet publish](/dotnet/core/tools/dotnet-publish) 명령을 사용하여 릴리스 구성으로 앱을 게시합니다.

```dotnetcli
dotnet publish -c Release
```

---

앱을 게시하면 배포할 자산을 만들기 전에 프로젝트 종속성의 [복원](/dotnet/core/tools/dotnet-restore)과 프로젝트의 [빌드](/dotnet/core/tools/dotnet-build)가 트리거됩니다. 빌드 프로세스의 일부로 앱 다운로드 크기와 로드 시간을 줄이기 위해 사용하지 않는 메서드와 어셈블리를 제거합니다.

게시 위치:

* Blazor WebAssembly
  * 독립 실행형 &ndash; 앱은 */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* 폴더에 게시됩니다. 앱을 정적 사이트로 배포하려면 *wwwroot* 폴더의 내용을 정적 사이트 호스트에 복사합니다.
  * 호스트됨 &ndash; 클라이언트 Blazor WebAssembly 앱이 서버 앱의 */bin/Release/{TARGET FRAMEWORK}/publish/wwwroot* 폴더에 서버 앱의 다른 정적 웹 자산과 함께 게시됩니다. 호스트에 *게시* 폴더의 콘텐츠를 배포합니다.
* Blazor 서버 &ndash; 앱이 */bin/Release/{TARGET FRAMEWORK}/publish* 폴더에 게시됩니다. 호스트에 *게시* 폴더의 콘텐츠를 배포합니다.

이 폴더의 자산은 웹 서버에 배포됩니다. 배포는 사용 중인 개발 도구에 따라 수동 프로세스일 수도 있고 자동 프로세스일 수도 있습니다.

## <a name="app-base-path"></a>앱 기본 경로

*앱 기본 경로*는 앱의 루트 URL 경로입니다. 다음 ASP.NET Core 앱 및 Blazor 하위 앱을 고려하세요.

* ASP.NET Core 앱은 `MyApp`으로 이름이 지정됩니다.
  * 앱은 실제로 *d:/MyApp*에 상주합니다.
  * 요청은 `https://www.contoso.com/{MYAPP RESOURCE}`에서 수신됩니다.
* Blazor이라는 이름의 `CoolApp` 앱은 `MyApp`의 하위 앱입니다.
  * 하위 앱은 물리적으로 *d:/MyApp/CoolApp*에 상주합니다.
  * 요청은 `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`에서 수신됩니다.

`CoolApp`에 대한 추가 구성을 지정하지 않으면 이 시나리오의 하위 앱은 서버에 상주하는 위치에 대해 알지 못합니다. 예를 들어 앱은 상대 URL 경로 `/CoolApp/`에 상주한다는 사실을 모르는 상태에서는 해당 리소스의 올바른 상대 URL을 생성할 수 없습니다.

Blazor이라는 `https://www.contoso.com/CoolApp/` 앱의 기본 경로에 대한 구성을 제공하기 위해 `<base>` 태그의 `href` 특성은 *Pages/_Host.cshtml* 파일(Blazor 서버) 또는 *wwwroot/index.html* 파일(Blazor WebAssembly)의 상대 루트 경로로 설정됩니다.

```html
<base href="/CoolApp/">
```

Blazor 서버 앱은 <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*>의 앱의 요청 파이프라인에서 `Startup.Configure`를 호출하여 서버 쪽 기본 경로를 추가로 설정합니다.

```csharp
app.UsePathBase("/CoolApp");
```

상대 URL 경로를 제공하면 루트 디렉터리에 없는 구성 요소는 앱의 루트 경로를 기준으로 URL을 생성할 수 있습니다. 디렉터리 구조의 다른 수준에 있는 구성 요소는 앱 전체의 위치에서 다른 리소스에 대한 링크를 만들 수 있습니다. 또한 링크의 `href` 대상이 앱 기본 경로 URI 공간 내에 있는 경우 선택한 하이퍼링크를 가로채는 데 앱 기본 경로가 사용됩니다. Blazor 라우터는 내부 탐색을 처리합니다.

많은 호스팅 시나리오에서 앱에 대한 상대 URL 경로는 앱의 루트입니다. 이러한 경우 앱의 상대 URL 기본 경로는 `<base href="/" />` 앱에 대한 기본 구성인 슬래시(Blazor)입니다. GitHub 페이지 및 IIS 하위 앱 같은 다른 호스팅 시나리오에서는 앱 기본 경로를 앱에 대한 서버의 상대 URL 경로로 설정해야 합니다.

앱의 기본 경로를 설정하려면 `<base>`Pages/_Host.cshtml`<head>` 파일(*서버) 또는*wwwroot/index.htmlBlazor 파일(*WebAssembly)의* 태그 요소 내에 Blazor 태그를 업데이트합니다. `href` 특성 값을 `/{RELATIVE URL PATH}/`(뒤에 슬래시가 필요함)로 설정합니다. 여기서 `{RELATIVE URL PATH}`는 앱의 전체 상대 URL 경로입니다.

루트가 아닌 상대 URL 경로를 가진 Blazor WebAssembly 앱(예: `<base href="/CoolApp/">`)의 경우, 앱은 *로컬로 실행하면* 해당 리소스를 찾지 못합니다. 로컬 개발 및 시험 중에 이 문제를 해결하려면 런타임에 *태그의* 값과 일치하는 `href`기본 경로`<base>` 인수를 제공할 수 있습니다. 후행 슬래시를 포함하지 않습니다. 앱을 로컬로 실행하는 경우 경로 기본 인수를 전달하려면 `dotnet run` 옵션을 통해 앱의 디렉터리에서 `--pathbase` 명령을 실행합니다.

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

Blazor의 상대 URL 경로를 사용하는 `/CoolApp/` WebAssembly 앱의 경우(`<base href="/CoolApp/">`) 명령은 다음과 같습니다.

```dotnetcli
dotnet run --pathbase=/CoolApp
```

Blazor WebAssembly 앱은 `http://localhost:port/CoolApp`에서 로컬로 응답합니다.

## <a name="deployment"></a>배포

배포 지침은 다음 항목을 참조하세요.

* <xref:host-and-deploy/blazor/webassembly>
* <xref:host-and-deploy/blazor/server>
