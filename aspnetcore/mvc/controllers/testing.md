---
title: ASP.NET Core에서 컨트롤러 논리 테스트
author: ardalis
description: ASP.NET Core에서 Moq 및 xUnit로 컨트롤러 논리를 테스트하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
uid: mvc/controllers/testing
ms.openlocfilehash: 3b5e60bbc76030b6d08bcb2f3aa1f6eca3c9bb05
ms.sourcegitcommit: 0365af91518004c4a44a30dc3a8ac324558a399b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71199098"
---
# <a name="test-controller-logic-in-aspnet-core"></a><span data-ttu-id="ffae8-103">ASP.NET Core에서 컨트롤러 논리 테스트</span><span class="sxs-lookup"><span data-stu-id="ffae8-103">Test controller logic in ASP.NET Core</span></span>

<span data-ttu-id="ffae8-104">작성자: [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="ffae8-104">By [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ffae8-105">[컨트롤러](xref:mvc/controllers/actions)는 임의의 ASP.NET Core MVC 앱에서 중심적인 역할을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-105">[Controllers](xref:mvc/controllers/actions) play a central role in any ASP.NET Core MVC app.</span></span> <span data-ttu-id="ffae8-106">따라서 컨트롤러가 의도한 대로 동작한다고 확신할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-106">As such, you should have confidence that controllers behave as intended.</span></span> <span data-ttu-id="ffae8-107">자동화된 테스트는 앱이 프로덕션 환경에 배포되기 전에 오류를 발견할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-107">Automated tests can detect errors before the app is deployed to a production environment.</span></span>

<span data-ttu-id="ffae8-108">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/testing/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ffae8-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/testing/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="unit-tests-of-controller-logic"></a><span data-ttu-id="ffae8-109">컨트롤러 논리의 단위 테스트</span><span class="sxs-lookup"><span data-stu-id="ffae8-109">Unit tests of controller logic</span></span>

<span data-ttu-id="ffae8-110">[단위 테스트](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)는 인프라 및 종속성으로부터 격리된 상태에서 앱의 일부를 테스트하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-110">[Unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) involve testing a part of an app in isolation from its infrastructure and dependencies.</span></span> <span data-ttu-id="ffae8-111">컨트롤러 논리를 유닛 테스트하는 경우 단일 작업의 콘텐츠만 테스트되고, 종속성 또는 프레임워크 자체의 동작은 테스트되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-111">When unit testing controller logic, only the contents of a single action are tested, not the behavior of its dependencies or of the framework itself.</span></span>

<span data-ttu-id="ffae8-112">컨트롤러의 동작에 초점을 맞춰 컨트롤러 동작의 단위 테스트를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-112">Set up unit tests of controller actions to focus on the controller's behavior.</span></span> <span data-ttu-id="ffae8-113">컨트롤러 단위 테스트는 [필터](xref:mvc/controllers/filters), [라우팅](xref:fundamentals/routing), [모델 바인딩](xref:mvc/models/model-binding) 같은 시나리오를 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-113">A controller unit test avoids scenarios such as [filters](xref:mvc/controllers/filters), [routing](xref:fundamentals/routing), and [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="ffae8-114">전체적으로 요청에 응답하는 구성 요소 간 상호 작용을 포함하는 테스트는 ‘통합 테스트’에서 처리합니다. </span><span class="sxs-lookup"><span data-stu-id="ffae8-114">Tests that cover the interactions among components that collectively respond to a request are handled by *integration tests*.</span></span> <span data-ttu-id="ffae8-115">통합 테스트에 대한 자세한 내용은 <xref:test/integration-tests>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ffae8-115">For more information on integration tests, see <xref:test/integration-tests>.</span></span>

<span data-ttu-id="ffae8-116">사용자 지정 필터 및 경로를 작성할 때 특정 컨트롤러 작업에 대한 테스트의 일부가 아닌 별도로 단위 테스트를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-116">If you're writing custom filters and routes, unit test them in isolation, not as part of tests on a particular controller action.</span></span>

<span data-ttu-id="ffae8-117">컨트롤러 단위 테스트를 데모하려면 샘플 앱에서 다음 컨트롤러를 검토하세요.</span><span class="sxs-lookup"><span data-stu-id="ffae8-117">To demonstrate controller unit tests, review the following controller in the sample app.</span></span> <span data-ttu-id="ffae8-118">홈 컨트롤러는 브레인스토밍 세션 목록을 표시하며 POST 요청을 사용하여 새로운 브레인스토밍 세션을 만들 수 있게 합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-118">The Home controller displays a list of brainstorming sessions and allows the creation of new brainstorming sessions with a POST request:</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/HomeController.cs?name=snippet_HomeController&highlight=1,5,10,31-32)]

<span data-ttu-id="ffae8-119">이전 컨트롤러는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-119">The preceding controller:</span></span>

* <span data-ttu-id="ffae8-120">[명시적 종속성 원칙](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-120">Follows the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>
* <span data-ttu-id="ffae8-121">[DI(종속성 주입)](xref:fundamentals/dependency-injection)에서 `IBrainstormSessionRepository`의 인스턴스를 제공할 것을 예상합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-121">Expects [dependency injection (DI)](xref:fundamentals/dependency-injection) to provide an instance of `IBrainstormSessionRepository`.</span></span>
* <span data-ttu-id="ffae8-122">[Moq](https://www.nuget.org/packages/Moq/)와 같은 모의 개체 프레임워크를 사용하여 모의 `IBrainstormSessionRepository` 서비스로 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-122">Can be tested with a mocked `IBrainstormSessionRepository` service using a mock object framework, such as [Moq](https://www.nuget.org/packages/Moq/).</span></span> <span data-ttu-id="ffae8-123">‘모의 개체’는 테스트에 사용되는 사전 결정된 속성 및 메서드 동작 집합이 있는 제작된 개체입니다. </span><span class="sxs-lookup"><span data-stu-id="ffae8-123">A *mocked object* is a fabricated object with a predetermined set of property and method behaviors used for testing.</span></span> <span data-ttu-id="ffae8-124">자세한 내용은 [통합 테스트 소개](xref:test/integration-tests#introduction-to-integration-tests)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ffae8-124">For more information, see [Introduction to integration tests](xref:test/integration-tests#introduction-to-integration-tests).</span></span>

<span data-ttu-id="ffae8-125">`HTTP GET Index` 메서드는 반복 또는 분기가 없으며 한 가지 메서드만 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-125">The `HTTP GET Index` method has no looping or branching and only calls one method.</span></span> <span data-ttu-id="ffae8-126">이 동작의 단위 테스트는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-126">The unit test for this action:</span></span>

* <span data-ttu-id="ffae8-127">`GetTestSessions` 메서드를 사용하여 `IBrainstormSessionRepository` 서비스를 모방합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-127">Mocks the `IBrainstormSessionRepository` service using the `GetTestSessions` method.</span></span> <span data-ttu-id="ffae8-128">`GetTestSessions`는 날짜 및 세션 이름이 있는 두 개의 모의 브레인스토밍 세션을 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-128">`GetTestSessions` creates two mock brainstorm sessions with dates and session names.</span></span>
* <span data-ttu-id="ffae8-129">`Index` 메서드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-129">Executes the `Index` method.</span></span>
* <span data-ttu-id="ffae8-130">메서드에서 반환한 결과에 대한 어설션을 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-130">Makes assertions on the result returned by the method:</span></span>
  * <span data-ttu-id="ffae8-131"><xref:Microsoft.AspNetCore.Mvc.ViewResult>가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-131">A <xref:Microsoft.AspNetCore.Mvc.ViewResult> is returned.</span></span>
  * <span data-ttu-id="ffae8-132">[ViewDataDictionary.Model](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary.Model*)은 `StormSessionViewModel`입니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-132">The [ViewDataDictionary.Model](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary.Model*) is a `StormSessionViewModel`.</span></span>
  * <span data-ttu-id="ffae8-133">`ViewDataDictionary.Model`에 저장된 두 개의 브레인스토밍 세션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-133">There are two brainstorming sessions stored in the `ViewDataDictionary.Model`.</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_Index_ReturnsAViewResult_WithAListOfBrainstormSessions&highlight=14-17)]

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_GetTestSessions)]

<span data-ttu-id="ffae8-134">홈 컨트롤러의 `HTTP POST Index` 메서드는 다음을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-134">The Home controller's `HTTP POST Index` method tests verifies that:</span></span>

* <span data-ttu-id="ffae8-135">[ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid*)가 `false`인 경우 작업 메서드가 적절한 데이터와 함께 ‘400 잘못된 요청’ <xref:Microsoft.AspNetCore.Mvc.ViewResult>를 반환함. </span><span class="sxs-lookup"><span data-stu-id="ffae8-135">When [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid*) is `false`, the action method returns a *400 Bad Request* <xref:Microsoft.AspNetCore.Mvc.ViewResult> with the appropriate data.</span></span>
* <span data-ttu-id="ffae8-136">`ModelState.IsValid`가 `true`인 경우:</span><span class="sxs-lookup"><span data-stu-id="ffae8-136">When `ModelState.IsValid` is `true`:</span></span>
  * <span data-ttu-id="ffae8-137">리포지토리에서 `Add` 메서드를 호출함.</span><span class="sxs-lookup"><span data-stu-id="ffae8-137">The `Add` method on the repository is called.</span></span>
  * <span data-ttu-id="ffae8-138"><xref:Microsoft.AspNetCore.Mvc.RedirectToActionResult>가 올바른 인수와 함께 반환됨.</span><span class="sxs-lookup"><span data-stu-id="ffae8-138">A <xref:Microsoft.AspNetCore.Mvc.RedirectToActionResult> is returned with the correct arguments.</span></span>

<span data-ttu-id="ffae8-139">아래의 첫 번째 테스트처럼 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*>를 사용해 오류를 추가하여 잘못된 모델 상태를 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-139">An invalid model state is tested by adding errors using <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> as shown in the first test below:</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_ModelState_ValidOrInvalid&highlight=9,16-17,38-41)]

<span data-ttu-id="ffae8-140">[ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)가 유효하지 않으면 GET 요청의 경우와 동일한 `ViewResult`가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-140">When [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) isn't valid, the same `ViewResult` is returned as for a GET request.</span></span> <span data-ttu-id="ffae8-141">이 테스트는 잘못된 모델을 전달하려고 시도하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-141">The test doesn't attempt to pass in an invalid model.</span></span> <span data-ttu-id="ffae8-142">([통합 테스트](xref:test/integration-tests)에서 모델 바인딩을 사용하는데도 불구하고) 모델 바인딩이 실행되지 않기 때문에, 잘못된 모델을 전달하는 것은 유효한 접근 방식이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-142">Passing an invalid model isn't a valid approach, since model binding isn't running (although an [integration test](xref:test/integration-tests) does use model binding).</span></span> <span data-ttu-id="ffae8-143">이 예에서는 모델 바인딩을 테스트하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-143">In this case, model binding isn't tested.</span></span> <span data-ttu-id="ffae8-144">이러한 단위 테스트는 작업 메서드의 코드만 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-144">These unit tests are only testing the code in the action method.</span></span>

<span data-ttu-id="ffae8-145">두 번째 테스트는 `ModelState`가 유효한 시기를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-145">The second test verifies that when the `ModelState` is valid:</span></span>

* <span data-ttu-id="ffae8-146">새 `BrainstormSession`이 (리포지토리를 통해) 추가됨.</span><span class="sxs-lookup"><span data-stu-id="ffae8-146">A new `BrainstormSession` is added (via the repository).</span></span>
* <span data-ttu-id="ffae8-147">메서드가 예상 속성과 함께 `RedirectToActionResult`를 반환함.</span><span class="sxs-lookup"><span data-stu-id="ffae8-147">The method returns a `RedirectToActionResult` with the expected properties.</span></span>

<span data-ttu-id="ffae8-148">호출되지 않은 모의 호출은 일반적으로 무시되지만, 설정 호출의 끝부분에서 `Verifiable`을 호출하면 테스트에서 모의 확인이 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-148">Mocked calls that aren't called are normally ignored, but calling `Verifiable` at the end of the setup call allows mock validation in the test.</span></span> <span data-ttu-id="ffae8-149">이것은 `mockRepo.Verify` 호출을 통해 수행되며, 예상된 메서드가 호출되지 않으면 테스트가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-149">This is performed with the call to `mockRepo.Verify`, which fails the test if the expected method wasn't called.</span></span>

> [!NOTE]
> <span data-ttu-id="ffae8-150">이 샘플에 사용된 Moq 라이브러리를 사용하면 확인 가능한 또는 “엄격한” 모의 개체를 확인 불가능한 모의 개체(“느슨한” 모의 개체 또는 스텁이라고도 함)와 혼합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-150">The Moq library used in this sample makes it possible to mix verifiable, or "strict", mocks with non-verifiable mocks (also called "loose" mocks or stubs).</span></span> <span data-ttu-id="ffae8-151">[Moq를 사용하여 모의 동작 사용자 지정](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior)에 대해 자세히 알아보세요.</span><span class="sxs-lookup"><span data-stu-id="ffae8-151">Learn more about [customizing Mock behavior with Moq](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior).</span></span>

<span data-ttu-id="ffae8-152">샘플 앱의 [SessionController](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs)는 특정 브레인스토밍 세션과 관련된 정보를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-152">[SessionController](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs) in the sample app displays information related to a particular brainstorming session.</span></span> <span data-ttu-id="ffae8-153">컨트롤러에는 잘못된 `id` 값(다음 예에는 이러한 시나리오를 다루는 두 개의 `return` 시나리오가 있음)을 처리하는 논리가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-153">The controller includes logic to deal with invalid `id` values (there are two `return` scenarios in the following example to cover these scenarios).</span></span> <span data-ttu-id="ffae8-154">마지막 `return` 문은 새 `StormSessionViewModel`을 보기 (*Controllers/SessionController.cs*)로 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-154">The final `return` statement returns a new `StormSessionViewModel` to the view (*Controllers/SessionController.cs*):</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs?name=snippet_SessionController&highlight=12-16,18-22,31)]

<span data-ttu-id="ffae8-155">단위 테스트에는 세션 컨트롤러 `Index` 작업에 각 `return` 시나리오에 대한 하나의 테스트가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-155">The unit tests include one test for each `return` scenario in the Session controller `Index` action:</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/SessionControllerTests.cs?name=snippet_SessionControllerTests&highlight=2,11-14,18,31-32,36,50-55)]

<span data-ttu-id="ffae8-156">아이디어 컨트롤러로 이동하면 앱은 `api/ideas` 경로에서 기능을 웹 API로 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-156">Moving to the Ideas controller, the app exposes functionality as a web API on the `api/ideas` route:</span></span>

* <span data-ttu-id="ffae8-157">브레인스토밍 세션과 연관된 아이디어(`IdeaDTO`)의 목록이 `ForSession` 메서드에 의해 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-157">A list of ideas (`IdeaDTO`) associated with a brainstorming session is returned by the `ForSession` method.</span></span>
* <span data-ttu-id="ffae8-158">`Create` 메서드는 세션에 새 아이디어를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-158">The `Create` method adds new ideas to a session.</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionAndCreate&highlight=1-2,21-22)]

<span data-ttu-id="ffae8-159">API 호출을 통해 직접 비즈니스 도메인 엔터티를 반환하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="ffae8-159">Avoid returning business domain entities directly via API calls.</span></span> <span data-ttu-id="ffae8-160">도메인 엔터티는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-160">Domain entities:</span></span>

* <span data-ttu-id="ffae8-161">종종 클라이언트에 필요한 것보다 많은 데이터를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-161">Often include more data than the client requires.</span></span>
* <span data-ttu-id="ffae8-162">공개적으로 노출된 API와 앱의 내부 도메인 모델을 불필요하게 결합합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-162">Unnecessarily couple the app's internal domain model with the publicly exposed API.</span></span>

<span data-ttu-id="ffae8-163">도메인 엔터티와 클라이언트에 반환된 유형 사이의 매핑을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-163">Mapping between domain entities and the types returned to the client can be performed:</span></span>

* <span data-ttu-id="ffae8-164">샘플 앱에서 사용하는 LINQ `Select`를 사용하여 수동으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-164">Manually with a LINQ `Select`, as the sample app uses.</span></span> <span data-ttu-id="ffae8-165">자세한 내용은 [LINQ(Language-Integrated Query)](/dotnet/standard/using-linq)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ffae8-165">For more information, see [LINQ (Language Integrated Query)](/dotnet/standard/using-linq).</span></span>
* <span data-ttu-id="ffae8-166">자동으로 [AutoMapper](https://github.com/AutoMapper/AutoMapper)와 같은 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-166">Automatically with a library, such as [AutoMapper](https://github.com/AutoMapper/AutoMapper).</span></span>

<span data-ttu-id="ffae8-167">다음으로 샘플 앱은 아이디어 컨트롤러의 `Create` 및 `ForSession` API 메서드에 대한 단위 테스트를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-167">Next, the sample app demonstrates unit tests for the `Create` and `ForSession` API methods of the Ideas controller.</span></span>

<span data-ttu-id="ffae8-168">샘플 앱에는 두 개의 `ForSession` 테스트가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-168">The sample app contains two `ForSession` tests.</span></span> <span data-ttu-id="ffae8-169">첫 번째 테스트에서 `ForSession`이 잘못된 세션에 대해 <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>(HTTP를 찾을 수 없음)를 반환하는지 여부를 판별합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-169">The first test determines if `ForSession` returns a <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult> (HTTP Not Found) for an invalid session:</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests4&highlight=5,7-8,15-16)]

<span data-ttu-id="ffae8-170">두 번째 `ForSession` 테스트는 `ForSession`에서 유효한 세션에 대한 세션 아이디어(`<List<IdeaDTO>>`) 목록을 반환하는지 여부를 판별합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-170">The second `ForSession` test determines if `ForSession` returns a list of session ideas (`<List<IdeaDTO>>`) for a valid session.</span></span> <span data-ttu-id="ffae8-171">또한 검사는 첫 번째 아이디어를 검사하여 `Name` 속성이 올바른지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-171">The checks also examine the first idea to confirm its `Name` property is correct:</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests5&highlight=5,7-8,15-18)]

