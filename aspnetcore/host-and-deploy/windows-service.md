---
title: Windows 서비스에서 ASP.NET Core 호스트
author: guardrex
description: Windows 서비스에서 ASP.NET Core 앱을 호스트하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 06/03/2019
uid: host-and-deploy/windows-service
ms.openlocfilehash: 4cfca4b38543ff073bb98dc09b483d96096928ae
ms.sourcegitcommit: 5dd2ce9709c9e41142771e652d1a4bd0b5248cec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2019
ms.locfileid: "66692561"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="fce43-103">Windows 서비스에서 ASP.NET Core 호스트</span><span class="sxs-lookup"><span data-stu-id="fce43-103">Host ASP.NET Core in a Windows Service</span></span>

<span data-ttu-id="fce43-104">작성자: [Luke Latham](https://github.com/guardrex) 및 [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="fce43-104">By [Luke Latham](https://github.com/guardrex) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="fce43-105">IIS를 사용하지 않고 Windows에서 ASP.NET Core 앱을 [Windows 서비스](/dotnet/framework/windows-services/introduction-to-windows-service-applications)로 호스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-105">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="fce43-106">Windows 서비스로 호스트되는 앱은 서버 다시 부팅 후 자동으로 시작됩니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-106">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="fce43-107">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fce43-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="fce43-108">전제 조건</span><span class="sxs-lookup"><span data-stu-id="fce43-108">Prerequisites</span></span>

* [<span data-ttu-id="fce43-109">ASP.NET Core SDK 2.1 이상</span><span class="sxs-lookup"><span data-stu-id="fce43-109">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="fce43-110">PowerShell 6.2 이상</span><span class="sxs-lookup"><span data-stu-id="fce43-110">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

::: moniker range=">= aspnetcore-3.0"

## <a name="worker-service-template"></a><span data-ttu-id="fce43-111">Worker Service 템플릿</span><span class="sxs-lookup"><span data-stu-id="fce43-111">Worker Service template</span></span>

<span data-ttu-id="fce43-112">ASP.NET Core Worker Service 템플릿은 장기간 실행되는 서비스 앱을 작성하기 위한 시작점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-112">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="fce43-113">템플릿을 Windows Service 앱의 기반으로 사용하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-113">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="fce43-114">.NET Core 템플릿에서 Worker Service 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-114">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="fce43-115">[앱 구성](#app-configuration) 섹션의 지침에 따라 Windows Service로 실행할 수 있도록 Worker Service 앱을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-115">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="fce43-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fce43-116">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="fce43-117">새 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-117">Create a new project.</span></span>
1. <span data-ttu-id="fce43-118">**새 ASP.NET Core 웹 애플리케이션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-118">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="fce43-119">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-119">Select **Next**.</span></span>
1. <span data-ttu-id="fce43-120">**프로젝트 이름** 필드에 프로젝트 이름을 제공하거나 기본 프로젝트 이름을 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-120">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="fce43-121">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-121">Select **Create**.</span></span>
1. <span data-ttu-id="fce43-122">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **.NET Core** 및 **ASP.NET Core 3.0**이 선택되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-122">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span>
1. <span data-ttu-id="fce43-123">**Worker Service** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-123">Select the **Worker Service** template.</span></span> <span data-ttu-id="fce43-124">**만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-124">Select **Create**.</span></span>

# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[<span data-ttu-id="fce43-125">Visual Studio Code/.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="fce43-125">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="fce43-126">명령 셸에서 [dotnet new](/dotnet/core/tools/dotnet-new) 명령과 함께 Worker Service(`worker`) 템플릿을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-126">Use the Worker Service (`worker`) template with the [dotnet new](/dotnet/core/tools/dotnet-new) command from a command shell.</span></span> <span data-ttu-id="fce43-127">다음 예제에서는 `ContosoWorkerService`라는 Worker Service 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-127">In the following example, a Worker Service app is created named `ContosoWorkerService`.</span></span> <span data-ttu-id="fce43-128">명령이 실행될 때 `ContosoWorkerService` 앱의 폴더가 자동으로 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-128">A folder for the `ContosoWorkerService` app is created automatically when the command is executed.</span></span>

```console
dotnet new worker -o ContosoWorkerService
```

---

::: moniker-end

## <a name="app-configuration"></a><span data-ttu-id="fce43-129">앱 구성</span><span class="sxs-lookup"><span data-stu-id="fce43-129">App configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fce43-130">[Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices) 패키지에서 제공된 `IHostBuilder.UseWindowsService`는 호스트를 빌드할 때 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-130">`IHostBuilder.UseWindowsService`, provided by the [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices) package, is called when building the host.</span></span> <span data-ttu-id="fce43-131">앱이 Windows 서비스로 실행되는 경우 이 메서드는 다음과 같이 합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-131">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="fce43-132">호스트 수명을 `WindowsServiceLifetime`으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-132">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="fce43-133">콘텐츠 루트를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-133">Sets the content root.</span></span>
* <span data-ttu-id="fce43-134">애플리케이션 이름을 기본 소스 이름으로 사용하여 이벤트 로그에 대한 로깅을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-134">Enables logging to the event log with the application name as the default source name.</span></span>
  * <span data-ttu-id="fce43-135">*appsettings.Production.json* 파일에서 `Logging:LogLevel:Default` 키를 사용하여 로그 수준을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-135">The log level can be configured using the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>
  * <span data-ttu-id="fce43-136">관리자만 새 이벤트 소스를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-136">Only administrators can create new event sources.</span></span> <span data-ttu-id="fce43-137">애플리케이션 이름을 사용하여 이벤트 소스를 만들 수 없는 경우 *Application* 소스에 경고가 기록되고 이벤트 로그가 사용하지 않도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-137">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

[!code-csharp[](windows-service/samples/3.x/AspNetCoreService/Program.cs?name=snippet_Program)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fce43-138">앱에 [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) 및 [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog)에 대한 패키지 참조가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-138">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="fce43-139">서비스 외부에서 실행할 때 테스트 및 디버그하려면 앱이 서비스 또는 콘솔 앱으로 실행되고 있는지 확인하는 코드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-139">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="fce43-140">디버거가 연결되었는지 또는 `--console` 스위치가 있는지 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-140">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="fce43-141">두 조건 중 하나라도 true이면(앱이 서비스로 실행되지 않음) <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-141">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="fce43-142">두 조건이 모두 false이면(앱이 서비스로 실행됨) 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-142">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="fce43-143"><xref:System.IO.Directory.SetCurrentDirectory*>를 호출하고 앱의 게시 위치에 대한 경로를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-143">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="fce43-144"><xref:System.IO.Directory.GetCurrentDirectory*>를 호출하는 경우 Windows 서비스 앱이 *C:\\WINDOWS\\system32* 폴더를 반환하므로 경로를 얻기 위해 <xref:System.IO.Directory.GetCurrentDirectory*>를 호출하지는 마세요.</span><span class="sxs-lookup"><span data-stu-id="fce43-144">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="fce43-145">자세한 내용은 [현재 디렉터리 및 콘텐츠 루트](#current-directory-and-content-root) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fce43-145">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="fce43-146">이 단계는 `CreateWebHostBuilder`에서 앱이 구성되기 전에 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-146">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="fce43-147"><xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>를 호출하여 앱을 서비스로 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-147">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="fce43-148">[명령줄 구성 공급자](xref:fundamentals/configuration/index#command-line-configuration-provider)에는 명령줄 인수의 이름-값 쌍이 필요하므로 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>가 인수를 수신하기 전에 `--console` 스위치가 인수에서 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-148">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="fce43-149">Windows 이벤트 로그에 기록하려면 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>에 EventLog 공급자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-149">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="fce43-150">*appsettings.Production.json* 파일에서 `Logging:LogLevel:Default` 키를 사용하여 로깅 수준을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-150">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="fce43-151">샘플 앱의 다음 예제에서는 앱 내에서 수명 이벤트를 처리하기 위해 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 대신 `RunAsCustomService`가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-151">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="fce43-152">자세한 내용은 [시작 및 중지 이벤트 처리](#handle-starting-and-stopping-events) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fce43-152">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

::: moniker-end

## <a name="deployment-type"></a><span data-ttu-id="fce43-153">배포 유형</span><span class="sxs-lookup"><span data-stu-id="fce43-153">Deployment type</span></span>

<span data-ttu-id="fce43-154">배포 시나리오에 대한 자세한 내용은 [.NET Core 애플리케이션 배포](/dotnet/core/deploying/)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fce43-154">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="fce43-155">FDD(프레임워크 종속 배포)</span><span class="sxs-lookup"><span data-stu-id="fce43-155">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="fce43-156">FDD(프레임워크 종속 배포)에서는 대상 시스템에 .NET Core의 공유 시스템 차원 버전이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-156">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="fce43-157">이 문서의 지침에 따라 FDD 시나리오가 채택된 경우 SDK는 *프레임워크 종속 실행 파일*이라는 실행 파일( *.exe*)을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-157">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fce43-158">다음 속성 요소를 프로젝트 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-158">Add the following property elements to the project file:</span></span>

* <span data-ttu-id="fce43-159">`<OutputType>` &ndash; 앱의 출력 형식(실행 파일의 경우 `Exe`).</span><span class="sxs-lookup"><span data-stu-id="fce43-159">`<OutputType>` &ndash; The app's output type (`Exe` for executable).</span></span>
* <span data-ttu-id="fce43-160">`<LangVersion>` &ndash; C# 언어 버전(`latest` 또는 `preview`).</span><span class="sxs-lookup"><span data-stu-id="fce43-160">`<LangVersion>` &ndash; The C# language version (`latest` or `preview`).</span></span>

<span data-ttu-id="fce43-161">ASP.NET Core 앱을 게시할 때 일반적으로 생성되는 *web.config* 파일은 Windows 서비스 앱에 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-161">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="fce43-162">*web.config* 파일이 생성되지 않도록 하려면 `<IsTransformWebConfigDisabled>` 속성을 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-162">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <OutputType>Exe</OutputType>
  <LangVersion>preview</LangVersion>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="fce43-163">Windows [RID(런타임 식별자)](/dotnet/core/rid-catalog)([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier))는 대상 프레임워크를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-163">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="fce43-164">다음 예제에서는 RID가 `win7-x64`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-164">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="fce43-165">`<SelfContained>` 속성은 `false`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-165">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="fce43-166">해당 속성은 SDK에 Windows용 실행 파일( *.exe*)과 공유 .NET Core Framework에 종속되는 앱을 생성하도록 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-166">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="fce43-167">ASP.NET Core 앱을 게시할 때 일반적으로 생성되는 *web.config* 파일은 Windows 서비스 앱에 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-167">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="fce43-168">*web.config* 파일이 생성되지 않도록 하려면 `<IsTransformWebConfigDisabled>` 속성을 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-168">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="fce43-169">Windows [RID(런타임 식별자)](/dotnet/core/rid-catalog)([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier))는 대상 프레임워크를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-169">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="fce43-170">다음 예제에서는 RID가 `win7-x64`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-170">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="fce43-171">`<SelfContained>` 속성은 `false`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-171">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="fce43-172">해당 속성은 SDK에 Windows용 실행 파일( *.exe*)과 공유 .NET Core Framework에 종속되는 앱을 생성하도록 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-172">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="fce43-173">`<UseAppHost>` 속성은 `true`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-173">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="fce43-174">이 속성은 서비스에 FDD의 활성화 경로(실행 파일, *.exe*)를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-174">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="fce43-175">ASP.NET Core 앱을 게시할 때 일반적으로 생성되는 *web.config* 파일은 Windows 서비스 앱에 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-175">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="fce43-176">*web.config* 파일이 생성되지 않도록 하려면 `<IsTransformWebConfigDisabled>` 속성을 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-176">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.1</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

::: moniker-end

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="fce43-177">SCD(자체 포함 배포)</span><span class="sxs-lookup"><span data-stu-id="fce43-177">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="fce43-178">SCD(자체 포함 배포)에서는 호스트 시스템에 공유 프레임워크가 없어도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-178">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="fce43-179">런타임 및 앱의 종속성은 앱과 함께 배포됩니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-179">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="fce43-180">Windows [RID(런타임 식별자)](/dotnet/core/rid-catalog)는 대상 프레임워크를 포함하는 `<PropertyGroup>`에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-180">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="fce43-181">여러 개의 RID를 게시하려면</span><span class="sxs-lookup"><span data-stu-id="fce43-181">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="fce43-182">세미콜론으로 구분된 목록으로 RID를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-182">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="fce43-183">속성 이름 [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers)(복수)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-183">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="fce43-184">자세한 내용은 [.NET Core RID 카탈로그](/dotnet/core/rid-catalog)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fce43-184">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fce43-185">`<SelfContained>` 속성이 `true`로 설정된 경우:</span><span class="sxs-lookup"><span data-stu-id="fce43-185">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

::: moniker-end

## <a name="service-user-account"></a><span data-ttu-id="fce43-186">서비스 사용자 계정</span><span class="sxs-lookup"><span data-stu-id="fce43-186">Service user account</span></span>

<span data-ttu-id="fce43-187">서비스의 사용자 계정을 만드려면 관리 PowerShell 6 명령 셸에서 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-187">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="fce43-188">Windows 10 2018년 10월 업데이트(버전 1809/빌드 10.0.17763) 이상의 경우:</span><span class="sxs-lookup"><span data-stu-id="fce43-188">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```PowerShell
New-LocalUser -Name {NAME}
```

<span data-ttu-id="fce43-189">Windows 10 2018년 10월 업데이트(버전 1809/빌드 10.0.17763) 이전 버전 Windows OS의 경우:</span><span class="sxs-lookup"><span data-stu-id="fce43-189">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {NAME}"
```

<span data-ttu-id="fce43-190">메시지가 표시되면 [강력한 암호](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements)를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-190">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="fce43-191">만료 <xref:System.DateTime>과 함께 `-AccountExpires` 매개 변수를 [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet에 제공한 경우가 아니면 계정이 만료되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-191">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="fce43-192">자세한 내용은 [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) 및 [서비스 사용자 계정](/windows/desktop/services/service-user-accounts)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fce43-192">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="fce43-193">Active Directory를 사용할 때 사용자를 관리하는 대체 방법은 관리 서비스 계정을 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-193">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="fce43-194">자세한 내용은 [그룹 관리 서비스 계정 개요](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fce43-194">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="fce43-195">서비스로 로그온 권한</span><span class="sxs-lookup"><span data-stu-id="fce43-195">Log on as a service rights</span></span>

<span data-ttu-id="fce43-196">서비스 사용자 계정에 대해 *서비스로 로그온* 권한을 설정하려면:</span><span class="sxs-lookup"><span data-stu-id="fce43-196">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="fce43-197">*secpool.msc*를 실행하여 로컬 보안 정책 편집기를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-197">Open the Local Security Policy editor by running *secpool.msc*.</span></span>
1. <span data-ttu-id="fce43-198">**로컬 정책** 노드를 확장하고 **사용자 권한 할당**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-198">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="fce43-199">**서비스로 로그온** 정책을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-199">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="fce43-200">**사용자 또는 그룹 추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-200">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="fce43-201">다음 방법 중 하나를 사용하여 개체 이름(사용자 계정)을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-201">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="fce43-202">개체 이름 필드에 사용자 계정(`{DOMAIN OR COMPUTER NAME\USER}`)을 입력하고 **확인**을 선택하여 정책에 사용자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-202">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="fce43-203">**고급**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-203">Select **Advanced**.</span></span> <span data-ttu-id="fce43-204">**지금 찾기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-204">Select **Find Now**.</span></span> <span data-ttu-id="fce43-205">목록에서 사용자 계정을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-205">Select the user account from the list.</span></span> <span data-ttu-id="fce43-206">**확인**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-206">Select **OK**.</span></span> <span data-ttu-id="fce43-207">다시 **확인**을 선택하여 정책에 사용자를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-207">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="fce43-208">**확인** 또는 **적용**을 선택하여 변경 내용을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-208">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="fce43-209">Windows 서비스 만들기 및 관리</span><span class="sxs-lookup"><span data-stu-id="fce43-209">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="fce43-210">서비스 만들기</span><span class="sxs-lookup"><span data-stu-id="fce43-210">Create a service</span></span>

<span data-ttu-id="fce43-211">PowerShell 명령을 사용하여 서비스를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-211">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="fce43-212">관리 PowerShell 6 명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-212">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="fce43-213">`{EXE PATH}` &ndash; 호스트의 앱 폴더 경로(예: `d:\myservice`).</span><span class="sxs-lookup"><span data-stu-id="fce43-213">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="fce43-214">경로에 앱의 실행 파일은 포함하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="fce43-214">Don't include the app's executable in the path.</span></span> <span data-ttu-id="fce43-215">후행 슬래시는 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-215">A trailing slash isn't required.</span></span>
* <span data-ttu-id="fce43-216">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; 서비스 사용자 계정(예: `Contoso\ServiceUser`).</span><span class="sxs-lookup"><span data-stu-id="fce43-216">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="fce43-217">`{NAME}` &ndash; 서비스 이름(예: `MyService`).</span><span class="sxs-lookup"><span data-stu-id="fce43-217">`{NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="fce43-218">`{EXE FILE PATH}` &ndash; 앱의 실행 파일 경로(예: `d:\myservice\myservice.exe`).</span><span class="sxs-lookup"><span data-stu-id="fce43-218">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="fce43-219">실행 파일 이름에 확장명을 포함하세요.</span><span class="sxs-lookup"><span data-stu-id="fce43-219">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="fce43-220">`{DESCRIPTION}` &ndash; 서비스 설명(예: `My sample service`).</span><span class="sxs-lookup"><span data-stu-id="fce43-220">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="fce43-221">`{DISPLAY NAME}` &ndash; 서비스 표시 이름(예: `My Service`).</span><span class="sxs-lookup"><span data-stu-id="fce43-221">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="fce43-222">서비스 시작</span><span class="sxs-lookup"><span data-stu-id="fce43-222">Start a service</span></span>

<span data-ttu-id="fce43-223">다음 PowerShell 6 명령을 사용하여 서비스를 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-223">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {NAME}
```

<span data-ttu-id="fce43-224">명령은 서비스를 시작하는 데 몇 초 정도 걸립니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-224">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="fce43-225">서비스의 상태 확인</span><span class="sxs-lookup"><span data-stu-id="fce43-225">Determine a service's status</span></span>

<span data-ttu-id="fce43-226">서비스의 상태를 확인하려면 다음 PowerShell 6 명령을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-226">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {NAME}
```

<span data-ttu-id="fce43-227">상태는 다음 값 중 하나로 보고됩니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-227">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="fce43-228">서비스 중지</span><span class="sxs-lookup"><span data-stu-id="fce43-228">Stop a service</span></span>

<span data-ttu-id="fce43-229">다음 PowerShell 6 명령을 사용하여 서비스를 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-229">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="fce43-230">서비스 제거</span><span class="sxs-lookup"><span data-stu-id="fce43-230">Remove a service</span></span>

<span data-ttu-id="fce43-231">서비스를 중지하는 짧은 지연 후에 다음 PowerShell 6 명령을 사용하여 서비스를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-231">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {NAME}
```

::: moniker range="< aspnetcore-3.0"

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="fce43-232">시작 및 중지 이벤트 처리</span><span class="sxs-lookup"><span data-stu-id="fce43-232">Handle starting and stopping events</span></span>

<span data-ttu-id="fce43-233"><xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*> 및 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> 이벤트를 처리하려면:</span><span class="sxs-lookup"><span data-stu-id="fce43-233">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="fce43-234">`OnStarting`, `OnStarted` 및 `OnStopping` 메서드를 사용하여 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService>에서 파생되는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-234">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="fce43-235">`CustomWebHostService`를 <xref:System.ServiceProcess.ServiceBase.Run*>에 전달하는 <xref:Microsoft.AspNetCore.Hosting.IWebHost>에 대한 확장 메서드를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-235">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="fce43-236">`Program.Main`에서 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> 대신 `RunAsCustomService` 확장 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-236">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="fce43-237">`Program.Main`에서 <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>의 위치를 확인하려면 [배포 유형](#deployment-type) 섹션에 표시된 코드 샘플을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fce43-237">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

::: moniker-end

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="fce43-238">프록시 서버 및 부하 분산 장치 시나리오</span><span class="sxs-lookup"><span data-stu-id="fce43-238">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="fce43-239">인터넷 또는 회사 네트워크의 요청과 상호 작용하고 프록시 또는 부하 분산 장치 뒤에 있는 서비스에는 추가 구성이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-239">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="fce43-240">자세한 내용은 <xref:host-and-deploy/proxy-load-balancer>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="fce43-240">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-https"></a><span data-ttu-id="fce43-241">HTTPS 구성</span><span class="sxs-lookup"><span data-stu-id="fce43-241">Configure HTTPS</span></span>

<span data-ttu-id="fce43-242">보안 엔드포인트로 서비스를 구성하려면:</span><span class="sxs-lookup"><span data-stu-id="fce43-242">To configure a service with a secure endpoint:</span></span>

1. <span data-ttu-id="fce43-243">플랫폼의 인증서 획득 및 배포 메커니즘을 사용하여 호스팅 시스템에 대한 X.509 인증서를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-243">Create an X.509 certificate for the hosting system using your platform's certificate acquisition and deployment mechanisms.</span></span>

1. <span data-ttu-id="fce43-244">[Kestrel 서버 HTTPS 엔드포인트 구성](xref:fundamentals/servers/kestrel#endpoint-configuration)을 지정하여 인증서를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-244">Specify a [Kestrel server HTTPS endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) to use the certificate.</span></span>

<span data-ttu-id="fce43-245">서비스 엔드포인트를 보호하기 위해 ASP.NET Core HTTPS 개발 인증서 사용은 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-245">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="fce43-246">현재 디렉터리 및 콘텐츠 루트</span><span class="sxs-lookup"><span data-stu-id="fce43-246">Current directory and content root</span></span>

<span data-ttu-id="fce43-247">Windows 서비스에 대해 <xref:System.IO.Directory.GetCurrentDirectory*>를 호출하여 반환된 현재 작업 디렉터리는 *C:\\WINDOWS\\system32* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-247">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="fce43-248">*system32* 폴더는 서비스의 파일(예: 설정 파일)을 저장을 저장하는 데 적절한 위치가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-248">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="fce43-249">다음 방법 중 하나를 사용하여 서비스의 자산 및 설정 파일을 유지 관리하고 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-249">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="fce43-250">ContentRootPath 또는 ContentRootFileProvider 사용</span><span class="sxs-lookup"><span data-stu-id="fce43-250">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="fce43-251">[IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) 또는 <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider>를 사용하여 앱의 리소스를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-251">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="fce43-252">콘텐츠 루트 경로를 앱 폴더로 설정</span><span class="sxs-lookup"><span data-stu-id="fce43-252">Set the content root path to the app's folder</span></span>

<span data-ttu-id="fce43-253"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*>는 서비스가 만들어질 때 `binPath` 인수에 제공된 것과 같은 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-253">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="fce43-254">`GetCurrentDirectory`를 호출하여 설정 파일의 경로를 만드는 대신, 앱의 콘텐츠 루트 경로를 사용하여 <xref:System.IO.Directory.SetCurrentDirectory*>를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-254">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's content root.</span></span>

<span data-ttu-id="fce43-255">`Program.Main`에서 서비스 실행 파일의 폴더 경로를 확인하고 이 경로를 사용하여 앱의 콘텐츠 루트를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-255">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

::: moniker-end

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="fce43-256">디스크의 적합한 위치에 서비스 파일 저장</span><span class="sxs-lookup"><span data-stu-id="fce43-256">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="fce43-257">파일이 포함된 폴더로 <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>를 사용하는 경우 <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>를 통해 절대 경로를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="fce43-257">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fce43-258">추가 자료</span><span class="sxs-lookup"><span data-stu-id="fce43-258">Additional resources</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="fce43-259">[Kestrel 엔드포인트 구성](xref:fundamentals/servers/kestrel#endpoint-configuration)(HTTPS 구성 및 SNI 지원 포함)</span><span class="sxs-lookup"><span data-stu-id="fce43-259">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="fce43-260">[Kestrel 엔드포인트 구성](xref:fundamentals/servers/kestrel#endpoint-configuration)(HTTPS 구성 및 SNI 지원 포함)</span><span class="sxs-lookup"><span data-stu-id="fce43-260">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
