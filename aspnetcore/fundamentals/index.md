---
title: ASP.NET Core 기본 사항
author: rick-anderson
description: ASP.NET Core 앱을 구축하기 위한 기본적인 개념을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/15/2020
uid: fundamentals/index
ms.openlocfilehash: 7533242140c31a937f32cc9082d760103347ce25
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80219183"
---
# <a name="aspnet-core-fundamentals"></a><span data-ttu-id="66931-103">ASP.NET Core 기본 사항</span><span class="sxs-lookup"><span data-stu-id="66931-103">ASP.NET Core fundamentals</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="66931-104">이 문서는 ASP.NET Core 앱을 개발하는 방법을 이해하기 위한 주요 항목의 개요입니다.</span><span class="sxs-lookup"><span data-stu-id="66931-104">This article is an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="66931-105">Startup 클래스</span><span class="sxs-lookup"><span data-stu-id="66931-105">The Startup class</span></span>

<span data-ttu-id="66931-106">`Startup` 클래스에서는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-106">The `Startup` class is where:</span></span>

* <span data-ttu-id="66931-107">앱에서 요구하는 서비스가 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="66931-107">Services required by the app are configured.</span></span>
* <span data-ttu-id="66931-108">요청 처리 파이프라인이 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="66931-108">The request handling pipeline is defined.</span></span>

<span data-ttu-id="66931-109">‘서비스’는 앱에서 사용되는 구성 요소입니다. </span><span class="sxs-lookup"><span data-stu-id="66931-109">*Services* are components that are used by the app.</span></span> <span data-ttu-id="66931-110">예를 들어 로깅 구성 요소는 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="66931-110">For example, a logging component is a service.</span></span> <span data-ttu-id="66931-111">서비스를 구성(또는 ‘등록’)하는 코드는 `Startup.ConfigureServices` 메서드에 추가됩니다. </span><span class="sxs-lookup"><span data-stu-id="66931-111">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span>

<span data-ttu-id="66931-112">요청 처리 파이프라인은 일련의 ‘미들웨어’ 구성 요소로 구성됩니다. </span><span class="sxs-lookup"><span data-stu-id="66931-112">The request handling pipeline is composed as a series of *middleware* components.</span></span> <span data-ttu-id="66931-113">예를 들어 미들웨어는 정적 파일에 대한 요청을 처리하거나 HTTP 요청을 HTTPS로 리디렉션할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-113">For example, a middleware might handle requests for static files or redirect HTTP requests to HTTPS.</span></span> <span data-ttu-id="66931-114">각 미들웨어는 `HttpContext` 상에서 비동기 작업을 수행한 다음, 파이프라인의 다음 미들웨어를 호출하거나 요청을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-114">Each middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span> <span data-ttu-id="66931-115">요청 처리 파이프라인을 구성하는 코드는 `Startup.Configure` 메서드에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="66931-115">Code to configure the request handling pipeline is added to the `Startup.Configure` method.</span></span>

<span data-ttu-id="66931-116">샘플 `Startup` 클래스는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-116">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=3,12)]

<span data-ttu-id="66931-117">자세한 내용은 <xref:fundamentals/startup>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="66931-117">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="66931-118">종속성 주입(서비스)</span><span class="sxs-lookup"><span data-stu-id="66931-118">Dependency injection (services)</span></span>

<span data-ttu-id="66931-119">ASP.NET Core에는 구성된 서비스를 앱의 클래스에 사용할 수 있도록 만드는 기본 제공 DI(종속성 주입) 프레임워크가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="66931-119">ASP.NET Core has a built-in dependency injection (DI) framework that makes configured services available to an app's classes.</span></span> <span data-ttu-id="66931-120">클래스에서 서비스의 인스턴스를 가져오는 한 가지 방법은 필수 형식의 매개 변수를 사용하여 생성자를 만드는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="66931-120">One way to get an instance of a service in a class is to create a constructor with a parameter of the required type.</span></span> <span data-ttu-id="66931-121">해당 매개 변수는 서비스 형식 또는 인터페이스일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-121">The parameter can be the service type or an interface.</span></span> <span data-ttu-id="66931-122">DI 시스템에서는 런타임 시 서비스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-122">The DI system provides the service at runtime.</span></span>

<span data-ttu-id="66931-123">Entity Framework Core 컨텍스트 개체를 가져오는 데 DI를 사용하는 클래스는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-123">Here's a class that uses DI to get an Entity Framework Core context object.</span></span> <span data-ttu-id="66931-124">강조 표시된 줄은 생성자 주입의 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="66931-124">The highlighted line is an example of constructor injection:</span></span>

