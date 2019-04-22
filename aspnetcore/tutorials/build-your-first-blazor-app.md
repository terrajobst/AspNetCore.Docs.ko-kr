---
title: 첫 번째 Blazor 앱 빌드
author: guardrex
description: Blazor 앱을 단계별로 빌드합니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: tutorials/first-blazor-app
ms.openlocfilehash: 310eb211f68076d6f52d6427940e07736d833e5b
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614712"
---
# <a name="build-your-first-blazor-app"></a><span data-ttu-id="f9253-103">첫 번째 Blazor 앱 빌드</span><span class="sxs-lookup"><span data-stu-id="f9253-103">Build your first Blazor app</span></span>

<span data-ttu-id="f9253-104">작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f9253-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

<span data-ttu-id="f9253-105">이 자습서에서는 서버 쪽 Razor 구성 요소 또는 클라이언트 쪽 Blazor를 사용하여 앱을 빌드하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-105">This tutorial shows you how to build an app with server-side Razor Components or client-side Blazor.</span></span>

<span data-ttu-id="f9253-106">서버 쪽 ASP.NET Core Razor 구성 요소(*권장*)를 사용하는 환경의 경우:</span><span class="sxs-lookup"><span data-stu-id="f9253-106">For an experience using server-side ASP.NET Core Razor Components (*recommended*):</span></span>

* <span data-ttu-id="f9253-107"><xref:blazor/get-started#server-side-razor-components-experience>의 *Razor 구성 요소 환경* 지침에 따라 Razor 구성 요소 기반 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-107">Follow the *Razor Components experience* guidance in <xref:blazor/get-started#server-side-razor-components-experience> to create a Razor Components-based project.</span></span>
* <span data-ttu-id="f9253-108">프로젝트 이름을 `RazorComponents`로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-108">Name the project `RazorComponents`.</span></span>

<span data-ttu-id="f9253-109">Blazor를 사용하는 환경의 경우:</span><span class="sxs-lookup"><span data-stu-id="f9253-109">For an experience using Blazor:</span></span>

* <span data-ttu-id="f9253-110"><xref:blazor/get-started#client-side-blazor-experience>의 *Razor 환경* 지침에 따라 Razor 구성 요소 기반 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-110">Follow the *Blazor experience* guidance in <xref:blazor/get-started#client-side-blazor-experience> to create a Blazor-based project.</span></span>
* <span data-ttu-id="f9253-111">프로젝트 이름을 `Blazor`로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-111">Name the project `Blazor`.</span></span>

<span data-ttu-id="f9253-112">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/build-your-first-blazor-app/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f9253-112">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/build-your-first-blazor-app/samples/) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="f9253-113">필수 구성 요소에 대해서는 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f9253-113">See the following topics for prerequisites:</span></span>

## <a name="build-components"></a><span data-ttu-id="f9253-114">구성 요소 빌드</span><span class="sxs-lookup"><span data-stu-id="f9253-114">Build components</span></span>

1. <span data-ttu-id="f9253-115">*Components/Pages* 폴더(Blazor의 *Pages*)에서 앱의 세 페이지 홈, 카운터 및 Fetch 데이터.</span><span class="sxs-lookup"><span data-stu-id="f9253-115">Browse to each of the app's three pages in the *Components/Pages* folder (*Pages* in Blazor): Home, Counter, and Fetch data.</span></span> <span data-ttu-id="f9253-116">이러한 페이지는 Razor 구성 요소 파일 *Index.razor*, *Counter.razor* 및 *FetchData.razor*로 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-116">These pages are implemented by Razor Component files: *Index.razor*, *Counter.razor*, and *FetchData.razor*.</span></span> <span data-ttu-id="f9253-117">(Blazor는 *.cshtml* 파일 확장명을 계속 사용하므로 *Index.cshtml*, *Counter.cshtml* 및 *FetchData.cshtml*입니다.)</span><span class="sxs-lookup"><span data-stu-id="f9253-117">(Blazor continues to use the *.cshtml* file extension: *Index.cshtml*, *Counter.cshtml*, and *FetchData.cshtml*.)</span></span>

1. <span data-ttu-id="f9253-118">카운터 페이지에서 **Click me** 단추를 선택하여 페이지 새로 고침 없이 카운터를 증분합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-118">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="f9253-119">웹 페이지의 카운터를 증분하려면 일반적으로 JavaScript를 작성해야 하지만, Blazor는 C#를 사용하여 더 나은 접근 방식을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-119">Incrementing a counter in a webpage normally requires writing JavaScript, but Blazor provides a better approach using C#.</span></span>

