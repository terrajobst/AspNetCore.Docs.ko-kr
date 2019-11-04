---
title: ASP.NET Core의 보기 구성 요소
author: rick-anderson
description: ASP.NET Core에서 보기 구성 요소가 사용되는 방법 및 이를 앱에 추가하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 05/14/2019
uid: mvc/views/view-components
ms.openlocfilehash: e6990368519857a27b291d7d565c09072f23f1b0
ms.sourcegitcommit: 7001657c00358b082734ba4273693b9b3ed35d2a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68670089"
---
# <a name="view-components-in-aspnet-core"></a><span data-ttu-id="db194-103">ASP.NET Core의 보기 구성 요소</span><span class="sxs-lookup"><span data-stu-id="db194-103">View components in ASP.NET Core</span></span>

<span data-ttu-id="db194-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="db194-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="db194-105">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="db194-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="view-components"></a><span data-ttu-id="db194-106">보기 구성 요소</span><span class="sxs-lookup"><span data-stu-id="db194-106">View components</span></span>

<span data-ttu-id="db194-107">보기 구성 요소는 부분 보기와 유사하지만 훨씬 강력합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-107">View components are similar to partial views, but they're much more powerful.</span></span> <span data-ttu-id="db194-108">보기 구성 요소는 모델 바인딩을 사용하지 않으며 호출 시 제공하는 데이터에만 의존합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-108">View components don't use model binding, and only depend on the data provided when calling into it.</span></span> <span data-ttu-id="db194-109">이 문서는 컨트롤러 및 보기를 사용하여 작성되었지만, 보기 구성 요소는 Razor Pages와 함께 사용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db194-109">This article was written using controllers and views, but view components also work with Razor Pages.</span></span>

<span data-ttu-id="db194-110">보기 구성 요소는 다음과 같은 특징을 갖고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db194-110">A view component:</span></span>

* <span data-ttu-id="db194-111">전체 응답보다는 청크를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-111">Renders a chunk rather than a whole response.</span></span>
* <span data-ttu-id="db194-112">컨트롤러 및 보기 간에서 발견할 수 있는 것과 동일한 문제의 분리 및 테스트 가능성의 이점을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-112">Includes the same separation-of-concerns and testability benefits found between a controller and view.</span></span>
* <span data-ttu-id="db194-113">매개 변수 및 비즈니스 논리를 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db194-113">Can have parameters and business logic.</span></span>
* <span data-ttu-id="db194-114">일반적으로 레이아웃 페이지에서 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="db194-114">Is typically invoked from a layout page.</span></span>

<span data-ttu-id="db194-115">보기 구성 요소는 다음과 같이 부분 보기로는 너무 복잡한 재사용 가능한 렌더링 논리를 갖는 모든 곳에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db194-115">View components are intended anywhere you have reusable rendering logic that's too complex for a partial view, such as:</span></span>

* <span data-ttu-id="db194-116">동적 탐색 메뉴</span><span class="sxs-lookup"><span data-stu-id="db194-116">Dynamic navigation menus</span></span>
* <span data-ttu-id="db194-117">태그 클라우드(여기서는 데이터베이스를 쿼리합니다.)</span><span class="sxs-lookup"><span data-stu-id="db194-117">Tag cloud (where it queries the database)</span></span>
* <span data-ttu-id="db194-118">로그인 패널</span><span class="sxs-lookup"><span data-stu-id="db194-118">Login panel</span></span>
* <span data-ttu-id="db194-119">쇼핑 카트</span><span class="sxs-lookup"><span data-stu-id="db194-119">Shopping cart</span></span>
* <span data-ttu-id="db194-120">최근에 게시된 문서</span><span class="sxs-lookup"><span data-stu-id="db194-120">Recently published articles</span></span>
* <span data-ttu-id="db194-121">일반적인 블로그의 사이드바 콘텐츠</span><span class="sxs-lookup"><span data-stu-id="db194-121">Sidebar content on a typical blog</span></span>
* <span data-ttu-id="db194-122">모든 페이지에 렌더링되고 사용자의 로그인 상태에 따라 로그아웃 또는 로그인 링크를 표시하는 로그인 패널</span><span class="sxs-lookup"><span data-stu-id="db194-122">A login panel that would be rendered on every page and show either the links to log out or log in, depending on the log in state of the user</span></span>

<span data-ttu-id="db194-123">보기 구성 요소는 클래스(일반적으로 [ViewComponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent)에서 파생됨)와 보기 구성 요소가 반환하는 결과(일반적으로 보기)의 두 부분으로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="db194-123">A view component consists of two parts: the class (typically derived from [ViewComponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent)) and the result it returns (typically a view).</span></span> <span data-ttu-id="db194-124">컨트롤러와 마찬가지로 보기 구성 요소는 POCO일 수 있지만 대부분의 개발자는 `ViewComponent`에서 파생하여 사용 가능한 메서드와 속성을 활용하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-124">Like controllers, a view component can be a POCO, but most developers will want to take advantage of the methods and properties available by deriving from `ViewComponent`.</span></span>

