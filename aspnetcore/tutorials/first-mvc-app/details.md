---
title: ASP.NET Core 앱의 Details 및 Delete 메서드 검사
author: rick-anderson
description: 기본적인 ASP.NET Core MVC 앱에서 Details 컨트롤러 메서드 및 보기에 대해 알아봅니다.
ms.author: riande
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/details
ms.openlocfilehash: 056017ea4f4073fa0b1cd747d06775b2a33616cf
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59012671"
---
# <a name="examine-the-details-and-delete-methods-of-an-aspnet-core-app"></a><span data-ttu-id="8ae95-103">ASP.NET Core 앱의 Details 및 Delete 메서드 검사</span><span class="sxs-lookup"><span data-stu-id="8ae95-103">Examine the Details and Delete methods of an ASP.NET Core app</span></span>

<span data-ttu-id="8ae95-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8ae95-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="8ae95-105">Movie 컨트롤러를 열고 `Details` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="8ae95-105">Open the Movie controller and examine the `Details` method:</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_details)]

<span data-ttu-id="8ae95-106">이 작업 메서드를 만든 MVC 스캐폴딩 엔진은 메서드를 호출하는 HTTP 요청을 보여 주는 설명을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="8ae95-106">The MVC scaffolding engine that created this action method adds a comment showing an HTTP request that invokes the method.</span></span> <span data-ttu-id="8ae95-107">이 경우 3개의 URL 세그먼트인 `Movies` 컨트롤러, `Details` 메서드 및 `id` 값을 가진 GET 요청입니다.</span><span class="sxs-lookup"><span data-stu-id="8ae95-107">In this case it's a GET request with three URL segments, the `Movies` controller, the `Details` method, and an `id` value.</span></span> <span data-ttu-id="8ae95-108">이러한 세그먼트 회수는 *Startup.cs*에서 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ae95-108">Recall these segments are defined in *Startup.cs*.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

<span data-ttu-id="8ae95-109">EF를 통해 `FirstOrDefaultAsync` 메서드를 사용하는 데이터를 쉽게 검색할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ae95-109">EF makes it easy to search for data using the `FirstOrDefaultAsync` method.</span></span> <span data-ttu-id="8ae95-110">메서드에 기본 구성된 중요한 보안 기능은 검색 메서드가 이를 사용하여 어떠한 작업을 시도하기 전에 동영상을 발견했는지 코드에서 확인하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="8ae95-110">An important security feature built into the method is that the code verifies that the search method has found a movie before it tries to do anything with it.</span></span> <span data-ttu-id="8ae95-111">예를 들어 해커는 링크에서 만든 URL을 `http://localhost:xxxx/Movies/Details/1`에서 `http://localhost:xxxx/Movies/Details/12345`(또는 실제 동영상을 표시하지 않는 다른 값)와 같은 URL로 변경하여 사이트에 오류를 발생시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ae95-111">For example, a hacker could introduce errors into the site by changing the URL created by the links from `http://localhost:xxxx/Movies/Details/1` to something like  `http://localhost:xxxx/Movies/Details/12345` (or some other value that doesn't represent an actual movie).</span></span> <span data-ttu-id="8ae95-112">Null 동영상을 검사하지 않은 경우 앱은 예외를 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="8ae95-112">If you didn't check for a null movie, the app would throw an exception.</span></span>

<span data-ttu-id="8ae95-113">`Delete` 및 `DeleteConfirmed` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="8ae95-113">Examine the `Delete` and `DeleteConfirmed` methods.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_delete)]

<span data-ttu-id="8ae95-114">`HTTP GET Delete` 메서드는 지정된 동영상을 삭제하지 않고 삭제를 제출(HttpPost)할 수 있는 동영상의 보기를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="8ae95-114">Note that the `HTTP GET Delete` method doesn't delete the specified movie, it returns a view of the movie where you can submit (HttpPost) the deletion.</span></span> <span data-ttu-id="8ae95-115">GET 요청에 대한 응답에서 삭제 작업 수행(또는 해당 문제를 위해 편집 작업 수행, 작업 만들기 또는 데이터를 변경하는 기타 작업)은 보안 허점을 야기합니다.</span><span class="sxs-lookup"><span data-stu-id="8ae95-115">Performing a delete operation in response to a GET request (or for that matter, performing an edit operation, create operation, or any other operation that changes data) opens up a security hole.</span></span>

<span data-ttu-id="8ae95-116">데이터를 삭제하는 `[HttpPost]` 메서드는 HTTP POST 메서드에 고유한 서명 또는 이름을 제공하기 위해 `DeleteConfirmed`로 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ae95-116">The `[HttpPost]` method that deletes the data is named `DeleteConfirmed` to give the HTTP POST method a unique signature or name.</span></span> <span data-ttu-id="8ae95-117">두 개의 메서드 서명은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8ae95-117">The two method signatures are shown below:</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete2)]

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete3)]

