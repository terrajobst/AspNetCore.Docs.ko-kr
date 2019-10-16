---
title: ASP.NET Core Blazor 호스트 및 배포
author: guardrex
description: Blazor 앱을 호스트하고 배포하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: host-and-deploy/blazor/index
ms.openlocfilehash: 271135a0ebe67d31fd8e2bcf672e723814727147
ms.sourcegitcommit: 35a86ce48041caaf6396b1e88b0472578ba24483
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72391333"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a><span data-ttu-id="3e13e-103">ASP.NET Core Blazor 호스트 및 배포</span><span class="sxs-lookup"><span data-stu-id="3e13e-103">Host and deploy ASP.NET Core Blazor</span></span>

<span data-ttu-id="3e13e-104">작성자: [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) 및 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="3e13e-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="publish-the-app"></a><span data-ttu-id="3e13e-105">앱 게시</span><span class="sxs-lookup"><span data-stu-id="3e13e-105">Publish the app</span></span>

<span data-ttu-id="3e13e-106">앱은 릴리스 구성으로 배포하기 위해 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="3e13e-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3e13e-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3e13e-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="3e13e-108">탐색 모음에서 **빌드** >  **{APPLICATION} 게시**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="3e13e-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="3e13e-109">*publish target*을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="3e13e-109">Select the *publish target*.</span></span> <span data-ttu-id="3e13e-110">로컬로 게시하려면 **폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="3e13e-110">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="3e13e-111">**폴더 선택** 필드에서 기본 위치를 그대로 사용하거나 다른 위치를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="3e13e-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="3e13e-112">**게시** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="3e13e-112">Select the **Publish** button.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="3e13e-113">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3e13e-113">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="3e13e-114">[dotnet publish](/dotnet/core/tools/dotnet-publish) 명령을 사용하여 릴리스 구성으로 앱을 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="3e13e-114">Use the [dotnet publish](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```dotnetcli
dotnet publish -c Release
```

---

<span data-ttu-id="3e13e-115">앱을 게시하면 배포할 자산을 만들기 전에 프로젝트 종속성의 [복원](/dotnet/core/tools/dotnet-restore)과 프로젝트의 [빌드](/dotnet/core/tools/dotnet-build)가 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="3e13e-115">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="3e13e-116">빌드 프로세스의 일부로 앱 다운로드 크기와 로드 시간을 줄이기 위해 사용하지 않는 메서드와 어셈블리를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="3e13e-116">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="3e13e-117">Blazor WebAssembly 앱은 */bin/Release/{TARGET FRAMEWORK}/publish/{ASSEMBLY NAME}/dist* 폴더에 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="3e13e-117">A Blazor WebAssembly app is published to the */bin/Release/{TARGET FRAMEWORK}/publish/{ASSEMBLY NAME}/dist* folder.</span></span> <span data-ttu-id="3e13e-118">Blazor 서버 앱은 */bin/Release/{TARGET FRAMEWORK}/publish* 폴더에 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="3e13e-118">A Blazor Server app is published to the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span>

<span data-ttu-id="3e13e-119">이 폴더의 자산은 웹 서버에 배포됩니다.</span><span class="sxs-lookup"><span data-stu-id="3e13e-119">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="3e13e-120">배포는 사용 중인 개발 도구에 따라 수동 프로세스일 수도 있고 자동 프로세스일 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3e13e-120">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="3e13e-121">앱 기본 경로</span><span class="sxs-lookup"><span data-stu-id="3e13e-121">App base path</span></span>

<span data-ttu-id="3e13e-122">*앱 기본 경로*는 앱의 루트 URL 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="3e13e-122">The *app base path* is the app's root URL path.</span></span> <span data-ttu-id="3e13e-123">다음 주요 앱 및 Blazor 앱을 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="3e13e-123">Consider the following main app and Blazor app:</span></span>

* <span data-ttu-id="3e13e-124">주 앱은 `MyApp`이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="3e13e-124">The main app is called `MyApp`:</span></span>
  * <span data-ttu-id="3e13e-125">앱은 실제로 *d:\\MyApp*에 상주합니다.</span><span class="sxs-lookup"><span data-stu-id="3e13e-125">The app physically resides at *d:\\MyApp*.</span></span>
  * <span data-ttu-id="3e13e-126">요청은 `https://www.contoso.com/{MYAPP RESOURCE}`에서 수신됩니다.</span><span class="sxs-lookup"><span data-stu-id="3e13e-126">Requests are received at `https://www.contoso.com/{MYAPP RESOURCE}`.</span></span>
* <span data-ttu-id="3e13e-127">`CoolApp`이라는 Blazor 앱은 `MyApp`의 하위 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="3e13e-127">A Blazor app called `CoolApp` is a sub-app of `MyApp`:</span></span>
  * <span data-ttu-id="3e13e-128">하위 앱은 물리적으로 *d:\\MyApp\\CoolApp*에 상주합니다.</span><span class="sxs-lookup"><span data-stu-id="3e13e-128">The sub-app physically resides at *d:\\MyApp\\CoolApp*.</span></span>
  * <span data-ttu-id="3e13e-129">요청은 `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`에서 수신됩니다.</span><span class="sxs-lookup"><span data-stu-id="3e13e-129">Requests are received at `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span></span>

<span data-ttu-id="3e13e-130">`CoolApp`에 대한 추가 구성을 지정하지 않으면 이 시나리오의 하위 앱은 서버에 상주하는 위치에 대해 알지 못합니다.</span><span class="sxs-lookup"><span data-stu-id="3e13e-130">Without specifying additional configuration for `CoolApp`, the sub-app in this scenario has no knowledge of where it resides on the server.</span></span> <span data-ttu-id="3e13e-131">예를 들어 앱은 상대 URL 경로 `/CoolApp/`에 상주한다는 사실을 모르는 상태에서는 해당 리소스의 올바른 상대 URL을 생성할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="3e13e-131">For example, the app can't construct correct relative URLs to its resources without knowing that it resides at the relative URL path `/CoolApp/`.</span></span>

<span data-ttu-id="3e13e-132">Blazor 앱의 기본 경로 `https://www.contoso.com/CoolApp/`에 대한 구성을 제공하기 위해 `<base>` 태그의 `href` 특성은 *wwwroot/index.html* 파일의 상대 루트 경로로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="3e13e-132">To provide configuration for the Blazor app's base path of `https://www.contoso.com/CoolApp/`, the `<base>` tag's `href` attribute is set to the relative root path in the *wwwroot/index.html* file:</span></span>

```html
<base href="/CoolApp/">
```

<span data-ttu-id="3e13e-133">상대 URL 경로를 제공하면 루트 디렉터리에 없는 구성 요소는 앱의 루트 경로를 기준으로 URL을 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3e13e-133">By providing the relative URL path, a component that isn't in the root directory can construct URLs relative to the app's root path.</span></span> <span data-ttu-id="3e13e-134">디렉터리 구조의 다른 수준에 있는 구성 요소는 앱 전체의 위치에서 다른 리소스에 대한 링크를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3e13e-134">Components at different levels of the directory structure can build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="3e13e-135">또한 링크의 `href` 대상이 앱 기본 경로 내에 있는 경우 &mdash; Blazor 라우터가 내부 탐색을 처리하는 경우 하이퍼링크 클릭을 가로채기 위해서도 앱 기본 경로를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="3e13e-135">The app base path is also used to intercept hyperlink clicks where the `href` target of the link is within the app base path URI space&mdash;the Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="3e13e-136">많은 호스팅 시나리오에서 앱에 대한 상대 URL 경로는 앱의 루트입니다.</span><span class="sxs-lookup"><span data-stu-id="3e13e-136">In many hosting scenarios, the relative URL path to the app is the root of the app.</span></span> <span data-ttu-id="3e13e-137">이러한 경우 앱의 상대 URL 기본 경로는 Blazor 앱에 대한 기본 구성인 슬래시(`<base href="/" />`)입니다.</span><span class="sxs-lookup"><span data-stu-id="3e13e-137">In these cases, the app's relative URL base path is a forward slash (`<base href="/" />`), which is the default configuration for a Blazor app.</span></span> <span data-ttu-id="3e13e-138">GitHub 페이지 및 IIS 하위 앱 같은 다른 호스팅 시나리오에서는 앱 기본 경로를 앱에 대한 서버의 상대 URL 경로로 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="3e13e-138">In other hosting scenarios, such as GitHub Pages and IIS sub-apps, the app base path must be set to the server's relative URL path to the app.</span></span>

<span data-ttu-id="3e13e-139">앱의 기본 경로를 설정하려면 *wwwroot/index.html* 파일의 `<head>` 태그 요소 내에 `<base>` 태그를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="3e13e-139">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the *wwwroot/index.html* file.</span></span> <span data-ttu-id="3e13e-140">`href` 특성 값을 `/{RELATIVE URL PATH}/`(뒤에 슬래시가 필요함)로 설정합니다. 여기서 `{RELATIVE URL PATH}`는 앱의 전체 상대 URL 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="3e13e-140">Set the `href` attribute value to `/{RELATIVE URL PATH}/` (the trailing slash is required), where `{RELATIVE URL PATH}` is the app's full relative URL path.</span></span>

<span data-ttu-id="3e13e-141">루트가 아닌 상대 URL 경로를 가진 앱(예: `<base href="/CoolApp/">`)의 경우, 앱은 *로컬로 실행하면* 해당 리소스를 찾지 못합니다.</span><span class="sxs-lookup"><span data-stu-id="3e13e-141">For an app with a non-root relative URL path (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="3e13e-142">로컬 개발 및 시험 중에 이 문제를 해결하려면 런타임에 `<base>` 태그의 `href` 값과 일치하는 *기본 경로* 인수를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="3e13e-142">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span> <span data-ttu-id="3e13e-143">앱을 로컬로 실행하는 경우 경로 기본 인수를 전달하려면 `--pathbase` 옵션을 통해 앱의 디렉터리에서 `dotnet run` 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="3e13e-143">To pass the path base argument when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

<span data-ttu-id="3e13e-144">`/CoolApp/`의 상대 URL 경로를 사용하는 앱의 경우(`<base href="/CoolApp/">`) 명령은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="3e13e-144">For an app with a relative URL path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```dotnetcli
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="3e13e-145">앱이 `http://localhost:port/CoolApp`에서 로컬로 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="3e13e-145">The app responds locally at `http://localhost:port/CoolApp`.</span></span>

## <a name="deployment"></a><span data-ttu-id="3e13e-146">배포</span><span class="sxs-lookup"><span data-stu-id="3e13e-146">Deployment</span></span>

<span data-ttu-id="3e13e-147">배포 지침은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="3e13e-147">For deployment guidance, see the following topics:</span></span>

* <xref:host-and-deploy/blazor/webassembly>
* <xref:host-and-deploy/blazor/server>