<span data-ttu-id="db194-125">보기 구성 요소가 앱의 사양을 충족하는지 고려할 때 Razor 구성 요소를 대신 사용하는 방안도 고려해보세요.</span><span class="sxs-lookup"><span data-stu-id="db194-125">When considering if view components meet an app's specifications, consider using Razor Components instead.</span></span> <span data-ttu-id="db194-126">Razor 구성 요소 역시 태그와 C# 코드를 결합하여 다시 사용할 수 있는 UI 단위를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-126">Razor Components also combine markup with C# code to produce reusable UI units.</span></span> <span data-ttu-id="db194-127">Razor 구성 요소는 클라이언트 쪽 UI 논리 및 컴퍼지션을 제공할 때 개발자 생산성을 위해 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="db194-127">Razor Components are designed for developer productivity when providing client-side UI logic and composition.</span></span> <span data-ttu-id="db194-128">자세한 내용은 <xref:blazor/components>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="db194-128">For more information, see <xref:blazor/components>.</span></span>

## <a name="creating-a-view-component"></a><span data-ttu-id="db194-129">보기 구성 요소 만들기</span><span class="sxs-lookup"><span data-stu-id="db194-129">Creating a view component</span></span>

<span data-ttu-id="db194-130">이 섹션에서는 보기 구성 요소를 만들기 위한 높은 수준의 요구 사항이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db194-130">This section contains the high-level requirements to create a view component.</span></span> <span data-ttu-id="db194-131">이 문서의 뒷부분에서는 각 단계를 자세히 검토하고 보기 구성 요소를 만들어봅니다.</span><span class="sxs-lookup"><span data-stu-id="db194-131">Later in the article, we'll examine each step in detail and create a view component.</span></span>

### <a name="the-view-component-class"></a><span data-ttu-id="db194-132">보기 구성 요소 클래스</span><span class="sxs-lookup"><span data-stu-id="db194-132">The view component class</span></span>

<span data-ttu-id="db194-133">다음 중 한 가지 방법을 사용하여 보기 구성 요소 클래스를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db194-133">A view component class can be created by any of the following:</span></span>

* <span data-ttu-id="db194-134">*ViewComponent*에서 파생</span><span class="sxs-lookup"><span data-stu-id="db194-134">Deriving from *ViewComponent*</span></span>
* <span data-ttu-id="db194-135">`[ViewComponent]` 특성을 사용하여 클래스를 데코레이팅하거나 `[ViewComponent]` 특성이 사용된 클래스에서 파생</span><span class="sxs-lookup"><span data-stu-id="db194-135">Decorating a class with the `[ViewComponent]` attribute, or deriving from a class with the `[ViewComponent]` attribute</span></span>
* <span data-ttu-id="db194-136">이름이 *ViewComponent* 접미사로 끝나는 클래스 만들기</span><span class="sxs-lookup"><span data-stu-id="db194-136">Creating a class where the name ends with the suffix *ViewComponent*</span></span>

<span data-ttu-id="db194-137">컨트롤러와 마찬가지로, 뷰 구성 요소는 공용이고 비중첩 및 비추상 클래스여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-137">Like controllers, view components must be public, non-nested, and non-abstract classes.</span></span> <span data-ttu-id="db194-138">뷰 구성 요소 이름은 "ViewComponent" 접미사가 제거된 클래스 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="db194-138">The view component name is the class name with the "ViewComponent" suffix removed.</span></span> <span data-ttu-id="db194-139">또한 `ViewComponentAttribute.Name` 속성을 사용하여 명시적으로 지정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db194-139">It can also be explicitly specified using the `ViewComponentAttribute.Name` property.</span></span>

<span data-ttu-id="db194-140">보기 구성 요소 클래스는 다음과 같은 특징을 갖고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db194-140">A view component class:</span></span>

* <span data-ttu-id="db194-141">생성자 [종속성 주입](../../fundamentals/dependency-injection.md)을 완벽하게 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-141">Fully supports constructor [dependency injection](../../fundamentals/dependency-injection.md)</span></span>

* <span data-ttu-id="db194-142">컨트롤러 수명 주기를 따르지 않습니다. 즉, 뷰 구성 요소에 [필터](../controllers/filters.md)를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="db194-142">Doesn't take part in the controller lifecycle, which means you can't use [filters](../controllers/filters.md) in a view component</span></span>

### <a name="view-component-methods"></a><span data-ttu-id="db194-143">보기 구성 요소 메서드</span><span class="sxs-lookup"><span data-stu-id="db194-143">View component methods</span></span>

<span data-ttu-id="db194-144">보기 구성 요소는 `Task<IViewComponentResult>`를 반환하는 `InvokeAsync` 메서드 또는 `IViewComponentResult`를 반환하는 동기 `Invoke` 메서드에서 해당 논리를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-144">A view component defines its logic in an `InvokeAsync` method that returns a `Task<IViewComponentResult>` or in a synchronous `Invoke` method that returns an `IViewComponentResult`.</span></span> <span data-ttu-id="db194-145">매개 변수는 모델 바인딩이 아니라 보기 구성 요소 호출에서 직접 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="db194-145">Parameters come directly from invocation of the view component, not from model binding.</span></span> <span data-ttu-id="db194-146">보기 구성 요소는 요청을 직접 처리하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="db194-146">A view component never directly handles a request.</span></span> <span data-ttu-id="db194-147">일반적으로 보기 구성 요소는 모델을 초기화하고 `View` 메서드를 호출하여 보기에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-147">Typically, a view component initializes a model and passes it to a view by calling the `View` method.</span></span> <span data-ttu-id="db194-148">요약해보면 보기 구성 요소 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="db194-148">In summary, view component methods:</span></span>