<span data-ttu-id="ffae8-172">`ModelState`가 유효하지 않을 때 `Create` 메서드의 동작을 테스트하기 위해 샘플 앱은 테스트의 일부로 컨트롤러에 모델 오류를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-172">To test the behavior of the `Create` method when the `ModelState` is invalid, the sample app adds a model error to the controller as part of the test.</span></span> <span data-ttu-id="ffae8-173">단위 테스트에서 모델 유효성 검사 또는 모델 바인딩을 테스트하지 말고, &mdash;잘못된 값이 나타나면 작업 메서드의 동작만 테스트`ModelState`하세요.</span><span class="sxs-lookup"><span data-stu-id="ffae8-173">Don't try to test model validation or model binding in unit tests&mdash;just test the action method's behavior when confronted with an invalid `ModelState`:</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests1&highlight=7,13)]

<span data-ttu-id="ffae8-174">두 번째 `Create` 테스트는 `null`을 반환하는 리포지토리를 사용하므로 모의 리포지토리가 `null`을 반환하도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-174">The second test of `Create` depends on the repository returning `null`, so the mock repository is configured to return `null`.</span></span> <span data-ttu-id="ffae8-175">테스트 데이터베이스를 만들고(메모리 내부에 또는 다른 위치에) 이 결과를 반환하는 쿼리를 작성할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-175">There's no need to create a test database (in memory or otherwise) and construct a query that returns this result.</span></span> <span data-ttu-id="ffae8-176">샘플 코드에 나온 것과 같이 단일 명령문으로 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-176">The test can be accomplished in a single statement, as the sample code illustrates:</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests2&highlight=7-8,15)]

