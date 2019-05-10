---
title: ASP.NET Core의 보기에 종속성 주입
author: ardalis
description: ASP.NET Core가 MVC 보기에 종속성 주입을 지원하는 방법을 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
uid: mvc/views/dependency-injection
ms.openlocfilehash: 940e237fd73a864d9fd59f5447fe486f93648f62
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087475"
---
# <a name="dependency-injection-into-views-in-aspnet-core"></a><span data-ttu-id="7f37c-103">ASP.NET Core의 보기에 종속성 주입</span><span class="sxs-lookup"><span data-stu-id="7f37c-103">Dependency injection into views in ASP.NET Core</span></span>

<span data-ttu-id="7f37c-104">작성자: [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="7f37c-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="7f37c-105">ASP.NET Core는 보기에 [종속성 주입](xref:fundamentals/dependency-injection)을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="7f37c-105">ASP.NET Core supports [dependency injection](xref:fundamentals/dependency-injection) into views.</span></span> <span data-ttu-id="7f37c-106">보기 요소를 채우는 데만 필요한 지역화 또는 데이터 같은 보기 관련 서비스에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="7f37c-106">This can be useful for view-specific services, such as localization or data required only for populating view elements.</span></span> <span data-ttu-id="7f37c-107">컨트롤러와 보기 사이에 [문제를 분리](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f37c-107">You should try to maintain [separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) between your controllers and views.</span></span> <span data-ttu-id="7f37c-108">보기에서 표시하는 대부분의 데이터는 컨트롤러에서 전달되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f37c-108">Most of the data your views display should be passed in from the controller.</span></span>

<span data-ttu-id="7f37c-109">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/dependency-injection/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7f37c-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="a-simple-example"></a><span data-ttu-id="7f37c-110">간단한 예제</span><span class="sxs-lookup"><span data-stu-id="7f37c-110">A Simple Example</span></span>

<span data-ttu-id="7f37c-111">`@inject` 지시문을 사용하여 보기에 서비스를 삽입할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7f37c-111">You can inject a service into a view using the `@inject` directive.</span></span> <span data-ttu-id="7f37c-112">`@inject`를 보기에 속성을 추가하고 DI를 사용하여 속성을 채우는 것으로 생각하셔도 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="7f37c-112">You can think of `@inject` as adding a property to your view, and populating the property using DI.</span></span>

<span data-ttu-id="7f37c-113">`@inject`에 대한 구문: `@inject <type> <name>`</span><span class="sxs-lookup"><span data-stu-id="7f37c-113">The syntax for `@inject`: `@inject <type> <name>`</span></span>

<span data-ttu-id="7f37c-114">실제로 작동하는 `@inject` 예제:</span><span class="sxs-lookup"><span data-stu-id="7f37c-114">An example of `@inject` in action:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/ToDo/Index.cshtml?highlight=4,5,15,16,17)]

<span data-ttu-id="7f37c-115">이 보기는 전체 통계를 보여주는 요약 정보와 함께 `ToDoItem` 인스턴스 목록을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="7f37c-115">This view displays a list of `ToDoItem` instances, along with a summary showing overall statistics.</span></span> <span data-ttu-id="7f37c-116">요약 정보는 주입된 `StatisticsService`에서 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="7f37c-116">The summary is populated from the injected `StatisticsService`.</span></span> <span data-ttu-id="7f37c-117">이 서비스는 *Startup.cs*의 `ConfigureServices`에서 종속성 주입을 위해 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f37c-117">This service is registered for dependency injection in `ConfigureServices` in *Startup.cs*:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Startup.cs?highlight=6,7&range=15-22)]

<span data-ttu-id="7f37c-118">`StatisticsService`는 리포지토리를 통해 액세스되는 `ToDoItem` 인스턴스 집합에서 몇 가지 계산을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7f37c-118">The `StatisticsService` performs some calculations on the set of `ToDoItem` instances, which it accesses via a repository:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/StatisticsService.cs?highlight=15,20,25)]

<span data-ttu-id="7f37c-119">샘플 리포지토리는 메모리 내 컬렉션을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7f37c-119">The sample repository uses an in-memory collection.</span></span> <span data-ttu-id="7f37c-120">위에서 보여드린 구현은 메모리 내 모든 데이터에서 작동하므로 원격으로 액세스되는 대규모 데이터 집합에는 권장하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7f37c-120">The implementation shown above (which operates on all of the data in memory) isn't recommended for large, remotely accessed data sets.</span></span>

<span data-ttu-id="7f37c-121">이 샘플은 보기에 바인딩된 모델과 보기에 주입된 서비스의 데이터를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="7f37c-121">The sample displays data from the model bound to the view and the service injected into the view:</span></span>

![총 항목, 완료된 항목, 평균 우선 순위, 작업 목록을 해당 우선 순위 수준 및 작업 완료를 나타내는 부울 값과 함께 나열하는 할 일 보기입니다.](dependency-injection/_static/screenshot.png)

## <a name="populating-lookup-data"></a><span data-ttu-id="7f37c-123">조회 데이터 채우기</span><span class="sxs-lookup"><span data-stu-id="7f37c-123">Populating Lookup Data</span></span>

