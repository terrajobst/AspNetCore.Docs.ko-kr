---
title: ASP.NET Core 기본 사항
author: rick-anderson
description: ASP.NET Core 앱을 구축하기 위한 기본적인 개념을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/15/2020
uid: fundamentals/index
ms.openlocfilehash: 3fbfc7c4c0d5e568339bc00a7cbe84a3932acf1f
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78644553"
---
# <a name="aspnet-core-fundamentals"></a><span data-ttu-id="dc683-103">ASP.NET Core 기본 사항</span><span class="sxs-lookup"><span data-stu-id="dc683-103">ASP.NET Core fundamentals</span></span>

<span data-ttu-id="dc683-104">이 문서는 ASP.NET Core 앱을 개발하는 방법을 이해하기 위한 주요 항목의 개요입니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-104">This article is an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="dc683-105">Startup 클래스</span><span class="sxs-lookup"><span data-stu-id="dc683-105">The Startup class</span></span>

<span data-ttu-id="dc683-106">`Startup` 클래스에서는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-106">The `Startup` class is where:</span></span>

* <span data-ttu-id="dc683-107">앱에서 요구하는 서비스가 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-107">Services required by the app are configured.</span></span>
* <span data-ttu-id="dc683-108">요청 처리 파이프라인이 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-108">The request handling pipeline is defined.</span></span>

<span data-ttu-id="dc683-109">*서비스*는 앱에서 사용되는 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-109">*Services* are components that are used by the app.</span></span> <span data-ttu-id="dc683-110">예를 들어 로깅 구성 요소는 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-110">For example, a logging component is a service.</span></span> <span data-ttu-id="dc683-111">서비스를 구성(또는 *등록*)하는 코드는 `Startup.ConfigureServices` 메서드에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-111">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span>

<span data-ttu-id="dc683-112">요청 처리 파이프라인은 일련의 *미들웨어* 구성 요소로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-112">The request handling pipeline is composed as a series of *middleware* components.</span></span> <span data-ttu-id="dc683-113">예를 들어 미들웨어는 정적 파일에 대한 요청을 처리하거나 HTTP 요청을 HTTPS로 리디렉션할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-113">For example, a middleware might handle requests for static files or redirect HTTP requests to HTTPS.</span></span> <span data-ttu-id="dc683-114">각 미들웨어는 `HttpContext` 상에서 비동기 작업을 수행한 다음, 파이프라인의 다음 미들웨어를 호출하거나 요청을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-114">Each middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span> <span data-ttu-id="dc683-115">요청 처리 파이프라인을 구성하는 코드는 `Startup.Configure` 메서드에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-115">Code to configure the request handling pipeline is added to the `Startup.Configure` method.</span></span>

<span data-ttu-id="dc683-116">샘플 `Startup` 클래스는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-116">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=3,12)]

<span data-ttu-id="dc683-117">자세한 내용은 <xref:fundamentals/startup>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dc683-117">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="dc683-118">종속성 주입(서비스)</span><span class="sxs-lookup"><span data-stu-id="dc683-118">Dependency injection (services)</span></span>

<span data-ttu-id="dc683-119">ASP.NET Core에는 구성된 서비스를 앱의 클래스에 사용할 수 있도록 만드는 기본 제공 DI(종속성 주입) 프레임워크가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-119">ASP.NET Core has a built-in dependency injection (DI) framework that makes configured services available to an app's classes.</span></span> <span data-ttu-id="dc683-120">클래스에서 서비스의 인스턴스를 가져오는 한 가지 방법은 필수 형식의 매개 변수를 사용하여 생성자를 만드는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-120">One way to get an instance of a service in a class is to create a constructor with a parameter of the required type.</span></span> <span data-ttu-id="dc683-121">해당 매개 변수는 서비스 형식 또는 인터페이스일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-121">The parameter can be the service type or an interface.</span></span> <span data-ttu-id="dc683-122">DI 시스템에서는 런타임 시 서비스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-122">The DI system provides the service at runtime.</span></span>