<span data-ttu-id="ffae8-177">세 번째 `Create` 테스트인 `Create_ReturnsNewlyCreatedIdeaForSession`에서는 리포지토리의 `UpdateAsync` 메서드가 호출되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-177">The third `Create` test, `Create_ReturnsNewlyCreatedIdeaForSession`, verifies that the repository's `UpdateAsync` method is called.</span></span> <span data-ttu-id="ffae8-178">`Verifiable`을 통해 모의 개체가 호출된 후 확인 가능한 메서드가 실행되었는지 확인하기 위해 모의 리포지토리의 `Verify` 메서드가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-178">The mock is called with `Verifiable`, and the mocked repository's `Verify` method is called to confirm the verifiable method is executed.</span></span> <span data-ttu-id="ffae8-179">`UpdateAsync` 메서드가 데이터를 저장하도록 보장하는 것은 단위 테스트의 책임이 아니며 통합 테스트로 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-179">It's not the unit test's responsibility to ensure that the `UpdateAsync` method saved the data&mdash;that can be performed with an integration test.</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests3&highlight=20-22,28-33)]

## <a name="test-actionresultt"></a><span data-ttu-id="ffae8-180">ActionResult\<T> 테스트</span><span class="sxs-lookup"><span data-stu-id="ffae8-180">Test ActionResult\<T></span></span>

