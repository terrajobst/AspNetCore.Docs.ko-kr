---
title: ASP.NET Core MVC 앱에 보기 추가
author: rick-anderson
description: 간단한 ASP.NET Core MVC 앱에 보기 추가
ms.author: riande
ms.date: 03/04/2017
uid: tutorials/first-mvc-app/adding-view
ms.openlocfilehash: c86e5aba6dbd1e39460cc3d2c266ce704282ee09
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815428"
---
# <a name="add-a-view-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="e4e18-103">ASP.NET Core MVC 앱에 보기 추가</span><span class="sxs-lookup"><span data-stu-id="e4e18-103">Add a view to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="e4e18-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e4e18-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e4e18-105">이 섹션에서는 [Razor](xref:mvc/views/razor) 보기 파일을 사용하도록 `HelloWorldController` 클래스를 수정하여 클라이언트에 HTML 응답을 생성하는 프로세스를 명확하게 캡슐화합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-105">In this section you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="e4e18-106">Razor를 사용하여 뷰 템플릿 파일을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-106">You create a view template file using Razor.</span></span> <span data-ttu-id="e4e18-107">Razor 기반 뷰 템플릿에는 *.cshtml* 파일 확장이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-107">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="e4e18-108">C#으로 HTML 출력을 만들 수 있는 세련된 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-108">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="e4e18-109">현재 `Index` 메서드는 컨트롤러 클래스에서 하드 코딩된 메시지 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-109">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="e4e18-110">`HelloWorldController` 클래스에서 `Index` 메서드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-110">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="e4e18-111">앞의 코드는 컨트롤러의 <xref:Microsoft.AspNetCore.Mvc.Controller.View*> 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-111">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="e4e18-112">HTML 응답을 생성하기 위해 뷰 템플릿을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-112">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="e4e18-113">위의 `Index` 메서드와 같은 컨트롤러 메서드(‘동작 메서드’라고도 함)는 일반적으로 `string`과 같은 형식이 아닌 <xref:Microsoft.AspNetCore.Mvc.IActionResult>(또는 <xref:Microsoft.AspNetCore.Mvc.ActionResult>에서 파생된 클래스)를 반환합니다. </span><span class="sxs-lookup"><span data-stu-id="e4e18-113">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="e4e18-114">보기 추가</span><span class="sxs-lookup"><span data-stu-id="e4e18-114">Add a view</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e4e18-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e4e18-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e4e18-116">마우스 오른쪽 단추로 *Views* 폴더, **추가 > 새 폴더**를 차례로 클릭하고 폴더의 이름을 *HelloWorld*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-116">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="e4e18-117">마우스 오른쪽 단추로 *Views/HelloWorld* 폴더, **추가 > 새 항목**을 차례로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-117">Right click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="e4e18-118">**새 항목 추가 - MvcMovie** 대화 상자에서</span><span class="sxs-lookup"><span data-stu-id="e4e18-118">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="e4e18-119">오른쪽 위의 검색 상자에서 *보기* 입력</span><span class="sxs-lookup"><span data-stu-id="e4e18-119">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="e4e18-120">**Razor 보기** 선택</span><span class="sxs-lookup"><span data-stu-id="e4e18-120">Select **Razor View**</span></span>

  * <span data-ttu-id="e4e18-121">*이름* 상자 값 **Index.cshtml**을 유지하세요.</span><span class="sxs-lookup"><span data-stu-id="e4e18-121">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="e4e18-122">**추가** 선택</span><span class="sxs-lookup"><span data-stu-id="e4e18-122">Select **Add**</span></span>