* <span data-ttu-id="db194-149">`Task<IViewComponentResult>`를 반환하는 `InvokeAsync` 메서드 또는 `IViewComponentResult`를 반환하는 동기 `Invoke` 메서드를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-149">Define an `InvokeAsync` method that returns a `Task<IViewComponentResult>` or a synchronous `Invoke` method that returns an `IViewComponentResult`.</span></span>
* <span data-ttu-id="db194-150">일반적으로 모델을 초기화하고 `ViewComponent` `View` 메서드를 호출하여 이를 보기에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-150">Typically initializes a model and passes it to a view by calling the `ViewComponent` `View` method.</span></span>
* <span data-ttu-id="db194-151">매개 변수는 HTTP가 아닌 호출 메서드에서 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="db194-151">Parameters come from the calling method, not HTTP.</span></span> <span data-ttu-id="db194-152">모델 바인딩이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="db194-152">There's no model binding.</span></span>
* <span data-ttu-id="db194-153">HTTP 엔드포인트로 직접 연결할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="db194-153">Are not reachable directly as an HTTP endpoint.</span></span> <span data-ttu-id="db194-154">(일반적으로 보기의) 코드에서 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="db194-154">They're invoked from your code (usually in a view).</span></span> <span data-ttu-id="db194-155">보기 구성 요소는 요청을 처리하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="db194-155">A view component never handles a request.</span></span>
* <span data-ttu-id="db194-156">현재 HTTP 요청의 세부 정보가 아닌 시그니처에 오버로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="db194-156">Are overloaded on the signature rather than any details from the current HTTP request.</span></span>

### <a name="view-search-path"></a><span data-ttu-id="db194-157">보기 검색 경로</span><span class="sxs-lookup"><span data-stu-id="db194-157">View search path</span></span>

<span data-ttu-id="db194-158">런타임은 다음 경로에서 보기를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-158">The runtime searches for the view in the following paths:</span></span>

* <span data-ttu-id="db194-159">/Views/{Controller Name}/Components/{View Component Name}/{View Name}</span><span class="sxs-lookup"><span data-stu-id="db194-159">/Views/{Controller Name}/Components/{View Component Name}/{View Name}</span></span>
* <span data-ttu-id="db194-160">/Views/Shared/Components/{View Component Name}/{View Name}</span><span class="sxs-lookup"><span data-stu-id="db194-160">/Views/Shared/Components/{View Component Name}/{View Name}</span></span>
* <span data-ttu-id="db194-161">/Pages/Shared/Components/{View Component Name}/{View Name}</span><span class="sxs-lookup"><span data-stu-id="db194-161">/Pages/Shared/Components/{View Component Name}/{View Name}</span></span>

<span data-ttu-id="db194-162">검색 경로는 컨트롤러 + 보기 및 Razor Pages를 사용하는 프로젝트에 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="db194-162">The search path applies to projects using controllers + views and Razor Pages.</span></span>

<span data-ttu-id="db194-163">보기 구성 요소에 대한 기본 보기 이름은 *Default*로 이는 일반적으로 보기 파일의 이름이 *Default.cshtml*로 지정됨을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-163">The default view name for a view component is *Default*, which means your view file will typically be named *Default.cshtml*.</span></span> <span data-ttu-id="db194-164">보기 구성 요소 결과를 만들거나 `View` 메서드를 호출할 때 다른 보기 이름을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db194-164">You can specify a different view name when creating the view component result or when calling the `View` method.</span></span>

<span data-ttu-id="db194-165">보기 파일 이름을 *Default.cshtml*로 지정하고 *Views/Shared/Components/{View Component Name}/{View Name}* 경로를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="db194-165">We recommend you name the view file *Default.cshtml* and use the *Views/Shared/Components/{View Component Name}/{View Name}* path.</span></span> <span data-ttu-id="db194-166">예제에 사용된 `PriorityList` 보기 구성 요소는 보기 구성 요소 보기로 *Views/Shared/Components/PriorityList/Default.cshtml*을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-166">The `PriorityList` view component used in this sample uses *Views/Shared/Components/PriorityList/Default.cshtml* for the view component view.</span></span>

## <a name="invoking-a-view-component"></a><span data-ttu-id="db194-167">보기 구성 요소 호출</span><span class="sxs-lookup"><span data-stu-id="db194-167">Invoking a view component</span></span>

<span data-ttu-id="db194-168">보기 구성 요소를 사용하려면 보기에서 다음을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-168">To use the view component, call the following inside a view:</span></span>

```cshtml
@await Component.InvokeAsync("Name of view component", {Anonymous Type Containing Parameters})
```