1. <span data-ttu-id="f9253-120">*Counter.razor* 파일에서 Counter 구성 요소의 구현을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-120">Examine the implementation of the Counter component in the *Counter.razor* file.</span></span>

   <span data-ttu-id="f9253-121">*Components/Pages/Counter.razor*(Blazor의 *Pages/Counter.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="f9253-121">*Components/Pages/Counter.razor* (*Pages/Counter.cshtml* in Blazor):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="f9253-122">Counter 구성 요소의 UI는 HTML을 사용하여 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-122">The UI of the Counter component is defined using HTML.</span></span> <span data-ttu-id="f9253-123">동적 렌더링 논리(예: 루프, 조건, 식)는 [Razor](xref:mvc/views/razor)라는 포함된 C# 구문을 사용하여 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-123">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="f9253-124">HTML 태그 및 C# 렌더링 논리는 빌드 시 구성 요소 클래스로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-124">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="f9253-125">생성된 .NET 클래스의 이름은 파일 이름과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-125">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="f9253-126">구성 요소 클래스의 멤버는 `@functions` 블록에 정의되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-126">Members of the component class are defined in a `@functions` block.</span></span> <span data-ttu-id="f9253-127">`@functions` 블록에서 구성 요소 상태(속성, 필드) 및 메서드는 이벤트 처리 또는 기타 구성 요소를 정의하기 위해 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-127">In the `@functions` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="f9253-128">그런 다음, 이러한 멤버를 구성 요소 렌더링 논리의 일부 및 이벤트 처리에 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-128">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="f9253-129">**Click me** 단추를 선택한 경우:</span><span class="sxs-lookup"><span data-stu-id="f9253-129">When the **Click me** button is selected:</span></span>

   * <span data-ttu-id="f9253-130">Counter 구성 요소의 등록된 `onclick` 처리기가 호출됩니다(`IncrementCount` 메서드).</span><span class="sxs-lookup"><span data-stu-id="f9253-130">The Counter component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="f9253-131">Counter 구성 요소가 렌더링 트리를 다시 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-131">The Counter component regenerates its render tree.</span></span>
   * <span data-ttu-id="f9253-132">새 렌더링 트리가 이전 렌더링 트리에 비교됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-132">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="f9253-133">DOM(문서 개체 모델)에 대한 수정 내용만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-133">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="f9253-134">표시된 개수가 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-134">The displayed count is updated.</span></span>

1. <span data-ttu-id="f9253-135">또한 Counter 구성 요소의 C# 논리를 수정하여 카운트를 1이 아닌 2씩 증분하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-135">Modify the C# logic of the Counter component to make the count increment by two instead of one.</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="f9253-136">앱을 다시 빌드하고 실행하여 변경 내용을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-136">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="f9253-137">**Click me** 단추를 선택하면 카운터가 2씩 증분됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-137">Select the **Click me** button, and the counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="f9253-138">구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="f9253-138">Use components</span></span>

<span data-ttu-id="f9253-139">HTML 유사 구문을 사용하여 구성 요소를 다른 구성 요소에 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-139">Include a component into another component using an HTML-like syntax.</span></span>

1. <span data-ttu-id="f9253-140">Index 구성 요소에 `<Counter />` 요소를 추가하여 앱의 Index(홈) 구성 요소에 Counter 구성 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-140">Add the Counter component to the app's Index (Home) component by adding a `<Counter />` element to the Index component.</span></span>

   <span data-ttu-id="f9253-141">이 환경에 Blazor를 사용하는 경우에는 Survey Prompt 구성 요소(`<SurveyPrompt>` 요소)가 Index 구성 요소에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-141">If you're using Blazor for this experience, a Survey Prompt component (`<SurveyPrompt>` element) is in the Index component.</span></span> <span data-ttu-id="f9253-142">`<SurveyPrompt>` 요소를 `<Counter>` 요소로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-142">Replace the `<SurveyPrompt>` element with the `<Counter>` element.</span></span>

   <span data-ttu-id="f9253-143">*Components/Pages/Index.razor*(Blazor의 *Pages/Index.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="f9253-143">*Components/Pages/Index.razor* (*Pages/Index.cshtml* in Blazor):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Index.razor?highlight=7)]

1. <span data-ttu-id="f9253-144">앱을 다시 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-144">Rebuild and run the app.</span></span> <span data-ttu-id="f9253-145">홈페이지에는 고유한 카운터가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-145">The Home page has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="f9253-146">구성 요소 매개 변수</span><span class="sxs-lookup"><span data-stu-id="f9253-146">Component parameters</span></span>