<span data-ttu-id="8ae95-118">CLR(공용 언어 런타임)은 고유한 매개 변수 서명을 갖기 위해 오버로드된 메서드가 필요합니다(동일한 메서드 이름이지만 다른 매개 변수의 목록).</span><span class="sxs-lookup"><span data-stu-id="8ae95-118">The common language runtime (CLR) requires overloaded methods to have a unique parameter signature (same method name but different list of parameters).</span></span> <span data-ttu-id="8ae95-119">그러나 여기에서 두 개의 `Delete` 메서드가 필요합니다. 하나는 GET에 대한 것이며 다른 하나는 POST에 대한 것입니다. 두 메서드에는 동일한 매개 변수 서명이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ae95-119">However, here you need two `Delete` methods -- one for GET and one for POST -- that both have the same parameter signature.</span></span> <span data-ttu-id="8ae95-120">(모두 매개 변수로 단일 정수를 허용해야 합니다.)</span><span class="sxs-lookup"><span data-stu-id="8ae95-120">(They both need to accept a single integer as a parameter.)</span></span>

<span data-ttu-id="8ae95-121">이 문제에 대한 두 가지의 방법이 있습니다. 하나는 메서드에 서로 다른 이름을 지정하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="8ae95-121">There are two approaches to this problem, one is to give the methods different names.</span></span> <span data-ttu-id="8ae95-122">앞의 예에서 스캐폴딩 메커니즘이 수행한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="8ae95-122">That's what the scaffolding mechanism did in the preceding example.</span></span> <span data-ttu-id="8ae95-123">그러나 이는 작은 문제를 야기합니다. ASP.NET은 URL의 세그먼트를 이름으로 작업 메서드에 매핑하고 메서드의 이름을 바꾸면 정상적으로 라우팅하여 해당 메서드를 찾을 수 없게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8ae95-123">However, this introduces a small problem: ASP.NET maps segments of a URL to action methods by name, and if you rename a method, routing normally wouldn't be able to find that method.</span></span> <span data-ttu-id="8ae95-124">솔루션은 예제에서 확인한 것으로, `ActionName("Delete")` 특성을 `DeleteConfirmed` 메서드에 추가하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="8ae95-124">The solution is what you see in the example, which is to add the `ActionName("Delete")` attribute to the `DeleteConfirmed` method.</span></span> <span data-ttu-id="8ae95-125">해당 특성은 POST 요청에 대한 /Delete/를 포함하는 URL이 `DeleteConfirmed` 메서드를 찾도록 라우팅 시스템에 대한 매핑을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="8ae95-125">That attribute performs mapping for the routing system so that a URL that includes /Delete/ for a POST request will find the `DeleteConfirmed` method.</span></span>

<span data-ttu-id="8ae95-126">동일한 이름 및 서명을 가진 메서드에 대한 또 다른 일반적인 해결책은 POST 메서드의 서명을 추가(사용되지 않는) 매개 변수를 포함하도록 인위적으로 변경하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="8ae95-126">Another common work around for methods that have identical names and signatures is to artificially change the signature of the POST method to include an extra (unused) parameter.</span></span> <span data-ttu-id="8ae95-127">즉, `notUsed` 매개 변수를 추가했을 때 이전 게시에서 수행했던 작업입니다.</span><span class="sxs-lookup"><span data-stu-id="8ae95-127">That's what we did in a previous post when we added the `notUsed` parameter.</span></span> <span data-ttu-id="8ae95-128">`[HttpPost] Delete` 메서드에 대해 여기에서 동일한 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8ae95-128">You could do the same thing here for the `[HttpPost] Delete` method:</span></span>

```csharp
// POST: Movies/Delete/6
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Delete(int id, bool notUsed)
```

### <a name="publish-to-azure"></a><span data-ttu-id="8ae95-129">Azure에 게시</span><span class="sxs-lookup"><span data-stu-id="8ae95-129">Publish to Azure</span></span>

<span data-ttu-id="8ae95-130">Azure에 배포하는 방법에 대한 자세한 내용은 [자습서: Azure App Service에서 .NET Core 및 SQL Database 웹앱 빌드](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8ae95-130">For information on deploying to Azure, see [Tutorial: Build a .NET Core and SQL Database web app in Azure App Service](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="8ae95-131">이전</span><span class="sxs-lookup"><span data-stu-id="8ae95-131">Previous</span></span>](validation.md)
