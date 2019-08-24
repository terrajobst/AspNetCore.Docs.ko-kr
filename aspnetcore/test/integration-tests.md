---
title: ASP.NET Core의 통합 테스트
author: guardrex
description: 통합 테스트를 사용하여 앱의 구성 요소가 데이터베이스, 파일 시스템 및 네트워크를 비롯한 인프라 수준에서 제대로 작동하는지 확인하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/23/2019
uid: test/integration-tests
ms.openlocfilehash: 195acd3e03f3de63ebd61767f2c86d1c0f38fca5
ms.sourcegitcommit: 983b31449fe398e6e922eb13e9eb6f4287ec91e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2019
ms.locfileid: "70017440"
---
# <a name="integration-tests-in-aspnet-core"></a><span data-ttu-id="fa9e0-103">ASP.NET Core의 통합 테스트</span><span class="sxs-lookup"><span data-stu-id="fa9e0-103">Integration tests in ASP.NET Core</span></span>

<span data-ttu-id="fa9e0-104">By [Luke Latham 문자](https://github.com/guardrex) 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="fa9e0-104">By [Luke Latham](https://github.com/guardrex) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="fa9e0-105">통합 테스트는 응용 프로그램의 구성 요소가 응용 프로그램의 지원 인프라 (예: 데이터베이스, 파일 시스템 및 네트워크)를 포함 하는 수준에서 올바르게 작동 하는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-105">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="fa9e0-106">ASP.NET Core는 테스트 웹 호스트 및 메모리 내 테스트 서버에서 단위 테스트 프레임 워크를 사용 하는 통합 테스트를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-106">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="fa9e0-107">이 항목에서는 단위 테스트에 대해 기본적으로 이해 하 고 있다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-107">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="fa9e0-108">테스트 개념을 잘 모르는 경우 [.Net Core의 단위 테스트 및 .NET Standard](/dotnet/core/testing/) 항목 및 연결 된 콘텐츠를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-108">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="fa9e0-109">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fa9e0-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="fa9e0-110">샘플 앱은 Razor Pages 앱 이며 Razor Pages를 기본적으로 이해 하 고 있다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-110">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="fa9e0-111">Razor Pages에 익숙하지 않은 경우 다음 항목을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-111">If unfamiliar with Razor Pages, see the following topics:</span></span>

* [<span data-ttu-id="fa9e0-112">Razor 페이지 소개</span><span class="sxs-lookup"><span data-stu-id="fa9e0-112">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="fa9e0-113">Razor 페이지 시작</span><span class="sxs-lookup"><span data-stu-id="fa9e0-113">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="fa9e0-114">Razor 페이지 단위 테스트</span><span class="sxs-lookup"><span data-stu-id="fa9e0-114">Razor Pages unit tests</span></span>](xref:test/razor-pages-tests)

> [!NOTE]
> <span data-ttu-id="fa9e0-115">SPAs 테스트를 위해 브라우저를 자동화할 수 있는 [Selenium](https://www.seleniumhq.org/)와 같은 도구를 권장 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-115">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="fa9e0-116">통합 테스트 소개</span><span class="sxs-lookup"><span data-stu-id="fa9e0-116">Introduction to integration tests</span></span>

<span data-ttu-id="fa9e0-117">통합 테스트는 [단위 테스트](/dotnet/core/testing/)보다 더 광범위 한 수준에서 앱의 구성 요소를 평가 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-117">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="fa9e0-118">단위 테스트는 개별 클래스 메서드와 같은 격리 된 소프트웨어 구성 요소를 테스트 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-118">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="fa9e0-119">통합 테스트는 두 개 이상의 앱 구성 요소가 함께 작동 하 여 요청을 완전히 처리 하는 데 필요한 모든 구성 요소를 포함 하 여 예상 결과를 생성 하는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-119">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="fa9e0-120">이러한 광범위 한 테스트는 다음과 같은 구성 요소를 포함 하 여 앱의 인프라 및 전체 프레임 워크를 테스트 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-120">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="fa9e0-121">데이터베이스</span><span class="sxs-lookup"><span data-stu-id="fa9e0-121">Database</span></span>
* <span data-ttu-id="fa9e0-122">파일 시스템</span><span class="sxs-lookup"><span data-stu-id="fa9e0-122">File system</span></span>
* <span data-ttu-id="fa9e0-123">네트워크 어플라이언스</span><span class="sxs-lookup"><span data-stu-id="fa9e0-123">Network appliances</span></span>
* <span data-ttu-id="fa9e0-124">요청-응답 파이프라인</span><span class="sxs-lookup"><span data-stu-id="fa9e0-124">Request-response pipeline</span></span>

<span data-ttu-id="fa9e0-125">단위 테스트는 인프라 구성 요소 대신 *fakes* 또는 *모의 개체*라고 하는 구성 된 구성 요소를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-125">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="fa9e0-126">단위 테스트와 달리 통합 테스트는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-126">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="fa9e0-127">프로덕션 환경에서 앱이 사용 하는 실제 구성 요소를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-127">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="fa9e0-128">더 많은 코드와 데이터 처리가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-128">Require more code and data processing.</span></span>
* <span data-ttu-id="fa9e0-129">실행 하는 데 시간이 더 오래 걸립니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-129">Take longer to run.</span></span>

<span data-ttu-id="fa9e0-130">따라서 가장 중요 한 인프라 시나리오에 대 한 통합 테스트 사용을 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-130">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="fa9e0-131">단위 테스트 또는 통합 테스트를 사용 하 여 동작을 테스트할 수 있는 경우 단위 테스트를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-131">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="fa9e0-132">데이터베이스 및 파일 시스템을 사용 하 여 모든 가능한 데이터 순열 및 파일 액세스에 대해 통합 테스트를 작성 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-132">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="fa9e0-133">응용 프로그램 전체에서 데이터베이스 및 파일 시스템과 상호 작용 하는 위치에 관계 없이, 일반적으로 데이터베이스 및 파일 시스템 구성 요소를 적절 하 게 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-133">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="fa9e0-134">이러한 구성 요소와 상호 작용 하는 메서드 논리의 루틴 테스트에 단위 테스트를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-134">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="fa9e0-135">단위 테스트에서 인프라 fakes/mock를 사용 하면 테스트 실행 속도가 빨라집니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-135">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="fa9e0-136">통합 테스트에 대 한 토론에서 테스트 된 프로젝트를 종종 *테스트 중인 시스템*또는 short의 경우 "sut" 라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-136">In discussions of integration tests, the tested project is frequently called the *system under test*, or "SUT" for short.</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="fa9e0-137">ASP.NET Core 통합 테스트</span><span class="sxs-lookup"><span data-stu-id="fa9e0-137">ASP.NET Core integration tests</span></span>

<span data-ttu-id="fa9e0-138">ASP.NET Core의 통합 테스트에는 다음이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-138">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="fa9e0-139">테스트 프로젝트는 테스트를 포함 하 고 실행 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-139">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="fa9e0-140">테스트 프로젝트에는 테스트 *대상 시스템* (sut) 이라고 하는 테스트 된 ASP.NET Core 프로젝트에 대 한 참조가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-140">The test project has a reference to the tested ASP.NET Core project, called the *system under test* (SUT).</span></span> <span data-ttu-id="fa9e0-141">_"SUT"는이 항목 전체에서 테스트 된 앱을 참조 하는 데 사용 됩니다._</span><span class="sxs-lookup"><span data-stu-id="fa9e0-141">_"SUT" is used throughout this topic to refer to the tested app._</span></span>
* <span data-ttu-id="fa9e0-142">테스트 프로젝트는 SUT에 대 한 테스트 웹 호스트를 만들고 테스트 서버 클라이언트를 사용 하 여 SUT에 대 한 요청 및 응답을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-142">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses to the SUT.</span></span>
* <span data-ttu-id="fa9e0-143">Test runner는 테스트를 실행 하 고 테스트 결과를 보고 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-143">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="fa9e0-144">통합 테스트는 일반적인 *정렬*, 작업 및 *어설션* 테스트 단계를 포함하는 이벤트 시퀀스를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-144">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="fa9e0-145">SUT의 웹 호스트가 구성 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-145">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="fa9e0-146">앱에 요청을 제출 하는 테스트 서버 클라이언트가 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-146">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="fa9e0-147">*정렬* 테스트 단계를 실행 합니다. 테스트 앱은 요청을 준비 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-147">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="fa9e0-148">*Act* 테스트 단계를 실행 합니다. 클라이언트는 요청을 제출 하 고 응답을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-148">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="fa9e0-149">*Assert* 테스트 단계가 실행 됩니다. *실제* 응답의 유효성은 *예상* 된 응답에 따라 *성공* 또는 *실패* 로 확인 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-149">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="fa9e0-150">모든 테스트가 실행 될 때까지 프로세스가 계속 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-150">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="fa9e0-151">테스트 결과가 보고 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-151">The test results are reported.</span></span>

<span data-ttu-id="fa9e0-152">일반적으로 테스트 웹 호스트는 테스트 실행을 위해 응용 프로그램의 일반 웹 호스트와 다르게 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-152">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="fa9e0-153">예를 들어 다른 데이터베이스 또는 다른 앱 설정을 테스트에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-153">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="fa9e0-154">테스트 웹 호스트 및 메모리 내 테스트 서버 ([Testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver))와 같은 인프라 구성 요소는 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 패키지에서 제공 하거나 관리 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-154">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="fa9e0-155">이 패키지를 사용 하면 테스트 생성 및 실행이 간소화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-155">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="fa9e0-156">패키지 `Microsoft.AspNetCore.Mvc.Testing` 에서 처리 하는 작업은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-156">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="fa9e0-157">Sut의 종속성 파일 ( *\*. deps*)을 테스트 프로젝트의 *bin* 디렉터리에 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-157">Copies the dependencies file (*\*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="fa9e0-158">테스트를 실행할 때 정적 파일 및 페이지/뷰가 검색 되도록 콘텐츠 루트를 SUT의 프로젝트 루트로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-158">Sets the content root to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="fa9e0-159">`TestServer`를 사용하여 Sut를 간소화하는 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) 클래스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-159">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="fa9e0-160">[단위 테스트](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) 설명서에서는 테스트를 실행 하는 방법에 대 한 자세한 지침과 테스트 및 테스트 클래스의 이름을 설정 하는 방법에 대 한 자세한 지침과 함께 테스트 프로젝트 및 test runner를 설정 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-160">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="fa9e0-161">앱에 대 한 테스트 프로젝트를 만들 때 통합 테스트에서 여러 프로젝트로 단위 테스트를 분리 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-161">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="fa9e0-162">이를 통해 인프라 테스트 구성 요소가 실수로 단위 테스트에 포함 되지 않도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-162">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="fa9e0-163">단위 및 통합 테스트를 분리 하 여 실행 되는 테스트 집합을 제어할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-163">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="fa9e0-164">Razor Pages 앱과 MVC 앱의 테스트에 대 한 구성의 차이는 거의 없습니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-164">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="fa9e0-165">유일한 차이점은 테스트의 이름을 지정 하는 방법 뿐입니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-165">The only difference is in how the tests are named.</span></span> <span data-ttu-id="fa9e0-166">Razor Pages 앱에서 페이지 끝점의 테스트는 일반적으로 페이지 모델 클래스 (예: `IndexPageTests` 인덱스 페이지에 대 한 구성 요소 통합을 테스트 하는 데 사용할 수 있습니다) 뒤에 이름이 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-166">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="fa9e0-167">MVC 앱에서 테스트는 일반적으로 컨트롤러 클래스로 구성 되며 테스트 `HomeControllerTests` 하는 컨트롤러 (예: 홈 컨트롤러에 대 한 구성 요소 통합 테스트) 뒤에 이름이 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-167">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="fa9e0-168">응용 프로그램 테스트 필수 조건</span><span class="sxs-lookup"><span data-stu-id="fa9e0-168">Test app prerequisites</span></span>

<span data-ttu-id="fa9e0-169">테스트 프로젝트는 다음을 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-169">The test project must:</span></span>

* <span data-ttu-id="fa9e0-170">다음 패키지를 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-170">Reference the following packages:</span></span>
  * [<span data-ttu-id="fa9e0-171">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="fa9e0-171">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [<span data-ttu-id="fa9e0-172">Microsoft.AspNetCore.Mvc.Testing</span><span class="sxs-lookup"><span data-stu-id="fa9e0-172">Microsoft.AspNetCore.Mvc.Testing</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* <span data-ttu-id="fa9e0-173">프로젝트 파일 (`<Project Sdk="Microsoft.NET.Sdk.Web">`)에 웹 SDK를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-173">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span> <span data-ttu-id="fa9e0-174">웹 SDK는 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app)를 참조할 때 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-174">The Web SDK is required when referencing the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="fa9e0-175">이러한 필수 구성 요소는 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)에서 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-175">These prerequisites can be seen in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="fa9e0-176">*테스트/RazorPagesProject* . 테스트/razor 프로젝트. .csproj 파일을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-176">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="fa9e0-177">샘플 앱은 [Xunit](https://xunit.github.io/) 테스트 프레임 워크 및 [AngleSharp](https://anglesharp.github.io/) 파서 라이브러리를 사용 하므로 샘플 앱은 다음을 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-177">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="fa9e0-178">xunit</span><span class="sxs-lookup"><span data-stu-id="fa9e0-178">xunit</span></span>](https://www.nuget.org/packages/xunit/)
* [<span data-ttu-id="fa9e0-179">xunit.runner.visualstudio</span><span class="sxs-lookup"><span data-stu-id="fa9e0-179">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [<span data-ttu-id="fa9e0-180">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="fa9e0-180">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a><span data-ttu-id="fa9e0-181">SUT 환경</span><span class="sxs-lookup"><span data-stu-id="fa9e0-181">SUT environment</span></span>

<span data-ttu-id="fa9e0-182">SUT의 [환경이](xref:fundamentals/environments) 설정 되지 않은 경우 환경의 기본값은 Development로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-182">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="fa9e0-183">기본 WebApplicationFactory를 사용 하는 기본 테스트</span><span class="sxs-lookup"><span data-stu-id="fa9e0-183">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="fa9e0-184">[WebApplicationFactory&lt;tentrypoint&gt; ](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) 는 통합 테스트용 [testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver) 를 만드는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-184">[WebApplicationFactory&lt;TEntryPoint&gt;](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="fa9e0-185">`TEntryPoint`는 sut의 진입점 클래스입니다. 일반적으로 `Startup` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-185">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="fa9e0-186">테스트 클래스는 클래스에 테스트를 포함 하 고 클래스의 테스트에서 공유 개체 인스턴스를 제공 하는 클래스 *fixture* 인터페이스 ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture))를 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-186">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

### <a name="basic-test-of-app-endpoints"></a><span data-ttu-id="fa9e0-187">앱 끝점의 기본 테스트</span><span class="sxs-lookup"><span data-stu-id="fa9e0-187">Basic test of app endpoints</span></span>

<span data-ttu-id="fa9e0-188">`BasicTests`다음 테스트 클래스인는 `WebApplicationFactory` 를 사용 하 여 sut를 부트스트랩 하 고 테스트 메서드 ( `Get_EndpointsReturnSuccessAndCorrectContentType`)에 [httpclient](/dotnet/api/system.net.http.httpclient) 를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-188">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="fa9e0-189">메서드는 응답 상태 코드 (200-299 범위의 상태 코드)가 성공 했는지 확인 하 고 헤더는 `Content-Type` 여러 앱 페이지에 대 한 것 `text/html; charset=utf-8` 입니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-189">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="fa9e0-190">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) 는 리디렉션을 자동으로 `HttpClient` 따르며 쿠키를 처리 하는의 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-190">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="fa9e0-191">기본적으로 [Gdpr 동의 정책을](xref:security/gdpr) 사용 하는 경우 요청에서 필수가 아닌 쿠키가 유지 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-191">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="fa9e0-192">TempData 공급자가 사용 하는 쿠키와 같이 필수가 아닌 쿠키를 유지 하려면 테스트에서 필수로 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-192">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="fa9e0-193">쿠키를 필수로 표시 하는 방법에 대 한 지침은 [필수 쿠키](xref:security/gdpr#essential-cookies)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-193">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

### <a name="test-a-secure-endpoint"></a><span data-ttu-id="fa9e0-194">보안 끝점 테스트</span><span class="sxs-lookup"><span data-stu-id="fa9e0-194">Test a secure endpoint</span></span>

<span data-ttu-id="fa9e0-195">`BasicTests` 클래스의 다른 테스트에서는 보안 끝점이 인증 되지 않은 사용자를 앱의 로그인 페이지로 리디렉션하는 지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-195">Another test in the `BasicTests` class checks that a secure endpoint redirects an unauthenticated user to the app's Login page.</span></span>

<span data-ttu-id="fa9e0-196">Sut에서 페이지는 `/SecurePage` [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) 규칙을 사용 하 여 페이지에 [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) 를 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-196">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="fa9e0-197">자세한 내용은 [Razor Pages 권한 부여 규칙](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-197">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="fa9e0-198">테스트에서 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) 은 [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) 를로 `false`설정 하 여 리디렉션을 허용 하지 않도록 설정 됩니다. `Get_SecurePageRequiresAnAuthenticatedUser`</span><span class="sxs-lookup"><span data-stu-id="fa9e0-198">In the `Get_SecurePageRequiresAnAuthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet2)]

<span data-ttu-id="fa9e0-199">클라이언트에서 리디렉션을 따르도록 허용 하지 않으면 다음 검사를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-199">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="fa9e0-200">SUT에서 반환 된 상태 코드는 HttpStatusCode 결과에 대해 확인할 수 있습니다 [.](/dotnet/api/system.net.httpstatuscode) 이는 로그인 페이지에 대 한 리디렉션 이후의 최종 상태 코드는 [HttpStatusCode](/dotnet/api/system.net.httpstatuscode)입니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-200">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="fa9e0-201">응답 헤더의 `http://localhost/Identity/Account/Login` `Location` 헤더 값은 헤더가 존재 하지 않는 최종 로그인 페이지 응답이 아니라로 시작 하는지 확인 하기 위해 확인 됩니다. `Location`</span><span class="sxs-lookup"><span data-stu-id="fa9e0-201">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="fa9e0-202">에 대 `WebApplicationFactoryClientOptions`한 자세한 내용은 [클라이언트 옵션](#client-options) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-202">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="fa9e0-203">WebApplicationFactory 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="fa9e0-203">Customize WebApplicationFactory</span></span>

<span data-ttu-id="fa9e0-204">웹 호스트 구성은에서 `WebApplicationFactory` 를 상속 하 여 하나 이상의 사용자 지정 팩터리를 만들기 때문에 테스트 클래스와 독립적으로 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-204">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="fa9e0-205">에서 `WebApplicationFactory` 상속 하 고 [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)를 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-205">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="fa9e0-206">[Iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) 를 사용 하면 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices)를 사용 하 여 서비스 컬렉션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-206">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="fa9e0-207">[샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) 에서 데이터베이스 시드는 `InitializeDbForTests` 메서드를 통해 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-207">Database seeding in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="fa9e0-208">메서드는 [통합 테스트 샘플에 설명 되어 있습니다. 앱 구성](#test-app-organization) 섹션을 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-208">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

2. <span data-ttu-id="fa9e0-209">테스트 클래스에서 `CustomWebApplicationFactory` 사용자 지정을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-209">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="fa9e0-210">다음 예제에서는 `IndexPageTests` 클래스에서 팩터리를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-210">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="fa9e0-211">샘플 앱의 클라이언트는 다음 리디렉션이 발생 하지 않도록 `HttpClient` 구성 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-211">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="fa9e0-212">[보안 끝점 테스트](#test-a-secure-endpoint) 섹션에서 설명한 대로이를 통해 테스트에서 앱의 첫 번째 응답의 결과를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-212">As explained in the [Test a secure endpoint](#test-a-secure-endpoint) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="fa9e0-213">첫 번째 응답은 `Location` 헤더를 사용 하는 이러한 테스트 대부분의 리디렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-213">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="fa9e0-214">일반적인 테스트는 `HttpClient` 및 도우미 메서드를 사용 하 여 요청 및 응답을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-214">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="fa9e0-215">SUT에 대 한 POST 요청은 앱의 [데이터 보호 방지 시스템이](xref:security/data-protection/introduction)자동으로 수행 하는 위조 방지 검사를 충족 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-215">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="fa9e0-216">테스트의 POST 요청을 정렬 하기 위해 테스트 앱은 다음을 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-216">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="fa9e0-217">페이지에 대 한 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-217">Make a request for the page.</span></span>
1. <span data-ttu-id="fa9e0-218">위조 방지 쿠키를 구문 분석 하 고 응답에서 유효성 검사 토큰을 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-218">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="fa9e0-219">위조 방지 쿠키 및 요청 유효성 검사 토큰을 사용 하 여 POST 요청을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-219">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="fa9e0-220">`SendAsync`도우미 확장 메서드 (*도우미/httpclientextensions .cs*)와 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)의 `GetDocumentAsync`도우미 메서드 (*도우미/htmlhelpers.cs*)는 [AngleSharp](https://anglesharp.github.io/) 파서를 사용 하 여 위조 방지를 처리 합니다. 다음 방법으로 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-220">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="fa9e0-221">`GetDocumentAsync` &ndash; [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage)를 수신하고 `IHtmlDocument`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-221">`GetDocumentAsync` &ndash; Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="fa9e0-222">`GetDocumentAsync`원본`HttpResponseMessage`에 기반 하 여 *가상 응답* 을 준비 하는 팩터리를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-222">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="fa9e0-223">자세한 내용은 [AngleSharp 설명서](https://github.com/AngleSharp/AngleSharp#documentation)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-223">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="fa9e0-224">`SendAsync``HttpClient` [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) 및 [SendAsync (HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) 를 작성 하는에 대 한 확장 메서드로 sut에 요청을 제출 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-224">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="fa9e0-225">에 대 `SendAsync` 한 오버 로드는 HTML`IHtmlFormElement`폼 () 및 다음을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-225">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="fa9e0-226">양식의 제출 단추 (`IHtmlElement`)</span><span class="sxs-lookup"><span data-stu-id="fa9e0-226">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="fa9e0-227">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="fa9e0-227">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="fa9e0-228">전송 단추 (`IHtmlElement`) 및 폼 값 (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="fa9e0-228">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="fa9e0-229">[AngleSharp](https://anglesharp.github.io/) 는이 항목 및 샘플 앱에서 데모용으로 사용 되는 타사 구문 분석 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-229">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="fa9e0-230">AngleSharp는 ASP.NET Core 앱의 통합 테스트에 지원 되지 않거나 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-230">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="fa9e0-231">[HAP (Html 민첩성 팩)](https://html-agility-pack.net/)와 같은 다른 파서를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-231">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="fa9e0-232">또 다른 방법은 위조 방지 시스템의 요청 확인 토큰을 처리 하 고 위조 쿠키를 직접 방지 하는 코드를 작성 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-232">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="fa9e0-233">WithWebHostBuilder를 사용 하 여 클라이언트 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="fa9e0-233">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="fa9e0-234">테스트 메서드 내에서 추가 구성이 필요한 경우 [withwebhostbuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) 는 구성으로 추가로 사용자 `WebApplicationFactory` 지정 되는 [iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) 를 사용 하 여 새를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-234">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="fa9e0-235">`Post_DeleteMessageHandler_ReturnsRedirectToRoot` [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) 의 테스트 메서드는를 사용 하는 `WithWebHostBuilder`방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-235">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="fa9e0-236">이 테스트는 SUT에서 양식 제출을 트리거하여 데이터베이스에서 레코드 삭제를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-236">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="fa9e0-237">`IndexPageTests` 클래스의 다른 테스트는 데이터베이스의 모든 레코드를 삭제 하는 작업을 수행 하 고 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 메서드 이전에 실행 될 수 있기 때문에이 테스트 메서드에서는 sut가 삭제할 레코드가 있는지 확인 하기 위해 데이터베이스를 시드 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-237">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is seeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="fa9e0-238">Sut에서 `messages` 폼의 단추를선택하는것은sut에대한요청에서시뮬레이션됩니다.`deleteBtn1`</span><span class="sxs-lookup"><span data-stu-id="fa9e0-238">Selecting the `deleteBtn1` button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="fa9e0-239">클라이언트 옵션</span><span class="sxs-lookup"><span data-stu-id="fa9e0-239">Client options</span></span>

<span data-ttu-id="fa9e0-240">다음 표에서는 `HttpClient` 인스턴스를 만들 때 사용할 수 있는 기본 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions)을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-240">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="fa9e0-241">옵션</span><span class="sxs-lookup"><span data-stu-id="fa9e0-241">Option</span></span> | <span data-ttu-id="fa9e0-242">설명</span><span class="sxs-lookup"><span data-stu-id="fa9e0-242">Description</span></span> | <span data-ttu-id="fa9e0-243">기본값</span><span class="sxs-lookup"><span data-stu-id="fa9e0-243">Default</span></span> |
| ------ | ----------- | ------- |
| [<span data-ttu-id="fa9e0-244">AllowAutoRedirect</span><span class="sxs-lookup"><span data-stu-id="fa9e0-244">AllowAutoRedirect</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | <span data-ttu-id="fa9e0-245">`HttpClient` 인스턴스가 자동으로 리디렉션 응답을 따르는지 여부를 가져오거나 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-245">Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span> | `true` |
| [<span data-ttu-id="fa9e0-246">BaseAddress</span><span class="sxs-lookup"><span data-stu-id="fa9e0-246">BaseAddress</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | <span data-ttu-id="fa9e0-247">`HttpClient` 인스턴스의 기본 주소를 가져오거나 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-247">Gets or sets the base address of `HttpClient` instances.</span></span> | `http://localhost` |
| [<span data-ttu-id="fa9e0-248">HandleCookies</span><span class="sxs-lookup"><span data-stu-id="fa9e0-248">HandleCookies</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | <span data-ttu-id="fa9e0-249">인스턴스에서 쿠키를 처리할지 `HttpClient` 여부를 가져오거나 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-249">Gets or sets whether `HttpClient` instances should handle cookies.</span></span> | `true` |
| [<span data-ttu-id="fa9e0-250">MaxAutomaticRedirections</span><span class="sxs-lookup"><span data-stu-id="fa9e0-250">MaxAutomaticRedirections</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | <span data-ttu-id="fa9e0-251">인스턴스가 따라야 하는 `HttpClient` 최대 리디렉션 응답 수를 가져오거나 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-251">Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> | <span data-ttu-id="fa9e0-252">7</span><span class="sxs-lookup"><span data-stu-id="fa9e0-252">7</span></span> |

<span data-ttu-id="fa9e0-253">`WebApplicationFactoryClientOptions` 클래스를 만들고 [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) 메서드에 전달 합니다 (기본값은 코드 예제에 표시 됨).</span><span class="sxs-lookup"><span data-stu-id="fa9e0-253">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="fa9e0-254">모의 서비스 삽입</span><span class="sxs-lookup"><span data-stu-id="fa9e0-254">Inject mock services</span></span>

<span data-ttu-id="fa9e0-255">호스트 작성기에서 [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) 에 대 한 호출을 사용 하 여 테스트에서 서비스를 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-255">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="fa9e0-256">**모의 서비스를 삽입 하려면 sut에 `Startup` `Startup.ConfigureServices` 메서드를 포함 하는 클래스가 있어야 합니다.**</span><span class="sxs-lookup"><span data-stu-id="fa9e0-256">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="fa9e0-257">샘플 SUT에는 견적을 반환 하는 범위 지정 서비스가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-257">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="fa9e0-258">인덱스 페이지를 요청 하면 따옴표가 인덱스 페이지의 숨겨진 필드에 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-258">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="fa9e0-259">*Services/IQuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="fa9e0-259">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="fa9e0-260">*Services/QuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="fa9e0-260">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="fa9e0-261">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="fa9e0-261">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="fa9e0-262">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="fa9e0-262">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="fa9e0-263">*Pages/Index .cs*:</span><span class="sxs-lookup"><span data-stu-id="fa9e0-263">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="fa9e0-264">다음 태그는 SUT 앱이 실행 될 때 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-264">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="fa9e0-265">통합 테스트에서 서비스 및 견적 주입을 테스트 하기 위해 테스트를 통해 모의 서비스를 SUT에 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-265">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="fa9e0-266">모의 서비스는 응용 프로그램 `QuoteService` `TestQuoteService`을 테스트 앱에서 제공 하는 서비스로 대체 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-266">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="fa9e0-267">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="fa9e0-267">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="fa9e0-268">`ConfigureTestServices`가 호출 되 고 범위가 지정 된 서비스가 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-268">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="fa9e0-269">테스트 실행 중에 생성 된 태그는에서 `TestQuoteService`제공 하는 따옴표 텍스트를 반영 하므로 어설션이 통과 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-269">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="fa9e0-270">테스트 인프라가 앱 콘텐츠 루트 경로를 유추 하는 방법</span><span class="sxs-lookup"><span data-stu-id="fa9e0-270">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="fa9e0-271">`WebApplicationFactory`생성자는 `TEntryPoint`어셈블리`System.Reflection.Assembly.FullName` 와 동일한 키를 사용하여 통합테스트를 포함하는 어셈블리에서[WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute)를 검색하여 앱 콘텐츠 루트 경로를 유추합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-271">The `WebApplicationFactory` constructor infers the app content root path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="fa9e0-272">올바른 키를 가진 특성을 찾을 수 `WebApplicationFactory` 없는 경우에서 솔루션 파일 ( *\*.sln*)을 검색 하 고 `TEntryPoint` 어셈블리 이름을 솔루션 디렉터리에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-272">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*\*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="fa9e0-273">응용 프로그램 루트 디렉터리 (콘텐츠 루트 경로)는 뷰와 콘텐츠 파일을 검색 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-273">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="fa9e0-274">섀도 복사 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="fa9e0-274">Disable shadow copying</span></span>

<span data-ttu-id="fa9e0-275">섀도 복사를 수행 하면 테스트가 출력 디렉터리와 다른 디렉터리에 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-275">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="fa9e0-276">테스트가 제대로 작동 하려면 섀도 복사를 사용 하지 않도록 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-276">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="fa9e0-277">[샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) 은 xunit을 사용 하 고 올바른 구성 설정을 사용 하 여 xunit *. runner* 파일을 포함 하 여 xunit에 대해 섀도 복사를 사용 하지 않도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-277">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="fa9e0-278">자세한 내용은 [JSON을 사용 하 여 xUnit 구성](https://xunit.github.io/docs/configuring-with-json.html)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-278">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="fa9e0-279">다음 콘텐츠를 사용 하 여 테스트 프로젝트의 루트에 *xunit.*</span><span class="sxs-lookup"><span data-stu-id="fa9e0-279">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

<span data-ttu-id="fa9e0-280">Visual Studio를 사용 하는 경우 파일의 **출력 디렉터리로 복사** 속성을 **항상 복사**로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-280">If using Visual Studio, set the file's **Copy to Output Directory** property to **Copy always**.</span></span> <span data-ttu-id="fa9e0-281">Visual Studio를 사용 하지 않는 경우 테스트 `Content` 앱의 프로젝트 파일에 대상을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-281">If not using Visual Studio, add a `Content` target to the test app's project file:</span></span>

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a><span data-ttu-id="fa9e0-282">개체 삭제</span><span class="sxs-lookup"><span data-stu-id="fa9e0-282">Disposal of objects</span></span>

<span data-ttu-id="fa9e0-283">`IClassFixture` 구현에 대 한 테스트를 실행 한 후 xunit에서 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)을 삭제 하면 [testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver) 및 [httpclient](/dotnet/api/system.net.http.httpclient) 가 삭제 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-283">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="fa9e0-284">개발자가 인스턴스화한 개체를 삭제 해야 하는 경우에는 `IClassFixture` 구현에서 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-284">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="fa9e0-285">자세한 내용은 [Dispose 메서드 구현](/dotnet/standard/garbage-collection/implementing-dispose)합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-285">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="fa9e0-286">통합 테스트 샘플</span><span class="sxs-lookup"><span data-stu-id="fa9e0-286">Integration tests sample</span></span>

<span data-ttu-id="fa9e0-287">[샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) 은 다음과 같은 두 개의 앱으로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-287">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="fa9e0-288">앱</span><span class="sxs-lookup"><span data-stu-id="fa9e0-288">App</span></span> | <span data-ttu-id="fa9e0-289">프로젝트 디렉터리</span><span class="sxs-lookup"><span data-stu-id="fa9e0-289">Project directory</span></span> | <span data-ttu-id="fa9e0-290">Description</span><span class="sxs-lookup"><span data-stu-id="fa9e0-290">Description</span></span> |
| --- | ----------------- | ----------- |
| <span data-ttu-id="fa9e0-291">메시지 앱 (SUT)</span><span class="sxs-lookup"><span data-stu-id="fa9e0-291">Message app (the SUT)</span></span> | <span data-ttu-id="fa9e0-292">*src/RazorPagesProject*</span><span class="sxs-lookup"><span data-stu-id="fa9e0-292">*src/RazorPagesProject*</span></span> | <span data-ttu-id="fa9e0-293">사용자가 메시지를 추가, 삭제, 삭제 및 분석할 수 있도록 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-293">Allows a user to add, delete one, delete all, and analyze messages.</span></span> |
| <span data-ttu-id="fa9e0-294">응용 프로그램 테스트</span><span class="sxs-lookup"><span data-stu-id="fa9e0-294">Test app</span></span> | <span data-ttu-id="fa9e0-295">*tests/RazorPagesProject.Tests*</span><span class="sxs-lookup"><span data-stu-id="fa9e0-295">*tests/RazorPagesProject.Tests*</span></span> | <span data-ttu-id="fa9e0-296">SUT 테스트를 통합 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-296">Used to integration test the SUT.</span></span> |

<span data-ttu-id="fa9e0-297">[Visual Studio](https://visualstudio.microsoft.com)와 같은 IDE의 기본 제공 테스트 기능을 사용 하 여 테스트를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-297">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="fa9e0-298">[Visual Studio Code](https://code.visualstudio.com/) 또는 명령줄을 사용 하는 경우 *테스트/RazorPagesProject. 테스트* 디렉터리의 명령 프롬프트에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-298">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```console
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="fa9e0-299">메시지 앱 (SUT) 조직</span><span class="sxs-lookup"><span data-stu-id="fa9e0-299">Message app (SUT) organization</span></span>

<span data-ttu-id="fa9e0-300">SUT는 다음과 같은 특징을 가진 Razor Pages 메시지 시스템입니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-300">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="fa9e0-301">응용 프로그램의 인덱스 페이지 (*Pages/index. cshtml* 및 *pages/인덱스인*)는 메시지의 추가, 삭제 및 분석 (메시지당 평균 단어)을 제어 하는 UI 및 페이지 모델 메서드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-301">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="fa9e0-302">메시지는 두 속성 `Message` `Id` (키) 및 `Text` (메시지)를 사용 하 여 클래스 (*데이터/메시지 .cs*)에서 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-302">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="fa9e0-303">`Text` 속성은 필수 이며 200 자로 제한 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-303">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="fa9e0-304">메시지는 [Entity Framework의 메모리 내 데이터베이스](/ef/core/providers/in-memory/)&#8224;를 사용 하 여 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-304">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="fa9e0-305">앱에는 데이터베이스 컨텍스트 클래스 (*data/AppDbContext*)에 DAL (데이터 액세스 `AppDbContext` 계층)이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-305">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="fa9e0-306">응용 프로그램이 시작 될 때 데이터베이스가 비어 있는 경우 메시지 저장소는 세 개의 메시지로 초기화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-306">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="fa9e0-307">앱에는 인증 `/SecurePage` 된 사용자만 액세스할 수 있는가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-307">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="fa9e0-308">&#8224;EF 항목인 [InMemory 테스트](/ef/core/miscellaneous/testing/in-memory)는 MSTest를 사용 하 여 테스트에 메모리 내 데이터베이스를 사용 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-308">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="fa9e0-309">이 항목에서는 [Xunit](https://xunit.github.io/) 테스트 프레임 워크를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-309">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="fa9e0-310">여러 테스트 프레임 워크에서 테스트 개념 및 테스트 구현은 비슷하지만 동일 하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-310">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="fa9e0-311">앱은 리포지토리 패턴을 사용 하지 않으며 [UoW (Unit Of Work) 패턴](https://martinfowler.com/eaaCatalog/unitOfWork.html)의 효과적인 예는 아니지만 Razor Pages 이러한 개발 패턴을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-311">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="fa9e0-312">자세한 내용은 [인프라 지 속성 계층](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) 및 [테스트 컨트롤러 논리](/aspnet/core/mvc/controllers/testing) 디자인 (샘플에서는 리포지토리 패턴 구현)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-312">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="fa9e0-313">앱 구성 테스트</span><span class="sxs-lookup"><span data-stu-id="fa9e0-313">Test app organization</span></span>

<span data-ttu-id="fa9e0-314">테스트 앱은 테스트 */Razor 프로젝트* 의 콘솔 앱입니다. 테스트 디렉터리입니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-314">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="fa9e0-315">응용 프로그램 디렉터리 테스트</span><span class="sxs-lookup"><span data-stu-id="fa9e0-315">Test app directory</span></span> | <span data-ttu-id="fa9e0-316">Description</span><span class="sxs-lookup"><span data-stu-id="fa9e0-316">Description</span></span> |
| ------------------ | ----------- |
| <span data-ttu-id="fa9e0-317">*BasicTests*</span><span class="sxs-lookup"><span data-stu-id="fa9e0-317">*BasicTests*</span></span> | <span data-ttu-id="fa9e0-318">*BasicTests.cs* 에는 라우팅을 위한 테스트 메서드, 인증 되지 않은 사용자가 보안 페이지에 액세스 하는 방법, GitHub 사용자 프로필 가져오기 및 프로필의 사용자 로그인 확인이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-318">*BasicTests.cs* contains test methods for routing, accessing a secure page by an unauthenticated user, and obtaining a GitHub user profile and checking the profile's user login.</span></span> |
| <span data-ttu-id="fa9e0-319">*IntegrationTests*</span><span class="sxs-lookup"><span data-stu-id="fa9e0-319">*IntegrationTests*</span></span> | <span data-ttu-id="fa9e0-320">*IndexPageTests.cs* 에는 사용자 지정 `WebApplicationFactory` 클래스를 사용 하는 인덱스 페이지에 대 한 통합 테스트가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-320">*IndexPageTests.cs* contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> |
| <span data-ttu-id="fa9e0-321">*도우미/유틸리티*</span><span class="sxs-lookup"><span data-stu-id="fa9e0-321">*Helpers/Utilities*</span></span> | <ul><li><span data-ttu-id="fa9e0-322">*Utilities.cs* 에는 `InitializeDbForTests` 테스트 데이터로 데이터베이스를 시드 하는 데 사용 되는 메서드가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-322">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="fa9e0-323">*HtmlHelpers.cs* 는 테스트 메서드에서 사용할 AngleSharp `IHtmlDocument` 을 반환 하는 메서드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-323">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="fa9e0-324">*HttpClientExtensions.cs* 는에 요청 `SendAsync` 을 전송 하는에 대 한 오버 로드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-324">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="fa9e0-325">테스트 프레임 워크는 [Xunit](https://xunit.github.io/)입니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-325">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="fa9e0-326">[AspNetCore. TestHost](/dotnet/api/microsoft.aspnetcore.testhost)( [testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver)포함)를 사용 하 여 통합 테스트를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-326">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="fa9e0-327">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 패키지는 테스트 호스트와 테스트 서버 `TestHost` 를 구성 하는 데 사용 되므로 및 `TestServer` 패키지에는 테스트 앱의 프로젝트 파일 또는 개발자에 게 직접 패키지 참조가 필요 하지 않습니다. 테스트 응용 프로그램의 구성입니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-327">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="fa9e0-328">**테스트를 위해 데이터베이스 시드**</span><span class="sxs-lookup"><span data-stu-id="fa9e0-328">**Seeding the database for testing**</span></span>

<span data-ttu-id="fa9e0-329">통합 테스트는 일반적으로 테스트 실행 전에 데이터베이스에 작은 데이터 집합이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-329">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="fa9e0-330">예를 들어 delete 테스트는 데이터베이스 레코드 삭제를 호출 하므로 삭제 요청이 성공 하려면 데이터베이스에 적어도 하나 이상의 레코드가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-330">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="fa9e0-331">샘플 앱은 테스트가 실행 될 때 사용할 수 있는 *Utilities.cs* 의 세 가지 메시지를 사용 하 여 데이터베이스를 시드 합니다.</span><span class="sxs-lookup"><span data-stu-id="fa9e0-331">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

## <a name="additional-resources"></a><span data-ttu-id="fa9e0-332">추가 자료</span><span class="sxs-lookup"><span data-stu-id="fa9e0-332">Additional resources</span></span>

* [<span data-ttu-id="fa9e0-333">단위 테스트</span><span class="sxs-lookup"><span data-stu-id="fa9e0-333">Unit tests</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* [<span data-ttu-id="fa9e0-334">Razor 페이지 단위 테스트</span><span class="sxs-lookup"><span data-stu-id="fa9e0-334">Razor Pages unit tests</span></span>](xref:test/razor-pages-tests)
* [<span data-ttu-id="fa9e0-335">미들웨어</span><span class="sxs-lookup"><span data-stu-id="fa9e0-335">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="fa9e0-336">테스트 컨트롤러</span><span class="sxs-lookup"><span data-stu-id="fa9e0-336">Test controllers</span></span>](xref:mvc/controllers/testing)