<span data-ttu-id="ffae8-181">ASP.NET Core 2.1 이상에서 [ActionResult\<T](xref:web-api/action-return-types#actionresultt-type)(<xref:Microsoft.AspNetCore.Mvc.ActionResult%601>)는 `ActionResult`에서 파생된 형식을 반환하거나 특정 형식을 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-181">In ASP.NET Core 2.1 or later, [ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type) (<xref:Microsoft.AspNetCore.Mvc.ActionResult%601>) enables you to return a type deriving from `ActionResult` or return a specific type.</span></span>

<span data-ttu-id="ffae8-182">샘플 앱에는 지정된 세션 `id`에 대한 `List<IdeaDTO>`를 반환하는 메서드가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-182">The sample app includes a method that returns a `List<IdeaDTO>` for a given session `id`.</span></span> <span data-ttu-id="ffae8-183">세션 `id`가 없으면 컨트롤러는 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-183">If the session `id` doesn't exist, the controller returns <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>:</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionActionResult&highlight=10,21)]

<span data-ttu-id="ffae8-184">`ForSessionActionResult` 컨트롤러에 대한 두 테스트는 `ApiIdeasControllerTests`에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-184">Two tests of the `ForSessionActionResult` controller are included in the `ApiIdeasControllerTests`.</span></span>

<span data-ttu-id="ffae8-185">첫 번째 테스트는 컨트롤러가 `ActionResult`를 반환하지만 존재하지 않는 세션 `id`에 대한 존재하지 않는 아이디어 목록을 반환하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-185">The first test confirms that the controller returns an `ActionResult` but not a nonexistent list of ideas for a nonexistent session `id`:</span></span>

* <span data-ttu-id="ffae8-186">`ActionResult` 유형이 `ActionResult<List<IdeaDTO>>`임.</span><span class="sxs-lookup"><span data-stu-id="ffae8-186">The `ActionResult` type is `ActionResult<List<IdeaDTO>>`.</span></span>
* <span data-ttu-id="ffae8-187"><xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*>는 <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>임.</span><span class="sxs-lookup"><span data-stu-id="ffae8-187">The <xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*> is a <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>.</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=7,10,13-14)]

<span data-ttu-id="ffae8-188">유효한 세션 `id`에 대한 두 번째 테스트는 메서드가 다음을 반환하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-188">For a valid session `id`, the second test confirms that the method returns:</span></span>

* <span data-ttu-id="ffae8-189">`List<IdeaDTO>` 유형의 `ActionResult`.</span><span class="sxs-lookup"><span data-stu-id="ffae8-189">An `ActionResult` with a `List<IdeaDTO>` type.</span></span>
* <span data-ttu-id="ffae8-190">[ActionResult\<T>.Value](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*)가 `List<IdeaDTO>` 유형입니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-190">The [ActionResult\<T>.Value](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) is a `List<IdeaDTO>` type.</span></span>
* <span data-ttu-id="ffae8-191">목록의 첫 번째 항목은 모의 세션(`GetTestSession` 호출로 얻음)에 저장된 아이디어와 일치하는 유효한 아이디어임.</span><span class="sxs-lookup"><span data-stu-id="ffae8-191">The first item in the list is a valid idea matching the idea stored in the mock session (obtained by calling `GetTestSession`).</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsIdeasForSession&highlight=7-8,15-18)]

<span data-ttu-id="ffae8-192">샘플 앱에는 지정된 세션에 대한 새 `Idea`를 작성하는 메서드도 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-192">The sample app also includes a method to create a new `Idea` for a given session.</span></span> <span data-ttu-id="ffae8-193">컨트롤러는 다음을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-193">The controller returns:</span></span>

* <span data-ttu-id="ffae8-194">잘못된 모델의 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>.</span><span class="sxs-lookup"><span data-stu-id="ffae8-194"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> for an invalid model.</span></span>
* <span data-ttu-id="ffae8-195"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>(세션이 없는 경우).</span><span class="sxs-lookup"><span data-stu-id="ffae8-195"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> if the session doesn't exist.</span></span>
* <span data-ttu-id="ffae8-196"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>(세션이 새 아이디어로 업데이트될 때).</span><span class="sxs-lookup"><span data-stu-id="ffae8-196"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> when the session is updated with the new idea.</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_CreateActionResult&highlight=9,16,29)]

<span data-ttu-id="ffae8-197">세 개의 `CreateActionResult` 테스트는 `ApiIdeasControllerTests`에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-197">Three tests of `CreateActionResult` are included in the `ApiIdeasControllerTests`.</span></span>

<span data-ttu-id="ffae8-198">첫 번째 텍스트는 잘못된 모델에 대해 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>가 반환되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-198">The first text confirms that a <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> is returned for an invalid model.</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsBadRequest_GivenInvalidModel&highlight=7,13-14)]

<span data-ttu-id="ffae8-199">두 번째 테스트는 세션이 존재하지 않는 경우 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>가 반환되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-199">The second test checks that a <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> is returned if the session doesn't exist.</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=5,15,22-23)]

<span data-ttu-id="ffae8-200">유효한 세션 `id`의 경우 최종 테스트는 다음을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-200">For a valid session `id`, the final test confirms that:</span></span>

* <span data-ttu-id="ffae8-201">메서드가 `BrainstormSession` 유형의 `ActionResult`를 반환함.</span><span class="sxs-lookup"><span data-stu-id="ffae8-201">The method returns an `ActionResult` with a `BrainstormSession` type.</span></span>
* <span data-ttu-id="ffae8-202">[ActionResult\<T>.Result](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Result*)는 <xref:Microsoft.AspNetCore.Mvc.CreatedAtActionResult>입니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-202">The [ActionResult\<T>.Result](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Result*) is a <xref:Microsoft.AspNetCore.Mvc.CreatedAtActionResult>.</span></span> <span data-ttu-id="ffae8-203">`CreatedAtActionResult`가 `Location` 헤더가 있는 *201 생성됨* 응답과 유사함.</span><span class="sxs-lookup"><span data-stu-id="ffae8-203">`CreatedAtActionResult` is analogous to a *201 Created* response with a `Location` header.</span></span>
* <span data-ttu-id="ffae8-204">[ActionResult\<T>.Value](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*)는 `BrainstormSession` 유형입니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-204">The [ActionResult\<T>.Value](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) is a `BrainstormSession` type.</span></span>
* <span data-ttu-id="ffae8-205">세션을 업데이트하기 위한 모의 호출 `UpdateAsync(testSession)`가 실행됨.</span><span class="sxs-lookup"><span data-stu-id="ffae8-205">The mock call to update the session, `UpdateAsync(testSession)`, was invoked.</span></span> <span data-ttu-id="ffae8-206">`Verifiable` 메서드 호출은 어설션에서 `mockRepo.Verify()`를 실행하여 확인됨.</span><span class="sxs-lookup"><span data-stu-id="ffae8-206">The `Verifiable` method call is checked by executing `mockRepo.Verify()` in the assertions.</span></span>
* <span data-ttu-id="ffae8-207">세션에 대해 두 개의 `Idea` 개체가 반환됨.</span><span class="sxs-lookup"><span data-stu-id="ffae8-207">Two `Idea` objects are returned for the session.</span></span>
* <span data-ttu-id="ffae8-208">마지막 항목(`UpdateAsync`에 대한 모의 호출에 의해 추가된 `Idea`)이 테스트의 세션에 추가된 `newIdea`와 일치함.</span><span class="sxs-lookup"><span data-stu-id="ffae8-208">The last item (the `Idea` added by the mock call to `UpdateAsync`) matches the `newIdea` added to the session in the test.</span></span>

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNewlyCreatedIdeaForSession&highlight=20-22,28-34)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ffae8-209">[컨트롤러](xref:mvc/controllers/actions)는 임의의 ASP.NET Core MVC 앱에서 중심적인 역할을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-209">[Controllers](xref:mvc/controllers/actions) play a central role in any ASP.NET Core MVC app.</span></span> <span data-ttu-id="ffae8-210">따라서 컨트롤러가 의도한 대로 동작한다고 확신할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-210">As such, you should have confidence that controllers behave as intended.</span></span> <span data-ttu-id="ffae8-211">자동화된 테스트는 앱이 프로덕션 환경에 배포되기 전에 오류를 발견할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-211">Automated tests can detect errors before the app is deployed to a production environment.</span></span>

