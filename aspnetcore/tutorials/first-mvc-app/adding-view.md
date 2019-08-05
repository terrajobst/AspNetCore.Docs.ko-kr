---
title: ASP.NET Core MVC 앱에 보기 추가
author: rick-anderson
description: 간단한 ASP.NET Core MVC 앱에 보기 추가
ms.author: riande
ms.date: 03/04/2017
uid: tutorials/first-mvc-app/adding-view
ms.openlocfilehash: 38efd234d919cd0427928df73388e65ce13527d1
ms.sourcegitcommit: 979dbfc5e9ce09b9470789989cddfcfb57079d94
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68681889"
---
# <a name="add-a-view-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="5a94c-103">ASP.NET Core MVC 앱에 보기 추가</span><span class="sxs-lookup"><span data-stu-id="5a94c-103">Add a view to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="5a94c-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5a94c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5a94c-105">이 섹션에서는 [Razor](xref:mvc/views/razor) 보기 파일을 사용하도록 `HelloWorldController` 클래스를 수정하여 클라이언트에 HTML 응답을 생성하는 프로세스를 명확하게 캡슐화합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-105">In this section you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="5a94c-106">Razor를 사용하여 뷰 템플릿 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-106">You create a view template file using Razor.</span></span> <span data-ttu-id="5a94c-107">Razor 기반 뷰 템플릿에는 *.cshtml* 파일 확장이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-107">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="5a94c-108">C#으로 HTML 출력을 만들 수 있는 세련된 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-108">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="5a94c-109">현재 `Index` 메서드는 컨트롤러 클래스에서 하드 코딩된 메시지 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-109">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="5a94c-110">`HelloWorldController` 클래스에서 `Index` 메서드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-110">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="5a94c-111">앞의 코드는 컨트롤러의 <xref:Microsoft.AspNetCore.Mvc.Controller.View*> 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-111">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="5a94c-112">HTML 응답을 생성하기 위해 뷰 템플릿을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-112">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="5a94c-113">위의 `Index` 메서드와 같은 컨트롤러 메서드(‘동작 메서드’라고도 함)는 일반적으로 `string`과 같은 형식이 아닌 <xref:Microsoft.AspNetCore.Mvc.IActionResult>(또는 <xref:Microsoft.AspNetCore.Mvc.ActionResult>에서 파생된 클래스)를 반환합니다. </span><span class="sxs-lookup"><span data-stu-id="5a94c-113">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="5a94c-114">보기 추가</span><span class="sxs-lookup"><span data-stu-id="5a94c-114">Add a view</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5a94c-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5a94c-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5a94c-116">마우스 오른쪽 단추로 *Views* 폴더, **추가 > 새 폴더**를 차례로 클릭하고 폴더의 이름을 *HelloWorld*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-116">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="5a94c-117">마우스 오른쪽 단추로 *Views/HelloWorld* 폴더, **추가 > 새 항목**을 차례로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-117">Right click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="5a94c-118">**새 항목 추가 - MvcMovie** 대화 상자에서</span><span class="sxs-lookup"><span data-stu-id="5a94c-118">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="5a94c-119">오른쪽 위의 검색 상자에서 *보기* 입력</span><span class="sxs-lookup"><span data-stu-id="5a94c-119">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="5a94c-120">**Razor 보기** 선택</span><span class="sxs-lookup"><span data-stu-id="5a94c-120">Select **Razor View**</span></span>

  * <span data-ttu-id="5a94c-121">*이름* 상자 값 **Index.cshtml**을 유지하세요.</span><span class="sxs-lookup"><span data-stu-id="5a94c-121">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="5a94c-122">**추가** 선택</span><span class="sxs-lookup"><span data-stu-id="5a94c-122">Select **Add**</span></span>