<span data-ttu-id="db194-169">매개 변수는 `InvokeAsync` 메서드에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="db194-169">The parameters will be passed to the `InvokeAsync` method.</span></span> <span data-ttu-id="db194-170">이 문서에서 개발하는 `PriorityList` 보기 구성 요소는 *Views/ToDo/Index.cshtml* 보기 파일에서 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="db194-170">The `PriorityList` view component developed in the article is invoked from the *Views/ToDo/Index.cshtml* view file.</span></span> <span data-ttu-id="db194-171">다음에서 `InvokeAsync` 메서드는 두 매개 변수를 사용하여 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="db194-171">In the following, the `InvokeAsync` method is called with two parameters:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

::: moniker range=">= aspnetcore-1.1"

## <a name="invoking-a-view-component-as-a-tag-helper"></a><span data-ttu-id="db194-172">태그 도우미로 보기 구성 요소 호출</span><span class="sxs-lookup"><span data-stu-id="db194-172">Invoking a view component as a Tag Helper</span></span>

<span data-ttu-id="db194-173">ASP.NET Core 1.1 이상에서는 [태그 도우미](xref:mvc/views/tag-helpers/intro)로 보기 구성 요소를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db194-173">For ASP.NET Core 1.1 and higher, you can invoke a view component as a [Tag Helper](xref:mvc/views/tag-helpers/intro):</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