<span data-ttu-id="ffae8-212">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/testing/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ffae8-212">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/testing/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="unit-tests-of-controller-logic"></a><span data-ttu-id="ffae8-213">컨트롤러 논리의 단위 테스트</span><span class="sxs-lookup"><span data-stu-id="ffae8-213">Unit tests of controller logic</span></span>

<span data-ttu-id="ffae8-214">[단위 테스트](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)는 인프라 및 종속성으로부터 격리된 상태에서 앱의 일부를 테스트하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-214">[Unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) involve testing a part of an app in isolation from its infrastructure and dependencies.</span></span> <span data-ttu-id="ffae8-215">컨트롤러 논리를 유닛 테스트하는 경우 단일 작업의 콘텐츠만 테스트되고, 종속성 또는 프레임워크 자체의 동작은 테스트되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-215">When unit testing controller logic, only the contents of a single action are tested, not the behavior of its dependencies or of the framework itself.</span></span>

<span data-ttu-id="ffae8-216">컨트롤러의 동작에 초점을 맞춰 컨트롤러 동작의 단위 테스트를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-216">Set up unit tests of controller actions to focus on the controller's behavior.</span></span> <span data-ttu-id="ffae8-217">컨트롤러 단위 테스트는 [필터](xref:mvc/controllers/filters), [라우팅](xref:fundamentals/routing), [모델 바인딩](xref:mvc/models/model-binding) 같은 시나리오를 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-217">A controller unit test avoids scenarios such as [filters](xref:mvc/controllers/filters), [routing](xref:fundamentals/routing), and [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="ffae8-218">전체적으로 요청에 응답하는 구성 요소 간 상호 작용을 포함하는 테스트는 ‘통합 테스트’에서 처리합니다. </span><span class="sxs-lookup"><span data-stu-id="ffae8-218">Tests that cover the interactions among components that collectively respond to a request are handled by *integration tests*.</span></span> <span data-ttu-id="ffae8-219">통합 테스트에 대한 자세한 내용은 <xref:test/integration-tests>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ffae8-219">For more information on integration tests, see <xref:test/integration-tests>.</span></span>

<span data-ttu-id="ffae8-220">사용자 지정 필터 및 경로를 작성할 때 특정 컨트롤러 작업에 대한 테스트의 일부가 아닌 별도로 단위 테스트를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-220">If you're writing custom filters and routes, unit test them in isolation, not as part of tests on a particular controller action.</span></span>

<span data-ttu-id="ffae8-221">컨트롤러 단위 테스트를 데모하려면 샘플 앱에서 다음 컨트롤러를 검토하세요.</span><span class="sxs-lookup"><span data-stu-id="ffae8-221">To demonstrate controller unit tests, review the following controller in the sample app.</span></span> <span data-ttu-id="ffae8-222">홈 컨트롤러는 브레인스토밍 세션 목록을 표시하며 POST 요청을 사용하여 새로운 브레인스토밍 세션을 만들 수 있게 합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-222">The Home controller displays a list of brainstorming sessions and allows the creation of new brainstorming sessions with a POST request:</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/HomeController.cs?name=snippet_HomeController&highlight=1,5,10,31-32)]

<span data-ttu-id="ffae8-223">이전 컨트롤러는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-223">The preceding controller:</span></span>

* <span data-ttu-id="ffae8-224">[명시적 종속성 원칙](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-224">Follows the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>
* <span data-ttu-id="ffae8-225">[DI(종속성 주입)](xref:fundamentals/dependency-injection)에서 `IBrainstormSessionRepository`의 인스턴스를 제공할 것을 예상합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-225">Expects [dependency injection (DI)](xref:fundamentals/dependency-injection) to provide an instance of `IBrainstormSessionRepository`.</span></span>
* <span data-ttu-id="ffae8-226">[Moq](https://www.nuget.org/packages/Moq/)와 같은 모의 개체 프레임워크를 사용하여 모의 `IBrainstormSessionRepository` 서비스로 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-226">Can be tested with a mocked `IBrainstormSessionRepository` service using a mock object framework, such as [Moq](https://www.nuget.org/packages/Moq/).</span></span> <span data-ttu-id="ffae8-227">‘모의 개체’는 테스트에 사용되는 사전 결정된 속성 및 메서드 동작 집합이 있는 제작된 개체입니다. </span><span class="sxs-lookup"><span data-stu-id="ffae8-227">A *mocked object* is a fabricated object with a predetermined set of property and method behaviors used for testing.</span></span> <span data-ttu-id="ffae8-228">자세한 내용은 [통합 테스트 소개](xref:test/integration-tests#introduction-to-integration-tests)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ffae8-228">For more information, see [Introduction to integration tests](xref:test/integration-tests#introduction-to-integration-tests).</span></span>

<span data-ttu-id="ffae8-229">`HTTP GET Index` 메서드는 반복 또는 분기가 없으며 한 가지 메서드만 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-229">The `HTTP GET Index` method has no looping or branching and only calls one method.</span></span> <span data-ttu-id="ffae8-230">이 동작의 단위 테스트는 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-230">The unit test for this action:</span></span>

* <span data-ttu-id="ffae8-231">`GetTestSessions` 메서드를 사용하여 `IBrainstormSessionRepository` 서비스를 모방합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-231">Mocks the `IBrainstormSessionRepository` service using the `GetTestSessions` method.</span></span> <span data-ttu-id="ffae8-232">`GetTestSessions`는 날짜 및 세션 이름이 있는 두 개의 모의 브레인스토밍 세션을 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-232">`GetTestSessions` creates two mock brainstorm sessions with dates and session names.</span></span>
* <span data-ttu-id="ffae8-233">`Index` 메서드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-233">Executes the `Index` method.</span></span>
* <span data-ttu-id="ffae8-234">메서드에서 반환한 결과에 대한 어설션을 작성합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-234">Makes assertions on the result returned by the method:</span></span>
  * <span data-ttu-id="ffae8-235"><xref:Microsoft.AspNetCore.Mvc.ViewResult>가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-235">A <xref:Microsoft.AspNetCore.Mvc.ViewResult> is returned.</span></span>
  * <span data-ttu-id="ffae8-236">[ViewDataDictionary.Model](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary.Model*)은 `StormSessionViewModel`입니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-236">The [ViewDataDictionary.Model](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary.Model*) is a `StormSessionViewModel`.</span></span>
  * <span data-ttu-id="ffae8-237">`ViewDataDictionary.Model`에 저장된 두 개의 브레인스토밍 세션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-237">There are two brainstorming sessions stored in the `ViewDataDictionary.Model`.</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_Index_ReturnsAViewResult_WithAListOfBrainstormSessions&highlight=14-17)]

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_GetTestSessions)]

<span data-ttu-id="ffae8-238">홈 컨트롤러의 `HTTP POST Index` 메서드는 다음을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-238">The Home controller's `HTTP POST Index` method tests verifies that:</span></span>

* <span data-ttu-id="ffae8-239">[ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid*)가 `false`인 경우 작업 메서드가 적절한 데이터와 함께 ‘400 잘못된 요청’ <xref:Microsoft.AspNetCore.Mvc.ViewResult>를 반환함. </span><span class="sxs-lookup"><span data-stu-id="ffae8-239">When [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid*) is `false`, the action method returns a *400 Bad Request* <xref:Microsoft.AspNetCore.Mvc.ViewResult> with the appropriate data.</span></span>
* <span data-ttu-id="ffae8-240">`ModelState.IsValid`가 `true`인 경우:</span><span class="sxs-lookup"><span data-stu-id="ffae8-240">When `ModelState.IsValid` is `true`:</span></span>
  * <span data-ttu-id="ffae8-241">리포지토리에서 `Add` 메서드를 호출함.</span><span class="sxs-lookup"><span data-stu-id="ffae8-241">The `Add` method on the repository is called.</span></span>
  * <span data-ttu-id="ffae8-242"><xref:Microsoft.AspNetCore.Mvc.RedirectToActionResult>가 올바른 인수와 함께 반환됨.</span><span class="sxs-lookup"><span data-stu-id="ffae8-242">A <xref:Microsoft.AspNetCore.Mvc.RedirectToActionResult> is returned with the correct arguments.</span></span>

<span data-ttu-id="ffae8-243">아래의 첫 번째 테스트처럼 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*>를 사용해 오류를 추가하여 잘못된 모델 상태를 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-243">An invalid model state is tested by adding errors using <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> as shown in the first test below:</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_ModelState_ValidOrInvalid&highlight=9,16-17,38-41)]

<span data-ttu-id="ffae8-244">[ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)가 유효하지 않으면 GET 요청의 경우와 동일한 `ViewResult`가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-244">When [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) isn't valid, the same `ViewResult` is returned as for a GET request.</span></span> <span data-ttu-id="ffae8-245">이 테스트는 잘못된 모델을 전달하려고 시도하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-245">The test doesn't attempt to pass in an invalid model.</span></span> <span data-ttu-id="ffae8-246">([통합 테스트](xref:test/integration-tests)에서 모델 바인딩을 사용하는데도 불구하고) 모델 바인딩이 실행되지 않기 때문에, 잘못된 모델을 전달하는 것은 유효한 접근 방식이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-246">Passing an invalid model isn't a valid approach, since model binding isn't running (although an [integration test](xref:test/integration-tests) does use model binding).</span></span> <span data-ttu-id="ffae8-247">이 예에서는 모델 바인딩을 테스트하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-247">In this case, model binding isn't tested.</span></span> <span data-ttu-id="ffae8-248">이러한 단위 테스트는 작업 메서드의 코드만 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-248">These unit tests are only testing the code in the action method.</span></span>

<span data-ttu-id="ffae8-249">두 번째 테스트는 `ModelState`가 유효한 시기를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-249">The second test verifies that when the `ModelState` is valid:</span></span>

* <span data-ttu-id="ffae8-250">새 `BrainstormSession`이 (리포지토리를 통해) 추가됨.</span><span class="sxs-lookup"><span data-stu-id="ffae8-250">A new `BrainstormSession` is added (via the repository).</span></span>
* <span data-ttu-id="ffae8-251">메서드가 예상 속성과 함께 `RedirectToActionResult`를 반환함.</span><span class="sxs-lookup"><span data-stu-id="ffae8-251">The method returns a `RedirectToActionResult` with the expected properties.</span></span>

<span data-ttu-id="ffae8-252">호출되지 않은 모의 호출은 일반적으로 무시되지만, 설정 호출의 끝부분에서 `Verifiable`을 호출하면 테스트에서 모의 확인이 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-252">Mocked calls that aren't called are normally ignored, but calling `Verifiable` at the end of the setup call allows mock validation in the test.</span></span> <span data-ttu-id="ffae8-253">이것은 `mockRepo.Verify` 호출을 통해 수행되며, 예상된 메서드가 호출되지 않으면 테스트가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-253">This is performed with the call to `mockRepo.Verify`, which fails the test if the expected method wasn't called.</span></span>

> [!NOTE]
> <span data-ttu-id="ffae8-254">이 샘플에 사용된 Moq 라이브러리를 사용하면 확인 가능한 또는 “엄격한” 모의 개체를 확인 불가능한 모의 개체(“느슨한” 모의 개체 또는 스텁이라고도 함)와 혼합할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-254">The Moq library used in this sample makes it possible to mix verifiable, or "strict", mocks with non-verifiable mocks (also called "loose" mocks or stubs).</span></span> <span data-ttu-id="ffae8-255">[Moq를 사용하여 모의 동작 사용자 지정](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior)에 대해 자세히 알아보세요.</span><span class="sxs-lookup"><span data-stu-id="ffae8-255">Learn more about [customizing Mock behavior with Moq](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior).</span></span>

<span data-ttu-id="ffae8-256">샘플 앱의 [SessionController](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs)는 특정 브레인스토밍 세션과 관련된 정보를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-256">[SessionController](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs) in the sample app displays information related to a particular brainstorming session.</span></span> <span data-ttu-id="ffae8-257">컨트롤러에는 잘못된 `id` 값(다음 예에는 이러한 시나리오를 다루는 두 개의 `return` 시나리오가 있음)을 처리하는 논리가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-257">The controller includes logic to deal with invalid `id` values (there are two `return` scenarios in the following example to cover these scenarios).</span></span> <span data-ttu-id="ffae8-258">마지막 `return` 문은 새 `StormSessionViewModel`을 보기 (*Controllers/SessionController.cs*)로 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-258">The final `return` statement returns a new `StormSessionViewModel` to the view (*Controllers/SessionController.cs*):</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs?name=snippet_SessionController&highlight=12-16,18-22,31)]

<span data-ttu-id="ffae8-259">단위 테스트에는 세션 컨트롤러 `Index` 작업에 각 `return` 시나리오에 대한 하나의 테스트가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-259">The unit tests include one test for each `return` scenario in the Session controller `Index` action:</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/SessionControllerTests.cs?name=snippet_SessionControllerTests&highlight=2,11-14,18,31-32,36,50-55)]

<span data-ttu-id="ffae8-260">아이디어 컨트롤러로 이동하면 앱은 `api/ideas` 경로에서 기능을 웹 API로 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-260">Moving to the Ideas controller, the app exposes functionality as a web API on the `api/ideas` route:</span></span>

* <span data-ttu-id="ffae8-261">브레인스토밍 세션과 연관된 아이디어(`IdeaDTO`)의 목록이 `ForSession` 메서드에 의해 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-261">A list of ideas (`IdeaDTO`) associated with a brainstorming session is returned by the `ForSession` method.</span></span>
* <span data-ttu-id="ffae8-262">`Create` 메서드는 세션에 새 아이디어를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-262">The `Create` method adds new ideas to a session.</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionAndCreate&highlight=1-2,21-22)]

<span data-ttu-id="ffae8-263">API 호출을 통해 직접 비즈니스 도메인 엔터티를 반환하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="ffae8-263">Avoid returning business domain entities directly via API calls.</span></span> <span data-ttu-id="ffae8-264">도메인 엔터티는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-264">Domain entities:</span></span>

* <span data-ttu-id="ffae8-265">종종 클라이언트에 필요한 것보다 많은 데이터를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-265">Often include more data than the client requires.</span></span>
* <span data-ttu-id="ffae8-266">공개적으로 노출된 API와 앱의 내부 도메인 모델을 불필요하게 결합합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-266">Unnecessarily couple the app's internal domain model with the publicly exposed API.</span></span>

<span data-ttu-id="ffae8-267">도메인 엔터티와 클라이언트에 반환된 유형 사이의 매핑을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-267">Mapping between domain entities and the types returned to the client can be performed:</span></span>

* <span data-ttu-id="ffae8-268">샘플 앱에서 사용하는 LINQ `Select`를 사용하여 수동으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-268">Manually with a LINQ `Select`, as the sample app uses.</span></span> <span data-ttu-id="ffae8-269">자세한 내용은 [LINQ(Language-Integrated Query)](/dotnet/standard/using-linq)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ffae8-269">For more information, see [LINQ (Language Integrated Query)](/dotnet/standard/using-linq).</span></span>
* <span data-ttu-id="ffae8-270">자동으로 [AutoMapper](https://github.com/AutoMapper/AutoMapper)와 같은 라이브러리를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-270">Automatically with a library, such as [AutoMapper](https://github.com/AutoMapper/AutoMapper).</span></span>

<span data-ttu-id="ffae8-271">다음으로 샘플 앱은 아이디어 컨트롤러의 `Create` 및 `ForSession` API 메서드에 대한 단위 테스트를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-271">Next, the sample app demonstrates unit tests for the `Create` and `ForSession` API methods of the Ideas controller.</span></span>

<span data-ttu-id="ffae8-272">샘플 앱에는 두 개의 `ForSession` 테스트가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-272">The sample app contains two `ForSession` tests.</span></span> <span data-ttu-id="ffae8-273">첫 번째 테스트에서 `ForSession`이 잘못된 세션에 대해 <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>(HTTP를 찾을 수 없음)를 반환하는지 여부를 판별합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-273">The first test determines if `ForSession` returns a <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult> (HTTP Not Found) for an invalid session:</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests4&highlight=5,7-8,15-16)]