[!code-csharp[](index/snapshots/2.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="66931-125">DI가 기본 제공되기는 하지만 원하는 경우 타사 IoC(Inversion of Control) 컨테이너를 플러그 인할 수 있도록 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-125">While DI is built in, it's designed to let you plug in a third-party Inversion of Control (IoC) container if you prefer.</span></span>

<span data-ttu-id="66931-126">자세한 내용은 <xref:fundamentals/dependency-injection>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="66931-126">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="66931-127">미들웨어</span><span class="sxs-lookup"><span data-stu-id="66931-127">Middleware</span></span>

<span data-ttu-id="66931-128">요청 처리 파이프라인은 일련의 미들웨어 구성 요소로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="66931-128">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="66931-129">각 구성 요소는 `HttpContext` 상에서 비동기 작업을 수행한 다음, 파이프라인의 다음 미들웨어를 호출하거나 요청을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-129">Each component performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="66931-130">규칙에 따라 미들웨어 구성 요소는 `Startup.Configure` 메서드에서 `Use...` 확장 메서드를 호출하여 파이프라인에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="66931-130">By convention, a middleware component is added to the pipeline by invoking its `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="66931-131">예를 들어 정적 파일을 렌더링하도록 설정하려면 `UseStaticFiles`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-131">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="66931-132">다음 예제에서 강조 표시된 코드는 요청 처리 파이프라인을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-132">The highlighted code in the following example configures the request handling pipeline:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=14-16)]

<span data-ttu-id="66931-133">ASP.NET Core는 풍부한 일련의 기본 제공 미들웨어를 포함하고 있으며, 사용자 지정 미들웨어를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-133">ASP.NET Core includes a rich set of built-in middleware, and you can write custom middleware.</span></span>

<span data-ttu-id="66931-134">자세한 내용은 <xref:fundamentals/middleware/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="66931-134">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="66931-135">호스트</span><span class="sxs-lookup"><span data-stu-id="66931-135">Host</span></span>

<span data-ttu-id="66931-136">ASP.NET Core 앱은 시작 시 ‘호스트’를 빌드합니다. </span><span class="sxs-lookup"><span data-stu-id="66931-136">An ASP.NET Core app builds a *host* on startup.</span></span> <span data-ttu-id="66931-137">호스트는 다음과 같은 앱의 리소스를 모두 캡슐화하는 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="66931-137">The host is an object that encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="66931-138">HTTP 서버 구현</span><span class="sxs-lookup"><span data-stu-id="66931-138">An HTTP server implementation</span></span>
* <span data-ttu-id="66931-139">미들웨어 구성 요소</span><span class="sxs-lookup"><span data-stu-id="66931-139">Middleware components</span></span>
* <span data-ttu-id="66931-140">로깅</span><span class="sxs-lookup"><span data-stu-id="66931-140">Logging</span></span>
* <span data-ttu-id="66931-141">DI</span><span class="sxs-lookup"><span data-stu-id="66931-141">DI</span></span>
* <span data-ttu-id="66931-142">Configuration</span><span class="sxs-lookup"><span data-stu-id="66931-142">Configuration</span></span>

<span data-ttu-id="66931-143">하나의 개체에 앱의 모든 상호 종속적 리소스를 포함하는 주요 원인은 수명 관리 즉, 앱 시작 및 종료에 대한 제어 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="66931-143">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="66931-144">두 호스트, 제네릭 호스트와 웹 호스트를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-144">Two hosts are available: the Generic Host and the Web Host.</span></span> <span data-ttu-id="66931-145">제네릭 호스트를 사용하는 것이 좋고, 웹 호스트는 이전 버전과만 호환 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-145">The Generic Host is recommended, and the Web Host is available only for backwards compatibility.</span></span>

<span data-ttu-id="66931-146">호스트를 만드는 코드는 `Program.Main`에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-146">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/snapshots/3.x/Program1.cs)]

<span data-ttu-id="66931-147">`CreateDefaultBuilder` 및 `ConfigureWebHostDefaults` 메서드는 다음과 같이 일반적으로 사용되는 옵션으로 호스트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-147">The `CreateDefaultBuilder` and `ConfigureWebHostDefaults` methods configure a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="66931-148">[Kestrel](#servers)을 웹 서버로 사용하고 IIS 통합을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-148">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="66931-149">*appsettings.json*, *appsettings.{Environment Name}.json*, 환경 변수, 명령줄 인수 및 기타 구성 소스의 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-149">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="66931-150">콘솔 및 디버그 공급자에게 로깅 출력을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="66931-150">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="66931-151">자세한 내용은 <xref:fundamentals/host/generic-host>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="66931-151">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="66931-152">비 웹 시나리오</span><span class="sxs-lookup"><span data-stu-id="66931-152">Non-web scenarios</span></span>

<span data-ttu-id="66931-153">제네릭 호스트를 통해 다른 형식의 앱에서 로깅, DI(종속성 주입), 구성 및 앱 수명 관리와 같은 교차 프레임워크 확장을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-153">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="66931-154">자세한 내용은 <xref:fundamentals/host/generic-host> 및 <xref:fundamentals/host/hosted-services>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="66931-154">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="66931-155">서버</span><span class="sxs-lookup"><span data-stu-id="66931-155">Servers</span></span>

<span data-ttu-id="66931-156">ASP.NET Core 앱은 HTTP 요청을 수신하기 위해 HTTP 서버 구현을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-156">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="66931-157">해당 서버는 `HttpContext`에 구성된 일련의 [요청 기능](xref:fundamentals/request-features)으로 앱에 대한 요청을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-157">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

# <a name="windows"></a>[<span data-ttu-id="66931-158">Windows</span><span class="sxs-lookup"><span data-stu-id="66931-158">Windows</span></span>](#tab/windows)

<span data-ttu-id="66931-159">ASP.NET Core는 다음과 같은 서버 구현을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-159">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="66931-160">*Kestrel*은 플랫폼 간 웹 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="66931-160">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="66931-161">Kestrel은 보통 [IIS](https://www.iis.net/)를 사용하여 역방향 프록시 구성에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="66931-161">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="66931-162">Kestrel은 ASP.NET Core 2.0 이상에서 인터넷에 직접 공개되는 공용 연결 에지 서버로 실행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-162">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="66931-163">‘IIS HTTP 서버’는 IIS를 사용하는 Windows용 서버입니다. </span><span class="sxs-lookup"><span data-stu-id="66931-163">*IIS HTTP Server* is a server for Windows that uses IIS.</span></span> <span data-ttu-id="66931-164">이 서버를 사용하면 ASP.NET Core 앱 및 IIS는 동일한 프로세스에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="66931-164">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="66931-165">*HTTP.sys*는 IIS에서 사용되지 않는 Windows용 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="66931-165">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="66931-166">macOS</span><span class="sxs-lookup"><span data-stu-id="66931-166">macOS</span></span>](#tab/macos)

<span data-ttu-id="66931-167">ASP.NET Core는 *Kestrel* 플랫폼 간 서버 구현을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-167">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="66931-168">Kestrel은 ASP.NET Core 2.0 이상에서 인터넷에 직접 공개되는 공용 연결 에지 서버로 실행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-168">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="66931-169">Kestrel은 보통 [Nginx](https://nginx.org) 또는 [Apache](https://httpd.apache.org/)를 사용하여 역방향 프록시 구성에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="66931-169">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="66931-170">Linux</span><span class="sxs-lookup"><span data-stu-id="66931-170">Linux</span></span>](#tab/linux)

<span data-ttu-id="66931-171">ASP.NET Core는 *Kestrel* 플랫폼 간 서버 구현을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-171">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="66931-172">Kestrel은 ASP.NET Core 2.0 이상에서 인터넷에 직접 공개되는 공용 연결 에지 서버로 실행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-172">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="66931-173">Kestrel은 보통 [Nginx](https://nginx.org) 또는 [Apache](https://httpd.apache.org/)를 사용하여 역방향 프록시 구성에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="66931-173">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

<span data-ttu-id="66931-174">자세한 내용은 <xref:fundamentals/servers/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="66931-174">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="66931-175">Configuration</span><span class="sxs-lookup"><span data-stu-id="66931-175">Configuration</span></span>

<span data-ttu-id="66931-176">ASP.NET Core는 정렬된 일련의 구성 공급 기업에서 이름-값 쌍으로 설정을 가져오는 구성 프레임워크를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-176">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="66931-177">*.json* 파일, *.xml* 파일, 환경 변수 및 명령줄 인수와 같은 다양한 원본의 기본 제공 구성 공급 기업이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-177">There are built-in configuration providers for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="66931-178">또는 사용자 지정 구성 공급 기업을 작성할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-178">You can also write custom configuration providers.</span></span>

<span data-ttu-id="66931-179">예를 들어 구성이 *appsettings.json* 및 환경 변수에서 제공되도록 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-179">For example, you could specify that configuration comes from *appsettings.json* and environment variables.</span></span> <span data-ttu-id="66931-180">그런 다음, *ConnectionString* 값이 요청되면 프레임워크는 먼저 *appsettings.json* 파일을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-180">Then when the value of *ConnectionString* is requested, the framework looks first in the *appsettings.json* file.</span></span> <span data-ttu-id="66931-181">값을 찾았지만 환경 변수에서도 값을 찾을 수 있다면 환경 변수의 값이 우선 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="66931-181">If the value is found there but also in an environment variable, the value from the environment variable would take precedence.</span></span>

<span data-ttu-id="66931-182">ASP.NET Core는 암호와 같은 기밀 구성 데이터를 관리하기 위해 [비밀 관리자 도구](xref:security/app-secrets)를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-182">For managing confidential configuration data such as passwords, ASP.NET Core provides a [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="66931-183">프로덕션 비밀의 경우 [Azure Key Vault](xref:security/key-vault-configuration)를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-183">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="66931-184">자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="66931-184">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="options"></a><span data-ttu-id="66931-185">옵션</span><span class="sxs-lookup"><span data-stu-id="66931-185">Options</span></span>

<span data-ttu-id="66931-186">가능할 경우 ASP.NET Core는 구성 값을 저장하고 검색하기 위해 ‘옵션 패턴’을 따릅니다. </span><span class="sxs-lookup"><span data-stu-id="66931-186">Where possible, ASP.NET Core follows the *options pattern* for storing and retrieving configuration values.</span></span> <span data-ttu-id="66931-187">옵션 패턴은 클래스를 사용하여 관련 설정 그룹을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="66931-187">The options pattern uses classes to represent groups of related settings.</span></span>

<span data-ttu-id="66931-188">예를 들어 다음 코드에서는 WebSockets 옵션을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-188">For example, the following code sets WebSockets options:</span></span>

```csharp
var options = new WebSocketOptions  
{  
   KeepAliveInterval = TimeSpan.FromSeconds(120),  
   ReceiveBufferSize = 4096
};  
app.UseWebSockets(options);
```

<span data-ttu-id="66931-189">자세한 내용은 <xref:fundamentals/configuration/options>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="66931-189">For more information, see <xref:fundamentals/configuration/options>.</span></span>

## <a name="environments"></a><span data-ttu-id="66931-190">환경</span><span class="sxs-lookup"><span data-stu-id="66931-190">Environments</span></span>

<span data-ttu-id="66931-191">‘개발’, ‘준비’ 및 ‘프로덕션’과 같은 실행 환경은 ASP.NET Core의 일급 개념입니다.   </span><span class="sxs-lookup"><span data-stu-id="66931-191">Execution environments, such as *Development*, *Staging*, and *Production*, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="66931-192">`ASPNETCORE_ENVIRONMENT` 환경 변수를 설정하여 앱을 실행 중인 환경을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-192">You can specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="66931-193">ASP.NET Core는 앱 시작 시 해당 환경 변수를 읽고 `IHostingEnvironment` 구현에서 값을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-193">ASP.NET Core reads that environment variable at app startup and stores the value in an `IHostingEnvironment` implementation.</span></span> <span data-ttu-id="66931-194">환경 개체는 DI를 통해 앱에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-194">The environment object is available anywhere in the app via DI.</span></span>

<span data-ttu-id="66931-195">`Startup` 클래스의 다음 샘플 코드는 개발 중에 실행할 때만 자세한 오류 정보를 제공하도록 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-195">The following sample code from the `Startup` class configures the app to provide detailed error information only when it runs in development:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup2.cs?highlight=3-6)]

<span data-ttu-id="66931-196">자세한 내용은 <xref:fundamentals/environments>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="66931-196">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="66931-197">로깅</span><span class="sxs-lookup"><span data-stu-id="66931-197">Logging</span></span>

<span data-ttu-id="66931-198">ASP.NET Core는 다양한 기본 제공 및 타사 로깅 공급자와 함께 작동하는 로깅 API를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-198">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="66931-199">지원되는 공급 기업에는 다음이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="66931-199">Available providers include the following:</span></span>

* <span data-ttu-id="66931-200">Console</span><span class="sxs-lookup"><span data-stu-id="66931-200">Console</span></span>
* <span data-ttu-id="66931-201">디버그</span><span class="sxs-lookup"><span data-stu-id="66931-201">Debug</span></span>
* <span data-ttu-id="66931-202">Windows 이벤트 추적</span><span class="sxs-lookup"><span data-stu-id="66931-202">Event Tracing on Windows</span></span>
* <span data-ttu-id="66931-203">Windows 이벤트 로그</span><span class="sxs-lookup"><span data-stu-id="66931-203">Windows Event Log</span></span>
* <span data-ttu-id="66931-204">TraceSource</span><span class="sxs-lookup"><span data-stu-id="66931-204">TraceSource</span></span>
* <span data-ttu-id="66931-205">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="66931-205">Azure App Service</span></span>
* <span data-ttu-id="66931-206">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="66931-206">Azure Application Insights</span></span>

<span data-ttu-id="66931-207">DI에서 `ILogger` 개체를 가져오고 로그 메서드를 호출하여 앱 코드 어디서나 로그를 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-207">Write logs from anywhere in an app's code by getting an `ILogger` object from DI and calling log methods.</span></span>

<span data-ttu-id="66931-208">생성자 주입 및 로깅 메서드 호출을 강조 표시하고 `ILogger` 개체를 사용하는 샘플 코드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-208">Here's sample code that uses an `ILogger` object, with constructor injection and the logging method calls highlighted.</span></span>

[!code-csharp[](index/snapshots/2.x/TodoController.cs?highlight=5,13,17)]

<span data-ttu-id="66931-209">`ILogger` 인터페이스를 통해 필드 개수에 관계 없이 로그 공급 기업에 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-209">The `ILogger` interface lets you pass any number of fields to the logging provider.</span></span> <span data-ttu-id="66931-210">필드는 일반적으로 메시지 문자열을 생성하는 데 사용되지만 공급 기업은 이를 별도 필드로 데이터 저장소에 보낼 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-210">The fields are commonly used to construct a message string, but the provider can also send them as separate fields to a data store.</span></span> <span data-ttu-id="66931-211">이 기능을 사용하면 로그 공급 기업이 [구조적 로깅이라고도 하는 의미 체계 로깅](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)을 구현할 수 있게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="66931-211">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="66931-212">자세한 내용은 <xref:fundamentals/logging/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="66931-212">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="66931-213">라우팅</span><span class="sxs-lookup"><span data-stu-id="66931-213">Routing</span></span>

<span data-ttu-id="66931-214">‘경로’는 처리기에 매핑되는 URL 패턴입니다. </span><span class="sxs-lookup"><span data-stu-id="66931-214">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="66931-215">처리기는 일반적으로 Razor 페이지, MVC 컨트롤러의 작업 메서드 또는 미들웨어와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-215">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="66931-216">ASP.NET Core 라우팅을 사용하면 앱에서 사용되는 URL을 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-216">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="66931-217">자세한 내용은 <xref:fundamentals/routing>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="66931-217">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="66931-218">오류 처리</span><span class="sxs-lookup"><span data-stu-id="66931-218">Error handling</span></span>

<span data-ttu-id="66931-219">ASP.NET Core에는 다음과 같은 오류를 처리하기 위한 기본 제공 기능이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="66931-219">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="66931-220">개발자 예외 페이지</span><span class="sxs-lookup"><span data-stu-id="66931-220">A developer exception page</span></span>
* <span data-ttu-id="66931-221">사용자 지정 오류 페이지</span><span class="sxs-lookup"><span data-stu-id="66931-221">Custom error pages</span></span>
* <span data-ttu-id="66931-222">정적 상태 코드 페이지</span><span class="sxs-lookup"><span data-stu-id="66931-222">Static status code pages</span></span>
* <span data-ttu-id="66931-223">시작 예외 처리</span><span class="sxs-lookup"><span data-stu-id="66931-223">Startup exception handling</span></span>

<span data-ttu-id="66931-224">자세한 내용은 <xref:fundamentals/error-handling>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="66931-224">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="66931-225">HTTP 요청 만들기</span><span class="sxs-lookup"><span data-stu-id="66931-225">Make HTTP requests</span></span>

<span data-ttu-id="66931-226">`HttpClient` 인스턴스를 만들기 위해 `IHttpClientFactory`를 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-226">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="66931-227">팩터리는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-227">The factory:</span></span>

* <span data-ttu-id="66931-228">논리적 `HttpClient` 인스턴스를 구성하고 이름을 지정하기 위한 중앙 위치를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-228">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="66931-229">예를 들어, *github* 클라이언트는 GitHub에 액세스하도록 등록 및 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-229">For example, a *github* client can be registered and configured to access GitHub.</span></span> <span data-ttu-id="66931-230">기본 클라이언트는 다른 용도로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-230">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="66931-231">나가는 요청 미들웨어 파이프라인을 빌드하기 위해 여러 위임 처리기를 연결하고 등록하도록 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-231">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="66931-232">이 패턴은 ASP.NET Core의 인바운드 미들웨어 파이프라인과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-232">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="66931-233">이 패턴은 캐싱, 오류 처리, 직렬화 및 로깅을 포함한 HTTP 요청을 둘러싼 횡단 관심사를 관리하기 위한 메커니즘을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-233">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="66931-234">일시적인 오류를 처리하기 위해 널리 사용되는 타사 라이브러리인 *Polly*와 통합합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-234">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="66931-235">`HttpClient` 수명을 수동으로 관리할 때 발생하는 일반적인 DNS 문제를 피하기 위해 기본 `HttpClientMessageHandler` 인스턴스의 풀링 및 수명을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-235">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="66931-236">팩터리에서 만든 클라이언트를 통해 전송된 모든 요청에 대해 구성 가능한 로깅 경험(`ILogger`을 통해)을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-236">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="66931-237">자세한 내용은 <xref:fundamentals/http-requests>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="66931-237">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="66931-238">콘텐츠 루트</span><span class="sxs-lookup"><span data-stu-id="66931-238">Content root</span></span>

<span data-ttu-id="66931-239">콘텐츠 루트는 다음의 기본 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="66931-239">The content root is the base path to the:</span></span>

* <span data-ttu-id="66931-240">앱을 호스트하는 실행 파일( *.exe*)</span><span class="sxs-lookup"><span data-stu-id="66931-240">Executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="66931-241">앱을 구성하는 컴파일된 어셈블리( *.dll*)</span><span class="sxs-lookup"><span data-stu-id="66931-241">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="66931-242">다음과 같은 앱에서 사용하는 비코드 콘텐츠 파일:</span><span class="sxs-lookup"><span data-stu-id="66931-242">Non-code content files used by the app, such as:</span></span>
  * <span data-ttu-id="66931-243">Razor 파일( *.cshtml*, *.razor*)</span><span class="sxs-lookup"><span data-stu-id="66931-243">Razor files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="66931-244">구성 파일( *.json*, *.xml*)</span><span class="sxs-lookup"><span data-stu-id="66931-244">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="66931-245">데이터 파일( *.db*)</span><span class="sxs-lookup"><span data-stu-id="66931-245">Data files (*.db*)</span></span>
* <span data-ttu-id="66931-246">[웹 루트](#web-root)(일반적으로 *wwwroot* 폴더에 게시됨)</span><span class="sxs-lookup"><span data-stu-id="66931-246">[Web root](#web-root), typically the published *wwwroot* folder.</span></span>

<span data-ttu-id="66931-247">개발 중:</span><span class="sxs-lookup"><span data-stu-id="66931-247">During development:</span></span>

* <span data-ttu-id="66931-248">콘텐츠 루트의 기본값은 프로젝트의 루트 디렉터리입니다.</span><span class="sxs-lookup"><span data-stu-id="66931-248">The content root defaults to the project's root directory.</span></span>
* <span data-ttu-id="66931-249">프로젝트의 루트 디렉터리는 다음을 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="66931-249">The project's root directory is used to create the:</span></span>
  * <span data-ttu-id="66931-250">프로젝트의 루트 디렉터리에서 앱의 비코드 콘텐츠 파일의 경로</span><span class="sxs-lookup"><span data-stu-id="66931-250">Path to the app's non-code content files in the project's root directory.</span></span>
  * <span data-ttu-id="66931-251">[웹 루트](#web-root)(일반적으로 프로젝트의 루트 디렉터리의 *wwwroot* 폴더)</span><span class="sxs-lookup"><span data-stu-id="66931-251">[Web root](#web-root), typically the *wwwroot* folder in the project's root directory.</span></span>

<span data-ttu-id="66931-252">[호스트를 빌드](#host)할 때 지정될 수 있는 대체 콘텐츠 루트 경로</span><span class="sxs-lookup"><span data-stu-id="66931-252">An alternative content root path can be specified when [building the host](#host).</span></span> <span data-ttu-id="66931-253">자세한 내용은 <xref:fundamentals/host/generic-host#contentrootpath>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="66931-253">For more information, see <xref:fundamentals/host/generic-host#contentrootpath>.</span></span>

## <a name="web-root"></a><span data-ttu-id="66931-254">웹 루트</span><span class="sxs-lookup"><span data-stu-id="66931-254">Web root</span></span>

<span data-ttu-id="66931-255">웹 루트는 다음과 같은 공용, 비코드, 정적 리소스 파일의 기본 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="66931-255">The web root is the base path to public, non-code, static resource files, such as:</span></span>

* <span data-ttu-id="66931-256">스타일시트( *.css*)</span><span class="sxs-lookup"><span data-stu-id="66931-256">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="66931-257">JavaScript( *.js*)</span><span class="sxs-lookup"><span data-stu-id="66931-257">JavaScript (*.js*)</span></span>
* <span data-ttu-id="66931-258">이미지( *.png*, *.jpg*)</span><span class="sxs-lookup"><span data-stu-id="66931-258">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="66931-259">정적 파일은 기본적으로 웹 루트 디렉터리 및 하위 디렉터리에서만 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="66931-259">Static files are only served by default from the web root directory (and sub-directories).</span></span>

<span data-ttu-id="66931-260">웹 루트 경로는 *{content root}/wwwroot*를 기본값으로 지정하지만 [호스트를 빌드](#host)할 때 다른 웹 루트를 지정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-260">The web root path defaults to *{content root}/wwwroot*, but a different web root can be specified when [building the host](#host).</span></span> <span data-ttu-id="66931-261">자세한 내용은 <xref:fundamentals/host/generic-host#webroot>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="66931-261">For more information, see <xref:fundamentals/host/generic-host#webroot>.</span></span>

<span data-ttu-id="66931-262">프로젝트 파일에서 [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content)을 사용하여 *wwwroot*에 파일을 게시하지 못하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-262">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="66931-263">다음 예에서는 *wwwroot/local* 디렉터리 및 하위 디렉터리에 콘텐츠를 게시하지 못하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-263">The following example prevents publishing content in the *wwwroot/local* directory and sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="66931-264">정적 ID 자산을 웹 루트에 게시하지 않으려면 <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="66931-264">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

<span data-ttu-id="66931-265">Razor( *.cshtml*) 파일에서는 물결표 슬래시(`~/`)가 웹 루트를 가리킵니다.</span><span class="sxs-lookup"><span data-stu-id="66931-265">In Razor (*.cshtml*) files, the tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="66931-266">`~/`(으)로 시작하는 경로를 ‘가상 경로’라고 합니다. </span><span class="sxs-lookup"><span data-stu-id="66931-266">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="66931-267">자세한 내용은 <xref:fundamentals/static-files>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="66931-267">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="66931-268">이 문서는 ASP.NET Core 앱을 개발하는 방법을 이해하기 위한 주요 항목의 개요입니다.</span><span class="sxs-lookup"><span data-stu-id="66931-268">This article is an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="66931-269">Startup 클래스</span><span class="sxs-lookup"><span data-stu-id="66931-269">The Startup class</span></span>

<span data-ttu-id="66931-270">`Startup` 클래스에서는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-270">The `Startup` class is where:</span></span>

* <span data-ttu-id="66931-271">앱에서 요구하는 서비스가 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="66931-271">Services required by the app are configured.</span></span>
* <span data-ttu-id="66931-272">요청 처리 파이프라인이 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="66931-272">The request handling pipeline is defined.</span></span>

<span data-ttu-id="66931-273">‘서비스’는 앱에서 사용되는 구성 요소입니다. </span><span class="sxs-lookup"><span data-stu-id="66931-273">*Services* are components that are used by the app.</span></span> <span data-ttu-id="66931-274">예를 들어 로깅 구성 요소는 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="66931-274">For example, a logging component is a service.</span></span> <span data-ttu-id="66931-275">서비스를 구성(또는 ‘등록’)하는 코드는 `Startup.ConfigureServices` 메서드에 추가됩니다. </span><span class="sxs-lookup"><span data-stu-id="66931-275">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span>

<span data-ttu-id="66931-276">요청 처리 파이프라인은 일련의 ‘미들웨어’ 구성 요소로 구성됩니다. </span><span class="sxs-lookup"><span data-stu-id="66931-276">The request handling pipeline is composed as a series of *middleware* components.</span></span> <span data-ttu-id="66931-277">예를 들어 미들웨어는 정적 파일에 대한 요청을 처리하거나 HTTP 요청을 HTTPS로 리디렉션할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-277">For example, a middleware might handle requests for static files or redirect HTTP requests to HTTPS.</span></span> <span data-ttu-id="66931-278">각 미들웨어는 `HttpContext` 상에서 비동기 작업을 수행한 다음, 파이프라인의 다음 미들웨어를 호출하거나 요청을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-278">Each middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span> <span data-ttu-id="66931-279">요청 처리 파이프라인을 구성하는 코드는 `Startup.Configure` 메서드에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="66931-279">Code to configure the request handling pipeline is added to the `Startup.Configure` method.</span></span>

<span data-ttu-id="66931-280">샘플 `Startup` 클래스는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-280">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=3,12)]

<span data-ttu-id="66931-281">자세한 내용은 <xref:fundamentals/startup>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="66931-281">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="66931-282">종속성 주입(서비스)</span><span class="sxs-lookup"><span data-stu-id="66931-282">Dependency injection (services)</span></span>

<span data-ttu-id="66931-283">ASP.NET Core에는 구성된 서비스를 앱의 클래스에 사용할 수 있도록 만드는 기본 제공 DI(종속성 주입) 프레임워크가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="66931-283">ASP.NET Core has a built-in dependency injection (DI) framework that makes configured services available to an app's classes.</span></span> <span data-ttu-id="66931-284">클래스에서 서비스의 인스턴스를 가져오는 한 가지 방법은 필수 형식의 매개 변수를 사용하여 생성자를 만드는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="66931-284">One way to get an instance of a service in a class is to create a constructor with a parameter of the required type.</span></span> <span data-ttu-id="66931-285">해당 매개 변수는 서비스 형식 또는 인터페이스일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-285">The parameter can be the service type or an interface.</span></span> <span data-ttu-id="66931-286">DI 시스템에서는 런타임 시 서비스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-286">The DI system provides the service at runtime.</span></span>

<span data-ttu-id="66931-287">Entity Framework Core 컨텍스트 개체를 가져오는 데 DI를 사용하는 클래스는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-287">Here's a class that uses DI to get an Entity Framework Core context object.</span></span> <span data-ttu-id="66931-288">강조 표시된 줄은 생성자 주입의 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="66931-288">The highlighted line is an example of constructor injection:</span></span>

[!code-csharp[](index/snapshots/2.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="66931-289">DI가 기본 제공되기는 하지만 원하는 경우 타사 IoC(Inversion of Control) 컨테이너를 플러그 인할 수 있도록 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-289">While DI is built in, it's designed to let you plug in a third-party Inversion of Control (IoC) container if you prefer.</span></span>

<span data-ttu-id="66931-290">자세한 내용은 <xref:fundamentals/dependency-injection>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="66931-290">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="66931-291">미들웨어</span><span class="sxs-lookup"><span data-stu-id="66931-291">Middleware</span></span>

<span data-ttu-id="66931-292">요청 처리 파이프라인은 일련의 미들웨어 구성 요소로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="66931-292">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="66931-293">각 구성 요소는 `HttpContext` 상에서 비동기 작업을 수행한 다음, 파이프라인의 다음 미들웨어를 호출하거나 요청을 종료합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-293">Each component performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="66931-294">규칙에 따라 미들웨어 구성 요소는 `Startup.Configure` 메서드에서 `Use...` 확장 메서드를 호출하여 파이프라인에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="66931-294">By convention, a middleware component is added to the pipeline by invoking its `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="66931-295">예를 들어 정적 파일을 렌더링하도록 설정하려면 `UseStaticFiles`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-295">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="66931-296">다음 예제에서 강조 표시된 코드는 요청 처리 파이프라인을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-296">The highlighted code in the following example configures the request handling pipeline:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=14-16)]

<span data-ttu-id="66931-297">ASP.NET Core는 풍부한 일련의 기본 제공 미들웨어를 포함하고 있으며, 사용자 지정 미들웨어를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-297">ASP.NET Core includes a rich set of built-in middleware, and you can write custom middleware.</span></span>

<span data-ttu-id="66931-298">자세한 내용은 <xref:fundamentals/middleware/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="66931-298">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="66931-299">호스트</span><span class="sxs-lookup"><span data-stu-id="66931-299">Host</span></span>

<span data-ttu-id="66931-300">ASP.NET Core 앱은 시작 시 ‘호스트’를 빌드합니다. </span><span class="sxs-lookup"><span data-stu-id="66931-300">An ASP.NET Core app builds a *host* on startup.</span></span> <span data-ttu-id="66931-301">호스트는 다음과 같은 앱의 리소스를 모두 캡슐화하는 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="66931-301">The host is an object that encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="66931-302">HTTP 서버 구현</span><span class="sxs-lookup"><span data-stu-id="66931-302">An HTTP server implementation</span></span>
* <span data-ttu-id="66931-303">미들웨어 구성 요소</span><span class="sxs-lookup"><span data-stu-id="66931-303">Middleware components</span></span>
* <span data-ttu-id="66931-304">로깅</span><span class="sxs-lookup"><span data-stu-id="66931-304">Logging</span></span>
* <span data-ttu-id="66931-305">DI</span><span class="sxs-lookup"><span data-stu-id="66931-305">DI</span></span>
* <span data-ttu-id="66931-306">Configuration</span><span class="sxs-lookup"><span data-stu-id="66931-306">Configuration</span></span>

<span data-ttu-id="66931-307">하나의 개체에 앱의 모든 상호 종속적 리소스를 포함하는 주요 원인은 수명 관리 즉, 앱 시작 및 종료에 대한 제어 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="66931-307">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="66931-308">두 호스트, 웹 호스트와 제네릭 호스트를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-308">Two hosts are available: the Web Host and the Generic Host.</span></span> <span data-ttu-id="66931-309">ASP.NET Core 2.x에서 제네릭 호스트는 비 웹 시나리오에만 해당됩니다.</span><span class="sxs-lookup"><span data-stu-id="66931-309">In ASP.NET Core 2.x, the Generic Host is only for non-web scenarios.</span></span>

<span data-ttu-id="66931-310">호스트를 만드는 코드는 `Program.Main`에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-310">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/snapshots/2.x/Program1.cs)]

<span data-ttu-id="66931-311">`CreateDefaultBuilder` 메서드는 다음과 같이 일반적으로 사용되는 옵션으로 호스트를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-311">The `CreateDefaultBuilder` method configures a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="66931-312">[Kestrel](#servers)을 웹 서버로 사용하고 IIS 통합을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-312">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="66931-313">*appsettings.json*, *appsettings.{Environment Name}.json*, 환경 변수, 명령줄 인수 및 기타 구성 소스의 구성을 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-313">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="66931-314">콘솔 및 디버그 공급자에게 로깅 출력을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="66931-314">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="66931-315">자세한 내용은 <xref:fundamentals/host/web-host>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="66931-315">For more information, see <xref:fundamentals/host/web-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="66931-316">비 웹 시나리오</span><span class="sxs-lookup"><span data-stu-id="66931-316">Non-web scenarios</span></span>

<span data-ttu-id="66931-317">제네릭 호스트를 통해 다른 형식의 앱에서 로깅, DI(종속성 주입), 구성 및 앱 수명 관리와 같은 교차 프레임워크 확장을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-317">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="66931-318">자세한 내용은 <xref:fundamentals/host/generic-host> 및 <xref:fundamentals/host/hosted-services>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="66931-318">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="66931-319">서버</span><span class="sxs-lookup"><span data-stu-id="66931-319">Servers</span></span>

<span data-ttu-id="66931-320">ASP.NET Core 앱은 HTTP 요청을 수신하기 위해 HTTP 서버 구현을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-320">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="66931-321">해당 서버는 `HttpContext`에 구성된 일련의 [요청 기능](xref:fundamentals/request-features)으로 앱에 대한 요청을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-321">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="66931-322">Windows</span><span class="sxs-lookup"><span data-stu-id="66931-322">Windows</span></span>](#tab/windows)

<span data-ttu-id="66931-323">ASP.NET Core는 다음과 같은 서버 구현을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-323">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="66931-324">*Kestrel*은 플랫폼 간 웹 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="66931-324">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="66931-325">Kestrel은 보통 [IIS](https://www.iis.net/)를 사용하여 역방향 프록시 구성에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="66931-325">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="66931-326">Kestrel은 인터넷에 직접 공개되는 공용 연결 에지 서버로 실행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-326">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="66931-327">‘IIS HTTP 서버’  는 IIS를 사용하는 Windows용 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="66931-327">*IIS HTTP Server* is a server for windows that uses IIS.</span></span> <span data-ttu-id="66931-328">이 서버를 사용하면 ASP.NET Core 앱 및 IIS는 동일한 프로세스에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="66931-328">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="66931-329">*HTTP.sys*는 IIS에서 사용되지 않는 Windows용 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="66931-329">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="66931-330">macOS</span><span class="sxs-lookup"><span data-stu-id="66931-330">macOS</span></span>](#tab/macos)

<span data-ttu-id="66931-331">ASP.NET Core는 *Kestrel* 플랫폼 간 서버 구현을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-331">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="66931-332">Kestrel은 인터넷에 직접 공개되는 공용 연결 에지 서버로 실행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-332">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="66931-333">Kestrel은 보통 [Nginx](https://nginx.org) 또는 [Apache](https://httpd.apache.org/)를 사용하여 역방향 프록시 구성에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="66931-333">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="66931-334">Linux</span><span class="sxs-lookup"><span data-stu-id="66931-334">Linux</span></span>](#tab/linux)

<span data-ttu-id="66931-335">ASP.NET Core는 *Kestrel* 플랫폼 간 서버 구현을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-335">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="66931-336">Kestrel은 인터넷에 직접 공개되는 공용 연결 에지 서버로 실행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-336">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="66931-337">Kestrel은 보통 [Nginx](https://nginx.org) 또는 [Apache](https://httpd.apache.org/)를 사용하여 역방향 프록시 구성에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="66931-337">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="66931-338">Windows</span><span class="sxs-lookup"><span data-stu-id="66931-338">Windows</span></span>](#tab/windows)

<span data-ttu-id="66931-339">ASP.NET Core는 다음과 같은 서버 구현을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-339">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="66931-340">*Kestrel*은 플랫폼 간 웹 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="66931-340">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="66931-341">Kestrel은 보통 [IIS](https://www.iis.net/)를 사용하여 역방향 프록시 구성에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="66931-341">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="66931-342">Kestrel은 인터넷에 직접 공개되는 공용 연결 에지 서버로 실행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-342">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="66931-343">*HTTP.sys*는 IIS에서 사용되지 않는 Windows용 서버입니다.</span><span class="sxs-lookup"><span data-stu-id="66931-343">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="66931-344">macOS</span><span class="sxs-lookup"><span data-stu-id="66931-344">macOS</span></span>](#tab/macos)

<span data-ttu-id="66931-345">ASP.NET Core는 *Kestrel* 플랫폼 간 서버 구현을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-345">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="66931-346">Kestrel은 인터넷에 직접 공개되는 공용 연결 에지 서버로 실행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-346">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="66931-347">Kestrel은 보통 [Nginx](https://nginx.org) 또는 [Apache](https://httpd.apache.org/)를 사용하여 역방향 프록시 구성에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="66931-347">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="66931-348">Linux</span><span class="sxs-lookup"><span data-stu-id="66931-348">Linux</span></span>](#tab/linux)

<span data-ttu-id="66931-349">ASP.NET Core는 *Kestrel* 플랫폼 간 서버 구현을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-349">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="66931-350">Kestrel은 인터넷에 직접 공개되는 공용 연결 에지 서버로 실행할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-350">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="66931-351">Kestrel은 보통 [Nginx](https://nginx.org) 또는 [Apache](https://httpd.apache.org/)를 사용하여 역방향 프록시 구성에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="66931-351">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="66931-352">자세한 내용은 <xref:fundamentals/servers/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="66931-352">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="66931-353">Configuration</span><span class="sxs-lookup"><span data-stu-id="66931-353">Configuration</span></span>

<span data-ttu-id="66931-354">ASP.NET Core는 정렬된 일련의 구성 공급 기업에서 이름-값 쌍으로 설정을 가져오는 구성 프레임워크를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-354">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="66931-355">*.json* 파일, *.xml* 파일, 환경 변수 및 명령줄 인수와 같은 다양한 원본의 기본 제공 구성 공급 기업이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-355">There are built-in configuration providers for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="66931-356">또는 사용자 지정 구성 공급 기업을 작성할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-356">You can also write custom configuration providers.</span></span>

<span data-ttu-id="66931-357">예를 들어 구성이 *appsettings.json* 및 환경 변수에서 제공되도록 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-357">For example, you could specify that configuration comes from *appsettings.json* and environment variables.</span></span> <span data-ttu-id="66931-358">그런 다음, *ConnectionString* 값이 요청되면 프레임워크는 먼저 *appsettings.json* 파일을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-358">Then when the value of *ConnectionString* is requested, the framework looks first in the *appsettings.json* file.</span></span> <span data-ttu-id="66931-359">값을 찾았지만 환경 변수에서도 값을 찾을 수 있다면 환경 변수의 값이 우선 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="66931-359">If the value is found there but also in an environment variable, the value from the environment variable would take precedence.</span></span>

<span data-ttu-id="66931-360">ASP.NET Core는 암호와 같은 기밀 구성 데이터를 관리하기 위해 [비밀 관리자 도구](xref:security/app-secrets)를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-360">For managing confidential configuration data such as passwords, ASP.NET Core provides a [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="66931-361">프로덕션 비밀의 경우 [Azure Key Vault](xref:security/key-vault-configuration)를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-361">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="66931-362">자세한 내용은 <xref:fundamentals/configuration/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="66931-362">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="options"></a><span data-ttu-id="66931-363">옵션</span><span class="sxs-lookup"><span data-stu-id="66931-363">Options</span></span>

<span data-ttu-id="66931-364">가능할 경우 ASP.NET Core는 구성 값을 저장하고 검색하기 위해 ‘옵션 패턴’을 따릅니다. </span><span class="sxs-lookup"><span data-stu-id="66931-364">Where possible, ASP.NET Core follows the *options pattern* for storing and retrieving configuration values.</span></span> <span data-ttu-id="66931-365">옵션 패턴은 클래스를 사용하여 관련 설정 그룹을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="66931-365">The options pattern uses classes to represent groups of related settings.</span></span>

<span data-ttu-id="66931-366">예를 들어 다음 코드에서는 WebSockets 옵션을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-366">For example, the following code sets WebSockets options:</span></span>

```csharp
var options = new WebSocketOptions  
{  
   KeepAliveInterval = TimeSpan.FromSeconds(120),  
   ReceiveBufferSize = 4096
};  
app.UseWebSockets(options);
```

<span data-ttu-id="66931-367">자세한 내용은 <xref:fundamentals/configuration/options>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="66931-367">For more information, see <xref:fundamentals/configuration/options>.</span></span>

## <a name="environments"></a><span data-ttu-id="66931-368">환경</span><span class="sxs-lookup"><span data-stu-id="66931-368">Environments</span></span>

<span data-ttu-id="66931-369">‘개발’, ‘준비’ 및 ‘프로덕션’과 같은 실행 환경은 ASP.NET Core의 일급 개념입니다.   </span><span class="sxs-lookup"><span data-stu-id="66931-369">Execution environments, such as *Development*, *Staging*, and *Production*, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="66931-370">`ASPNETCORE_ENVIRONMENT` 환경 변수를 설정하여 앱을 실행 중인 환경을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-370">You can specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="66931-371">ASP.NET Core는 앱 시작 시 해당 환경 변수를 읽고 `IHostingEnvironment` 구현에서 값을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-371">ASP.NET Core reads that environment variable at app startup and stores the value in an `IHostingEnvironment` implementation.</span></span> <span data-ttu-id="66931-372">환경 개체는 DI를 통해 앱에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-372">The environment object is available anywhere in the app via DI.</span></span>

<span data-ttu-id="66931-373">`Startup` 클래스의 다음 샘플 코드는 개발 중에 실행할 때만 자세한 오류 정보를 제공하도록 앱을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-373">The following sample code from the `Startup` class configures the app to provide detailed error information only when it runs in development:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup2.cs?highlight=3-6)]

<span data-ttu-id="66931-374">자세한 내용은 <xref:fundamentals/environments>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="66931-374">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="66931-375">로깅</span><span class="sxs-lookup"><span data-stu-id="66931-375">Logging</span></span>

<span data-ttu-id="66931-376">ASP.NET Core는 다양한 기본 제공 및 타사 로깅 공급자와 함께 작동하는 로깅 API를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-376">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="66931-377">지원되는 공급 기업에는 다음이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="66931-377">Available providers include the following:</span></span>

* <span data-ttu-id="66931-378">Console</span><span class="sxs-lookup"><span data-stu-id="66931-378">Console</span></span>
* <span data-ttu-id="66931-379">디버그</span><span class="sxs-lookup"><span data-stu-id="66931-379">Debug</span></span>
* <span data-ttu-id="66931-380">Windows 이벤트 추적</span><span class="sxs-lookup"><span data-stu-id="66931-380">Event Tracing on Windows</span></span>
* <span data-ttu-id="66931-381">Windows 이벤트 로그</span><span class="sxs-lookup"><span data-stu-id="66931-381">Windows Event Log</span></span>
* <span data-ttu-id="66931-382">TraceSource</span><span class="sxs-lookup"><span data-stu-id="66931-382">TraceSource</span></span>
* <span data-ttu-id="66931-383">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="66931-383">Azure App Service</span></span>
* <span data-ttu-id="66931-384">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="66931-384">Azure Application Insights</span></span>

<span data-ttu-id="66931-385">DI에서 `ILogger` 개체를 가져오고 로그 메서드를 호출하여 앱 코드 어디서나 로그를 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-385">Write logs from anywhere in an app's code by getting an `ILogger` object from DI and calling log methods.</span></span>

<span data-ttu-id="66931-386">생성자 주입 및 로깅 메서드 호출을 강조 표시하고 `ILogger` 개체를 사용하는 샘플 코드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-386">Here's sample code that uses an `ILogger` object, with constructor injection and the logging method calls highlighted.</span></span>

[!code-csharp[](index/snapshots/2.x/TodoController.cs?highlight=5,13,17)]

<span data-ttu-id="66931-387">`ILogger` 인터페이스를 통해 필드 개수에 관계 없이 로그 공급 기업에 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-387">The `ILogger` interface lets you pass any number of fields to the logging provider.</span></span> <span data-ttu-id="66931-388">필드는 일반적으로 메시지 문자열을 생성하는 데 사용되지만 공급 기업은 이를 별도 필드로 데이터 저장소에 보낼 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-388">The fields are commonly used to construct a message string, but the provider can also send them as separate fields to a data store.</span></span> <span data-ttu-id="66931-389">이 기능을 사용하면 로그 공급 기업이 [구조적 로깅이라고도 하는 의미 체계 로깅](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)을 구현할 수 있게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="66931-389">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="66931-390">자세한 내용은 <xref:fundamentals/logging/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="66931-390">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="66931-391">라우팅</span><span class="sxs-lookup"><span data-stu-id="66931-391">Routing</span></span>

<span data-ttu-id="66931-392">‘경로’는 처리기에 매핑되는 URL 패턴입니다. </span><span class="sxs-lookup"><span data-stu-id="66931-392">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="66931-393">처리기는 일반적으로 Razor 페이지, MVC 컨트롤러의 작업 메서드 또는 미들웨어와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-393">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="66931-394">ASP.NET Core 라우팅을 사용하면 앱에서 사용되는 URL을 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-394">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="66931-395">자세한 내용은 <xref:fundamentals/routing>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="66931-395">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="66931-396">오류 처리</span><span class="sxs-lookup"><span data-stu-id="66931-396">Error handling</span></span>

<span data-ttu-id="66931-397">ASP.NET Core에는 다음과 같은 오류를 처리하기 위한 기본 제공 기능이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="66931-397">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="66931-398">개발자 예외 페이지</span><span class="sxs-lookup"><span data-stu-id="66931-398">A developer exception page</span></span>
* <span data-ttu-id="66931-399">사용자 지정 오류 페이지</span><span class="sxs-lookup"><span data-stu-id="66931-399">Custom error pages</span></span>
* <span data-ttu-id="66931-400">정적 상태 코드 페이지</span><span class="sxs-lookup"><span data-stu-id="66931-400">Static status code pages</span></span>
* <span data-ttu-id="66931-401">시작 예외 처리</span><span class="sxs-lookup"><span data-stu-id="66931-401">Startup exception handling</span></span>

<span data-ttu-id="66931-402">자세한 내용은 <xref:fundamentals/error-handling>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="66931-402">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="66931-403">HTTP 요청 만들기</span><span class="sxs-lookup"><span data-stu-id="66931-403">Make HTTP requests</span></span>

<span data-ttu-id="66931-404">`HttpClient` 인스턴스를 만들기 위해 `IHttpClientFactory`를 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-404">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="66931-405">팩터리는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-405">The factory:</span></span>

* <span data-ttu-id="66931-406">논리적 `HttpClient` 인스턴스를 구성하고 이름을 지정하기 위한 중앙 위치를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-406">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="66931-407">예를 들어, *github* 클라이언트는 GitHub에 액세스하도록 등록 및 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-407">For example, a *github* client can be registered and configured to access GitHub.</span></span> <span data-ttu-id="66931-408">기본 클라이언트는 다른 용도로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-408">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="66931-409">나가는 요청 미들웨어 파이프라인을 빌드하기 위해 여러 위임 처리기를 연결하고 등록하도록 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-409">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="66931-410">이 패턴은 ASP.NET Core의 인바운드 미들웨어 파이프라인과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-410">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="66931-411">이 패턴은 캐싱, 오류 처리, 직렬화 및 로깅을 포함한 HTTP 요청을 둘러싼 횡단 관심사를 관리하기 위한 메커니즘을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-411">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="66931-412">일시적인 오류를 처리하기 위해 널리 사용되는 타사 라이브러리인 *Polly*와 통합합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-412">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="66931-413">`HttpClient` 수명을 수동으로 관리할 때 발생하는 일반적인 DNS 문제를 피하기 위해 기본 `HttpClientMessageHandler` 인스턴스의 풀링 및 수명을 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-413">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="66931-414">팩터리에서 만든 클라이언트를 통해 전송된 모든 요청에 대해 구성 가능한 로깅 경험(`ILogger`을 통해)을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-414">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="66931-415">자세한 내용은 <xref:fundamentals/http-requests>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="66931-415">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="66931-416">콘텐츠 루트</span><span class="sxs-lookup"><span data-stu-id="66931-416">Content root</span></span>

<span data-ttu-id="66931-417">콘텐츠 루트는 다음의 기본 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="66931-417">The content root is the base path to the:</span></span>

* <span data-ttu-id="66931-418">앱을 호스트하는 실행 파일( *.exe*)</span><span class="sxs-lookup"><span data-stu-id="66931-418">Executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="66931-419">앱을 구성하는 컴파일된 어셈블리( *.dll*)</span><span class="sxs-lookup"><span data-stu-id="66931-419">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="66931-420">다음과 같은 앱에서 사용하는 비코드 콘텐츠 파일:</span><span class="sxs-lookup"><span data-stu-id="66931-420">Non-code content files used by the app, such as:</span></span>
  * <span data-ttu-id="66931-421">Razor 파일( *.cshtml*, *.razor*)</span><span class="sxs-lookup"><span data-stu-id="66931-421">Razor files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="66931-422">구성 파일( *.json*, *.xml*)</span><span class="sxs-lookup"><span data-stu-id="66931-422">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="66931-423">데이터 파일( *.db*)</span><span class="sxs-lookup"><span data-stu-id="66931-423">Data files (*.db*)</span></span>
* <span data-ttu-id="66931-424">[웹 루트](#web-root)(일반적으로 *wwwroot* 폴더에 게시됨)</span><span class="sxs-lookup"><span data-stu-id="66931-424">[Web root](#web-root), typically the published *wwwroot* folder.</span></span>

<span data-ttu-id="66931-425">개발 중:</span><span class="sxs-lookup"><span data-stu-id="66931-425">During development:</span></span>

* <span data-ttu-id="66931-426">콘텐츠 루트의 기본값은 프로젝트의 루트 디렉터리입니다.</span><span class="sxs-lookup"><span data-stu-id="66931-426">The content root defaults to the project's root directory.</span></span>
* <span data-ttu-id="66931-427">프로젝트의 루트 디렉터리는 다음을 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="66931-427">The project's root directory is used to create the:</span></span>
  * <span data-ttu-id="66931-428">프로젝트의 루트 디렉터리에서 앱의 비코드 콘텐츠 파일의 경로</span><span class="sxs-lookup"><span data-stu-id="66931-428">Path to the app's non-code content files in the project's root directory.</span></span>
  * <span data-ttu-id="66931-429">[웹 루트](#web-root)(일반적으로 프로젝트의 루트 디렉터리의 *wwwroot* 폴더)</span><span class="sxs-lookup"><span data-stu-id="66931-429">[Web root](#web-root), typically the *wwwroot* folder in the project's root directory.</span></span>

<span data-ttu-id="66931-430">[호스트를 빌드](#host)할 때 지정될 수 있는 대체 콘텐츠 루트 경로</span><span class="sxs-lookup"><span data-stu-id="66931-430">An alternative content root path can be specified when [building the host](#host).</span></span> <span data-ttu-id="66931-431">자세한 내용은 <xref:fundamentals/host/web-host#content-root>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="66931-431">For more information, see <xref:fundamentals/host/web-host#content-root>.</span></span>

## <a name="web-root"></a><span data-ttu-id="66931-432">웹 루트</span><span class="sxs-lookup"><span data-stu-id="66931-432">Web root</span></span>

<span data-ttu-id="66931-433">웹 루트는 다음과 같은 공용, 비코드, 정적 리소스 파일의 기본 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="66931-433">The web root is the base path to public, non-code, static resource files, such as:</span></span>

* <span data-ttu-id="66931-434">스타일시트( *.css*)</span><span class="sxs-lookup"><span data-stu-id="66931-434">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="66931-435">JavaScript( *.js*)</span><span class="sxs-lookup"><span data-stu-id="66931-435">JavaScript (*.js*)</span></span>
* <span data-ttu-id="66931-436">이미지( *.png*, *.jpg*)</span><span class="sxs-lookup"><span data-stu-id="66931-436">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="66931-437">정적 파일은 기본적으로 웹 루트 디렉터리 및 하위 디렉터리에서만 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="66931-437">Static files are only served by default from the web root directory (and sub-directories).</span></span>

<span data-ttu-id="66931-438">웹 루트 경로는 *{content root}/wwwroot*를 기본값으로 지정하지만 [호스트를 빌드](#host)할 때 다른 웹 루트를 지정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="66931-438">The web root path defaults to *{content root}/wwwroot*, but a different web root can be specified when [building the host](#host).</span></span> <span data-ttu-id="66931-439">자세한 내용은 [웹 루트](xref:fundamentals/host/web-host#web-root)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="66931-439">For more information, see [Web root](xref:fundamentals/host/web-host#web-root).</span></span>

<span data-ttu-id="66931-440">프로젝트 파일에서 [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content)을 사용하여 *wwwroot*에 파일을 게시하지 못하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-440">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="66931-441">다음 예에서는 *wwwroot/local* 디렉터리 및 하위 디렉터리에 콘텐츠를 게시하지 못하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="66931-441">The following example prevents publishing content in the *wwwroot/local* directory and sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="66931-442">Razor( *.cshtml*) 파일에서는 물결표 슬래시(`~/`)가 웹 루트를 가리킵니다.</span><span class="sxs-lookup"><span data-stu-id="66931-442">In Razor (*.cshtml*) files, the tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="66931-443">`~/`(으)로 시작하는 경로를 ‘가상 경로’라고 합니다. </span><span class="sxs-lookup"><span data-stu-id="66931-443">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="66931-444">자세한 내용은 <xref:fundamentals/static-files>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="66931-444">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end
