---
title: ASP.NET Core 소개
author: rick-anderson
description: 최신 클라우드 기반 인터넷 연결 응용 프로그램을 빌드하기 위한 플랫폼 간 고성능 오픈 소스 프레임워크인 ASP.NET Core를 소개합니다.
ms.author: riande
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- SignalR
uid: index
ms.openlocfilehash: 3e41b329feb359c006258b8ed558496dfcbbf3b0
ms.sourcegitcommit: 57b85708f4cded99b8f008a69830cb104cd8e879
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/13/2020
ms.locfileid: "75914219"
---
# <a name="introduction-to-aspnet-core"></a><span data-ttu-id="2464b-103">ASP.NET Core 소개</span><span class="sxs-lookup"><span data-stu-id="2464b-103">Introduction to ASP.NET Core</span></span>

<span data-ttu-id="2464b-104">작성자: [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Shaun Luttin](https://twitter.com/dicshaunary)</span><span class="sxs-lookup"><span data-stu-id="2464b-104">By [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Shaun Luttin](https://twitter.com/dicshaunary)</span></span>

<span data-ttu-id="2464b-105">ASP.NET Core는 최신 클라우드 기반 인터넷 연결 응용 프로그램을 빌드하기 위한 플랫폼 간 고성능 [오픈 소스](https://github.com/aspnet/home) 프레임워크입니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-105">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/aspnet/home) framework for building modern, cloud-based, Internet-connected applications.</span></span> <span data-ttu-id="2464b-106">ASP.NET Core를 사용하여 다음과 같은 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-106">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="2464b-107">웹앱 및 서비스, [IoT](https://www.microsoft.com/internet-of-things/) 앱 및 모바일 백 엔드를 빌드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-107">Build web apps and services, [IoT](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="2464b-108">Windows, macOS 및 Linux에서 선호하는 개발 도구를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-108">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="2464b-109">클라우드 또는 온-프레미스에 배포할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-109">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="2464b-110">[.NET Core 또는.NET Framework](/dotnet/articles/standard/choosing-core-framework-server)에서 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-110">Run on [.NET Core or .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span></span>

## <a name="why-choose-aspnet-core"></a><span data-ttu-id="2464b-111">ASP.NET Core를 선택해야 하는 이유</span><span class="sxs-lookup"><span data-stu-id="2464b-111">Why choose ASP.NET Core?</span></span>

<span data-ttu-id="2464b-112">수백만 명의 개발자가 [ASP.NET 4.x](/aspnet/overview)를 사용하여 웹앱을 만들었고 계속 만들고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-112">Millions of developers have used (and continue to use) [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="2464b-113">ASP.NET Core는 보다 간결하고 모듈화된 프레임워크를 만드는 아키텍처 변경 내용이 담긴 ASP.NET 4.x의 재설계입니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-113">ASP.NET Core is a redesign of ASP.NET 4.x, with architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="2464b-114">ASP.NET Core MVC를 사용하여 웹 API 및 웹 UI 빌드</span><span class="sxs-lookup"><span data-stu-id="2464b-114">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="2464b-115">ASP.NET Core MVC는 [Web API](xref:tutorials/first-web-api) 및 [웹앱](xref:tutorials/razor-pages/index)을 만들기 위한 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-115">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="2464b-116">[MVC(모델-뷰-컨트롤러) 패턴](xref:mvc/overview)을 통해 웹 API 및 웹앱을 테스트 가능하게 합니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-116">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="2464b-117">[Razor Pages](xref:razor-pages/index)는 웹 UI를 쉽게 빌드하고 생산성을 높일 수 있는 페이지 기반 프로그래밍 모델입니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-117">[Razor Pages](xref:razor-pages/index) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="2464b-118">[Razor 태그](xref:mvc/views/razor)는 [Razor 페이지](xref:razor-pages/index) 및 [MVC 보기](xref:mvc/views/overview)를 위한 생산적인 구문을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-118">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="2464b-119">[태그 도우미](xref:mvc/views/tag-helpers/intro)를 사용하면 Razor 파일에서 HTML 요소를 만들고 렌더링하는 데 서버 쪽 코드를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-119">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="2464b-120">[다양한 데이터 형식 및 콘텐츠 협상](xref:web-api/advanced/formatting)에 대한 기본 제공 지원을 통해 웹 API를 브라우저 및 모바일 디바이스를 포함한 다양한 클라이언트에 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-120">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="2464b-121">[모델 바인딩](xref:mvc/models/model-binding)은 HTTP 요청의 데이터를 작업 메서드 매개 변수에 자동으로 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-121">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="2464b-122">[모델 유효성 검사](xref:mvc/models/validation)는 클라이언트 쪽 및 서버 쪽 유효성 검사를 자동으로 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-122">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="2464b-123">클라이언트 쪽 개발</span><span class="sxs-lookup"><span data-stu-id="2464b-123">Client-side development</span></span>

<span data-ttu-id="2464b-124">ASP.NET Core는 [[!OP.NO-LOC(Blazor)]](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react) 및 [부트스트랩](https://getbootstrap.com/) 등 유명한 클라이언트 쪽 프레임워크 및 라이브러리와 원활하게 통합됩니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-124">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [[!OP.NO-LOC(Blazor)]](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="2464b-125">자세한 내용은 <xref:blazor/index> 및 ‘클라이언트 쪽 개발’의 관련 항목을 참조하세요. </span><span class="sxs-lookup"><span data-stu-id="2464b-125">For more information, see <xref:blazor/index> and related topics under *Client-side development*.</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a><span data-ttu-id="2464b-126">ASP.NET Core 대상 .NET Framework</span><span class="sxs-lookup"><span data-stu-id="2464b-126">ASP.NET Core targeting .NET Framework</span></span>

<span data-ttu-id="2464b-127">ASP.NET Core 2.x는 .NET Core 또는 .NET Framework를 대상으로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-127">ASP.NET Core 2.x can target .NET Core or .NET Framework.</span></span> <span data-ttu-id="2464b-128">.NET Framework를 대상으로 지정한 ASP.NET Core 앱은 플랫폼 간 교차 사용이 불가능하며&mdash;Windows에서만 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-128">ASP.NET Core apps targeting .NET Framework aren't cross-platform&mdash;they run on Windows only.</span></span> <span data-ttu-id="2464b-129">일반적으로 ASP.NET Core 2.x는 [.NET 표준](/dotnet/standard/net-standard) 라이브러리로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-129">Generally, ASP.NET Core 2.x is made up of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="2464b-130">.NET Standard 2.0으로 작성된 라이브러리는 [.NET Standard 2.0을 구현하는 모든 .NET 플랫폼](/dotnet/standard/net-standard#net-implementation-support)에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-130">Libraries written with .NET Standard 2.0 run on any [.NET platform that implements .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span></span>

<span data-ttu-id="2464b-131">ASP.NET Core 2.x는 .NET Standard 2.0을 구현하는 .NET Framework 버전에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-131">ASP.NET Core 2.x is supported on .NET Framework versions that implement .NET Standard 2.0:</span></span>

* <span data-ttu-id="2464b-132">.NET Framework 최신 버전을 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-132">.NET Framework latest version is strongly recommended.</span></span>
* <span data-ttu-id="2464b-133">.NET Framework 4.6.1 이상</span><span class="sxs-lookup"><span data-stu-id="2464b-133">.NET Framework 4.6.1 and later.</span></span>

<span data-ttu-id="2464b-134">ASP.NET Core 3.0 이상은 .NET Core에서만 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-134">ASP.NET Core 3.0 and later will only run on .NET Core.</span></span> <span data-ttu-id="2464b-135">이 변경 사항에 대한 자세한 내용은 [ASP.NET Core 3.0에 도입되는 변경 사항 개요](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2464b-135">For more details regarding this change, see [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<span data-ttu-id="2464b-136">.NET Core를 대상으로 지정하면 여러 이점이 있으며 이러한 이점은 각 릴리스마다 늘어나고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-136">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="2464b-137">.NET Framework에 비해 .NET Core를 사용하여 얻을 수 있는 몇 가지 이점은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-137">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="2464b-138">플랫폼 간 사용 가능.</span><span class="sxs-lookup"><span data-stu-id="2464b-138">Cross-platform.</span></span> <span data-ttu-id="2464b-139">macOS, Linux 및 Windows에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-139">Runs on macOS, Linux, and Windows.</span></span>
* <span data-ttu-id="2464b-140">향상된 성능</span><span class="sxs-lookup"><span data-stu-id="2464b-140">Improved performance</span></span>
* [<span data-ttu-id="2464b-141">Side-by-side 버전 관리</span><span class="sxs-lookup"><span data-stu-id="2464b-141">Side-by-side versioning</span></span>](/dotnet/standard/choosing-core-framework-server#a-need-for-side-by-side-of-net-versions-per-application-level)
* <span data-ttu-id="2464b-142">새로운 API</span><span class="sxs-lookup"><span data-stu-id="2464b-142">New APIs</span></span>
* <span data-ttu-id="2464b-143">오픈 소스</span><span class="sxs-lookup"><span data-stu-id="2464b-143">Open source</span></span>

<span data-ttu-id="2464b-144">.NET Framework와 .NET Core 간의 API 격차를 줄이기 위해 최선을 다하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-144">We're working hard to close the API gap from .NET Framework to .NET Core.</span></span> <span data-ttu-id="2464b-145">[Windows 호환 팩](/dotnet/core/porting/windows-compat-pack)을 통해 수천 개의 Windows 전용 API를 .NET Core에서도 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-145">The [Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) made thousands of Windows-only APIs available in .NET Core.</span></span> <span data-ttu-id="2464b-146">이러한 API는 .NET Core 1.x에서는 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-146">These APIs weren't available in .NET Core 1.x.</span></span>

## <a name="recommended-learning-path"></a><span data-ttu-id="2464b-147">권장되는 학습 경로</span><span class="sxs-lookup"><span data-stu-id="2464b-147">Recommended learning path</span></span>

<span data-ttu-id="2464b-148">ASP.NET Core 앱 개발을 소개하는 자습서 및 문서는 다음의 순서대로 살펴보는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-148">We recommend the following sequence of tutorials and articles for an introduction to developing ASP.NET Core apps:</span></span>

1. <span data-ttu-id="2464b-149">개발하거나 유지 관리하려는 앱 형식에 대한 자습서를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="2464b-149">Follow a tutorial for the type of app you want to develop or maintain:</span></span>

   |<span data-ttu-id="2464b-150">앱 형식</span><span class="sxs-lookup"><span data-stu-id="2464b-150">App type</span></span>  |<span data-ttu-id="2464b-151">시나리오</span><span class="sxs-lookup"><span data-stu-id="2464b-151">Scenario</span></span>  |<span data-ttu-id="2464b-152">자습서</span><span class="sxs-lookup"><span data-stu-id="2464b-152">Tutorial</span></span>  |
   |----------|----------|----------|
   |<span data-ttu-id="2464b-153">웹앱</span><span class="sxs-lookup"><span data-stu-id="2464b-153">Web app</span></span>                   | <span data-ttu-id="2464b-154">새로운 개발의 경우</span><span class="sxs-lookup"><span data-stu-id="2464b-154">For new development</span></span>        |[<span data-ttu-id="2464b-155">Razor 페이지 시작</span><span class="sxs-lookup"><span data-stu-id="2464b-155">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start) |
   |<span data-ttu-id="2464b-156">웹앱</span><span class="sxs-lookup"><span data-stu-id="2464b-156">Web app</span></span>                   | <span data-ttu-id="2464b-157">MVC 앱을 유지 관리하는 경우</span><span class="sxs-lookup"><span data-stu-id="2464b-157">For maintaining an MVC app</span></span> |[<span data-ttu-id="2464b-158">MVC 시작</span><span class="sxs-lookup"><span data-stu-id="2464b-158">Get started with MVC</span></span>](xref:tutorials/first-mvc-app/start-mvc)|
   |<span data-ttu-id="2464b-159">Web API</span><span class="sxs-lookup"><span data-stu-id="2464b-159">Web API</span></span>                   |                            |<span data-ttu-id="2464b-160">[웹 API 만들기](xref:tutorials/first-web-api)\*</span><span class="sxs-lookup"><span data-stu-id="2464b-160">[Create a web API](xref:tutorials/first-web-api)\*</span></span>  |
   |<span data-ttu-id="2464b-161">실시간 앱</span><span class="sxs-lookup"><span data-stu-id="2464b-161">Real-time app</span></span>             |                            |<span data-ttu-id="2464b-162">[[!OP.NO-LOC(SignalR)] 시작하기](xref:tutorials/signalr)</span><span class="sxs-lookup"><span data-stu-id="2464b-162">[Get started with [!OP.NO-LOC(SignalR)]](xref:tutorials/signalr)</span></span> |
   |[!OP.NO-LOC(Blazor)]<span data-ttu-id="2464b-163"> 앱</span><span class="sxs-lookup"><span data-stu-id="2464b-163"> app</span></span>                |                            |<span data-ttu-id="2464b-164">[[!OP.NO-LOC(Blazor)] 시작하기](xref:blazor/get-started)</span><span class="sxs-lookup"><span data-stu-id="2464b-164">[Get started with [!OP.NO-LOC(Blazor)]](xref:blazor/get-started)</span></span> |
   |<span data-ttu-id="2464b-165">원격 프로시저 호출 앱</span><span class="sxs-lookup"><span data-stu-id="2464b-165">Remote Procedure Call app</span></span> |                            |[<span data-ttu-id="2464b-166">gRPC 서비스 시작</span><span class="sxs-lookup"><span data-stu-id="2464b-166">Get started with a gRPC service</span></span>](xref:tutorials/grpc/grpc-start) |

1. <span data-ttu-id="2464b-167">기본 데이터 액세스를 수행하는 방법을 보여주는 자습서를 살펴보세요.</span><span class="sxs-lookup"><span data-stu-id="2464b-167">Follow a tutorial that shows how to do basic data access:</span></span>

   |<span data-ttu-id="2464b-168">시나리오</span><span class="sxs-lookup"><span data-stu-id="2464b-168">Scenario</span></span>  |<span data-ttu-id="2464b-169">자습서</span><span class="sxs-lookup"><span data-stu-id="2464b-169">Tutorial</span></span>  |
   |----------|----------|
   | <span data-ttu-id="2464b-170">새로운 개발의 경우</span><span class="sxs-lookup"><span data-stu-id="2464b-170">For new development</span></span>        |[<span data-ttu-id="2464b-171">Entity Framework Core를 사용한 Razor Pages</span><span class="sxs-lookup"><span data-stu-id="2464b-171">Razor Pages with Entity Framework Core</span></span>](xref:data/ef-rp/intro) |
   | <span data-ttu-id="2464b-172">MVC 앱을 유지 관리하는 경우</span><span class="sxs-lookup"><span data-stu-id="2464b-172">For maintaining an MVC app</span></span> |[<span data-ttu-id="2464b-173">Entity Framework Core를 사용한 MVC</span><span class="sxs-lookup"><span data-stu-id="2464b-173">MVC with Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

1. <span data-ttu-id="2464b-174">모든 앱 형식에 적용되는 ASP.NET Core 기능의 개요를 참고하세요.</span><span class="sxs-lookup"><span data-stu-id="2464b-174">Read an overview of ASP.NET Core features that apply to all app types:</span></span>

   * [<span data-ttu-id="2464b-175">기본 사항</span><span class="sxs-lookup"><span data-stu-id="2464b-175">Fundamentals</span></span>](xref:fundamentals/index)

1. <span data-ttu-id="2464b-176">관심 있는 다른 항목은 목차를 찾아보세요.</span><span class="sxs-lookup"><span data-stu-id="2464b-176">Browse the Table of Contents for other topics of interest.</span></span>

<span data-ttu-id="2464b-177">\*로컬 IDE를 설치할 필요 없이, [브라우저에서 모든 과정을 따라해볼 수 있는 새로운 웹 API 자습서](https://docs.microsoft.com/learn/modules/build-web-api-net-core)가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-177">\* There is a new [web API tutorial that you follow entirely in the browser](https://docs.microsoft.com/learn/modules/build-web-api-net-core), no local IDE installation required.</span></span>  <span data-ttu-id="2464b-178">해당 코드는 [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)에서 실행되고, 테스트에는 [curl](https://curl.haxx.se/)이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-178">The code runs in an [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), and [curl](https://curl.haxx.se/) is used for testing.</span></span>

## <a name="migration-from-the-net-framework"></a><span data-ttu-id="2464b-179">.NET Framework에서 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="2464b-179">Migration from the .NET Framework</span></span>

<span data-ttu-id="2464b-180">ASP.NET 앱을 ASP.NET Core로 마이그레이션하기 위한 참조 가이드는 <xref:migration/proper-to-2x/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2464b-180">For a reference guide to migrating ASP.NET apps to ASP.NET Core, see <xref:migration/proper-to-2x/index>.</span></span>

## <a name="how-to-download-a-sample"></a><span data-ttu-id="2464b-181">예제 다운로드 방법</span><span class="sxs-lookup"><span data-stu-id="2464b-181">How to download a sample</span></span>

<span data-ttu-id="2464b-182">대부분의 문서 및 자습서에는 예제 코드에 대한 링크가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-182">Many of the articles and tutorials include links to sample code.</span></span>

1. <span data-ttu-id="2464b-183">[ASP.NET 리포지토리 zip 파일을 다운로드합니다](https://codeload.github.com/aspnet/AspNetCore.Docs/zip/master).</span><span class="sxs-lookup"><span data-stu-id="2464b-183">[Download the ASP.NET repository zip file](https://codeload.github.com/aspnet/AspNetCore.Docs/zip/master).</span></span>
1. <span data-ttu-id="2464b-184">*Docs-master.zip* 파일의 압축을 풉니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-184">Unzip the *Docs-master.zip* file.</span></span>
1. <span data-ttu-id="2464b-185">예제 링크의 URL을 참고하여 예제 디렉터리로 이동할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-185">Use the URL in the sample link to help you navigate to the sample directory.</span></span>

### <a name="preprocessor-directives-in-sample-code"></a><span data-ttu-id="2464b-186">예제 코드의 전처리기 지시문</span><span class="sxs-lookup"><span data-stu-id="2464b-186">Preprocessor directives in sample code</span></span>

<span data-ttu-id="2464b-187">다양한 시나리오를 보여주기 위해 예제 앱은 `#define` 및 `#if-#else/#elif-#endif` 전처리기 지시문을 사용하여 예제 코드의 서로 다른 섹션을 선택적으로 컴파일하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-187">To demonstrate multiple scenarios, sample apps use the `#define` and `#if-#else/#elif-#endif` preprocessor directives to selectively compile and run different sections of sample code.</span></span> <span data-ttu-id="2464b-188">이 방법을 사용하는 예제의 경우 C# 파일 상단에 위치한 `#define` 지시문을 설정하여 실행할 시나리오와 연결된 기호를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-188">For those samples that make use of this approach, set the `#define` directive at the top of the C# files to define the symbol associated with the scenario that you want to run.</span></span> <span data-ttu-id="2464b-189">일부 예제는 시나리오를 실행하기 위해 여러 파일의 맨 상단에 기호를 정의해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-189">Some samples require defining the symbol at the top of multiple files in order to run a scenario.</span></span>

<span data-ttu-id="2464b-190">예를 들어, 다음 `#define` 기호 목록은 네 가지 시나리오를 사용할 수 있음을 나타냅니다(기호당 하나의 시나리오).</span><span class="sxs-lookup"><span data-stu-id="2464b-190">For example, the following `#define` symbol list indicates that four scenarios are available (one scenario per symbol).</span></span> <span data-ttu-id="2464b-191">현재 예제 구성에서는 `TemplateCode` 시나리오를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-191">The current sample configuration runs the `TemplateCode` scenario:</span></span>

```csharp
#define TemplateCode // or LogFromMain or ExpandDefault or FilterInCode
```

<span data-ttu-id="2464b-192">`ExpandDefault` 시나리오를 실행하도록 예제를 변경하려면 `ExpandDefault` 기호를 정의하고 나머지 기호는 주석으로 처리하세요.</span><span class="sxs-lookup"><span data-stu-id="2464b-192">To change the sample to run the `ExpandDefault` scenario, define the `ExpandDefault` symbol and leave the remaining symbols commented-out:</span></span>

```csharp
#define ExpandDefault // TemplateCode or LogFromMain or FilterInCode
```

<span data-ttu-id="2464b-193">[C# 전처리기 지시문](/dotnet/csharp/language-reference/preprocessor-directives/)을 사용하여 코드 섹션을 선택적으로 컴파일하는 방법에 대한 자세한 내용은 [#define(C# 참조)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) 및 [#if(C# 참조)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2464b-193">For more information on using [C# preprocessor directives](/dotnet/csharp/language-reference/preprocessor-directives/) to selectively compile sections of code, see [#define (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) and [#if (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span></span>

### <a name="regions-in-sample-code"></a><span data-ttu-id="2464b-194">예제 코드의 지역</span><span class="sxs-lookup"><span data-stu-id="2464b-194">Regions in sample code</span></span>

<span data-ttu-id="2464b-195">일부 예제 앱에는 [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) 및 [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) C# 지시문으로 둘러싸인 코드 섹션이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-195">Some sample apps contain sections of code surrounded by [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) and [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) C# directives.</span></span> <span data-ttu-id="2464b-196">설명서 빌드 시스템은 렌더링된 설명서 토픽에 이러한 지역을 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-196">The documentation build system injects these regions into the rendered documentation topics.</span></span>  

<span data-ttu-id="2464b-197">일반적으로 지역 이름에는 "snippet"이라는 단어가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-197">Region names usually contain the word "snippet."</span></span> <span data-ttu-id="2464b-198">다음 예제는 `snippet_WebHostDefaults`라는 지역을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-198">The following example shows a region named `snippet_WebHostDefaults`:</span></span>

```csharp
#region snippet_WebHostDefaults
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
    });
#endregion
```

<span data-ttu-id="2464b-199">이 C# 코드 조각은 다음 줄을 포함하고 있는 항목의 마크다운 파일에서 참조됩니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-199">The preceding C# code snippet is referenced in the topic's markdown file with the following line:</span></span>

```md
[!code-csharp[](sample/SampleApp/Program.cs?name=snippet_WebHostDefaults)]
```

<span data-ttu-id="2464b-200">코드를 둘러싸고 있는 `#region` 및 `#endregion` 지시문은 안전하게 무시(또는 제거)할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-200">You may safely ignore (or remove) the `#region` and `#endregion` directives that surround the code.</span></span> <span data-ttu-id="2464b-201">항목에 설명된 예제 시나리오를 실행하려는 경우 이러한 지시문 내에서 코드를 변경하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="2464b-201">Don't alter the code within these directives if you plan to run the sample scenarios described in the topic.</span></span> <span data-ttu-id="2464b-202">다른 시나리오를 실험하는 경우 자유롭게 코드를 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-202">Feel free to alter the code when experimenting with other scenarios.</span></span>

<span data-ttu-id="2464b-203">자세한 내용은 [ASP.NET 설명서에 참여: 코드 조각](https://github.com/aspnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2464b-203">For more information, see [Contribute to the ASP.NET documentation: Code snippets](https://github.com/aspnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).</span></span>

## <a name="next-steps"></a><span data-ttu-id="2464b-204">다음 단계</span><span class="sxs-lookup"><span data-stu-id="2464b-204">Next steps</span></span>

<span data-ttu-id="2464b-205">자세한 내용은 다음 자료를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="2464b-205">For more information, see the following resources:</span></span>

* <xref:getting-started>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="2464b-206">ASP.NET Core 기본 사항</span><span class="sxs-lookup"><span data-stu-id="2464b-206">ASP.NET Core fundamentals</span></span>](xref:fundamentals/index)
* <span data-ttu-id="2464b-207">[주간 ASP.NET 커뮤니티 스탠드업](https://live.asp.net/)은 팀의 진행 상태 및 계획을 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-207">[The weekly ASP.NET community standup](https://live.asp.net/) covers the team's progress and plans.</span></span> <span data-ttu-id="2464b-208">새로운 블로그 및 타사 소프트웨어도 소개합니다.</span><span class="sxs-lookup"><span data-stu-id="2464b-208">It features new blogs and third-party software.</span></span>