<span data-ttu-id="ffae8-274">두 번째 `ForSession` 테스트는 `ForSession`에서 유효한 세션에 대한 세션 아이디어(`<List<IdeaDTO>>`) 목록을 반환하는지 여부를 판별합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-274">The second `ForSession` test determines if `ForSession` returns a list of session ideas (`<List<IdeaDTO>>`) for a valid session.</span></span> <span data-ttu-id="ffae8-275">또한 검사는 첫 번째 아이디어를 검사하여 `Name` 속성이 올바른지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-275">The checks also examine the first idea to confirm its `Name` property is correct:</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests5&highlight=5,7-8,15-18)]

<span data-ttu-id="ffae8-276">`ModelState`가 유효하지 않을 때 `Create` 메서드의 동작을 테스트하기 위해 샘플 앱은 테스트의 일부로 컨트롤러에 모델 오류를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-276">To test the behavior of the `Create` method when the `ModelState` is invalid, the sample app adds a model error to the controller as part of the test.</span></span> <span data-ttu-id="ffae8-277">단위 테스트에서 모델 유효성 검사 또는 모델 바인딩을 테스트하지 말고, &mdash;잘못된 값이 나타나면 작업 메서드의 동작만 테스트`ModelState`하세요.</span><span class="sxs-lookup"><span data-stu-id="ffae8-277">Don't try to test model validation or model binding in unit tests&mdash;just test the action method's behavior when confronted with an invalid `ModelState`:</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests1&highlight=7,13)]

