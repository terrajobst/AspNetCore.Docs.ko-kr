---
title: ASP.NET Core의 통합 테스트
author: rick-anderson
description: 통합 테스트를 사용하여 앱의 구성 요소가 데이터베이스, 파일 시스템 및 네트워크를 비롯한 인프라 수준에서 제대로 작동하는지 확인하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/06/2019
uid: test/integration-tests
ms.openlocfilehash: 414e47b9c5a1c843755bd79d313f503b554945db
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78649647"
---
# <a name="integration-tests-in-aspnet-core"></a><span data-ttu-id="5b05a-103">ASP.NET Core의 통합 테스트</span><span class="sxs-lookup"><span data-stu-id="5b05a-103">Integration tests in ASP.NET Core</span></span>

<span data-ttu-id="5b05a-104">작성자: [Javier Calvarro Nelson](https://github.com/javiercn), [Steve Smith](https://ardalis.com/) 및 [Jos van der Til](https://jvandertil.nl)</span><span class="sxs-lookup"><span data-stu-id="5b05a-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Steve Smith](https://ardalis.com/), and [Jos van der Til](https://jvandertil.nl)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5b05a-105">통합 테스트는 앱의 구성 요소가 데이터베이스, 파일 시스템 및 네트워크를 비롯하여 앱의 지원 인프라를 포함하는 수준에서 올바르게 작동하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-105">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="5b05a-106">ASP.NET Core는 테스트 웹 호스트 및 메모리 내 테스트 서버에서 단위 테스트 프레임워크를 사용하는 통합 테스트를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-106">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="5b05a-107">이 항목에서는 단위 테스트에 대한 기본적인 지식이 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-107">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="5b05a-108">테스트 개념을 잘 모르는 경우 .[.NET Core 및 .NET Standard의 단위 테스트](/dotnet/core/testing/) 항목 및 연결된 콘텐츠를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5b05a-108">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="5b05a-109">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5b05a-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="5b05a-110">샘플 앱은 Razor Pages 앱이며 Razor Pages를 기본적으로 이해하고 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-110">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="5b05a-111">Razor Pages에 익숙하지 않은 경우 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5b05a-111">If unfamiliar with Razor Pages, see the following topics:</span></span>

* [<span data-ttu-id="5b05a-112">Razor 페이지 소개</span><span class="sxs-lookup"><span data-stu-id="5b05a-112">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="5b05a-113">Razor 페이지 시작</span><span class="sxs-lookup"><span data-stu-id="5b05a-113">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="5b05a-114">Razor 페이지 단위 테스트</span><span class="sxs-lookup"><span data-stu-id="5b05a-114">Razor Pages unit tests</span></span>](xref:test/razor-pages-tests)

> [!NOTE]
> <span data-ttu-id="5b05a-115">SPA 테스트의 경우 브라우저를 자동화할 수 있는 [Selenium](https://www.seleniumhq.org/)과 같은 도구가 권장됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-115">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="5b05a-116">통합 테스트 소개</span><span class="sxs-lookup"><span data-stu-id="5b05a-116">Introduction to integration tests</span></span>

<span data-ttu-id="5b05a-117">통합 테스트는 [단위 테스트](/dotnet/core/testing/)보다 광범위한 수준에서 앱의 구성 요소를 평가합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-117">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="5b05a-118">단위 테스트는 개별 클래스 메서드와 같은 격리된 소프트웨어 구성 요소를 테스트하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-118">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="5b05a-119">통합 테스트는 두 개 이상의 앱 구성 요소가 함께 작동하여 요청을 완전히 처리하는 데 필요한 모든 구성 요소를 포함하여 예상되는 결과를 생성하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-119">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="5b05a-120">이러한 광범위한 테스트는 다음과 같은 구성 요소를 포함하여 앱의 인프라 및 전체 프레임워크를 테스트하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-120">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="5b05a-121">데이터베이스</span><span class="sxs-lookup"><span data-stu-id="5b05a-121">Database</span></span>
* <span data-ttu-id="5b05a-122">파일 시스템</span><span class="sxs-lookup"><span data-stu-id="5b05a-122">File system</span></span>
* <span data-ttu-id="5b05a-123">네트워크 어플라이언스</span><span class="sxs-lookup"><span data-stu-id="5b05a-123">Network appliances</span></span>
* <span data-ttu-id="5b05a-124">요청-응답 파이프라인</span><span class="sxs-lookup"><span data-stu-id="5b05a-124">Request-response pipeline</span></span>

<span data-ttu-id="5b05a-125">단위 테스트는 인프라 구성 요소 대신 *가짜* 또는 *모의 개체*로 알려져 있는 제작된 구성 요소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-125">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="5b05a-126">단위 테스트와 달리, 통합 테스트는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-126">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="5b05a-127">앱이 프로덕션 환경에서 사용하는 실제 구성 요소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-127">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="5b05a-128">더 많은 코드와 데이터 처리가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-128">Require more code and data processing.</span></span>
* <span data-ttu-id="5b05a-129">실행하는 데 시간이 더 오래 걸립니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-129">Take longer to run.</span></span>

<span data-ttu-id="5b05a-130">따라서 통합 테스트 사용은 가장 중요한 인프라 시나리오로 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-130">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="5b05a-131">단위 테스트 또는 통합 테스트를 사용하여 동작을 테스트할 수 있는 경우 단위 테스트를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-131">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="5b05a-132">데이터베이스 및 파일 시스템을 사용하는 데이터 및 파일 액세스의 가능한 모든 데이터 순열에 대해 통합 테스트를 작성하지는 마세요.</span><span class="sxs-lookup"><span data-stu-id="5b05a-132">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="5b05a-133">앱에서 데이터베이스 및 파일 시스템과 상호 작용하는 위치 수에 관계없이, 주요 읽기, 쓰기, 업데이트 및 삭제 통합 테스트 세트를 통해 일반적으로 데이터베이스 및 파일 시스템 구성 요소를 적절하게 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-133">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="5b05a-134">이러한 구성 요소와 상호 작용하는 메서드 논리의 루틴 테스트를 위해 단위 테스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-134">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="5b05a-135">단위 테스트에서 인프라 가짜/모의 시험을 사용하면 테스트 실행 속도가 더 빨라집니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-135">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="5b05a-136">통합 테스트를 논의할 때 테스트된 프로젝트를 주로 *테스트 중인 시스템* 또는 간단히 "SUT"라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-136">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="5b05a-137">*이 항목 전체에서 테스트된 ASP.NET Core 앱을 나타내기 위해 "SUT"를 사용합니다.*</span><span class="sxs-lookup"><span data-stu-id="5b05a-137">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="5b05a-138">ASP.NET Core의 통합 테스트</span><span class="sxs-lookup"><span data-stu-id="5b05a-138">ASP.NET Core integration tests</span></span>

<span data-ttu-id="5b05a-139">ASP.NET Core의 통합 테스트에는 다음이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-139">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="5b05a-140">테스트 프로젝트는 테스트를 포함하고 실행하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-140">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="5b05a-141">테스트 프로젝트에는 SUT에 대한 참조가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-141">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="5b05a-142">테스트 프로젝트는 SUT에 대한 테스트 웹 호스트를 만들고 테스트 서버 클라이언트를 사용하여 SUT에서 요청 및 응답을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-142">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="5b05a-143">Test Runner는 테스트를 실행하고 테스트 결과를 보고하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-143">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="5b05a-144">통합 테스트는 일반적인 *정렬*, *실행*및 *어설션* 테스트 단계를 포함하는 이벤트 시퀀스를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-144">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="5b05a-145">SUT의 웹 호스트가 구성되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-145">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="5b05a-146">앱에 요청을 제출하는 테스트 서버 클라이언트가 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-146">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="5b05a-147">*정렬* 테스트 단계가 실행됩니다. 테스트 앱은 요청을 준비합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-147">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="5b05a-148">*실행* 테스트 단계가 실행됩니다. 클라이언트는 요청을 제출하고 응답을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-148">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="5b05a-149">*어설션* 테스트 단계가 실행됩니다. *실제* 응답은 *예상* 응답에 따라 *성공* 또는 *실패*로 검증됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-149">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="5b05a-150">모든 테스트가 실행될 때까지 프로세스가 계속됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-150">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="5b05a-151">테스트 결과가 보고됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-151">The test results are reported.</span></span>

<span data-ttu-id="5b05a-152">일반적으로 테스트 웹 호스트는 테스트 실행을 위해 앱의 일반 웹 호스트와는 다르게 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-152">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="5b05a-153">예를 들어, 다른 데이터베이스 또는 다른 앱 설정을 테스트에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-153">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="5b05a-154">테스트 웹 호스트 및 메모리 내 테스트 서버([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver))와 같은 인프라 구성 요소는 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 패키지에서 제공 및 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-154">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="5b05a-155">이 패키지를 사용하면 테스트 생성 및 실행이 간소화됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-155">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="5b05a-156">`Microsoft.AspNetCore.Mvc.Testing` 패키지는 다음 작업을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-156">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="5b05a-157">SUT의 종속성 파일(*deps*)을 테스트 프로젝트의 *bin* 디렉터리로 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-157">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="5b05a-158">테스트를 실행하면 고정 파일 및 페이지/뷰를 찾을 수 있도록 [콘텐츠 루트](xref:fundamentals/index#content-root)를 SUT의 프로젝트 루트로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-158">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="5b05a-159">`TestServer`에서 SUT 부트스트랩을 간소화하기 위해 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) 클래스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-159">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="5b05a-160">[단위 테스트](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) 설명서에서는 테스트를 실행하는 방법에 대한 자세한 지침과 테스트 및 테스트 클래스의 이름을 설정하는 방법에 대한 권장 사항을 제공하고 테스트 프로젝트 및 Test Runner를 설정하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-160">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="5b05a-161">앱용 테스트 프로젝트를 만들 때 다른 프로젝트에 대한 통합 테스트에서 단위 테스트를 분리합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-161">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="5b05a-162">이렇게 하면 인프라 테스트 구성 요소가 실수로 단위 테스트에 포함되지 않도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-162">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="5b05a-163">또한 단위 및 통합 테스트를 분리하면 실행되는 테스트 세트를 제어할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-163">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="5b05a-164">Razor Pages 앱과 MVC 앱의 테스트 구성 간에는 차이는 거의 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-164">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="5b05a-165">유일한 차이점은 테스트의 이름을 지정하는 방법 뿐입니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-165">The only difference is in how the tests are named.</span></span> <span data-ttu-id="5b05a-166">Razor Pages 앱에서 페이지 엔드포인트의 테스트 이름은 일반적으로 페이지 모델 클래스의 이름을 따서 지정합니다(예: 인덱스 페이지에 대한 구성 요소 통합을 테스트하려는 경우 `IndexPageTests`).</span><span class="sxs-lookup"><span data-stu-id="5b05a-166">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="5b05a-167">MVC 앱에서 테스트는 일반적으로 컨트롤러 클래스로 구성되며 해당 이름은 테스트하는 컨트롤러의 이름을 따서 지정합니다(예: 홈 컨트롤러에 대한 구성 요소 통합을 테스트하려는 경우 `HomeControllerTests`).</span><span class="sxs-lookup"><span data-stu-id="5b05a-167">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="5b05a-168">테스트 앱 필수 조건</span><span class="sxs-lookup"><span data-stu-id="5b05a-168">Test app prerequisites</span></span>

<span data-ttu-id="5b05a-169">테스트 프로젝트는 다음을 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-169">The test project must:</span></span>

* <span data-ttu-id="5b05a-170">[Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 패키지를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-170">Reference the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span>
* <span data-ttu-id="5b05a-171">프로젝트 파일(`<Project Sdk="Microsoft.NET.Sdk.Web">`)에 웹 SDK를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-171">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span>

<span data-ttu-id="5b05a-172">이러한 필수 구성 요소는 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)에서 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-172">These prerequisites can be seen in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="5b05a-173">*tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* 파일을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-173">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="5b05a-174">샘플 앱은 [xUnit](https://xunit.github.io/) 테스트 프레임워크 및 [AngleSharp](https://anglesharp.github.io/) 파서 라이브러리를 사용하므로 샘플 앱은 다음을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-174">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="5b05a-175">xunit</span><span class="sxs-lookup"><span data-stu-id="5b05a-175">xunit</span></span>](https://www.nuget.org/packages/xunit)
* [<span data-ttu-id="5b05a-176">xunit.runner.visualstudio</span><span class="sxs-lookup"><span data-stu-id="5b05a-176">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [<span data-ttu-id="5b05a-177">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="5b05a-177">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp)

<span data-ttu-id="5b05a-178">Entity Framework Core도 테스트에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-178">Entity Framework Core is also used in the tests.</span></span> <span data-ttu-id="5b05a-179">앱 참조:</span><span class="sxs-lookup"><span data-stu-id="5b05a-179">The app references:</span></span>

* [<span data-ttu-id="5b05a-180">Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="5b05a-180">Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* [<span data-ttu-id="5b05a-181">Microsoft.AspNetCore.Identity.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="5b05a-181">Microsoft.AspNetCore.Identity.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)
* [<span data-ttu-id="5b05a-182">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="5b05a-182">Microsoft.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [<span data-ttu-id="5b05a-183">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="5b05a-183">Microsoft.EntityFrameworkCore.InMemory</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [<span data-ttu-id="5b05a-184">Microsoft.EntityFrameworkCore.Tools</span><span class="sxs-lookup"><span data-stu-id="5b05a-184">Microsoft.EntityFrameworkCore.Tools</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a><span data-ttu-id="5b05a-185">SUT 환경</span><span class="sxs-lookup"><span data-stu-id="5b05a-185">SUT environment</span></span>

<span data-ttu-id="5b05a-186">SUT의 [환경](xref:fundamentals/environments)이 설정되지 않은 경우 환경은 기본적으로 Development입니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-186">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="5b05a-187">기본 WebApplicationFactory를 사용하는 기본 테스트</span><span class="sxs-lookup"><span data-stu-id="5b05a-187">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="5b05a-188">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)는 통합 테스트에 대한 [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)를 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-188">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="5b05a-189">`TEntryPoint`는 SUT의 진입점 클래스로, 일반적으로 `Startup` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-189">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="5b05a-190">테스트 클래스는 *클래스 픽스쳐* 인터페이스([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture))를 구현하여 클래스가 테스트를 포함하고 클래스의 테스트에서 공유 개체 인스턴스를 제공함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-190">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

<span data-ttu-id="5b05a-191">다음 테스트 클래스 `BasicTests`에서는 `WebApplicationFactory`를 사용하여 SUT를 부트스트랩하고 [HttpClient](/dotnet/api/system.net.http.httpclient)를 테스트 메서드 `Get_EndpointsReturnSuccessAndCorrectContentType`에 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-191">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="5b05a-192">메서드는 응답 상태 코드가 성공(200-299 범위의 상태 코드)인지 `Content-Type` 헤더가 여러 앱 페이지에 대해 `text/html; charset=utf-8`인지를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-192">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="5b05a-193">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)는 자동으로 리디렉션을 따르고 쿠키를 처리하는 `HttpClient`의 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-193">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="5b05a-194">기본적으로 [GDPR 동의 정책](xref:security/gdpr)을 사용하도록 설정한 경우에는 필요하지 않은 쿠키가 요청에서 유지되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-194">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="5b05a-195">TempData 공급자가 사용하는 쿠키와 같이 필수가 아닌 쿠키를 유지하려면 테스트에서 필수로 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-195">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="5b05a-196">쿠키를 필수로 표시하는 방법에 대한 지침은 [필수 쿠키](xref:security/gdpr#essential-cookies)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5b05a-196">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="5b05a-197">WebApplicationFactory 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="5b05a-197">Customize WebApplicationFactory</span></span>

<span data-ttu-id="5b05a-198">웹 호스트 구성은 하나 이상의 사용자 지정 팩터리를 만들기 위해 `WebApplicationFactory`에서 상속하여 테스트 클래스와는 별개로 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-198">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="5b05a-199">`WebApplicationFactory`에서 상속하고 [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-199">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="5b05a-200">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)는 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices)를 사용하여 서비스 컬렉션을 구성할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-200">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="5b05a-201">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)의 데이터베이스 시드는 `InitializeDbForTests` 메서드에서 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-201">Database seeding in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="5b05a-202">이 메서드는 [통합 테스트 샘플: 테스트 앱 조직](#test-app-organization) 섹션에 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-202">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

   <span data-ttu-id="5b05a-203">SUT의 데이터베이스 컨텍스트는 `Startup.ConfigureServices` 메서드에 등록되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-203">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="5b05a-204">테스트 앱의 `builder.ConfigureServices` 콜백은 앱의 `Startup.ConfigureServices` 코드가 실행된 *후*에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-204">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="5b05a-205">실행 순서는 ASP.NET Core 3.0의 릴리스를 포함하는 [일반 호스트](xref:fundamentals/host/generic-host)의 호환성이 손상되는 변경에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-205">The execution order is a breaking change for the [Generic Host](xref:fundamentals/host/generic-host) with the release of ASP.NET Core 3.0.</span></span> <span data-ttu-id="5b05a-206">앱의 데이터베이스와는 다른 데이터베이스를 테스트에 사용하려면 앱의 데이터베이스 컨텍스트를 `builder.ConfigureServices`에서 바꾸어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-206">To use a different database for the tests than the app's database, the app's database context must be replaced in `builder.ConfigureServices`.</span></span>

   <span data-ttu-id="5b05a-207">샘플 앱은 데이터베이스 컨텍스트에 대한 서비스 설명자를 찾고, 이 설명자를 사용하여 서비스 등록을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-207">The sample app finds the service descriptor for the database context and uses the descriptor to remove the service registration.</span></span> <span data-ttu-id="5b05a-208">그런 다음, 팩터리는 테스트를 위해 메모리 내 데이터베이스를 사용하는 새 `ApplicationDbContext`를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-208">Next, the factory adds a new `ApplicationDbContext` that uses an in-memory database for the tests.</span></span>

   <span data-ttu-id="5b05a-209">메모리 내 데이터베이스가 아닌 다른 데이터베이스에 연결하려면 `UseInMemoryDatabase` 호출을 변경하여 컨텍스트를 다른 데이터베이스에 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-209">To connect to a different database than the in-memory database, change the `UseInMemoryDatabase` call to connect the context to a different database.</span></span> <span data-ttu-id="5b05a-210">SQL Server 테스트 데이터베이스를 사용하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-210">To use a SQL Server test database:</span></span>

   * <span data-ttu-id="5b05a-211">프로젝트 파일에서 [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet 패키지를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-211">Reference the [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet package in the project file.</span></span>
   * <span data-ttu-id="5b05a-212">데이터베이스에 대한 연결 문자열을 사용하여 `UseSqlServer`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-212">Call `UseSqlServer` with a connection string to the database.</span></span>

   ```csharp
   services.AddDbContext<ApplicationDbContext>((options, context) => 
   {
       context.UseSqlServer(
           Configuration.GetConnectionString("TestingDbConnectionString"));
   });
   ```

2. <span data-ttu-id="5b05a-213">테스트 클래스에서 사용자 지정 `CustomWebApplicationFactory`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-213">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="5b05a-214">다음 예제에서는 `IndexPageTests` 클래스에서 팩터리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-214">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="5b05a-215">샘플 앱 클라이언트는 `HttpClient`의 다음 리디렉션을 방지하도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-215">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="5b05a-216">[모의 인증](#mock-authentication) 섹션 뒷부분에 설명된 것처럼 이를 통해 테스트는 앱의 첫 번째 응답 결과를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-216">As explained later in the [Mock authentication](#mock-authentication) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="5b05a-217">첫 번째 응답은 `Location` 헤더를 사용하는 이러한 테스트 대부분에서 리디렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-217">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="5b05a-218">일반적인 테스트는 `HttpClient` 및 도우미 메서드를 사용하여 요청 및 응답을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-218">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="5b05a-219">SUT에 대한 POST 요청은 앱의 [데이터 보호 위조 방지 시스템](xref:security/data-protection/introduction)에서 자동으로 수행하는 위조 방지 검사를 충족해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-219">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="5b05a-220">테스트의 POST 요청을 정렬하기 위해 테스트 앱은 다음을 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-220">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="5b05a-221">페이지에 대한 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-221">Make a request for the page.</span></span>
1. <span data-ttu-id="5b05a-222">위조 방지 쿠키를 구문 분석하고 응답에서 유효성 검사 토큰을 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-222">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="5b05a-223">위조 방지 쿠키 및 요청 유효성 검사 토큰을 사용하여 POST 요청을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-223">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="5b05a-224">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)의 `SendAsync` 도우미 확장 메서드(*Helpers/HttpClientExtensions.cs*) 및 `GetDocumentAsync` 도우미 메서드(*Helpers/HtmlHelpers.cs*)는 [AngleSharp](https://anglesharp.github.io/) 파서를 사용하여 다음 메서드를 사용한 위조 방지 확인을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-224">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="5b05a-225">`GetDocumentAsync` &ndash; [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage)를 수신하고 `IHtmlDocument`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-225">`GetDocumentAsync` &ndash; Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="5b05a-226">`GetDocumentAsync`는 원본 `HttpResponseMessage`에 따라 *가상 응답*을 준비하는 팩터리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-226">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="5b05a-227">자세한 내용은 [AngleSharp 설명서](https://github.com/AngleSharp/AngleSharp#documentation)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5b05a-227">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="5b05a-228">`HttpClient`에 대한 `SendAsync` 확장 메서드는 [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage)를 작성하고 [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_)를 호출하여 요청을 SUT에 제출합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-228">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="5b05a-229">`SendAsync`에 대한 오버로드는 HTML 양식(`IHtmlFormElement`) 및 다음을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-229">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="5b05a-230">양식(`IHtmlElement`)의 제출 단추</span><span class="sxs-lookup"><span data-stu-id="5b05a-230">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="5b05a-231">양식 값 컬렉션(`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="5b05a-231">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="5b05a-232">제출 단추(`IHtmlElement`) 및 양식 값(`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="5b05a-232">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="5b05a-233">[AngleSharp](https://anglesharp.github.io/)는 이 항목 및 샘플 앱에서 데모용으로 사용되는 타사 구문 분석 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-233">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="5b05a-234">AngleSharp는 ASP.NET Core 앱의 통합 테스트에 대해 지원되지 않거나 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-234">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="5b05a-235">[HAP(Html Agility Pack)](https://html-agility-pack.net/)와 같은 기타 파서를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-235">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="5b05a-236">또 다른 방법은 위조 방지 시스템의 요청 확인 토큰과 위조 방지 쿠키를 직접 처리하는 코드를 작성하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-236">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="5b05a-237">WithWebHostBuilder를 사용하여 클라이언트 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="5b05a-237">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="5b05a-238">테스트 메서드 내에서 추가 구성이 필요한 경우 [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder)는 구성에서 추가로 사용자 지정되는 [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)를 사용하여 새 `WebApplicationFactory`를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-238">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="5b05a-239">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)의 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 테스트 메서드는 `WithWebHostBuilder`의 사용을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-239">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="5b05a-240">이 테스트는 SUT에서 양식 제출을 트리거하여 데이터베이스에서 레코드 삭제를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-240">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="5b05a-241">`IndexPageTests` 클래스의 다른 테스트는 데이터베이스의 모든 레코드를 삭제하는 작업을 수행하고 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 메서드 이전에 실행될 수 있기 때문에, 이 테스트 메서드에서 데이터베이스가 다시 시드되어 SUT가 삭제할 레코드가 존재하게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-241">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="5b05a-242">SUT에서 `messages` 양식의 첫 번째 삭제 단추를 선택하면 SUT에 대한 요청에서 시뮬레이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-242">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="5b05a-243">클라이언트 옵션</span><span class="sxs-lookup"><span data-stu-id="5b05a-243">Client options</span></span>

<span data-ttu-id="5b05a-244">다음 표에서는 `HttpClient` 인스턴스를 만들 때 사용할 수 있는 기본 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions)를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-244">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="5b05a-245">옵션</span><span class="sxs-lookup"><span data-stu-id="5b05a-245">Option</span></span> | <span data-ttu-id="5b05a-246">설명</span><span class="sxs-lookup"><span data-stu-id="5b05a-246">Description</span></span> | <span data-ttu-id="5b05a-247">기본값</span><span class="sxs-lookup"><span data-stu-id="5b05a-247">Default</span></span> |
| ------ | ----------- | ------- |
| [<span data-ttu-id="5b05a-248">AllowAutoRedirect</span><span class="sxs-lookup"><span data-stu-id="5b05a-248">AllowAutoRedirect</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | <span data-ttu-id="5b05a-249">`HttpClient` 인스턴스가 자동으로 리디렉션 응답을 따르는지 여부를 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-249">Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span> | `true` |
| [<span data-ttu-id="5b05a-250">BaseAddress</span><span class="sxs-lookup"><span data-stu-id="5b05a-250">BaseAddress</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | <span data-ttu-id="5b05a-251">`HttpClient` 인스턴스의 기준 주소를 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-251">Gets or sets the base address of `HttpClient` instances.</span></span> | `http://localhost` |
| [<span data-ttu-id="5b05a-252">HandleCookies</span><span class="sxs-lookup"><span data-stu-id="5b05a-252">HandleCookies</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | <span data-ttu-id="5b05a-253">`HttpClient` 인스턴스에서 쿠키를 처리할지 여부를 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-253">Gets or sets whether `HttpClient` instances should handle cookies.</span></span> | `true` |
| [<span data-ttu-id="5b05a-254">MaxAutomaticRedirections</span><span class="sxs-lookup"><span data-stu-id="5b05a-254">MaxAutomaticRedirections</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | <span data-ttu-id="5b05a-255">`HttpClient` 인스턴스에서 따라야 하는 리디렉션 응답의 최대 수를 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-255">Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> | <span data-ttu-id="5b05a-256">7</span><span class="sxs-lookup"><span data-stu-id="5b05a-256">7</span></span> |

<span data-ttu-id="5b05a-257">`WebApplicationFactoryClientOptions` 클래스를 만들고 [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) 메서드에 전달합니다(기본값은 코드 예제에 표시됨).</span><span class="sxs-lookup"><span data-stu-id="5b05a-257">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="5b05a-258">모의 서비스 주입</span><span class="sxs-lookup"><span data-stu-id="5b05a-258">Inject mock services</span></span>

<span data-ttu-id="5b05a-259">호스트 작성기에서 [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices)에 대한 호출을 사용하여 테스트에서 서비스를 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-259">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="5b05a-260">**모의 서비스를 주입하려면 SUT에 `Startup.ConfigureServices` 메서드를 포함하는 `Startup` 클래스가 있어야 합니다.**</span><span class="sxs-lookup"><span data-stu-id="5b05a-260">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="5b05a-261">샘플 SUT에는 따옴표를 반환하는 범위 지정 서비스가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-261">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="5b05a-262">인덱스 페이지를 요청하면 따옴표가 인덱스 페이지의 숨겨진 필드에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-262">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="5b05a-263">*Services/IQuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="5b05a-263">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="5b05a-264">*Services/QuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="5b05a-264">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="5b05a-265">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="5b05a-265">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="5b05a-266">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="5b05a-266">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="5b05a-267">*Pages/Index.cs*:</span><span class="sxs-lookup"><span data-stu-id="5b05a-267">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="5b05a-268">다음 태그는 SUT 앱이 실행될 때 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-268">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="5b05a-269">통합 테스트에서 서비스 및 따옴표 주입을 테스트하기 위해, 테스트를 통해 모의 서비스가 SUT에 주입됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-269">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="5b05a-270">모의 서비스는 앱의 `QuoteService`를 `TestQuoteService`이라는 테스트 앱에서 제공하는 서비스로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-270">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="5b05a-271">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="5b05a-271">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="5b05a-272">`ConfigureTestServices`가 호출되고 범위 지정 서비스가 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-272">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="5b05a-273">테스트 실행 중에 생성된 태그는 `TestQuoteService`에서 제공하는 따옴표 텍스트를 반영하므로 어설션은 성공합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-273">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a><span data-ttu-id="5b05a-274">모의 인증</span><span class="sxs-lookup"><span data-stu-id="5b05a-274">Mock authentication</span></span>

<span data-ttu-id="5b05a-275">`AuthTests` 클래스의 테스트는 보안 엔드포인트가 다음을 수행하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-275">Tests in the `AuthTests` class check that a secure endpoint:</span></span>

* <span data-ttu-id="5b05a-276">인증되지 않은 사용자를 앱의 로그인 페이지로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-276">Redirects an unauthenticated user to the app's Login page.</span></span>
* <span data-ttu-id="5b05a-277">인증된 사용자에 대한 콘텐츠를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-277">Returns content for an authenticated user.</span></span>

<span data-ttu-id="5b05a-278">SUT에서 `/SecurePage` 페이지는 [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) 규칙을 사용하여 페이지에 [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter)를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-278">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="5b05a-279">자세한 내용은 [Razor Pages 권한 부여 규칙](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5b05a-279">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="5b05a-280">`Get_SecurePageRedirectsAnUnauthenticatedUser` 테스트에서 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions)는 [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect)를 `false`로 설정하여 리디렉션을 허용하지 않도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-280">In the `Get_SecurePageRedirectsAnUnauthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

<span data-ttu-id="5b05a-281">클라이언트에서 리디렉션을 따르도록 허용하지 않으면 다음 검사를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-281">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="5b05a-282">SUT에서 반환된 상태 코드는 로그인 페이지로 리디렉션된 후의 최종 상태 코드([HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode))가 아니라 예상된 [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) 결과에 따라 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-282">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="5b05a-283">응답 헤더의 `Location` 헤더 값을 검토하여 `Location` 헤더가 없는 마지막 로그인 페이지 응답이 아니라 `http://localhost/Identity/Account/Login`으로 시작하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-283">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="5b05a-284">테스트 앱은 인증 및 권한 부여의 측면을 테스트하기 위해 [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices)에서 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>를 모의로 시험할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-284">The test app can mock an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) in order to test aspects of authentication and authorization.</span></span> <span data-ttu-id="5b05a-285">최소 시나리오는 [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*)를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-285">A minimal scenario returns an [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

<span data-ttu-id="5b05a-286">`TestAuthHandler`는 인증 체계가 `Test`로 설정된 사용자를 인증하도록 호출됩니다. 여기서는 `AddAuthentication`이 `ConfigureTestServices`에 등록되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-286">The `TestAuthHandler` is called to authenticate a user when the authentication scheme is set to `Test` where `AddAuthentication` is registered for `ConfigureTestServices`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

<span data-ttu-id="5b05a-287">`WebApplicationFactoryClientOptions`에 대한 자세한 내용은 [클라이언트 옵션](#client-options) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5b05a-287">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="5b05a-288">환경 설정</span><span class="sxs-lookup"><span data-stu-id="5b05a-288">Set the environment</span></span>

<span data-ttu-id="5b05a-289">기본적으로 SUT의 호스트 및 앱 환경은 개발 환경을 사용하도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-289">By default, the SUT's host and app environment is configured to use the Development environment.</span></span> <span data-ttu-id="5b05a-290">SUT의 환경을 재정의하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-290">To override the SUT's environment:</span></span>

* <span data-ttu-id="5b05a-291">`ASPNETCORE_ENVIRONMENT` 환경 변수(예: `Staging`, `Production` 또는 `Testing`과 같은 기타 사용자 지정 값)를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-291">Set the `ASPNETCORE_ENVIRONMENT` environment variable (for example, `Staging`, `Production`, or other custom value, such as `Testing`).</span></span>
* <span data-ttu-id="5b05a-292">`ASPNETCORE`를 접두사로 사용하는 환경 변수를 읽도록 테스트 앱의 `CreateHostBuilder`를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-292">Override `CreateHostBuilder` in the test app to read environment variables prefixed with `ASPNETCORE`.</span></span>

```csharp
protected override IHostBuilder CreateHostBuilder() => 
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="5b05a-293">테스트 인프라가 앱 콘텐츠 루트 경로를 유추하는 방법</span><span class="sxs-lookup"><span data-stu-id="5b05a-293">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="5b05a-294">`WebApplicationFactory` 생성자는 `TEntryPoint` 어셈블리 `System.Reflection.Assembly.FullName`와 같은 키가 있는 통합 테스트를 포함하는 어셈블리에서 [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute)를 검색하여 앱 [콘텐츠 루트](xref:fundamentals/index#content-root) 경로를 유추합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-294">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="5b05a-295">올바른 키를 포함하는 특성을 찾을 수 없는 경우 `WebApplicationFactory`는 솔루션 파일( *.sln*) 검색으로 대체되고 `TEntryPoint` 어셈블리 이름을 솔루션 디렉터리에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-295">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="5b05a-296">앱 루트 디렉터리(콘텐츠 루트 경로)는 뷰와 콘텐츠 파일을 검색하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-296">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="5b05a-297">섀도 복사 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="5b05a-297">Disable shadow copying</span></span>

<span data-ttu-id="5b05a-298">섀도 복사를 수행하면 테스트가 출력 디렉터리와는 다른 디렉터리에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-298">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="5b05a-299">테스트가 제대로 작동하려면 섀도 복사를 사용하지 않도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-299">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="5b05a-300">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)은 xUnit을 사용하고 올바른 구성 설정에 *xunit.runner.json* 파일을 포함하여 xUnit에 대한 섀도 복사를 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-300">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="5b05a-301">자세한 내용은 [JSON으로 xUnit 구성](https://xunit.github.io/docs/configuring-with-json.html)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5b05a-301">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="5b05a-302">다음 콘텐츠를 사용하여 테스트 프로젝트의 루트에 *xunit.runner.json* 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-302">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a><span data-ttu-id="5b05a-303">개체 삭제</span><span class="sxs-lookup"><span data-stu-id="5b05a-303">Disposal of objects</span></span>

<span data-ttu-id="5b05a-304">`IClassFixture` 구현의 테스트를 실행한 후 [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) 및 [HttpClient](/dotnet/api/system.net.http.httpclient)는 xUnit이 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)을 삭제하면 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-304">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="5b05a-305">개발자가 인스턴스화한 개체를 삭제해야 하는 경우 `IClassFixture` 구현에서 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-305">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="5b05a-306">자세한 내용은 [Dispose 메서드 구현](/dotnet/standard/garbage-collection/implementing-dispose)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5b05a-306">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="5b05a-307">통합 테스트 샘플</span><span class="sxs-lookup"><span data-stu-id="5b05a-307">Integration tests sample</span></span>

<span data-ttu-id="5b05a-308">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)은 다음 두 앱으로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-308">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="5b05a-309">앱</span><span class="sxs-lookup"><span data-stu-id="5b05a-309">App</span></span> | <span data-ttu-id="5b05a-310">프로젝트 디렉터리</span><span class="sxs-lookup"><span data-stu-id="5b05a-310">Project directory</span></span> | <span data-ttu-id="5b05a-311">설명</span><span class="sxs-lookup"><span data-stu-id="5b05a-311">Description</span></span> |
| --- | ----------------- | ----------- |
| <span data-ttu-id="5b05a-312">메시지 앱(SUT)</span><span class="sxs-lookup"><span data-stu-id="5b05a-312">Message app (the SUT)</span></span> | <span data-ttu-id="5b05a-313">*src/RazorPagesProject*</span><span class="sxs-lookup"><span data-stu-id="5b05a-313">*src/RazorPagesProject*</span></span> | <span data-ttu-id="5b05a-314">사용자가 메시지를 추가, 메시지 1개 또는 모든 메시지를 삭제, 메시지를 분석할 수 있도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-314">Allows a user to add, delete one, delete all, and analyze messages.</span></span> |
| <span data-ttu-id="5b05a-315">앱 테스트</span><span class="sxs-lookup"><span data-stu-id="5b05a-315">Test app</span></span> | <span data-ttu-id="5b05a-316">*tests/RazorPagesProject.Tests*</span><span class="sxs-lookup"><span data-stu-id="5b05a-316">*tests/RazorPagesProject.Tests*</span></span> | <span data-ttu-id="5b05a-317">SUT에 대해 통합 테스트를 수행하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-317">Used to integration test the SUT.</span></span> |

<span data-ttu-id="5b05a-318">[Visual Studio](https://visualstudio.microsoft.com)와 같은 IDE의 기본 제공 테스트 기능을 사용하여 테스트를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-318">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="5b05a-319">[Visual Studio Code](https://code.visualstudio.com/) 또는 명령줄을 사용하는 경우 *tests/RazorPagesProject.Tests* 디렉터리의 명령 프롬프트에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-319">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```console
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="5b05a-320">메시지 앱(SUT) 조직</span><span class="sxs-lookup"><span data-stu-id="5b05a-320">Message app (SUT) organization</span></span>

<span data-ttu-id="5b05a-321">SUT는 다음과 같은 특징을 가진 Razor Pages 메시지 시스템입니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-321">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="5b05a-322">앱의 인덱스 페이지(*Pages/Index.cshtml* 및 *Pages/Index.cshtml.cs*)는 메시지의 추가, 삭제 및 분석을 제어하기 위한 UI 및 페이지 모델 메서드를 제공합니다(메시지당 평균 단어 수).</span><span class="sxs-lookup"><span data-stu-id="5b05a-322">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="5b05a-323">메시지는 `Id`(키) 및 `Text`(메시지)의 두 가지 속성을 사용하여 `Message` 클래스(*Data/Message.cs*)에서 설명됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-323">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="5b05a-324">`Text` 속성은 필수이며 200자로 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-324">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="5b05a-325">메시지는 [Entity Framework의 메모리 내 데이터베이스](/ef/core/providers/in-memory/)를 사용하여 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-325">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="5b05a-326">앱은 데이터베이스 컨텍스트 클래스 `AppDbContext`(*Data/AppDbContext*)에 DAL(데이터 액세스 계층)을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-326">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="5b05a-327">앱 시작 시 데이터베이스가 비어 있는 경우 메시지 저장소는 세 개의 메시지로 초기화됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-327">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="5b05a-328">앱에는 인증된 사용자만 액세스할 수 있는 `/SecurePage`가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-328">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="5b05a-329">EF 항목 [InMemory로 테스트](/ef/core/miscellaneous/testing/in-memory)에서는 MSTest를 사용하여 테스트에 메모리 내 데이터베이스를 사용하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-329">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="5b05a-330">이 항목에서는 [xUnit](https://xunit.github.io/) 테스트 프레임워크를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-330">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="5b05a-331">여러 테스트 프레임워크의 테스트 개념 및 테스트 구현은 비슷하지만 동일하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-331">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="5b05a-332">앱은 리포지토리 패턴을 사용하지 않고 [UoW(작업 단위) 패턴](https://martinfowler.com/eaaCatalog/unitOfWork.html)의 효과적인 예가 아니지만 Razor Pages는 이러한 개발 패턴을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-332">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="5b05a-333">자세한 내용은 [인프라 지속성 계층 디자인](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) 및 [테스트 컨트롤러 논리](/aspnet/core/mvc/controllers/testing)(샘플에서 리포지토리 패턴 구현)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5b05a-333">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="5b05a-334">테스트 앱 구성</span><span class="sxs-lookup"><span data-stu-id="5b05a-334">Test app organization</span></span>

<span data-ttu-id="5b05a-335">테스트 앱은 *tests/RazorPagesProject.Tests* 디렉터리 내에 있는 콘솔 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-335">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="5b05a-336">테스트 앱 디렉터리</span><span class="sxs-lookup"><span data-stu-id="5b05a-336">Test app directory</span></span> | <span data-ttu-id="5b05a-337">설명</span><span class="sxs-lookup"><span data-stu-id="5b05a-337">Description</span></span> |
| ------------------ | ----------- |
| <span data-ttu-id="5b05a-338">*AuthTests*</span><span class="sxs-lookup"><span data-stu-id="5b05a-338">*AuthTests*</span></span> | <span data-ttu-id="5b05a-339">다음에 대한 테스트 메서드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-339">Contains test methods for:</span></span><ul><li><span data-ttu-id="5b05a-340">인증되지 않은 사용자가 보안 페이지에 액세스</span><span class="sxs-lookup"><span data-stu-id="5b05a-340">Accessing a secure page by an unauthenticated user.</span></span></li><li><span data-ttu-id="5b05a-341">모의 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>를 사용하여 인증된 사용자가 보안 페이지에 액세스</span><span class="sxs-lookup"><span data-stu-id="5b05a-341">Accessing a secure page by an authenticated user with a mock <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span></li><li><span data-ttu-id="5b05a-342">GitHub 사용자 프로필을 가져오고 프로필의 사용자 로그인 확인</span><span class="sxs-lookup"><span data-stu-id="5b05a-342">Obtaining a GitHub user profile and checking the profile's user login.</span></span></li></ul> |
| <span data-ttu-id="5b05a-343">*BasicTests*</span><span class="sxs-lookup"><span data-stu-id="5b05a-343">*BasicTests*</span></span> | <span data-ttu-id="5b05a-344">라우팅 및 콘텐츠 형식에 대한 테스트 메서드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-344">Contains a test method for routing and content type.</span></span> |
| <span data-ttu-id="5b05a-345">*IntegrationTests*</span><span class="sxs-lookup"><span data-stu-id="5b05a-345">*IntegrationTests*</span></span> | <span data-ttu-id="5b05a-346">사용자 지정 `WebApplicationFactory` 클래스를 사용하여 인덱스 페이지에 대한 통합 테스트를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-346">Contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> |
| <span data-ttu-id="5b05a-347">*Helpers/Utilities*</span><span class="sxs-lookup"><span data-stu-id="5b05a-347">*Helpers/Utilities*</span></span> | <ul><li><span data-ttu-id="5b05a-348">*Utilities.cs*에는 테스트 데이터로 데이터베이스를 시드하는 데 사용되는 `InitializeDbForTests` 메서드가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-348">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="5b05a-349">*HtmlHelpers.cs*는 테스트 메서드에서 사용할 AngleSharp `IHtmlDocument`를 반환하기 위한 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-349">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="5b05a-350">*HttpClientExtensions.cs*는 `SendAsync`에 대한 오버로드를 제공하여 SUT에 요청을 제출합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-350">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="5b05a-351">테스트 프레임워크는 [xUnit](https://xunit.github.io/)입니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-351">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="5b05a-352">[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)를 포함하는 [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost)를 사용하여 통합 테스트가 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-352">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="5b05a-353">[Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 패키지를 사용하여 테스트 호스트와 테스트 서버를 구성하기 때문에 `TestHost` 및 `TestServer` 패키지에는 테스트 앱의 프로젝트 파일 또는 테스트 앱의 개발자 구성에서 직접 패키지 참조가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-353">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="5b05a-354">**테스트를 위해 데이터베이스 시드**</span><span class="sxs-lookup"><span data-stu-id="5b05a-354">**Seeding the database for testing**</span></span>

<span data-ttu-id="5b05a-355">통합 테스트에서는 일반적으로 테스트 실행 전에 데이터베이스에 작은 데이터 세트가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-355">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="5b05a-356">예를 들어, 삭제 테스트는 데이터베이스 레코드 삭제를 요구하므로 삭제 요청이 성공하려면 데이터베이스에 적어도 하나 이상의 레코드가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-356">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="5b05a-357">샘플 앱은 테스트가 실행될 때 사용할 수 있는 *Utilities.cs*의 세 가지 메시지로 데이터베이스를 시드합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-357">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

<span data-ttu-id="5b05a-358">SUT의 데이터베이스 컨텍스트는 `Startup.ConfigureServices` 메서드에 등록되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-358">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="5b05a-359">테스트 앱의 `builder.ConfigureServices` 콜백은 앱의 `Startup.ConfigureServices` 코드가 실행된 *후*에 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-359">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="5b05a-360">다른 데이터베이스를 테스트에 사용하려면 앱의 데이터베이스 컨텍스트를 `builder.ConfigureServices`에서 바꾸어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-360">To use a different database for the tests, the app's database context must be replaced in `builder.ConfigureServices`.</span></span> <span data-ttu-id="5b05a-361">자세한 내용은 [WebApplicationFactory 사용자 지정](#customize-webapplicationfactory) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5b05a-361">For more information, see the [Customize WebApplicationFactory](#customize-webapplicationfactory) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5b05a-362">통합 테스트는 앱의 구성 요소가 데이터베이스, 파일 시스템 및 네트워크를 비롯하여 앱의 지원 인프라를 포함하는 수준에서 올바르게 작동하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-362">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="5b05a-363">ASP.NET Core는 테스트 웹 호스트 및 메모리 내 테스트 서버에서 단위 테스트 프레임워크를 사용하는 통합 테스트를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-363">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="5b05a-364">이 항목에서는 단위 테스트에 대한 기본적인 지식이 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-364">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="5b05a-365">테스트 개념을 잘 모르는 경우 .[.NET Core 및 .NET Standard의 단위 테스트](/dotnet/core/testing/) 항목 및 연결된 콘텐츠를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5b05a-365">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="5b05a-366">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5b05a-366">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="5b05a-367">샘플 앱은 Razor Pages 앱이며 Razor Pages를 기본적으로 이해하고 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-367">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="5b05a-368">Razor Pages에 익숙하지 않은 경우 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5b05a-368">If unfamiliar with Razor Pages, see the following topics:</span></span>

* [<span data-ttu-id="5b05a-369">Razor 페이지 소개</span><span class="sxs-lookup"><span data-stu-id="5b05a-369">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="5b05a-370">Razor 페이지 시작</span><span class="sxs-lookup"><span data-stu-id="5b05a-370">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="5b05a-371">Razor 페이지 단위 테스트</span><span class="sxs-lookup"><span data-stu-id="5b05a-371">Razor Pages unit tests</span></span>](xref:test/razor-pages-tests)

> [!NOTE]
> <span data-ttu-id="5b05a-372">SPA 테스트의 경우 브라우저를 자동화할 수 있는 [Selenium](https://www.seleniumhq.org/)과 같은 도구가 권장됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-372">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="5b05a-373">통합 테스트 소개</span><span class="sxs-lookup"><span data-stu-id="5b05a-373">Introduction to integration tests</span></span>

<span data-ttu-id="5b05a-374">통합 테스트는 [단위 테스트](/dotnet/core/testing/)보다 광범위한 수준에서 앱의 구성 요소를 평가합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-374">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="5b05a-375">단위 테스트는 개별 클래스 메서드와 같은 격리된 소프트웨어 구성 요소를 테스트하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-375">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="5b05a-376">통합 테스트는 두 개 이상의 앱 구성 요소가 함께 작동하여 요청을 완전히 처리하는 데 필요한 모든 구성 요소를 포함하여 예상되는 결과를 생성하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-376">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="5b05a-377">이러한 광범위한 테스트는 다음과 같은 구성 요소를 포함하여 앱의 인프라 및 전체 프레임워크를 테스트하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-377">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="5b05a-378">데이터베이스</span><span class="sxs-lookup"><span data-stu-id="5b05a-378">Database</span></span>
* <span data-ttu-id="5b05a-379">파일 시스템</span><span class="sxs-lookup"><span data-stu-id="5b05a-379">File system</span></span>
* <span data-ttu-id="5b05a-380">네트워크 어플라이언스</span><span class="sxs-lookup"><span data-stu-id="5b05a-380">Network appliances</span></span>
* <span data-ttu-id="5b05a-381">요청-응답 파이프라인</span><span class="sxs-lookup"><span data-stu-id="5b05a-381">Request-response pipeline</span></span>

<span data-ttu-id="5b05a-382">단위 테스트는 인프라 구성 요소 대신 *가짜* 또는 *모의 개체*로 알려져 있는 제작된 구성 요소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-382">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="5b05a-383">단위 테스트와 달리, 통합 테스트는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-383">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="5b05a-384">앱이 프로덕션 환경에서 사용하는 실제 구성 요소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-384">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="5b05a-385">더 많은 코드와 데이터 처리가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-385">Require more code and data processing.</span></span>
* <span data-ttu-id="5b05a-386">실행하는 데 시간이 더 오래 걸립니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-386">Take longer to run.</span></span>

<span data-ttu-id="5b05a-387">따라서 통합 테스트 사용은 가장 중요한 인프라 시나리오로 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-387">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="5b05a-388">단위 테스트 또는 통합 테스트를 사용하여 동작을 테스트할 수 있는 경우 단위 테스트를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-388">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="5b05a-389">데이터베이스 및 파일 시스템을 사용하는 데이터 및 파일 액세스의 가능한 모든 데이터 순열에 대해 통합 테스트를 작성하지는 마세요.</span><span class="sxs-lookup"><span data-stu-id="5b05a-389">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="5b05a-390">앱에서 데이터베이스 및 파일 시스템과 상호 작용하는 위치 수에 관계없이, 주요 읽기, 쓰기, 업데이트 및 삭제 통합 테스트 세트를 통해 일반적으로 데이터베이스 및 파일 시스템 구성 요소를 적절하게 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-390">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="5b05a-391">이러한 구성 요소와 상호 작용하는 메서드 논리의 루틴 테스트를 위해 단위 테스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-391">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="5b05a-392">단위 테스트에서 인프라 가짜/모의 시험을 사용하면 테스트 실행 속도가 더 빨라집니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-392">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="5b05a-393">통합 테스트를 논의할 때 테스트된 프로젝트를 주로 *테스트 중인 시스템* 또는 간단히 "SUT"라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-393">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="5b05a-394">*이 항목 전체에서 테스트된 ASP.NET Core 앱을 나타내기 위해 "SUT"를 사용합니다.*</span><span class="sxs-lookup"><span data-stu-id="5b05a-394">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="5b05a-395">ASP.NET Core의 통합 테스트</span><span class="sxs-lookup"><span data-stu-id="5b05a-395">ASP.NET Core integration tests</span></span>

<span data-ttu-id="5b05a-396">ASP.NET Core의 통합 테스트에는 다음이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-396">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="5b05a-397">테스트 프로젝트는 테스트를 포함하고 실행하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-397">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="5b05a-398">테스트 프로젝트에는 SUT에 대한 참조가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-398">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="5b05a-399">테스트 프로젝트는 SUT에 대한 테스트 웹 호스트를 만들고 테스트 서버 클라이언트를 사용하여 SUT에서 요청 및 응답을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-399">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="5b05a-400">Test Runner는 테스트를 실행하고 테스트 결과를 보고하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-400">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="5b05a-401">통합 테스트는 일반적인 *정렬*, *실행*및 *어설션* 테스트 단계를 포함하는 이벤트 시퀀스를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-401">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="5b05a-402">SUT의 웹 호스트가 구성되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-402">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="5b05a-403">앱에 요청을 제출하는 테스트 서버 클라이언트가 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-403">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="5b05a-404">*정렬* 테스트 단계가 실행됩니다. 테스트 앱은 요청을 준비합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-404">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="5b05a-405">*실행* 테스트 단계가 실행됩니다. 클라이언트는 요청을 제출하고 응답을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-405">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="5b05a-406">*어설션* 테스트 단계가 실행됩니다. *실제* 응답은 *예상* 응답에 따라 *성공* 또는 *실패*로 검증됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-406">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="5b05a-407">모든 테스트가 실행될 때까지 프로세스가 계속됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-407">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="5b05a-408">테스트 결과가 보고됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-408">The test results are reported.</span></span>

<span data-ttu-id="5b05a-409">일반적으로 테스트 웹 호스트는 테스트 실행을 위해 앱의 일반 웹 호스트와는 다르게 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-409">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="5b05a-410">예를 들어, 다른 데이터베이스 또는 다른 앱 설정을 테스트에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-410">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="5b05a-411">테스트 웹 호스트 및 메모리 내 테스트 서버([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver))와 같은 인프라 구성 요소는 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 패키지에서 제공 및 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-411">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="5b05a-412">이 패키지를 사용하면 테스트 생성 및 실행이 간소화됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-412">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="5b05a-413">`Microsoft.AspNetCore.Mvc.Testing` 패키지는 다음 작업을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-413">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="5b05a-414">SUT의 종속성 파일(*deps*)을 테스트 프로젝트의 *bin* 디렉터리로 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-414">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="5b05a-415">테스트를 실행하면 고정 파일 및 페이지/뷰를 찾을 수 있도록 [콘텐츠 루트](xref:fundamentals/index#content-root)를 SUT의 프로젝트 루트로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-415">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="5b05a-416">`TestServer`에서 SUT 부트스트랩을 간소화하기 위해 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) 클래스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-416">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="5b05a-417">[단위 테스트](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) 설명서에서는 테스트를 실행하는 방법에 대한 자세한 지침과 테스트 및 테스트 클래스의 이름을 설정하는 방법에 대한 권장 사항을 제공하고 테스트 프로젝트 및 Test Runner를 설정하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-417">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="5b05a-418">앱용 테스트 프로젝트를 만들 때 다른 프로젝트에 대한 통합 테스트에서 단위 테스트를 분리합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-418">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="5b05a-419">이렇게 하면 인프라 테스트 구성 요소가 실수로 단위 테스트에 포함되지 않도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-419">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="5b05a-420">또한 단위 및 통합 테스트를 분리하면 실행되는 테스트 세트를 제어할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-420">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="5b05a-421">Razor Pages 앱과 MVC 앱의 테스트 구성 간에는 차이는 거의 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-421">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="5b05a-422">유일한 차이점은 테스트의 이름을 지정하는 방법 뿐입니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-422">The only difference is in how the tests are named.</span></span> <span data-ttu-id="5b05a-423">Razor Pages 앱에서 페이지 엔드포인트의 테스트 이름은 일반적으로 페이지 모델 클래스의 이름을 따서 지정합니다(예: 인덱스 페이지에 대한 구성 요소 통합을 테스트하려는 경우 `IndexPageTests`).</span><span class="sxs-lookup"><span data-stu-id="5b05a-423">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="5b05a-424">MVC 앱에서 테스트는 일반적으로 컨트롤러 클래스로 구성되며 해당 이름은 테스트하는 컨트롤러의 이름을 따서 지정합니다(예: 홈 컨트롤러에 대한 구성 요소 통합을 테스트하려는 경우 `HomeControllerTests`).</span><span class="sxs-lookup"><span data-stu-id="5b05a-424">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="5b05a-425">테스트 앱 필수 조건</span><span class="sxs-lookup"><span data-stu-id="5b05a-425">Test app prerequisites</span></span>

<span data-ttu-id="5b05a-426">테스트 프로젝트는 다음을 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-426">The test project must:</span></span>

* <span data-ttu-id="5b05a-427">다음 패키지를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-427">Reference the following packages:</span></span>
  * [<span data-ttu-id="5b05a-428">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="5b05a-428">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [<span data-ttu-id="5b05a-429">Microsoft.AspNetCore.Mvc.Testing</span><span class="sxs-lookup"><span data-stu-id="5b05a-429">Microsoft.AspNetCore.Mvc.Testing</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* <span data-ttu-id="5b05a-430">프로젝트 파일(`<Project Sdk="Microsoft.NET.Sdk.Web">`)에 웹 SDK를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-430">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span> <span data-ttu-id="5b05a-431">웹 SDK는 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)를 참조할 때 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-431">The Web SDK is required when referencing the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="5b05a-432">이러한 필수 구성 요소는 [샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)에서 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-432">These prerequisites can be seen in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="5b05a-433">*tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* 파일을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-433">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="5b05a-434">샘플 앱은 [xUnit](https://xunit.github.io/) 테스트 프레임워크 및 [AngleSharp](https://anglesharp.github.io/) 파서 라이브러리를 사용하므로 샘플 앱은 다음을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-434">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="5b05a-435">xunit</span><span class="sxs-lookup"><span data-stu-id="5b05a-435">xunit</span></span>](https://www.nuget.org/packages/xunit/)
* [<span data-ttu-id="5b05a-436">xunit.runner.visualstudio</span><span class="sxs-lookup"><span data-stu-id="5b05a-436">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [<span data-ttu-id="5b05a-437">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="5b05a-437">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a><span data-ttu-id="5b05a-438">SUT 환경</span><span class="sxs-lookup"><span data-stu-id="5b05a-438">SUT environment</span></span>

<span data-ttu-id="5b05a-439">SUT의 [환경](xref:fundamentals/environments)이 설정되지 않은 경우 환경은 기본적으로 Development입니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-439">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="5b05a-440">기본 WebApplicationFactory를 사용하는 기본 테스트</span><span class="sxs-lookup"><span data-stu-id="5b05a-440">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="5b05a-441">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)는 통합 테스트에 대한 [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)를 만드는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-441">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="5b05a-442">`TEntryPoint`는 SUT의 진입점 클래스로, 일반적으로 `Startup` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-442">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="5b05a-443">테스트 클래스는 *클래스 픽스쳐* 인터페이스([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture))를 구현하여 클래스가 테스트를 포함하고 클래스의 테스트에서 공유 개체 인스턴스를 제공함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-443">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

<span data-ttu-id="5b05a-444">다음 테스트 클래스 `BasicTests`에서는 `WebApplicationFactory`를 사용하여 SUT를 부트스트랩하고 [HttpClient](/dotnet/api/system.net.http.httpclient)를 테스트 메서드 `Get_EndpointsReturnSuccessAndCorrectContentType`에 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-444">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="5b05a-445">메서드는 응답 상태 코드가 성공(200-299 범위의 상태 코드)인지 `Content-Type` 헤더가 여러 앱 페이지에 대해 `text/html; charset=utf-8`인지를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-445">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="5b05a-446">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)는 자동으로 리디렉션을 따르고 쿠키를 처리하는 `HttpClient`의 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-446">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="5b05a-447">기본적으로 [GDPR 동의 정책](xref:security/gdpr)을 사용하도록 설정한 경우에는 필요하지 않은 쿠키가 요청에서 유지되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-447">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="5b05a-448">TempData 공급자가 사용하는 쿠키와 같이 필수가 아닌 쿠키를 유지하려면 테스트에서 필수로 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-448">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="5b05a-449">쿠키를 필수로 표시하는 방법에 대한 지침은 [필수 쿠키](xref:security/gdpr#essential-cookies)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5b05a-449">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="5b05a-450">WebApplicationFactory 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="5b05a-450">Customize WebApplicationFactory</span></span>

<span data-ttu-id="5b05a-451">웹 호스트 구성은 하나 이상의 사용자 지정 팩터리를 만들기 위해 `WebApplicationFactory`에서 상속하여 테스트 클래스와는 별개로 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-451">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="5b05a-452">`WebApplicationFactory`에서 상속하고 [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-452">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="5b05a-453">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)는 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices)를 사용하여 서비스 컬렉션을 구성할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-453">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="5b05a-454">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)의 데이터베이스 시드는 `InitializeDbForTests` 메서드에서 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-454">Database seeding in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="5b05a-455">이 메서드는 [통합 테스트 샘플: 테스트 앱 조직](#test-app-organization) 섹션에 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-455">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

2. <span data-ttu-id="5b05a-456">테스트 클래스에서 사용자 지정 `CustomWebApplicationFactory`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-456">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="5b05a-457">다음 예제에서는 `IndexPageTests` 클래스에서 팩터리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-457">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="5b05a-458">샘플 앱 클라이언트는 `HttpClient`의 다음 리디렉션을 방지하도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-458">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="5b05a-459">[모의 인증](#mock-authentication) 섹션 뒷부분에 설명된 것처럼 이를 통해 테스트는 앱의 첫 번째 응답 결과를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-459">As explained later in the [Mock authentication](#mock-authentication) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="5b05a-460">첫 번째 응답은 `Location` 헤더를 사용하는 이러한 테스트 대부분에서 리디렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-460">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="5b05a-461">일반적인 테스트는 `HttpClient` 및 도우미 메서드를 사용하여 요청 및 응답을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-461">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="5b05a-462">SUT에 대한 POST 요청은 앱의 [데이터 보호 위조 방지 시스템](xref:security/data-protection/introduction)에서 자동으로 수행하는 위조 방지 검사를 충족해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-462">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="5b05a-463">테스트의 POST 요청을 정렬하기 위해 테스트 앱은 다음을 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-463">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="5b05a-464">페이지에 대한 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-464">Make a request for the page.</span></span>
1. <span data-ttu-id="5b05a-465">위조 방지 쿠키를 구문 분석하고 응답에서 유효성 검사 토큰을 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-465">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="5b05a-466">위조 방지 쿠키 및 요청 유효성 검사 토큰을 사용하여 POST 요청을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-466">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="5b05a-467">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)의 `SendAsync` 도우미 확장 메서드(*Helpers/HttpClientExtensions.cs*) 및 `GetDocumentAsync` 도우미 메서드(*Helpers/HtmlHelpers.cs*)는 [AngleSharp](https://anglesharp.github.io/) 파서를 사용하여 다음 메서드를 사용한 위조 방지 확인을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-467">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="5b05a-468">`GetDocumentAsync` &ndash; [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage)를 수신하고 `IHtmlDocument`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-468">`GetDocumentAsync` &ndash; Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="5b05a-469">`GetDocumentAsync`는 원본 `HttpResponseMessage`에 따라 *가상 응답*을 준비하는 팩터리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-469">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="5b05a-470">자세한 내용은 [AngleSharp 설명서](https://github.com/AngleSharp/AngleSharp#documentation)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5b05a-470">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="5b05a-471">`HttpClient`에 대한 `SendAsync` 확장 메서드는 [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage)를 작성하고 [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_)를 호출하여 요청을 SUT에 제출합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-471">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="5b05a-472">`SendAsync`에 대한 오버로드는 HTML 양식(`IHtmlFormElement`) 및 다음을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-472">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="5b05a-473">양식(`IHtmlElement`)의 제출 단추</span><span class="sxs-lookup"><span data-stu-id="5b05a-473">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="5b05a-474">양식 값 컬렉션(`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="5b05a-474">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="5b05a-475">제출 단추(`IHtmlElement`) 및 양식 값(`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="5b05a-475">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="5b05a-476">[AngleSharp](https://anglesharp.github.io/)는 이 항목 및 샘플 앱에서 데모용으로 사용되는 타사 구문 분석 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-476">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="5b05a-477">AngleSharp는 ASP.NET Core 앱의 통합 테스트에 대해 지원되지 않거나 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-477">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="5b05a-478">[HAP(Html Agility Pack)](https://html-agility-pack.net/)와 같은 기타 파서를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-478">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="5b05a-479">또 다른 방법은 위조 방지 시스템의 요청 확인 토큰과 위조 방지 쿠키를 직접 처리하는 코드를 작성하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-479">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="5b05a-480">WithWebHostBuilder를 사용하여 클라이언트 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="5b05a-480">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="5b05a-481">테스트 메서드 내에서 추가 구성이 필요한 경우 [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder)는 구성에서 추가로 사용자 지정되는 [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)를 사용하여 새 `WebApplicationFactory`를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-481">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="5b05a-482">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)의 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 테스트 메서드는 `WithWebHostBuilder`의 사용을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-482">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="5b05a-483">이 테스트는 SUT에서 양식 제출을 트리거하여 데이터베이스에서 레코드 삭제를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-483">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="5b05a-484">`IndexPageTests` 클래스의 다른 테스트는 데이터베이스의 모든 레코드를 삭제하는 작업을 수행하고 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 메서드 이전에 실행될 수 있기 때문에, 이 테스트 메서드에서 데이터베이스가 다시 시드되어 SUT가 삭제할 레코드가 존재하게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-484">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="5b05a-485">SUT에서 `messages` 양식의 첫 번째 삭제 단추를 선택하면 SUT에 대한 요청에서 시뮬레이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-485">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="5b05a-486">클라이언트 옵션</span><span class="sxs-lookup"><span data-stu-id="5b05a-486">Client options</span></span>

<span data-ttu-id="5b05a-487">다음 표에서는 `HttpClient` 인스턴스를 만들 때 사용할 수 있는 기본 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions)를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-487">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="5b05a-488">옵션</span><span class="sxs-lookup"><span data-stu-id="5b05a-488">Option</span></span> | <span data-ttu-id="5b05a-489">설명</span><span class="sxs-lookup"><span data-stu-id="5b05a-489">Description</span></span> | <span data-ttu-id="5b05a-490">기본값</span><span class="sxs-lookup"><span data-stu-id="5b05a-490">Default</span></span> |
| ------ | ----------- | ------- |
| [<span data-ttu-id="5b05a-491">AllowAutoRedirect</span><span class="sxs-lookup"><span data-stu-id="5b05a-491">AllowAutoRedirect</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | <span data-ttu-id="5b05a-492">`HttpClient` 인스턴스가 자동으로 리디렉션 응답을 따르는지 여부를 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-492">Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span> | `true` |
| [<span data-ttu-id="5b05a-493">BaseAddress</span><span class="sxs-lookup"><span data-stu-id="5b05a-493">BaseAddress</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | <span data-ttu-id="5b05a-494">`HttpClient` 인스턴스의 기준 주소를 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-494">Gets or sets the base address of `HttpClient` instances.</span></span> | `http://localhost` |
| [<span data-ttu-id="5b05a-495">HandleCookies</span><span class="sxs-lookup"><span data-stu-id="5b05a-495">HandleCookies</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | <span data-ttu-id="5b05a-496">`HttpClient` 인스턴스에서 쿠키를 처리할지 여부를 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-496">Gets or sets whether `HttpClient` instances should handle cookies.</span></span> | `true` |
| [<span data-ttu-id="5b05a-497">MaxAutomaticRedirections</span><span class="sxs-lookup"><span data-stu-id="5b05a-497">MaxAutomaticRedirections</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | <span data-ttu-id="5b05a-498">`HttpClient` 인스턴스에서 따라야 하는 리디렉션 응답의 최대 수를 가져오거나 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-498">Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> | <span data-ttu-id="5b05a-499">7</span><span class="sxs-lookup"><span data-stu-id="5b05a-499">7</span></span> |

<span data-ttu-id="5b05a-500">`WebApplicationFactoryClientOptions` 클래스를 만들고 [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) 메서드에 전달합니다(기본값은 코드 예제에 표시됨).</span><span class="sxs-lookup"><span data-stu-id="5b05a-500">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="5b05a-501">모의 서비스 주입</span><span class="sxs-lookup"><span data-stu-id="5b05a-501">Inject mock services</span></span>

<span data-ttu-id="5b05a-502">호스트 작성기에서 [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices)에 대한 호출을 사용하여 테스트에서 서비스를 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-502">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="5b05a-503">**모의 서비스를 주입하려면 SUT에 `Startup.ConfigureServices` 메서드를 포함하는 `Startup` 클래스가 있어야 합니다.**</span><span class="sxs-lookup"><span data-stu-id="5b05a-503">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="5b05a-504">샘플 SUT에는 따옴표를 반환하는 범위 지정 서비스가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-504">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="5b05a-505">인덱스 페이지를 요청하면 따옴표가 인덱스 페이지의 숨겨진 필드에 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-505">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="5b05a-506">*Services/IQuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="5b05a-506">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="5b05a-507">*Services/QuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="5b05a-507">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="5b05a-508">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="5b05a-508">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="5b05a-509">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="5b05a-509">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="5b05a-510">*Pages/Index.cs*:</span><span class="sxs-lookup"><span data-stu-id="5b05a-510">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="5b05a-511">다음 태그는 SUT 앱이 실행될 때 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-511">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="5b05a-512">통합 테스트에서 서비스 및 따옴표 주입을 테스트하기 위해, 테스트를 통해 모의 서비스가 SUT에 주입됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-512">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="5b05a-513">모의 서비스는 앱의 `QuoteService`를 `TestQuoteService`이라는 테스트 앱에서 제공하는 서비스로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-513">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="5b05a-514">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="5b05a-514">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="5b05a-515">`ConfigureTestServices`가 호출되고 범위 지정 서비스가 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-515">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="5b05a-516">테스트 실행 중에 생성된 태그는 `TestQuoteService`에서 제공하는 따옴표 텍스트를 반영하므로 어설션은 성공합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-516">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a><span data-ttu-id="5b05a-517">모의 인증</span><span class="sxs-lookup"><span data-stu-id="5b05a-517">Mock authentication</span></span>

<span data-ttu-id="5b05a-518">`AuthTests` 클래스의 테스트는 보안 엔드포인트가 다음을 수행하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-518">Tests in the `AuthTests` class check that a secure endpoint:</span></span>

* <span data-ttu-id="5b05a-519">인증되지 않은 사용자를 앱의 로그인 페이지로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-519">Redirects an unauthenticated user to the app's Login page.</span></span>
* <span data-ttu-id="5b05a-520">인증된 사용자에 대한 콘텐츠를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-520">Returns content for an authenticated user.</span></span>

<span data-ttu-id="5b05a-521">SUT에서 `/SecurePage` 페이지는 [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) 규칙을 사용하여 페이지에 [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter)를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-521">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="5b05a-522">자세한 내용은 [Razor Pages 권한 부여 규칙](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5b05a-522">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="5b05a-523">`Get_SecurePageRedirectsAnUnauthenticatedUser` 테스트에서 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions)는 [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect)를 `false`로 설정하여 리디렉션을 허용하지 않도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-523">In the `Get_SecurePageRedirectsAnUnauthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

<span data-ttu-id="5b05a-524">클라이언트에서 리디렉션을 따르도록 허용하지 않으면 다음 검사를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-524">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="5b05a-525">SUT에서 반환된 상태 코드는 로그인 페이지로 리디렉션된 후의 최종 상태 코드([HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode))가 아니라 예상된 [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) 결과에 따라 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-525">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="5b05a-526">응답 헤더의 `Location` 헤더 값을 검토하여 `Location` 헤더가 없는 마지막 로그인 페이지 응답이 아니라 `http://localhost/Identity/Account/Login`으로 시작하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-526">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="5b05a-527">테스트 앱은 인증 및 권한 부여의 측면을 테스트하기 위해 [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices)에서 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>를 모의로 시험할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-527">The test app can mock an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) in order to test aspects of authentication and authorization.</span></span> <span data-ttu-id="5b05a-528">최소 시나리오는 [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*)를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-528">A minimal scenario returns an [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

<span data-ttu-id="5b05a-529">`TestAuthHandler`는 인증 체계가 `Test`로 설정된 사용자를 인증하도록 호출됩니다. 여기서는 `AddAuthentication`이 `ConfigureTestServices`에 등록되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-529">The `TestAuthHandler` is called to authenticate a user when the authentication scheme is set to `Test` where `AddAuthentication` is registered for `ConfigureTestServices`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

<span data-ttu-id="5b05a-530">`WebApplicationFactoryClientOptions`에 대한 자세한 내용은 [클라이언트 옵션](#client-options) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5b05a-530">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="5b05a-531">환경 설정</span><span class="sxs-lookup"><span data-stu-id="5b05a-531">Set the environment</span></span>

<span data-ttu-id="5b05a-532">기본적으로 SUT의 호스트 및 앱 환경은 개발 환경을 사용하도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-532">By default, the SUT's host and app environment is configured to use the Development environment.</span></span> <span data-ttu-id="5b05a-533">SUT의 환경을 재정의하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-533">To override the SUT's environment:</span></span>

* <span data-ttu-id="5b05a-534">`ASPNETCORE_ENVIRONMENT` 환경 변수(예: `Staging`, `Production` 또는 `Testing`과 같은 기타 사용자 지정 값)를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-534">Set the `ASPNETCORE_ENVIRONMENT` environment variable (for example, `Staging`, `Production`, or other custom value, such as `Testing`).</span></span>
* <span data-ttu-id="5b05a-535">`ASPNETCORE`를 접두사로 사용하는 환경 변수를 읽도록 테스트 앱의 `CreateHostBuilder`를 재정의합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-535">Override `CreateHostBuilder` in the test app to read environment variables prefixed with `ASPNETCORE`.</span></span>

```csharp
protected override IHostBuilder CreateHostBuilder() => 
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="5b05a-536">테스트 인프라가 앱 콘텐츠 루트 경로를 유추하는 방법</span><span class="sxs-lookup"><span data-stu-id="5b05a-536">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="5b05a-537">`WebApplicationFactory` 생성자는 `TEntryPoint` 어셈블리 `System.Reflection.Assembly.FullName`와 같은 키가 있는 통합 테스트를 포함하는 어셈블리에서 [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute)를 검색하여 앱 [콘텐츠 루트](xref:fundamentals/index#content-root) 경로를 유추합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-537">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="5b05a-538">올바른 키를 포함하는 특성을 찾을 수 없는 경우 `WebApplicationFactory`는 솔루션 파일( *.sln*) 검색으로 대체되고 `TEntryPoint` 어셈블리 이름을 솔루션 디렉터리에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-538">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="5b05a-539">앱 루트 디렉터리(콘텐츠 루트 경로)는 뷰와 콘텐츠 파일을 검색하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-539">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="5b05a-540">섀도 복사 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="5b05a-540">Disable shadow copying</span></span>

<span data-ttu-id="5b05a-541">섀도 복사를 수행하면 테스트가 출력 디렉터리와는 다른 디렉터리에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-541">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="5b05a-542">테스트가 제대로 작동하려면 섀도 복사를 사용하지 않도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-542">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="5b05a-543">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)은 xUnit을 사용하고 올바른 구성 설정에 *xunit.runner.json* 파일을 포함하여 xUnit에 대한 섀도 복사를 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-543">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="5b05a-544">자세한 내용은 [JSON으로 xUnit 구성](https://xunit.github.io/docs/configuring-with-json.html)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5b05a-544">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="5b05a-545">다음 콘텐츠를 사용하여 테스트 프로젝트의 루트에 *xunit.runner.json* 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-545">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

<span data-ttu-id="5b05a-546">Visual Studio를 사용하는 경우 파일의 **출력 디렉터리로 복사** 속성을 **항상 복사**로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-546">If using Visual Studio, set the file's **Copy to Output Directory** property to **Copy always**.</span></span> <span data-ttu-id="5b05a-547">Visual Studio를 사용하지 않는 경우 테스트 앱의 프로젝트 파일에 `Content` 대상을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-547">If not using Visual Studio, add a `Content` target to the test app's project file:</span></span>

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a><span data-ttu-id="5b05a-548">개체 삭제</span><span class="sxs-lookup"><span data-stu-id="5b05a-548">Disposal of objects</span></span>

<span data-ttu-id="5b05a-549">`IClassFixture` 구현의 테스트를 실행한 후 [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) 및 [HttpClient](/dotnet/api/system.net.http.httpclient)는 xUnit이 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)을 삭제하면 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-549">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="5b05a-550">개발자가 인스턴스화한 개체를 삭제해야 하는 경우 `IClassFixture` 구현에서 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-550">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="5b05a-551">자세한 내용은 [Dispose 메서드 구현](/dotnet/standard/garbage-collection/implementing-dispose)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5b05a-551">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="5b05a-552">통합 테스트 샘플</span><span class="sxs-lookup"><span data-stu-id="5b05a-552">Integration tests sample</span></span>

<span data-ttu-id="5b05a-553">[샘플 앱](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)은 다음 두 앱으로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-553">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="5b05a-554">앱</span><span class="sxs-lookup"><span data-stu-id="5b05a-554">App</span></span> | <span data-ttu-id="5b05a-555">프로젝트 디렉터리</span><span class="sxs-lookup"><span data-stu-id="5b05a-555">Project directory</span></span> | <span data-ttu-id="5b05a-556">설명</span><span class="sxs-lookup"><span data-stu-id="5b05a-556">Description</span></span> |
| --- | ----------------- | ----------- |
| <span data-ttu-id="5b05a-557">메시지 앱(SUT)</span><span class="sxs-lookup"><span data-stu-id="5b05a-557">Message app (the SUT)</span></span> | <span data-ttu-id="5b05a-558">*src/RazorPagesProject*</span><span class="sxs-lookup"><span data-stu-id="5b05a-558">*src/RazorPagesProject*</span></span> | <span data-ttu-id="5b05a-559">사용자가 메시지를 추가, 메시지 1개 또는 모든 메시지를 삭제, 메시지를 분석할 수 있도록 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-559">Allows a user to add, delete one, delete all, and analyze messages.</span></span> |
| <span data-ttu-id="5b05a-560">앱 테스트</span><span class="sxs-lookup"><span data-stu-id="5b05a-560">Test app</span></span> | <span data-ttu-id="5b05a-561">*tests/RazorPagesProject.Tests*</span><span class="sxs-lookup"><span data-stu-id="5b05a-561">*tests/RazorPagesProject.Tests*</span></span> | <span data-ttu-id="5b05a-562">SUT에 대해 통합 테스트를 수행하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-562">Used to integration test the SUT.</span></span> |

<span data-ttu-id="5b05a-563">[Visual Studio](https://visualstudio.microsoft.com)와 같은 IDE의 기본 제공 테스트 기능을 사용하여 테스트를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-563">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="5b05a-564">[Visual Studio Code](https://code.visualstudio.com/) 또는 명령줄을 사용하는 경우 *tests/RazorPagesProject.Tests* 디렉터리의 명령 프롬프트에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-564">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="5b05a-565">메시지 앱(SUT) 조직</span><span class="sxs-lookup"><span data-stu-id="5b05a-565">Message app (SUT) organization</span></span>

<span data-ttu-id="5b05a-566">SUT는 다음과 같은 특징을 가진 Razor Pages 메시지 시스템입니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-566">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="5b05a-567">앱의 인덱스 페이지(*Pages/Index.cshtml* 및 *Pages/Index.cshtml.cs*)는 메시지의 추가, 삭제 및 분석을 제어하기 위한 UI 및 페이지 모델 메서드를 제공합니다(메시지당 평균 단어 수).</span><span class="sxs-lookup"><span data-stu-id="5b05a-567">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="5b05a-568">메시지는 `Id`(키) 및 `Text`(메시지)의 두 가지 속성을 사용하여 `Message` 클래스(*Data/Message.cs*)에서 설명됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-568">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="5b05a-569">`Text` 속성은 필수이며 200자로 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-569">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="5b05a-570">메시지는 [Entity Framework의 메모리 내 데이터베이스](/ef/core/providers/in-memory/)를 사용하여 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-570">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="5b05a-571">앱은 데이터베이스 컨텍스트 클래스 `AppDbContext`(*Data/AppDbContext*)에 DAL(데이터 액세스 계층)을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-571">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="5b05a-572">앱 시작 시 데이터베이스가 비어 있는 경우 메시지 저장소는 세 개의 메시지로 초기화됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-572">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="5b05a-573">앱에는 인증된 사용자만 액세스할 수 있는 `/SecurePage`가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-573">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="5b05a-574">EF 항목 [InMemory로 테스트](/ef/core/miscellaneous/testing/in-memory)에서는 MSTest를 사용하여 테스트에 메모리 내 데이터베이스를 사용하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-574">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="5b05a-575">이 항목에서는 [xUnit](https://xunit.github.io/) 테스트 프레임워크를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-575">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="5b05a-576">여러 테스트 프레임워크의 테스트 개념 및 테스트 구현은 비슷하지만 동일하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-576">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="5b05a-577">앱은 리포지토리 패턴을 사용하지 않고 [UoW(작업 단위) 패턴](https://martinfowler.com/eaaCatalog/unitOfWork.html)의 효과적인 예가 아니지만 Razor Pages는 이러한 개발 패턴을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-577">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="5b05a-578">자세한 내용은 [인프라 지속성 계층 디자인](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) 및 [테스트 컨트롤러 논리](/aspnet/core/mvc/controllers/testing)(샘플에서 리포지토리 패턴 구현)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5b05a-578">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="5b05a-579">테스트 앱 구성</span><span class="sxs-lookup"><span data-stu-id="5b05a-579">Test app organization</span></span>

<span data-ttu-id="5b05a-580">테스트 앱은 *tests/RazorPagesProject.Tests* 디렉터리 내에 있는 콘솔 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-580">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="5b05a-581">테스트 앱 디렉터리</span><span class="sxs-lookup"><span data-stu-id="5b05a-581">Test app directory</span></span> | <span data-ttu-id="5b05a-582">설명</span><span class="sxs-lookup"><span data-stu-id="5b05a-582">Description</span></span> |
| ------------------ | ----------- |
| <span data-ttu-id="5b05a-583">*AuthTests*</span><span class="sxs-lookup"><span data-stu-id="5b05a-583">*AuthTests*</span></span> | <span data-ttu-id="5b05a-584">다음에 대한 테스트 메서드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-584">Contains test methods for:</span></span><ul><li><span data-ttu-id="5b05a-585">인증되지 않은 사용자가 보안 페이지에 액세스</span><span class="sxs-lookup"><span data-stu-id="5b05a-585">Accessing a secure page by an unauthenticated user.</span></span></li><li><span data-ttu-id="5b05a-586">모의 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>를 사용하여 인증된 사용자가 보안 페이지에 액세스</span><span class="sxs-lookup"><span data-stu-id="5b05a-586">Accessing a secure page by an authenticated user with a mock <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span></li><li><span data-ttu-id="5b05a-587">GitHub 사용자 프로필을 가져오고 프로필의 사용자 로그인 확인</span><span class="sxs-lookup"><span data-stu-id="5b05a-587">Obtaining a GitHub user profile and checking the profile's user login.</span></span></li></ul> |
| <span data-ttu-id="5b05a-588">*BasicTests*</span><span class="sxs-lookup"><span data-stu-id="5b05a-588">*BasicTests*</span></span> | <span data-ttu-id="5b05a-589">라우팅 및 콘텐츠 형식에 대한 테스트 메서드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-589">Contains a test method for routing and content type.</span></span> |
| <span data-ttu-id="5b05a-590">*IntegrationTests*</span><span class="sxs-lookup"><span data-stu-id="5b05a-590">*IntegrationTests*</span></span> | <span data-ttu-id="5b05a-591">사용자 지정 `WebApplicationFactory` 클래스를 사용하여 인덱스 페이지에 대한 통합 테스트를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-591">Contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> |
| <span data-ttu-id="5b05a-592">*Helpers/Utilities*</span><span class="sxs-lookup"><span data-stu-id="5b05a-592">*Helpers/Utilities*</span></span> | <ul><li><span data-ttu-id="5b05a-593">*Utilities.cs*에는 테스트 데이터로 데이터베이스를 시드하는 데 사용되는 `InitializeDbForTests` 메서드가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-593">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="5b05a-594">*HtmlHelpers.cs*는 테스트 메서드에서 사용할 AngleSharp `IHtmlDocument`를 반환하기 위한 메서드를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-594">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="5b05a-595">*HttpClientExtensions.cs*는 `SendAsync`에 대한 오버로드를 제공하여 SUT에 요청을 제출합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-595">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="5b05a-596">테스트 프레임워크는 [xUnit](https://xunit.github.io/)입니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-596">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="5b05a-597">[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)를 포함하는 [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost)를 사용하여 통합 테스트가 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-597">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="5b05a-598">[Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 패키지를 사용하여 테스트 호스트와 테스트 서버를 구성하기 때문에 `TestHost` 및 `TestServer` 패키지에는 테스트 앱의 프로젝트 파일 또는 테스트 앱의 개발자 구성에서 직접 패키지 참조가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-598">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="5b05a-599">**테스트를 위해 데이터베이스 시드**</span><span class="sxs-lookup"><span data-stu-id="5b05a-599">**Seeding the database for testing**</span></span>

<span data-ttu-id="5b05a-600">통합 테스트에서는 일반적으로 테스트 실행 전에 데이터베이스에 작은 데이터 세트가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-600">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="5b05a-601">예를 들어, 삭제 테스트는 데이터베이스 레코드 삭제를 요구하므로 삭제 요청이 성공하려면 데이터베이스에 적어도 하나 이상의 레코드가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-601">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="5b05a-602">샘플 앱은 테스트가 실행될 때 사용할 수 있는 *Utilities.cs*의 세 가지 메시지로 데이터베이스를 시드합니다.</span><span class="sxs-lookup"><span data-stu-id="5b05a-602">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="5b05a-603">추가 자료</span><span class="sxs-lookup"><span data-stu-id="5b05a-603">Additional resources</span></span>

* [<span data-ttu-id="5b05a-604">단위 테스트</span><span class="sxs-lookup"><span data-stu-id="5b05a-604">Unit tests</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:test/razor-pages-tests>
* <xref:fundamentals/middleware/index>
* <xref:mvc/controllers/testing>