<span data-ttu-id="f9253-147">구성 요소에는 매개 변수도 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-147">Components can also have parameters.</span></span> <span data-ttu-id="f9253-148">구성 요소 매개 변수는 `[Parameter]`로 데코레이트된 component 클래스에서 public이 아닌 속성을 사용하여 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-148">Component parameters are defined using non-public properties on the component class decorated with `[Parameter]`.</span></span> <span data-ttu-id="f9253-149">특성을 사용하여 태그에서 구성 요소의 인수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-149">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="f9253-150">구성 요소의 `@functions` C# 코드를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-150">Update the component's `@functions` C# code:</span></span>

   * <span data-ttu-id="f9253-151">`[Parameter]` 특성으로 데코레이트된 `IncrementAmount` 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-151">Add a `IncrementAmount` property decorated with the `[Parameter]` attribute.</span></span>
   * <span data-ttu-id="f9253-152">`currentCount` 값을 늘릴 때 `IncrementAmount`를 사용하도록 `IncrementCount` 메서드를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-152">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="f9253-153">*Components/Pages/Counter.razor*(Blazor의 *Pages/Counter.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="f9253-153">*Components/Pages/Counter.razor* (*Pages/Counter.cshtml* in Blazor):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples/3.x/RazorComponents/Components/Pages/Counter.razor?highlight=12,16)]

<!-- Add back when supported.
   > [!NOTE]
   > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
-->

1. <span data-ttu-id="f9253-154">특성을 사용하여 Home 구성 요소의 `<Counter>` 요소에 `IncrementAmount` 매개 변수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-154">Specify an `IncrementAmount` parameter in the Home component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="f9253-155">카운터를 10씩 증분하도록 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-155">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="f9253-156">*Components/Pages/Index.razor*(Blazor의 *Pages/Index.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="f9253-156">*Components/Pages/Index.razor* (*Pages/Index.cshtml* in Blazor):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples/3.x/RazorComponents/Components/Pages/Index.razor?highlight=7)]

1. <span data-ttu-id="f9253-157">홈페이지를 다시 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-157">Reload the Home page.</span></span> <span data-ttu-id="f9253-158">**여기를 클릭** 단추가 선택될 때마다 해당 카운터가 10씩 증분됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-158">The counter increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="f9253-159">카운터 페이지의 카운터는 1씩 증분됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-159">The counter on the Counter page increments by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="f9253-160">구성 요소 경로</span><span class="sxs-lookup"><span data-stu-id="f9253-160">Route to components</span></span>

<span data-ttu-id="f9253-161">*Counter.razor* 파일 맨 위의 `@page` 지시문은 이 구성 요소가 라우팅 엔드포인트임을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-161">The `@page` directive at the top of the *Counter.razor* file specifies that this component is a routing endpoint.</span></span> <span data-ttu-id="f9253-162">Counter 구성 요소는 `/Counter`에 전송된 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-162">The Counter component handles requests sent to `/Counter`.</span></span> <span data-ttu-id="f9253-163">`@page` 지시문이 없으면 구성 요소는 라우트된 요청을 처리하지 않지만 구성 요소는 다른 구성 요소에서 계속 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-163">Without the `@page` directive, the component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="f9253-164">종속성 주입</span><span class="sxs-lookup"><span data-stu-id="f9253-164">Dependency injection</span></span>

<span data-ttu-id="f9253-165">앱의 서비스 컨테이너에 등록된 서비스는 [DI(종속성 주입)](xref:fundamentals/dependency-injection)를 통해 구성 요소에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-165">Services registered in the app's service container are available to components via [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="f9253-166">`@inject` 지시문을 사용하여 서비스를 구성 요소에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-166">Inject services into a component using the `@inject` directive.</span></span>

<span data-ttu-id="f9253-167">샘플 앱에서 FetchData 구성 요소의 지시문을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-167">Examine the directives of the FetchData component in the sample app.</span></span>

