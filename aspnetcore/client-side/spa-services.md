---
title: JavaScript 서비스를 사용 하 여 ASP.NET Core에서 단일 페이지 응용 프로그램 만들기
author: scottaddie
description: JavaScript 서비스를 사용 하 여 ASP.NET Core에서 지 원하는 SPA (단일 페이지 응용 프로그램)를 만드는 경우의 이점에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017
ms.date: 09/06/2019
uid: client-side/spa-services
ms.openlocfilehash: caff1a735de3274b371f67e6e485dc42e579452c
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828219"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a><span data-ttu-id="eb10c-103">JavaScript 서비스를 사용 하 여 ASP.NET Core에서 단일 페이지 응용 프로그램 만들기</span><span class="sxs-lookup"><span data-stu-id="eb10c-103">Use JavaScript Services to Create Single Page Applications in ASP.NET Core</span></span>

<span data-ttu-id="eb10c-104">작성자: [Scott Addie](https://github.com/scottaddie) 및 [Fiyaz Hasan](https://fiyazhasan.me/)</span><span class="sxs-lookup"><span data-stu-id="eb10c-104">By [Scott Addie](https://github.com/scottaddie) and [Fiyaz Hasan](https://fiyazhasan.me/)</span></span>

<span data-ttu-id="eb10c-105">단일 페이지 응용 프로그램(SPA)은 내재된 풍부한 사용자 경험으로 인해서 인기 있는 웹 응용 프로그램 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-105">A Single Page Application (SPA) is a popular type of web application due to its inherent rich user experience.</span></span> <span data-ttu-id="eb10c-106">ASP.NET Core와 같은 서버 쪽 프레임 워크를 사용 하 여 클라이언트 쪽 SPA 프레임 워크 또는 라이브러리 (예: [각도](https://angular.io/) 또는 [반응](https://facebook.github.io/react/))를 통합 하는 것은 어려울 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-106">Integrating client-side SPA frameworks or libraries, such as [Angular](https://angular.io/) or [React](https://facebook.github.io/react/), with server-side frameworks such as ASP.NET Core can be difficult.</span></span> <span data-ttu-id="eb10c-107">JavaScript 서비스는 통합 프로세스의 마찰을 줄이기 위해 개발 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-107">JavaScript Services was developed to reduce friction in the integration process.</span></span> <span data-ttu-id="eb10c-108">서로 다른 클라이언트 및 서버 기술 스택 간의 원활한 작업을 가능하게 해줍니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-108">It enables seamless operation between the different client and server technology stacks.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="eb10c-109">이 문서에서 설명 하는 기능은 ASP.NET Core 3.0에서 사용 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-109">The features described in this article are obsolete as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="eb10c-110">더 간단한 SPA 프레임 워크 통합 메커니즘은 [AspNetCore. SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet 패키지에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-110">A simpler SPA frameworks integration mechanism is available in the [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet package.</span></span> <span data-ttu-id="eb10c-111">자세한 내용은 [[알림] Obsoleting 및 AspNetCore 서비스](https://github.com/dotnet/AspNetCore/issues/12890)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="eb10c-111">For more information, see [[Announcement] Obsoleting Microsoft.AspNetCore.SpaServices and Microsoft.AspNetCore.NodeServices](https://github.com/dotnet/AspNetCore/issues/12890).</span></span>

::: moniker-end

## <a name="what-is-javascript-services"></a><span data-ttu-id="eb10c-112">JavaScript Services 란?</span><span class="sxs-lookup"><span data-stu-id="eb10c-112">What is JavaScript Services</span></span>

<span data-ttu-id="eb10c-113">JavaScript 서비스는 ASP.NET Core에 대 한 클라이언트 쪽 기술의 모음입니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-113">JavaScript Services is a collection of client-side technologies for ASP.NET Core.</span></span> <span data-ttu-id="eb10c-114">이 기술의 목표는 ASP.NET Core를 SPA 구축 시 개발자가 선호하는 서버 측 플랫폼으로 자리매김하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-114">Its goal is to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span>

<span data-ttu-id="eb10c-115">JavaScript 서비스는 두 개의 고유한 NuGet 패키지로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-115">JavaScript Services consists of two distinct NuGet packages:</span></span>

* <span data-ttu-id="eb10c-116">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span><span class="sxs-lookup"><span data-stu-id="eb10c-116">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span></span>
* <span data-ttu-id="eb10c-117">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span><span class="sxs-lookup"><span data-stu-id="eb10c-117">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span></span>

<span data-ttu-id="eb10c-118">이러한 패키지는 다음과 같은 시나리오에서 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-118">These packages are useful in the following scenarios:</span></span>

* <span data-ttu-id="eb10c-119">서버에서 JavaScript를 실행하는 경우.</span><span class="sxs-lookup"><span data-stu-id="eb10c-119">Run JavaScript on the server</span></span>
* <span data-ttu-id="eb10c-120">SPA 프레임워크나 라이브러리를 사용하는 경우.</span><span class="sxs-lookup"><span data-stu-id="eb10c-120">Use a SPA framework or library</span></span>
* <span data-ttu-id="eb10c-121">Webpack을 사용하여 클라이언트 측 자산을 빌드하는 경우.</span><span class="sxs-lookup"><span data-stu-id="eb10c-121">Build client-side assets with Webpack</span></span>

<span data-ttu-id="eb10c-122">이 문서는 대부분의 초점을 SpaServices 패키지의 사용 방법에 두고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-122">Much of the focus in this article is placed on using the SpaServices package.</span></span>

## <a name="what-is-spaservices"></a><span data-ttu-id="eb10c-123">SpaServices란</span><span class="sxs-lookup"><span data-stu-id="eb10c-123">What is SpaServices</span></span>

<span data-ttu-id="eb10c-124">SpaServices는 ASP.NET Core를 SPA 구축 시 개발자가 선호하는 서버 측 플랫폼으로 자리매김하기 위해서 개발되었습니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-124">SpaServices was created to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span> <span data-ttu-id="eb10c-125">SpaServices는 ASP.NET Core에서 SPAs를 개발 하는 데 필요 하지 않으며 특정 클라이언트 프레임 워크로 개발자를 잠그지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-125">SpaServices isn't required to develop SPAs with ASP.NET Core, and it doesn't lock developers into a particular client framework.</span></span>

<span data-ttu-id="eb10c-126">SpaServices는 다음과 같은 유용한 인프라를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-126">SpaServices provides useful infrastructure such as:</span></span>

* [<span data-ttu-id="eb10c-127">서버 측 사전 렌더링</span><span class="sxs-lookup"><span data-stu-id="eb10c-127">Server-side prerendering</span></span>](#server-side-prerendering)
* [<span data-ttu-id="eb10c-128">Webpack Dev 미들웨어</span><span class="sxs-lookup"><span data-stu-id="eb10c-128">Webpack Dev Middleware</span></span>](#webpack-dev-middleware)
* [<span data-ttu-id="eb10c-129">실시간 모듈 교체</span><span class="sxs-lookup"><span data-stu-id="eb10c-129">Hot Module Replacement</span></span>](#hot-module-replacement)
* [<span data-ttu-id="eb10c-130">라우팅 도우미</span><span class="sxs-lookup"><span data-stu-id="eb10c-130">Routing helpers</span></span>](#routing-helpers)

<span data-ttu-id="eb10c-131">종합적으로 이러한 인프라 구성 요소는 개발 워크플로와 런타임 환경 모두를 향상시킵니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-131">Collectively, these infrastructure components enhance both the development workflow and the runtime experience.</span></span> <span data-ttu-id="eb10c-132">이 구성 요소들은 개별적으로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-132">The components can be adopted individually.</span></span>

## <a name="prerequisites-for-using-spaservices"></a><span data-ttu-id="eb10c-133">SpaServices 사용을 위한 전제 조건</span><span class="sxs-lookup"><span data-stu-id="eb10c-133">Prerequisites for using SpaServices</span></span>

<span data-ttu-id="eb10c-134">SpaServices를 사용하려면 다음을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-134">To work with SpaServices, install the following:</span></span>

* <span data-ttu-id="eb10c-135">npm을 비롯한 [Node.js](https://nodejs.org/)(버전 6 이상)</span><span class="sxs-lookup"><span data-stu-id="eb10c-135">[Node.js](https://nodejs.org/) (version 6 or later) with npm</span></span>

  * <span data-ttu-id="eb10c-136">구성 요소가 설치되어 있으며 찾을 수 있는지 확인해보려면 명령줄에서 다음을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-136">To verify these components are installed and can be found, run the following from the command line:</span></span>

    ```console
    node -v && npm -v
    ```

  * <span data-ttu-id="eb10c-137">Azure 웹 사이트에 배포 하는 경우 서버 환경에서 node.js를 설치 하 고 사용할 수&mdash;아무런 조치가 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-137">If deploying to an Azure web site, no action is required&mdash;Node.js is installed and available in the server environments.</span></span>

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * <span data-ttu-id="eb10c-138">Visual Studio 2017을 사용 하는 Windows에서는 **.Net Core 플랫폼 간 개발** 워크 로드를 선택 하 여 SDK를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-138">On Windows using Visual Studio 2017, the SDK is installed by selecting the **.NET Core cross-platform development** workload.</span></span>

* <span data-ttu-id="eb10c-139">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet 패키지</span><span class="sxs-lookup"><span data-stu-id="eb10c-139">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet package</span></span>

## <a name="server-side-prerendering"></a><span data-ttu-id="eb10c-140">서버 측 사전 렌더링</span><span class="sxs-lookup"><span data-stu-id="eb10c-140">Server-side prerendering</span></span>

<span data-ttu-id="eb10c-141">유니버설(또는 동형) 응용 프로그램은 서버 및 클라이언트 양쪽 모두에서 실행할 수 있는 JavaScript 응용 프로그램입니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-141">A universal (also known as isomorphic) application is a JavaScript application capable of running both on the server and the client.</span></span> <span data-ttu-id="eb10c-142">Angular, React 및 기타 인기 있는 프레임워크는 이 응용 프로그램 개발 스타일을 위한 유니버설 플랫폼을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-142">Angular, React, and other popular frameworks provide a universal platform for this application development style.</span></span> <span data-ttu-id="eb10c-143">그 개념은 우선 서버에서 Node.js를 통해서 프레임워크 구성 요소를 렌더링한 다음 이후의 실행은 클라이언트에 위임하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-143">The idea is to first render the framework components on the server via Node.js, and then delegate further execution to the client.</span></span>

<span data-ttu-id="eb10c-144">SpaServices가 제공하는 ASP.NET Core [태그 도우미](xref:mvc/views/tag-helpers/intro)는 서버에서 JavaScript 함수를 호출하여 서버 측 사전 렌더링의 구현을 단순화합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-144">ASP.NET Core [Tag Helpers](xref:mvc/views/tag-helpers/intro) provided by SpaServices simplify the implementation of server-side prerendering by invoking the JavaScript functions on the server.</span></span>

### <a name="server-side-prerendering-prerequisites"></a><span data-ttu-id="eb10c-145">서버 쪽 렌더링 전 필수 구성 요소</span><span class="sxs-lookup"><span data-stu-id="eb10c-145">Server-side prerendering prerequisites</span></span>

<span data-ttu-id="eb10c-146">[Aspnet 렌더링](https://www.npmjs.com/package/aspnet-prerendering) npm 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-146">Install the [aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering) npm package:</span></span>

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a><span data-ttu-id="eb10c-147">서버 쪽 렌더링 전 구성</span><span class="sxs-lookup"><span data-stu-id="eb10c-147">Server-side prerendering configuration</span></span>

<span data-ttu-id="eb10c-148">태그 도우미는 프로젝트의 *_ViewImports.cshtml* 파일에서 네임스페이스를 등록하여 검색 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-148">The Tag Helpers are made discoverable via namespace registration in the project's *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

<span data-ttu-id="eb10c-149">태그 도우미는 Razor 뷰 내부에서 HTML과 유사한 구문을 활용하여 저수준 API와 직접 통신하는 복잡함을 추상화합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-149">These Tag Helpers abstract away the intricacies of communicating directly with low-level APIs by leveraging an HTML-like syntax inside the Razor view:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a><span data-ttu-id="eb10c-150">asp-prerender-모듈 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="eb10c-150">asp-prerender-module Tag Helper</span></span>

<span data-ttu-id="eb10c-151">위의 코드 예제에 사용된 `asp-prerender-module` 태그 도우미는 서버에서 Node.js를 통해서 *ClientApp/dist/main-server.js*를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-151">The `asp-prerender-module` Tag Helper, used in the preceding code example, executes *ClientApp/dist/main-server.js* on the server via Node.js.</span></span> <span data-ttu-id="eb10c-152">명확히 말하자면 *main-server.js* 파일은 [Webpack](https://webpack.github.io/) 빌드 프로세스의 TypeScript-JavaScript 소스 간 변환 작업의 결과물입니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-152">For clarity's sake, *main-server.js* file is an artifact of the TypeScript-to-JavaScript transpilation task in the [Webpack](https://webpack.github.io/) build process.</span></span> <span data-ttu-id="eb10c-153">Webpack은 `main-server`의 진입점 별칭과 *ClientApp/boot-server.ts* 파일에서 시작되는 이 별칭에 대한 종속성 그래프의 순회를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-153">Webpack defines an entry point alias of `main-server`; and, traversal of the dependency graph for this alias begins at the *ClientApp/boot-server.ts* file:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

<span data-ttu-id="eb10c-154">다음 Angular 예제에서 *ClientApp/boot-server.ts* 파일은 `aspnet-prerendering` npm 패키지의 `createServerRenderer` 함수 및 `RenderResult` 형식을 사용하여 Node.js를 통한 서버 렌더링을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-154">In the following Angular example, the *ClientApp/boot-server.ts* file utilizes the `createServerRenderer` function and `RenderResult` type of the `aspnet-prerendering` npm package to configure server rendering via Node.js.</span></span> <span data-ttu-id="eb10c-155">서버 측 렌더링에 사용될 HTML 마크업은 강력한 형식의 JavaScript `Promise` 개체로 래핑된 resolve 함수 호출로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-155">The HTML markup destined for server-side rendering is passed to a resolve function call, which is wrapped in a strongly-typed JavaScript `Promise` object.</span></span> <span data-ttu-id="eb10c-156">이 `Promise` 개체가 중요한 이유는 DOM의 자리 표시자 요소에 삽입하기 위해 비동기적으로 HTML의 마크업을 페이지에 제공하기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-156">The `Promise` object's significance is that it asynchronously supplies the HTML markup to the page for injection in the DOM's placeholder element.</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a><span data-ttu-id="eb10c-157">asp-prerender-데이터 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="eb10c-157">asp-prerender-data Tag Helper</span></span>

<span data-ttu-id="eb10c-158">`asp-prerender-module` 태그 도우미와 함께 `asp-prerender-data` 태그 도우미를 사용하면 Razor 뷰에서 서버 측 JavaScript로 컨텍스트 정보를 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-158">When coupled with the `asp-prerender-module` Tag Helper, the `asp-prerender-data` Tag Helper can be used to pass contextual information from the Razor view to the server-side JavaScript.</span></span> <span data-ttu-id="eb10c-159">예를 들어 다음 태그는 사용자 데이터를 `main-server` 모듈로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-159">For example, the following markup passes user data to the `main-server` module:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

<span data-ttu-id="eb10c-160">지정한 `UserName` 인수는 기본 제공 JSON 직렬 변환기를 이용하여 직렬화되고 `params.data` 개체에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-160">The received `UserName` argument is serialized using the built-in JSON serializer and is stored in the `params.data` object.</span></span> <span data-ttu-id="eb10c-161">다음의 Angular 예제는 이 데이터를 사용하여 `h1` 요소 내에 개인화된 인사말을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-161">In the following Angular example, the data is used to construct a personalized greeting within an `h1` element:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

<span data-ttu-id="eb10c-162">태그 도우미에 전달 된 속성 이름은 **대/소문자** 표기를 사용 하 여 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-162">Property names passed in Tag Helpers are represented with **PascalCase** notation.</span></span> <span data-ttu-id="eb10c-163">이와는 달리 JavaScript에서는 동일한 속성 이름이 **camelCase**로 표현됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-163">Contrast that to JavaScript, where the same property names are represented with **camelCase**.</span></span> <span data-ttu-id="eb10c-164">이 차이는 기본 JSON 직렬화 구성으로 인한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-164">The default JSON serialization configuration is responsible for this difference.</span></span>

<span data-ttu-id="eb10c-165">위의 코드 예제를 확장하기 위해 `resolve` 함수에 제공되는 `globals` 속성을 추가하여 데이터를 서버에서 뷰로 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-165">To expand upon the preceding code example, data can be passed from the server to the view by hydrating the `globals` property provided to the `resolve` function:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

<span data-ttu-id="eb10c-166">`globals` 개체 내에 정의된 `postList` 배열은 브라우저의 전역 `window` 개체에 연결됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-166">The `postList` array defined inside the `globals` object is attached to the browser's global `window` object.</span></span> <span data-ttu-id="eb10c-167">이렇게 전역 범위로 변수를 끌어올리면 특히 동일한 데이터를 서버에서 한 번 그리고 클라이언트에서 다시 한 번 로딩하는 작업과 관련된 반복적인 노력을 줄일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-167">This variable hoisting to global scope eliminates duplication of effort, particularly as it pertains to loading the same data once on the server and again on the client.</span></span>

![window 개체에 연결된 전역 postList 변수](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a><span data-ttu-id="eb10c-169">Webpack Dev 미들웨어</span><span class="sxs-lookup"><span data-stu-id="eb10c-169">Webpack Dev Middleware</span></span>

<span data-ttu-id="eb10c-170">[Webpack Dev 미들웨어](https://webpack.js.org/guides/development/#using-webpack-dev-middleware)는 필요할 때마다 Webpack이 리소스를 빌드하는 간결한 개발 워크플로를 도입합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-170">[Webpack Dev Middleware](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) introduces a streamlined development workflow whereby Webpack builds resources on demand.</span></span> <span data-ttu-id="eb10c-171">이 미들웨어는 페이지가 브라우저에서 다시 로드될 때 클라이언트 측 리소스를 자동으로 컴파일하고 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-171">The middleware automatically compiles and serves client-side resources when a page is reloaded in the browser.</span></span> <span data-ttu-id="eb10c-172">다른 대안은 타사의 종속성이나 사용자 지정 코드가 변경될 때 프로젝트의 npm 빌드 스크립트를 사용해서 직접 Webpack을 호출하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-172">The alternate approach is to manually invoke Webpack via the project's npm build script when a third-party dependency or the custom code changes.</span></span> <span data-ttu-id="eb10c-173">다음 예제는 *package.json* 파일의 npm 빌드 스크립트를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-173">An npm build script in the *package.json* file is shown in the following example:</span></span>

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a><span data-ttu-id="eb10c-174">Webpack Dev 미들웨어 필수 구성 요소</span><span class="sxs-lookup"><span data-stu-id="eb10c-174">Webpack Dev Middleware prerequisites</span></span>

<span data-ttu-id="eb10c-175">[Aspnet webpack](https://www.npmjs.com/package/aspnet-webpack) npm 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-175">Install the [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm package:</span></span>

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a><span data-ttu-id="eb10c-176">Webpack Dev 미들웨어 구성</span><span class="sxs-lookup"><span data-stu-id="eb10c-176">Webpack Dev Middleware configuration</span></span>

<span data-ttu-id="eb10c-177">Webpack Dev 미들웨어는 *Startup.cs* 파일의 `Configure` 메서드에서 다음 코드를 통해서 HTTP 요청 파이프라인에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-177">Webpack Dev Middleware is registered into the HTTP request pipeline via the following code in the *Startup.cs* file's `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

<span data-ttu-id="eb10c-178">`UseWebpackDevMiddleware` 확장 메서드는 `UseStaticFiles` 확장 메서드를 통해서 [정적 파일 호스팅을 등록](xref:fundamentals/static-files)하기 전에 미리 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-178">The `UseWebpackDevMiddleware` extension method must be called before [registering static file hosting](xref:fundamentals/static-files) via the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="eb10c-179">보안 상의 이유로 앱이 개발 모드에서 실행될 때만 이 미들웨어를 등록해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-179">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="eb10c-180">*webpack.config.js* 파일의 `output.publicPath` 속성은 미들웨어에게 `dist` 폴더의 변경 사항을 모니터링하도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-180">The *webpack.config.js* file's `output.publicPath` property tells the middleware to watch the `dist` folder for changes:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a><span data-ttu-id="eb10c-181">실시간 모듈 교체</span><span class="sxs-lookup"><span data-stu-id="eb10c-181">Hot Module Replacement</span></span>

<span data-ttu-id="eb10c-182">Webpack의 [실시간 모듈 교체(HMR)](https://webpack.js.org/concepts/hot-module-replacement/) 기능은 [Webpack Dev 미들웨어](#webpack-dev-middleware)가 진화한 것으로 생각하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-182">Think of Webpack's [Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) feature as an evolution of [Webpack Dev Middleware](#webpack-dev-middleware).</span></span> <span data-ttu-id="eb10c-183">HMR은 모든 동일한 이점을 제공할 뿐만 아니라 변경 사항을 컴파일 한 뒤 페이지의 내용을 자동으로 갱신하여 개발 워크플로우를 더욱 간소화합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-183">HMR introduces all the same benefits, but it further streamlines the development workflow by automatically updating page content after compiling the changes.</span></span> <span data-ttu-id="eb10c-184">SPA의 현재 메모리 내 상태와 디버깅 세션을 방해하지 않도록 이 기능과 브라우저 새로 고침을 혼동하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="eb10c-184">Don't confuse this with a refresh of the browser, which would interfere with the current in-memory state and debugging session of the SPA.</span></span> <span data-ttu-id="eb10c-185">Webpack Dev 미들웨어 서비스와 브라우저 간에는 실시간 링크가 존재하며, 이 얘기는 변경 사항이 브라우저로 푸시된다는 뜻입니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-185">There's a live link between the Webpack Dev Middleware service and the browser, which means changes are pushed to the browser.</span></span>

### <a name="hot-module-replacement-prerequisites"></a><span data-ttu-id="eb10c-186">핫 모듈 교체 필수 조건</span><span class="sxs-lookup"><span data-stu-id="eb10c-186">Hot Module Replacement prerequisites</span></span>

<span data-ttu-id="eb10c-187">[Webpack-핫 미들웨어](https://www.npmjs.com/package/webpack-hot-middleware) npm 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-187">Install the [webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm package:</span></span>

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a><span data-ttu-id="eb10c-188">핫 모듈 교체 구성</span><span class="sxs-lookup"><span data-stu-id="eb10c-188">Hot Module Replacement configuration</span></span>

<span data-ttu-id="eb10c-189">`Configure` 메서드에서 MVC의 HTTP 요청 파이프라인에 HMR 구성 요소를 등록해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-189">The HMR component must be registered into MVC's HTTP request pipeline in the `Configure` method:</span></span>

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

<span data-ttu-id="eb10c-190">[Webpack Dev 미들웨어](#webpack-dev-middleware)의 경우와 마찬가지로`UseStaticFiles` 확장 메서드를 호출 하기 전에 `UseWebpackDevMiddleware` 확장 메서드를 먼저 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-190">As was true with [Webpack Dev Middleware](#webpack-dev-middleware), the `UseWebpackDevMiddleware` extension method must be called before the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="eb10c-191">보안 상의 이유로 앱이 개발 모드에서 실행될 때만 이 미들웨어를 등록해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-191">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="eb10c-192">*webpack.config.js* 파일에서는 `plugins` 배열이 비어 있더라도 이를 정의해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-192">The *webpack.config.js* file must define a `plugins` array, even if it's left empty:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

<span data-ttu-id="eb10c-193">브라우저에서 앱을 로딩한 다음, 개발자 도구의 콘솔 탭에서 HMR의 활성화를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-193">After loading the app in the browser, the developer tools' Console tab provides confirmation of HMR activation:</span></span>

![실시간 모듈 교체 연결 메시지](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a><span data-ttu-id="eb10c-195">라우팅 도우미</span><span class="sxs-lookup"><span data-stu-id="eb10c-195">Routing helpers</span></span>

<span data-ttu-id="eb10c-196">대부분의 ASP.NET Core 기반 SPAs에서 클라이언트 쪽 라우팅은 서버 쪽 라우팅 외에도 필요한 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-196">In most ASP.NET Core-based SPAs, client-side routing is often desired in addition to server-side routing.</span></span> <span data-ttu-id="eb10c-197">SPA 및 MVC 라우팅 시스템은 서로 간섭 없이 독립적으로 동작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-197">The SPA and MVC routing systems can work independently without interference.</span></span> <span data-ttu-id="eb10c-198">그러나 404 HTTP 응답 식별이라는 한 가지 까다로운 문제가 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-198">There's, however, one edge case posing challenges: identifying 404 HTTP responses.</span></span>

<span data-ttu-id="eb10c-199">확장자가 존재하지 않는 `/some/page`라는 경로가 사용되는 시나리오를 생각해보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-199">Consider the scenario in which an extensionless route of `/some/page` is used.</span></span> <span data-ttu-id="eb10c-200">요청이 서버 측 경로와는 패턴이 일치하지 않지만 클라이언트 측 경로와는 패턴이 일치한다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-200">Assume the request doesn't pattern-match a server-side route, but its pattern does match a client-side route.</span></span> <span data-ttu-id="eb10c-201">이제 일반적으로 서버에서 이미지 파일을 찾는 것으로 예상되는 `/images/user-512.png`에 대한 들어오는 요청을 생각해보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-201">Now consider an incoming request for `/images/user-512.png`, which generally expects to find an image file on the server.</span></span> <span data-ttu-id="eb10c-202">요청 된 리소스 경로가 서버 쪽 경로 또는 정적 파일과 일치 하지 않을 경우 일반적으로 404 HTTP 상태 코드를 반환 하&mdash;클라이언트 쪽 응용 프로그램에서이를 처리할 가능성이 거의 없습니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-202">If that requested resource path doesn't match any server-side route or static file, it's unlikely that the client-side application would handle it&mdash;generally returning a 404 HTTP status code is desired.</span></span>

### <a name="routing-helpers-prerequisites"></a><span data-ttu-id="eb10c-203">라우팅 도우미 필수 조건</span><span class="sxs-lookup"><span data-stu-id="eb10c-203">Routing helpers prerequisites</span></span>

<span data-ttu-id="eb10c-204">클라이언트 쪽 라우팅 npm 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-204">Install the client-side routing npm package.</span></span> <span data-ttu-id="eb10c-205">예를 들어 Angular를 사용하는 경우입니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-205">Using Angular as an example:</span></span>

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a><span data-ttu-id="eb10c-206">라우팅 도우미 구성</span><span class="sxs-lookup"><span data-stu-id="eb10c-206">Routing helpers configuration</span></span>

<span data-ttu-id="eb10c-207">`Configure` 메서드에서 `MapSpaFallbackRoute`라는 확장 메서드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-207">An extension method named `MapSpaFallbackRoute` is used in the `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

<span data-ttu-id="eb10c-208">경로는 구성 된 순서 대로 평가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-208">Routes are evaluated in the order in which they're configured.</span></span> <span data-ttu-id="eb10c-209">따라서 앞의 코드 예제에서는 `default` 경로가 가장 먼저 패턴 일치에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-209">Consequently, the `default` route in the preceding code example is used first for pattern matching.</span></span>

## <a name="create-a-new-project"></a><span data-ttu-id="eb10c-210">새 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="eb10c-210">Create a new project</span></span>

<span data-ttu-id="eb10c-211">JavaScript 서비스는 미리 구성 된 응용 프로그램 템플릿을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-211">JavaScript Services provide pre-configured application templates.</span></span> <span data-ttu-id="eb10c-212">SpaServices는 이러한 템플릿에서 각 프레임 워크 및 라이브러리 (예: 각도, 반응, Redux)와 함께 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-212">SpaServices is used in these templates in conjunction with different frameworks and libraries such as Angular, React, and Redux.</span></span>

<span data-ttu-id="eb10c-213">이러한 템플릿들은 다음 명령을 실행하여 .NET Core CLI를 통해서 설치할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-213">These templates can be installed via the .NET Core CLI by running the following command:</span></span>

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

<span data-ttu-id="eb10c-214">사용 가능한 SPA 템플릿 목록은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-214">A list of available SPA templates is displayed:</span></span>

| <span data-ttu-id="eb10c-215">템플릿</span><span class="sxs-lookup"><span data-stu-id="eb10c-215">Templates</span></span>                                 | <span data-ttu-id="eb10c-216">짧은 이름</span><span class="sxs-lookup"><span data-stu-id="eb10c-216">Short Name</span></span> | <span data-ttu-id="eb10c-217">Language</span><span class="sxs-lookup"><span data-stu-id="eb10c-217">Language</span></span> | <span data-ttu-id="eb10c-218">Tags</span><span class="sxs-lookup"><span data-stu-id="eb10c-218">Tags</span></span>        |
| ------------------------------------------| :--------: | :------: | :---------: |
| <span data-ttu-id="eb10c-219">MVC ASP.NET Core with Angular</span><span class="sxs-lookup"><span data-stu-id="eb10c-219">MVC ASP.NET Core with Angular</span></span>             | <span data-ttu-id="eb10c-220">angular</span><span class="sxs-lookup"><span data-stu-id="eb10c-220">angular</span></span>    | <span data-ttu-id="eb10c-221">[C#]</span><span class="sxs-lookup"><span data-stu-id="eb10c-221">[C#]</span></span>     | <span data-ttu-id="eb10c-222">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="eb10c-222">Web/MVC/SPA</span></span> |
| <span data-ttu-id="eb10c-223">MVC ASP.NET Core with React.js</span><span class="sxs-lookup"><span data-stu-id="eb10c-223">MVC ASP.NET Core with React.js</span></span>            | <span data-ttu-id="eb10c-224">react</span><span class="sxs-lookup"><span data-stu-id="eb10c-224">react</span></span>      | <span data-ttu-id="eb10c-225">[C#]</span><span class="sxs-lookup"><span data-stu-id="eb10c-225">[C#]</span></span>     | <span data-ttu-id="eb10c-226">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="eb10c-226">Web/MVC/SPA</span></span> |
| <span data-ttu-id="eb10c-227">MVC ASP.NET Core with React.js and Redux</span><span class="sxs-lookup"><span data-stu-id="eb10c-227">MVC ASP.NET Core with React.js and Redux</span></span>  | <span data-ttu-id="eb10c-228">reactredux</span><span class="sxs-lookup"><span data-stu-id="eb10c-228">reactredux</span></span> | <span data-ttu-id="eb10c-229">[C#]</span><span class="sxs-lookup"><span data-stu-id="eb10c-229">[C#]</span></span>     | <span data-ttu-id="eb10c-230">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="eb10c-230">Web/MVC/SPA</span></span> |

<span data-ttu-id="eb10c-231">SPA 템플릿 중 하나를 사용하여 새로운 프로젝트를 만들려면 [dotnet new](/dotnet/core/tools/dotnet-new) 명령 뒤에 템플릿의 **약식 이름**을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-231">To create a new project using one of the SPA templates, include the **Short Name** of the template in the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="eb10c-232">다음 명령은 서버 측에 ASP.NET Core MVC가 구성된 Angular 응용 프로그램을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-232">The following command creates an Angular application with ASP.NET Core MVC configured for the server side:</span></span>

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a><span data-ttu-id="eb10c-233">런타임 구성 모드 설정</span><span class="sxs-lookup"><span data-stu-id="eb10c-233">Set the runtime configuration mode</span></span>

<span data-ttu-id="eb10c-234">다음과 같은 두 가지 기본 런타임 구성 모드가 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-234">Two primary runtime configuration modes exist:</span></span>

* <span data-ttu-id="eb10c-235">**개발**:</span><span class="sxs-lookup"><span data-stu-id="eb10c-235">**Development**:</span></span>
  * <span data-ttu-id="eb10c-236">손쉬운 디버깅을 위해 소스 맵이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-236">Includes source maps to ease debugging.</span></span>
  * <span data-ttu-id="eb10c-237">성능을 위한 클라이언트 측 코드를 최적화하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-237">Doesn't optimize the client-side code for performance.</span></span>
* <span data-ttu-id="eb10c-238">**프로덕션**:</span><span class="sxs-lookup"><span data-stu-id="eb10c-238">**Production**:</span></span>
  * <span data-ttu-id="eb10c-239">소스 맵을 제외합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-239">Excludes source maps.</span></span>
  * <span data-ttu-id="eb10c-240">묶음 및 축소를 통해 클라이언트 쪽 코드를 최적화 합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-240">Optimizes the client-side code via bundling and minification.</span></span>

<span data-ttu-id="eb10c-241">ASP.NET Core는 `ASPNETCORE_ENVIRONMENT` 라는 환경 변수를 사용하여 구성 모드를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-241">ASP.NET Core uses an environment variable named `ASPNETCORE_ENVIRONMENT` to store the configuration mode.</span></span> <span data-ttu-id="eb10c-242">자세한 내용은 [환경 설정](xref:fundamentals/environments#set-the-environment)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="eb10c-242">For more information, see [Set the environment](xref:fundamentals/environments#set-the-environment).</span></span>

### <a name="run-with-net-core-cli"></a><span data-ttu-id="eb10c-243">.NET Core CLI를 사용 하 여 실행</span><span class="sxs-lookup"><span data-stu-id="eb10c-243">Run with .NET Core CLI</span></span>

<span data-ttu-id="eb10c-244">다음 명령을 프로젝트 루트에서 실행하여 필요한 NuGet 및 npm 패키지를 복원합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-244">Restore the required NuGet and npm packages by running the following command at the project root:</span></span>

```dotnetcli
dotnet restore && npm i
```

<span data-ttu-id="eb10c-245">응용 프로그램을 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-245">Build and run the application:</span></span>

```dotnetcli
dotnet run
```

<span data-ttu-id="eb10c-246">응용 프로그램은 [런타임 구성 모드](#set-the-runtime-configuration-mode)에 따라 localhost에서 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-246">The application starts on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span> <span data-ttu-id="eb10c-247">브라우저에서 `http://localhost:5000`로 이동하면 방문 페이지가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-247">Navigating to `http://localhost:5000` in the browser displays the landing page.</span></span>

### <a name="run-with-visual-studio-2017"></a><span data-ttu-id="eb10c-248">Visual Studio 2017에서 실행</span><span class="sxs-lookup"><span data-stu-id="eb10c-248">Run with Visual Studio 2017</span></span>

<span data-ttu-id="eb10c-249">[dotnet new](/dotnet/core/tools/dotnet-new) 명령으로 생성한 *.csproj* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-249">Open the *.csproj* file generated by the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="eb10c-250">필요한 NuGet 및 npm 패키지는 프로젝트가 열릴 때 자동으로 복원됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-250">The required NuGet and npm packages are restored automatically upon project open.</span></span> <span data-ttu-id="eb10c-251">이 복원 프로세스는 최대 몇 분 정도 걸릴 수 있으며 완료되면 응용 프로그램을 실행할 준비가 완료됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-251">This restoration process may take up to a few minutes, and the application is ready to run when it completes.</span></span> <span data-ttu-id="eb10c-252">녹색 실행 버튼을 클릭하거나 `Ctrl + F5` 키를 누르면 브라우저가 응용 프로그램의 방문 페이지를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-252">Click the green run button or press `Ctrl + F5`, and the browser opens to the application's landing page.</span></span> <span data-ttu-id="eb10c-253">응용 프로그램은 [런타임 구성 모드](#set-the-runtime-configuration-mode)에 따라 localhost에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-253">The application runs on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span>

## <a name="test-the-app"></a><span data-ttu-id="eb10c-254">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-254">Test the app</span></span>

<span data-ttu-id="eb10c-255">SpaServices 템플릿은 [Karma](https://karma-runner.github.io/1.0/index.html)와 [Jasmine](https://jasmine.github.io/)을 사용하여 클라이언트 측 테스트를 실행하도록 미리 구성되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-255">SpaServices templates are pre-configured to run client-side tests using [Karma](https://karma-runner.github.io/1.0/index.html) and [Jasmine](https://jasmine.github.io/).</span></span> <span data-ttu-id="eb10c-256">Jasmine은 인기 있는 JavaScript 용 단위 테스트 프레임워크인 반면, Karma는 해당 테스트에 대한 테스트 러너입니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-256">Jasmine is a popular unit testing framework for JavaScript, whereas Karma is a test runner for those tests.</span></span> <span data-ttu-id="eb10c-257">Karma는 [Webpack Dev 미들웨어](#webpack-dev-middleware)와 함께 동작하도록 구성되어 있으므로 개발자는 변경 사항이 있을 때마다 매번 중단하고 테스트를 실행할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-257">Karma is configured to work with the [Webpack Dev Middleware](#webpack-dev-middleware) such that the developer isn't required to stop and run the test every time changes are made.</span></span> <span data-ttu-id="eb10c-258">테스트 케이스에 대해 실행 중인 코드든 테스트 케이스 자체든 상관없이 테스트가 자동으로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-258">Whether it's the code running against the test case or the test case itself, the test runs automatically.</span></span>

<span data-ttu-id="eb10c-259">Angular 응용 프로그램을 예로 들면, *counter.component.spec.ts* 파일에 `CounterComponent`에 대한 두 가지 Jasmine 테스트 케이스가 미리 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-259">Using the Angular application as an example, two Jasmine test cases are already provided for the `CounterComponent` in the *counter.component.spec.ts* file:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

<span data-ttu-id="eb10c-260">*ClientApp* 디렉터리에서 명령 프롬프트를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-260">Open the command prompt in the *ClientApp* directory.</span></span> <span data-ttu-id="eb10c-261">명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-261">Run the following command:</span></span>

```console
npm test
```

<span data-ttu-id="eb10c-262">이 스크립트는 *karma.conf.js* 파일에 정의된 설정을 읽는 Karma 테스트 러너를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-262">The script launches the Karma test runner, which reads the settings defined in the *karma.conf.js* file.</span></span> <span data-ttu-id="eb10c-263">*karma.conf.js*는 다른 설정들 중에서 `files` 배열을 통해서 실행될 테스트 파일을 식별합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-263">Among other settings, the *karma.conf.js* identifies the test files to be executed via its `files` array:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a><span data-ttu-id="eb10c-264">앱 게시</span><span class="sxs-lookup"><span data-stu-id="eb10c-264">Publish the app</span></span>

<span data-ttu-id="eb10c-265">Azure에 게시 하는 방법에 대 한 자세한 내용은 [이 GitHub 문제](https://github.com/aspnet/AspNetCore.Docs/issues/12474) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="eb10c-265">See [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/12474) for more information on publishing to Azure.</span></span>

<span data-ttu-id="eb10c-266">생성된 클라이언트 측 자산과 게시된 ASP.NET Core의 결과물을 배포 준비 패키지로 결합하는 일은 번거로운 작업일 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-266">Combining the generated client-side assets and the published ASP.NET Core artifacts into a ready-to-deploy package can be cumbersome.</span></span> <span data-ttu-id="eb10c-267">다행스럽게도 SpaServices는 `RunWebpack`이라는 이름의 사용자 지정 MSBuild 대상으로 전체 게시 프로세스를 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-267">Thankfully, SpaServices orchestrates that entire publication process with a custom MSBuild target named `RunWebpack`:</span></span>

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

<span data-ttu-id="eb10c-268">이 MSBuild 대상은 다음과 같은 작업을 책임집니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-268">The MSBuild target has the following responsibilities:</span></span>

1. <span data-ttu-id="eb10c-269">Npm 패키지를 복원 합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-269">Restore the npm packages.</span></span>
1. <span data-ttu-id="eb10c-270">타사 클라이언트 쪽 자산의 프로덕션 등급 빌드를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-270">Create a production-grade build of the third-party, client-side assets.</span></span>
1. <span data-ttu-id="eb10c-271">사용자 지정 클라이언트 쪽 자산의 프로덕션 등급 빌드를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-271">Create a production-grade build of the custom client-side assets.</span></span>
1. <span data-ttu-id="eb10c-272">Webpack에서 생성 된 자산을 게시 폴더에 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-272">Copy the Webpack-generated assets to the publish folder.</span></span>

<span data-ttu-id="eb10c-273">다음을 실행하면 MSBuild 대상이 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="eb10c-273">The MSBuild target is invoked when running:</span></span>

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a><span data-ttu-id="eb10c-274">추가 자료</span><span class="sxs-lookup"><span data-stu-id="eb10c-274">Additional resources</span></span>

* [<span data-ttu-id="eb10c-275">Angular Docs</span><span class="sxs-lookup"><span data-stu-id="eb10c-275">Angular Docs</span></span>](https://angular.io/docs)