<span data-ttu-id="db194-174">태그 도우미에 대한 파스칼식 클래스 및 메서드 매개 변수는 [케밥식](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101)으로 번역됩니다.</span><span class="sxs-lookup"><span data-stu-id="db194-174">Pascal-cased class and method parameters for Tag Helpers are translated into their [kebab case](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span></span> <span data-ttu-id="db194-175">보기 구성 요소를 호출하는 태그 도우미는 `<vc></vc>` 요소를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-175">The Tag Helper to invoke a view component uses the `<vc></vc>` element.</span></span> <span data-ttu-id="db194-176">보기 구성 요소는 다음과 같이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="db194-176">The view component is specified as follows:</span></span>

```cshtml
<vc:[view-component-name]
  parameter1="parameter1 value"
  parameter2="parameter2 value">
</vc:[view-component-name]>
```

<span data-ttu-id="db194-177">보기 구성 요소를 태그 도우미로 사용하려면 `@addTagHelper` 지시문을 사용하여 보기 구성 요소가 포함된 어셈블리를 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-177">To use a view component as a Tag Helper, register the assembly containing the view component using the `@addTagHelper` directive.</span></span> <span data-ttu-id="db194-178">보기 구성 요소가 `MyWebApp`이라는 어셈블리에 있는 경우 다음 지시문을 *_ViewImports.cshtml* 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-178">If your view component is in an assembly called `MyWebApp`, add the following directive to the *_ViewImports.cshtml* file:</span></span>

```cshtml
@addTagHelper *, MyWebApp
```

<span data-ttu-id="db194-179">보기 구성 요소를 참조하는 모든 파일에 보기 구성 요소를 태그 도우미로 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db194-179">You can register a view component as a Tag Helper to any file that references the view component.</span></span> <span data-ttu-id="db194-180">태그 도우미를 등록하는 방법에 대한 자세한 내용은 [태그 도우미 범위 관리](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="db194-180">See [Managing Tag Helper Scope](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope) for more information on how to register Tag Helpers.</span></span>

<span data-ttu-id="db194-181">이 자습서에 사용된 `InvokeAsync` 메서드:</span><span class="sxs-lookup"><span data-stu-id="db194-181">The `InvokeAsync` method used in this tutorial:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

<span data-ttu-id="db194-182">태그 도우미 태그:</span><span class="sxs-lookup"><span data-stu-id="db194-182">In Tag Helper markup:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

<span data-ttu-id="db194-183">위의 예제에서 `PriorityList` 보기 구성 요소는 `priority-list`가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="db194-183">In the sample above, the `PriorityList` view component becomes `priority-list`.</span></span> <span data-ttu-id="db194-184">보기 구성 요소에 대한 매개 변수는 케밥식의 특성으로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="db194-184">The parameters to the view component are passed as attributes in kebab case.</span></span>

::: moniker-end

### <a name="invoking-a-view-component-directly-from-a-controller"></a><span data-ttu-id="db194-185">컨트롤러에서 보기 구성 요소 직접 호출</span><span class="sxs-lookup"><span data-stu-id="db194-185">Invoking a view component directly from a controller</span></span>

<span data-ttu-id="db194-186">일반적으로 보기 구성 요소는 보기에서 호출되지만 컨트롤러 메서드에서 직접 호출할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db194-186">View components are typically invoked from a view, but you can invoke them directly from a controller method.</span></span> <span data-ttu-id="db194-187">보기 구성 요소는 컨트롤러 같은 엔드포인트를 정의하지 않지만 `ViewComponentResult`의 내용을 반환하는 컨트롤러 동작을 쉽게 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db194-187">While view components don't define endpoints like controllers, you can easily implement a controller action that returns the content of a `ViewComponentResult`.</span></span>

<span data-ttu-id="db194-188">이 예제에서는 보기 구성 요소가 컨트롤러에서 직접 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="db194-188">In this example, the view component is called directly from the controller:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

## <a name="walkthrough-creating-a-simple-view-component"></a><span data-ttu-id="db194-189">연습: 간단한 뷰 구성 요소 만들기</span><span class="sxs-lookup"><span data-stu-id="db194-189">Walkthrough: Creating a simple view component</span></span>

<span data-ttu-id="db194-190">시작 코드를 [다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample), 빌드 및 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-190">[Download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample), build and test the starter code.</span></span> <span data-ttu-id="db194-191">*ToDo* 항목의 목록을 표시하는 `ToDo` 컨트롤러가 포함된 간단한 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="db194-191">It's a simple project with a `ToDo` controller that displays a list of *ToDo* items.</span></span>

![ToDo 목록](view-components/_static/2dos.png)

### <a name="add-a-viewcomponent-class"></a><span data-ttu-id="db194-193">ViewComponent 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="db194-193">Add a ViewComponent class</span></span>

<span data-ttu-id="db194-194">*ViewComponents* 폴더를 만들고 다음 `PriorityListViewComponent` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-194">Create a *ViewComponents* folder and add the following `PriorityListViewComponent` class:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponent1.cs?name=snippet1)]

<span data-ttu-id="db194-195">코드에 대한 참고 사항:</span><span class="sxs-lookup"><span data-stu-id="db194-195">Notes on the code:</span></span>

* <span data-ttu-id="db194-196">보기 구성 요소 클래스는 프로젝트의 **모든** 폴더에 포함될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db194-196">View component classes can be contained in **any** folder in the project.</span></span>
* <span data-ttu-id="db194-197">클래스 이름 PriorityList**ViewComponent**는 **ViewComponent** 접미사로 끝나기 때문에 런타임은 뷰에서 클래스 구성 요소를 참조할 때 "PriorityList" 문자열을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-197">Because the class name PriorityList**ViewComponent** ends with the suffix **ViewComponent**, the runtime will use the string "PriorityList" when referencing the class component from a view.</span></span> <span data-ttu-id="db194-198">나중에 보다 자세히 설명하겠습니다.</span><span class="sxs-lookup"><span data-stu-id="db194-198">I'll explain that in more detail later.</span></span>
* <span data-ttu-id="db194-199">`[ViewComponent]` 특성은 보기 구성 요소를 참조하는 데 사용되는 이름을 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db194-199">The `[ViewComponent]` attribute can change the name used to reference a view component.</span></span> <span data-ttu-id="db194-200">예를 들어 클래스의 이름을 `XYZ`로 지정하고 `ViewComponent` 특성을 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db194-200">For example, we could've named the class `XYZ` and applied the `ViewComponent` attribute:</span></span>

  ```csharp
  [ViewComponent(Name = "PriorityList")]
     public class XYZ : ViewComponent
     ```

* <span data-ttu-id="db194-201">위의 `[ViewComponent]` 특성은 구성 요소와 연관된 뷰를 찾을 때 `PriorityList` 이름을 사용하고, 뷰에서 클래스 구성 요소를 참조할 때 "PriorityList" 문자열을 사용하도록 뷰 구성 요소 선택기에 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-201">The `[ViewComponent]` attribute above tells the view component selector to use the name `PriorityList` when looking for the views associated with the component, and to use the string "PriorityList" when referencing the class component from a view.</span></span> <span data-ttu-id="db194-202">나중에 보다 자세히 설명하겠습니다.</span><span class="sxs-lookup"><span data-stu-id="db194-202">I'll explain that in more detail later.</span></span>
* <span data-ttu-id="db194-203">이 구성 요소에서는 [종속성 주입](../../fundamentals/dependency-injection.md)을 사용하여 데이터 컨텍스트를 사용할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-203">The component uses [dependency injection](../../fundamentals/dependency-injection.md) to make the data context available.</span></span>
* <span data-ttu-id="db194-204">`InvokeAsync`는 보기에서 호출할 수 있는 메서드를 노출하며 임의의 개수의 인수를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db194-204">`InvokeAsync` exposes a method which can be called from a view, and it can take an arbitrary number of arguments.</span></span>
* <span data-ttu-id="db194-205">`InvokeAsync` 메서드는 `isDone` 및 `maxPriority` 매개 변수를 만족하는 `ToDo` 항목 집합을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-205">The `InvokeAsync` method returns the set of `ToDo` items that satisfy the `isDone` and `maxPriority` parameters.</span></span>

### <a name="create-the-view-component-razor-view"></a><span data-ttu-id="db194-206">보기 구성 요소 Razor 보기 만들기</span><span class="sxs-lookup"><span data-stu-id="db194-206">Create the view component Razor view</span></span>

* <span data-ttu-id="db194-207">*Views/Shared/Components* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="db194-207">Create the *Views/Shared/Components* folder.</span></span> <span data-ttu-id="db194-208">이 폴더의 이름은 *Components*로 **지정되어야만** 합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-208">This folder **must** be named *Components*.</span></span>

* <span data-ttu-id="db194-209">*Views/Shared/Components/PriorityList* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="db194-209">Create the *Views/Shared/Components/PriorityList* folder.</span></span> <span data-ttu-id="db194-210">이 폴더 이름은 보기 구성 요소 클래스의 이름 또는 클래스 이름에서 접미사를 뺀 이름과 일치해야 합니다(규칙을 준수하고 클래스 이름에 *ViewComponent* 접미사를 사용한 경우).</span><span class="sxs-lookup"><span data-stu-id="db194-210">This folder name must match the name of the view component class, or the name of the class minus the suffix (if we followed convention and used the *ViewComponent* suffix in the class name).</span></span> <span data-ttu-id="db194-211">`ViewComponent` 특성을 사용한 경우 클래스 이름은 특성 지정과 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-211">If you used the `ViewComponent` attribute, the class name would need to match the attribute designation.</span></span>

* <span data-ttu-id="db194-212">*Views/Shared/Components/PriorityList/Default.cshtml* Razor 보기를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="db194-212">Create a *Views/Shared/Components/PriorityList/Default.cshtml* Razor view:</span></span>


  [!code-cshtml[](view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/Default1.cshtml)]

   <span data-ttu-id="db194-213">Razor 뷰는 `TodoItem` 목록을 가져와 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-213">The Razor view takes a list of `TodoItem` and displays them.</span></span> <span data-ttu-id="db194-214">뷰 구성 요소 `InvokeAsync` 메서드가 뷰의 이름을 전달하지 않은 경우(샘플에서처럼) 규칙에 따라 뷰 이름으로 *Default*가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="db194-214">If the view component `InvokeAsync` method doesn't pass the name of the view (as in our sample), *Default* is used for the view name by convention.</span></span> <span data-ttu-id="db194-215">자습서의 뒷부분에서 뷰 이름을 전달하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="db194-215">Later in the tutorial, I'll show you how to pass the name of the view.</span></span> <span data-ttu-id="db194-216">특정 컨트롤러에 대한 기본 스타일 지정을 재정의하려면 컨트롤러 관련 뷰 폴더에 뷰를 추가합니다(예: *Views/ToDo/Components/PriorityList/Default.cshtml)* .</span><span class="sxs-lookup"><span data-stu-id="db194-216">To override the default styling for a specific controller, add a view to the controller-specific view folder (for example *Views/ToDo/Components/PriorityList/Default.cshtml)*.</span></span>

    <span data-ttu-id="db194-217">보기 구성 요소가 컨트롤러에 관한 것이면 컨트롤러 관련 폴더에 추가할 수 있습니다(*Views/ToDo/Components/PriorityList/Default.cshtml*).</span><span class="sxs-lookup"><span data-stu-id="db194-217">If the view component is controller-specific, you can add it to the controller-specific folder (*Views/ToDo/Components/PriorityList/Default.cshtml*).</span></span>

* <span data-ttu-id="db194-218">우선 순위 목록 구성 요소에 대한 호출을 포함하는 `div`를 *Views/ToDo/index.cshtml* 파일 맨 아래에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-218">Add a `div` containing a call to the priority list component to the bottom of the *Views/ToDo/index.cshtml* file:</span></span>

    [!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFirst.cshtml?range=34-38)]