<span data-ttu-id="ffae8-278">두 번째 `Create` 테스트는 `null`을 반환하는 리포지토리를 사용하므로 모의 리포지토리가 `null`을 반환하도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-278">The second test of `Create` depends on the repository returning `null`, so the mock repository is configured to return `null`.</span></span> <span data-ttu-id="ffae8-279">테스트 데이터베이스를 만들고(메모리 내부에 또는 다른 위치에) 이 결과를 반환하는 쿼리를 작성할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-279">There's no need to create a test database (in memory or otherwise) and construct a query that returns this result.</span></span> <span data-ttu-id="ffae8-280">샘플 코드에 나온 것과 같이 단일 명령문으로 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-280">The test can be accomplished in a single statement, as the sample code illustrates:</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests2&highlight=7-8,15)]

<span data-ttu-id="ffae8-281">세 번째 `Create` 테스트인 `Create_ReturnsNewlyCreatedIdeaForSession`에서는 리포지토리의 `UpdateAsync` 메서드가 호출되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-281">The third `Create` test, `Create_ReturnsNewlyCreatedIdeaForSession`, verifies that the repository's `UpdateAsync` method is called.</span></span> <span data-ttu-id="ffae8-282">`Verifiable`을 통해 모의 개체가 호출된 후 확인 가능한 메서드가 실행되었는지 확인하기 위해 모의 리포지토리의 `Verify` 메서드가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-282">The mock is called with `Verifiable`, and the mocked repository's `Verify` method is called to confirm the verifiable method is executed.</span></span> <span data-ttu-id="ffae8-283">`UpdateAsync` 메서드가 데이터를 저장하도록 보장하는 것은 단위 테스트의 책임이 아니며 통합 테스트로 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-283">It's not the unit test's responsibility to ensure that the `UpdateAsync` method saved the data&mdash;that can be performed with an integration test.</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests3&highlight=20-22,28-33)]

## <a name="test-actionresultt"></a><span data-ttu-id="ffae8-284">ActionResult\<T> 테스트</span><span class="sxs-lookup"><span data-stu-id="ffae8-284">Test ActionResult\<T></span></span>

