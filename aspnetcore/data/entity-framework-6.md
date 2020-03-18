---
title: ASP.NET Core 및 Entity Framework 6 시작
author: rick-anderson
description: 이 문서에는 ASP.NET Core 애플리케이션에서 Entity Framework 6을 사용하는 방법을 보여 줍니다.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: data/entity-framework-6
ms.openlocfilehash: 85cf86dcb22ef94cfc87975abaab176e4f1227d3
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78645429"
---
# <a name="get-started-with-aspnet-core-and-entity-framework-6"></a><span data-ttu-id="7c030-103">ASP.NET Core 및 Entity Framework 6 시작</span><span class="sxs-lookup"><span data-stu-id="7c030-103">Get Started with ASP.NET Core and Entity Framework 6</span></span>

<span data-ttu-id="7c030-104">작성자: [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex) 및 [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="7c030-104">By [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="7c030-105">이 문서에는 ASP.NET Core 애플리케이션에서 Entity Framework 6을 사용하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7c030-105">This article shows how to use Entity Framework 6 in an ASP.NET Core application.</span></span>

## <a name="overview"></a><span data-ttu-id="7c030-106">개요</span><span class="sxs-lookup"><span data-stu-id="7c030-106">Overview</span></span>

<span data-ttu-id="7c030-107">Entity Framework 6을 사용하려면 Entity Framework 6에서 .NET Core를 지원하지 않으므로 .NET Framework에 대해 프로젝트를 컴파일해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7c030-107">To use Entity Framework 6, your project has to compile against .NET Framework, as Entity Framework 6 doesn't support .NET Core.</span></span> <span data-ttu-id="7c030-108">플랫폼 간 기능이 필요한 경우 [Entity Framework Core](/ef/)로 업그레이드해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7c030-108">If you need cross-platform features you will need to upgrade to [Entity Framework Core](/ef/).</span></span>

<span data-ttu-id="7c030-109">ASP.NET Core 애플리케이션에서 Entity Framework 6을 사용하는 권장되는 방법은 EF6 컨텍스트 및 모델 클래스를 .NET Framework를 대상으로 하는 클래스 라이브러리 프로젝트에 넣는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7c030-109">The recommended way to use Entity Framework 6 in an ASP.NET Core application is to put the EF6 context and model classes in a class library project that targets .NET Framework.</span></span> <span data-ttu-id="7c030-110">ASP.NET Core 프로젝트에서 클래스 라이브러리에 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7c030-110">Add a reference to the class library from the ASP.NET Core project.</span></span> <span data-ttu-id="7c030-111">샘플 [EF6 및 ASP.NET Core 프로젝트가 있는 Visual Studio 솔루션](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7c030-111">See the sample [Visual Studio solution with EF6 and ASP.NET Core projects](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/).</span></span>

<span data-ttu-id="7c030-112">.NET Core 프로젝트는 *Enable-Migrations*와 같은 EF6 명령이 요구하는 모든 기능을 지원하지 않으므로 ASP.NET Core 프로젝트에 EF6 컨텍스트를 배치할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7c030-112">You can't put an EF6 context in an ASP.NET Core project because .NET Core projects don't support all of the functionality that EF6 commands such as *Enable-Migrations* require.</span></span>

<span data-ttu-id="7c030-113">EF6 컨텍스트를 찾는 프로젝트 형식에 관계없이 EF6 명령줄 도구만 EF6 컨텍스트에서 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="7c030-113">Regardless of project type in which you locate your EF6 context, only EF6 command-line tools work with an EF6 context.</span></span> <span data-ttu-id="7c030-114">예를 들어 `Scaffold-DbContext`는 Entity Framework Core에서만 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7c030-114">For example, `Scaffold-DbContext` is only available in Entity Framework Core.</span></span> <span data-ttu-id="7c030-115">데이터베이스를 EF6 모델로 리버스 엔지니어링해야 하는 경우 [기존 데이터베이스에 대한 Code First](https://msdn.microsoft.com/jj200620)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7c030-115">If you need to do reverse engineering of a database into an EF6 model, see [Code First to an Existing Database](https://msdn.microsoft.com/jj200620).</span></span>

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a><span data-ttu-id="7c030-116">ASP.NET Core 프로젝트에서 전체 프레임워크 및 EF6 참조</span><span class="sxs-lookup"><span data-stu-id="7c030-116">Reference full framework and EF6 in the ASP.NET Core project</span></span>

<span data-ttu-id="7c030-117">ASP.NET Core 프로젝트는 .NET Framework를 대상으로 하고 EF6을 참조해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7c030-117">Your ASP.NET Core project needs to target .NET Framework and reference EF6.</span></span> <span data-ttu-id="7c030-118">예를 들어 ASP.NET Core 프로젝트의 *.csproj* 파일은 다음 예제와 유사합니다(파일의 관련 부분만 표시됨).</span><span class="sxs-lookup"><span data-stu-id="7c030-118">For example, the *.csproj* file of your ASP.NET Core project will look similar to the following example (only relevant parts of the file are shown).</span></span>

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]

<span data-ttu-id="7c030-119">새 프로젝트를 만들 때 **ASP.NET Core 웹 애플리케이션(.NET Framework)** 템플릿을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7c030-119">When creating a new project, use the **ASP.NET Core Web Application (.NET Framework)** template.</span></span>

## <a name="handle-connection-strings"></a><span data-ttu-id="7c030-120">연결 문자열 처리</span><span class="sxs-lookup"><span data-stu-id="7c030-120">Handle connection strings</span></span>

<span data-ttu-id="7c030-121">EF6 클래스 라이브러리 프로젝트에서 사용할 EF6 명령줄 도구에는 컨텍스트를 인스턴스화할 수 있도록 기본 생성자가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="7c030-121">The EF6 command-line tools that you'll use in the EF6 class library project require a default constructor so they can instantiate the context.</span></span> <span data-ttu-id="7c030-122">그러나 ASP.NET Core 프로젝트에서 사용할 연결 문자열을 지정하려는 경우 컨텍스트 생성자에 연결 문자열로 전달할 수 있는 매개 변수가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7c030-122">But you'll probably want to specify the connection string to use in the ASP.NET Core project, in which case your context constructor must have a parameter that lets you pass in the connection string.</span></span> <span data-ttu-id="7c030-123">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7c030-123">Here's an example.</span></span>

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]

<span data-ttu-id="7c030-124">EF6 컨텍스트에는 매개 변수가 없는 생성자가 없으므로 EF6 프로젝트는 [IDbContextFactory](https://msdn.microsoft.com/library/hh506876)의 구현을 제공해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7c030-124">Since your EF6 context doesn't have a parameterless constructor, your EF6 project has to provide an implementation of [IDbContextFactory](https://msdn.microsoft.com/library/hh506876).</span></span> <span data-ttu-id="7c030-125">EF6 명령줄 도구는 해당 구현을 찾아 사용하므로 컨텍스트를 인스턴스화할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7c030-125">The EF6 command-line tools will find and use that implementation so they can instantiate the context.</span></span> <span data-ttu-id="7c030-126">예를 들면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7c030-126">Here's an example.</span></span>

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]

<span data-ttu-id="7c030-127">이 샘플 코드에서 `IDbContextFactory` 구현은 하드 코딩된 연결 문자열을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="7c030-127">In this sample code, the `IDbContextFactory` implementation passes in a hard-coded connection string.</span></span> <span data-ttu-id="7c030-128">이것은 명령줄 도구에서 사용할 연결 문자열입니다.</span><span class="sxs-lookup"><span data-stu-id="7c030-128">This is the connection string that the command-line tools will use.</span></span> <span data-ttu-id="7c030-129">클래스 라이브러리가 호출 애플리케이션이 사용하는 것과 동일한 연결 문자열을 사용하도록 하는 전략을 구현하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="7c030-129">You'll want to implement a strategy to ensure that the class library uses the same connection string that the calling application uses.</span></span> <span data-ttu-id="7c030-130">예를 들어, 두 프로젝트의 환경 변수에서 값을 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7c030-130">For example, you could get the value from an environment variable in both projects.</span></span>

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a><span data-ttu-id="7c030-131">ASP.NET Core 프로젝트에서 종속성 주입 설정</span><span class="sxs-lookup"><span data-stu-id="7c030-131">Set up dependency injection in the ASP.NET Core project</span></span>

<span data-ttu-id="7c030-132">Core 프로젝트의 *Startup.cs* 파일에서 `ConfigureServices`에 종속성 주입(DI)에 대한 EF6 컨텍스트를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7c030-132">In the Core project's *Startup.cs* file, set up the EF6 context for dependency injection (DI) in `ConfigureServices`.</span></span> <span data-ttu-id="7c030-133">EF 컨텍스트 개체는 요청당 수명에 대해 범위가 지정되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7c030-133">EF context objects should be scoped for a per-request lifetime.</span></span>

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]

<span data-ttu-id="7c030-134">그러면 DI를 사용하여 컨트롤러에서 컨텍스트의 인스턴스를 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7c030-134">You can then get an instance of the context in your controllers by using DI.</span></span> <span data-ttu-id="7c030-135">코드는 EF Core 컨텍스트에 대해 작성한 코드와 유사합니다.</span><span class="sxs-lookup"><span data-stu-id="7c030-135">The code is similar to what you'd write for an EF Core context:</span></span>

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]