<span data-ttu-id="f9253-168">Razor 구성 요소 샘플 앱에서 `WeatherForecastService` 서비스는 [싱글톤](xref:fundamentals/dependency-injection#service-lifetimes)으로 등록되므로, 서비스의 한 인스턴스를 앱 전체에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-168">In the Razor Components sample app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes), so one instance of the service is available throughout the app.</span></span> <span data-ttu-id="f9253-169">`@inject` 지시문은 `WeatherForecastService` 서비스의 인스턴스를 구성 요소에 삽입하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-169">The `@inject` directive is used to inject the instance of the `WeatherForecastService` service into the component.</span></span>

<span data-ttu-id="f9253-170">*Components/Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="f9253-170">*Components/Pages/FetchData.razor*:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="f9253-171">FetchData 구성 요소는 삽입된 서비스를 `ForecastService`로 사용하여 `WeatherForecast` 개체의 배열을 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-171">The FetchData component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

<span data-ttu-id="f9253-172">Blazor 버전의 샘플 앱에서 `HttpClient`를 삽입하여 *wwwroot/sample-data* 폴더의 *weather.json* 파일에서 일기 예보 데이터를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-172">In the Blazor version of the sample app, `HttpClient` is injected to obtain weather forecast data from the *weather.json* file in the *wwwroot/sample-data* folder:</span></span>

<span data-ttu-id="f9253-173">*Pages/FetchData.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="f9253-173">*Pages/FetchData.cshtml*:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.cshtml?highlight=7)]

<span data-ttu-id="f9253-174">두 샘플 앱에서 [@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) 루프를 사용하여 각 예보 인스턴스를 날씨 데이터 테이블의 행으로 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-174">In both sample apps, a [@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a><span data-ttu-id="f9253-175">할 일 목록 빌드</span><span class="sxs-lookup"><span data-stu-id="f9253-175">Build a todo list</span></span>

<span data-ttu-id="f9253-176">간단한 할 일 목록을 구현하는 앱에 새 구성 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-176">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="f9253-177">샘플 앱에 빈 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-177">Add an empty file to the sample app:</span></span>

   * <span data-ttu-id="f9253-178">Razor 구성 요소 환경의 경우 *Components/Pages* 폴더에 *Todo.razor* 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-178">For the Razor Components experience, add a *Todo.razor* file to the *Components/Pages* folder.</span></span>
   * <span data-ttu-id="f9253-179">Blazor 환경의 경우 *Pages* 폴더에 *Todo.cshtml* 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-179">For the Blazor experience, add a *Todo.cshtml* file to the *Pages* folder.</span></span>

1. <span data-ttu-id="f9253-180">구성 요소의 초기 태그를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-180">Provide the initial markup for the component:</span></span>

   ```cshtml
   @page "/todo"

   <h1>Todo</h1>
   ```

