---
title: 첫 번째 Blazor 앱 빌드
author: guardrex
description: Blazor 앱을 단계별로 빌드합니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/14/2019
uid: tutorials/first-blazor-app
ms.openlocfilehash: c1b142ebdbd85eb10ddf8c8b70edd9782732a4f1
ms.sourcegitcommit: 3ee6ee0051c3d2c8d47a58cb17eef1a84a4c46a0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65621095"
---
# <a name="build-your-first-blazor-app"></a><span data-ttu-id="45e43-103">첫 번째 Blazor 앱 빌드</span><span class="sxs-lookup"><span data-stu-id="45e43-103">Build your first Blazor app</span></span>

<span data-ttu-id="45e43-104">작성자: [Daniel Roth](https://github.com/danroth27) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="45e43-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="45e43-105">이 자습서에서는 Blazor 앱을 빌드 및 수정하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-105">This tutorial shows you how to build and modify a Blazor app.</span></span>

<span data-ttu-id="45e43-106"><xref:blazor/get-started> 문서의 지침에 따라 이 자습서에 사용할 Blazor 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-106">Follow the guidance in the <xref:blazor/get-started> article to create a Blazor project for this tutorial.</span></span>

## <a name="build-components"></a><span data-ttu-id="45e43-107">구성 요소 빌드</span><span class="sxs-lookup"><span data-stu-id="45e43-107">Build components</span></span>

1. <span data-ttu-id="45e43-108">*Pages* 폴더에 있는 앱의 3개 페이지로 각각 이동합니다. 홈, 카운터 및 Fetch 데이터.</span><span class="sxs-lookup"><span data-stu-id="45e43-108">Browse to each of the app's three pages in the *Pages* folder: Home, Counter, and Fetch data.</span></span> <span data-ttu-id="45e43-109">이 페이지는 Razor 구성 요소 파일 *Index.razor*, *Counter.razor* 및 *FetchData.razor*로 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-109">These pages are implemented by the Razor component files *Index.razor*, *Counter.razor*, and *FetchData.razor*.</span></span>

1. <span data-ttu-id="45e43-110">카운터 페이지에서 **Click me** 단추를 선택하여 페이지 새로 고침 없이 카운터를 증분합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-110">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="45e43-111">웹 페이지의 카운터를 증분하려면 일반적으로 JavaScript를 작성해야 하지만, Blazor는 C#를 사용하여 더 나은 접근 방식을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-111">Incrementing a counter in a webpage normally requires writing JavaScript, but Blazor provides a better approach using C#.</span></span>

1. <span data-ttu-id="45e43-112">*Counter.razor* 파일에서 Counter 구성 요소의 구현을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-112">Examine the implementation of the Counter component in the *Counter.razor* file.</span></span>

   <span data-ttu-id="45e43-113">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="45e43-113">*Pages/Counter.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="45e43-114">Counter 구성 요소의 UI는 HTML을 사용하여 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-114">The UI of the Counter component is defined using HTML.</span></span> <span data-ttu-id="45e43-115">동적 렌더링 논리(예: 루프, 조건, 식)는 [Razor](xref:mvc/views/razor)라는 포함된 C# 구문을 사용하여 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-115">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="45e43-116">HTML 태그 및 C# 렌더링 논리는 빌드 시 구성 요소 클래스로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-116">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="45e43-117">생성된 .NET 클래스의 이름은 파일 이름과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-117">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="45e43-118">구성 요소 클래스의 멤버는 `@functions` 블록에서 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-118">Members of the component class are defined in an `@functions` block.</span></span> <span data-ttu-id="45e43-119">`@functions` 블록에서 구성 요소 상태(속성, 필드) 및 메서드는 이벤트 처리 또는 기타 구성 요소를 정의하기 위해 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-119">In the `@functions` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="45e43-120">그런 다음, 이러한 멤버를 구성 요소 렌더링 논리의 일부 및 이벤트 처리에 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-120">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="45e43-121">**Click me** 단추를 선택한 경우:</span><span class="sxs-lookup"><span data-stu-id="45e43-121">When the **Click me** button is selected:</span></span>

   * <span data-ttu-id="45e43-122">Counter 구성 요소의 등록된 `onclick` 처리기가 호출됩니다(`IncrementCount` 메서드).</span><span class="sxs-lookup"><span data-stu-id="45e43-122">The Counter component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="45e43-123">Counter 구성 요소가 렌더링 트리를 다시 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-123">The Counter component regenerates its render tree.</span></span>
   * <span data-ttu-id="45e43-124">새 렌더링 트리가 이전 렌더링 트리에 비교됩니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-124">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="45e43-125">DOM(문서 개체 모델)에 대한 수정 내용만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-125">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="45e43-126">표시된 개수가 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-126">The displayed count is updated.</span></span>

1. <span data-ttu-id="45e43-127">또한 Counter 구성 요소의 C# 논리를 수정하여 카운트를 1이 아닌 2씩 증분하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-127">Modify the C# logic of the Counter component to make the count increment by two instead of one.</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="45e43-128">앱을 다시 빌드하고 실행하여 변경 내용을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-128">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="45e43-129">**Click me** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-129">Select the **Click me** button.</span></span> <span data-ttu-id="45e43-130">카운터가 2씩 증분됩니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-130">The counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="45e43-131">구성 요소 사용</span><span class="sxs-lookup"><span data-stu-id="45e43-131">Use components</span></span>

<span data-ttu-id="45e43-132">HTML 구문을 사용하여 구성 요소를 다른 구성 요소에 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-132">Include a component in another component using an HTML syntax.</span></span>

1. <span data-ttu-id="45e43-133">Index 구성 요소(*Index.razor*)에 `<Counter />` 요소를 추가하여 앱의 Index 구성 요소에 Counter 구성 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-133">Add the Counter component to the app's Index component by adding a `<Counter />` element to the Index component (*Index.razor*).</span></span>

   <span data-ttu-id="45e43-134">이 환경에 Blazor 클라이언트 쪽을 사용하는 경우에는 Survey Prompt 구성 요소(`<SurveyPrompt>` 요소)가 Index 구성 요소에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-134">If you're using Blazor client-side for this experience, a Survey Prompt component (`<SurveyPrompt>` element) is in the Index component.</span></span> <span data-ttu-id="45e43-135">`<SurveyPrompt>` 요소를 `<Counter>` 요소로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-135">Replace the `<SurveyPrompt>` element with the `<Counter>` element.</span></span> <span data-ttu-id="45e43-136">이 환경에 Blazor 서버 쪽 앱을 사용하는 경우에는 Index 구성 요소에 `<Counter>` 구성 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-136">If you're using a Blazor server-side app for this experience, add the `<Counter>` element to the Index component:</span></span>

   <span data-ttu-id="45e43-137">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="45e43-137">*Pages/Index.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. <span data-ttu-id="45e43-138">앱을 다시 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-138">Rebuild and run the app.</span></span> <span data-ttu-id="45e43-139">Index 구성 요소에는 자체 카운터가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-139">The Index component has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="45e43-140">구성 요소 매개 변수</span><span class="sxs-lookup"><span data-stu-id="45e43-140">Component parameters</span></span>

<span data-ttu-id="45e43-141">구성 요소에는 매개 변수도 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-141">Components can also have parameters.</span></span> <span data-ttu-id="45e43-142">구성 요소 매개 변수는 `[Parameter]`로 데코레이트된 component 클래스에서 public이 아닌 속성을 사용하여 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-142">Component parameters are defined using non-public properties on the component class decorated with `[Parameter]`.</span></span> <span data-ttu-id="45e43-143">특성을 사용하여 태그에서 구성 요소의 인수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-143">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="45e43-144">구성 요소의 `@functions` C# 코드를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-144">Update the component's `@functions` C# code:</span></span>

   * <span data-ttu-id="45e43-145">`[Parameter]` 특성으로 데코레이트된 `IncrementAmount` 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-145">Add a `IncrementAmount` property decorated with the `[Parameter]` attribute.</span></span>
   * <span data-ttu-id="45e43-146">`currentCount` 값을 늘릴 때 `IncrementAmount`를 사용하도록 `IncrementCount` 메서드를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-146">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="45e43-147">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="45e43-147">*Pages/Counter.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

<!-- Add back when supported.
   > [!NOTE]
   > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
-->

1. <span data-ttu-id="45e43-148">특성을 사용하여 Index 구성 요소의 `<Counter>` 요소에 `IncrementAmount` 매개 변수를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-148">Specify an `IncrementAmount` parameter in the Index component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="45e43-149">카운터를 10씩 증분하도록 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-149">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="45e43-150">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="45e43-150">*Pages/Index.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. <span data-ttu-id="45e43-151">Index 구성 요소를 다시 로드합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-151">Reload the Index component.</span></span> <span data-ttu-id="45e43-152">**여기를 클릭** 단추가 선택될 때마다 해당 카운터가 10씩 증분됩니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-152">The counter increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="45e43-153">Counter 구성 요소의 카운터는 계속 1씩 증분됩니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-153">The counter in the Counter component continues to increment by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="45e43-154">구성 요소 경로</span><span class="sxs-lookup"><span data-stu-id="45e43-154">Route to components</span></span>

<span data-ttu-id="45e43-155">*Counter.razor* 파일 맨 위의 `@page` 지시문은 Counter 구성 요소가 라우팅 엔드포인트임을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-155">The `@page` directive at the top of the *Counter.razor* file specifies that the Counter component is a routing endpoint.</span></span> <span data-ttu-id="45e43-156">Counter 구성 요소는 `/counter`에 전송된 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-156">The Counter component handles requests sent to `/counter`.</span></span> <span data-ttu-id="45e43-157">`@page` 지시문이 없으면 구성 요소는 라우팅된 요청을 처리하지 않지만 구성 요소는 다른 구성 요소에서 계속 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-157">Without the `@page` directive, a component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="45e43-158">종속성 주입</span><span class="sxs-lookup"><span data-stu-id="45e43-158">Dependency injection</span></span>

<span data-ttu-id="45e43-159">앱의 서비스 컨테이너에 등록된 서비스는 [DI(종속성 주입)](xref:fundamentals/dependency-injection)를 통해 구성 요소에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-159">Services registered in the app's service container are available to components via [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="45e43-160">`@inject` 지시문을 사용하여 서비스를 구성 요소에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-160">Inject services into a component using the `@inject` directive.</span></span>

<span data-ttu-id="45e43-161">FetchData 구성 요소의 지시문을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-161">Examine the directives of the FetchData component.</span></span>

<span data-ttu-id="45e43-162">Blazor 서버 쪽 앱을 사용하는 경우 `WeatherForecastService` 서비스는 [싱글톤](xref:fundamentals/dependency-injection#service-lifetimes)으로 등록되므로, 서비스의 한 인스턴스를 앱 전체에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-162">If working with a Blazor server-side app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes), so one instance of the service is available throughout the app.</span></span> <span data-ttu-id="45e43-163">`@inject` 지시문은 `WeatherForecastService` 서비스의 인스턴스를 구성 요소에 삽입하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-163">The `@inject` directive is used to inject the instance of the `WeatherForecastService` service into the component.</span></span>

<span data-ttu-id="45e43-164">*Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="45e43-164">*Pages/FetchData.razor*:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="45e43-165">FetchData 구성 요소는 삽입된 서비스를 `ForecastService`로 사용하여 `WeatherForecast` 개체의 배열을 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-165">The FetchData component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

<span data-ttu-id="45e43-166">Blazor 클라이언트 쪽 앱을 사용하는 경우 `HttpClient`를 삽입하여 *wwwroot/sample-data* 폴더의 *weather.json* 파일에서 일기 예보 데이터를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-166">If working with a Blazor client-side app, `HttpClient` is injected to obtain weather forecast data from the *weather.json* file in the *wwwroot/sample-data* folder:</span></span>

<span data-ttu-id="45e43-167">*Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="45e43-167">*Pages/FetchData.razor*:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-8)]