![새 항목 추가 대화 상자](adding-view/_static/add_view.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e4e18-124">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e4e18-124">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e4e18-125">`HelloWorldController`에 대한 `Index` 보기를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-125">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="e4e18-126">*Views/HelloWorld*라는 새 폴더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-126">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="e4e18-127">*Views/HelloWorld* 폴더에 새 파일을 추가하고 *Index.cshtml*의 이름을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-127">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e4e18-128">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="e4e18-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e4e18-129">마우스 오른쪽 단추로 *Views* 폴더, **추가 > 새 폴더**를 차례로 클릭하고 폴더의 이름을 *HelloWorld*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-129">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="e4e18-130">마우스 오른쪽 단추로 *Views/HelloWorld* 폴더, **추가 > 새 파일**을 차례로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-130">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="e4e18-131">**새 파일** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="e4e18-131">In the **New File** dialog:</span></span>

  * <span data-ttu-id="e4e18-132">왼쪽 창에서 **웹**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-132">Select **Web** in the left pane.</span></span>
  * <span data-ttu-id="e4e18-133">가운데 창에서 **빈 HTML 파일**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-133">Select **Empty HTML file** in the center pane.</span></span>
  * <span data-ttu-id="e4e18-134">**이름** 상자에 *Index.cshtml*을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-134">Type *Index.cshtml* in the **Name** box.</span></span>
  * <span data-ttu-id="e4e18-135">**새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-135">Select **New**.</span></span>

![새 항목 추가 대화 상자](adding-view/_static/add_view.png)

---

<span data-ttu-id="e4e18-137">*Views/HelloWorld/Index.cshtml* Razor 보기 파일의 콘텐츠를 다음으로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-137">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="e4e18-138">`https://localhost:xxxx/HelloWorld`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-138">Navigate to `https://localhost:xxxx/HelloWorld`.</span></span> <span data-ttu-id="e4e18-139">`HelloWorldController`에서 `Index` 메서드는 많은 작업을 수행하지 않았습니다. `return View();` 문을 실행했습니다. 해당 문은 메서드가 브라우저에 대한 응답을 렌더링하기 위해 보기 템플릿 파일을 사용해야 함을 지정했습니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-139">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="e4e18-140">보기 템플릿 파일 이름을 지정하지 않았기 때문에 MVC는 기본적으로 기본 보기 파일을 사용하도록 설정되었습니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-140">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="e4e18-141">기본 보기 파일은 메서드(`Index`)와 이름이 같으므로 */Views/HelloWorld/Index.cshtml*이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-141">The default view file has the same name as the method (`Index`), so in the */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="e4e18-142">아래 이미지는 보기에서 하드 코드된 “Hello from our View Template!”</span><span class="sxs-lookup"><span data-stu-id="e4e18-142">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="e4e18-143">문자열을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-143">hard-coded in the view.</span></span>

![브라우저 창](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="e4e18-145">보기 및 레이아웃 페이지 변경</span><span class="sxs-lookup"><span data-stu-id="e4e18-145">Change views and layout pages</span></span>

<span data-ttu-id="e4e18-146">메뉴 링크를 선택합니다(**MvcMovie**, **홈** 및 **개인 정보**).</span><span class="sxs-lookup"><span data-stu-id="e4e18-146">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="e4e18-147">각 페이지는 동일한 메뉴 레이아웃을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-147">Each page shows the same menu layout.</span></span> <span data-ttu-id="e4e18-148">메뉴 레이아웃은 *Views/Shared/_Layout.cshtml* 파일에서 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-148">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="e4e18-149">*Views/Shared/_Layout.cshtml* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-149">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="e4e18-150">[레이아웃](xref:mvc/views/layout) 템플릿을 사용하면 한 곳에서 사이트의 HTML 컨테이너 레이아웃을 지정한 다음 사이트에서 여러 페이지에 걸쳐 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-150">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="e4e18-151">`@RenderBody()` 줄을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-151">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="e4e18-152">`RenderBody`는 사용자가 만드는 모든 보기 전용 페이지가 표시되는 자리 표시자이며 레이아웃 페이지에서 *래핑됩니다*.</span><span class="sxs-lookup"><span data-stu-id="e4e18-152">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="e4e18-153">예를 들어 **개인 정보** 링크를 선택하는 경우 **Views/Home/Privacy.cshtml** 보기는 `RenderBody` 메서드 내에서 렌더링됩니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-153">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="e4e18-154">레이아웃 파일의 제목, 바닥글 및 메뉴 링크 변경</span><span class="sxs-lookup"><span data-stu-id="e4e18-154">Change the title, footer, and menu link in the layout file</span></span>

* <span data-ttu-id="e4e18-155">제목 및 바닥글 요소에서 `MvcMovie`를 `Movie App`으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-155">In the title and footer elements, change `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="e4e18-156">앵커 요소 `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>`를 `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`으로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-156">Change the anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="e4e18-157">다음 태그는 강조 표시된 변경 내용을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-157">The following markup shows the highlighted changes:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

<span data-ttu-id="e4e18-158">위의 태그에서 이 앱이 [Areas](xref:mvc/controllers/areas)를 사용하지 않기 때문에 `asp-area` [앵커 태그 도우미 특성](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)이 생략되었습니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-158">In the preceding markup, the `asp-area` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

<span data-ttu-id="e4e18-159">**참고**: `Movies` 컨트롤러가 구현되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-159">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="e4e18-160">이 시점에서 `Movie App` 링크는 작동하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-160">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="e4e18-161">변경 내용을 저장하고 **개인 정보** 링크를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-161">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="e4e18-162">브라우저 탭의 제목에서 **개인정보처리방침 - Mvc 동영상** 대신 **개인정보처리방침 - 동영상 앱**을 표시하는 방법을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-162">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![개인 정보 탭](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

<span data-ttu-id="e4e18-164">**홈** 링크를 선택하고 제목 및 앵커 텍스트가 **동영상 앱**을 표시하는지도 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-164">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="e4e18-165">레이아웃 템플릿에 변경 사항을 한 번 적용할 수 있었으며 사이트의 모든 페이지에 새 링크 텍스트 및 새 제목을 반영했습니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-165">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="e4e18-166">*Views/_ViewStart.cshtml* 파일을 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-166">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```HTML
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="e4e18-167">*Views/_ViewStart.cshtml* 파일에서 각 보기에 대한 *Views/Shared/_Layout.cshtml* 파일을 가져 옵니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-167">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="e4e18-168">`Layout` 속성을 사용하여 다른 레이아웃 보기를 설정하거나 레이아웃 파일을 사용하지 않도록 `null`로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-168">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="e4e18-169">*Views/HelloWorld/Index.cshtml* 보기 파일의 제목과 `<h2>` 요소를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-169">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="e4e18-170">제목과 `<h2>` 요소가 약간 다르기 때문에 어떤 코드가 표시를 변경하는지 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-170">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="e4e18-171">위의 코드에서 `ViewData["Title"] = "Movie List";`는 `ViewData` 사전의 `Title` 속성을 "Movie List"로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-171">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="e4e18-172">`Title` 속성은 레이아웃 페이지의 `<title>` HTML 요소에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-172">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```HTML
<title>@ViewData["Title"] - Movie App</title>
   ```

<span data-ttu-id="e4e18-173">변경 내용을 저장하고 `https://localhost:xxxx/HelloWorld`로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-173">Save the change and navigate to `https://localhost:xxxx/HelloWorld`.</span></span> <span data-ttu-id="e4e18-174">브라우저 제목, 기본 제목 및 작은 제목이 변경된 것을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-174">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="e4e18-175">(브라우저에서 변경 내용을 확인할 수 없는 경우 캐시된 콘텐츠를 보고 있을 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-175">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="e4e18-176">브라우저에서 Ctrl+F5 키를 눌러 로드될 서버에서 응답을 강제로 적용합니다.) 브라우저 제목은 *Index.cshtml* 보기 템플릿에서 설정한 `ViewData["Title"]` 및 레이아웃 파일에 추가된 추가 "- Movie App"으로 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-176">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="e4e18-177">또한 *Index.cshtml* 보기 템플릿에서 콘텐츠가 *Views/Shared/_Layout.cshtml* 보기 템플릿으로 병합되고 단일 HTML 응답이 브라우저로 전송된 방식을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-177">Also notice how the content in the *Index.cshtml* view template was merged with the *Views/Shared/_Layout.cshtml* view template and a single HTML response was sent to the browser.</span></span> <span data-ttu-id="e4e18-178">레이아웃 템플릿을 사용하면 애플리케이션의 모든 페이지에 걸쳐 적용되는 변경 내용을 쉽게 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-178">Layout templates make it really easy to make changes that apply across all of the pages in your application.</span></span> <span data-ttu-id="e4e18-179">자세한 내용은 [레이아웃](xref:mvc/views/layout)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e4e18-179">To learn more see [Layout](xref:mvc/views/layout).</span></span>

![동영상 목록 보기](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="e4e18-181">그렇지만 일부 “데이터”(이 경우 “Hello from our View Template!”</span><span class="sxs-lookup"><span data-stu-id="e4e18-181">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="e4e18-182">메시지)는 하드 코드되었습니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-182">message) is hard-coded, though.</span></span> <span data-ttu-id="e4e18-183">MVC 애플리케이션에는 "V"(보기)가 있으며 "C"(컨트롤러)가 있지만 "M"(모델)은 아직 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-183">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="e4e18-184">컨트롤러에서 보기로 데이터 전달</span><span class="sxs-lookup"><span data-stu-id="e4e18-184">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="e4e18-185">컨트롤러 작업은 들어오는 URL 요청에 대한 응답으로 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-185">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="e4e18-186">컨트롤러 클래스는 들어오는 브라우저 요청을 처리하는 코드가 작성된 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-186">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="e4e18-187">컨트롤러는 데이터 원본에서 데이터를 검색하고 브라우저에 다시 전송할 응답의 유형을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-187">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="e4e18-188">보기 템플릿은 브라우저에 대한 HTML 응답을 만들고 서식 지정하도록 컨트롤러에서 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-188">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="e4e18-189">컨트롤러는 보기 템플릿에서 응답을 렌더링하기 위해 필요한 데이터를 제공할 책임이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-189">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="e4e18-190">모범 사례: 보기 템플릿은 비즈니스 논리를 수행하거나 데이터베이스와 직접 상호 작용하지 **않아야** 합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-190">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="e4e18-191">대신 보기 템플릿은 컨트롤러에 의해 제공되는 데이터와만 작동해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-191">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="e4e18-192">이 "문제의 분리"를 유지 관리하면 코드를 깨끗하고, 테스트 가능하고 유지 관리 가능하게 유지할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-192">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="e4e18-193">현재 `HelloWorldController` 클래스에서 `Welcome` 메서드는 `name` 및 `ID` 매개 변수를 가져온 다음 값을 브라우저로 직접 출력합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-193">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="e4e18-194">컨트롤러가 이 응답을 문자열로 렌더링하게 하는 대신, 보기 템플릿을 사용하도록 컨트롤러를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-194">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="e4e18-195">보기 템플릿은 응답을 생성합니다. 즉, 응답을 생성하기 위해 컨트롤러에서 보기로 일부 적절한 데이터를 전달해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-195">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="e4e18-196">보기 템플릿에서 액세스할 수 있도록 보기 템플릿이 `ViewData` 사전에서 필요한 동적 데이터(매개 변수)를 컨트롤러에서 배치하도록 하여 이를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-196">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="e4e18-197">*HelloWorldController.cs*에서 `Message` 및 `NumTimes` 값을 `ViewData` 사전에 추가하도록 `Welcome` 메서드를 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-197">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="e4e18-198">`ViewData` 사전은 동적 개체로 어떤 유형이든 사용할 수 있음을 의미합니다. 어떤 것을 내부에 넣을 때까지 `ViewData` 개체에는 정의된 속성이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-198">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="e4e18-199">[MVC 모델 바인딩 시스템](xref:mvc/models/model-binding)은 주소 표시줄의 쿼리 문자열에서 메서드의 매개 변수로 명명된 매개 변수(`name` 및 `numTimes`)를 자동으로 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-199">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="e4e18-200">전체 *HelloWorldController.cs* 파일은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-200">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="e4e18-201">`ViewData` 사전 개체는 보기에 전달되는 데이터를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-201">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="e4e18-202">*Views/HelloWorld/Welcome.cshtml*이라는 시작 보기 템플릿을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-202">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="e4e18-203">"Hello" `NumTimes`를 표시하는 *Welcome.cshtml* 보기 템플릿에 반복을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-203">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="e4e18-204">*Views/HelloWorld/Welcome.cshtml*의 콘텐츠를 다음으로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-204">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="e4e18-205">변경 내용을 저장하고 다음 URL로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-205">Save your changes and browse to the following URL:</span></span>

`https://localhost:xxxx/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="e4e18-206">URL에서 데이터를 가져오고 [MVC 모델 바인더](xref:mvc/models/model-binding)를 사용하여 컨트롤러에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-206">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="e4e18-207">컨트롤러는 데이터를 `ViewData` 사전으로 패키징하고 해당 개체를 보기에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-207">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="e4e18-208">그런 다음 보기는 데이터를 HTML로 브라우저에 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-208">The view then renders the data as HTML to the browser.</span></span>

![시작 레이블과 네 번 표시되는 구 Hello Rick을 보여주는 개인 정보 보기](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="e4e18-210">위의 샘플에서 컨트롤러에서 보기로 데이터를 전달하는 데 `ViewData` 사전이 사용되었습니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-210">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="e4e18-211">자습서의 뒷부분에서 보기 모델은 컨트롤러에서 보기로 데이터를 전달하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-211">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="e4e18-212">데이터를 전달하는 보기 모델 방법은 일반적으로 `ViewData` 사전 접근 방법보다 훨씬 많이 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-212">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="e4e18-213">자세한 내용은 [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/)(ViewBag, ViewData 또는 TempData를 사용하는 경우)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e4e18-213">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="e4e18-214">다음 자습서에서는 영화 데이터베이스가 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="e4e18-214">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="e4e18-215">[이전](adding-controller.md)
> [다음](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="e4e18-215">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>