![새 항목 추가 대화 상자](adding-view/_static/add_view.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5a94c-124">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5a94c-124">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5a94c-125">`HelloWorldController`에 대한 `Index` 보기를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-125">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="5a94c-126">*Views/HelloWorld*라는 새 폴더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-126">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="5a94c-127">*Views/HelloWorld* 폴더에 새 파일을 추가하고 *Index.cshtml*의 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-127">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5a94c-128">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5a94c-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="5a94c-129">마우스 오른쪽 단추로 *Views* 폴더, **추가 > 새 폴더**를 차례로 클릭하고 폴더의 이름을 *HelloWorld*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-129">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="5a94c-130">마우스 오른쪽 단추로 *Views/HelloWorld* 폴더, **추가 > 새 파일**을 차례로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-130">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="5a94c-131">**새 파일** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="5a94c-131">In the **New File** dialog:</span></span>

  * <span data-ttu-id="5a94c-132">왼쪽 창에서 **웹**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-132">Select **Web** in the left pane.</span></span>
  * <span data-ttu-id="5a94c-133">가운데 창에서 **빈 HTML 파일**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-133">Select **Empty HTML file** in the center pane.</span></span>
  * <span data-ttu-id="5a94c-134">**이름** 상자에 *Index.cshtml*을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-134">Type *Index.cshtml* in the **Name** box.</span></span>
  * <span data-ttu-id="5a94c-135">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-135">Select **New**.</span></span>

![새 항목 추가 대화 상자](adding-view/_static/add_view.png)

---

<span data-ttu-id="5a94c-137">*Views/HelloWorld/Index.cshtml* Razor 보기 파일의 콘텐츠를 다음으로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-137">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="5a94c-138">`https://localhost:xxxx/HelloWorld`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-138">Navigate to `https://localhost:xxxx/HelloWorld`.</span></span> <span data-ttu-id="5a94c-139">`HelloWorldController`에서 `Index` 메서드는 많은 작업을 수행하지 않았습니다. `return View();` 문을 실행했습니다. 해당 문은 메서드가 브라우저에 대한 응답을 렌더링하기 위해 보기 템플릿 파일을 사용해야 함을 지정했습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-139">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="5a94c-140">보기 템플릿 파일 이름을 지정하지 않았기 때문에 MVC는 기본적으로 기본 보기 파일을 사용하도록 설정되었습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-140">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="5a94c-141">기본 보기 파일은 메서드(`Index`)와 이름이 같으므로 */Views/HelloWorld/Index.cshtml*이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-141">The default view file has the same name as the method (`Index`), so in the */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="5a94c-142">아래 이미지는 보기에서 하드 코드된 “Hello from our View Template!”</span><span class="sxs-lookup"><span data-stu-id="5a94c-142">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="5a94c-143">문자열을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-143">hard-coded in the view.</span></span>

![브라우저 창](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="5a94c-145">보기 및 레이아웃 페이지 변경</span><span class="sxs-lookup"><span data-stu-id="5a94c-145">Change views and layout pages</span></span>

<span data-ttu-id="5a94c-146">메뉴 링크를 선택합니다(**MvcMovie**, **홈** 및 **개인 정보**).</span><span class="sxs-lookup"><span data-stu-id="5a94c-146">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="5a94c-147">각 페이지는 동일한 메뉴 레이아웃을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-147">Each page shows the same menu layout.</span></span> <span data-ttu-id="5a94c-148">메뉴 레이아웃은 *Views/Shared/_Layout.cshtml* 파일에서 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-148">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="5a94c-149">*Views/Shared/_Layout.cshtml* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-149">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="5a94c-150">[레이아웃](xref:mvc/views/layout) 템플릿을 사용하면 한 곳에서 사이트의 HTML 컨테이너 레이아웃을 지정한 다음 사이트에서 여러 페이지에 걸쳐 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-150">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="5a94c-151">`@RenderBody()` 줄을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-151">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="5a94c-152">`RenderBody`는 사용자가 만드는 모든 보기 전용 페이지가 표시되는 자리 표시자이며 레이아웃 페이지에서 *래핑됩니다*.</span><span class="sxs-lookup"><span data-stu-id="5a94c-152">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="5a94c-153">예를 들어 **개인 정보** 링크를 선택하는 경우 **Views/Home/Privacy.cshtml** 보기는 `RenderBody` 메서드 내에서 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-153">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="5a94c-154">레이아웃 파일의 제목, 바닥글 및 메뉴 링크 변경</span><span class="sxs-lookup"><span data-stu-id="5a94c-154">Change the title, footer, and menu link in the layout file</span></span>

<span data-ttu-id="5a94c-155">*Views\Shared\_Layout.cshtml* 파일의 내용을 다음 태그로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-155">Replace the content of the *Views\Shared\_Layout.cshtml* file with the following markup.</span></span> <span data-ttu-id="5a94c-156">변경 내용은 강조 표시되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-156">The changes are highlighted:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

<span data-ttu-id="5a94c-157">위에 태그에서는 다음과 같은 변경을 수행했습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-157">The preceding markup made the following changes:</span></span>

* <span data-ttu-id="5a94c-158">`MvcMovie` 3개를 `Movie App`으로 변경.</span><span class="sxs-lookup"><span data-stu-id="5a94c-158">3 occurrences of `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="5a94c-159">앵커 요소 `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>`를 `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`로 변경.</span><span class="sxs-lookup"><span data-stu-id="5a94c-159">The anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="5a94c-160">위의 태그에서 이 앱이 [Areas](xref:mvc/controllers/areas)를 사용하지 않기 때문에 `asp-area=""` [앵커 태그 도우미 특성](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) 및 특성 값이 생략되었습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-160">In the preceding markup, the `asp-area=""` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) and attribute value was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<span data-ttu-id="5a94c-161">**참고**: `Movies` 컨트롤러가 구현되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-161">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="5a94c-162">이 시점에서 `Movie App` 링크는 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-162">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="5a94c-163">변경 내용을 저장하고 **개인 정보** 링크를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-163">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="5a94c-164">브라우저 탭의 제목에서 **개인정보처리방침 - Mvc 동영상** 대신 **개인정보처리방침 - 동영상 앱**을 표시하는 방법을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-164">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![개인 정보 탭](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

<span data-ttu-id="5a94c-166">**홈** 링크를 선택하고 제목 및 앵커 텍스트가 **동영상 앱**을 표시하는지도 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-166">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="5a94c-167">레이아웃 템플릿에 변경 사항을 한 번 적용할 수 있었으며 사이트의 모든 페이지에 새 링크 텍스트 및 새 제목을 반영했습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-167">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="5a94c-168">*Views/_ViewStart.cshtml* 파일을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-168">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```HTML
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="5a94c-169">*Views/_ViewStart.cshtml* 파일에서 각 보기에 대한 *Views/Shared/_Layout.cshtml* 파일을 가져 옵니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-169">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="5a94c-170">`Layout` 속성을 사용하여 다른 레이아웃 보기를 설정하거나 레이아웃 파일을 사용하지 않도록 `null`로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-170">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="5a94c-171">*Views/HelloWorld/Index.cshtml* 보기 파일의 제목과 `<h2>` 요소를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-171">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="5a94c-172">제목과 `<h2>` 요소가 약간 다르기 때문에 어떤 코드가 표시를 변경하는지 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-172">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="5a94c-173">위의 코드에서 `ViewData["Title"] = "Movie List";`는 `ViewData` 사전의 `Title` 속성을 "Movie List"로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-173">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="5a94c-174">`Title` 속성은 레이아웃 페이지의 `<title>` HTML 요소에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-174">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```HTML
<title>@ViewData["Title"] - Movie App</title>
   ```

<span data-ttu-id="5a94c-175">변경 내용을 저장하고 `https://localhost:xxxx/HelloWorld`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-175">Save the change and navigate to `https://localhost:xxxx/HelloWorld`.</span></span> <span data-ttu-id="5a94c-176">브라우저 제목, 기본 제목 및 작은 제목이 변경된 것을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-176">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="5a94c-177">(브라우저에서 변경 내용을 확인할 수 없는 경우 캐시된 콘텐츠를 보고 있을 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-177">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="5a94c-178">브라우저에서 Ctrl+F5 키를 눌러 로드될 서버에서 응답을 강제로 적용합니다.) 브라우저 제목은 *Index.cshtml* 보기 템플릿에서 설정한 `ViewData["Title"]` 및 레이아웃 파일에 추가된 추가 "- Movie App"으로 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-178">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="5a94c-179">*Index.cshtml* 보기 템플릿에서 콘텐츠가 *Views/Shared/_Layout.cshtml* 보기 템플릿으로 병합되었습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-179">The content in the *Index.cshtml* view template is merged with the *Views/Shared/_Layout.cshtml* view template.</span></span> <span data-ttu-id="5a94c-180">단일 HTML 응답이 브라우저로 전송되었습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-180">A single HTML response is sent to the browser.</span></span> <span data-ttu-id="5a94c-181">레이아웃 템플릿을 사용하면 애플리케이션의 모든 페이지에 걸쳐 적용되는 변경 내용을 쉽게 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-181">Layout templates make it easy to make changes that apply across all of the pages in an app.</span></span> <span data-ttu-id="5a94c-182">자세한 내용은 [레이아웃](xref:mvc/views/layout)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a94c-182">To learn more, see [Layout](xref:mvc/views/layout).</span></span>

![동영상 목록 보기](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="5a94c-184">그렇지만 일부 “데이터”(이 경우 “Hello from our View Template!”</span><span class="sxs-lookup"><span data-stu-id="5a94c-184">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="5a94c-185">메시지)는 하드 코드되었습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-185">message) is hard-coded, though.</span></span> <span data-ttu-id="5a94c-186">MVC 애플리케이션에는 "V"(보기)가 있으며 "C"(컨트롤러)가 있지만 "M"(모델)은 아직 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-186">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="5a94c-187">컨트롤러에서 보기로 데이터 전달</span><span class="sxs-lookup"><span data-stu-id="5a94c-187">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="5a94c-188">컨트롤러 작업은 들어오는 URL 요청에 대한 응답으로 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-188">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="5a94c-189">컨트롤러 클래스는 들어오는 브라우저 요청을 처리하는 코드가 작성된 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-189">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="5a94c-190">컨트롤러는 데이터 원본에서 데이터를 검색하고 브라우저에 다시 전송할 응답의 유형을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-190">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="5a94c-191">보기 템플릿은 브라우저에 대한 HTML 응답을 만들고 서식 지정하도록 컨트롤러에서 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-191">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="5a94c-192">컨트롤러는 보기 템플릿에서 응답을 렌더링하기 위해 필요한 데이터를 제공할 책임이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-192">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="5a94c-193">모범 사례: 보기 템플릿은 비즈니스 논리를 수행하거나 데이터베이스와 직접 상호 작용하지 **않아야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-193">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="5a94c-194">대신 보기 템플릿은 컨트롤러에 의해 제공되는 데이터와만 작동해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-194">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="5a94c-195">이 "문제의 분리"를 유지 관리하면 코드를 깨끗하고, 테스트 가능하고 유지 관리 가능하게 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-195">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="5a94c-196">현재 `HelloWorldController` 클래스에서 `Welcome` 메서드는 `name` 및 `ID` 매개 변수를 가져온 다음 값을 브라우저로 직접 출력합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-196">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="5a94c-197">컨트롤러가 이 응답을 문자열로 렌더링하게 하는 대신, 보기 템플릿을 사용하도록 컨트롤러를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-197">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="5a94c-198">보기 템플릿은 응답을 생성합니다. 즉, 응답을 생성하기 위해 컨트롤러에서 보기로 일부 적절한 데이터를 전달해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-198">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="5a94c-199">보기 템플릿에서 액세스할 수 있도록 보기 템플릿이 `ViewData` 사전에서 필요한 동적 데이터(매개 변수)를 컨트롤러에서 배치하도록 하여 이를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-199">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="5a94c-200">*HelloWorldController.cs*에서 `Message` 및 `NumTimes` 값을 `ViewData` 사전에 추가하도록 `Welcome` 메서드를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-200">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="5a94c-201">`ViewData` 사전은 동적 개체로 어떤 유형이든 사용할 수 있음을 의미합니다. 어떤 것을 내부에 넣을 때까지 `ViewData` 개체에는 정의된 속성이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-201">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="5a94c-202">[MVC 모델 바인딩 시스템](xref:mvc/models/model-binding)은 주소 표시줄의 쿼리 문자열에서 메서드의 매개 변수로 명명된 매개 변수(`name` 및 `numTimes`)를 자동으로 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-202">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="5a94c-203">전체 *HelloWorldController.cs* 파일은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-203">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="5a94c-204">`ViewData` 사전 개체는 보기에 전달되는 데이터를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-204">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="5a94c-205">*Views/HelloWorld/Welcome.cshtml*이라는 시작 보기 템플릿을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-205">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="5a94c-206">"Hello" `NumTimes`를 표시하는 *Welcome.cshtml* 보기 템플릿에 반복을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-206">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="5a94c-207">*Views/HelloWorld/Welcome.cshtml*의 콘텐츠를 다음으로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-207">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="5a94c-208">변경 내용을 저장하고 다음 URL로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-208">Save your changes and browse to the following URL:</span></span>

`https://localhost:xxxx/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="5a94c-209">URL에서 데이터를 가져오고 [MVC 모델 바인더](xref:mvc/models/model-binding)를 사용하여 컨트롤러에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-209">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="5a94c-210">컨트롤러는 데이터를 `ViewData` 사전으로 패키징하고 해당 개체를 보기에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-210">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="5a94c-211">그런 다음 보기는 데이터를 HTML로 브라우저에 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-211">The view then renders the data as HTML to the browser.</span></span>

![시작 레이블과 네 번 표시되는 구 Hello Rick을 보여주는 개인 정보 보기](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="5a94c-213">위의 샘플에서 컨트롤러에서 보기로 데이터를 전달하는 데 `ViewData` 사전이 사용되었습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-213">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="5a94c-214">자습서의 뒷부분에서 보기 모델은 컨트롤러에서 보기로 데이터를 전달하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-214">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="5a94c-215">데이터를 전달하는 보기 모델 방법은 일반적으로 `ViewData` 사전 접근 방법보다 훨씬 많이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-215">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="5a94c-216">자세한 내용은 [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/)(ViewBag, ViewData 또는 TempData를 사용하는 경우)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a94c-216">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="5a94c-217">다음 자습서에서는 영화 데이터베이스가 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-217">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="5a94c-218">[이전](adding-controller.md)
> [다음](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="5a94c-218">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5a94c-219">이 섹션에서는 [Razor](xref:mvc/views/razor) 보기 파일을 사용하도록 `HelloWorldController` 클래스를 수정하여 클라이언트에 HTML 응답을 생성하는 프로세스를 명확하게 캡슐화합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-219">In this section you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="5a94c-220">Razor를 사용하여 뷰 템플릿 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-220">You create a view template file using Razor.</span></span> <span data-ttu-id="5a94c-221">Razor 기반 뷰 템플릿에는 *.cshtml* 파일 확장이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-221">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="5a94c-222">C#으로 HTML 출력을 만들 수 있는 세련된 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-222">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="5a94c-223">현재 `Index` 메서드는 컨트롤러 클래스에서 하드 코딩된 메시지 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-223">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="5a94c-224">`HelloWorldController` 클래스에서 `Index` 메서드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-224">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="5a94c-225">앞의 코드는 컨트롤러의 <xref:Microsoft.AspNetCore.Mvc.Controller.View*> 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-225">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="5a94c-226">HTML 응답을 생성하기 위해 뷰 템플릿을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-226">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="5a94c-227">위의 `Index` 메서드와 같은 컨트롤러 메서드(‘동작 메서드’라고도 함)는 일반적으로 `string`과 같은 형식이 아닌 <xref:Microsoft.AspNetCore.Mvc.IActionResult>(또는 <xref:Microsoft.AspNetCore.Mvc.ActionResult>에서 파생된 클래스)를 반환합니다. </span><span class="sxs-lookup"><span data-stu-id="5a94c-227">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="5a94c-228">보기 추가</span><span class="sxs-lookup"><span data-stu-id="5a94c-228">Add a view</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5a94c-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5a94c-229">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5a94c-230">마우스 오른쪽 단추로 *Views* 폴더, **추가 > 새 폴더**를 차례로 클릭하고 폴더의 이름을 *HelloWorld*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-230">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="5a94c-231">마우스 오른쪽 단추로 *Views/HelloWorld* 폴더, **추가 > 새 항목**을 차례로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-231">Right click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="5a94c-232">**새 항목 추가 - MvcMovie** 대화 상자에서</span><span class="sxs-lookup"><span data-stu-id="5a94c-232">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="5a94c-233">오른쪽 위의 검색 상자에서 *보기* 입력</span><span class="sxs-lookup"><span data-stu-id="5a94c-233">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="5a94c-234">**Razor 보기** 선택</span><span class="sxs-lookup"><span data-stu-id="5a94c-234">Select **Razor View**</span></span>

  * <span data-ttu-id="5a94c-235">*이름* 상자 값 **Index.cshtml**을 유지하세요.</span><span class="sxs-lookup"><span data-stu-id="5a94c-235">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="5a94c-236">**추가** 선택</span><span class="sxs-lookup"><span data-stu-id="5a94c-236">Select **Add**</span></span>

![새 항목 추가 대화 상자](adding-view/_static/add_view.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5a94c-238">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5a94c-238">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5a94c-239">`HelloWorldController`에 대한 `Index` 보기를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-239">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="5a94c-240">*Views/HelloWorld*라는 새 폴더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-240">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="5a94c-241">*Views/HelloWorld* 폴더에 새 파일을 추가하고 *Index.cshtml*의 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-241">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5a94c-242">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5a94c-242">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="5a94c-243">마우스 오른쪽 단추로 *Views* 폴더, **추가 > 새 폴더**를 차례로 클릭하고 폴더의 이름을 *HelloWorld*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-243">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="5a94c-244">마우스 오른쪽 단추로 *Views/HelloWorld* 폴더, **추가 > 새 파일**을 차례로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-244">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="5a94c-245">**새 파일** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="5a94c-245">In the **New File** dialog:</span></span>

  * <span data-ttu-id="5a94c-246">왼쪽 창에서 **웹**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-246">Select **Web** in the left pane.</span></span>
  * <span data-ttu-id="5a94c-247">가운데 창에서 **빈 HTML 파일**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-247">Select **Empty HTML file** in the center pane.</span></span>
  * <span data-ttu-id="5a94c-248">**이름** 상자에 *Index.cshtml*을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-248">Type *Index.cshtml* in the **Name** box.</span></span>
  * <span data-ttu-id="5a94c-249">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-249">Select **New**.</span></span>

![새 항목 추가 대화 상자](adding-view/_static/add_view.png)

---

<span data-ttu-id="5a94c-251">*Views/HelloWorld/Index.cshtml* Razor 보기 파일의 콘텐츠를 다음으로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-251">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="5a94c-252">`https://localhost:xxxx/HelloWorld`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-252">Navigate to `https://localhost:xxxx/HelloWorld`.</span></span> <span data-ttu-id="5a94c-253">`HelloWorldController`에서 `Index` 메서드는 많은 작업을 수행하지 않았습니다. `return View();` 문을 실행했습니다. 해당 문은 메서드가 브라우저에 대한 응답을 렌더링하기 위해 보기 템플릿 파일을 사용해야 함을 지정했습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-253">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="5a94c-254">보기 템플릿 파일 이름을 지정하지 않았기 때문에 MVC는 기본적으로 기본 보기 파일을 사용하도록 설정되었습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-254">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="5a94c-255">기본 보기 파일은 메서드(`Index`)와 이름이 같으므로 */Views/HelloWorld/Index.cshtml*이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-255">The default view file has the same name as the method (`Index`), so in the */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="5a94c-256">아래 이미지는 보기에서 하드 코드된 “Hello from our View Template!”</span><span class="sxs-lookup"><span data-stu-id="5a94c-256">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="5a94c-257">문자열을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-257">hard-coded in the view.</span></span>

![브라우저 창](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="5a94c-259">보기 및 레이아웃 페이지 변경</span><span class="sxs-lookup"><span data-stu-id="5a94c-259">Change views and layout pages</span></span>

<span data-ttu-id="5a94c-260">메뉴 링크를 선택합니다(**MvcMovie**, **홈** 및 **개인 정보**).</span><span class="sxs-lookup"><span data-stu-id="5a94c-260">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="5a94c-261">각 페이지는 동일한 메뉴 레이아웃을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-261">Each page shows the same menu layout.</span></span> <span data-ttu-id="5a94c-262">메뉴 레이아웃은 *Views/Shared/_Layout.cshtml* 파일에서 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-262">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="5a94c-263">*Views/Shared/_Layout.cshtml* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-263">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="5a94c-264">[레이아웃](xref:mvc/views/layout) 템플릿을 사용하면 한 곳에서 사이트의 HTML 컨테이너 레이아웃을 지정한 다음 사이트에서 여러 페이지에 걸쳐 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-264">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="5a94c-265">`@RenderBody()` 줄을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-265">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="5a94c-266">`RenderBody`는 사용자가 만드는 모든 보기 전용 페이지가 표시되는 자리 표시자이며 레이아웃 페이지에서 *래핑됩니다*.</span><span class="sxs-lookup"><span data-stu-id="5a94c-266">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="5a94c-267">예를 들어 **개인 정보** 링크를 선택하는 경우 **Views/Home/Privacy.cshtml** 보기는 `RenderBody` 메서드 내에서 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-267">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="5a94c-268">레이아웃 파일의 제목, 바닥글 및 메뉴 링크 변경</span><span class="sxs-lookup"><span data-stu-id="5a94c-268">Change the title, footer, and menu link in the layout file</span></span>

* <span data-ttu-id="5a94c-269">제목 및 바닥글 요소에서 `MvcMovie`를 `Movie App`으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-269">In the title and footer elements, change `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="5a94c-270">앵커 요소 `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>`를 `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-270">Change the anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="5a94c-271">다음 태그는 강조 표시된 변경 내용을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-271">The following markup shows the highlighted changes:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

<span data-ttu-id="5a94c-272">위의 태그에서 이 앱이 [Areas](xref:mvc/controllers/areas)를 사용하지 않기 때문에 `asp-area` [앵커 태그 도우미 특성](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)이 생략되었습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-272">In the preceding markup, the `asp-area` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

<span data-ttu-id="5a94c-273">**참고**: `Movies` 컨트롤러가 구현되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-273">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="5a94c-274">이 시점에서 `Movie App` 링크는 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-274">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="5a94c-275">변경 내용을 저장하고 **개인 정보** 링크를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-275">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="5a94c-276">브라우저 탭의 제목에서 **개인정보처리방침 - Mvc 동영상** 대신 **개인정보처리방침 - 동영상 앱**을 표시하는 방법을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-276">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![개인 정보 탭](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

<span data-ttu-id="5a94c-278">**홈** 링크를 선택하고 제목 및 앵커 텍스트가 **동영상 앱**을 표시하는지도 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-278">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="5a94c-279">레이아웃 템플릿에 변경 사항을 한 번 적용할 수 있었으며 사이트의 모든 페이지에 새 링크 텍스트 및 새 제목을 반영했습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-279">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="5a94c-280">*Views/_ViewStart.cshtml* 파일을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-280">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```HTML
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="5a94c-281">*Views/_ViewStart.cshtml* 파일에서 각 보기에 대한 *Views/Shared/_Layout.cshtml* 파일을 가져 옵니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-281">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="5a94c-282">`Layout` 속성을 사용하여 다른 레이아웃 보기를 설정하거나 레이아웃 파일을 사용하지 않도록 `null`로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-282">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="5a94c-283">*Views/HelloWorld/Index.cshtml* 보기 파일의 제목과 `<h2>` 요소를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-283">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="5a94c-284">제목과 `<h2>` 요소가 약간 다르기 때문에 어떤 코드가 표시를 변경하는지 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-284">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="5a94c-285">위의 코드에서 `ViewData["Title"] = "Movie List";`는 `ViewData` 사전의 `Title` 속성을 "Movie List"로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-285">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="5a94c-286">`Title` 속성은 레이아웃 페이지의 `<title>` HTML 요소에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-286">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```HTML
<title>@ViewData["Title"] - Movie App</title>
   ```

<span data-ttu-id="5a94c-287">변경 내용을 저장하고 `https://localhost:xxxx/HelloWorld`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-287">Save the change and navigate to `https://localhost:xxxx/HelloWorld`.</span></span> <span data-ttu-id="5a94c-288">브라우저 제목, 기본 제목 및 작은 제목이 변경된 것을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-288">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="5a94c-289">(브라우저에서 변경 내용을 확인할 수 없는 경우 캐시된 콘텐츠를 보고 있을 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-289">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="5a94c-290">브라우저에서 Ctrl+F5 키를 눌러 로드될 서버에서 응답을 강제로 적용합니다.) 브라우저 제목은 *Index.cshtml* 보기 템플릿에서 설정한 `ViewData["Title"]` 및 레이아웃 파일에 추가된 추가 "- Movie App"으로 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-290">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="5a94c-291">또한 *Index.cshtml* 보기 템플릿에서 콘텐츠가 *Views/Shared/_Layout.cshtml* 보기 템플릿으로 병합되고 단일 HTML 응답이 브라우저로 전송된 방식을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-291">Also notice how the content in the *Index.cshtml* view template was merged with the *Views/Shared/_Layout.cshtml* view template and a single HTML response was sent to the browser.</span></span> <span data-ttu-id="5a94c-292">레이아웃 템플릿을 사용하면 애플리케이션의 모든 페이지에 걸쳐 적용되는 변경 내용을 쉽게 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-292">Layout templates make it really easy to make changes that apply across all of the pages in your application.</span></span> <span data-ttu-id="5a94c-293">자세한 내용은 [레이아웃](xref:mvc/views/layout)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a94c-293">To learn more see [Layout](xref:mvc/views/layout).</span></span>

![동영상 목록 보기](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="5a94c-295">그렇지만 일부 “데이터”(이 경우 “Hello from our View Template!”</span><span class="sxs-lookup"><span data-stu-id="5a94c-295">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="5a94c-296">메시지)는 하드 코드되었습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-296">message) is hard-coded, though.</span></span> <span data-ttu-id="5a94c-297">MVC 애플리케이션에는 "V"(보기)가 있으며 "C"(컨트롤러)가 있지만 "M"(모델)은 아직 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-297">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="5a94c-298">컨트롤러에서 보기로 데이터 전달</span><span class="sxs-lookup"><span data-stu-id="5a94c-298">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="5a94c-299">컨트롤러 작업은 들어오는 URL 요청에 대한 응답으로 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-299">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="5a94c-300">컨트롤러 클래스는 들어오는 브라우저 요청을 처리하는 코드가 작성된 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-300">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="5a94c-301">컨트롤러는 데이터 원본에서 데이터를 검색하고 브라우저에 다시 전송할 응답의 유형을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-301">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="5a94c-302">보기 템플릿은 브라우저에 대한 HTML 응답을 만들고 서식 지정하도록 컨트롤러에서 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-302">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="5a94c-303">컨트롤러는 보기 템플릿에서 응답을 렌더링하기 위해 필요한 데이터를 제공할 책임이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-303">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="5a94c-304">모범 사례: 보기 템플릿은 비즈니스 논리를 수행하거나 데이터베이스와 직접 상호 작용하지 **않아야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-304">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="5a94c-305">대신 보기 템플릿은 컨트롤러에 의해 제공되는 데이터와만 작동해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-305">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="5a94c-306">이 "문제의 분리"를 유지 관리하면 코드를 깨끗하고, 테스트 가능하고 유지 관리 가능하게 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-306">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="5a94c-307">현재 `HelloWorldController` 클래스에서 `Welcome` 메서드는 `name` 및 `ID` 매개 변수를 가져온 다음 값을 브라우저로 직접 출력합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-307">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="5a94c-308">컨트롤러가 이 응답을 문자열로 렌더링하게 하는 대신, 보기 템플릿을 사용하도록 컨트롤러를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-308">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="5a94c-309">보기 템플릿은 응답을 생성합니다. 즉, 응답을 생성하기 위해 컨트롤러에서 보기로 일부 적절한 데이터를 전달해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-309">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="5a94c-310">보기 템플릿에서 액세스할 수 있도록 보기 템플릿이 `ViewData` 사전에서 필요한 동적 데이터(매개 변수)를 컨트롤러에서 배치하도록 하여 이를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-310">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="5a94c-311">*HelloWorldController.cs*에서 `Message` 및 `NumTimes` 값을 `ViewData` 사전에 추가하도록 `Welcome` 메서드를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-311">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="5a94c-312">`ViewData` 사전은 동적 개체로 어떤 유형이든 사용할 수 있음을 의미합니다. 어떤 것을 내부에 넣을 때까지 `ViewData` 개체에는 정의된 속성이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-312">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="5a94c-313">[MVC 모델 바인딩 시스템](xref:mvc/models/model-binding)은 주소 표시줄의 쿼리 문자열에서 메서드의 매개 변수로 명명된 매개 변수(`name` 및 `numTimes`)를 자동으로 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-313">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="5a94c-314">전체 *HelloWorldController.cs* 파일은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-314">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="5a94c-315">`ViewData` 사전 개체는 보기에 전달되는 데이터를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-315">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="5a94c-316">*Views/HelloWorld/Welcome.cshtml*이라는 시작 보기 템플릿을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-316">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="5a94c-317">"Hello" `NumTimes`를 표시하는 *Welcome.cshtml* 보기 템플릿에 반복을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-317">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="5a94c-318">*Views/HelloWorld/Welcome.cshtml*의 콘텐츠를 다음으로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-318">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="5a94c-319">변경 내용을 저장하고 다음 URL로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-319">Save your changes and browse to the following URL:</span></span>

`https://localhost:xxxx/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="5a94c-320">URL에서 데이터를 가져오고 [MVC 모델 바인더](xref:mvc/models/model-binding)를 사용하여 컨트롤러에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-320">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="5a94c-321">컨트롤러는 데이터를 `ViewData` 사전으로 패키징하고 해당 개체를 보기에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-321">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="5a94c-322">그런 다음 보기는 데이터를 HTML로 브라우저에 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-322">The view then renders the data as HTML to the browser.</span></span>

![시작 레이블과 네 번 표시되는 구 Hello Rick을 보여주는 개인 정보 보기](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="5a94c-324">위의 샘플에서 컨트롤러에서 보기로 데이터를 전달하는 데 `ViewData` 사전이 사용되었습니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-324">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="5a94c-325">자습서의 뒷부분에서 보기 모델은 컨트롤러에서 보기로 데이터를 전달하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-325">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="5a94c-326">데이터를 전달하는 보기 모델 방법은 일반적으로 `ViewData` 사전 접근 방법보다 훨씬 많이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-326">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="5a94c-327">자세한 내용은 [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/)(ViewBag, ViewData 또는 TempData를 사용하는 경우)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="5a94c-327">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="5a94c-328">다음 자습서에서는 영화 데이터베이스가 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="5a94c-328">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="5a94c-329">[이전](adding-controller.md)
> [다음](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="5a94c-329">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end