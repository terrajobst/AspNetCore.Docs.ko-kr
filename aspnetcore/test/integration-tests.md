---
title: ASP.NET Core의 통합 테스트
author: guardrex
description: 통합 테스트를 사용하여 앱의 구성 요소가 데이터베이스, 파일 시스템 및 네트워크를 비롯한 인프라 수준에서 제대로 작동하는지 확인하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/28/2019
uid: test/integration-tests
ms.openlocfilehash: 33f3e29bc649fa65efdff0c47e54a83662005577
ms.sourcegitcommit: de0fc77487a4d342bcc30965ec5c142d10d22c03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73143371"
---
# <a name="integration-tests-in-aspnet-core"></a><span data-ttu-id="2e99d-103">ASP.NET Core의 통합 테스트</span><span class="sxs-lookup"><span data-stu-id="2e99d-103">Integration tests in ASP.NET Core</span></span>

<span data-ttu-id="2e99d-104">By [Luke Latham](https://github.com/guardrex), [Javier Calvarro e](https://github.com/javiercn), [Steve Smith](https://ardalis.com/)및 [jos van der Til](https://jvandertil.nl)</span><span class="sxs-lookup"><span data-stu-id="2e99d-104">By [Luke Latham](https://github.com/guardrex), [Javier Calvarro Nelson](https://github.com/javiercn), [Steve Smith](https://ardalis.com/), and [Jos van der Til](https://jvandertil.nl)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2e99d-105">통합 테스트는 응용 프로그램의 구성 요소가 응용 프로그램의 지원 인프라 (예: 데이터베이스, 파일 시스템 및 네트워크)를 포함 하는 수준에서 올바르게 작동 하는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-105">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="2e99d-106">ASP.NET Core는 테스트 웹 호스트 및 메모리 내 테스트 서버에서 단위 테스트 프레임 워크를 사용 하는 통합 테스트를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-106">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="2e99d-107">이 항목에서는 단위 테스트에 대해 기본적으로 이해 하 고 있다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-107">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="2e99d-108">테스트 개념을 잘 모르는 경우 [.Net Core의 단위 테스트 및 .NET Standard](/dotnet/core/testing/) 항목 및 연결 된 콘텐츠를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2e99d-108">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="2e99d-109">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2e99d-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="2e99d-110">샘플 앱은 Razor Pages 앱 이며 Razor Pages를 기본적으로 이해 하 고 있다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-110">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="2e99d-111">Razor Pages에 익숙하지 않은 경우 다음 항목을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2e99d-111">If unfamiliar with Razor Pages, see the following topics:</span></span>

* [<span data-ttu-id="2e99d-112">Razor 페이지 소개</span><span class="sxs-lookup"><span data-stu-id="2e99d-112">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="2e99d-113">Razor 페이지 시작</span><span class="sxs-lookup"><span data-stu-id="2e99d-113">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="2e99d-114">Razor 페이지 단위 테스트</span><span class="sxs-lookup"><span data-stu-id="2e99d-114">Razor Pages unit tests</span></span>](xref:test/razor-pages-tests)

> [!NOTE]
> <span data-ttu-id="2e99d-115">SPAs 테스트를 위해 브라우저를 자동화할 수 있는 [Selenium](https://www.seleniumhq.org/)와 같은 도구를 권장 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-115">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="2e99d-116">통합 테스트 소개</span><span class="sxs-lookup"><span data-stu-id="2e99d-116">Introduction to integration tests</span></span>

<span data-ttu-id="2e99d-117">통합 테스트는 [단위 테스트](/dotnet/core/testing/)보다 더 광범위 한 수준에서 앱의 구성 요소를 평가 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-117">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="2e99d-118">단위 테스트는 개별 클래스 메서드와 같은 격리 된 소프트웨어 구성 요소를 테스트 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-118">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="2e99d-119">통합 테스트는 두 개 이상의 앱 구성 요소가 함께 작동 하 여 요청을 완전히 처리 하는 데 필요한 모든 구성 요소를 포함 하 여 예상 결과를 생성 하는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-119">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="2e99d-120">이러한 광범위 한 테스트는 다음과 같은 구성 요소를 포함 하 여 앱의 인프라 및 전체 프레임 워크를 테스트 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-120">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="2e99d-121">데이터베이스</span><span class="sxs-lookup"><span data-stu-id="2e99d-121">Database</span></span>
* <span data-ttu-id="2e99d-122">파일 시스템</span><span class="sxs-lookup"><span data-stu-id="2e99d-122">File system</span></span>
* <span data-ttu-id="2e99d-123">네트워크 어플라이언스</span><span class="sxs-lookup"><span data-stu-id="2e99d-123">Network appliances</span></span>
* <span data-ttu-id="2e99d-124">요청-응답 파이프라인</span><span class="sxs-lookup"><span data-stu-id="2e99d-124">Request-response pipeline</span></span>

<span data-ttu-id="2e99d-125">단위 테스트는 인프라 구성 요소 대신 *fakes* 또는 *모의 개체*라고 하는 구성 된 구성 요소를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-125">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="2e99d-126">단위 테스트와 달리 통합 테스트는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-126">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="2e99d-127">프로덕션 환경에서 앱이 사용 하는 실제 구성 요소를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-127">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="2e99d-128">더 많은 코드와 데이터 처리가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-128">Require more code and data processing.</span></span>
* <span data-ttu-id="2e99d-129">실행 하는 데 시간이 더 오래 걸립니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-129">Take longer to run.</span></span>

<span data-ttu-id="2e99d-130">따라서 가장 중요 한 인프라 시나리오에 대 한 통합 테스트 사용을 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-130">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="2e99d-131">단위 테스트 또는 통합 테스트를 사용 하 여 동작을 테스트할 수 있는 경우 단위 테스트를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-131">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="2e99d-132">데이터베이스 및 파일 시스템을 사용 하 여 모든 가능한 데이터 순열 및 파일 액세스에 대해 통합 테스트를 작성 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="2e99d-132">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="2e99d-133">응용 프로그램 전체에서 데이터베이스 및 파일 시스템과 상호 작용 하는 위치에 관계 없이, 일반적으로 데이터베이스 및 파일 시스템 구성 요소를 적절 하 게 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-133">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="2e99d-134">이러한 구성 요소와 상호 작용 하는 메서드 논리의 루틴 테스트에 단위 테스트를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-134">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="2e99d-135">단위 테스트에서 인프라 fakes/mock를 사용 하면 테스트 실행 속도가 빨라집니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-135">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="2e99d-136">통합 테스트에 대 한 토론에서 테스트 된 프로젝트를 종종 *테스트 중인 시스템*또는 short의 경우 "sut" 라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-136">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="2e99d-137">*이 항목 전체에서 "SUT"를 사용 하 여 테스트 된 ASP.NET Core 앱을 참조 합니다.*</span><span class="sxs-lookup"><span data-stu-id="2e99d-137">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="2e99d-138">ASP.NET Core 통합 테스트</span><span class="sxs-lookup"><span data-stu-id="2e99d-138">ASP.NET Core integration tests</span></span>

<span data-ttu-id="2e99d-139">ASP.NET Core의 통합 테스트에는 다음이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-139">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="2e99d-140">테스트 프로젝트는 테스트를 포함 하 고 실행 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-140">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="2e99d-141">테스트 프로젝트에는 SUT에 대 한 참조가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-141">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="2e99d-142">테스트 프로젝트는 SUT에 대 한 테스트 웹 호스트를 만들고 테스트 서버 클라이언트를 사용 하 여 SUT를 사용 하 여 요청 및 응답을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-142">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="2e99d-143">Test runner는 테스트를 실행 하 고 테스트 결과를 보고 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-143">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="2e99d-144">통합 테스트는 일반적인 *정렬*, 작업 및 *어설션* 테스트 *단계를 포함*하는 이벤트 시퀀스를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-144">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="2e99d-145">SUT의 웹 호스트가 구성 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-145">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="2e99d-146">앱에 요청을 제출 하는 테스트 서버 클라이언트가 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-146">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="2e99d-147">테스트 응용 프로그램에서 요청을 준비 하는 *정렬* 테스트 단계를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-147">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="2e99d-148">작업 *테스트 단계가* 실행 됩니다. 즉, 클라이언트가 요청을 제출 하 고 응답을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-148">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="2e99d-149">*Assert* 테스트 단계가 실행 됩니다. *실제* 응답은 *예상* 된 응답에 따라 *통과* 또는 *실패* 의 유효성을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-149">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="2e99d-150">모든 테스트가 실행 될 때까지 프로세스가 계속 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-150">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="2e99d-151">테스트 결과가 보고 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-151">The test results are reported.</span></span>

<span data-ttu-id="2e99d-152">일반적으로 테스트 웹 호스트는 테스트 실행을 위해 응용 프로그램의 일반 웹 호스트와 다르게 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-152">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="2e99d-153">예를 들어 다른 데이터베이스 또는 다른 앱 설정을 테스트에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-153">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="2e99d-154">테스트 웹 호스트 및 메모리 내 테스트 서버 ([Testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver))와 같은 인프라 구성 요소는 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 패키지에서 제공 하거나 관리 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-154">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="2e99d-155">이 패키지를 사용 하면 테스트 생성 및 실행이 간소화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-155">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="2e99d-156">`Microsoft.AspNetCore.Mvc.Testing` 패키지는 다음 작업을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-156">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="2e99d-157">SUT의 종속성 파일 ( *. deps*)을 테스트 프로젝트의 *bin* 디렉터리에 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-157">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="2e99d-158">테스트를 실행할 때 정적 파일 및 페이지/뷰가 검색 되도록 [콘텐츠 루트](xref:fundamentals/index#content-root) 를 sut의 프로젝트 루트로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-158">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="2e99d-159">`TestServer`를 사용 하 여 SUT를 간소화 하는 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) 클래스를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-159">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="2e99d-160">[단위 테스트](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) 설명서에서는 테스트를 실행 하는 방법에 대 한 자세한 지침과 테스트 및 테스트 클래스의 이름을 설정 하는 방법에 대 한 자세한 지침과 함께 테스트 프로젝트 및 test runner를 설정 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-160">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="2e99d-161">앱에 대 한 테스트 프로젝트를 만들 때 통합 테스트에서 여러 프로젝트로 단위 테스트를 분리 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-161">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="2e99d-162">이를 통해 인프라 테스트 구성 요소가 실수로 단위 테스트에 포함 되지 않도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-162">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="2e99d-163">단위 및 통합 테스트를 분리 하 여 실행 되는 테스트 집합을 제어할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-163">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="2e99d-164">Razor Pages 앱과 MVC 앱의 테스트에 대 한 구성의 차이는 거의 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-164">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="2e99d-165">유일한 차이점은 테스트의 이름을 지정 하는 방법 뿐입니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-165">The only difference is in how the tests are named.</span></span> <span data-ttu-id="2e99d-166">Razor Pages 앱에서 페이지 끝점의 테스트는 일반적으로 페이지 모델 클래스 (예: 인덱스 페이지에 대 한 구성 요소 통합을 테스트 하기 위해 `IndexPageTests`) 뒤에 이름이 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-166">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="2e99d-167">MVC 앱에서 테스트는 일반적으로 컨트롤러 클래스로 구성 되며 테스트 하는 컨트롤러 뒤에 이름이 지정 됩니다 (예: `HomeControllerTests`은 Home 컨트롤러에 대 한 구성 요소 통합 테스트).</span><span class="sxs-lookup"><span data-stu-id="2e99d-167">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="2e99d-168">응용 프로그램 테스트 필수 조건</span><span class="sxs-lookup"><span data-stu-id="2e99d-168">Test app prerequisites</span></span>

<span data-ttu-id="2e99d-169">테스트 프로젝트는 다음을 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-169">The test project must:</span></span>

* <span data-ttu-id="2e99d-170">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 패키지를 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-170">Reference the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span>
* <span data-ttu-id="2e99d-171">프로젝트 파일 (`<Project Sdk="Microsoft.NET.Sdk.Web">`)에서 웹 SDK를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-171">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span>

<span data-ttu-id="2e99d-172">이러한 필수 구성 요소는 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)에서 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-172">These prerequisites can be seen in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="2e99d-173">*테스트/RazorPagesProject. 테스트/razor*  프로젝트. .csproj 파일을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-173">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="2e99d-174">샘플 앱은 [Xunit](https://xunit.github.io/) 테스트 프레임 워크 및 [AngleSharp](https://anglesharp.github.io/) 파서 라이브러리를 사용 하므로 샘플 앱은 다음을 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-174">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="2e99d-175">xunit</span><span class="sxs-lookup"><span data-stu-id="2e99d-175">xunit</span></span>](https://www.nuget.org/packages/xunit)
* [<span data-ttu-id="2e99d-176">xunit. visualstudio</span><span class="sxs-lookup"><span data-stu-id="2e99d-176">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [<span data-ttu-id="2e99d-177">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="2e99d-177">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp)

<span data-ttu-id="2e99d-178">Entity Framework Core는 테스트에도 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-178">Entity Framework Core is also used in the tests.</span></span> <span data-ttu-id="2e99d-179">앱 참조:</span><span class="sxs-lookup"><span data-stu-id="2e99d-179">The app references:</span></span>

* [<span data-ttu-id="2e99d-180">AspNetCore. Microsoft.entityframeworkcore.tools.dotnet</span><span class="sxs-lookup"><span data-stu-id="2e99d-180">Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* [<span data-ttu-id="2e99d-181">AspNetCore. Microsoft.entityframeworkcore.tools.dotnet</span><span class="sxs-lookup"><span data-stu-id="2e99d-181">Microsoft.AspNetCore.Identity.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)
* [<span data-ttu-id="2e99d-182">Microsoft.entityframeworkcore.tools.dotnet</span><span class="sxs-lookup"><span data-stu-id="2e99d-182">Microsoft.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [<span data-ttu-id="2e99d-183">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="2e99d-183">Microsoft.EntityFrameworkCore.InMemory</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [<span data-ttu-id="2e99d-184">Microsoft.entityframeworkcore.tools.dotnet</span><span class="sxs-lookup"><span data-stu-id="2e99d-184">Microsoft.EntityFrameworkCore.Tools</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a><span data-ttu-id="2e99d-185">SUT 환경</span><span class="sxs-lookup"><span data-stu-id="2e99d-185">SUT environment</span></span>

<span data-ttu-id="2e99d-186">SUT의 [환경이](xref:fundamentals/environments) 설정 되지 않은 경우 환경의 기본값은 Development로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-186">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="2e99d-187">기본 WebApplicationFactory를 사용 하는 기본 테스트</span><span class="sxs-lookup"><span data-stu-id="2e99d-187">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="2e99d-188">[WebApplicationFactory\<TEntryPoint >](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) 는 통합 테스트용 [testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver) 를 만드는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-188">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="2e99d-189">`TEntryPoint`은 SUT의 진입점 클래스입니다. 일반적으로 `Startup` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-189">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="2e99d-190">테스트 클래스는 클래스에 테스트를 포함 하 고 클래스의 테스트에서 공유 개체 인스턴스를 제공 하는 클래스 *fixture* 인터페이스 ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture))를 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-190">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

<span data-ttu-id="2e99d-191">다음 테스트 클래스 `BasicTests`에서는 `WebApplicationFactory`를 사용 하 여 SUT를 부트스트랩 하 고 `Get_EndpointsReturnSuccessAndCorrectContentType`테스트 메서드에 [Httpclient](/dotnet/api/system.net.http.httpclient) 를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-191">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="2e99d-192">메서드는 응답 상태 코드가 성공 (200-299 범위의 상태 코드) 되었는지 확인 하 고 `Content-Type` 헤더는 여러 앱 페이지에 대해 `text/html; charset=utf-8`입니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-192">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="2e99d-193">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) 는 리디렉션을 자동으로 따르며 쿠키를 처리 하는 `HttpClient`의 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-193">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="2e99d-194">기본적으로 [Gdpr 동의 정책을](xref:security/gdpr) 사용 하는 경우 요청에서 필수가 아닌 쿠키가 유지 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-194">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="2e99d-195">TempData 공급자가 사용 하는 쿠키와 같이 필수가 아닌 쿠키를 유지 하려면 테스트에서 필수로 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-195">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="2e99d-196">쿠키를 필수로 표시 하는 방법에 대 한 지침은 [필수 쿠키](xref:security/gdpr#essential-cookies)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2e99d-196">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="2e99d-197">WebApplicationFactory 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="2e99d-197">Customize WebApplicationFactory</span></span>

<span data-ttu-id="2e99d-198">웹 호스트 구성은 하나 이상의 사용자 지정 팩터리를 만들기 위해 `WebApplicationFactory`에서 상속 하 여 테스트 클래스와 독립적으로 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-198">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="2e99d-199">`WebApplicationFactory`에서 상속 하 고 [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)를 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-199">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="2e99d-200">[Iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) 를 사용 하면 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices)를 사용 하 여 서비스 컬렉션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-200">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="2e99d-201">[샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) 에서의 데이터베이스 시드는 `InitializeDbForTests` 메서드로 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-201">Database seeding in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="2e99d-202">메서드는 [통합 테스트 샘플: 테스트 앱 구성](#test-app-organization) 섹션에 설명 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-202">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

   <span data-ttu-id="2e99d-203">SUT의 데이터베이스 컨텍스트가 `Startup.ConfigureServices` 메서드에 등록 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-203">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="2e99d-204">응용 프로그램의 `builder.ConfigureServices` 콜백은 응용 프로그램의 `Startup.ConfigureServices` 코드가 실행 된 *후* 에 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-204">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="2e99d-205">앱 데이터베이스와 다른 테스트에 다른 데이터베이스를 사용 하려면 앱의 데이터베이스 컨텍스트를 `builder.ConfigureServices`으로 바꾸어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-205">To use a different database for the tests than the app's database, the app's database context must be replaced in `builder.ConfigureServices`.</span></span>

   <span data-ttu-id="2e99d-206">샘플 앱은 데이터베이스 컨텍스트에 대 한 서비스 설명자를 찾고 설명자를 사용 하 여 서비스 등록을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-206">The sample app finds the service descriptor for the database context and uses the descriptor to remove the service registration.</span></span> <span data-ttu-id="2e99d-207">그런 다음, 팩터리는 테스트를 위해 메모리 내 데이터베이스를 사용 하는 새 `ApplicationDbContext`을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-207">Next, the factory adds a new `ApplicationDbContext` that uses an in-memory database for the tests.</span></span>

   <span data-ttu-id="2e99d-208">메모리 내 데이터베이스가 아닌 다른 데이터베이스에 연결 하려면 `UseInMemoryDatabase` 호출을 변경 하 여 컨텍스트를 다른 데이터베이스에 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-208">To connect to a different database than the in-memory database, change the `UseInMemoryDatabase` call to connect the context to a different database.</span></span> <span data-ttu-id="2e99d-209">SQL Server 테스트 데이터베이스를 사용 하려면 다음을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-209">To use a SQL Server test database:</span></span>

   * <span data-ttu-id="2e99d-210">프로젝트 파일에서 [Microsoft.entityframeworkcore.tools.dotnet](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet 패키지를 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-210">Reference the [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet package in the project file.</span></span>
   * <span data-ttu-id="2e99d-211">데이터베이스에 대 한 연결 문자열을 사용 하 여 `UseSqlServer`을 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-211">Call `UseSqlServer` with a connection string to the database.</span></span>

   ```csharp
   services.AddDbContext<ApplicationDbContext>((options, context) => 
   {
       context.UseSqlServer(
           Configuration.GetConnectionString("TestingDbConnectionString"));
   });
   ```

2. <span data-ttu-id="2e99d-212">테스트 클래스에서 사용자 지정 `CustomWebApplicationFactory`을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-212">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="2e99d-213">다음 예제에서는 `IndexPageTests` 클래스에서 팩터리를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-213">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="2e99d-214">샘플 응용 프로그램의 클라이언트는 다음 리디렉션이 `HttpClient` 않도록 구성 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-214">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="2e99d-215">[모의 인증](#mock-authentication) 섹션의 뒷부분에서 설명한 대로 테스트에서 앱의 첫 번째 응답의 결과를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-215">As explained later in the [Mock authentication](#mock-authentication) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="2e99d-216">첫 번째 응답은 `Location` 헤더를 사용 하는 이러한 테스트 대부분의 리디렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-216">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="2e99d-217">일반적인 테스트는 `HttpClient` 및 도우미 메서드를 사용 하 여 요청 및 응답을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-217">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="2e99d-218">SUT에 대 한 POST 요청은 앱의 [데이터 보호 방지 시스템이](xref:security/data-protection/introduction)자동으로 수행 하는 위조 방지 검사를 충족 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-218">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="2e99d-219">테스트의 POST 요청을 정렬 하기 위해 테스트 앱은 다음을 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-219">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="2e99d-220">페이지에 대 한 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-220">Make a request for the page.</span></span>
1. <span data-ttu-id="2e99d-221">위조 방지 쿠키를 구문 분석 하 고 응답에서 유효성 검사 토큰을 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-221">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="2e99d-222">위조 방지 쿠키 및 요청 유효성 검사 토큰을 사용 하 여 POST 요청을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-222">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="2e99d-223">`SendAsync` 도우미 확장 메서드 (*도우미/HttpClientExtensions*) 및 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) 의 `GetDocumentAsync` 도우미 메서드 (*도우미/htmlhelpers*)는 [AngleSharp](https://anglesharp.github.io/) 파서를 사용 하 여 위조 방지 검사를 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-223">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="2e99d-224">`GetDocumentAsync` &ndash; [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) 수신 하 고 `IHtmlDocument`를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-224">`GetDocumentAsync` &ndash; Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="2e99d-225">`GetDocumentAsync`은 원래 `HttpResponseMessage`에 따라 *가상 응답* 을 준비 하는 팩터리를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-225">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="2e99d-226">자세한 내용은 [AngleSharp 설명서](https://github.com/AngleSharp/AngleSharp#documentation)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2e99d-226">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="2e99d-227">[HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) 및 [SendAsync (HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) 를 작성 하는 `SendAsync` 확장 `HttpClient` 메서드를 작성 하 여 sut에 요청을 제출 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-227">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="2e99d-228">`SendAsync`에 대 한 오버 로드는 HTML 폼 (`IHtmlFormElement`) 및 다음을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-228">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="2e99d-229">양식의 제출 단추 (`IHtmlElement`)</span><span class="sxs-lookup"><span data-stu-id="2e99d-229">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="2e99d-230">양식 값 컬렉션 (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="2e99d-230">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="2e99d-231">제출 단추 (`IHtmlElement`) 및 폼 값 (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="2e99d-231">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="2e99d-232">[AngleSharp](https://anglesharp.github.io/) 는이 항목 및 샘플 앱에서 데모용으로 사용 되는 타사 구문 분석 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-232">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="2e99d-233">AngleSharp는 ASP.NET Core 앱의 통합 테스트에 지원 되지 않거나 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-233">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="2e99d-234">[HAP (Html 민첩성 팩)](https://html-agility-pack.net/)와 같은 다른 파서를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-234">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="2e99d-235">또 다른 방법은 위조 방지 시스템의 요청 확인 토큰을 처리 하 고 위조 쿠키를 직접 방지 하는 코드를 작성 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-235">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="2e99d-236">WithWebHostBuilder를 사용 하 여 클라이언트 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="2e99d-236">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="2e99d-237">테스트 메서드 내에서 추가 구성이 필요한 경우 [Withwebhostbuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) 는 구성으로 추가로 사용자 지정 된 [iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) 를 사용 하 여 새 `WebApplicationFactory`을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-237">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="2e99d-238">[샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) 의 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 테스트 메서드는 `WithWebHostBuilder`사용 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-238">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="2e99d-239">이 테스트는 SUT에서 양식 제출을 트리거하여 데이터베이스에서 레코드 삭제를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-239">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="2e99d-240">`IndexPageTests` 클래스의 다른 테스트는 데이터베이스의 모든 레코드를 삭제 하는 작업을 수행 하 고 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 메서드 이전에 실행 될 수 있기 때문에이 테스트 메서드에서 데이터베이스를 다시 시드됩니다 하 여 SUT가 삭제할 레코드가 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-240">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="2e99d-241">SUT에서 `messages` 폼의 첫 번째 삭제 단추를 선택 하면 SUT에 대 한 요청에서 시뮬레이션 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-241">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="2e99d-242">클라이언트 옵션</span><span class="sxs-lookup"><span data-stu-id="2e99d-242">Client options</span></span>

<span data-ttu-id="2e99d-243">다음 표에서는 `HttpClient` 인스턴스를 만들 때 사용할 수 있는 기본 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) 을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-243">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="2e99d-244">옵션</span><span class="sxs-lookup"><span data-stu-id="2e99d-244">Option</span></span> | <span data-ttu-id="2e99d-245">설명</span><span class="sxs-lookup"><span data-stu-id="2e99d-245">Description</span></span> | <span data-ttu-id="2e99d-246">기본</span><span class="sxs-lookup"><span data-stu-id="2e99d-246">Default</span></span> |
| ------ | ----------- | ------- |
| [<span data-ttu-id="2e99d-247">AllowAutoRedirect</span><span class="sxs-lookup"><span data-stu-id="2e99d-247">AllowAutoRedirect</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | <span data-ttu-id="2e99d-248">`HttpClient` 인스턴스가 자동으로 리디렉션 응답을 따르는지 여부를 가져오거나 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-248">Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span> | `true` |
| [<span data-ttu-id="2e99d-249">BaseAddress</span><span class="sxs-lookup"><span data-stu-id="2e99d-249">BaseAddress</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | <span data-ttu-id="2e99d-250">`HttpClient` 인스턴스의 기본 주소를 가져오거나 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-250">Gets or sets the base address of `HttpClient` instances.</span></span> | `http://localhost` |
| [<span data-ttu-id="2e99d-251">HandleCookies</span><span class="sxs-lookup"><span data-stu-id="2e99d-251">HandleCookies</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | <span data-ttu-id="2e99d-252">`HttpClient` 인스턴스에서 쿠키를 처리할지 여부를 가져오거나 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-252">Gets or sets whether `HttpClient` instances should handle cookies.</span></span> | `true` |
| [<span data-ttu-id="2e99d-253">Max자동 리디렉션</span><span class="sxs-lookup"><span data-stu-id="2e99d-253">MaxAutomaticRedirections</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | <span data-ttu-id="2e99d-254">`HttpClient` 인스턴스에서 따라야 하는 리디렉션 응답의 최대 수를 가져오거나 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-254">Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> | <span data-ttu-id="2e99d-255">7</span><span class="sxs-lookup"><span data-stu-id="2e99d-255">7</span></span> |

<span data-ttu-id="2e99d-256">`WebApplicationFactoryClientOptions` 클래스를 만들어 [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) 메서드에 전달 합니다 (기본값은 코드 예제에 표시 됨).</span><span class="sxs-lookup"><span data-stu-id="2e99d-256">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="2e99d-257">모의 서비스 삽입</span><span class="sxs-lookup"><span data-stu-id="2e99d-257">Inject mock services</span></span>

<span data-ttu-id="2e99d-258">호스트 작성기에서 [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) 에 대 한 호출을 사용 하 여 테스트에서 서비스를 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-258">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="2e99d-259">**모의 서비스를 삽입 하려면 SUT에 `Startup.ConfigureServices` 메서드를 사용 하는 `Startup` 클래스가 있어야 합니다.**</span><span class="sxs-lookup"><span data-stu-id="2e99d-259">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="2e99d-260">샘플 SUT에는 견적을 반환 하는 범위 지정 서비스가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-260">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="2e99d-261">인덱스 페이지를 요청 하면 따옴표가 인덱스 페이지의 숨겨진 필드에 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-261">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="2e99d-262">*Services/IQuoteService*:</span><span class="sxs-lookup"><span data-stu-id="2e99d-262">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="2e99d-263">*Services/QuoteService*:</span><span class="sxs-lookup"><span data-stu-id="2e99d-263">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="2e99d-264">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2e99d-264">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="2e99d-265">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="2e99d-265">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="2e99d-266">*Pages/Index .cs*:</span><span class="sxs-lookup"><span data-stu-id="2e99d-266">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="2e99d-267">다음 태그는 SUT 앱이 실행 될 때 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-267">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="2e99d-268">통합 테스트에서 서비스 및 견적 주입을 테스트 하기 위해 테스트를 통해 모의 서비스를 SUT에 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-268">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="2e99d-269">모의 서비스는 앱의 `QuoteService`을 테스트 앱에서 제공 하는 서비스 (`TestQuoteService` 이라고 함)로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-269">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="2e99d-270">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="2e99d-270">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="2e99d-271">`ConfigureTestServices`이 호출 되 고 범위가 지정 된 서비스가 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-271">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="2e99d-272">테스트 실행 중에 생성 된 태그는 `TestQuoteService`에서 제공 된 따옴표 텍스트를 반영 하므로 어설션이 통과 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-272">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a><span data-ttu-id="2e99d-273">모의 인증</span><span class="sxs-lookup"><span data-stu-id="2e99d-273">Mock authentication</span></span>

<span data-ttu-id="2e99d-274">`AuthTests` 클래스의 테스트는 보안 끝점을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-274">Tests in the `AuthTests` class check that a secure endpoint:</span></span>

* <span data-ttu-id="2e99d-275">인증 되지 않은 사용자를 앱의 로그인 페이지로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-275">Redirects an unauthenticated user to the app's Login page.</span></span>
* <span data-ttu-id="2e99d-276">인증 된 사용자에 대 한 콘텐츠를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-276">Returns content for an authenticated user.</span></span>

<span data-ttu-id="2e99d-277">SUT에서 `/SecurePage` 페이지는 [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) 규칙을 사용 하 여 페이지에 [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) 를 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-277">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="2e99d-278">자세한 내용은 [Razor Pages 권한 부여 규칙](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2e99d-278">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="2e99d-279">`Get_SecurePageRedirectsAnUnauthenticatedUser` 테스트에서 [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) 를 `false`으로 설정 하 여 리디렉션을 허용 하지 않도록 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) 를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-279">In the `Get_SecurePageRedirectsAnUnauthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

<span data-ttu-id="2e99d-280">클라이언트에서 리디렉션을 따르도록 허용 하지 않으면 다음 검사를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-280">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="2e99d-281">SUT에서 반환 된 상태 코드는 HttpStatusCode 결과에 대해 확인할 수 있습니다 [.](/dotnet/api/system.net.httpstatuscode) 이는 로그인 페이지에 대 한 리디렉션 이후의 최종 상태 코드는 [HttpStatusCode](/dotnet/api/system.net.httpstatuscode)입니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-281">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="2e99d-282">응답 헤더의 `Location` 헤더 값은 마지막 로그인 페이지 응답이 아닌 `http://localhost/Identity/Account/Login`로 시작 하는 것을 확인 하기 위해 확인 됩니다 .이 값은 `Location` 헤더가 존재 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-282">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="2e99d-283">테스트 앱은 인증 및 권한 부여의 측면을 테스트 하기 위해 [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) 에서 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>를 mock 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-283">The test app can mock an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) in order to test aspects of authentication and authorization.</span></span> <span data-ttu-id="2e99d-284">최소한의 시나리오는 AuthenticateResult을 반환 [합니다.](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*)</span><span class="sxs-lookup"><span data-stu-id="2e99d-284">A minimal scenario returns an [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

<span data-ttu-id="2e99d-285">`TestAuthHandler`는 인증 체계가 `ConfigureTestServices`에 대해 등록 된 `AddAuthentication` `Test`으로 설정 된 경우 사용자를 인증 하기 위해 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-285">The `TestAuthHandler` is called to authenticate a user when the authentication scheme is set to `Test` where `AddAuthentication` is registered for `ConfigureTestServices`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

<span data-ttu-id="2e99d-286">`WebApplicationFactoryClientOptions`에 대 한 자세한 내용은 [클라이언트 옵션](#client-options) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2e99d-286">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="2e99d-287">환경 설정</span><span class="sxs-lookup"><span data-stu-id="2e99d-287">Set the environment</span></span>

<span data-ttu-id="2e99d-288">기본적으로 SUT의 호스트 및 앱 환경은 개발 환경을 사용 하도록 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-288">By default, the SUT's host and app environment is configured to use the Development environment.</span></span> <span data-ttu-id="2e99d-289">SUT의 환경을 재정의 하려면:</span><span class="sxs-lookup"><span data-stu-id="2e99d-289">To override the SUT's environment:</span></span>

* <span data-ttu-id="2e99d-290">`ASPNETCORE_ENVIRONMENT` 환경 변수를 설정 합니다 (예: `Staging`, `Production`또는 기타 사용자 지정 값 (예: `Testing`)).</span><span class="sxs-lookup"><span data-stu-id="2e99d-290">Set the `ASPNETCORE_ENVIRONMENT` environment variable (for example, `Staging`, `Production`, or other custom value, such as `Testing`).</span></span>
* <span data-ttu-id="2e99d-291">`ASPNETCORE`를 접두사로 사용 하는 환경 변수를 읽도록 테스트 앱의 `CreateHostBuilder`를 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-291">Override `CreateHostBuilder` in the test app to read environment variables prefixed with `ASPNETCORE`.</span></span>

```csharp
protected override IHostBuilder CreateHostBuilder() => 
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="2e99d-292">테스트 인프라가 앱 콘텐츠 루트 경로를 유추 하는 방법</span><span class="sxs-lookup"><span data-stu-id="2e99d-292">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="2e99d-293">`WebApplicationFactory` 생성자는 `TEntryPoint` 어셈블리 `System.Reflection.Assembly.FullName`와 같은 키를 사용 하 여 통합 테스트가 포함 된 어셈블리에서 [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) 를 검색 하 여 앱 [콘텐츠 루트](xref:fundamentals/index#content-root) 경로를 유추 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-293">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="2e99d-294">올바른 키를 가진 특성을 찾을 수 없는 경우 `WebApplicationFactory`은 솔루션 파일 ( *.sln*)을 검색 하는 것으로 대체 되 고 `TEntryPoint` 어셈블리 이름을 솔루션 디렉터리에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-294">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="2e99d-295">응용 프로그램 루트 디렉터리 (콘텐츠 루트 경로)는 뷰와 콘텐츠 파일을 검색 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-295">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="2e99d-296">섀도 복사 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="2e99d-296">Disable shadow copying</span></span>

<span data-ttu-id="2e99d-297">섀도 복사를 수행 하면 테스트가 출력 디렉터리와 다른 디렉터리에 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-297">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="2e99d-298">테스트가 제대로 작동 하려면 섀도 복사를 사용 하지 않도록 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-298">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="2e99d-299">[샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) 은 xunit을 사용 하 고 올바른 구성 설정을 사용 하 여 xunit *. runner* 파일을 포함 하 여 xunit에 대해 섀도 복사를 사용 하지 않도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-299">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="2e99d-300">자세한 내용은 [JSON을 사용 하 여 xUnit 구성](https://xunit.github.io/docs/configuring-with-json.html)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2e99d-300">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="2e99d-301">다음 콘텐츠를 사용 하 여 테스트 프로젝트의 루트에 *xunit.*</span><span class="sxs-lookup"><span data-stu-id="2e99d-301">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a><span data-ttu-id="2e99d-302">개체 삭제</span><span class="sxs-lookup"><span data-stu-id="2e99d-302">Disposal of objects</span></span>

<span data-ttu-id="2e99d-303">`IClassFixture` 구현에 대 한 테스트를 실행 한 후 xUnit에서 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)을 삭제 하면 [Testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver) 및 [httpclient](/dotnet/api/system.net.http.httpclient) 가 삭제 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-303">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="2e99d-304">개발자가 인스턴스화한 개체를 삭제 해야 하는 경우 `IClassFixture` 구현에서 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-304">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="2e99d-305">자세한 내용은 [Dispose 메서드 구현](/dotnet/standard/garbage-collection/implementing-dispose)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2e99d-305">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="2e99d-306">통합 테스트 샘플</span><span class="sxs-lookup"><span data-stu-id="2e99d-306">Integration tests sample</span></span>

<span data-ttu-id="2e99d-307">[샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) 은 다음과 같은 두 개의 앱으로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-307">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="2e99d-308">App</span><span class="sxs-lookup"><span data-stu-id="2e99d-308">App</span></span> | <span data-ttu-id="2e99d-309">프로젝트 디렉터리</span><span class="sxs-lookup"><span data-stu-id="2e99d-309">Project directory</span></span> | <span data-ttu-id="2e99d-310">설명</span><span class="sxs-lookup"><span data-stu-id="2e99d-310">Description</span></span> |
| --- | ----------------- | ----------- |
| <span data-ttu-id="2e99d-311">메시지 앱 (SUT)</span><span class="sxs-lookup"><span data-stu-id="2e99d-311">Message app (the SUT)</span></span> | <span data-ttu-id="2e99d-312">*src/Razorororproject*</span><span class="sxs-lookup"><span data-stu-id="2e99d-312">*src/RazorPagesProject*</span></span> | <span data-ttu-id="2e99d-313">사용자가 메시지를 추가, 삭제, 삭제 및 분석할 수 있도록 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-313">Allows a user to add, delete one, delete all, and analyze messages.</span></span> |
| <span data-ttu-id="2e99d-314">응용 프로그램 테스트</span><span class="sxs-lookup"><span data-stu-id="2e99d-314">Test app</span></span> | <span data-ttu-id="2e99d-315">*테스트/RazorPagesProject. 테스트*</span><span class="sxs-lookup"><span data-stu-id="2e99d-315">*tests/RazorPagesProject.Tests*</span></span> | <span data-ttu-id="2e99d-316">SUT 테스트를 통합 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-316">Used to integration test the SUT.</span></span> |

<span data-ttu-id="2e99d-317">[Visual Studio](https://visualstudio.microsoft.com)와 같은 IDE의 기본 제공 테스트 기능을 사용 하 여 테스트를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-317">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="2e99d-318">[Visual Studio Code](https://code.visualstudio.com/) 또는 명령줄을 사용 하는 경우 *테스트/RazorPagesProject. 테스트* 디렉터리의 명령 프롬프트에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-318">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```console
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="2e99d-319">메시지 앱 (SUT) 조직</span><span class="sxs-lookup"><span data-stu-id="2e99d-319">Message app (SUT) organization</span></span>

<span data-ttu-id="2e99d-320">SUT는 다음과 같은 특징을 가진 Razor Pages 메시지 시스템입니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-320">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="2e99d-321">응용 프로그램의 인덱스 페이지 (*Pages/index. cshtml* 및 *pages/인덱스인*)는 메시지의 추가, 삭제 및 분석 (메시지당 평균 단어)을 제어 하는 UI 및 페이지 모델 메서드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-321">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="2e99d-322">메시지는 `Id` (키) 및 `Text` (메시지) 라는 두 가지 속성을 사용 하 여 `Message` 클래스 (*데이터/메시지 .cs*)에서 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-322">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="2e99d-323">`Text` 속성은 필수 이며 200 자로 제한 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-323">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="2e99d-324">메시지는 [Entity Framework의 메모리 내 데이터베이스](/ef/core/providers/in-memory/)&#8224;를 사용 하 여 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-324">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="2e99d-325">앱에는 데이터베이스 컨텍스트 클래스의 DAL (데이터 액세스 계층)이 포함 되 고 `AppDbContext` (*data/AppDbContext*)이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-325">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="2e99d-326">응용 프로그램이 시작 될 때 데이터베이스가 비어 있는 경우 메시지 저장소는 세 개의 메시지로 초기화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-326">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="2e99d-327">앱에는 인증 된 사용자만 액세스할 수 있는 `/SecurePage`이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-327">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="2e99d-328">&#8224;EF 항목인 [InMemory 테스트](/ef/core/miscellaneous/testing/in-memory)는 MSTest를 사용 하 여 테스트에 메모리 내 데이터베이스를 사용 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-328">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="2e99d-329">이 항목에서는 [Xunit](https://xunit.github.io/) 테스트 프레임 워크를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-329">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="2e99d-330">여러 테스트 프레임 워크에서 테스트 개념 및 테스트 구현은 비슷하지만 동일 하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-330">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="2e99d-331">앱은 리포지토리 패턴을 사용 하지 않으며 [UoW (Unit Of Work) 패턴](https://martinfowler.com/eaaCatalog/unitOfWork.html)의 효과적인 예는 아니지만 Razor Pages 이러한 개발 패턴을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-331">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="2e99d-332">자세한 내용은 [인프라 지 속성 계층](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) 및 [테스트 컨트롤러 논리](/aspnet/core/mvc/controllers/testing) 디자인 (샘플에서는 리포지토리 패턴 구현)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2e99d-332">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="2e99d-333">앱 구성 테스트</span><span class="sxs-lookup"><span data-stu-id="2e99d-333">Test app organization</span></span>

<span data-ttu-id="2e99d-334">테스트 앱은 테스트 */Razor 프로젝트* 의 콘솔 앱입니다. 테스트 디렉터리입니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-334">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="2e99d-335">응용 프로그램 디렉터리 테스트</span><span class="sxs-lookup"><span data-stu-id="2e99d-335">Test app directory</span></span> | <span data-ttu-id="2e99d-336">설명</span><span class="sxs-lookup"><span data-stu-id="2e99d-336">Description</span></span> |
| ------------------ | ----------- |
| <span data-ttu-id="2e99d-337">*AuthTests*</span><span class="sxs-lookup"><span data-stu-id="2e99d-337">*AuthTests*</span></span> | <span data-ttu-id="2e99d-338">다음에 대 한 테스트 메서드를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-338">Contains test methods for:</span></span><ul><li><span data-ttu-id="2e99d-339">인증 되지 않은 사용자가 보안 페이지에 액세스 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-339">Accessing a secure page by an unauthenticated user.</span></span></li><li><span data-ttu-id="2e99d-340">모의 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>를 사용 하 여 인증 된 사용자가 보안 페이지에 액세스 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-340">Accessing a secure page by an authenticated user with a mock <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span></li><li><span data-ttu-id="2e99d-341">GitHub 사용자 프로필을 가져오고 프로필의 사용자 로그인을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-341">Obtaining a GitHub user profile and checking the profile's user login.</span></span></li></ul> |
| <span data-ttu-id="2e99d-342">*BasicTests*</span><span class="sxs-lookup"><span data-stu-id="2e99d-342">*BasicTests*</span></span> | <span data-ttu-id="2e99d-343">라우팅 및 콘텐츠 형식에 대 한 테스트 메서드를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-343">Contains a test method for routing and content type.</span></span> |
| <span data-ttu-id="2e99d-344">*IntegrationTests*</span><span class="sxs-lookup"><span data-stu-id="2e99d-344">*IntegrationTests*</span></span> | <span data-ttu-id="2e99d-345">사용자 지정 `WebApplicationFactory` 클래스를 사용 하 여 인덱스 페이지에 대 한 통합 테스트를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-345">Contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> |
| <span data-ttu-id="2e99d-346">*도우미/유틸리티*</span><span class="sxs-lookup"><span data-stu-id="2e99d-346">*Helpers/Utilities*</span></span> | <ul><li><span data-ttu-id="2e99d-347">*Utilities.cs* 에는 테스트 데이터로 데이터베이스를 시드 하는 데 사용 되는 `InitializeDbForTests` 메서드가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-347">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="2e99d-348">*HtmlHelpers.cs* 는 테스트 메서드에서 사용할 AngleSharp `IHtmlDocument`을 반환 하는 메서드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-348">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="2e99d-349">*HttpClientExtensions.cs* 는 `SendAsync`에 대 한 오버 로드를 제공 하 여 sut에 요청을 제출 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-349">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="2e99d-350">테스트 프레임 워크는 [Xunit](https://xunit.github.io/)입니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-350">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="2e99d-351">[AspNetCore. TestHost](/dotnet/api/microsoft.aspnetcore.testhost)( [testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver)포함)를 사용 하 여 통합 테스트를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-351">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="2e99d-352">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 패키지는 테스트 호스트와 테스트 서버를 구성 하는 데 사용 되기 때문에 `TestHost` 및 `TestServer` 패키지는 테스트 응용 프로그램의 프로젝트 파일 또는 테스트의 개발자 구성에서 직접 패키지 참조를 요구 하지 않습니다. 다운로드.</span><span class="sxs-lookup"><span data-stu-id="2e99d-352">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="2e99d-353">**테스트를 위해 데이터베이스 시드**</span><span class="sxs-lookup"><span data-stu-id="2e99d-353">**Seeding the database for testing**</span></span>

<span data-ttu-id="2e99d-354">통합 테스트는 일반적으로 테스트 실행 전에 데이터베이스에 작은 데이터 집합이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-354">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="2e99d-355">예를 들어 delete 테스트는 데이터베이스 레코드 삭제를 호출 하므로 삭제 요청이 성공 하려면 데이터베이스에 적어도 하나 이상의 레코드가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-355">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="2e99d-356">샘플 앱은 테스트가 실행 될 때 사용할 수 있는 *Utilities.cs* 의 세 가지 메시지를 사용 하 여 데이터베이스를 시드 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-356">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

<span data-ttu-id="2e99d-357">SUT의 데이터베이스 컨텍스트가 `Startup.ConfigureServices` 메서드에 등록 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-357">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="2e99d-358">응용 프로그램의 `builder.ConfigureServices` 콜백은 응용 프로그램의 `Startup.ConfigureServices` 코드가 실행 된 *후* 에 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-358">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="2e99d-359">테스트에 다른 데이터베이스를 사용 하려면 앱의 데이터베이스 컨텍스트를 `builder.ConfigureServices`으로 바꾸어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-359">To use a different database for the tests, the app's database context must be replaced in `builder.ConfigureServices`.</span></span> <span data-ttu-id="2e99d-360">자세한 내용은 [WebApplicationFactory 사용자 지정](#customize-webapplicationfactory) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2e99d-360">For more information, see the [Customize WebApplicationFactory](#customize-webapplicationfactory) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2e99d-361">통합 테스트는 응용 프로그램의 구성 요소가 응용 프로그램의 지원 인프라 (예: 데이터베이스, 파일 시스템 및 네트워크)를 포함 하는 수준에서 올바르게 작동 하는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-361">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="2e99d-362">ASP.NET Core는 테스트 웹 호스트 및 메모리 내 테스트 서버에서 단위 테스트 프레임 워크를 사용 하는 통합 테스트를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-362">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="2e99d-363">이 항목에서는 단위 테스트에 대해 기본적으로 이해 하 고 있다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-363">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="2e99d-364">테스트 개념을 잘 모르는 경우 [.Net Core의 단위 테스트 및 .NET Standard](/dotnet/core/testing/) 항목 및 연결 된 콘텐츠를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2e99d-364">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="2e99d-365">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2e99d-365">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="2e99d-366">샘플 앱은 Razor Pages 앱 이며 Razor Pages를 기본적으로 이해 하 고 있다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-366">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="2e99d-367">Razor Pages에 익숙하지 않은 경우 다음 항목을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2e99d-367">If unfamiliar with Razor Pages, see the following topics:</span></span>

* [<span data-ttu-id="2e99d-368">Razor 페이지 소개</span><span class="sxs-lookup"><span data-stu-id="2e99d-368">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="2e99d-369">Razor 페이지 시작</span><span class="sxs-lookup"><span data-stu-id="2e99d-369">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="2e99d-370">Razor 페이지 단위 테스트</span><span class="sxs-lookup"><span data-stu-id="2e99d-370">Razor Pages unit tests</span></span>](xref:test/razor-pages-tests)

> [!NOTE]
> <span data-ttu-id="2e99d-371">SPAs 테스트를 위해 브라우저를 자동화할 수 있는 [Selenium](https://www.seleniumhq.org/)와 같은 도구를 권장 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-371">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="2e99d-372">통합 테스트 소개</span><span class="sxs-lookup"><span data-stu-id="2e99d-372">Introduction to integration tests</span></span>

<span data-ttu-id="2e99d-373">통합 테스트는 [단위 테스트](/dotnet/core/testing/)보다 더 광범위 한 수준에서 앱의 구성 요소를 평가 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-373">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="2e99d-374">단위 테스트는 개별 클래스 메서드와 같은 격리 된 소프트웨어 구성 요소를 테스트 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-374">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="2e99d-375">통합 테스트는 두 개 이상의 앱 구성 요소가 함께 작동 하 여 요청을 완전히 처리 하는 데 필요한 모든 구성 요소를 포함 하 여 예상 결과를 생성 하는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-375">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="2e99d-376">이러한 광범위 한 테스트는 다음과 같은 구성 요소를 포함 하 여 앱의 인프라 및 전체 프레임 워크를 테스트 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-376">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="2e99d-377">데이터베이스</span><span class="sxs-lookup"><span data-stu-id="2e99d-377">Database</span></span>
* <span data-ttu-id="2e99d-378">파일 시스템</span><span class="sxs-lookup"><span data-stu-id="2e99d-378">File system</span></span>
* <span data-ttu-id="2e99d-379">네트워크 어플라이언스</span><span class="sxs-lookup"><span data-stu-id="2e99d-379">Network appliances</span></span>
* <span data-ttu-id="2e99d-380">요청-응답 파이프라인</span><span class="sxs-lookup"><span data-stu-id="2e99d-380">Request-response pipeline</span></span>

<span data-ttu-id="2e99d-381">단위 테스트는 인프라 구성 요소 대신 *fakes* 또는 *모의 개체*라고 하는 구성 된 구성 요소를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-381">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="2e99d-382">단위 테스트와 달리 통합 테스트는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-382">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="2e99d-383">프로덕션 환경에서 앱이 사용 하는 실제 구성 요소를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-383">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="2e99d-384">더 많은 코드와 데이터 처리가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-384">Require more code and data processing.</span></span>
* <span data-ttu-id="2e99d-385">실행 하는 데 시간이 더 오래 걸립니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-385">Take longer to run.</span></span>

<span data-ttu-id="2e99d-386">따라서 가장 중요 한 인프라 시나리오에 대 한 통합 테스트 사용을 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-386">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="2e99d-387">단위 테스트 또는 통합 테스트를 사용 하 여 동작을 테스트할 수 있는 경우 단위 테스트를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-387">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="2e99d-388">데이터베이스 및 파일 시스템을 사용 하 여 모든 가능한 데이터 순열 및 파일 액세스에 대해 통합 테스트를 작성 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="2e99d-388">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="2e99d-389">응용 프로그램 전체에서 데이터베이스 및 파일 시스템과 상호 작용 하는 위치에 관계 없이, 일반적으로 데이터베이스 및 파일 시스템 구성 요소를 적절 하 게 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-389">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="2e99d-390">이러한 구성 요소와 상호 작용 하는 메서드 논리의 루틴 테스트에 단위 테스트를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-390">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="2e99d-391">단위 테스트에서 인프라 fakes/mock를 사용 하면 테스트 실행 속도가 빨라집니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-391">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="2e99d-392">통합 테스트에 대 한 토론에서 테스트 된 프로젝트를 종종 *테스트 중인 시스템*또는 short의 경우 "sut" 라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-392">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="2e99d-393">*이 항목 전체에서 "SUT"를 사용 하 여 테스트 된 ASP.NET Core 앱을 참조 합니다.*</span><span class="sxs-lookup"><span data-stu-id="2e99d-393">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="2e99d-394">ASP.NET Core 통합 테스트</span><span class="sxs-lookup"><span data-stu-id="2e99d-394">ASP.NET Core integration tests</span></span>

<span data-ttu-id="2e99d-395">ASP.NET Core의 통합 테스트에는 다음이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-395">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="2e99d-396">테스트 프로젝트는 테스트를 포함 하 고 실행 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-396">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="2e99d-397">테스트 프로젝트에는 SUT에 대 한 참조가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-397">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="2e99d-398">테스트 프로젝트는 SUT에 대 한 테스트 웹 호스트를 만들고 테스트 서버 클라이언트를 사용 하 여 SUT를 사용 하 여 요청 및 응답을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-398">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="2e99d-399">Test runner는 테스트를 실행 하 고 테스트 결과를 보고 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-399">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="2e99d-400">통합 테스트는 일반적인 *정렬*, 작업 및 *어설션* 테스트 *단계를 포함*하는 이벤트 시퀀스를 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-400">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="2e99d-401">SUT의 웹 호스트가 구성 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-401">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="2e99d-402">앱에 요청을 제출 하는 테스트 서버 클라이언트가 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-402">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="2e99d-403">테스트 응용 프로그램에서 요청을 준비 하는 *정렬* 테스트 단계를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-403">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="2e99d-404">작업 *테스트 단계가* 실행 됩니다. 즉, 클라이언트가 요청을 제출 하 고 응답을 받습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-404">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="2e99d-405">*Assert* 테스트 단계가 실행 됩니다. *실제* 응답은 *예상* 된 응답에 따라 *통과* 또는 *실패* 의 유효성을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-405">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="2e99d-406">모든 테스트가 실행 될 때까지 프로세스가 계속 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-406">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="2e99d-407">테스트 결과가 보고 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-407">The test results are reported.</span></span>

<span data-ttu-id="2e99d-408">일반적으로 테스트 웹 호스트는 테스트 실행을 위해 응용 프로그램의 일반 웹 호스트와 다르게 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-408">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="2e99d-409">예를 들어 다른 데이터베이스 또는 다른 앱 설정을 테스트에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-409">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="2e99d-410">테스트 웹 호스트 및 메모리 내 테스트 서버 ([Testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver))와 같은 인프라 구성 요소는 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 패키지에서 제공 하거나 관리 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-410">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="2e99d-411">이 패키지를 사용 하면 테스트 생성 및 실행이 간소화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-411">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="2e99d-412">`Microsoft.AspNetCore.Mvc.Testing` 패키지는 다음 작업을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-412">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="2e99d-413">SUT의 종속성 파일 ( *. deps*)을 테스트 프로젝트의 *bin* 디렉터리에 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-413">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="2e99d-414">테스트를 실행할 때 정적 파일 및 페이지/뷰가 검색 되도록 [콘텐츠 루트](xref:fundamentals/index#content-root) 를 sut의 프로젝트 루트로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-414">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="2e99d-415">`TestServer`를 사용 하 여 SUT를 간소화 하는 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) 클래스를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-415">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="2e99d-416">[단위 테스트](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) 설명서에서는 테스트를 실행 하는 방법에 대 한 자세한 지침과 테스트 및 테스트 클래스의 이름을 설정 하는 방법에 대 한 자세한 지침과 함께 테스트 프로젝트 및 test runner를 설정 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-416">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="2e99d-417">앱에 대 한 테스트 프로젝트를 만들 때 통합 테스트에서 여러 프로젝트로 단위 테스트를 분리 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-417">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="2e99d-418">이를 통해 인프라 테스트 구성 요소가 실수로 단위 테스트에 포함 되지 않도록 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-418">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="2e99d-419">단위 및 통합 테스트를 분리 하 여 실행 되는 테스트 집합을 제어할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-419">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="2e99d-420">Razor Pages 앱과 MVC 앱의 테스트에 대 한 구성의 차이는 거의 없습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-420">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="2e99d-421">유일한 차이점은 테스트의 이름을 지정 하는 방법 뿐입니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-421">The only difference is in how the tests are named.</span></span> <span data-ttu-id="2e99d-422">Razor Pages 앱에서 페이지 끝점의 테스트는 일반적으로 페이지 모델 클래스 (예: 인덱스 페이지에 대 한 구성 요소 통합을 테스트 하기 위해 `IndexPageTests`) 뒤에 이름이 지정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-422">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="2e99d-423">MVC 앱에서 테스트는 일반적으로 컨트롤러 클래스로 구성 되며 테스트 하는 컨트롤러 뒤에 이름이 지정 됩니다 (예: `HomeControllerTests`은 Home 컨트롤러에 대 한 구성 요소 통합 테스트).</span><span class="sxs-lookup"><span data-stu-id="2e99d-423">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="2e99d-424">응용 프로그램 테스트 필수 조건</span><span class="sxs-lookup"><span data-stu-id="2e99d-424">Test app prerequisites</span></span>

<span data-ttu-id="2e99d-425">테스트 프로젝트는 다음을 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-425">The test project must:</span></span>

* <span data-ttu-id="2e99d-426">다음 패키지를 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-426">Reference the following packages:</span></span>
  * [<span data-ttu-id="2e99d-427">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="2e99d-427">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [<span data-ttu-id="2e99d-428">AspNetCore.</span><span class="sxs-lookup"><span data-stu-id="2e99d-428">Microsoft.AspNetCore.Mvc.Testing</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* <span data-ttu-id="2e99d-429">프로젝트 파일 (`<Project Sdk="Microsoft.NET.Sdk.Web">`)에서 웹 SDK를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-429">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span> <span data-ttu-id="2e99d-430">웹 SDK는 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app)를 참조할 때 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-430">The Web SDK is required when referencing the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="2e99d-431">이러한 필수 구성 요소는 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)에서 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-431">These prerequisites can be seen in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="2e99d-432">*테스트/RazorPagesProject. 테스트/razor*  프로젝트. .csproj 파일을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-432">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="2e99d-433">샘플 앱은 [Xunit](https://xunit.github.io/) 테스트 프레임 워크 및 [AngleSharp](https://anglesharp.github.io/) 파서 라이브러리를 사용 하므로 샘플 앱은 다음을 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-433">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="2e99d-434">xunit</span><span class="sxs-lookup"><span data-stu-id="2e99d-434">xunit</span></span>](https://www.nuget.org/packages/xunit/)
* [<span data-ttu-id="2e99d-435">xunit. visualstudio</span><span class="sxs-lookup"><span data-stu-id="2e99d-435">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [<span data-ttu-id="2e99d-436">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="2e99d-436">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a><span data-ttu-id="2e99d-437">SUT 환경</span><span class="sxs-lookup"><span data-stu-id="2e99d-437">SUT environment</span></span>

<span data-ttu-id="2e99d-438">SUT의 [환경이](xref:fundamentals/environments) 설정 되지 않은 경우 환경의 기본값은 Development로 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-438">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="2e99d-439">기본 WebApplicationFactory를 사용 하는 기본 테스트</span><span class="sxs-lookup"><span data-stu-id="2e99d-439">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="2e99d-440">[WebApplicationFactory\<TEntryPoint >](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) 는 통합 테스트용 [testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver) 를 만드는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-440">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="2e99d-441">`TEntryPoint`은 SUT의 진입점 클래스입니다. 일반적으로 `Startup` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-441">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="2e99d-442">테스트 클래스는 클래스에 테스트를 포함 하 고 클래스의 테스트에서 공유 개체 인스턴스를 제공 하는 클래스 *fixture* 인터페이스 ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture))를 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-442">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

<span data-ttu-id="2e99d-443">다음 테스트 클래스 `BasicTests`에서는 `WebApplicationFactory`를 사용 하 여 SUT를 부트스트랩 하 고 `Get_EndpointsReturnSuccessAndCorrectContentType`테스트 메서드에 [Httpclient](/dotnet/api/system.net.http.httpclient) 를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-443">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="2e99d-444">메서드는 응답 상태 코드가 성공 (200-299 범위의 상태 코드) 되었는지 확인 하 고 `Content-Type` 헤더는 여러 앱 페이지에 대해 `text/html; charset=utf-8`입니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-444">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="2e99d-445">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) 는 리디렉션을 자동으로 따르며 쿠키를 처리 하는 `HttpClient`의 인스턴스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-445">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="2e99d-446">기본적으로 [Gdpr 동의 정책을](xref:security/gdpr) 사용 하는 경우 요청에서 필수가 아닌 쿠키가 유지 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-446">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="2e99d-447">TempData 공급자가 사용 하는 쿠키와 같이 필수가 아닌 쿠키를 유지 하려면 테스트에서 필수로 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-447">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="2e99d-448">쿠키를 필수로 표시 하는 방법에 대 한 지침은 [필수 쿠키](xref:security/gdpr#essential-cookies)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2e99d-448">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="2e99d-449">WebApplicationFactory 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="2e99d-449">Customize WebApplicationFactory</span></span>

<span data-ttu-id="2e99d-450">웹 호스트 구성은 하나 이상의 사용자 지정 팩터리를 만들기 위해 `WebApplicationFactory`에서 상속 하 여 테스트 클래스와 독립적으로 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-450">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="2e99d-451">`WebApplicationFactory`에서 상속 하 고 [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)를 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-451">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="2e99d-452">[Iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) 를 사용 하면 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices)를 사용 하 여 서비스 컬렉션을 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-452">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="2e99d-453">[샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) 에서의 데이터베이스 시드는 `InitializeDbForTests` 메서드로 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-453">Database seeding in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="2e99d-454">메서드는 [통합 테스트 샘플: 테스트 앱 구성](#test-app-organization) 섹션에 설명 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-454">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

2. <span data-ttu-id="2e99d-455">테스트 클래스에서 사용자 지정 `CustomWebApplicationFactory`을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-455">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="2e99d-456">다음 예제에서는 `IndexPageTests` 클래스에서 팩터리를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-456">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="2e99d-457">샘플 응용 프로그램의 클라이언트는 다음 리디렉션이 `HttpClient` 않도록 구성 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-457">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="2e99d-458">[모의 인증](#mock-authentication) 섹션의 뒷부분에서 설명한 대로 테스트에서 앱의 첫 번째 응답의 결과를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-458">As explained later in the [Mock authentication](#mock-authentication) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="2e99d-459">첫 번째 응답은 `Location` 헤더를 사용 하는 이러한 테스트 대부분의 리디렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-459">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="2e99d-460">일반적인 테스트는 `HttpClient` 및 도우미 메서드를 사용 하 여 요청 및 응답을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-460">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="2e99d-461">SUT에 대 한 POST 요청은 앱의 [데이터 보호 방지 시스템이](xref:security/data-protection/introduction)자동으로 수행 하는 위조 방지 검사를 충족 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-461">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="2e99d-462">테스트의 POST 요청을 정렬 하기 위해 테스트 앱은 다음을 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-462">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="2e99d-463">페이지에 대 한 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-463">Make a request for the page.</span></span>
1. <span data-ttu-id="2e99d-464">위조 방지 쿠키를 구문 분석 하 고 응답에서 유효성 검사 토큰을 요청 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-464">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="2e99d-465">위조 방지 쿠키 및 요청 유효성 검사 토큰을 사용 하 여 POST 요청을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-465">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="2e99d-466">`SendAsync` 도우미 확장 메서드 (*도우미/HttpClientExtensions*) 및 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) 의 `GetDocumentAsync` 도우미 메서드 (*도우미/htmlhelpers*)는 [AngleSharp](https://anglesharp.github.io/) 파서를 사용 하 여 위조 방지 검사를 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-466">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="2e99d-467">`GetDocumentAsync` &ndash; [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) 수신 하 고 `IHtmlDocument`를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-467">`GetDocumentAsync` &ndash; Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="2e99d-468">`GetDocumentAsync`은 원래 `HttpResponseMessage`에 따라 *가상 응답* 을 준비 하는 팩터리를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-468">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="2e99d-469">자세한 내용은 [AngleSharp 설명서](https://github.com/AngleSharp/AngleSharp#documentation)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2e99d-469">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="2e99d-470">[HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) 및 [SendAsync (HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) 를 작성 하는 `SendAsync` 확장 `HttpClient` 메서드를 작성 하 여 sut에 요청을 제출 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-470">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="2e99d-471">`SendAsync`에 대 한 오버 로드는 HTML 폼 (`IHtmlFormElement`) 및 다음을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-471">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="2e99d-472">양식의 제출 단추 (`IHtmlElement`)</span><span class="sxs-lookup"><span data-stu-id="2e99d-472">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="2e99d-473">양식 값 컬렉션 (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="2e99d-473">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="2e99d-474">제출 단추 (`IHtmlElement`) 및 폼 값 (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="2e99d-474">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="2e99d-475">[AngleSharp](https://anglesharp.github.io/) 는이 항목 및 샘플 앱에서 데모용으로 사용 되는 타사 구문 분석 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-475">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="2e99d-476">AngleSharp는 ASP.NET Core 앱의 통합 테스트에 지원 되지 않거나 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-476">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="2e99d-477">[HAP (Html 민첩성 팩)](https://html-agility-pack.net/)와 같은 다른 파서를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-477">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="2e99d-478">또 다른 방법은 위조 방지 시스템의 요청 확인 토큰을 처리 하 고 위조 쿠키를 직접 방지 하는 코드를 작성 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-478">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="2e99d-479">WithWebHostBuilder를 사용 하 여 클라이언트 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="2e99d-479">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="2e99d-480">테스트 메서드 내에서 추가 구성이 필요한 경우 [Withwebhostbuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) 는 구성으로 추가로 사용자 지정 된 [iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) 를 사용 하 여 새 `WebApplicationFactory`을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-480">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="2e99d-481">[샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) 의 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 테스트 메서드는 `WithWebHostBuilder`사용 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-481">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="2e99d-482">이 테스트는 SUT에서 양식 제출을 트리거하여 데이터베이스에서 레코드 삭제를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-482">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="2e99d-483">`IndexPageTests` 클래스의 다른 테스트는 데이터베이스의 모든 레코드를 삭제 하는 작업을 수행 하 고 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 메서드 이전에 실행 될 수 있기 때문에이 테스트 메서드에서 데이터베이스를 다시 시드됩니다 하 여 SUT가 삭제할 레코드가 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-483">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="2e99d-484">SUT에서 `messages` 폼의 첫 번째 삭제 단추를 선택 하면 SUT에 대 한 요청에서 시뮬레이션 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-484">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="2e99d-485">클라이언트 옵션</span><span class="sxs-lookup"><span data-stu-id="2e99d-485">Client options</span></span>

<span data-ttu-id="2e99d-486">다음 표에서는 `HttpClient` 인스턴스를 만들 때 사용할 수 있는 기본 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) 을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-486">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="2e99d-487">옵션</span><span class="sxs-lookup"><span data-stu-id="2e99d-487">Option</span></span> | <span data-ttu-id="2e99d-488">설명</span><span class="sxs-lookup"><span data-stu-id="2e99d-488">Description</span></span> | <span data-ttu-id="2e99d-489">기본</span><span class="sxs-lookup"><span data-stu-id="2e99d-489">Default</span></span> |
| ------ | ----------- | ------- |
| [<span data-ttu-id="2e99d-490">AllowAutoRedirect</span><span class="sxs-lookup"><span data-stu-id="2e99d-490">AllowAutoRedirect</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | <span data-ttu-id="2e99d-491">`HttpClient` 인스턴스가 자동으로 리디렉션 응답을 따르는지 여부를 가져오거나 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-491">Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span> | `true` |
| [<span data-ttu-id="2e99d-492">BaseAddress</span><span class="sxs-lookup"><span data-stu-id="2e99d-492">BaseAddress</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | <span data-ttu-id="2e99d-493">`HttpClient` 인스턴스의 기본 주소를 가져오거나 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-493">Gets or sets the base address of `HttpClient` instances.</span></span> | `http://localhost` |
| [<span data-ttu-id="2e99d-494">HandleCookies</span><span class="sxs-lookup"><span data-stu-id="2e99d-494">HandleCookies</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | <span data-ttu-id="2e99d-495">`HttpClient` 인스턴스에서 쿠키를 처리할지 여부를 가져오거나 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-495">Gets or sets whether `HttpClient` instances should handle cookies.</span></span> | `true` |
| [<span data-ttu-id="2e99d-496">Max자동 리디렉션</span><span class="sxs-lookup"><span data-stu-id="2e99d-496">MaxAutomaticRedirections</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | <span data-ttu-id="2e99d-497">`HttpClient` 인스턴스에서 따라야 하는 리디렉션 응답의 최대 수를 가져오거나 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-497">Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> | <span data-ttu-id="2e99d-498">7</span><span class="sxs-lookup"><span data-stu-id="2e99d-498">7</span></span> |

<span data-ttu-id="2e99d-499">`WebApplicationFactoryClientOptions` 클래스를 만들어 [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) 메서드에 전달 합니다 (기본값은 코드 예제에 표시 됨).</span><span class="sxs-lookup"><span data-stu-id="2e99d-499">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="2e99d-500">모의 서비스 삽입</span><span class="sxs-lookup"><span data-stu-id="2e99d-500">Inject mock services</span></span>

<span data-ttu-id="2e99d-501">호스트 작성기에서 [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) 에 대 한 호출을 사용 하 여 테스트에서 서비스를 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-501">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="2e99d-502">**모의 서비스를 삽입 하려면 SUT에 `Startup.ConfigureServices` 메서드를 사용 하는 `Startup` 클래스가 있어야 합니다.**</span><span class="sxs-lookup"><span data-stu-id="2e99d-502">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="2e99d-503">샘플 SUT에는 견적을 반환 하는 범위 지정 서비스가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-503">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="2e99d-504">인덱스 페이지를 요청 하면 따옴표가 인덱스 페이지의 숨겨진 필드에 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-504">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="2e99d-505">*Services/IQuoteService*:</span><span class="sxs-lookup"><span data-stu-id="2e99d-505">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="2e99d-506">*Services/QuoteService*:</span><span class="sxs-lookup"><span data-stu-id="2e99d-506">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="2e99d-507">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="2e99d-507">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="2e99d-508">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="2e99d-508">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="2e99d-509">*Pages/Index .cs*:</span><span class="sxs-lookup"><span data-stu-id="2e99d-509">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="2e99d-510">다음 태그는 SUT 앱이 실행 될 때 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-510">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="2e99d-511">통합 테스트에서 서비스 및 견적 주입을 테스트 하기 위해 테스트를 통해 모의 서비스를 SUT에 삽입 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-511">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="2e99d-512">모의 서비스는 앱의 `QuoteService`을 테스트 앱에서 제공 하는 서비스 (`TestQuoteService` 이라고 함)로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-512">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="2e99d-513">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="2e99d-513">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="2e99d-514">`ConfigureTestServices`이 호출 되 고 범위가 지정 된 서비스가 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-514">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="2e99d-515">테스트 실행 중에 생성 된 태그는 `TestQuoteService`에서 제공 된 따옴표 텍스트를 반영 하므로 어설션이 통과 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-515">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a><span data-ttu-id="2e99d-516">모의 인증</span><span class="sxs-lookup"><span data-stu-id="2e99d-516">Mock authentication</span></span>

<span data-ttu-id="2e99d-517">`AuthTests` 클래스의 테스트는 보안 끝점을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-517">Tests in the `AuthTests` class check that a secure endpoint:</span></span>

* <span data-ttu-id="2e99d-518">인증 되지 않은 사용자를 앱의 로그인 페이지로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-518">Redirects an unauthenticated user to the app's Login page.</span></span>
* <span data-ttu-id="2e99d-519">인증 된 사용자에 대 한 콘텐츠를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-519">Returns content for an authenticated user.</span></span>

<span data-ttu-id="2e99d-520">SUT에서 `/SecurePage` 페이지는 [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) 규칙을 사용 하 여 페이지에 [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) 를 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-520">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="2e99d-521">자세한 내용은 [Razor Pages 권한 부여 규칙](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2e99d-521">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="2e99d-522">`Get_SecurePageRedirectsAnUnauthenticatedUser` 테스트에서 [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) 를 `false`으로 설정 하 여 리디렉션을 허용 하지 않도록 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) 를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-522">In the `Get_SecurePageRedirectsAnUnauthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

<span data-ttu-id="2e99d-523">클라이언트에서 리디렉션을 따르도록 허용 하지 않으면 다음 검사를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-523">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="2e99d-524">SUT에서 반환 된 상태 코드는 HttpStatusCode 결과에 대해 확인할 수 있습니다 [.](/dotnet/api/system.net.httpstatuscode) 이는 로그인 페이지에 대 한 리디렉션 이후의 최종 상태 코드는 [HttpStatusCode](/dotnet/api/system.net.httpstatuscode)입니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-524">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="2e99d-525">응답 헤더의 `Location` 헤더 값은 마지막 로그인 페이지 응답이 아닌 `http://localhost/Identity/Account/Login`로 시작 하는 것을 확인 하기 위해 확인 됩니다 .이 값은 `Location` 헤더가 존재 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-525">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="2e99d-526">테스트 앱은 인증 및 권한 부여의 측면을 테스트 하기 위해 [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) 에서 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>를 mock 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-526">The test app can mock an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) in order to test aspects of authentication and authorization.</span></span> <span data-ttu-id="2e99d-527">최소한의 시나리오는 AuthenticateResult을 반환 [합니다.](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*)</span><span class="sxs-lookup"><span data-stu-id="2e99d-527">A minimal scenario returns an [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

<span data-ttu-id="2e99d-528">`TestAuthHandler`는 인증 체계가 `ConfigureTestServices`에 대해 등록 된 `AddAuthentication` `Test`으로 설정 된 경우 사용자를 인증 하기 위해 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-528">The `TestAuthHandler` is called to authenticate a user when the authentication scheme is set to `Test` where `AddAuthentication` is registered for `ConfigureTestServices`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

<span data-ttu-id="2e99d-529">`WebApplicationFactoryClientOptions`에 대 한 자세한 내용은 [클라이언트 옵션](#client-options) 섹션을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2e99d-529">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="2e99d-530">환경 설정</span><span class="sxs-lookup"><span data-stu-id="2e99d-530">Set the environment</span></span>

<span data-ttu-id="2e99d-531">기본적으로 SUT의 호스트 및 앱 환경은 개발 환경을 사용 하도록 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-531">By default, the SUT's host and app environment is configured to use the Development environment.</span></span> <span data-ttu-id="2e99d-532">SUT의 환경을 재정의 하려면:</span><span class="sxs-lookup"><span data-stu-id="2e99d-532">To override the SUT's environment:</span></span>

* <span data-ttu-id="2e99d-533">`ASPNETCORE_ENVIRONMENT` 환경 변수를 설정 합니다 (예: `Staging`, `Production`또는 기타 사용자 지정 값 (예: `Testing`)).</span><span class="sxs-lookup"><span data-stu-id="2e99d-533">Set the `ASPNETCORE_ENVIRONMENT` environment variable (for example, `Staging`, `Production`, or other custom value, such as `Testing`).</span></span>
* <span data-ttu-id="2e99d-534">`ASPNETCORE`를 접두사로 사용 하는 환경 변수를 읽도록 테스트 앱의 `CreateHostBuilder`를 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-534">Override `CreateHostBuilder` in the test app to read environment variables prefixed with `ASPNETCORE`.</span></span>

```csharp
protected override IHostBuilder CreateHostBuilder() => 
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="2e99d-535">테스트 인프라가 앱 콘텐츠 루트 경로를 유추 하는 방법</span><span class="sxs-lookup"><span data-stu-id="2e99d-535">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="2e99d-536">`WebApplicationFactory` 생성자는 `TEntryPoint` 어셈블리 `System.Reflection.Assembly.FullName`와 같은 키를 사용 하 여 통합 테스트가 포함 된 어셈블리에서 [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) 를 검색 하 여 앱 [콘텐츠 루트](xref:fundamentals/index#content-root) 경로를 유추 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-536">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="2e99d-537">올바른 키를 가진 특성을 찾을 수 없는 경우 `WebApplicationFactory`은 솔루션 파일 ( *.sln*)을 검색 하는 것으로 대체 되 고 `TEntryPoint` 어셈블리 이름을 솔루션 디렉터리에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-537">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="2e99d-538">응용 프로그램 루트 디렉터리 (콘텐츠 루트 경로)는 뷰와 콘텐츠 파일을 검색 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-538">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="2e99d-539">섀도 복사 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="2e99d-539">Disable shadow copying</span></span>

<span data-ttu-id="2e99d-540">섀도 복사를 수행 하면 테스트가 출력 디렉터리와 다른 디렉터리에 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-540">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="2e99d-541">테스트가 제대로 작동 하려면 섀도 복사를 사용 하지 않도록 설정 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-541">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="2e99d-542">[샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) 은 xunit을 사용 하 고 올바른 구성 설정을 사용 하 여 xunit *. runner* 파일을 포함 하 여 xunit에 대해 섀도 복사를 사용 하지 않도록 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-542">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="2e99d-543">자세한 내용은 [JSON을 사용 하 여 xUnit 구성](https://xunit.github.io/docs/configuring-with-json.html)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2e99d-543">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="2e99d-544">다음 콘텐츠를 사용 하 여 테스트 프로젝트의 루트에 *xunit.*</span><span class="sxs-lookup"><span data-stu-id="2e99d-544">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

<span data-ttu-id="2e99d-545">Visual Studio를 사용 하는 경우 파일의 **출력 디렉터리로 복사** 속성을 **항상 복사**로 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-545">If using Visual Studio, set the file's **Copy to Output Directory** property to **Copy always**.</span></span> <span data-ttu-id="2e99d-546">Visual Studio를 사용 하지 않는 경우 테스트 앱의 프로젝트 파일에 `Content` 대상을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-546">If not using Visual Studio, add a `Content` target to the test app's project file:</span></span>

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a><span data-ttu-id="2e99d-547">개체 삭제</span><span class="sxs-lookup"><span data-stu-id="2e99d-547">Disposal of objects</span></span>

<span data-ttu-id="2e99d-548">`IClassFixture` 구현에 대 한 테스트를 실행 한 후 xUnit에서 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)을 삭제 하면 [Testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver) 및 [httpclient](/dotnet/api/system.net.http.httpclient) 가 삭제 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-548">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="2e99d-549">개발자가 인스턴스화한 개체를 삭제 해야 하는 경우 `IClassFixture` 구현에서 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-549">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="2e99d-550">자세한 내용은 [Dispose 메서드 구현](/dotnet/standard/garbage-collection/implementing-dispose)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2e99d-550">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="2e99d-551">통합 테스트 샘플</span><span class="sxs-lookup"><span data-stu-id="2e99d-551">Integration tests sample</span></span>

<span data-ttu-id="2e99d-552">[샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) 은 다음과 같은 두 개의 앱으로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-552">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="2e99d-553">App</span><span class="sxs-lookup"><span data-stu-id="2e99d-553">App</span></span> | <span data-ttu-id="2e99d-554">프로젝트 디렉터리</span><span class="sxs-lookup"><span data-stu-id="2e99d-554">Project directory</span></span> | <span data-ttu-id="2e99d-555">설명</span><span class="sxs-lookup"><span data-stu-id="2e99d-555">Description</span></span> |
| --- | ----------------- | ----------- |
| <span data-ttu-id="2e99d-556">메시지 앱 (SUT)</span><span class="sxs-lookup"><span data-stu-id="2e99d-556">Message app (the SUT)</span></span> | <span data-ttu-id="2e99d-557">*src/Razorororproject*</span><span class="sxs-lookup"><span data-stu-id="2e99d-557">*src/RazorPagesProject*</span></span> | <span data-ttu-id="2e99d-558">사용자가 메시지를 추가, 삭제, 삭제 및 분석할 수 있도록 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-558">Allows a user to add, delete one, delete all, and analyze messages.</span></span> |
| <span data-ttu-id="2e99d-559">응용 프로그램 테스트</span><span class="sxs-lookup"><span data-stu-id="2e99d-559">Test app</span></span> | <span data-ttu-id="2e99d-560">*테스트/RazorPagesProject. 테스트*</span><span class="sxs-lookup"><span data-stu-id="2e99d-560">*tests/RazorPagesProject.Tests*</span></span> | <span data-ttu-id="2e99d-561">SUT 테스트를 통합 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-561">Used to integration test the SUT.</span></span> |

<span data-ttu-id="2e99d-562">[Visual Studio](https://visualstudio.microsoft.com)와 같은 IDE의 기본 제공 테스트 기능을 사용 하 여 테스트를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-562">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="2e99d-563">[Visual Studio Code](https://code.visualstudio.com/) 또는 명령줄을 사용 하는 경우 *테스트/RazorPagesProject. 테스트* 디렉터리의 명령 프롬프트에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-563">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="2e99d-564">메시지 앱 (SUT) 조직</span><span class="sxs-lookup"><span data-stu-id="2e99d-564">Message app (SUT) organization</span></span>

<span data-ttu-id="2e99d-565">SUT는 다음과 같은 특징을 가진 Razor Pages 메시지 시스템입니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-565">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="2e99d-566">응용 프로그램의 인덱스 페이지 (*Pages/index. cshtml* 및 *pages/인덱스인*)는 메시지의 추가, 삭제 및 분석 (메시지당 평균 단어)을 제어 하는 UI 및 페이지 모델 메서드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-566">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="2e99d-567">메시지는 `Id` (키) 및 `Text` (메시지) 라는 두 가지 속성을 사용 하 여 `Message` 클래스 (*데이터/메시지 .cs*)에서 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-567">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="2e99d-568">`Text` 속성은 필수 이며 200 자로 제한 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-568">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="2e99d-569">메시지는 [Entity Framework의 메모리 내 데이터베이스](/ef/core/providers/in-memory/)&#8224;를 사용 하 여 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-569">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="2e99d-570">앱에는 데이터베이스 컨텍스트 클래스의 DAL (데이터 액세스 계층)이 포함 되 고 `AppDbContext` (*data/AppDbContext*)이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-570">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="2e99d-571">응용 프로그램이 시작 될 때 데이터베이스가 비어 있는 경우 메시지 저장소는 세 개의 메시지로 초기화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-571">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="2e99d-572">앱에는 인증 된 사용자만 액세스할 수 있는 `/SecurePage`이 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-572">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="2e99d-573">&#8224;EF 항목인 [InMemory 테스트](/ef/core/miscellaneous/testing/in-memory)는 MSTest를 사용 하 여 테스트에 메모리 내 데이터베이스를 사용 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-573">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="2e99d-574">이 항목에서는 [Xunit](https://xunit.github.io/) 테스트 프레임 워크를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-574">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="2e99d-575">여러 테스트 프레임 워크에서 테스트 개념 및 테스트 구현은 비슷하지만 동일 하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-575">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="2e99d-576">앱은 리포지토리 패턴을 사용 하지 않으며 [UoW (Unit Of Work) 패턴](https://martinfowler.com/eaaCatalog/unitOfWork.html)의 효과적인 예는 아니지만 Razor Pages 이러한 개발 패턴을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-576">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="2e99d-577">자세한 내용은 [인프라 지 속성 계층](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) 및 [테스트 컨트롤러 논리](/aspnet/core/mvc/controllers/testing) 디자인 (샘플에서는 리포지토리 패턴 구현)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="2e99d-577">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="2e99d-578">앱 구성 테스트</span><span class="sxs-lookup"><span data-stu-id="2e99d-578">Test app organization</span></span>

<span data-ttu-id="2e99d-579">테스트 앱은 테스트 */Razor 프로젝트* 의 콘솔 앱입니다. 테스트 디렉터리입니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-579">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="2e99d-580">응용 프로그램 디렉터리 테스트</span><span class="sxs-lookup"><span data-stu-id="2e99d-580">Test app directory</span></span> | <span data-ttu-id="2e99d-581">설명</span><span class="sxs-lookup"><span data-stu-id="2e99d-581">Description</span></span> |
| ------------------ | ----------- |
| <span data-ttu-id="2e99d-582">*AuthTests*</span><span class="sxs-lookup"><span data-stu-id="2e99d-582">*AuthTests*</span></span> | <span data-ttu-id="2e99d-583">다음에 대 한 테스트 메서드를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-583">Contains test methods for:</span></span><ul><li><span data-ttu-id="2e99d-584">인증 되지 않은 사용자가 보안 페이지에 액세스 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-584">Accessing a secure page by an unauthenticated user.</span></span></li><li><span data-ttu-id="2e99d-585">모의 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>를 사용 하 여 인증 된 사용자가 보안 페이지에 액세스 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-585">Accessing a secure page by an authenticated user with a mock <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span></li><li><span data-ttu-id="2e99d-586">GitHub 사용자 프로필을 가져오고 프로필의 사용자 로그인을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-586">Obtaining a GitHub user profile and checking the profile's user login.</span></span></li></ul> |
| <span data-ttu-id="2e99d-587">*BasicTests*</span><span class="sxs-lookup"><span data-stu-id="2e99d-587">*BasicTests*</span></span> | <span data-ttu-id="2e99d-588">라우팅 및 콘텐츠 형식에 대 한 테스트 메서드를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-588">Contains a test method for routing and content type.</span></span> |
| <span data-ttu-id="2e99d-589">*IntegrationTests*</span><span class="sxs-lookup"><span data-stu-id="2e99d-589">*IntegrationTests*</span></span> | <span data-ttu-id="2e99d-590">사용자 지정 `WebApplicationFactory` 클래스를 사용 하 여 인덱스 페이지에 대 한 통합 테스트를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-590">Contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> |
| <span data-ttu-id="2e99d-591">*도우미/유틸리티*</span><span class="sxs-lookup"><span data-stu-id="2e99d-591">*Helpers/Utilities*</span></span> | <ul><li><span data-ttu-id="2e99d-592">*Utilities.cs* 에는 테스트 데이터로 데이터베이스를 시드 하는 데 사용 되는 `InitializeDbForTests` 메서드가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-592">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="2e99d-593">*HtmlHelpers.cs* 는 테스트 메서드에서 사용할 AngleSharp `IHtmlDocument`을 반환 하는 메서드를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-593">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="2e99d-594">*HttpClientExtensions.cs* 는 `SendAsync`에 대 한 오버 로드를 제공 하 여 sut에 요청을 제출 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-594">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="2e99d-595">테스트 프레임 워크는 [Xunit](https://xunit.github.io/)입니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-595">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="2e99d-596">[AspNetCore. TestHost](/dotnet/api/microsoft.aspnetcore.testhost)( [testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver)포함)를 사용 하 여 통합 테스트를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-596">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="2e99d-597">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 패키지는 테스트 호스트와 테스트 서버를 구성 하는 데 사용 되기 때문에 `TestHost` 및 `TestServer` 패키지는 테스트 응용 프로그램의 프로젝트 파일 또는 테스트의 개발자 구성에서 직접 패키지 참조를 요구 하지 않습니다. 다운로드.</span><span class="sxs-lookup"><span data-stu-id="2e99d-597">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="2e99d-598">**테스트를 위해 데이터베이스 시드**</span><span class="sxs-lookup"><span data-stu-id="2e99d-598">**Seeding the database for testing**</span></span>

<span data-ttu-id="2e99d-599">통합 테스트는 일반적으로 테스트 실행 전에 데이터베이스에 작은 데이터 집합이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-599">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="2e99d-600">예를 들어 delete 테스트는 데이터베이스 레코드 삭제를 호출 하므로 삭제 요청이 성공 하려면 데이터베이스에 적어도 하나 이상의 레코드가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-600">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="2e99d-601">샘플 앱은 테스트가 실행 될 때 사용할 수 있는 *Utilities.cs* 의 세 가지 메시지를 사용 하 여 데이터베이스를 시드 합니다.</span><span class="sxs-lookup"><span data-stu-id="2e99d-601">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="2e99d-602">추가 자료</span><span class="sxs-lookup"><span data-stu-id="2e99d-602">Additional resources</span></span>

* [<span data-ttu-id="2e99d-603">단위 테스트</span><span class="sxs-lookup"><span data-stu-id="2e99d-603">Unit tests</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:test/razor-pages-tests>
* <xref:fundamentals/middleware/index>
* <xref:mvc/controllers/testing>