<span data-ttu-id="ffae8-285">ASP.NET Core 2.1 이상에서 [ActionResult\<T](xref:web-api/action-return-types#actionresultt-type)(<xref:Microsoft.AspNetCore.Mvc.ActionResult%601>)는 `ActionResult`에서 파생된 형식을 반환하거나 특정 형식을 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-285">In ASP.NET Core 2.1 or later, [ActionResult\<T>](xref:web-api/action-return-types#actionresultt-type) (<xref:Microsoft.AspNetCore.Mvc.ActionResult%601>) enables you to return a type deriving from `ActionResult` or return a specific type.</span></span>

<span data-ttu-id="ffae8-286">샘플 앱에는 지정된 세션 `id`에 대한 `List<IdeaDTO>`를 반환하는 메서드가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-286">The sample app includes a method that returns a `List<IdeaDTO>` for a given session `id`.</span></span> <span data-ttu-id="ffae8-287">세션 `id`가 없으면 컨트롤러는 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-287">If the session `id` doesn't exist, the controller returns <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>:</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionActionResult&highlight=10,21)]

<span data-ttu-id="ffae8-288">`ForSessionActionResult` 컨트롤러에 대한 두 테스트는 `ApiIdeasControllerTests`에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-288">Two tests of the `ForSessionActionResult` controller are included in the `ApiIdeasControllerTests`.</span></span>

<span data-ttu-id="ffae8-289">첫 번째 테스트는 컨트롤러가 `ActionResult`를 반환하지만 존재하지 않는 세션 `id`에 대한 존재하지 않는 아이디어 목록을 반환하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-289">The first test confirms that the controller returns an `ActionResult` but not a nonexistent list of ideas for a nonexistent session `id`:</span></span>

* <span data-ttu-id="ffae8-290">`ActionResult` 유형이 `ActionResult<List<IdeaDTO>>`임.</span><span class="sxs-lookup"><span data-stu-id="ffae8-290">The `ActionResult` type is `ActionResult<List<IdeaDTO>>`.</span></span>
* <span data-ttu-id="ffae8-291"><xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*>는 <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>임.</span><span class="sxs-lookup"><span data-stu-id="ffae8-291">The <xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*> is a <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>.</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=7,10,13-14)]

<span data-ttu-id="ffae8-292">유효한 세션 `id`에 대한 두 번째 테스트는 메서드가 다음을 반환하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-292">For a valid session `id`, the second test confirms that the method returns:</span></span>

* <span data-ttu-id="ffae8-293">`List<IdeaDTO>` 유형의 `ActionResult`.</span><span class="sxs-lookup"><span data-stu-id="ffae8-293">An `ActionResult` with a `List<IdeaDTO>` type.</span></span>
* <span data-ttu-id="ffae8-294">[ActionResult\<T>.Value](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*)가 `List<IdeaDTO>` 유형입니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-294">The [ActionResult\<T>.Value](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) is a `List<IdeaDTO>` type.</span></span>
* <span data-ttu-id="ffae8-295">목록의 첫 번째 항목은 모의 세션(`GetTestSession` 호출로 얻음)에 저장된 아이디어와 일치하는 유효한 아이디어임.</span><span class="sxs-lookup"><span data-stu-id="ffae8-295">The first item in the list is a valid idea matching the idea stored in the mock session (obtained by calling `GetTestSession`).</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsIdeasForSession&highlight=7-8,15-18)]

<span data-ttu-id="ffae8-296">샘플 앱에는 지정된 세션에 대한 새 `Idea`를 작성하는 메서드도 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-296">The sample app also includes a method to create a new `Idea` for a given session.</span></span> <span data-ttu-id="ffae8-297">컨트롤러는 다음을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-297">The controller returns:</span></span>

* <span data-ttu-id="ffae8-298">잘못된 모델의 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>.</span><span class="sxs-lookup"><span data-stu-id="ffae8-298"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> for an invalid model.</span></span>
* <span data-ttu-id="ffae8-299"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>(세션이 없는 경우).</span><span class="sxs-lookup"><span data-stu-id="ffae8-299"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> if the session doesn't exist.</span></span>
* <span data-ttu-id="ffae8-300"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>(세션이 새 아이디어로 업데이트될 때).</span><span class="sxs-lookup"><span data-stu-id="ffae8-300"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> when the session is updated with the new idea.</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_CreateActionResult&highlight=9,16,29)]

<span data-ttu-id="ffae8-301">세 개의 `CreateActionResult` 테스트는 `ApiIdeasControllerTests`에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-301">Three tests of `CreateActionResult` are included in the `ApiIdeasControllerTests`.</span></span>

<span data-ttu-id="ffae8-302">첫 번째 텍스트는 잘못된 모델에 대해 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>가 반환되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-302">The first text confirms that a <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> is returned for an invalid model.</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsBadRequest_GivenInvalidModel&highlight=7,13-14)]

<span data-ttu-id="ffae8-303">두 번째 테스트는 세션이 존재하지 않는 경우 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>가 반환되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-303">The second test checks that a <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> is returned if the session doesn't exist.</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=5,15,22-23)]

<span data-ttu-id="ffae8-304">유효한 세션 `id`의 경우 최종 테스트는 다음을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-304">For a valid session `id`, the final test confirms that:</span></span>

* <span data-ttu-id="ffae8-305">메서드가 `BrainstormSession` 유형의 `ActionResult`를 반환함.</span><span class="sxs-lookup"><span data-stu-id="ffae8-305">The method returns an `ActionResult` with a `BrainstormSession` type.</span></span>
* <span data-ttu-id="ffae8-306">[ActionResult\<T>.Result](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Result*)는 <xref:Microsoft.AspNetCore.Mvc.CreatedAtActionResult>입니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-306">The [ActionResult\<T>.Result](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Result*) is a <xref:Microsoft.AspNetCore.Mvc.CreatedAtActionResult>.</span></span> <span data-ttu-id="ffae8-307">`CreatedAtActionResult`가 `Location` 헤더가 있는 *201 생성됨* 응답과 유사함.</span><span class="sxs-lookup"><span data-stu-id="ffae8-307">`CreatedAtActionResult` is analogous to a *201 Created* response with a `Location` header.</span></span>
* <span data-ttu-id="ffae8-308">[ActionResult\<T>.Value](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*)는 `BrainstormSession` 유형입니다.</span><span class="sxs-lookup"><span data-stu-id="ffae8-308">The [ActionResult\<T>.Value](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) is a `BrainstormSession` type.</span></span>
* <span data-ttu-id="ffae8-309">세션을 업데이트하기 위한 모의 호출 `UpdateAsync(testSession)`가 실행됨.</span><span class="sxs-lookup"><span data-stu-id="ffae8-309">The mock call to update the session, `UpdateAsync(testSession)`, was invoked.</span></span> <span data-ttu-id="ffae8-310">`Verifiable` 메서드 호출은 어설션에서 `mockRepo.Verify()`를 실행하여 확인됨.</span><span class="sxs-lookup"><span data-stu-id="ffae8-310">The `Verifiable` method call is checked by executing `mockRepo.Verify()` in the assertions.</span></span>
* <span data-ttu-id="ffae8-311">세션에 대해 두 개의 `Idea` 개체가 반환됨.</span><span class="sxs-lookup"><span data-stu-id="ffae8-311">Two `Idea` objects are returned for the session.</span></span>
* <span data-ttu-id="ffae8-312">마지막 항목(`UpdateAsync`에 대한 모의 호출에 의해 추가된 `Idea`)이 테스트의 세션에 추가된 `newIdea`와 일치함.</span><span class="sxs-lookup"><span data-stu-id="ffae8-312">The last item (the `Idea` added by the mock call to `UpdateAsync`) matches the `newIdea` added to the session in the test.</span></span>

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNewlyCreatedIdeaForSession&highlight=20-22,28-34)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="ffae8-313">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="ffae8-313">Additional resources</span></span>

* <xref:test/integration-tests>
* <span data-ttu-id="ffae8-314">[Visual Studio를 사용하여 단위 테스트를 만들고 실행](/visualstudio/test/unit-test-your-code)</span><span class="sxs-lookup"><span data-stu-id="ffae8-314">[Create and run unit tests with Visual Studio](/visualstudio/test/unit-test-your-code).</span></span>