## <a name="sample-application"></a><span data-ttu-id="7c030-136">애플리케이션 예제</span><span class="sxs-lookup"><span data-stu-id="7c030-136">Sample application</span></span>

<span data-ttu-id="7c030-137">작업 중인 애플리케이션 예제는 이 문서와 함께 제공되는 [샘플 Visual Studio 솔루션](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7c030-137">For a working sample application, see the [sample Visual Studio solution](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) that accompanies this article.</span></span>

<span data-ttu-id="7c030-138">Visual Studio에서 다음 단계에 따라 이 샘플을 처음부터 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7c030-138">This sample can be created from scratch by the following steps in Visual Studio:</span></span>

* <span data-ttu-id="7c030-139">솔루션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7c030-139">Create a solution.</span></span>

* <span data-ttu-id="7c030-140"> > **새 프로젝트** > **웹** > **ASP.NET Core 웹 애플리케이션** **추가**</span><span class="sxs-lookup"><span data-stu-id="7c030-140">**Add** > **New Project** > **Web** > **ASP.NET Core Web Application**</span></span>
  * <span data-ttu-id="7c030-141">프로젝트 템플릿 선택 대화 상자의 드롭다운에서 API 및 .NET Framework 선택</span><span class="sxs-lookup"><span data-stu-id="7c030-141">In project template selection dialog, select API and .NET Framework in dropdown</span></span>

* <span data-ttu-id="7c030-142"> > **새 프로젝트** > **Windows 데스크톱** > **클래스 라이브러리(.NET Framework)** **추가**</span><span class="sxs-lookup"><span data-stu-id="7c030-142">**Add** > **New Project** > **Windows Desktop** > **Class Library (.NET Framework)**</span></span>

* <span data-ttu-id="7c030-143">두 프로젝트에 대한 **PMC(패키지 관리자 콘솔)** 에서 `Install-Package Entityframework` 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7c030-143">In **Package Manager Console** (PMC) for both projects, run the command `Install-Package Entityframework`.</span></span>

* <span data-ttu-id="7c030-144">클래스 라이브러리 프로젝트에서 데이터 모델 클래스 및 컨텍스트 클래스와 `IDbContextFactory`의 구현을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7c030-144">In the class library project, create data model classes and a context class, and an implementation of `IDbContextFactory`.</span></span>

* <span data-ttu-id="7c030-145">클래스 라이브러리 프로젝트에 대한 PMC에서 `Enable-Migrations` 및 `Add-Migration Initial` 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7c030-145">In PMC for the class library project, run the commands `Enable-Migrations` and `Add-Migration Initial`.</span></span> <span data-ttu-id="7c030-146">ASP.NET Core 프로젝트를 시작 프로젝트로 설정한 경우 `-StartupProjectName EF6`을 이러한 명령에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7c030-146">If you have set the ASP.NET Core project as the startup project, add `-StartupProjectName EF6` to these commands.</span></span>

* <span data-ttu-id="7c030-147">Core 프로젝트에서 클래스 라이브러리 프로젝트에 프로젝트 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7c030-147">In the Core project, add a project reference to the class library project.</span></span>

* <span data-ttu-id="7c030-148">Core 프로젝트의 *Startup.cs*에서 DI에 대한 컨텍스트를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="7c030-148">In the Core project, in *Startup.cs*, register the context for DI.</span></span>

* <span data-ttu-id="7c030-149">Core 프로젝트의 *appsettings.json*에서 연결 문자열을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7c030-149">In the Core project, in *appsettings.json*, add the connection string.</span></span>

* <span data-ttu-id="7c030-150">Core 프로젝트에서 데이터를 읽고 쓸 수 있는지 확인하는 컨트롤러 및 뷰를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7c030-150">In the Core project, add a controller and view(s) to verify that you can read and write data.</span></span> <span data-ttu-id="7c030-151">(ASP.NET Core MVC 스캐폴딩은 클래스 라이브러리에서 참조된 EF6 컨텍스트와 작동하지 않습니다.)</span><span class="sxs-lookup"><span data-stu-id="7c030-151">(Note that ASP.NET Core MVC scaffolding won't work with the EF6 context referenced from the class library.)</span></span>

## <a name="summary"></a><span data-ttu-id="7c030-152">요약</span><span class="sxs-lookup"><span data-stu-id="7c030-152">Summary</span></span>

<span data-ttu-id="7c030-153">이 문서에서는 ASP.NET Core 애플리케이션에서 Entity Framework 6을 사용하기 위한 기본 지침을 제공했습니다.</span><span class="sxs-lookup"><span data-stu-id="7c030-153">This article has provided basic guidance for using Entity Framework 6 in an ASP.NET Core application.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7c030-154">추가 자료</span><span class="sxs-lookup"><span data-stu-id="7c030-154">Additional resources</span></span>

* [<span data-ttu-id="7c030-155">Entity Framework - 코드 기반 구성</span><span class="sxs-lookup"><span data-stu-id="7c030-155">Entity Framework - Code-Based Configuration</span></span>](https://msdn.microsoft.com/data/jj680699.aspx)