<span data-ttu-id="db194-219">`@await Component.InvokeAsync` 태그는 호출하는 보기 구성 요소에 대한 구문을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="db194-219">The markup `@await Component.InvokeAsync` shows the syntax for calling view components.</span></span> <span data-ttu-id="db194-220">첫 번째 인수는 실행하거나 호출하려는 구성 요소의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="db194-220">The first argument is the name of the component we want to invoke or call.</span></span> <span data-ttu-id="db194-221">후속 매개 변수는 구성 요소에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="db194-221">Subsequent parameters are passed to the component.</span></span> <span data-ttu-id="db194-222">`InvokeAsync`는 임의 개수의 인수를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db194-222">`InvokeAsync` can take an arbitrary number of arguments.</span></span>

<span data-ttu-id="db194-223">앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-223">Test the app.</span></span> <span data-ttu-id="db194-224">다음 이미지는 ToDo 목록 및 우선 순위 항목을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="db194-224">The following image shows the ToDo list and the priority items:</span></span>

![todo 목록 및 우선 순위 항목](view-components/_static/pi.png)

<span data-ttu-id="db194-226">또한 컨트롤러에서 보기 구성 요소를 직접 호출할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db194-226">You can also call the view component directly from the controller:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

![IndexVC 작업에서 우선 순위 항목](view-components/_static/indexvc.png)

### <a name="specifying-a-view-name"></a><span data-ttu-id="db194-228">보기 이름 지정</span><span class="sxs-lookup"><span data-stu-id="db194-228">Specifying a view name</span></span>

<span data-ttu-id="db194-229">복잡한 뷰 구성 요소에는 조건에 따라 기본값이 아닌 뷰를 지정해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db194-229">A complex view component might need to specify a non-default view under some conditions.</span></span> <span data-ttu-id="db194-230">다음 코드에서 `InvokeAsync` 메서드에서 "PVC" 뷰를 지정하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="db194-230">The following code shows how to specify the "PVC" view  from the `InvokeAsync` method.</span></span> <span data-ttu-id="db194-231">`PriorityListViewComponent` 클래스에서 `InvokeAsync` 메서드를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-231">Update the `InvokeAsync` method in the `PriorityListViewComponent` class.</span></span>

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponentFinal.cs?highlight=4,5,6,7,8,9&range=28-39)]