<span data-ttu-id="dc683-123">Entity Framework Core 컨텍스트 개체를 가져오는 데 DI를 사용하는 클래스는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-123">Here's a class that uses DI to get an Entity Framework Core context object.</span></span> <span data-ttu-id="dc683-124">강조 표시된 줄은 생성자 주입의 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-124">The highlighted line is an example of constructor injection:</span></span>

[!code-csharp[](index/snapshots/2.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="dc683-125">DI가 기본 제공되기는 하지만 원하는 경우 타사 IoC(Inversion of Control) 컨테이너를 플러그 인할 수 있도록 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-125">While DI is built in, it's designed to let you plug in a third-party Inversion of Control (IoC) container if you prefer.</span></span>

<span data-ttu-id="dc683-126">자세한 내용은 <xref:fundamentals/dependency-injection>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dc683-126">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="dc683-127">미들웨어</span><span class="sxs-lookup"><span data-stu-id="dc683-127">Middleware</span></span>

<span data-ttu-id="dc683-128">요청 처리 파이프라인은 일련의 미들웨어 구성 요소로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-128">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="dc683-129">각 구성 요소는 `HttpContext` 상에서 비동기 작업을 수행한 다음, 파이프라인의 다음 미들웨어를 호출하거나 요청을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-129">Each component performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="dc683-130">규칙에 따라 미들웨어 구성 요소는 `Startup.Configure` 메서드에서 `Use...` 확장 메서드를 호출하여 파이프라인에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-130">By convention, a middleware component is added to the pipeline by invoking its `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="dc683-131">예를 들어 정적 파일을 렌더링하도록 설정하려면 `UseStaticFiles`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-131">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="dc683-132">다음 예제에서 강조 표시된 코드는 요청 처리 파이프라인을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-132">The highlighted code in the following example configures the request handling pipeline:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=14-16)]

<span data-ttu-id="dc683-133">ASP.NET Core는 풍부한 일련의 기본 제공 미들웨어를 포함하고 있으며, 사용자 지정 미들웨어를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-133">ASP.NET Core includes a rich set of built-in middleware, and you can write custom middleware.</span></span>

<span data-ttu-id="dc683-134">자세한 내용은 <xref:fundamentals/middleware/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dc683-134">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="dc683-135">호스트</span><span class="sxs-lookup"><span data-stu-id="dc683-135">Host</span></span>

<span data-ttu-id="dc683-136">ASP.NET Core 앱은 시작 시 *호스트*를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-136">An ASP.NET Core app builds a *host* on startup.</span></span> <span data-ttu-id="dc683-137">호스트는 다음과 같은 앱의 리소스를 모두 캡슐화하는 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-137">The host is an object that encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="dc683-138">HTTP 서버 구현</span><span class="sxs-lookup"><span data-stu-id="dc683-138">An HTTP server implementation</span></span>
* <span data-ttu-id="dc683-139">미들웨어 구성 요소</span><span class="sxs-lookup"><span data-stu-id="dc683-139">Middleware components</span></span>
* <span data-ttu-id="dc683-140">로깅</span><span class="sxs-lookup"><span data-stu-id="dc683-140">Logging</span></span>
* <span data-ttu-id="dc683-141">DI</span><span class="sxs-lookup"><span data-stu-id="dc683-141">DI</span></span>
* <span data-ttu-id="dc683-142">Configuration</span><span class="sxs-lookup"><span data-stu-id="dc683-142">Configuration</span></span>

<span data-ttu-id="dc683-143">하나의 개체에 앱의 모든 상호 종속적 리소스를 포함하는 주요 원인은 수명 관리 즉, 앱 시작 및 종료에 대한 제어 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-143">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="dc683-144">두 호스트, 제네릭 호스트와 웹 호스트를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-144">Two hosts are available: the Generic Host and the Web Host.</span></span> <span data-ttu-id="dc683-145">제네릭 호스트를 사용하는 것이 좋고, 웹 호스트는 이전 버전과만 호환 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-145">The Generic Host is recommended, and the Web Host is available only for backwards compatibility.</span></span>

<span data-ttu-id="dc683-146">호스트를 만드는 코드는 `Program.Main`에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-146">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/snapshots/3.x/Program1.cs)]

<span data-ttu-id="dc683-147">`CreateDefaultBuilder` 및 `ConfigureWebHostDefaults` 메서드는 다음과 같이 일반적으로 사용되는 옵션으로 호스트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-147">The `CreateDefaultBuilder` and `ConfigureWebHostDefaults` methods configure a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="dc683-148">[Kestrel](#servers)을 웹 서버로 사용하고 IIS 통합을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-148">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="dc683-149">*appsettings.json*, *appsettings.{Environment Name}.json*, 환경 변수, 명령줄 인수 및 기타 구성 소스의 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-149">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="dc683-150">콘솔 및 디버그 공급자에게 로깅 출력을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-150">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="dc683-151">자세한 내용은 <xref:fundamentals/host/generic-host>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dc683-151">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="dc683-152">두 호스트, 웹 호스트와 제네릭 호스트를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-152">Two hosts are available: the Web Host and the Generic Host.</span></span> <span data-ttu-id="dc683-153">ASP.NET Core 2.x에서 제네릭 호스트는 비 웹 시나리오에만 해당됩니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-153">In ASP.NET Core 2.x, the Generic Host is only for non-web scenarios.</span></span>

<span data-ttu-id="dc683-154">호스트를 만드는 코드는 `Program.Main`에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-154">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/snapshots/2.x/Program1.cs)]

<span data-ttu-id="dc683-155">`CreateDefaultBuilder` 메서드는 다음과 같이 일반적으로 사용되는 옵션으로 호스트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-155">The `CreateDefaultBuilder` method configures a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="dc683-156">[Kestrel](#servers)을 웹 서버로 사용하고 IIS 통합을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-156">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="dc683-157">*appsettings.json*, *appsettings.{Environment Name}.json*, 환경 변수, 명령줄 인수 및 기타 구성 소스의 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-157">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="dc683-158">콘솔 및 디버그 공급자에게 로깅 출력을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-158">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="dc683-159">자세한 내용은 <xref:fundamentals/host/web-host>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dc683-159">For more information, see <xref:fundamentals/host/web-host>.</span></span>

::: moniker-end

### <a name="non-web-scenarios"></a><span data-ttu-id="dc683-160">비 웹 시나리오</span><span class="sxs-lookup"><span data-stu-id="dc683-160">Non-web scenarios</span></span>

<span data-ttu-id="dc683-161">제네릭 호스트를 통해 다른 형식의 앱에서 로깅, DI(종속성 주입), 구성 및 앱 수명 관리와 같은 교차 프레임워크 확장을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-161">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="dc683-162">자세한 내용은 <xref:fundamentals/host/generic-host> 및 <xref:fundamentals/host/hosted-services>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dc683-162">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="dc683-163">서버</span><span class="sxs-lookup"><span data-stu-id="dc683-163">Servers</span></span>

<span data-ttu-id="dc683-164">ASP.NET Core 앱은 HTTP 요청을 수신하기 위해 HTTP 서버 구현을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-164">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="dc683-165">해당 서버는 `HttpContext`에 구성된 일련의 [요청 기능](xref:fundamentals/request-features)으로 앱에 대한 요청을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-165">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="dc683-166">Windows</span><span class="sxs-lookup"><span data-stu-id="dc683-166">Windows</span></span>](#tab/windows)

<span data-ttu-id="dc683-167">ASP.NET Core는 다음과 같은 서버 구현을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-167">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="dc683-168">*Kestrel*은 플랫폼 간 웹 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-168">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="dc683-169">Kestrel은 보통 [IIS](https://www.iis.net/)를 사용하여 역방향 프록시 구성에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-169">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="dc683-170">Kestrel은 ASP.NET Core 2.0 이상에서 인터넷에 직접 공개되는 공용 연결 에지 서버로 실행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-170">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="dc683-171">*IIS HTTP 서버*는 IIS를 사용하는 Windows용 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-171">*IIS HTTP Server* is a server for windows that uses IIS.</span></span> <span data-ttu-id="dc683-172">이 서버를 사용하면 ASP.NET Core 앱 및 IIS는 동일한 프로세스에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-172">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="dc683-173">*HTTP.sys*는 IIS에서 사용되지 않는 Windows용 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-173">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="dc683-174">macOS</span><span class="sxs-lookup"><span data-stu-id="dc683-174">macOS</span></span>](#tab/macos)

<span data-ttu-id="dc683-175">ASP.NET Core는 *Kestrel* 플랫폼 간 서버 구현을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-175">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="dc683-176">Kestrel은 ASP.NET Core 2.0 이상에서 인터넷에 직접 공개되는 공용 연결 에지 서버로 실행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-176">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="dc683-177">Kestrel은 보통 [Nginx](https://nginx.org) 또는 [Apache](https://httpd.apache.org/)를 사용하여 역방향 프록시 구성에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-177">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="dc683-178">Linux</span><span class="sxs-lookup"><span data-stu-id="dc683-178">Linux</span></span>](#tab/linux)

<span data-ttu-id="dc683-179">ASP.NET Core는 *Kestrel* 플랫폼 간 서버 구현을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-179">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="dc683-180">Kestrel은 ASP.NET Core 2.0 이상에서 인터넷에 직접 공개되는 공용 연결 에지 서버로 실행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-180">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="dc683-181">Kestrel은 보통 [Nginx](https://nginx.org) 또는 [Apache](https://httpd.apache.org/)를 사용하여 역방향 프록시 구성에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-181">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="dc683-182">Windows</span><span class="sxs-lookup"><span data-stu-id="dc683-182">Windows</span></span>](#tab/windows)

<span data-ttu-id="dc683-183">ASP.NET Core는 다음과 같은 서버 구현을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-183">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="dc683-184">*Kestrel*은 플랫폼 간 웹 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-184">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="dc683-185">Kestrel은 보통 [IIS](https://www.iis.net/)를 사용하여 역방향 프록시 구성에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-185">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="dc683-186">Kestrel은 ASP.NET Core 2.0 이상에서 인터넷에 직접 공개되는 공용 연결 에지 서버로 실행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-186">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="dc683-187">*HTTP.sys*는 IIS에서 사용되지 않는 Windows용 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-187">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="dc683-188">macOS</span><span class="sxs-lookup"><span data-stu-id="dc683-188">macOS</span></span>](#tab/macos)

<span data-ttu-id="dc683-189">ASP.NET Core는 *Kestrel* 플랫폼 간 서버 구현을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-189">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="dc683-190">Kestrel은 ASP.NET Core 2.0 이상에서 인터넷에 직접 공개되는 공용 연결 에지 서버로 실행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-190">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="dc683-191">Kestrel은 보통 [Nginx](https://nginx.org) 또는 [Apache](https://httpd.apache.org/)를 사용하여 역방향 프록시 구성에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-191">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="dc683-192">Linux</span><span class="sxs-lookup"><span data-stu-id="dc683-192">Linux</span></span>](#tab/linux)

<span data-ttu-id="dc683-193">ASP.NET Core는 *Kestrel* 플랫폼 간 서버 구현을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-193">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="dc683-194">Kestrel은 ASP.NET Core 2.0 이상에서 인터넷에 직접 공개되는 공용 연결 에지 서버로 실행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-194">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="dc683-195">Kestrel은 보통 [Nginx](https://nginx.org) 또는 [Apache](https://httpd.apache.org/)를 사용하여 역방향 프록시 구성에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-195">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

<span data-ttu-id="dc683-196">자세한 내용은 <xref:fundamentals/servers/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dc683-196">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="dc683-197">Configuration</span><span class="sxs-lookup"><span data-stu-id="dc683-197">Configuration</span></span>

<span data-ttu-id="dc683-198">ASP.NET Core는 정렬된 일련의 구성 공급 기업에서 이름-값 쌍으로 설정을 가져오는 구성 프레임워크를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-198">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="dc683-199">*.json* 파일, *.xml* 파일, 환경 변수 및 명령줄 인수와 같은 다양한 원본의 기본 제공 구성 공급 기업이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-199">There are built-in configuration providers for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="dc683-200">또는 사용자 지정 구성 공급 기업을 작성할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-200">You can also write custom configuration providers.</span></span>

<span data-ttu-id="dc683-201">예를 들어 구성이 *appsettings.json* 및 환경 변수에서 제공되도록 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-201">For example, you could specify that configuration comes from *appsettings.json* and environment variables.</span></span> <span data-ttu-id="dc683-202">그런 다음, *ConnectionString* 값이 요청되면 프레임워크는 먼저 *appsettings.json* 파일을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-202">Then when the value of *ConnectionString* is requested, the framework looks first in the *appsettings.json* file.</span></span> <span data-ttu-id="dc683-203">값을 찾았지만 환경 변수에서도 값을 찾을 수 있다면 환경 변수의 값이 우선 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-203">If the value is found there but also in an environment variable, the value from the environment variable would take precedence.</span></span>

<span data-ttu-id="dc683-204">ASP.NET Core는 암호와 같은 기밀 구성 데이터를 관리하기 위해 [비밀 관리자 도구](xref:security/app-secrets)를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-204">For managing confidential configuration data such as passwords, ASP.NET Core provides a [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="dc683-205">프로덕션 비밀의 경우 [Azure Key Vault](xref:security/key-vault-configuration)를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-205">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="dc683-206">자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dc683-206">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="options"></a><span data-ttu-id="dc683-207">옵션</span><span class="sxs-lookup"><span data-stu-id="dc683-207">Options</span></span>

<span data-ttu-id="dc683-208">가능할 경우 ASP.NET Core는 구성 값을 저장하고 검색하기 위해 *옵션 패턴*을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-208">Where possible, ASP.NET Core follows the *options pattern* for storing and retrieving configuration values.</span></span> <span data-ttu-id="dc683-209">옵션 패턴은 클래스를 사용하여 관련 설정 그룹을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-209">The options pattern uses classes to represent groups of related settings.</span></span>

<span data-ttu-id="dc683-210">예를 들어 다음 코드에서는 WebSockets 옵션을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-210">For example, the following code sets WebSockets options:</span></span>

```csharp
var options = new WebSocketOptions  
{  
   KeepAliveInterval = TimeSpan.FromSeconds(120),  
   ReceiveBufferSize = 4096
};  
app.UseWebSockets(options);
```

<span data-ttu-id="dc683-211">자세한 내용은 <xref:fundamentals/configuration/options>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dc683-211">For more information, see <xref:fundamentals/configuration/options>.</span></span>

## <a name="environments"></a><span data-ttu-id="dc683-212">환경</span><span class="sxs-lookup"><span data-stu-id="dc683-212">Environments</span></span>

<span data-ttu-id="dc683-213">*개발*, *준비* 및 *프로덕션*과 같은 실행 환경은 ASP.NET Core의 일급 개념입니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-213">Execution environments, such as *Development*, *Staging*, and *Production*, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="dc683-214">`ASPNETCORE_ENVIRONMENT` 환경 변수를 설정하여 앱을 실행 중인 환경을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-214">You can specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="dc683-215">ASP.NET Core는 앱 시작 시 해당 환경 변수를 읽고 `IHostingEnvironment` 구현에서 값을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-215">ASP.NET Core reads that environment variable at app startup and stores the value in an `IHostingEnvironment` implementation.</span></span> <span data-ttu-id="dc683-216">환경 개체는 DI를 통해 앱에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-216">The environment object is available anywhere in the app via DI.</span></span>

<span data-ttu-id="dc683-217">`Startup` 클래스의 다음 샘플 코드는 개발 중에 실행할 때만 자세한 오류 정보를 제공하도록 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-217">The following sample code from the `Startup` class configures the app to provide detailed error information only when it runs in development:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup2.cs?highlight=3-6)]

<span data-ttu-id="dc683-218">자세한 내용은 <xref:fundamentals/environments>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dc683-218">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="dc683-219">로깅</span><span class="sxs-lookup"><span data-stu-id="dc683-219">Logging</span></span>

<span data-ttu-id="dc683-220">ASP.NET Core는 다양한 기본 제공 및 타사 로깅 공급자와 함께 작동하는 로깅 API를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-220">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="dc683-221">지원되는 공급 기업에는 다음이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-221">Available providers include the following:</span></span>

* <span data-ttu-id="dc683-222">Console</span><span class="sxs-lookup"><span data-stu-id="dc683-222">Console</span></span>
* <span data-ttu-id="dc683-223">디버그</span><span class="sxs-lookup"><span data-stu-id="dc683-223">Debug</span></span>
* <span data-ttu-id="dc683-224">Windows 이벤트 추적</span><span class="sxs-lookup"><span data-stu-id="dc683-224">Event Tracing on Windows</span></span>
* <span data-ttu-id="dc683-225">Windows 이벤트 로그</span><span class="sxs-lookup"><span data-stu-id="dc683-225">Windows Event Log</span></span>
* <span data-ttu-id="dc683-226">TraceSource</span><span class="sxs-lookup"><span data-stu-id="dc683-226">TraceSource</span></span>
* <span data-ttu-id="dc683-227">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="dc683-227">Azure App Service</span></span>
* <span data-ttu-id="dc683-228">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="dc683-228">Azure Application Insights</span></span>

<span data-ttu-id="dc683-229">DI에서 `ILogger` 개체를 가져오고 로그 메서드를 호출하여 앱 코드 어디서나 로그를 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-229">Write logs from anywhere in an app's code by getting an `ILogger` object from DI and calling log methods.</span></span>

<span data-ttu-id="dc683-230">생성자 주입 및 로깅 메서드 호출을 강조 표시하고 `ILogger` 개체를 사용하는 샘플 코드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-230">Here's sample code that uses an `ILogger` object, with constructor injection and the logging method calls highlighted.</span></span>

[!code-csharp[](index/snapshots/2.x/TodoController.cs?highlight=5,13,17)]

<span data-ttu-id="dc683-231">`ILogger` 인터페이스를 통해 필드 개수에 관계 없이 로그 공급 기업에 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-231">The `ILogger` interface lets you pass any number of fields to the logging provider.</span></span> <span data-ttu-id="dc683-232">필드는 일반적으로 메시지 문자열을 생성하는 데 사용되지만 공급 기업은 이를 별도 필드로 데이터 저장소에 보낼 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-232">The fields are commonly used to construct a message string, but the provider can also send them as separate fields to a data store.</span></span> <span data-ttu-id="dc683-233">이 기능을 사용하면 로그 공급 기업이 [구조적 로깅이라고도 하는 의미 체계 로깅](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)을 구현할 수 있게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-233">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="dc683-234">자세한 내용은 <xref:fundamentals/logging/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dc683-234">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="dc683-235">라우팅</span><span class="sxs-lookup"><span data-stu-id="dc683-235">Routing</span></span>

<span data-ttu-id="dc683-236">*경로*는 처리기에 매핑되는 URL 패턴입니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-236">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="dc683-237">처리기는 일반적으로 Razor 페이지, MVC 컨트롤러의 작업 메서드 또는 미들웨어와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-237">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="dc683-238">ASP.NET Core 라우팅을 사용하면 앱에서 사용되는 URL을 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-238">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="dc683-239">자세한 내용은 <xref:fundamentals/routing>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dc683-239">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="dc683-240">오류 처리</span><span class="sxs-lookup"><span data-stu-id="dc683-240">Error handling</span></span>

<span data-ttu-id="dc683-241">ASP.NET Core에는 다음과 같은 오류를 처리하기 위한 기본 제공 기능이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-241">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="dc683-242">개발자 예외 페이지</span><span class="sxs-lookup"><span data-stu-id="dc683-242">A developer exception page</span></span>
* <span data-ttu-id="dc683-243">사용자 지정 오류 페이지</span><span class="sxs-lookup"><span data-stu-id="dc683-243">Custom error pages</span></span>
* <span data-ttu-id="dc683-244">정적 상태 코드 페이지</span><span class="sxs-lookup"><span data-stu-id="dc683-244">Static status code pages</span></span>
* <span data-ttu-id="dc683-245">시작 예외 처리</span><span class="sxs-lookup"><span data-stu-id="dc683-245">Startup exception handling</span></span>

<span data-ttu-id="dc683-246">자세한 내용은 <xref:fundamentals/error-handling>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dc683-246">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="dc683-247">HTTP 요청 만들기</span><span class="sxs-lookup"><span data-stu-id="dc683-247">Make HTTP requests</span></span>

<span data-ttu-id="dc683-248">`HttpClient` 인스턴스를 만들기 위해 `IHttpClientFactory`를 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-248">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="dc683-249">팩터리는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-249">The factory:</span></span>

* <span data-ttu-id="dc683-250">논리적 `HttpClient` 인스턴스를 구성하고 이름을 지정하기 위한 중앙 위치를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-250">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="dc683-251">예를 들어, *github* 클라이언트는 GitHub에 액세스하도록 등록 및 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-251">For example, a *github* client can be registered and configured to access GitHub.</span></span> <span data-ttu-id="dc683-252">기본 클라이언트는 다른 용도로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-252">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="dc683-253">나가는 요청 미들웨어 파이프라인을 빌드하기 위해 여러 위임 처리기를 연결하고 등록하도록 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-253">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="dc683-254">이 패턴은 ASP.NET Core의 인바운드 미들웨어 파이프라인과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-254">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="dc683-255">이 패턴은 캐싱, 오류 처리, 직렬화 및 로깅을 포함한 HTTP 요청을 둘러싼 횡단 관심사를 관리하기 위한 메커니즘을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-255">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="dc683-256">일시적인 오류를 처리하기 위해 널리 사용되는 타사 라이브러리인 *Polly*와 통합합니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-256">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="dc683-257">`HttpClient` 수명을 수동으로 관리할 때 발생하는 일반적인 DNS 문제를 피하기 위해 기본 `HttpClientMessageHandler` 인스턴스의 풀링 및 수명을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-257">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="dc683-258">팩터리에서 만든 클라이언트를 통해 전송된 모든 요청에 대해 구성 가능한 로깅 경험(`ILogger`을 통해)을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-258">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="dc683-259">자세한 내용은 <xref:fundamentals/http-requests>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dc683-259">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="dc683-260">콘텐츠 루트</span><span class="sxs-lookup"><span data-stu-id="dc683-260">Content root</span></span>

<span data-ttu-id="dc683-261">콘텐츠 루트는 다음의 기본 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-261">The content root is the base path to the:</span></span>

* <span data-ttu-id="dc683-262">앱을 호스트하는 실행 파일(*.exe*)</span><span class="sxs-lookup"><span data-stu-id="dc683-262">Executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="dc683-263">앱을 구성하는 컴파일된 어셈블리(*.dll*)</span><span class="sxs-lookup"><span data-stu-id="dc683-263">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="dc683-264">다음과 같은 앱에서 사용하는 비코드 콘텐츠 파일:</span><span class="sxs-lookup"><span data-stu-id="dc683-264">Non-code content files used by the app, such as:</span></span>
  * <span data-ttu-id="dc683-265">Razor 파일(*.cshtml*, *.razor*)</span><span class="sxs-lookup"><span data-stu-id="dc683-265">Razor files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="dc683-266">구성 파일(*.json*, *.xml*)</span><span class="sxs-lookup"><span data-stu-id="dc683-266">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="dc683-267">데이터 파일(*.db*)</span><span class="sxs-lookup"><span data-stu-id="dc683-267">Data files (*.db*)</span></span>
* <span data-ttu-id="dc683-268">[웹 루트](#web-root)(일반적으로 *wwwroot* 폴더에 게시됨)</span><span class="sxs-lookup"><span data-stu-id="dc683-268">[Web root](#web-root), typically the published *wwwroot* folder.</span></span>

<span data-ttu-id="dc683-269">개발 중:</span><span class="sxs-lookup"><span data-stu-id="dc683-269">During development:</span></span>

* <span data-ttu-id="dc683-270">콘텐츠 루트의 기본값은 프로젝트의 루트 디렉터리입니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-270">The content root defaults to the project's root directory.</span></span>
* <span data-ttu-id="dc683-271">프로젝트의 루트 디렉터리는 다음을 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-271">The project's root directory is used to create the:</span></span>
  * <span data-ttu-id="dc683-272">프로젝트의 루트 디렉터리에서 앱의 비코드 콘텐츠 파일의 경로</span><span class="sxs-lookup"><span data-stu-id="dc683-272">Path to the app's non-code content files in the project's root directory.</span></span>
  * <span data-ttu-id="dc683-273">[웹 루트](#web-root)(일반적으로 프로젝트의 루트 디렉터리의 *wwwroot* 폴더)</span><span class="sxs-lookup"><span data-stu-id="dc683-273">[Web root](#web-root), typically the *wwwroot* folder in the project's root directory.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="dc683-274">[호스트를 빌드](#host)할 때 지정될 수 있는 대체 콘텐츠 루트 경로</span><span class="sxs-lookup"><span data-stu-id="dc683-274">An alternative content root path can be specified when [building the host](#host).</span></span> <span data-ttu-id="dc683-275">자세한 내용은 <xref:fundamentals/host/generic-host#contentrootpath>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dc683-275">For more information, see <xref:fundamentals/host/generic-host#contentrootpath>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="dc683-276">[호스트를 빌드](#host)할 때 지정될 수 있는 대체 콘텐츠 루트 경로</span><span class="sxs-lookup"><span data-stu-id="dc683-276">An alternative content root path can be specified when [building the host](#host).</span></span> <span data-ttu-id="dc683-277">자세한 내용은 <xref:fundamentals/host/web-host#content-root>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dc683-277">For more information, see <xref:fundamentals/host/web-host#content-root>.</span></span>

::: moniker-end

## <a name="web-root"></a><span data-ttu-id="dc683-278">웹 루트</span><span class="sxs-lookup"><span data-stu-id="dc683-278">Web root</span></span>

<span data-ttu-id="dc683-279">웹 루트는 다음과 같은 공용, 비코드, 정적 리소스 파일의 기본 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-279">The web root is the base path to public, non-code, static resource files, such as:</span></span>

* <span data-ttu-id="dc683-280">스타일시트(*.css*)</span><span class="sxs-lookup"><span data-stu-id="dc683-280">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="dc683-281">JavaScript(*.js*)</span><span class="sxs-lookup"><span data-stu-id="dc683-281">JavaScript (*.js*)</span></span>
* <span data-ttu-id="dc683-282">이미지(*.png*, *.jpg*)</span><span class="sxs-lookup"><span data-stu-id="dc683-282">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="dc683-283">정적 파일은 기본적으로 웹 루트 디렉터리 및 하위 디렉터리에서만 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-283">Static files are only served by default from the web root directory (and sub-directories).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="dc683-284">웹 루트 경로는 *{content root}/wwwroot*를 기본값으로 지정하지만 [호스트를 빌드](#host)할 때 다른 웹 루트를 지정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-284">The web root path defaults to *{content root}/wwwroot*, but a different web root can be specified when [building the host](#host).</span></span> <span data-ttu-id="dc683-285">자세한 내용은 <xref:fundamentals/host/generic-host#webroot>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dc683-285">For more information, see <xref:fundamentals/host/generic-host#webroot>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="dc683-286">웹 루트 경로는 *{content root}/wwwroot*를 기본값으로 지정하지만 [호스트를 빌드](#host)할 때 다른 웹 루트를 지정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-286">The web root path defaults to *{content root}/wwwroot*, but a different web root can be specified when [building the host](#host).</span></span> <span data-ttu-id="dc683-287">자세한 내용은 [웹 루트](xref:fundamentals/host/web-host#web-root)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dc683-287">For more information, see [Web root](xref:fundamentals/host/web-host#web-root).</span></span>

::: moniker-end

<span data-ttu-id="dc683-288">프로젝트 파일에서 [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content)을 사용하여 *wwwroot*에 파일을 게시하지 못하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-288">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="dc683-289">다음 예에서는 *wwwroot/local* 디렉터리 및 하위 디렉터리에 콘텐츠를 게시하지 못하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-289">The following example prevents publishing content in the *wwwroot/local* directory and sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="dc683-290">정적 ID 자산을 웹 루트에 게시하지 않으려면 <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dc683-290">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

::: moniker-end

<span data-ttu-id="dc683-291">Razor(*.cshtml*) 파일에서는 물결표 슬래시(`~/`)가 웹 루트를 가리킵니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-291">In Razor (*.cshtml*) files, the tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="dc683-292">`~/`(으)로 시작하는 경로를 *가상 경로*라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="dc683-292">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="dc683-293">자세한 내용은 <xref:fundamentals/static-files>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="dc683-293">For more information, see <xref:fundamentals/static-files>.</span></span>
