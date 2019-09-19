---
title: ASP.NET Core에서 호스팅 시작 어셈블리 사용
author: guardrex
description: IHostingStartup 구현을 사용하여 외부 어셈블리에서 ASP.NET Core 앱을 강화하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 08/02/2019
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: f20f8c4e9b1089acca6689d14cdf84bef648d461
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71081751"
---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a><span data-ttu-id="ac198-103">ASP.NET Core에서 호스팅 시작 어셈블리 사용</span><span class="sxs-lookup"><span data-stu-id="ac198-103">Use hosting startup assemblies in ASP.NET Core</span></span>

<span data-ttu-id="ac198-104">작성자: [Luke Latham](https://github.com/guardrex), [Pavel Krymets](https://github.com/pakrym)</span><span class="sxs-lookup"><span data-stu-id="ac198-104">By [Luke Latham](https://github.com/guardrex) and [Pavel Krymets](https://github.com/pakrym)</span></span>

<span data-ttu-id="ac198-105">[IHostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup)(호스팅 시작) 구현에서는 외부 어셈블리에서 시작할 때 앱에 향상된 기능을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-105">An [IHostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup) (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="ac198-106">예를 들어 외부 라이브러리는 호스팅 시작 구현을 사용하여 앱에 추가 구성 공급자 또는 서비스를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-106">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="ac198-107">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ac198-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="ac198-108">HostingStartup 특성</span><span class="sxs-lookup"><span data-stu-id="ac198-108">HostingStartup attribute</span></span>

<span data-ttu-id="ac198-109">[HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute) 특성은 런타임에 활성화할 호스팅 시작 어셈블리의 현재 상태를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-109">A [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="ac198-110">항목 어셈블리 또는 `Startup` 클래스가 포함된 어셈블리에서는 `HostingStartup` 특성을 자동으로 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-110">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="ac198-111">`HostingStartup` 특성을 검색할 어셈블리 목록은 [WebHostDefaults.HostingStartupAssembliesKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.hostingstartupassemblieskey)의 구성에서 런타임에 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-111">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.hostingstartupassemblieskey).</span></span> <span data-ttu-id="ac198-112">검색에서 제외할 어셈블리 목록은 [WebHostDefaults.HostingStartupExcludeAssembliesKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.hostingstartupexcludeassemblieskey)에서 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-112">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.hostingstartupexcludeassemblieskey).</span></span> <span data-ttu-id="ac198-113">자세한 내용은 [웹 호스트: 호스팅 시작 어셈블리](xref:fundamentals/host/web-host#hosting-startup-assemblies) 및 [웹 호스트: 호스팅 시작 어셈블리 제외](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ac198-113">For more information, see [Web Host: Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) and [Web Host: Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span></span>

<span data-ttu-id="ac198-114">다음 예제에서는 호스팅 시작 어셈블리의 네임스페이스가 `StartupEnhancement`입니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-114">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="ac198-115">호스팅 시작 코드를 포함하는 클래스는 `StartupEnhancementHostingStartup`입니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-115">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="ac198-116">`HostingStartup` 특성은 일반적으로 호스팅 시작 어셈블리의 `IHostingStartup` 구현 클래스 파일에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-116">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="ac198-117">로드된 호스팅 시작 어셈블리 검색</span><span class="sxs-lookup"><span data-stu-id="ac198-117">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="ac198-118">로드된 호스팅 시작 어셈블리를 검색하려면 로깅을 사용하도록 설정하고 앱의 로그를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-118">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="ac198-119">어셈블리를 로드할 때 발생하는 오류가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-119">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="ac198-120">로드된 호스팅 시작 어셈블리는 디버그 수준에서 기록되고 모든 오류가 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-120">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="ac198-121">호스팅 시작 어셈블리의 자동 로드 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="ac198-121">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="ac198-122">호스팅 시작 어셈블리의 자동 로딩을 사용하지 않으려면 다음 방법 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-122">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="ac198-123">모든 호스팅 시작 어셈블리가 로드되지 않도록 하려면 다음 중 하나를 `true` 또는 `1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-123">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>
  * <span data-ttu-id="ac198-124">[호스팅 시작 방지](xref:fundamentals/host/web-host#prevent-hosting-startup) 호스트 구성 설정.</span><span class="sxs-lookup"><span data-stu-id="ac198-124">[Prevent Hosting Startup](xref:fundamentals/host/web-host#prevent-hosting-startup) host configuration setting.</span></span>
  * <span data-ttu-id="ac198-125">`ASPNETCORE_PREVENTHOSTINGSTARTUP` 환경 변수.</span><span class="sxs-lookup"><span data-stu-id="ac198-125">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>
* <span data-ttu-id="ac198-126">특정 호스팅 시작 어셈블리가 로드되지 않도록 하려면 시작 시 제외할 호스팅 시작 어셈블리의 세미콜론으로 구분된 문자열로 다음 중 하나를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-126">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>
  * <span data-ttu-id="ac198-127">[호스팅 시작 어셈블리 제외](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) 호스트 구성 설정.</span><span class="sxs-lookup"><span data-stu-id="ac198-127">[Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) host configuration setting.</span></span>
  * <span data-ttu-id="ac198-128">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` 환경 변수.</span><span class="sxs-lookup"><span data-stu-id="ac198-128">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="ac198-129">호스트 구성 설정과 환경 변수가 모두 설정되면 호스트 설정이 동작을 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-129">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="ac198-130">호스트 설정 또는 환경 변수를 사용하여 호스팅 시작 어셈블리를 사용하지 않도록 설정하면 어셈블리가 전역적으로 해제되고 앱의 여러 특징을 해제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-130">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="ac198-131">프로젝트</span><span class="sxs-lookup"><span data-stu-id="ac198-131">Project</span></span>

<span data-ttu-id="ac198-132">다음 프로젝트 형식 중 하나를 사용하여 호스팅 시작을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-132">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="ac198-133">클래스 라이브러리</span><span class="sxs-lookup"><span data-stu-id="ac198-133">Class library</span></span>](#class-library)
* [<span data-ttu-id="ac198-134">진입점 없는 콘솔 앱</span><span class="sxs-lookup"><span data-stu-id="ac198-134">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="ac198-135">클래스 라이브러리</span><span class="sxs-lookup"><span data-stu-id="ac198-135">Class library</span></span>

<span data-ttu-id="ac198-136">클래스 라이브러리에서 호스팅 시작 기능 향상을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-136">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="ac198-137">라이브러리에는 `HostingStartup` 특성이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-137">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="ac198-138">[샘플 코드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)에는 Razor 페이지 앱, *HostingStartupApp* 및 클래스 라이브러리, *HostingStartupLibrary*가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-138">The [sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="ac198-139">클래스 라이브러리:</span><span class="sxs-lookup"><span data-stu-id="ac198-139">The class library:</span></span>

* <span data-ttu-id="ac198-140">`IHostingStartup`을 구현하는 호스트 시작 클래스(`ServiceKeyInjection`)가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-140">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="ac198-141">`ServiceKeyInjection`은 메모리 내 구성 공급자([AddInMemoryCollection](/dotnet/api/microsoft.extensions.configuration.memoryconfigurationbuilderextensions.addinmemorycollection))를 사용하여 앱의 구성에 서비스 문자열 쌍을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-141">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](/dotnet/api/microsoft.extensions.configuration.memoryconfigurationbuilderextensions.addinmemorycollection)).</span></span>
* <span data-ttu-id="ac198-142">호스팅 시작의 네임스페이스 및 클래스를 식별하는 `HostingStartup` 특성을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-142">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="ac198-143">`ServiceKeyInjection` 클래스의 [구성](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) 메서드는 [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)를 사용하여 앱에 향상된 기능을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-143">The `ServiceKeyInjection` class's [Configure](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) method uses an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) to add enhancements to an app.</span></span>

<span data-ttu-id="ac198-144">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="ac198-144">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="ac198-145">앱의 인덱스 페이지는 클래스 라이브러리의 호스팅 시작 어셈블리에 의해 설정된 두 키에 대한 구성 값을 읽고 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-145">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="ac198-146">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="ac198-146">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="ac198-147">[샘플 코드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)에는 별도의 호스팅 시작인 *HostingStartupPackage*를 제공하는 NuGet 패키지 프로젝트도 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-147">The [sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="ac198-148">패키지는 앞에서 설명한 클래스 라이브러리와 같은 특징이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-148">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="ac198-149">패키지:</span><span class="sxs-lookup"><span data-stu-id="ac198-149">The package:</span></span>

* <span data-ttu-id="ac198-150">`IHostingStartup`을 구현하는 호스트 시작 클래스(`ServiceKeyInjection`)가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-150">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="ac198-151">`ServiceKeyInjection`은 앱의 구성에 서비스 문자열 쌍을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-151">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="ac198-152">`HostingStartup` 특성을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-152">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="ac198-153">*HostingStartupPackage/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="ac198-153">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="ac198-154">앱의 인덱스 페이지는 패키지의 호스팅 시작 어셈블리에 의해 설정된 두 키에 대한 구성 값을 읽고 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-154">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="ac198-155">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="ac198-155">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="ac198-156">진입점 없는 콘솔 앱</span><span class="sxs-lookup"><span data-stu-id="ac198-156">Console app without an entry point</span></span>

<span data-ttu-id="ac198-157">*이 방법은 .NET Framework가 아닌 .NET Core 앱에서만 사용할 수 있습니다.*</span><span class="sxs-lookup"><span data-stu-id="ac198-157">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="ac198-158">활성화에 대한 컴파일 시간 참조가 필요하지 않은 동적 호스팅 시작 기능 향상은 `HostingStartup` 특성이 포함되는 진입점 없는 콘솔 앱에서 제공될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-158">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="ac198-159">콘솔 앱을 게시하면 런타임 저장소에서 사용할 수 있는 호스팅 시작 어셈블리가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-159">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="ac198-160">다음과 같은 이유로 이 프로세스에서는 진입점 없는 콘솔 앱이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-160">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="ac198-161">호스팅 시작 어셈블리에서 호스팅 시작을 사용하려면 종속성 파일이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-161">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="ac198-162">종속성 파일은 라이브러리가 아닌 앱을 게시하여 생성된 실행 가능한 앱 자산입니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-162">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="ac198-163">라이브러리를 [런타임 패키지 저장소](/dotnet/core/deploying/runtime-store)에 직접 추가할 수 없습니다. 이 저장소에는 공유 런타임을 대상으로 하는 실행 가능한 프로젝트가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-163">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="ac198-164">동적 호스팅 시작 만들기:</span><span class="sxs-lookup"><span data-stu-id="ac198-164">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="ac198-165">호스팅 시작 어셈블리는 다음과 같은 진입점 없는 콘솔 앱에서 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-165">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="ac198-166">`IHostingStartup` 구현이 포함되는 클래스를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-166">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="ac198-167">`IHostingStartup` 구현 클래스를 식별하는 [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute) 특성을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-167">Includes a [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="ac198-168">콘솔 앱은 호스팅 시작의 종속성을 얻기 위해 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-168">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="ac198-169">콘솔 앱을 게시하면 사용되지 않는 종속성이 종속성 파일에서 트리밍됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-169">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="ac198-170">종속성 파일은 호스팅 시작 어셈블리의 런타임 위치를 설정하도록 수정됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-170">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="ac198-171">호스팅 시작 어셈블리 및 해당 종속성 파일은 런타임 패키지 저장소에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-171">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="ac198-172">호스팅 시작 어셈블리 및 해당 종속성 파일을 검색하기 위해 한 쌍의 환경 변수에 나열됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-172">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="ac198-173">콘솔 앱은 [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) 패키지를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-173">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

<span data-ttu-id="ac198-174">[HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute) 특성은 [IWebHost](/dotnet/api/microsoft.aspnetcore.hosting.iwebhost)를 빌드할 때 로드 및 실행을 위해 `IHostingStartup`의 구현으로 클래스를 식별합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-174">A [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the [IWebHost](/dotnet/api/microsoft.aspnetcore.hosting.iwebhost).</span></span> <span data-ttu-id="ac198-175">다음 예제에서 네임스페이스는 `StartupEnhancement`이고 클래스는 `StartupEnhancementHostingStartup`입니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-175">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="ac198-176">클래스는 `IHostingStartup`을 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-176">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="ac198-177">클래스의 [구성](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) 메서드는 [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)를 사용하여 앱에 향상된 기능을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-177">The class's [Configure](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) method uses an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) to add enhancements to an app.</span></span> <span data-ttu-id="ac198-178">호스팅 시작 어셈블리의 `IHostingStartup.Configure`는 사용자 코드로 `Startup.Configure` 전에 런타임에서 호출됩니다. 그러면 사용자 코드가 호스팅 시작 어셈블리에서 제공하는 모든 구성을 덮어쓸 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-178">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="ac198-179">`IHostingStartup` 프로젝트를 빌드할 때 종속성 파일( *.deps.json*)은 어셈블리의 `runtime` 위치를 *bin* 폴더로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-179">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="ac198-180">파일의 일부만 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-180">Only part of the file is shown.</span></span> <span data-ttu-id="ac198-181">이 예제의 어셈블리 이름은 `StartupEnhancement`입니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-181">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="ac198-182">호스팅 시작으로 제공하는 구성</span><span class="sxs-lookup"><span data-stu-id="ac198-182">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="ac198-183">호스팅 시작의 구성을 우선할지, 앱의 구성을 우선할지에 따라 두 가지 방법으로 구성을 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-183">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="ac198-184">앱의 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> 대리자가 실행된 후 구성을 로드하려면 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*>을 사용하여 앱에 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-184">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="ac198-185">이 방법을 사용하면 호스팅 시작 구성이 앱의 구성보다 우선 순위가 높습니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-185">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="ac198-186">앱의 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> 대리자가 실행되기 전에 구성을 로드하려면 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*>을 사용하여 앱에 구성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-186">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="ac198-187">이 방법을 사용하면 호스팅 시작으로 제공된 값보다 앱의 구성 값이 우선 순위가 높습니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-187">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="ac198-188">호스팅 시작 어셈블리 지정</span><span class="sxs-lookup"><span data-stu-id="ac198-188">Specify the hosting startup assembly</span></span>

<span data-ttu-id="ac198-189">클래스 라이브러리 또는 콘솔 앱 제공 호스팅 시작의 경우 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 환경 변수에 호스팅 시작 어셈블리 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-189">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="ac198-190">환경 변수는 세미콜론으로 구분된 어셈블리 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-190">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="ac198-191">호스팅 시작 어셈블리만 `HostingStartup` 특성을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-191">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="ac198-192">샘플 앱(*HostingStartupApp*)의 경우 앞에서 설명한 호스팅 시작을 검색하기 위해 환경 변수가 다음 값으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-192">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="ac198-193">호스팅 시작 어셈블리는 [호스팅 시작 어셈블리](xref:fundamentals/host/web-host#hosting-startup-assemblies) 호스트 구성 설정을 사용하여 설정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-193">A hosting startup assembly can also be set using the [Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) host configuration setting.</span></span>

<span data-ttu-id="ac198-194">여러 개의 호스팅 시작 어셈블이 표시되어 있는 경우 해당 [구성](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) 메서드가 어셈블리가 나열되는 순서대로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-194">When multiple hosting startup assembles are present, their [Configure](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="ac198-195">활성화</span><span class="sxs-lookup"><span data-stu-id="ac198-195">Activation</span></span>

<span data-ttu-id="ac198-196">호스팅 시작 활성화 옵션은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-196">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="ac198-197">[런타임 저장소](#runtime-store) &ndash; 활성화에는 활성화를 위한 컴파일 시간 참조가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-197">[Runtime store](#runtime-store) &ndash; Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="ac198-198">샘플 앱은 호스팅 시작 어셈블리 및 종속성 파일을 *배포* 폴더 안에 배치하여 다중 머신 환경에서 호스팅 시작의 배포를 용이하게 합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-198">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="ac198-199">*배포* 폴더에는 호스팅 시작을 사용하도록 배포 시스템에 환경 변수를 만들거나 수정하는 PowerShell 스크립트도 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-199">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="ac198-200">활성화에 필요한 컴파일 시간 참조</span><span class="sxs-lookup"><span data-stu-id="ac198-200">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="ac198-201">NuGet 패키지</span><span class="sxs-lookup"><span data-stu-id="ac198-201">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="ac198-202">프로젝트 bin 폴더</span><span class="sxs-lookup"><span data-stu-id="ac198-202">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="ac198-203">런타임 저장소</span><span class="sxs-lookup"><span data-stu-id="ac198-203">Runtime store</span></span>

<span data-ttu-id="ac198-204">호스팅 시작 구현은 [런타임 저장소](/dotnet/core/deploying/runtime-store)에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-204">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="ac198-205">향상된 앱에서는 어셈블리에 대한 컴파일 시간 참조가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-205">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="ac198-206">호스팅 시작이 빌드된 후에는 매니페스트 프로젝트 파일과 [dotnet store](/dotnet/core/tools/dotnet-store) 명령을 사용하여 런타임 저장소가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-206">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="ac198-207">샘플 앱(*RuntimeStore* 프로젝트)에서 다음 명령이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-207">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="ac198-208">런타임에서 런타임 저장소를 검색하기 위해 런타임 저장소의 위치가 `DOTNET_SHARED_STORE` 환경 변수에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-208">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="ac198-209">**호스팅 시작의 종속성 파일 수정 및 배치**</span><span class="sxs-lookup"><span data-stu-id="ac198-209">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="ac198-210">향상된 기능에 대한 패키지 참조 없이 향상된 기능을 활성화하려면 `additionalDeps`를 사용하여 런타임에 추가 종속성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-210">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="ac198-211">`additionalDeps`를 사용하면 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-211">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="ac198-212">시작 시 앱의 고유한 *.deps.json* 파일과 병합하기 위해 추가 *.deps.json* 파일 집합을 제공하여 앱의 라이브러리 그래프를 확장합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-212">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="ac198-213">호스팅 시작 어셈블리를 검색 가능하고 로드 가능하게 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-213">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="ac198-214">추가 종속성 파일을 생성하기 위해 권장되는 방법은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-214">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="ac198-215">이전 섹션에서 참조한 런타임 저장소 매니페스트 파일에서 `dotnet publish`를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-215">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="ac198-216">결과 *.deps.json* 파일의 `runtime` 섹션 및 라이브러리에서 매니페스트 참조를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-216">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="ac198-217">예제 프로젝트에서 `store.manifest/1.0.0` 속성은 `targets` 및 `libraries` 섹션에서 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-217">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v2.1",
    "signature": "4ea77c7b75ad1895ae1ea65e6ba2399010514f99"
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v2.1": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp2.1/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-oiQr60vBQW7+nBTmgKLSldj06WNLRTdhOZpAdEbCuapoZ+M2DJH2uQbRLvFT8EGAAv4TAKzNtcztpx5YOgBXQQ==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

<span data-ttu-id="ac198-218">*.deps.json* 파일을 다음 위치에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-218">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="ac198-219">`{ADDITIONAL DEPENDENCIES PATH}` &ndash; `DOTNET_ADDITIONAL_DEPS` 환경 변수에 추가되는 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-219">`{ADDITIONAL DEPENDENCIES PATH}` &ndash; Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="ac198-220">`{SHARED FRAMEWORK NAME}` &ndash; 이 추가 종속성 파일에 대한 공유 프레임워크가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-220">`{SHARED FRAMEWORK NAME}` &ndash; Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="ac198-221">`{SHARED FRAMEWORK VERSION}` &ndash; 최소 공유 프레임워크 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-221">`{SHARED FRAMEWORK VERSION}` &ndash; Minimum shared framework version.</span></span>
* <span data-ttu-id="ac198-222">`{ENHANCEMENT ASSEMBLY NAME}` &ndash; 향상된 기능의 어셈블리 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-222">`{ENHANCEMENT ASSEMBLY NAME}` &ndash; The enhancement's assembly name.</span></span>

<span data-ttu-id="ac198-223">샘플 앱(*RuntimeStore* 프로젝트)에서 추가 종속성 파일은 다음 위치에 배치됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-223">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="ac198-224">런타임에서 런타임 저장소 위치를 검색하기 위해 추가 종속성 파일 위치가 `DOTNET_ADDITIONAL_DEPS` 환경 변수에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-224">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="ac198-225">샘플 앱( *RuntimeStore* 프로젝트)에서 런타임 저장소 빌드 및 추가 종속성 파일 생성은 [PowerShell](/powershell/scripting/powershell-scripting) 스크립트를 사용하여 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-225">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="ac198-226">다양한 운영 체제에 대한 환경 변수를 설정하는 방법의 예제는 [여러 환경 사용](xref:fundamentals/environments)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ac198-226">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="ac198-227">**배포**</span><span class="sxs-lookup"><span data-stu-id="ac198-227">**Deployment**</span></span>

<span data-ttu-id="ac198-228">다중 머신 환경에서 호스팅 시작의 배포를 용이하게 하기 위해 샘플 앱은 다음을 포함하는 게시된 출력에 *배포* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-228">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="ac198-229">호스팅 시작 런타임 저장소.</span><span class="sxs-lookup"><span data-stu-id="ac198-229">The hosting startup runtime store.</span></span>
* <span data-ttu-id="ac198-230">호스팅 시작 종속성 파일.</span><span class="sxs-lookup"><span data-stu-id="ac198-230">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="ac198-231">호스팅 시작 활성화를 지원하기 위해 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` 및 `DOTNET_ADDITIONAL_DEPS` 를 만들거나 수정하는 PowerShell 스크립트입니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-231">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="ac198-232">배포 시스템의 관리 PowerShell 명령 프롬프트에서 스크립트를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-232">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="ac198-233">NuGet 패키지</span><span class="sxs-lookup"><span data-stu-id="ac198-233">NuGet package</span></span>

<span data-ttu-id="ac198-234">NuGet 패키지에서 호스팅 시작 기능 향상을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-234">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="ac198-235">패키지에 `HostingStartup` 특성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-235">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="ac198-236">패키지에서 제공하는 호스팅 시작 형식은 다음 방법 중 하나를 통해 앱에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-236">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="ac198-237">향상된 앱의 프로젝트 파일은 앱의 프로젝트 파일(컴파일 시간 참조)에서 호스팅 시작을 위한 패키지 참조를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-237">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="ac198-238">이곳에서 컴파일 시간 참조를 사용하면 호스팅 시작 어셈블리 및 모든 종속성이 앱의 종속성 파일( *.deps.json*)에 통합됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-238">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="ac198-239">이 방식은 [nuget.org](https://www.nuget.org/)에 게시된 호스팅 시작 어셈블리 패키지에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-239">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="ac198-240">호스팅 시작 종속성 파일은 [런타임 저장소](#runtime-store) 섹션에 설명된 대로 향상된 앱에서 사용할 수 있습니다(컴파일 시간 참조 없이).</span><span class="sxs-lookup"><span data-stu-id="ac198-240">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="ac198-241">NuGet 패키지 및 런타임 저장소에 대한 자세한 내용은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ac198-241">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="ac198-242">플랫폼 간 도구로 NuGet 패키지를 만드는 방법</span><span class="sxs-lookup"><span data-stu-id="ac198-242">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="ac198-243">패키지 게시</span><span class="sxs-lookup"><span data-stu-id="ac198-243">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="ac198-244">런타임 패키지 저장소</span><span class="sxs-lookup"><span data-stu-id="ac198-244">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="ac198-245">프로젝트 bin 폴더</span><span class="sxs-lookup"><span data-stu-id="ac198-245">Project bin folder</span></span>

<span data-ttu-id="ac198-246">호스팅 시작 향상 기능은 향상된 앱의 *bin* 배포 어셈블리에 의해 제공될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-246">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="ac198-247">어셈블리에서 제공하는 호스팅 시작 형식은 다음 방법 중 하나를 통해 앱에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-247">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="ac198-248">향상된 앱의 프로젝트 파일은 호스팅 시작에 대한 어셈블리 참조를 만듭니다(컴파일 시간 참조).</span><span class="sxs-lookup"><span data-stu-id="ac198-248">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="ac198-249">이곳에서 컴파일 시간 참조를 사용하면 호스팅 시작 어셈블리 및 모든 종속성이 앱의 종속성 파일( *.deps.json*)에 통합됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-249">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="ac198-250">이 방법은 배포 시나리오에서 호스팅 시작 어셈블리( *.dll* 파일)에 대한 컴파일 시간 참조를 수행하고 어셈블리를 다음 중 하나로 이동해야 할 때 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-250">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="ac198-251">사용하는 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-251">The consuming project.</span></span>
  * <span data-ttu-id="ac198-252">사용하는 프로젝트에서 액세스할 수 있는 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-252">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="ac198-253">호스팅 시작 종속성 파일은 [런타임 저장소](#runtime-store) 섹션에 설명된 대로 향상된 앱에서 사용할 수 있습니다(컴파일 시간 참조 없이).</span><span class="sxs-lookup"><span data-stu-id="ac198-253">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="ac198-254">.NET Framework를 대상으로 지정하는 경우 어셈블리는 기본 로드 컨텍스트에 로드될 수 있습니다. 즉, .NET Framework는 어셈블리가 다음 위치 중 하나에 있는 것을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-254">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="ac198-255">애플리케이션 기준 위치 경로 &ndash; 앱의 실행 파일( *.exe*)이 있는 *bin* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-255">Application base path &ndash; The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="ac198-256">GAC(전역 어셈블리 캐시) &ndash; GAC는 여러 .NET Framework 앱이 공유하는 어셈블리를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-256">Global Assembly Cache (GAC) &ndash; The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="ac198-257">자세한 내용은 [방법: .NET Framework 설명서의 전역 어셈블리 캐시](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac)에 어셈블리를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-257">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="ac198-258">샘플 코드</span><span class="sxs-lookup"><span data-stu-id="ac198-258">Sample code</span></span>

<span data-ttu-id="ac198-259">[샘플 코드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/)([다운로드 방법](xref:index#how-to-download-a-sample))는 호스팅 시작 구현 시나리오를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-259">The [sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="ac198-260">두 개의 호스팅 시작 어셈블리(클래스 라이브러리)는 각각 한 쌍의 메모리 내 구성 키-값 쌍을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-260">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="ac198-261">NuGet 패키지(*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="ac198-261">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="ac198-262">클래스 라이브러리(*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="ac198-262">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="ac198-263">호스팅 시작은 런타임 저장소 배포 어셈블리에서 활성화됩니다(*StartupDiagnostics*).</span><span class="sxs-lookup"><span data-stu-id="ac198-263">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="ac198-264">어셈블리는 시작 시 다음과 같은 진단 정보를 제공하는 두 개의 미들웨어를 앱에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-264">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="ac198-265">등록된 서비스</span><span class="sxs-lookup"><span data-stu-id="ac198-265">Registered services</span></span>
  * <span data-ttu-id="ac198-266">주소(구성표, 호스트, 기본 경로, 경로, 쿼리 문자열)</span><span class="sxs-lookup"><span data-stu-id="ac198-266">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="ac198-267">연결(원격 IP, 원격 포트, 로컬 IP, 로컬 포트, 클라이언트 인증서)</span><span class="sxs-lookup"><span data-stu-id="ac198-267">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="ac198-268">요청 헤더</span><span class="sxs-lookup"><span data-stu-id="ac198-268">Request headers</span></span>
  * <span data-ttu-id="ac198-269">환경 변수</span><span class="sxs-lookup"><span data-stu-id="ac198-269">Environment variables</span></span>

<span data-ttu-id="ac198-270">샘플을 실행하려면:</span><span class="sxs-lookup"><span data-stu-id="ac198-270">To run the sample:</span></span>

<span data-ttu-id="ac198-271">**NuGet 패키지에서 활성화**</span><span class="sxs-lookup"><span data-stu-id="ac198-271">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="ac198-272">[dotnet pack](/dotnet/core/tools/dotnet-pack) 명령을 사용하여 *HostingStartupPackage* 패키지를 컴파일합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-272">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="ac198-273">*HostingStartupPackage* 패키지의 어셈블리 이름을 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 환경 변수에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-273">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="ac198-274">앱을 컴파일하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-274">Compile and run the app.</span></span> <span data-ttu-id="ac198-275">패키지 참조가 향상된 앱(컴파일 시간 참조)에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-275">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="ac198-276">앱의 프로젝트 파일에 있는 `<PropertyGroup>`에서는 패키지 프로젝트의 출력( *../HostingStartupPackage/bin/Debug*)을 패키지 원본으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-276">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="ac198-277">이렇게 하면 [nuget.org](https://www.nuget.org/)에 패키지를 업로드하지 않고 패키지를 사용할 수 있습니다. 자세한 내용은 HostingStartupApp의 프로젝트 파일에 있는 정보를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ac198-277">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="ac198-278">인덱스 페이지에 의해 렌더링된 서비스 구성 키 값이 패키지의 `ServiceKeyInjection.Configure` 메서드에서 설정된 값과 일치하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-278">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="ac198-279">*HostingStartupPackage* 프로젝트를 변경하고 다시 컴파일하는 경우, 로컬 NuGet 패키지 캐시의 선택을 취소하여 *HostingStartupApp*이 로컬 캐시에서 부실 패키지가 아닌 업데이트된 패키지를 수신하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-279">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="ac198-280">로컬 NuGet 캐시를 지우려면 다음 [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-280">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="ac198-281">**클래스 라이브러리에서 활성화**</span><span class="sxs-lookup"><span data-stu-id="ac198-281">**Activation from a class library**</span></span>

1. <span data-ttu-id="ac198-282">[dotnet build](/dotnet/core/tools/dotnet-build) 명령을 사용하여 *HostingStartupLibrary* 클래스 라이브러리를 컴파일합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-282">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="ac198-283">*HostingStartupLibrary* 클래스 라이브러리의 어셈블리 이름을 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 환경 변수에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-283">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="ac198-284">*bin* - *HostingStartupLibrary.dll* 파일을 클래스 라이브러리의 컴파일된 출력에서 앱의 *bin/Debug* 폴더로 복사하여 클래스 라이브러리의 어셈블리를 앱에 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-284">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="ac198-285">앱을 컴파일하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-285">Compile and run the app.</span></span> <span data-ttu-id="ac198-286">앱의 프로젝트 파일에 있는 `<ItemGroup>`는 클래스 라이브러리의 어셈블리( *.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (컴파일 시간 참조)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ac198-286">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="ac198-287">자세한 내용은 HostingStartupApp의 프로젝트 파일에 있는 정보를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ac198-287">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="ac198-288">인덱스 페이지에 의해 렌더링된 서비스 구성 키 값이 클래스 라이브러리의 `ServiceKeyInjection.Configure` 메서드에서 설정된 값과 일치하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-288">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="ac198-289">**런타임 저장소 배포 어셈블리에서 활성화**</span><span class="sxs-lookup"><span data-stu-id="ac198-289">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="ac198-290">*StartupDiagnostics* 프로젝트는 [PowerShell](/powershell/scripting/powershell-scripting)을 사용하여 해당 *StartupDiagnostics.deps.json* 파일을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-290">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="ac198-291">PowerShell은 Windows 7 SP1 및 Windows Server 2008 R2 SP1부터 Windows에서 기본적으로 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-291">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="ac198-292">다른 플랫폼에서 PowerShell을 가져오려면 [Windows PowerShell 설치](/powershell/scripting/setup/installing-powershell#powershell-core)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ac198-292">To obtain PowerShell on other platforms, see [Installing Windows PowerShell](/powershell/scripting/setup/installing-powershell#powershell-core).</span></span>
1. <span data-ttu-id="ac198-293">*RuntimeStore* 폴더에서 *build.ps1* 스크립트를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-293">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="ac198-294">스크립트는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-294">The script:</span></span>
   * <span data-ttu-id="ac198-295">`StartupDiagnostics` 패키지를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-295">Generates the `StartupDiagnostics` package.</span></span>
   * <span data-ttu-id="ac198-296">*store* 폴더에서 `StartupDiagnostics`의 런타임 저장소를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-296">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="ac198-297">해당 스크립트에서 `dotnet store` 명령은 Windows에 배포된 호스팅 시작에서 `win7-x64` [RID(런타임 식별자)](/dotnet/core/rid-catalog)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-297">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="ac198-298">다른 런타임에 호스팅 시작을 제공할 때 스크립트의 줄 37에서 올바른 RID로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-298">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span>
   * <span data-ttu-id="ac198-299">*additionalDeps/shared/Microsoft.AspNetCore.App/{Shared Framework Version}/* 폴더에서 `StartupDiagnostics`의 `additionalDeps`를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-299">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps/shared/Microsoft.AspNetCore.App/{Shared Framework Version}/* folder.</span></span>
   * <span data-ttu-id="ac198-300">*deployment* 폴더에 *deploy.ps1* 파일을 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-300">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="ac198-301">*배포* 폴더에서 *deploy.ps1* 스크립트를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-301">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="ac198-302">스크립트는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-302">The script appends:</span></span>
   * <span data-ttu-id="ac198-303">`StartupDiagnostics`를 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 환경 변수에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-303">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="ac198-304">호스팅 시작 종속성 경로를 `DOTNET_ADDITIONAL_DEPS` 환경 변수에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-304">The hosting startup dependencies path to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="ac198-305">런타임 저장소 경로를 `DOTNET_SHARED_STORE` 환경 변수에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-305">The runtime store path to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="ac198-306">샘플 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-306">Run the sample app.</span></span>
1. <span data-ttu-id="ac198-307">`/services` 엔드포인트가 앱의 등록된 서비스를 확인하도록 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-307">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="ac198-308">`/diag` 엔드포인트가 진단 정보를 확인하도록 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="ac198-308">Request the `/diag` endpoint to see the diagnostic information.</span></span>