<span data-ttu-id="db194-232">*Views/Shared/Components/PriorityList/Default.cshtml* 파일을 *Views/Shared/Components/PriorityList/PVC.cshtml*이라는 보기로 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-232">Copy the *Views/Shared/Components/PriorityList/Default.cshtml* file to a view named *Views/Shared/Components/PriorityList/PVC.cshtml*.</span></span> <span data-ttu-id="db194-233">PVC 보기가 사용되었음을 나타내는 제목을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-233">Add a heading to indicate the PVC view is being used.</span></span>

[!code-cshtml[](../../mvc/views/view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/PVC.cshtml?highlight=3)]

<span data-ttu-id="db194-234">*Views/ToDo/Index.cshtml*을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-234">Update *Views/ToDo/Index.cshtml*:</span></span>

<!-- Views/ToDo/Index.cshtml is never imported, so change to test tutorial -->

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

<span data-ttu-id="db194-235">앱을 실행하고 PVC 보기를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-235">Run the app and verify PVC view.</span></span>

![우선 순위 보기 구성 요소](view-components/_static/pvc.png)

<span data-ttu-id="db194-237">PVC 보기가 렌더링되지 않는다면 우선 순위가 4 이상인 보기 구성 요소를 호출하고 있는지 확인해봅니다.</span><span class="sxs-lookup"><span data-stu-id="db194-237">If the PVC view isn't rendered, verify you are calling the view component with a priority of 4 or higher.</span></span>

### <a name="examine-the-view-path"></a><span data-ttu-id="db194-238">보기 경로 검토</span><span class="sxs-lookup"><span data-stu-id="db194-238">Examine the view path</span></span>

* <span data-ttu-id="db194-239">우선 순위 보기가 반환되지 않도록 우선 순위 매개 변수를 3 이하로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-239">Change the priority parameter to three or less so the priority view isn't returned.</span></span>
* <span data-ttu-id="db194-240">임시로 *Views/ToDo/Components/PriorityList/Default.cshtml*의 이름을 *1Default.cshtml*로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-240">Temporarily rename the *Views/ToDo/Components/PriorityList/Default.cshtml* to *1Default.cshtml*.</span></span>
* <span data-ttu-id="db194-241">앱을 테스트하면 다음과 같은 오류가 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-241">Test the app, you'll get the following error:</span></span>

   ```
   An unhandled exception occurred while processing the request.
   InvalidOperationException: The view 'Components/PriorityList/Default' wasn't found. The following locations were searched:
   /Views/ToDo/Components/PriorityList/Default.cshtml
   /Views/Shared/Components/PriorityList/Default.cshtml
   EnsureSuccessful
   ```

* <span data-ttu-id="db194-242">*Views/ToDo/Components/PriorityList/1Default.cshtml*을 *Views/Shared/Components/PriorityList/Default.cshtml*에 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-242">Copy *Views/ToDo/Components/PriorityList/1Default.cshtml* to *Views/Shared/Components/PriorityList/Default.cshtml*.</span></span>
* <span data-ttu-id="db194-243">*Shared* ToDo 보기 구성 요소 보기에 일부 태그를 추가하여 보기가 *Shared* 폴더에 있음을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-243">Add some markup to the *Shared* ToDo view component view to indicate the view is from the *Shared* folder.</span></span>
* <span data-ttu-id="db194-244">**Shared** 구성 요소 보기를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-244">Test the **Shared** component view.</span></span>

![공유 구성 요소 보기가 있는 ToDo 출력](view-components/_static/shared.png)

### <a name="avoiding-hard-coded-strings"></a><span data-ttu-id="db194-246">하드 코드된 문자열 방지</span><span class="sxs-lookup"><span data-stu-id="db194-246">Avoiding hard-coded strings</span></span>

<span data-ttu-id="db194-247">컴파일 시간 안전성을 원하는 경우 하드 코드된 뷰 구성 요소 이름을 클래스 이름으로 바꿀 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="db194-247">If you want compile time safety, you can replace the hard-coded view component name with the class name.</span></span> <span data-ttu-id="db194-248">"ViewComponent" 접미사 없이 뷰 구성 요소를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="db194-248">Create the view component without the "ViewComponent" suffix:</span></span>

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityList.cs?highlight=10&range=5-35)]

<span data-ttu-id="db194-249">`using` 문을 Razor 보기 파일에 추가하고 `nameof` 연산자를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-249">Add a `using` statement to your Razor view file, and use the `nameof` operator:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexNameof.cshtml?range=1-6,35-)]

## <a name="perform-synchronous-work"></a><span data-ttu-id="db194-250">동기 작업 수행</span><span class="sxs-lookup"><span data-stu-id="db194-250">Perform synchronous work</span></span>

<span data-ttu-id="db194-251">비동기 작업을 수행할 필요가 없는 경우 프레임워크에서 동기 `Invoke` 메서드 호출을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-251">The framework handles invoking a synchronous `Invoke` method if you don't need to perform asynchronous work.</span></span> <span data-ttu-id="db194-252">다음 메서드는 동기 `Invoke` 보기 구성 요소를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="db194-252">The following method creates a synchronous `Invoke` view component:</span></span>