<span data-ttu-id="45e43-168">[\@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) 루프는 각 예측 인스턴스를 날씨 데이터 테이블의 행으로 렌더링하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-168">A [\@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]


## <a name="build-a-todo-list"></a><span data-ttu-id="45e43-169">할 일 목록 빌드</span><span class="sxs-lookup"><span data-stu-id="45e43-169">Build a todo list</span></span>

<span data-ttu-id="45e43-170">간단한 할 일 목록을 구현하는 앱에 새 구성 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-170">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="45e43-171">*Todo.razor*라는 빈 파일을 *Pages* 폴더의 앱에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-171">Add an empty file named *Todo.razor* to the app in the *Pages* folder:</span></span>

1. <span data-ttu-id="45e43-172">구성 요소의 초기 태그를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-172">Provide the initial markup for the component:</span></span>

   ```cshtml
   @page "/todo"

   <h1>Todo</h1>
   ```

1. <span data-ttu-id="45e43-173">Todo 구성 요소를 탐색 모음에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-173">Add the Todo component to the navigation bar.</span></span>

   <span data-ttu-id="45e43-174">NavMenu 구성 요소(*Shared/NavMenu.razor*)는 앱의 레이아웃에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-174">The NavMenu component (*Shared/NavMenu.razor*) is used in the app's layout.</span></span> <span data-ttu-id="45e43-175">레이아웃은 앱의 콘텐츠 중복을 방지할 수 있는 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-175">Layouts are components that allow you to avoid duplication of content in the app.</span></span> <span data-ttu-id="45e43-176">자세한 내용은 <xref:blazor/layouts>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="45e43-176">For more information, see <xref:blazor/layouts>.</span></span>

   <span data-ttu-id="45e43-177">*Shared/NavMenu.razor* 파일의 기존 목록 항목 아래에 다음 목록 항목 태그를 추가하여 Todo 구성 요소의 `<NavLink>`를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-177">Add a `<NavLink>` for the Todo component by adding the following list item markup below the existing list items in the *Shared/NavMenu.razor* file:</span></span>

   ```cshtml
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="45e43-178">앱을 다시 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-178">Rebuild and run the app.</span></span> <span data-ttu-id="45e43-179">새 Todo 페이지를 방문하여 Todo 구성 요소에 대한 링크가 작동하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-179">Visit the new Todo page to confirm that the link to the Todo component works.</span></span>

1. <span data-ttu-id="45e43-180">Blazor 서버 쪽 앱을 빌드하는 경우 앱의 네임스페이스를 *\_Imports.razor* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-180">If building a Blazor server-side app, add the app's namespace to the *\_Imports.razor* file.</span></span> <span data-ttu-id="45e43-181">다음 `@using` 문에서는 앱의 네임스페이스가 `WebApplication`인 것으로 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-181">The following `@using` statement assumes that the app's namespace is `WebApplication`:</span></span>

   ```cshtml
   @using WebApplication
   ```
   
   <span data-ttu-id="45e43-182">Blazor 클라이언트 쪽 앱은 기본적으로 앱의 네임스페이스를 *\_Imports.razor* 파일에 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-182">Blazor client-side apps include the app's namespace by default in the *\_Imports.razor* file.</span></span>

1. <span data-ttu-id="45e43-183">프로젝트 루트에 *TodoItem.cs* 파일을 추가하여 Todo 항목을 나타내는 클래스를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-183">Add a *TodoItem.cs* file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="45e43-184">`TodoItem` 클래스에 대해 다음 C# 코드를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-184">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="45e43-185">Todo 구성 요소(*Pages/Todo.razor*)로 돌아갑니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-185">Return to the Todo component (*Pages/Todo.razor*):</span></span>

   * <span data-ttu-id="45e43-186">`@functions` 블록에 있는 할 일 항목에 대한 필드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-186">Add a field for the todo items in an `@functions` block.</span></span> <span data-ttu-id="45e43-187">Todo 구성 요소는 이 필드를 사용하여 할 일 목록의 상태를 유지 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-187">The Todo component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="45e43-188">순서가 지정되지 않은 목록 태그 및 `foreach` 루프를 추가하여 각 할 일 항목을 목록 항목으로 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-188">Add unordered list markup and a `foreach` loop to render each todo item as a list item.</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="45e43-189">할 일 항목을 목록에 추가하려면 앱에 UI 요소가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-189">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="45e43-190">목록 아래에 텍스트 입력과 단추를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-190">Add a text input and a button below the list:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="45e43-191">앱을 다시 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-191">Rebuild and run the app.</span></span> <span data-ttu-id="45e43-192">단추에 이벤트 처리기가 연결되어 있지 않으므로 **할 일 추가** 단추를 선택하면 아무것도 발생하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-192">When the **Add todo** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="45e43-193">`AddTodo` 메서드를 Todo 구성 요소에 추가하고 `onclick` 특성을 사용하여 단추 클릭에 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-193">Add an `AddTodo` method to the Todo component and register it for button clicks using the `onclick` attribute:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

   <span data-ttu-id="45e43-194">단추가 선택되면 `AddTodo` C# 메서드가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-194">The `AddTodo` C# method is called when the button is selected.</span></span>

1. <span data-ttu-id="45e43-195">새 할 일 항목의 제목을 가져오려면 `newTodo` 문자열 필드를 추가하고 `bind` 특성을 사용하여 텍스트 입력 값에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-195">To get the title of the new todo item, add a `newTodo` string field and bind it to the value of the text input using the `bind` attribute:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```cshtml
   <input placeholder="Something todo" bind="@newTodo">
   ```

1. <span data-ttu-id="45e43-196">`AddTodo` 메서드를 업데이트하여 지정된 제목이 있는 `TodoItem`을 목록에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-196">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="45e43-197">`newTodo`를 빈 문자열로 설정하여 텍스트 입력 값을 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-197">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="45e43-198">앱을 다시 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-198">Rebuild and run the app.</span></span> <span data-ttu-id="45e43-199">할 일 목록에 일부 할 일 항목을 추가하여 새 코드를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-199">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="45e43-200">각 할 일 항목의 제목 텍스트를 편집 가능하게 설정하고 확인란을 통해 사용자가 완료된 항목을 추적하도록 도울 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-200">The title text for each todo item can be made editable and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="45e43-201">각 할 일 항목의 확인란 입력을 추가하고 해당 값을 `IsDone` 속성에 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-201">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="45e43-202">`@todo.Title`을 `@todo.Title`에 바인딩된 `<input>` 요소로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-202">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="45e43-203">해당 값이 바인딩되었는지 확인하려면 `<h1>` 헤더를 업데이트하여 완료되지 않은(`IsDone`이 `false`임) 할 일 항목 수를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-203">To verify that these values are bound, update the `<h1>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```cshtml
   <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
   ```

1. <span data-ttu-id="45e43-204">완료된 Todo 구성 요소(*Pages/Todo.razor*):</span><span class="sxs-lookup"><span data-stu-id="45e43-204">The completed Todo component (*Pages/Todo.razor*):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="45e43-205">앱을 다시 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-205">Rebuild and run the app.</span></span> <span data-ttu-id="45e43-206">할 일 항목을 추가하여 새 코드를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="45e43-206">Add todo items to test the new code.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="45e43-207">앱 게시 및 배포</span><span class="sxs-lookup"><span data-stu-id="45e43-207">Publish and deploy the app</span></span>

<span data-ttu-id="45e43-208">앱을 게시하려면 <xref:host-and-deploy/blazor/index>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="45e43-208">To publish the app, see <xref:host-and-deploy/blazor/index>.</span></span>