1. <span data-ttu-id="f9253-181">Todo 구성 요소를 탐색 모음에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-181">Add the Todo component to the navigation bar.</span></span>

   <span data-ttu-id="f9253-182">NavMenu 구성 요소(*Components/Shared/NavMenu.razor* 또는 Blazor의 *Shared/NavMenu.cshtml*)는 앱의 레이아웃에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-182">The NavMenu component (*Components/Shared/NavMenu.razor* or *Shared/NavMenu.cshtml* in Blazor) is used in the app's layout.</span></span> <span data-ttu-id="f9253-183">레이아웃은 앱의 콘텐츠 중복을 방지할 수 있는 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-183">Layouts are components that allow you to avoid duplication of content in the app.</span></span> <span data-ttu-id="f9253-184">자세한 내용은 <xref:blazor/layouts>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f9253-184">For more information, see <xref:blazor/layouts>.</span></span>

   <span data-ttu-id="f9253-185">*Components/Shared/NavMenu.razor*(Blazor의 *Shared/NavMenu.cshtml*) 파일의 기존 목록 항목 아래에 다음 목록 항목 태그를 추가하여 Todo 구성 요소의 `<NavLink>`를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-185">Add a `<NavLink>` for the Todo component by adding the following list item markup below the existing list items in the *Components/Shared/NavMenu.razor* (*Shared/NavMenu.cshtml* in Blazor) file:</span></span>

   ```cshtml
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="f9253-186">앱을 다시 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-186">Rebuild and run the app.</span></span> <span data-ttu-id="f9253-187">새 Todo 페이지를 방문하여 Todo 구성 요소에 대한 링크가 작동하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-187">Visit the new Todo page to confirm that the link to the Todo component works.</span></span>

1. <span data-ttu-id="f9253-188">프로젝트 루트에 *TodoItem.cs* 파일을 추가하여 Todo 항목을 나타내는 클래스를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-188">Add a *TodoItem.cs* file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="f9253-189">`TodoItem` 클래스에 대해 다음 C# 코드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-189">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples/3.x/RazorComponents/TodoItem.cs)]

1. <span data-ttu-id="f9253-190">Todo 구성 요소(*Components/Pages/Todo.razor* 또는 Blazor의 *Pages/Todo.cshtml*)로 돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-190">Return to the Todo component (*Components/Pages/Todo.razor* or *Pages/Todo.cshtml* in Blazor):</span></span>

   * <span data-ttu-id="f9253-191">`@functions` 블록에 있는 할 일에 대한 필드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-191">Add a field for the todos in an `@functions` block.</span></span> <span data-ttu-id="f9253-192">Todo 구성 요소는 이 필드를 사용하여 할 일 목록의 상태를 유지 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-192">The Todo component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="f9253-193">순서가 지정되지 않은 목록 태그 및 `foreach` 루프를 추가하여 각 할 일 항목을 목록 항목으로 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-193">Add unordered list markup and a `foreach` loop to render each todo item as a list item.</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="f9253-194">할 일을 목록에 추가하려면 앱에 UI 요소가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-194">The app requires UI elements for adding todos to the list.</span></span> <span data-ttu-id="f9253-195">목록 아래에 텍스트 입력과 단추를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-195">Add a text input and a button below the list:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="f9253-196">앱을 다시 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-196">Rebuild and run the app.</span></span> <span data-ttu-id="f9253-197">단추에 이벤트 처리기가 연결되어 있지 않으므로 **할 일 추가** 단추를 선택하면 아무것도 발생하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-197">When the **Add todo** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="f9253-198">`AddTodo` 메서드를 Todo 구성 요소에 추가하고 `onclick` 특성을 사용하여 단추 클릭에 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-198">Add an `AddTodo` method to the Todo component and register it for button clicks using the `onclick` attribute:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

   <span data-ttu-id="f9253-199">단추가 선택되면 `AddTodo` C# 메서드가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-199">The `AddTodo` C# method is called when the button is selected.</span></span>

1. <span data-ttu-id="f9253-200">새 할 일 항목의 제목을 가져오려면 `newTodo` 문자열 필드를 추가하고 `bind` 특성을 사용하여 텍스트 입력 값에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-200">To get the title of the new todo item, add a `newTodo` string field and bind it to the value of the text input using the `bind` attribute:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```cshtml
   <input placeholder="Something todo" bind="@newTodo">
   ```

1. <span data-ttu-id="f9253-201">`AddTodo` 메서드를 업데이트하여 지정된 제목이 있는 `TodoItem`을 목록에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-201">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="f9253-202">`newTodo`를 빈 문자열로 설정하여 텍스트 입력 값을 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-202">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="f9253-203">앱을 다시 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-203">Rebuild and run the app.</span></span> <span data-ttu-id="f9253-204">할 일 목록에 일부 할 일을 추가하여 새 코드를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-204">Add some todos to the todo list to test the new code.</span></span>

1. <span data-ttu-id="f9253-205">각 할 일 항목의 제목 텍스트를 편집 가능하게 설정하고 확인란을 통해 사용자가 완료된 항목을 추적하도록 도울 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-205">The title text for each todo item can be made editable and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="f9253-206">각 할 일 항목의 확인란 입력을 추가하고 해당 값을 `IsDone` 속성에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-206">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="f9253-207">`@todo.Title`을 `@todo.Title`에 바인딩된 `<input>` 요소로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-207">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="f9253-208">해당 값이 바인딩되었는지 확인하려면 `<h1>` 헤더를 업데이트하여 완료되지 않은(`IsDone`이 `false`임) 할 일 항목 수를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-208">To verify that these values are bound, update the `<h1>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```cshtml
   <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
   ```

1. <span data-ttu-id="f9253-209">완료된 Todo 구성 요소(*Components/Pages/Todo.razor* 또는 Blazor의 *Pages/Todo.cshtml*)는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-209">The completed Todo component (*Components/Pages/Todo.razor* or *Pages/Todo.cshtml* in Blazor):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples/3.x/RazorComponents/Components/Pages/Todo.razor)]

1. <span data-ttu-id="f9253-210">앱을 다시 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-210">Rebuild and run the app.</span></span> <span data-ttu-id="f9253-211">할 일 항목을 추가하여 새 코드를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="f9253-211">Add todo items to test the new code.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="f9253-212">앱 게시 및 배포</span><span class="sxs-lookup"><span data-stu-id="f9253-212">Publish and deploy the app</span></span>

<span data-ttu-id="f9253-213">앱을 게시하려면 <xref:host-and-deploy/blazor/index>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f9253-213">To publish the app, see <xref:host-and-deploy/blazor/index>.</span></span>