```csharp
public class PriorityList : ViewComponent
{
    public IViewComponentResult Invoke(int maxPriority, bool isDone)
    {
        var items = new List<string> { $"maxPriority: {maxPriority}", $"isDone: {isDone}" };
        return View(items);
    }
}
```

<span data-ttu-id="db194-253">보기 구성 요소의 Razor 파일은 `Invoke` 메서드에 전달된 문자열을 나열합니다(*Views/Home/Components/PriorityList/Default.cshtml*).</span><span class="sxs-lookup"><span data-stu-id="db194-253">The view component's Razor file lists the strings passed to the `Invoke` method (*Views/Home/Components/PriorityList/Default.cshtml*):</span></span>

```cshtml
@model List<string>

<h3>Priority Items</h3>
<ul>
    @foreach (var item in Model)
    {
        <li>@item</li>
    }
</ul>
```

::: moniker range=">= aspnetcore-1.1"

<span data-ttu-id="db194-254">보기 구성 요소는 다음 방법 중 하나를 사용하여 Razor 파일(예: *Views/Home/Index.cshtml*)에서 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="db194-254">The view component is invoked in a Razor file (for example, *Views/Home/Index.cshtml*) using one of the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>
* [<span data-ttu-id="db194-255">태그 도우미</span><span class="sxs-lookup"><span data-stu-id="db194-255">Tag Helper</span></span>](xref:mvc/views/tag-helpers/intro)

<span data-ttu-id="db194-256"><xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> 접근 방법을 사용하려면 `Component.InvokeAsync`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-256">To use the <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> approach, call `Component.InvokeAsync`:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-1.1"

<span data-ttu-id="db194-257">보기 구성 요소는 <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>를 사용하여 Razor 파일(예: *Views/Home/Index.cshtml*)에서 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="db194-257">The view component is invoked in a Razor file (for example, *Views/Home/Index.cshtml*) with <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>.</span></span>

<span data-ttu-id="db194-258">`Component.InvokeAsync`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-258">Call `Component.InvokeAsync`:</span></span>

::: moniker-end

```cshtml
@await Component.InvokeAsync(nameof(PriorityList), new { maxPriority = 4, isDone = true })
```

::: moniker range=">= aspnetcore-1.1"

<span data-ttu-id="db194-259">태그 도우미를 사용하려면 `@addTagHelper` 지시문을 사용하여 보기 구성 요소가 포함된 어셈블리를 등록합니다(보기 구성 요소는 `MyWebApp`이라는 어셈블리에 있음).</span><span class="sxs-lookup"><span data-stu-id="db194-259">To use the Tag Helper, register the assembly containing the View Component using the `@addTagHelper` directive (the view component is in an assembly called `MyWebApp`):</span></span>

```cshtml
@addTagHelper *, MyWebApp
```

<span data-ttu-id="db194-260">Razor 태그 파일에서 보기 구성 요소 태그 도우미를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-260">Use the view component Tag Helper in the Razor markup file:</span></span>

```cshtml
<vc:priority-list max-priority="999" is-done="false">
</vc:priority-list>
```

::: moniker-end

<span data-ttu-id="db194-261">`PriorityList.Invoke`의 메서드 시그니처는 동기식이지만, Razor는 태그 파일에서 `Component.InvokeAsync`를 사용하여 메서드를 찾고 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="db194-261">The method signature of `PriorityList.Invoke` is synchronous, but Razor finds and calls the method with `Component.InvokeAsync` in the markup file.</span></span>

## <a name="all-view-component-parameters-are-required"></a><span data-ttu-id="db194-262">모든 보기 구성 요소 매개 변수는 필수</span><span class="sxs-lookup"><span data-stu-id="db194-262">All view component parameters are required</span></span>

<span data-ttu-id="db194-263">보기 구성 요소의 각 매개 변수는 필수 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="db194-263">Each parameter in a view component is a required attribute.</span></span> <span data-ttu-id="db194-264">이 [GitHub 이슈](https://github.com/aspnet/AspNetCore/issues/5011)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="db194-264">See [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/5011).</span></span> <span data-ttu-id="db194-265">매개 변수가 하나라도 생략되면:</span><span class="sxs-lookup"><span data-stu-id="db194-265">If any  parameter is omitted:</span></span>

* <span data-ttu-id="db194-266">`InvokeAsync` 메서드 시그니처가 일치하지 않게 되므로 메서드가 실행되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="db194-266">The `InvokeAsync` method signature won't match, therefore the method won't execute.</span></span>
* <span data-ttu-id="db194-267">ViewComponent가 마크업을 렌더링하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="db194-267">The ViewComponent won't render any markup.</span></span>
* <span data-ttu-id="db194-268">오류가 던져지지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="db194-268">No errors will be thrown.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="db194-269">추가 자료</span><span class="sxs-lookup"><span data-stu-id="db194-269">Additional resources</span></span>

* [<span data-ttu-id="db194-270">보기에 종속성 주입</span><span class="sxs-lookup"><span data-stu-id="db194-270">Dependency injection into views</span></span>](xref:mvc/views/dependency-injection)