<span data-ttu-id="7f37c-124">보기 주입은 드롭다운 목록 같은 UI 요소의 옵션을 채우는 데 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7f37c-124">View injection can be useful to populate options in UI elements, such as dropdown lists.</span></span> <span data-ttu-id="7f37c-125">성별, 상태 및 기타 기본 설정을 지정하는 옵션이 포함된 사용자 프로필 양식을 고려해 보세요.</span><span class="sxs-lookup"><span data-stu-id="7f37c-125">Consider a user profile form that includes options for specifying gender, state, and other preferences.</span></span> <span data-ttu-id="7f37c-126">표준 MVC 방식을 사용하는 양식 같은 렌더링의 경우 컨트롤러에서 이러한 각 옵션 집합에 대한 데이터 액세스 서비스를 요청한 후 모델 또는 `ViewBag`을 바인딩할 각 옵션 집합으로 채워야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f37c-126">Rendering such a form using a standard MVC approach would require the controller to request data access services for each of these sets of options, and then populate a model or `ViewBag` with each set of options to be bound.</span></span>

<span data-ttu-id="7f37c-127">다른 방법은 서비스를 보기에 직접 주입하여 옵션을 가져오는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="7f37c-127">An alternative approach injects services directly into the view to obtain the options.</span></span> <span data-ttu-id="7f37c-128">이 방법은 컨트롤러에 필요한 코드 양을 최소화하고, 이 보기 요소 생성 논리를 보기 자체로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="7f37c-128">This minimizes the amount of code required by the controller, moving this view element construction logic into the view itself.</span></span> <span data-ttu-id="7f37c-129">프로필 편집 양식을 표시하는 컨트롤러 작업은 양식을 프로필 인스턴스에 전달하기만 하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f37c-129">The controller action to display a profile editing form only needs to pass the form the profile instance:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Controllers/ProfileController.cs?highlight=9,19)]

<span data-ttu-id="7f37c-130">이러한 기본 설정을 업데이트하는 데 사용되는 HTML 양식으로는 다음과 같은 세 가지 속성에 대한 드롭다운 목록이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f37c-130">The HTML form used to update these preferences includes dropdown lists for three of the properties:</span></span>

![이름, 성별, 상태 및 좋아하는 색을 입력할 수 있는 양식을 제공하는 프로필 업데이트 보기.](dependency-injection/_static/updateprofile.png)

<span data-ttu-id="7f37c-132">이러한 목록은 보기에 주입된 서비스로 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="7f37c-132">These lists are populated by a service that has been injected into the view:</span></span>

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Profile/Index.cshtml?highlight=4,16,17,21,22,26,27)]

<span data-ttu-id="7f37c-133">`ProfileOptionsService`는 이 양식에 필요한 데이터만 제공하도록 설계된 UI 수준 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="7f37c-133">The `ProfileOptionsService` is a UI-level service designed to provide just the data needed for this form:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/ProfileOptionsService.cs?highlight=7,13,24)]

> [!IMPORTANT]
> <span data-ttu-id="7f37c-134">`Startup.ConfigureServices`에서 종속성 주입을 통해 요청할 형식을 등록하는 것을 잊지 마세요.</span><span class="sxs-lookup"><span data-stu-id="7f37c-134">Don't forget to register types you request through dependency injection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7f37c-135">서비스 공급자가 [GetRequiredService](/dotnet/api/microsoft.extensions.dependencyinjection.serviceproviderserviceextensions.getrequiredservice)를 통해 내부적으로 쿼리되기 때문에 등록되지 않은 형식은 런타임 시 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="7f37c-135">An unregistered type throws an exception at runtime because the service provider is internally queried via [GetRequiredService](/dotnet/api/microsoft.extensions.dependencyinjection.serviceproviderserviceextensions.getrequiredservice).</span></span>

## <a name="overriding-services"></a><span data-ttu-id="7f37c-136">서비스 재정의</span><span class="sxs-lookup"><span data-stu-id="7f37c-136">Overriding Services</span></span>

<span data-ttu-id="7f37c-137">새 서비스 주입 외에도, 이 기술은 페이지에서 이전에 주입된 서비스를 재정의하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7f37c-137">In addition to injecting new services, this technique can also be used to override previously injected services on a page.</span></span> <span data-ttu-id="7f37c-138">아래 그림은 첫 번째 예에서 사용된 페이지에서 사용 가능한 모든 필드를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="7f37c-138">The figure below shows all of the fields available on the page used in the first example:</span></span>

![입력된 @ 기호에서 Html, 구성 요소, StatsService 및 Url 필드를 나열하는 Intellisense 상황에 맞는 메뉴](dependency-injection/_static/razor-fields.png)

<span data-ttu-id="7f37c-140">보시는 것처럼 기본 필드로 `Html`, `Component` 및 `Url`(그리고 우리가 주입한 `StatsService`)이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7f37c-140">As you can see, the default fields include `Html`, `Component`, and `Url` (as well as the `StatsService` that we injected).</span></span> <span data-ttu-id="7f37c-141">예를 들어 기본 HTML 도우미를 개발자 고유의 도우미로 바꾸려면 간단하게 `@inject`를 사용하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f37c-141">If for instance you wanted to replace the default HTML Helpers with your own, you could easily do so using `@inject`:</span></span>

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Helper/Index.cshtml?highlight=3,11)]

<span data-ttu-id="7f37c-142">기존 서비스를 확장하려는 경우 간단하게 이 기술을 사용하여 기존 구현에서 상속하거나 기존 구현을 개발자 고유의 구현으로 래핑하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f37c-142">If you want to extend existing services, you can simply use this technique while inheriting from or wrapping the existing implementation with your own.</span></span>

## <a name="see-also"></a><span data-ttu-id="7f37c-143">참고 항목</span><span class="sxs-lookup"><span data-stu-id="7f37c-143">See Also</span></span>

* <span data-ttu-id="7f37c-144">Simon Timms 블로그: [Getting Lookup Data Into Your View](http://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)(보기로 조회 데이터 가져오기)</span><span class="sxs-lookup"><span data-stu-id="7f37c-144">Simon Timms Blog: [Getting Lookup Data Into Your View](http://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)</span></span>
