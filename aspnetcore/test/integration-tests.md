---
title: ASP.NET Core의 통합 테스트
author: guardrex
description: 통합 테스트를 사용하여 앱의 구성 요소가 데이터베이스, 파일 시스템 및 네트워크를 비롯한 인프라 수준에서 제대로 작동하는지 확인하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/14/2019
uid: test/integration-tests
ms.openlocfilehash: 863b95230d376d050c34a9ed585b7696e649cb05
ms.sourcegitcommit: dd026eceee79e943bd6b4a37b144803b50617583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72378712"
---
# <a name="integration-tests-in-aspnet-core"></a><span data-ttu-id="4aef2-103">ASP.NET Core의 통합 테스트</span><span class="sxs-lookup"><span data-stu-id="4aef2-103">Integration tests in ASP.NET Core</span></span>

<span data-ttu-id="4aef2-104">By [Luke Latham 문자](https://github.com/guardrex) 및 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="4aef2-104">By [Luke Latham](https://github.com/guardrex) and [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4aef2-105">통합 테스트는 응용 프로그램의 구성 요소가 응용 프로그램의 지원 인프라 (예: 데이터베이스, 파일 시스템 및 네트워크)를 포함 하는 수준에서 올바르게 작동 하는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-105">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="4aef2-106">ASP.NET Core는 테스트 웹 호스트 및 메모리 내 테스트 서버에서 단위 테스트 프레임 워크를 사용 하는 통합 테스트를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-106">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="4aef2-107">이 항목에서는 단위 테스트에 대해 기본적으로 이해 하 고 있다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-107">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="4aef2-108">테스트 개념을 잘 모르는 경우 [.Net Core의 단위 테스트 및 .NET Standard](/dotnet/core/testing/) 항목 및 연결 된 콘텐츠를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="4aef2-108">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="4aef2-109">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4aef2-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="4aef2-110">샘플 앱은 Razor Pages 앱 이며 Razor Pages를 기본적으로 이해 하 고 있다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-110">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="4aef2-111">Razor Pages에 익숙하지 않은 경우 다음 항목을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="4aef2-111">If unfamiliar with Razor Pages, see the following topics:</span></span>

* [<span data-ttu-id="4aef2-112">Razor 페이지 소개</span><span class="sxs-lookup"><span data-stu-id="4aef2-112">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="4aef2-113">Razor 페이지 시작</span><span class="sxs-lookup"><span data-stu-id="4aef2-113">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="4aef2-114">Razor 페이지 단위 테스트</span><span class="sxs-lookup"><span data-stu-id="4aef2-114">Razor Pages unit tests</span></span>](xref:test/razor-pages-tests)

> [!NOTE]
> <span data-ttu-id="4aef2-115">SPAs 테스트를 위해 브라우저를 자동화할 수 있는 [Selenium](https://www.seleniumhq.org/)와 같은 도구를 권장 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-115">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="4aef2-116">통합 테스트 소개</span><span class="sxs-lookup"><span data-stu-id="4aef2-116">Introduction to integration tests</span></span>

<span data-ttu-id="4aef2-117">통합 테스트는 [단위 테스트](/dotnet/core/testing/)보다 더 광범위 한 수준에서 앱의 구성 요소를 평가 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-117">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="4aef2-118">단위 테스트는 개별 클래스 메서드와 같은 격리 된 소프트웨어 구성 요소를 테스트 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-118">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="4aef2-119">통합 테스트는 두 개 이상의 앱 구성 요소가 함께 작동 하 여 요청을 완전히 처리 하는 데 필요한 모든 구성 요소를 포함 하 여 예상 결과를 생성 하는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-119">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="4aef2-120">이러한 광범위 한 테스트는 다음과 같은 구성 요소를 포함 하 여 앱의 인프라 및 전체 프레임 워크를 테스트 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-120">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="4aef2-121">데이터베이스</span><span class="sxs-lookup"><span data-stu-id="4aef2-121">Database</span></span>
* <span data-ttu-id="4aef2-122">파일 시스템</span><span class="sxs-lookup"><span data-stu-id="4aef2-122">File system</span></span>
* <span data-ttu-id="4aef2-123">네트워크 어플라이언스</span><span class="sxs-lookup"><span data-stu-id="4aef2-123">Network appliances</span></span>
* <span data-ttu-id="4aef2-124">요청-응답 파이프라인</span><span class="sxs-lookup"><span data-stu-id="4aef2-124">Request-response pipeline</span></span>

<span data-ttu-id="4aef2-125">단위 테스트는 인프라 구성 요소 대신 *fakes* 또는 *모의 개체*라고 하는 구성 된 구성 요소를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-125">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="4aef2-126">단위 테스트와 달리 통합 테스트는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-126">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="4aef2-127">프로덕션 환경에서 앱이 사용 하는 실제 구성 요소를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-127">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="4aef2-128">더 많은 코드와 데이터 처리가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-128">Require more code and data processing.</span></span>
* <span data-ttu-id="4aef2-129">실행 하는 데 시간이 더 오래 걸립니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-129">Take longer to run.</span></span>

<span data-ttu-id="4aef2-130">따라서 가장 중요 한 인프라 시나리오에 대 한 통합 테스트 사용을 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-130">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="4aef2-131">단위 테스트 또는 통합 테스트를 사용 하 여 동작을 테스트할 수 있는 경우 단위 테스트를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-131">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="4aef2-132">데이터베이스 및 파일 시스템을 사용 하 여 모든 가능한 데이터 순열 및 파일 액세스에 대해 통합 테스트를 작성 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="4aef2-132">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="4aef2-133">응용 프로그램 전체에서 데이터베이스 및 파일 시스템과 상호 작용 하는 위치에 관계 없이, 일반적으로 데이터베이스 및 파일 시스템 구성 요소를 적절 하 게 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-133">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="4aef2-134">이러한 구성 요소와 상호 작용 하는 메서드 논리의 루틴 테스트에 단위 테스트를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-134">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="4aef2-135">단위 테스트에서 인프라 fakes/mock를 사용 하면 테스트 실행 속도가 빨라집니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-135">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="4aef2-136">통합 테스트에 대 한 토론에서 테스트 된 프로젝트를 종종 *테스트 중인 시스템*또는 short의 경우 "sut" 라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-136">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="4aef2-137">*이 항목 전체에서 "SUT"를 사용 하 여 테스트 된 ASP.NET Core 앱을 참조 합니다.*</span><span class="sxs-lookup"><span data-stu-id="4aef2-137">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="4aef2-138">ASP.NET Core 통합 테스트</span><span class="sxs-lookup"><span data-stu-id="4aef2-138">ASP.NET Core integration tests</span></span>

<span data-ttu-id="4aef2-139">ASP.NET Core의 통합 테스트에는 다음이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-139">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="4aef2-140">테스트 프로젝트는 테스트를 포함 하 고 실행 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-140">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="4aef2-141">테스트 프로젝트에는 SUT에 대 한 참조가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-141">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="4aef2-142">테스트 프로젝트는 SUT에 대 한 테스트 웹 호스트를 만들고 테스트 서버 클라이언트를 사용 하 여 SUT를 사용 하 여 요청 및 응답을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-142">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="4aef2-143">Test runner는 테스트를 실행 하 고 테스트 결과를 보고 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-143">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="4aef2-144">통합 테스트는 일반적인 *정렬*, 작업 및 *어설션* 테스트 *단계를 포함*하는 이벤트 시퀀스를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-144">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="4aef2-145">SUT의 웹 호스트가 구성 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-145">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="4aef2-146">앱에 요청을 제출 하는 테스트 서버 클라이언트가 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-146">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="4aef2-147">테스트 응용 프로그램에서 요청을 준비 하는 *정렬* 테스트 단계를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-147">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="4aef2-148">작업 *테스트 단계가* 실행 됩니다. 즉, 클라이언트가 요청을 제출 하 고 응답을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-148">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="4aef2-149">*Assert* 테스트 단계가 실행 됩니다. *실제* 응답은 *예상* 된 응답에 따라 *통과* 또는 *실패* 의 유효성을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-149">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="4aef2-150">모든 테스트가 실행 될 때까지 프로세스가 계속 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-150">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="4aef2-151">테스트 결과가 보고 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-151">The test results are reported.</span></span>

<span data-ttu-id="4aef2-152">일반적으로 테스트 웹 호스트는 테스트 실행을 위해 응용 프로그램의 일반 웹 호스트와 다르게 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-152">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="4aef2-153">예를 들어 다른 데이터베이스 또는 다른 앱 설정을 테스트에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-153">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="4aef2-154">테스트 웹 호스트 및 메모리 내 테스트 서버 ([Testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver))와 같은 인프라 구성 요소는 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 패키지에서 제공 하거나 관리 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-154">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="4aef2-155">이 패키지를 사용 하면 테스트 생성 및 실행이 간소화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-155">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="4aef2-156">@No__t-0 패키지는 다음 작업을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-156">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="4aef2-157">SUT의 종속성 파일 (*. deps*)을 테스트 프로젝트의 *bin* 디렉터리에 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-157">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="4aef2-158">테스트를 실행할 때 정적 파일 및 페이지/뷰가 검색 되도록 [콘텐츠 루트](xref:fundamentals/index#content-root) 를 sut의 프로젝트 루트로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-158">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="4aef2-159">@No__t-1을 사용 하 여 SUT의 부트스트래핑을 간소화 하는 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) 클래스를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-159">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="4aef2-160">[단위 테스트](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) 설명서에서는 테스트를 실행 하는 방법에 대 한 자세한 지침과 테스트 및 테스트 클래스의 이름을 설정 하는 방법에 대 한 자세한 지침과 함께 테스트 프로젝트 및 test runner를 설정 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-160">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="4aef2-161">앱에 대 한 테스트 프로젝트를 만들 때 통합 테스트에서 여러 프로젝트로 단위 테스트를 분리 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-161">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="4aef2-162">이를 통해 인프라 테스트 구성 요소가 실수로 단위 테스트에 포함 되지 않도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-162">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="4aef2-163">단위 및 통합 테스트를 분리 하 여 실행 되는 테스트 집합을 제어할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-163">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="4aef2-164">Razor Pages 앱과 MVC 앱의 테스트에 대 한 구성의 차이는 거의 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-164">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="4aef2-165">유일한 차이점은 테스트의 이름을 지정 하는 방법 뿐입니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-165">The only difference is in how the tests are named.</span></span> <span data-ttu-id="4aef2-166">Razor Pages 앱에서 페이지 끝점의 테스트는 일반적으로 페이지 모델 클래스의 이름을 지정 합니다 (예: 인덱스 페이지에 대 한 구성 요소 통합을 테스트 하기 위해-0 @no__t).</span><span class="sxs-lookup"><span data-stu-id="4aef2-166">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="4aef2-167">MVC 앱에서 테스트는 일반적으로 컨트롤러 클래스로 구성 되며 테스트 하는 컨트롤러 뒤에 이름이 지정 됩니다 (예: `HomeControllerTests`은 Home 컨트롤러에 대 한 구성 요소 통합 테스트).</span><span class="sxs-lookup"><span data-stu-id="4aef2-167">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="4aef2-168">응용 프로그램 테스트 필수 조건</span><span class="sxs-lookup"><span data-stu-id="4aef2-168">Test app prerequisites</span></span>

<span data-ttu-id="4aef2-169">테스트 프로젝트는 다음을 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-169">The test project must:</span></span>

* <span data-ttu-id="4aef2-170">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 패키지를 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-170">Reference the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span>
* <span data-ttu-id="4aef2-171">프로젝트 파일 (`<Project Sdk="Microsoft.NET.Sdk.Web">`)에서 웹 SDK를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-171">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span>

<span data-ttu-id="4aef2-172">이러한 필수 구성 요소는 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)에서 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-172">These prerequisites can be seen in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="4aef2-173">\*테스트/RazorPagesProject. 테스트/razor \* 프로젝트. .csproj 파일을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-173">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="4aef2-174">샘플 앱은 [Xunit](https://xunit.github.io/) 테스트 프레임 워크 및 [AngleSharp](https://anglesharp.github.io/) 파서 라이브러리를 사용 하므로 샘플 앱은 다음을 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-174">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="4aef2-175">xunit</span><span class="sxs-lookup"><span data-stu-id="4aef2-175">xunit</span></span>](https://www.nuget.org/packages/xunit)
* [<span data-ttu-id="4aef2-176">xunit. visualstudio</span><span class="sxs-lookup"><span data-stu-id="4aef2-176">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [<span data-ttu-id="4aef2-177">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="4aef2-177">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp)

<span data-ttu-id="4aef2-178">Entity Framework Core는 테스트에도 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-178">Entity Framework Core is also used in the tests.</span></span> <span data-ttu-id="4aef2-179">앱 참조:</span><span class="sxs-lookup"><span data-stu-id="4aef2-179">The app references:</span></span>

* [<span data-ttu-id="4aef2-180">AspNetCore. Microsoft.entityframeworkcore.tools.dotnet</span><span class="sxs-lookup"><span data-stu-id="4aef2-180">Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* [<span data-ttu-id="4aef2-181">AspNetCore. Microsoft.entityframeworkcore.tools.dotnet</span><span class="sxs-lookup"><span data-stu-id="4aef2-181">Microsoft.AspNetCore.Identity.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)
* [<span data-ttu-id="4aef2-182">Microsoft.entityframeworkcore.tools.dotnet</span><span class="sxs-lookup"><span data-stu-id="4aef2-182">Microsoft.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [<span data-ttu-id="4aef2-183">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="4aef2-183">Microsoft.EntityFrameworkCore.InMemory</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [<span data-ttu-id="4aef2-184">Microsoft.entityframeworkcore.tools.dotnet</span><span class="sxs-lookup"><span data-stu-id="4aef2-184">Microsoft.EntityFrameworkCore.Tools</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a><span data-ttu-id="4aef2-185">SUT 환경</span><span class="sxs-lookup"><span data-stu-id="4aef2-185">SUT environment</span></span>

<span data-ttu-id="4aef2-186">SUT의 [환경이](xref:fundamentals/environments) 설정 되지 않은 경우 환경의 기본값은 Development로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-186">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="4aef2-187">기본 WebApplicationFactory를 사용 하는 기본 테스트</span><span class="sxs-lookup"><span data-stu-id="4aef2-187">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="4aef2-188">[WebApplicationFactory @ no__t-1TEntryPoint >](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) 는 통합 테스트용 [testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver) 를 만드는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-188">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="4aef2-189">`TEntryPoint`은 SUT의 진입점 클래스입니다. 일반적으로 `Startup` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-189">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="4aef2-190">테스트 클래스는 클래스에 테스트를 포함 하 고 클래스의 테스트에서 공유 개체 인스턴스를 제공 하는 클래스 *fixture* 인터페이스 ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture))를 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-190">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

### <a name="basic-test-of-app-endpoints"></a><span data-ttu-id="4aef2-191">앱 끝점의 기본 테스트</span><span class="sxs-lookup"><span data-stu-id="4aef2-191">Basic test of app endpoints</span></span>

<span data-ttu-id="4aef2-192">다음 테스트 클래스인 `WebApplicationFactory`은 을 사용 하 여 SUT를 부트스트랩 하 고, `Get_EndpointsReturnSuccessAndCorrectContentType` 인 테스트 메서드에 [Httpclient](/dotnet/api/system.net.http.httpclient) 를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-192">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="4aef2-193">메서드는 응답 상태 코드가 성공 (200-299 범위의 상태 코드) 되었는지 확인 하 고 `Content-Type` 헤더는 여러 앱 페이지에 대해 `text/html; charset=utf-8`입니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-193">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="4aef2-194">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) 는 리디렉션을 자동으로 따르며 쿠키를 처리 하는 `HttpClient`의 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-194">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="4aef2-195">기본적으로 [Gdpr 동의 정책을](xref:security/gdpr) 사용 하는 경우 요청에서 필수가 아닌 쿠키가 유지 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-195">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="4aef2-196">TempData 공급자가 사용 하는 쿠키와 같이 필수가 아닌 쿠키를 유지 하려면 테스트에서 필수로 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-196">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="4aef2-197">쿠키를 필수로 표시 하는 방법에 대 한 지침은 [필수 쿠키](xref:security/gdpr#essential-cookies)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="4aef2-197">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

### <a name="test-a-secure-endpoint"></a><span data-ttu-id="4aef2-198">보안 끝점 테스트</span><span class="sxs-lookup"><span data-stu-id="4aef2-198">Test a secure endpoint</span></span>

<span data-ttu-id="4aef2-199">@No__t-0 클래스의 다른 테스트는 보안 끝점이 인증 되지 않은 사용자를 앱의 로그인 페이지로 리디렉션하는 지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-199">Another test in the `BasicTests` class checks that a secure endpoint redirects an unauthenticated user to the app's Login page.</span></span>

<span data-ttu-id="4aef2-200">SUT에서 `/SecurePage` 페이지는 [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) 규칙을 사용 하 여 페이지에 [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) 를 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-200">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="4aef2-201">자세한 내용은 [Razor Pages 권한 부여 규칙](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="4aef2-201">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="4aef2-202">@No__t-0 테스트에서는 [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) 를 `false`으로 설정 하 여 리디렉션을 허용 하지 않도록 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) 가 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-202">In the `Get_SecurePageRequiresAnAuthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet2)]

<span data-ttu-id="4aef2-203">클라이언트에서 리디렉션을 따르도록 허용 하지 않으면 다음 검사를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-203">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="4aef2-204">SUT에서 반환 된 상태 코드는 HttpStatusCode 결과에 대해 확인할 수 있습니다 [.](/dotnet/api/system.net.httpstatuscode) 이는 로그인 페이지에 대 한 리디렉션 이후의 최종 상태 코드는 [HttpStatusCode](/dotnet/api/system.net.httpstatuscode)입니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-204">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="4aef2-205">응답 헤더의 `Location` 헤더 값은 마지막 로그인 페이지 응답이 아닌 `http://localhost/Identity/Account/Login`로 시작 하는 것을 확인 하기 위해 확인 됩니다 .이 값은 `Location` 헤더가 존재 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-205">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="4aef2-206">@No__t-0에 대 한 자세한 내용은 [클라이언트 옵션](#client-options) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="4aef2-206">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="4aef2-207">WebApplicationFactory 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="4aef2-207">Customize WebApplicationFactory</span></span>

<span data-ttu-id="4aef2-208">@No__t-0에서 상속 하 여 하나 이상의 사용자 지정 팩터리를 만들도록 테스트 클래스와 별개로 웹 호스트 구성을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-208">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="4aef2-209">@No__t-0에서 상속 하 고 [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)를 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-209">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="4aef2-210">[Iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) 를 사용 하면 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices)를 사용 하 여 서비스 컬렉션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-210">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="4aef2-211">[샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) 에서의 데이터베이스 시드는 `InitializeDbForTests` 메서드로 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-211">Database seeding in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="4aef2-212">메서드는 [통합 테스트 샘플: 테스트 앱 구성](#test-app-organization) 섹션에 설명 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-212">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

   <span data-ttu-id="4aef2-213">SUT의 데이터베이스 컨텍스트가 `Startup.ConfigureServices` 메서드에 등록 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-213">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="4aef2-214">응용 프로그램의 `builder.ConfigureServices` 콜백은 응용 프로그램의 @no__t 코드를 실행 *한 후* 에 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-214">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="4aef2-215">앱 데이터베이스와 다른 테스트에 다른 데이터베이스를 사용 하려면 앱의 데이터베이스 컨텍스트를 `builder.ConfigureServices`으로 바꾸어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-215">To use a different database for the tests than the app's database, the app's database context must be replaced in `builder.ConfigureServices`.</span></span>

   <span data-ttu-id="4aef2-216">샘플 앱은 데이터베이스 컨텍스트에 대 한 서비스 설명자를 찾고 설명자를 사용 하 여 서비스 등록을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-216">The sample app finds the service descriptor for the database context and uses the descriptor to remove the service registration.</span></span> <span data-ttu-id="4aef2-217">그런 다음, 팩터리는 테스트를 위해 메모리 내 데이터베이스를 사용 하는 새 `ApplicationDbContext`을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-217">Next, the factory adds a new `ApplicationDbContext` that uses an in-memory database for the tests.</span></span>

   <span data-ttu-id="4aef2-218">메모리 내 데이터베이스가 아닌 다른 데이터베이스에 연결 하려면 `UseInMemoryDatabase` 호출을 변경 하 여 컨텍스트를 다른 데이터베이스에 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-218">To connect to a different database than the in-memory database, change the `UseInMemoryDatabase` call to connect the context to a different database.</span></span> <span data-ttu-id="4aef2-219">SQL Server 테스트 데이터베이스를 사용 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-219">To use a SQL Server test database:</span></span>

   * <span data-ttu-id="4aef2-220">프로젝트 파일에서 [Microsoft.entityframeworkcore.tools.dotnet] https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet 패키지를 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-220">Reference the [Microsoft.EntityFrameworkCore.SqlServer]https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet package in the project file.</span></span>
   * <span data-ttu-id="4aef2-221">데이터베이스에 대 한 연결 문자열을 사용 하 여 `UseSqlServer`을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-221">Call `UseSqlServer` with a connection string to the database.</span></span>

   ```csharp
   services.AddDbContext<ApplicationDbContext>((options, context) => 
   {
       context.UseSqlServer(
           Configuration.GetConnectionString("TestingDbConnectionString"));
   });
   ```

2. <span data-ttu-id="4aef2-222">테스트 클래스에서 사용자 지정 `CustomWebApplicationFactory`을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-222">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="4aef2-223">다음 예제에서는 `IndexPageTests` 클래스에서 팩터리를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-223">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="4aef2-224">샘플 앱의 클라이언트는 다음 리디렉션의 @no__t 0이 되지 않도록 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-224">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="4aef2-225">[보안 끝점 테스트](#test-a-secure-endpoint) 섹션에서 설명한 대로이를 통해 테스트에서 앱의 첫 번째 응답의 결과를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-225">As explained in the [Test a secure endpoint](#test-a-secure-endpoint) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="4aef2-226">첫 번째 응답은 `Location` 헤더를 사용 하는 이러한 테스트 대부분의 리디렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-226">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="4aef2-227">일반적인 테스트는 `HttpClient` 및 도우미 메서드를 사용 하 여 요청 및 응답을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-227">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="4aef2-228">SUT에 대 한 POST 요청은 앱의 [데이터 보호 방지 시스템이](xref:security/data-protection/introduction)자동으로 수행 하는 위조 방지 검사를 충족 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-228">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="4aef2-229">테스트의 POST 요청을 정렬 하기 위해 테스트 앱은 다음을 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-229">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="4aef2-230">페이지에 대 한 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-230">Make a request for the page.</span></span>
1. <span data-ttu-id="4aef2-231">위조 방지 쿠키를 구문 분석 하 고 응답에서 유효성 검사 토큰을 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-231">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="4aef2-232">위조 방지 쿠키 및 요청 유효성 검사 토큰을 사용 하 여 POST 요청을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-232">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="4aef2-233">[샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) 의 `SendAsync` 도우미 확장 메서드 (도우미 */httpclientextensions*) 및 @no__t 도우미 메서드 (helper */Htmlhelpers. cs*)는 [AngleSharp](https://anglesharp.github.io/) 파서를 사용 하 여 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-233">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="4aef2-234">`GetDocumentAsync` @no__t [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) 를 받고 `IHtmlDocument`을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-234">`GetDocumentAsync` &ndash; Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="4aef2-235">`GetDocumentAsync`은 원래 `HttpResponseMessage`에 따라 *가상 응답* 을 준비 하는 팩터리를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-235">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="4aef2-236">자세한 내용은 [AngleSharp 설명서](https://github.com/AngleSharp/AngleSharp#documentation)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="4aef2-236">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="4aef2-237">`HttpClient` @no__t에 대 한 확장 메서드는 [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) 을 작성 하 고 [SendAsync (HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) 를 호출 하 여 요청을 sut에 제출 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-237">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="4aef2-238">@No__t-0에 대 한 오버 로드는 HTML 폼 (`IHtmlFormElement`) 및 다음을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-238">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="4aef2-239">양식의 제출 단추 (`IHtmlElement`)</span><span class="sxs-lookup"><span data-stu-id="4aef2-239">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="4aef2-240">양식 값 컬렉션 (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="4aef2-240">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="4aef2-241">제출 단추 (`IHtmlElement`) 및 폼 값 (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="4aef2-241">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="4aef2-242">[AngleSharp](https://anglesharp.github.io/) 는이 항목 및 샘플 앱에서 데모용으로 사용 되는 타사 구문 분석 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-242">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="4aef2-243">AngleSharp는 ASP.NET Core 앱의 통합 테스트에 지원 되지 않거나 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-243">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="4aef2-244">[HAP (Html 민첩성 팩)](https://html-agility-pack.net/)와 같은 다른 파서를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-244">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="4aef2-245">또 다른 방법은 위조 방지 시스템의 요청 확인 토큰을 처리 하 고 위조 쿠키를 직접 방지 하는 코드를 작성 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-245">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="4aef2-246">WithWebHostBuilder를 사용 하 여 클라이언트 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="4aef2-246">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="4aef2-247">테스트 메서드 내에서 추가 구성이 필요한 경우 [Withwebhostbuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) 는 구성으로 추가로 사용자 지정 된 [iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) 를 사용 하 여 새 `WebApplicationFactory`을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-247">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="4aef2-248">[샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) 의 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 테스트 메서드는 `WithWebHostBuilder`를 사용 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-248">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="4aef2-249">이 테스트는 SUT에서 양식 제출을 트리거하여 데이터베이스에서 레코드 삭제를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-249">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="4aef2-250">@No__t-0 클래스의 다른 테스트는 데이터베이스의 모든 레코드를 삭제 하 고 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 메서드 이전에 실행할 수 있는 작업을 수행 하기 때문에이 테스트 메서드에서 데이터베이스를 다시 시드됩니다 하 여 SUT가 삭제할 레코드가 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-250">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="4aef2-251">SUT에서 `messages` 폼의 첫 번째 삭제 단추를 선택 하면 SUT에 대 한 요청에서 시뮬레이션 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-251">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="4aef2-252">클라이언트 옵션</span><span class="sxs-lookup"><span data-stu-id="4aef2-252">Client options</span></span>

<span data-ttu-id="4aef2-253">다음 표에서는 @no__t 1 인스턴스를 만들 때 사용할 수 있는 기본 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) 을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-253">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="4aef2-254">옵션</span><span class="sxs-lookup"><span data-stu-id="4aef2-254">Option</span></span> | <span data-ttu-id="4aef2-255">설명</span><span class="sxs-lookup"><span data-stu-id="4aef2-255">Description</span></span> | <span data-ttu-id="4aef2-256">기본</span><span class="sxs-lookup"><span data-stu-id="4aef2-256">Default</span></span> |
| ------ | ----------- | ------- |
| [<span data-ttu-id="4aef2-257">AllowAutoRedirect</span><span class="sxs-lookup"><span data-stu-id="4aef2-257">AllowAutoRedirect</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | <span data-ttu-id="4aef2-258">@No__t-0 인스턴스가 자동으로 리디렉션 응답을 따르는지 여부를 가져오거나 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-258">Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span> | `true` |
| [<span data-ttu-id="4aef2-259">BaseAddress</span><span class="sxs-lookup"><span data-stu-id="4aef2-259">BaseAddress</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | <span data-ttu-id="4aef2-260">@No__t 0 인스턴스의 기본 주소를 가져오거나 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-260">Gets or sets the base address of `HttpClient` instances.</span></span> | `http://localhost` |
| [<span data-ttu-id="4aef2-261">HandleCookies</span><span class="sxs-lookup"><span data-stu-id="4aef2-261">HandleCookies</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | <span data-ttu-id="4aef2-262">@No__t-0 인스턴스에서 쿠키를 처리할지 여부를 가져오거나 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-262">Gets or sets whether `HttpClient` instances should handle cookies.</span></span> | `true` |
| [<span data-ttu-id="4aef2-263">Max자동 리디렉션</span><span class="sxs-lookup"><span data-stu-id="4aef2-263">MaxAutomaticRedirections</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | <span data-ttu-id="4aef2-264">@No__t-0 인스턴스가 따라야 하는 리디렉션 응답의 최대 수를 가져오거나 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-264">Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> | <span data-ttu-id="4aef2-265">7</span><span class="sxs-lookup"><span data-stu-id="4aef2-265">7</span></span> |

<span data-ttu-id="4aef2-266">@No__t-0 클래스를 만들어 [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) 메서드에 전달 합니다 (기본값은 코드 예제에 표시 됨).</span><span class="sxs-lookup"><span data-stu-id="4aef2-266">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="4aef2-267">모의 서비스 삽입</span><span class="sxs-lookup"><span data-stu-id="4aef2-267">Inject mock services</span></span>

<span data-ttu-id="4aef2-268">호스트 작성기에서 [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) 에 대 한 호출을 사용 하 여 테스트에서 서비스를 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-268">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="4aef2-269">**모의 서비스를 삽입 하려면 SUT에 @no__t 메서드를 사용 하는 `Startup` 클래스가 있어야 합니다.**</span><span class="sxs-lookup"><span data-stu-id="4aef2-269">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="4aef2-270">샘플 SUT에는 견적을 반환 하는 범위 지정 서비스가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-270">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="4aef2-271">인덱스 페이지를 요청 하면 따옴표가 인덱스 페이지의 숨겨진 필드에 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-271">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="4aef2-272">*Services/IQuoteService*:</span><span class="sxs-lookup"><span data-stu-id="4aef2-272">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="4aef2-273">*Services/QuoteService*:</span><span class="sxs-lookup"><span data-stu-id="4aef2-273">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="4aef2-274">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="4aef2-274">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="4aef2-275">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="4aef2-275">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="4aef2-276">*Pages/Index .cs*:</span><span class="sxs-lookup"><span data-stu-id="4aef2-276">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="4aef2-277">다음 태그는 SUT 앱이 실행 될 때 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-277">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="4aef2-278">통합 테스트에서 서비스 및 견적 주입을 테스트 하기 위해 테스트를 통해 모의 서비스를 SUT에 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-278">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="4aef2-279">모의 서비스는 앱의 `QuoteService`을 테스트 앱에서 제공 하는 서비스 (`TestQuoteService` 이라고 함)로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-279">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="4aef2-280">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="4aef2-280">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="4aef2-281">`ConfigureTestServices`이 호출 되 고 범위가 지정 된 서비스가 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-281">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="4aef2-282">테스트 실행 중에 생성 된 태그는 `TestQuoteService`에서 제공 된 따옴표 텍스트를 반영 하므로 어설션이 통과 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-282">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="4aef2-283">테스트 인프라가 앱 콘텐츠 루트 경로를 유추 하는 방법</span><span class="sxs-lookup"><span data-stu-id="4aef2-283">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="4aef2-284">@No__t-0 생성자는 키가 @no__t 3 어셈블리인 `System.Reflection.Assembly.FullName` 인 통합 테스트가 포함 된 어셈블리에서 [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) 를 검색 하 여 앱 [콘텐츠 루트](xref:fundamentals/index#content-root) 경로를 유추 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-284">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="4aef2-285">올바른 키를 가진 특성을 찾을 수 없는 경우 `WebApplicationFactory`은 솔루션 파일 (*.sln*)을 검색 하는 것으로 대체 되 고 `TEntryPoint` 어셈블리 이름을 솔루션 디렉터리에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-285">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="4aef2-286">응용 프로그램 루트 디렉터리 (콘텐츠 루트 경로)는 뷰와 콘텐츠 파일을 검색 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-286">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="4aef2-287">섀도 복사 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="4aef2-287">Disable shadow copying</span></span>

<span data-ttu-id="4aef2-288">섀도 복사를 수행 하면 테스트가 출력 디렉터리와 다른 디렉터리에 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-288">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="4aef2-289">테스트가 제대로 작동 하려면 섀도 복사를 사용 하지 않도록 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-289">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="4aef2-290">[샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) 은 xunit을 사용 하 고 올바른 구성 설정을 사용 하 여 xunit *. runner* 파일을 포함 하 여 xunit에 대해 섀도 복사를 사용 하지 않도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-290">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="4aef2-291">자세한 내용은 [JSON을 사용 하 여 xUnit 구성](https://xunit.github.io/docs/configuring-with-json.html)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="4aef2-291">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="4aef2-292">다음 콘텐츠를 사용 하 여 테스트 프로젝트의 루트에 *xunit.*</span><span class="sxs-lookup"><span data-stu-id="4aef2-292">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a><span data-ttu-id="4aef2-293">개체 삭제</span><span class="sxs-lookup"><span data-stu-id="4aef2-293">Disposal of objects</span></span>

<span data-ttu-id="4aef2-294">@No__t-0 구현의 테스트를 실행 한 후 xUnit에서 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)을 삭제 하면 [Testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver) 및 [httpclient](/dotnet/api/system.net.http.httpclient) 가 삭제 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-294">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="4aef2-295">개발자가 인스턴스화한 개체를 삭제 해야 하는 경우 `IClassFixture` 구현에서 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-295">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="4aef2-296">자세한 내용은 [Dispose 메서드 구현](/dotnet/standard/garbage-collection/implementing-dispose)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="4aef2-296">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="4aef2-297">통합 테스트 샘플</span><span class="sxs-lookup"><span data-stu-id="4aef2-297">Integration tests sample</span></span>

<span data-ttu-id="4aef2-298">[샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) 은 다음과 같은 두 개의 앱으로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-298">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="4aef2-299">App</span><span class="sxs-lookup"><span data-stu-id="4aef2-299">App</span></span> | <span data-ttu-id="4aef2-300">프로젝트 디렉터리</span><span class="sxs-lookup"><span data-stu-id="4aef2-300">Project directory</span></span> | <span data-ttu-id="4aef2-301">설명</span><span class="sxs-lookup"><span data-stu-id="4aef2-301">Description</span></span> |
| --- | ----------------- | ----------- |
| <span data-ttu-id="4aef2-302">메시지 앱 (SUT)</span><span class="sxs-lookup"><span data-stu-id="4aef2-302">Message app (the SUT)</span></span> | <span data-ttu-id="4aef2-303">*src/Razorororproject*</span><span class="sxs-lookup"><span data-stu-id="4aef2-303">*src/RazorPagesProject*</span></span> | <span data-ttu-id="4aef2-304">사용자가 메시지를 추가, 삭제, 삭제 및 분석할 수 있도록 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-304">Allows a user to add, delete one, delete all, and analyze messages.</span></span> |
| <span data-ttu-id="4aef2-305">응용 프로그램 테스트</span><span class="sxs-lookup"><span data-stu-id="4aef2-305">Test app</span></span> | <span data-ttu-id="4aef2-306">*테스트/RazorPagesProject. 테스트*</span><span class="sxs-lookup"><span data-stu-id="4aef2-306">*tests/RazorPagesProject.Tests*</span></span> | <span data-ttu-id="4aef2-307">SUT 테스트를 통합 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-307">Used to integration test the SUT.</span></span> |

<span data-ttu-id="4aef2-308">[Visual Studio](https://visualstudio.microsoft.com)와 같은 IDE의 기본 제공 테스트 기능을 사용 하 여 테스트를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-308">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="4aef2-309">[Visual Studio Code](https://code.visualstudio.com/) 또는 명령줄을 사용 하는 경우 *테스트/RazorPagesProject. 테스트* 디렉터리의 명령 프롬프트에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-309">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```console
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="4aef2-310">메시지 앱 (SUT) 조직</span><span class="sxs-lookup"><span data-stu-id="4aef2-310">Message app (SUT) organization</span></span>

<span data-ttu-id="4aef2-311">SUT는 다음과 같은 특징을 가진 Razor Pages 메시지 시스템입니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-311">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="4aef2-312">응용 프로그램의 인덱스 페이지 (*Pages/index. cshtml* 및 *pages/인덱스인*)는 메시지의 추가, 삭제 및 분석 (메시지당 평균 단어)을 제어 하는 UI 및 페이지 모델 메서드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-312">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="4aef2-313">메시지는 두 개의 속성 (@no__t (키) 및 `Text` (메시지)의 두 가지 속성을 사용 하 여 `Message` 클래스 (*데이터/메시지 .cs*)에서 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-313">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="4aef2-314">@No__t-0 속성은 필수 이며 200 자로 제한 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-314">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="4aef2-315">메시지는 [Entity Framework의 메모리 내 데이터베이스](/ef/core/providers/in-memory/)&#8224;를 사용 하 여 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-315">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="4aef2-316">앱에는 데이터베이스 컨텍스트 클래스의 DAL (데이터 액세스 계층)이 포함 되 고 `AppDbContext` (*data/AppDbContext*)이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-316">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="4aef2-317">응용 프로그램이 시작 될 때 데이터베이스가 비어 있는 경우 메시지 저장소는 세 개의 메시지로 초기화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-317">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="4aef2-318">앱에는 인증 된 사용자만 액세스할 수 있는 `/SecurePage`이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-318">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="4aef2-319">&#8224;EF 항목인 [InMemory 테스트](/ef/core/miscellaneous/testing/in-memory)는 MSTest를 사용 하 여 테스트에 메모리 내 데이터베이스를 사용 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-319">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="4aef2-320">이 항목에서는 [Xunit](https://xunit.github.io/) 테스트 프레임 워크를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-320">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="4aef2-321">여러 테스트 프레임 워크에서 테스트 개념 및 테스트 구현은 비슷하지만 동일 하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-321">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="4aef2-322">앱은 리포지토리 패턴을 사용 하지 않으며 [UoW (Unit Of Work) 패턴](https://martinfowler.com/eaaCatalog/unitOfWork.html)의 효과적인 예는 아니지만 Razor Pages 이러한 개발 패턴을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-322">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="4aef2-323">자세한 내용은 [인프라 지 속성 계층](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) 및 [테스트 컨트롤러 논리](/aspnet/core/mvc/controllers/testing) 디자인 (샘플에서는 리포지토리 패턴 구현)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="4aef2-323">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="4aef2-324">앱 구성 테스트</span><span class="sxs-lookup"><span data-stu-id="4aef2-324">Test app organization</span></span>

<span data-ttu-id="4aef2-325">테스트 앱은 테스트 */Razor 프로젝트* 의 콘솔 앱입니다. 테스트 디렉터리입니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-325">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="4aef2-326">응용 프로그램 디렉터리 테스트</span><span class="sxs-lookup"><span data-stu-id="4aef2-326">Test app directory</span></span> | <span data-ttu-id="4aef2-327">설명</span><span class="sxs-lookup"><span data-stu-id="4aef2-327">Description</span></span> |
| ------------------ | ----------- |
| <span data-ttu-id="4aef2-328">*BasicTests*</span><span class="sxs-lookup"><span data-stu-id="4aef2-328">*BasicTests*</span></span> | <span data-ttu-id="4aef2-329">*BasicTests.cs* 에는 라우팅을 위한 테스트 메서드, 인증 되지 않은 사용자가 보안 페이지에 액세스 하는 방법, GitHub 사용자 프로필 가져오기 및 프로필의 사용자 로그인 확인이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-329">*BasicTests.cs* contains test methods for routing, accessing a secure page by an unauthenticated user, and obtaining a GitHub user profile and checking the profile's user login.</span></span> |
| <span data-ttu-id="4aef2-330">*IntegrationTests*</span><span class="sxs-lookup"><span data-stu-id="4aef2-330">*IntegrationTests*</span></span> | <span data-ttu-id="4aef2-331">*IndexPageTests.cs* 사용자 지정 `WebApplicationFactory` 클래스를 사용 하 여 인덱스 페이지에 대 한 통합 테스트를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-331">*IndexPageTests.cs* contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> |
| <span data-ttu-id="4aef2-332">*도우미/유틸리티*</span><span class="sxs-lookup"><span data-stu-id="4aef2-332">*Helpers/Utilities*</span></span> | <ul><li><span data-ttu-id="4aef2-333">*Utilities.cs* 에는 테스트 데이터로 데이터베이스를 시드 하는 데 사용 되는 @no__t 1 메서드가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-333">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="4aef2-334">*HtmlHelpers.cs* 는 테스트 메서드에서 사용할 AngleSharp `IHtmlDocument`을 반환 하는 메서드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-334">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="4aef2-335">*HttpClientExtensions.cs* 는 `SendAsync`에 대 한 오버 로드를 제공 하 여 sut에 요청을 제출 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-335">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="4aef2-336">테스트 프레임 워크는 [Xunit](https://xunit.github.io/)입니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-336">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="4aef2-337">[AspNetCore. TestHost](/dotnet/api/microsoft.aspnetcore.testhost)( [testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver)포함)를 사용 하 여 통합 테스트를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-337">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="4aef2-338">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 패키지는 테스트 호스트와 테스트 서버를 구성 하는 데 사용 되기 때문에 @no__t 1 및 @no__t 패키지는 테스트 응용 프로그램의 프로젝트 파일 또는 테스트의 개발자 구성에서 직접 패키지 참조를 요구 하지 않습니다. 다운로드.</span><span class="sxs-lookup"><span data-stu-id="4aef2-338">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="4aef2-339">**테스트를 위해 데이터베이스 시드**</span><span class="sxs-lookup"><span data-stu-id="4aef2-339">**Seeding the database for testing**</span></span>

<span data-ttu-id="4aef2-340">통합 테스트는 일반적으로 테스트 실행 전에 데이터베이스에 작은 데이터 집합이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-340">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="4aef2-341">예를 들어 delete 테스트는 데이터베이스 레코드 삭제를 호출 하므로 삭제 요청이 성공 하려면 데이터베이스에 적어도 하나 이상의 레코드가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-341">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="4aef2-342">샘플 앱은 테스트가 실행 될 때 사용할 수 있는 *Utilities.cs* 의 세 가지 메시지를 사용 하 여 데이터베이스를 시드 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-342">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

<span data-ttu-id="4aef2-343">SUT의 데이터베이스 컨텍스트가 `Startup.ConfigureServices` 메서드에 등록 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-343">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="4aef2-344">응용 프로그램의 `builder.ConfigureServices` 콜백은 응용 프로그램의 @no__t 코드를 실행 *한 후* 에 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-344">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="4aef2-345">테스트에 다른 데이터베이스를 사용 하려면 앱의 데이터베이스 컨텍스트를 `builder.ConfigureServices`으로 바꾸어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-345">To use a different database for the tests, the app's database context must be replaced in `builder.ConfigureServices`.</span></span> <span data-ttu-id="4aef2-346">자세한 내용은 [WebApplicationFactory 사용자 지정](#customize-webapplicationfactory) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="4aef2-346">For more information, see the [Customize WebApplicationFactory](#customize-webapplicationfactory) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4aef2-347">통합 테스트는 응용 프로그램의 구성 요소가 응용 프로그램의 지원 인프라 (예: 데이터베이스, 파일 시스템 및 네트워크)를 포함 하는 수준에서 올바르게 작동 하는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-347">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="4aef2-348">ASP.NET Core는 테스트 웹 호스트 및 메모리 내 테스트 서버에서 단위 테스트 프레임 워크를 사용 하는 통합 테스트를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-348">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="4aef2-349">이 항목에서는 단위 테스트에 대해 기본적으로 이해 하 고 있다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-349">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="4aef2-350">테스트 개념을 잘 모르는 경우 [.Net Core의 단위 테스트 및 .NET Standard](/dotnet/core/testing/) 항목 및 연결 된 콘텐츠를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="4aef2-350">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="4aef2-351">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4aef2-351">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="4aef2-352">샘플 앱은 Razor Pages 앱 이며 Razor Pages를 기본적으로 이해 하 고 있다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-352">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="4aef2-353">Razor Pages에 익숙하지 않은 경우 다음 항목을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="4aef2-353">If unfamiliar with Razor Pages, see the following topics:</span></span>

* [<span data-ttu-id="4aef2-354">Razor 페이지 소개</span><span class="sxs-lookup"><span data-stu-id="4aef2-354">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="4aef2-355">Razor 페이지 시작</span><span class="sxs-lookup"><span data-stu-id="4aef2-355">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="4aef2-356">Razor 페이지 단위 테스트</span><span class="sxs-lookup"><span data-stu-id="4aef2-356">Razor Pages unit tests</span></span>](xref:test/razor-pages-tests)

> [!NOTE]
> <span data-ttu-id="4aef2-357">SPAs 테스트를 위해 브라우저를 자동화할 수 있는 [Selenium](https://www.seleniumhq.org/)와 같은 도구를 권장 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-357">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="4aef2-358">통합 테스트 소개</span><span class="sxs-lookup"><span data-stu-id="4aef2-358">Introduction to integration tests</span></span>

<span data-ttu-id="4aef2-359">통합 테스트는 [단위 테스트](/dotnet/core/testing/)보다 더 광범위 한 수준에서 앱의 구성 요소를 평가 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-359">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="4aef2-360">단위 테스트는 개별 클래스 메서드와 같은 격리 된 소프트웨어 구성 요소를 테스트 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-360">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="4aef2-361">통합 테스트는 두 개 이상의 앱 구성 요소가 함께 작동 하 여 요청을 완전히 처리 하는 데 필요한 모든 구성 요소를 포함 하 여 예상 결과를 생성 하는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-361">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="4aef2-362">이러한 광범위 한 테스트는 다음과 같은 구성 요소를 포함 하 여 앱의 인프라 및 전체 프레임 워크를 테스트 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-362">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="4aef2-363">데이터베이스</span><span class="sxs-lookup"><span data-stu-id="4aef2-363">Database</span></span>
* <span data-ttu-id="4aef2-364">파일 시스템</span><span class="sxs-lookup"><span data-stu-id="4aef2-364">File system</span></span>
* <span data-ttu-id="4aef2-365">네트워크 어플라이언스</span><span class="sxs-lookup"><span data-stu-id="4aef2-365">Network appliances</span></span>
* <span data-ttu-id="4aef2-366">요청-응답 파이프라인</span><span class="sxs-lookup"><span data-stu-id="4aef2-366">Request-response pipeline</span></span>

<span data-ttu-id="4aef2-367">단위 테스트는 인프라 구성 요소 대신 *fakes* 또는 *모의 개체*라고 하는 구성 된 구성 요소를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-367">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="4aef2-368">단위 테스트와 달리 통합 테스트는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-368">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="4aef2-369">프로덕션 환경에서 앱이 사용 하는 실제 구성 요소를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-369">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="4aef2-370">더 많은 코드와 데이터 처리가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-370">Require more code and data processing.</span></span>
* <span data-ttu-id="4aef2-371">실행 하는 데 시간이 더 오래 걸립니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-371">Take longer to run.</span></span>

<span data-ttu-id="4aef2-372">따라서 가장 중요 한 인프라 시나리오에 대 한 통합 테스트 사용을 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-372">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="4aef2-373">단위 테스트 또는 통합 테스트를 사용 하 여 동작을 테스트할 수 있는 경우 단위 테스트를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-373">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="4aef2-374">데이터베이스 및 파일 시스템을 사용 하 여 모든 가능한 데이터 순열 및 파일 액세스에 대해 통합 테스트를 작성 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="4aef2-374">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="4aef2-375">응용 프로그램 전체에서 데이터베이스 및 파일 시스템과 상호 작용 하는 위치에 관계 없이, 일반적으로 데이터베이스 및 파일 시스템 구성 요소를 적절 하 게 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-375">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="4aef2-376">이러한 구성 요소와 상호 작용 하는 메서드 논리의 루틴 테스트에 단위 테스트를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-376">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="4aef2-377">단위 테스트에서 인프라 fakes/mock를 사용 하면 테스트 실행 속도가 빨라집니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-377">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="4aef2-378">통합 테스트에 대 한 토론에서 테스트 된 프로젝트를 종종 *테스트 중인 시스템*또는 short의 경우 "sut" 라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-378">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="4aef2-379">*이 항목 전체에서 "SUT"를 사용 하 여 테스트 된 ASP.NET Core 앱을 참조 합니다.*</span><span class="sxs-lookup"><span data-stu-id="4aef2-379">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="4aef2-380">ASP.NET Core 통합 테스트</span><span class="sxs-lookup"><span data-stu-id="4aef2-380">ASP.NET Core integration tests</span></span>

<span data-ttu-id="4aef2-381">ASP.NET Core의 통합 테스트에는 다음이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-381">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="4aef2-382">테스트 프로젝트는 테스트를 포함 하 고 실행 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-382">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="4aef2-383">테스트 프로젝트에는 SUT에 대 한 참조가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-383">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="4aef2-384">테스트 프로젝트는 SUT에 대 한 테스트 웹 호스트를 만들고 테스트 서버 클라이언트를 사용 하 여 SUT를 사용 하 여 요청 및 응답을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-384">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="4aef2-385">Test runner는 테스트를 실행 하 고 테스트 결과를 보고 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-385">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="4aef2-386">통합 테스트는 일반적인 *정렬*, 작업 및 *어설션* 테스트 *단계를 포함*하는 이벤트 시퀀스를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-386">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="4aef2-387">SUT의 웹 호스트가 구성 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-387">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="4aef2-388">앱에 요청을 제출 하는 테스트 서버 클라이언트가 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-388">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="4aef2-389">테스트 응용 프로그램에서 요청을 준비 하는 *정렬* 테스트 단계를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-389">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="4aef2-390">작업 *테스트 단계가* 실행 됩니다. 즉, 클라이언트가 요청을 제출 하 고 응답을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-390">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="4aef2-391">*Assert* 테스트 단계가 실행 됩니다. *실제* 응답은 *예상* 된 응답에 따라 *통과* 또는 *실패* 의 유효성을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-391">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="4aef2-392">모든 테스트가 실행 될 때까지 프로세스가 계속 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-392">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="4aef2-393">테스트 결과가 보고 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-393">The test results are reported.</span></span>

<span data-ttu-id="4aef2-394">일반적으로 테스트 웹 호스트는 테스트 실행을 위해 응용 프로그램의 일반 웹 호스트와 다르게 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-394">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="4aef2-395">예를 들어 다른 데이터베이스 또는 다른 앱 설정을 테스트에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-395">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="4aef2-396">테스트 웹 호스트 및 메모리 내 테스트 서버 ([Testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver))와 같은 인프라 구성 요소는 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 패키지에서 제공 하거나 관리 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-396">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="4aef2-397">이 패키지를 사용 하면 테스트 생성 및 실행이 간소화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-397">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="4aef2-398">@No__t-0 패키지는 다음 작업을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-398">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="4aef2-399">SUT의 종속성 파일 (*. deps*)을 테스트 프로젝트의 *bin* 디렉터리에 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-399">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="4aef2-400">테스트를 실행할 때 정적 파일 및 페이지/뷰가 검색 되도록 [콘텐츠 루트](xref:fundamentals/index#content-root) 를 sut의 프로젝트 루트로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-400">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="4aef2-401">@No__t-1을 사용 하 여 SUT의 부트스트래핑을 간소화 하는 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) 클래스를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-401">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="4aef2-402">[단위 테스트](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) 설명서에서는 테스트를 실행 하는 방법에 대 한 자세한 지침과 테스트 및 테스트 클래스의 이름을 설정 하는 방법에 대 한 자세한 지침과 함께 테스트 프로젝트 및 test runner를 설정 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-402">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="4aef2-403">앱에 대 한 테스트 프로젝트를 만들 때 통합 테스트에서 여러 프로젝트로 단위 테스트를 분리 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-403">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="4aef2-404">이를 통해 인프라 테스트 구성 요소가 실수로 단위 테스트에 포함 되지 않도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-404">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="4aef2-405">단위 및 통합 테스트를 분리 하 여 실행 되는 테스트 집합을 제어할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-405">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="4aef2-406">Razor Pages 앱과 MVC 앱의 테스트에 대 한 구성의 차이는 거의 없습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-406">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="4aef2-407">유일한 차이점은 테스트의 이름을 지정 하는 방법 뿐입니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-407">The only difference is in how the tests are named.</span></span> <span data-ttu-id="4aef2-408">Razor Pages 앱에서 페이지 끝점의 테스트는 일반적으로 페이지 모델 클래스의 이름을 지정 합니다 (예: 인덱스 페이지에 대 한 구성 요소 통합을 테스트 하기 위해-0 @no__t).</span><span class="sxs-lookup"><span data-stu-id="4aef2-408">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="4aef2-409">MVC 앱에서 테스트는 일반적으로 컨트롤러 클래스로 구성 되며 테스트 하는 컨트롤러 뒤에 이름이 지정 됩니다 (예: `HomeControllerTests`은 Home 컨트롤러에 대 한 구성 요소 통합 테스트).</span><span class="sxs-lookup"><span data-stu-id="4aef2-409">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="4aef2-410">응용 프로그램 테스트 필수 조건</span><span class="sxs-lookup"><span data-stu-id="4aef2-410">Test app prerequisites</span></span>

<span data-ttu-id="4aef2-411">테스트 프로젝트는 다음을 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-411">The test project must:</span></span>

* <span data-ttu-id="4aef2-412">다음 패키지를 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-412">Reference the following packages:</span></span>
  * [<span data-ttu-id="4aef2-413">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="4aef2-413">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [<span data-ttu-id="4aef2-414">AspNetCore.</span><span class="sxs-lookup"><span data-stu-id="4aef2-414">Microsoft.AspNetCore.Mvc.Testing</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* <span data-ttu-id="4aef2-415">프로젝트 파일 (`<Project Sdk="Microsoft.NET.Sdk.Web">`)에서 웹 SDK를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-415">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span> <span data-ttu-id="4aef2-416">웹 SDK는 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app)를 참조할 때 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-416">The Web SDK is required when referencing the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="4aef2-417">이러한 필수 구성 요소는 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)에서 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-417">These prerequisites can be seen in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="4aef2-418">\*테스트/RazorPagesProject. 테스트/razor \* 프로젝트. .csproj 파일을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-418">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="4aef2-419">샘플 앱은 [Xunit](https://xunit.github.io/) 테스트 프레임 워크 및 [AngleSharp](https://anglesharp.github.io/) 파서 라이브러리를 사용 하므로 샘플 앱은 다음을 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-419">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="4aef2-420">xunit</span><span class="sxs-lookup"><span data-stu-id="4aef2-420">xunit</span></span>](https://www.nuget.org/packages/xunit/)
* [<span data-ttu-id="4aef2-421">xunit. visualstudio</span><span class="sxs-lookup"><span data-stu-id="4aef2-421">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [<span data-ttu-id="4aef2-422">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="4aef2-422">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a><span data-ttu-id="4aef2-423">SUT 환경</span><span class="sxs-lookup"><span data-stu-id="4aef2-423">SUT environment</span></span>

<span data-ttu-id="4aef2-424">SUT의 [환경이](xref:fundamentals/environments) 설정 되지 않은 경우 환경의 기본값은 Development로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-424">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="4aef2-425">기본 WebApplicationFactory를 사용 하는 기본 테스트</span><span class="sxs-lookup"><span data-stu-id="4aef2-425">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="4aef2-426">[WebApplicationFactory @ no__t-1TEntryPoint >](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) 는 통합 테스트용 [testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver) 를 만드는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-426">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="4aef2-427">`TEntryPoint`은 SUT의 진입점 클래스입니다. 일반적으로 `Startup` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-427">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="4aef2-428">테스트 클래스는 클래스에 테스트를 포함 하 고 클래스의 테스트에서 공유 개체 인스턴스를 제공 하는 클래스 *fixture* 인터페이스 ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture))를 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-428">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

### <a name="basic-test-of-app-endpoints"></a><span data-ttu-id="4aef2-429">앱 끝점의 기본 테스트</span><span class="sxs-lookup"><span data-stu-id="4aef2-429">Basic test of app endpoints</span></span>

<span data-ttu-id="4aef2-430">다음 테스트 클래스인 `WebApplicationFactory`은 을 사용 하 여 SUT를 부트스트랩 하 고, `Get_EndpointsReturnSuccessAndCorrectContentType` 인 테스트 메서드에 [Httpclient](/dotnet/api/system.net.http.httpclient) 를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-430">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="4aef2-431">메서드는 응답 상태 코드가 성공 (200-299 범위의 상태 코드) 되었는지 확인 하 고 `Content-Type` 헤더는 여러 앱 페이지에 대해 `text/html; charset=utf-8`입니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-431">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="4aef2-432">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) 는 리디렉션을 자동으로 따르며 쿠키를 처리 하는 `HttpClient`의 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-432">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="4aef2-433">기본적으로 [Gdpr 동의 정책을](xref:security/gdpr) 사용 하는 경우 요청에서 필수가 아닌 쿠키가 유지 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-433">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="4aef2-434">TempData 공급자가 사용 하는 쿠키와 같이 필수가 아닌 쿠키를 유지 하려면 테스트에서 필수로 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-434">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="4aef2-435">쿠키를 필수로 표시 하는 방법에 대 한 지침은 [필수 쿠키](xref:security/gdpr#essential-cookies)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="4aef2-435">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

### <a name="test-a-secure-endpoint"></a><span data-ttu-id="4aef2-436">보안 끝점 테스트</span><span class="sxs-lookup"><span data-stu-id="4aef2-436">Test a secure endpoint</span></span>

<span data-ttu-id="4aef2-437">@No__t-0 클래스의 다른 테스트는 보안 끝점이 인증 되지 않은 사용자를 앱의 로그인 페이지로 리디렉션하는 지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-437">Another test in the `BasicTests` class checks that a secure endpoint redirects an unauthenticated user to the app's Login page.</span></span>

<span data-ttu-id="4aef2-438">SUT에서 `/SecurePage` 페이지는 [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) 규칙을 사용 하 여 페이지에 [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) 를 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-438">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="4aef2-439">자세한 내용은 [Razor Pages 권한 부여 규칙](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="4aef2-439">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="4aef2-440">@No__t-0 테스트에서는 [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) 를 `false`으로 설정 하 여 리디렉션을 허용 하지 않도록 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) 가 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-440">In the `Get_SecurePageRequiresAnAuthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet2)]

<span data-ttu-id="4aef2-441">클라이언트에서 리디렉션을 따르도록 허용 하지 않으면 다음 검사를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-441">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="4aef2-442">SUT에서 반환 된 상태 코드는 HttpStatusCode 결과에 대해 확인할 수 있습니다 [.](/dotnet/api/system.net.httpstatuscode) 이는 로그인 페이지에 대 한 리디렉션 이후의 최종 상태 코드는 [HttpStatusCode](/dotnet/api/system.net.httpstatuscode)입니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-442">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="4aef2-443">응답 헤더의 `Location` 헤더 값은 마지막 로그인 페이지 응답이 아닌 `http://localhost/Identity/Account/Login`로 시작 하는 것을 확인 하기 위해 확인 됩니다 .이 값은 `Location` 헤더가 존재 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-443">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="4aef2-444">@No__t-0에 대 한 자세한 내용은 [클라이언트 옵션](#client-options) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="4aef2-444">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="4aef2-445">WebApplicationFactory 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="4aef2-445">Customize WebApplicationFactory</span></span>

<span data-ttu-id="4aef2-446">@No__t-0에서 상속 하 여 하나 이상의 사용자 지정 팩터리를 만들도록 테스트 클래스와 별개로 웹 호스트 구성을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-446">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="4aef2-447">@No__t-0에서 상속 하 고 [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)를 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-447">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="4aef2-448">[Iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) 를 사용 하면 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices)를 사용 하 여 서비스 컬렉션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-448">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="4aef2-449">[샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) 에서의 데이터베이스 시드는 `InitializeDbForTests` 메서드로 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-449">Database seeding in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="4aef2-450">메서드는 [통합 테스트 샘플: 테스트 앱 구성](#test-app-organization) 섹션에 설명 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-450">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

2. <span data-ttu-id="4aef2-451">테스트 클래스에서 사용자 지정 `CustomWebApplicationFactory`을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-451">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="4aef2-452">다음 예제에서는 `IndexPageTests` 클래스에서 팩터리를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-452">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="4aef2-453">샘플 앱의 클라이언트는 다음 리디렉션의 @no__t 0이 되지 않도록 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-453">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="4aef2-454">[보안 끝점 테스트](#test-a-secure-endpoint) 섹션에서 설명한 대로이를 통해 테스트에서 앱의 첫 번째 응답의 결과를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-454">As explained in the [Test a secure endpoint](#test-a-secure-endpoint) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="4aef2-455">첫 번째 응답은 `Location` 헤더를 사용 하는 이러한 테스트 대부분의 리디렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-455">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="4aef2-456">일반적인 테스트는 `HttpClient` 및 도우미 메서드를 사용 하 여 요청 및 응답을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-456">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="4aef2-457">SUT에 대 한 POST 요청은 앱의 [데이터 보호 방지 시스템이](xref:security/data-protection/introduction)자동으로 수행 하는 위조 방지 검사를 충족 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-457">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="4aef2-458">테스트의 POST 요청을 정렬 하기 위해 테스트 앱은 다음을 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-458">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="4aef2-459">페이지에 대 한 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-459">Make a request for the page.</span></span>
1. <span data-ttu-id="4aef2-460">위조 방지 쿠키를 구문 분석 하 고 응답에서 유효성 검사 토큰을 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-460">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="4aef2-461">위조 방지 쿠키 및 요청 유효성 검사 토큰을 사용 하 여 POST 요청을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-461">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="4aef2-462">[샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) 의 `SendAsync` 도우미 확장 메서드 (도우미 */httpclientextensions*) 및 @no__t 도우미 메서드 (helper */Htmlhelpers. cs*)는 [AngleSharp](https://anglesharp.github.io/) 파서를 사용 하 여 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-462">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="4aef2-463">`GetDocumentAsync` @no__t [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) 를 받고 `IHtmlDocument`을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-463">`GetDocumentAsync` &ndash; Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="4aef2-464">`GetDocumentAsync`은 원래 `HttpResponseMessage`에 따라 *가상 응답* 을 준비 하는 팩터리를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-464">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="4aef2-465">자세한 내용은 [AngleSharp 설명서](https://github.com/AngleSharp/AngleSharp#documentation)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="4aef2-465">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="4aef2-466">`HttpClient` @no__t에 대 한 확장 메서드는 [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) 을 작성 하 고 [SendAsync (HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) 를 호출 하 여 요청을 sut에 제출 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-466">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="4aef2-467">@No__t-0에 대 한 오버 로드는 HTML 폼 (`IHtmlFormElement`) 및 다음을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-467">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="4aef2-468">양식의 제출 단추 (`IHtmlElement`)</span><span class="sxs-lookup"><span data-stu-id="4aef2-468">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="4aef2-469">양식 값 컬렉션 (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="4aef2-469">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="4aef2-470">제출 단추 (`IHtmlElement`) 및 폼 값 (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="4aef2-470">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="4aef2-471">[AngleSharp](https://anglesharp.github.io/) 는이 항목 및 샘플 앱에서 데모용으로 사용 되는 타사 구문 분석 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-471">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="4aef2-472">AngleSharp는 ASP.NET Core 앱의 통합 테스트에 지원 되지 않거나 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-472">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="4aef2-473">[HAP (Html 민첩성 팩)](https://html-agility-pack.net/)와 같은 다른 파서를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-473">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="4aef2-474">또 다른 방법은 위조 방지 시스템의 요청 확인 토큰을 처리 하 고 위조 쿠키를 직접 방지 하는 코드를 작성 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-474">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="4aef2-475">WithWebHostBuilder를 사용 하 여 클라이언트 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="4aef2-475">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="4aef2-476">테스트 메서드 내에서 추가 구성이 필요한 경우 [Withwebhostbuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) 는 구성으로 추가로 사용자 지정 된 [iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) 를 사용 하 여 새 `WebApplicationFactory`을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-476">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="4aef2-477">[샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) 의 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 테스트 메서드는 `WithWebHostBuilder`를 사용 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-477">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="4aef2-478">이 테스트는 SUT에서 양식 제출을 트리거하여 데이터베이스에서 레코드 삭제를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-478">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="4aef2-479">@No__t-0 클래스의 다른 테스트는 데이터베이스의 모든 레코드를 삭제 하 고 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 메서드 이전에 실행할 수 있는 작업을 수행 하기 때문에이 테스트 메서드에서 데이터베이스를 다시 시드됩니다 하 여 SUT가 삭제할 레코드가 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-479">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="4aef2-480">SUT에서 `messages` 폼의 첫 번째 삭제 단추를 선택 하면 SUT에 대 한 요청에서 시뮬레이션 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-480">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="4aef2-481">클라이언트 옵션</span><span class="sxs-lookup"><span data-stu-id="4aef2-481">Client options</span></span>

<span data-ttu-id="4aef2-482">다음 표에서는 @no__t 1 인스턴스를 만들 때 사용할 수 있는 기본 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) 을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-482">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="4aef2-483">옵션</span><span class="sxs-lookup"><span data-stu-id="4aef2-483">Option</span></span> | <span data-ttu-id="4aef2-484">설명</span><span class="sxs-lookup"><span data-stu-id="4aef2-484">Description</span></span> | <span data-ttu-id="4aef2-485">기본</span><span class="sxs-lookup"><span data-stu-id="4aef2-485">Default</span></span> |
| ------ | ----------- | ------- |
| [<span data-ttu-id="4aef2-486">AllowAutoRedirect</span><span class="sxs-lookup"><span data-stu-id="4aef2-486">AllowAutoRedirect</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | <span data-ttu-id="4aef2-487">@No__t-0 인스턴스가 자동으로 리디렉션 응답을 따르는지 여부를 가져오거나 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-487">Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span> | `true` |
| [<span data-ttu-id="4aef2-488">BaseAddress</span><span class="sxs-lookup"><span data-stu-id="4aef2-488">BaseAddress</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | <span data-ttu-id="4aef2-489">@No__t 0 인스턴스의 기본 주소를 가져오거나 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-489">Gets or sets the base address of `HttpClient` instances.</span></span> | `http://localhost` |
| [<span data-ttu-id="4aef2-490">HandleCookies</span><span class="sxs-lookup"><span data-stu-id="4aef2-490">HandleCookies</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | <span data-ttu-id="4aef2-491">@No__t-0 인스턴스에서 쿠키를 처리할지 여부를 가져오거나 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-491">Gets or sets whether `HttpClient` instances should handle cookies.</span></span> | `true` |
| [<span data-ttu-id="4aef2-492">Max자동 리디렉션</span><span class="sxs-lookup"><span data-stu-id="4aef2-492">MaxAutomaticRedirections</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | <span data-ttu-id="4aef2-493">@No__t-0 인스턴스가 따라야 하는 리디렉션 응답의 최대 수를 가져오거나 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-493">Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> | <span data-ttu-id="4aef2-494">7</span><span class="sxs-lookup"><span data-stu-id="4aef2-494">7</span></span> |

<span data-ttu-id="4aef2-495">@No__t-0 클래스를 만들어 [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) 메서드에 전달 합니다 (기본값은 코드 예제에 표시 됨).</span><span class="sxs-lookup"><span data-stu-id="4aef2-495">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="4aef2-496">모의 서비스 삽입</span><span class="sxs-lookup"><span data-stu-id="4aef2-496">Inject mock services</span></span>

<span data-ttu-id="4aef2-497">호스트 작성기에서 [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) 에 대 한 호출을 사용 하 여 테스트에서 서비스를 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-497">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="4aef2-498">**모의 서비스를 삽입 하려면 SUT에 @no__t 메서드를 사용 하는 `Startup` 클래스가 있어야 합니다.**</span><span class="sxs-lookup"><span data-stu-id="4aef2-498">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="4aef2-499">샘플 SUT에는 견적을 반환 하는 범위 지정 서비스가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-499">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="4aef2-500">인덱스 페이지를 요청 하면 따옴표가 인덱스 페이지의 숨겨진 필드에 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-500">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="4aef2-501">*Services/IQuoteService*:</span><span class="sxs-lookup"><span data-stu-id="4aef2-501">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="4aef2-502">*Services/QuoteService*:</span><span class="sxs-lookup"><span data-stu-id="4aef2-502">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="4aef2-503">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="4aef2-503">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="4aef2-504">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="4aef2-504">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="4aef2-505">*Pages/Index .cs*:</span><span class="sxs-lookup"><span data-stu-id="4aef2-505">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="4aef2-506">다음 태그는 SUT 앱이 실행 될 때 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-506">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="4aef2-507">통합 테스트에서 서비스 및 견적 주입을 테스트 하기 위해 테스트를 통해 모의 서비스를 SUT에 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-507">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="4aef2-508">모의 서비스는 앱의 `QuoteService`을 테스트 앱에서 제공 하는 서비스 (`TestQuoteService` 이라고 함)로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-508">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="4aef2-509">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="4aef2-509">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="4aef2-510">`ConfigureTestServices`이 호출 되 고 범위가 지정 된 서비스가 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-510">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="4aef2-511">테스트 실행 중에 생성 된 태그는 `TestQuoteService`에서 제공 된 따옴표 텍스트를 반영 하므로 어설션이 통과 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-511">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="4aef2-512">테스트 인프라가 앱 콘텐츠 루트 경로를 유추 하는 방법</span><span class="sxs-lookup"><span data-stu-id="4aef2-512">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="4aef2-513">@No__t-0 생성자는 키가 @no__t 3 어셈블리인 `System.Reflection.Assembly.FullName` 인 통합 테스트가 포함 된 어셈블리에서 [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) 를 검색 하 여 앱 [콘텐츠 루트](xref:fundamentals/index#content-root) 경로를 유추 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-513">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="4aef2-514">올바른 키를 가진 특성을 찾을 수 없는 경우 `WebApplicationFactory`은 솔루션 파일 (*.sln*)을 검색 하는 것으로 대체 되 고 `TEntryPoint` 어셈블리 이름을 솔루션 디렉터리에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-514">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="4aef2-515">응용 프로그램 루트 디렉터리 (콘텐츠 루트 경로)는 뷰와 콘텐츠 파일을 검색 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-515">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="4aef2-516">섀도 복사 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="4aef2-516">Disable shadow copying</span></span>

<span data-ttu-id="4aef2-517">섀도 복사를 수행 하면 테스트가 출력 디렉터리와 다른 디렉터리에 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-517">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="4aef2-518">테스트가 제대로 작동 하려면 섀도 복사를 사용 하지 않도록 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-518">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="4aef2-519">[샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) 은 xunit을 사용 하 고 올바른 구성 설정을 사용 하 여 xunit *. runner* 파일을 포함 하 여 xunit에 대해 섀도 복사를 사용 하지 않도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-519">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="4aef2-520">자세한 내용은 [JSON을 사용 하 여 xUnit 구성](https://xunit.github.io/docs/configuring-with-json.html)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="4aef2-520">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="4aef2-521">다음 콘텐츠를 사용 하 여 테스트 프로젝트의 루트에 *xunit.*</span><span class="sxs-lookup"><span data-stu-id="4aef2-521">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

<span data-ttu-id="4aef2-522">Visual Studio를 사용 하는 경우 파일의 **출력 디렉터리로 복사** 속성을 **항상 복사**로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-522">If using Visual Studio, set the file's **Copy to Output Directory** property to **Copy always**.</span></span> <span data-ttu-id="4aef2-523">Visual Studio를 사용 하지 않는 경우 테스트 앱의 프로젝트 파일에 `Content` 대상을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-523">If not using Visual Studio, add a `Content` target to the test app's project file:</span></span>

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a><span data-ttu-id="4aef2-524">개체 삭제</span><span class="sxs-lookup"><span data-stu-id="4aef2-524">Disposal of objects</span></span>

<span data-ttu-id="4aef2-525">@No__t-0 구현의 테스트를 실행 한 후 xUnit에서 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)을 삭제 하면 [Testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver) 및 [httpclient](/dotnet/api/system.net.http.httpclient) 가 삭제 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-525">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="4aef2-526">개발자가 인스턴스화한 개체를 삭제 해야 하는 경우 `IClassFixture` 구현에서 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-526">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="4aef2-527">자세한 내용은 [Dispose 메서드 구현](/dotnet/standard/garbage-collection/implementing-dispose)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="4aef2-527">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="4aef2-528">통합 테스트 샘플</span><span class="sxs-lookup"><span data-stu-id="4aef2-528">Integration tests sample</span></span>

<span data-ttu-id="4aef2-529">[샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) 은 다음과 같은 두 개의 앱으로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-529">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="4aef2-530">App</span><span class="sxs-lookup"><span data-stu-id="4aef2-530">App</span></span> | <span data-ttu-id="4aef2-531">프로젝트 디렉터리</span><span class="sxs-lookup"><span data-stu-id="4aef2-531">Project directory</span></span> | <span data-ttu-id="4aef2-532">설명</span><span class="sxs-lookup"><span data-stu-id="4aef2-532">Description</span></span> |
| --- | ----------------- | ----------- |
| <span data-ttu-id="4aef2-533">메시지 앱 (SUT)</span><span class="sxs-lookup"><span data-stu-id="4aef2-533">Message app (the SUT)</span></span> | <span data-ttu-id="4aef2-534">*src/Razorororproject*</span><span class="sxs-lookup"><span data-stu-id="4aef2-534">*src/RazorPagesProject*</span></span> | <span data-ttu-id="4aef2-535">사용자가 메시지를 추가, 삭제, 삭제 및 분석할 수 있도록 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-535">Allows a user to add, delete one, delete all, and analyze messages.</span></span> |
| <span data-ttu-id="4aef2-536">응용 프로그램 테스트</span><span class="sxs-lookup"><span data-stu-id="4aef2-536">Test app</span></span> | <span data-ttu-id="4aef2-537">*테스트/RazorPagesProject. 테스트*</span><span class="sxs-lookup"><span data-stu-id="4aef2-537">*tests/RazorPagesProject.Tests*</span></span> | <span data-ttu-id="4aef2-538">SUT 테스트를 통합 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-538">Used to integration test the SUT.</span></span> |

<span data-ttu-id="4aef2-539">[Visual Studio](https://visualstudio.microsoft.com)와 같은 IDE의 기본 제공 테스트 기능을 사용 하 여 테스트를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-539">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="4aef2-540">[Visual Studio Code](https://code.visualstudio.com/) 또는 명령줄을 사용 하는 경우 *테스트/RazorPagesProject. 테스트* 디렉터리의 명령 프롬프트에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-540">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="4aef2-541">메시지 앱 (SUT) 조직</span><span class="sxs-lookup"><span data-stu-id="4aef2-541">Message app (SUT) organization</span></span>

<span data-ttu-id="4aef2-542">SUT는 다음과 같은 특징을 가진 Razor Pages 메시지 시스템입니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-542">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="4aef2-543">응용 프로그램의 인덱스 페이지 (*Pages/index. cshtml* 및 *pages/인덱스인*)는 메시지의 추가, 삭제 및 분석 (메시지당 평균 단어)을 제어 하는 UI 및 페이지 모델 메서드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-543">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="4aef2-544">메시지는 두 개의 속성 (@no__t (키) 및 `Text` (메시지)의 두 가지 속성을 사용 하 여 `Message` 클래스 (*데이터/메시지 .cs*)에서 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-544">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="4aef2-545">@No__t-0 속성은 필수 이며 200 자로 제한 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-545">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="4aef2-546">메시지는 [Entity Framework의 메모리 내 데이터베이스](/ef/core/providers/in-memory/)&#8224;를 사용 하 여 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-546">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="4aef2-547">앱에는 데이터베이스 컨텍스트 클래스의 DAL (데이터 액세스 계층)이 포함 되 고 `AppDbContext` (*data/AppDbContext*)이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-547">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="4aef2-548">응용 프로그램이 시작 될 때 데이터베이스가 비어 있는 경우 메시지 저장소는 세 개의 메시지로 초기화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-548">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="4aef2-549">앱에는 인증 된 사용자만 액세스할 수 있는 `/SecurePage`이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-549">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="4aef2-550">&#8224;EF 항목인 [InMemory 테스트](/ef/core/miscellaneous/testing/in-memory)는 MSTest를 사용 하 여 테스트에 메모리 내 데이터베이스를 사용 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-550">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="4aef2-551">이 항목에서는 [Xunit](https://xunit.github.io/) 테스트 프레임 워크를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-551">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="4aef2-552">여러 테스트 프레임 워크에서 테스트 개념 및 테스트 구현은 비슷하지만 동일 하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-552">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="4aef2-553">앱은 리포지토리 패턴을 사용 하지 않으며 [UoW (Unit Of Work) 패턴](https://martinfowler.com/eaaCatalog/unitOfWork.html)의 효과적인 예는 아니지만 Razor Pages 이러한 개발 패턴을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-553">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="4aef2-554">자세한 내용은 [인프라 지 속성 계층](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) 및 [테스트 컨트롤러 논리](/aspnet/core/mvc/controllers/testing) 디자인 (샘플에서는 리포지토리 패턴 구현)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="4aef2-554">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="4aef2-555">앱 구성 테스트</span><span class="sxs-lookup"><span data-stu-id="4aef2-555">Test app organization</span></span>

<span data-ttu-id="4aef2-556">테스트 앱은 테스트 */Razor 프로젝트* 의 콘솔 앱입니다. 테스트 디렉터리입니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-556">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="4aef2-557">응용 프로그램 디렉터리 테스트</span><span class="sxs-lookup"><span data-stu-id="4aef2-557">Test app directory</span></span> | <span data-ttu-id="4aef2-558">설명</span><span class="sxs-lookup"><span data-stu-id="4aef2-558">Description</span></span> |
| ------------------ | ----------- |
| <span data-ttu-id="4aef2-559">*BasicTests*</span><span class="sxs-lookup"><span data-stu-id="4aef2-559">*BasicTests*</span></span> | <span data-ttu-id="4aef2-560">*BasicTests.cs* 에는 라우팅을 위한 테스트 메서드, 인증 되지 않은 사용자가 보안 페이지에 액세스 하는 방법, GitHub 사용자 프로필 가져오기 및 프로필의 사용자 로그인 확인이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-560">*BasicTests.cs* contains test methods for routing, accessing a secure page by an unauthenticated user, and obtaining a GitHub user profile and checking the profile's user login.</span></span> |
| <span data-ttu-id="4aef2-561">*IntegrationTests*</span><span class="sxs-lookup"><span data-stu-id="4aef2-561">*IntegrationTests*</span></span> | <span data-ttu-id="4aef2-562">*IndexPageTests.cs* 사용자 지정 `WebApplicationFactory` 클래스를 사용 하 여 인덱스 페이지에 대 한 통합 테스트를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-562">*IndexPageTests.cs* contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> |
| <span data-ttu-id="4aef2-563">*도우미/유틸리티*</span><span class="sxs-lookup"><span data-stu-id="4aef2-563">*Helpers/Utilities*</span></span> | <ul><li><span data-ttu-id="4aef2-564">*Utilities.cs* 에는 테스트 데이터로 데이터베이스를 시드 하는 데 사용 되는 @no__t 1 메서드가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-564">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="4aef2-565">*HtmlHelpers.cs* 는 테스트 메서드에서 사용할 AngleSharp `IHtmlDocument`을 반환 하는 메서드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-565">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="4aef2-566">*HttpClientExtensions.cs* 는 `SendAsync`에 대 한 오버 로드를 제공 하 여 sut에 요청을 제출 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-566">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="4aef2-567">테스트 프레임 워크는 [Xunit](https://xunit.github.io/)입니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-567">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="4aef2-568">[AspNetCore. TestHost](/dotnet/api/microsoft.aspnetcore.testhost)( [testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver)포함)를 사용 하 여 통합 테스트를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-568">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="4aef2-569">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 패키지는 테스트 호스트와 테스트 서버를 구성 하는 데 사용 되기 때문에 @no__t 1 및 @no__t 패키지는 테스트 응용 프로그램의 프로젝트 파일 또는 테스트의 개발자 구성에서 직접 패키지 참조를 요구 하지 않습니다. 다운로드.</span><span class="sxs-lookup"><span data-stu-id="4aef2-569">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="4aef2-570">**테스트를 위해 데이터베이스 시드**</span><span class="sxs-lookup"><span data-stu-id="4aef2-570">**Seeding the database for testing**</span></span>

<span data-ttu-id="4aef2-571">통합 테스트는 일반적으로 테스트 실행 전에 데이터베이스에 작은 데이터 집합이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-571">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="4aef2-572">예를 들어 delete 테스트는 데이터베이스 레코드 삭제를 호출 하므로 삭제 요청이 성공 하려면 데이터베이스에 적어도 하나 이상의 레코드가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-572">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="4aef2-573">샘플 앱은 테스트가 실행 될 때 사용할 수 있는 *Utilities.cs* 의 세 가지 메시지를 사용 하 여 데이터베이스를 시드 합니다.</span><span class="sxs-lookup"><span data-stu-id="4aef2-573">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="4aef2-574">추가 자료</span><span class="sxs-lookup"><span data-stu-id="4aef2-574">Additional resources</span></span>

* [<span data-ttu-id="4aef2-575">단위 테스트</span><span class="sxs-lookup"><span data-stu-id="4aef2-575">Unit tests</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* [<span data-ttu-id="4aef2-576">Razor 페이지 단위 테스트</span><span class="sxs-lookup"><span data-stu-id="4aef2-576">Razor Pages unit tests</span></span>](xref:test/razor-pages-tests)
* [<span data-ttu-id="4aef2-577">미들웨어</span><span class="sxs-lookup"><span data-stu-id="4aef2-577">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="4aef2-578">테스트 컨트롤러</span><span class="sxs-lookup"><span data-stu-id="4aef2-578">Test controllers</span></span>](xref:mvc/controllers/testing)
