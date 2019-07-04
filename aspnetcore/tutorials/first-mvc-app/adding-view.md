---
title: ASP.NET Core MVC 앱에 보기 추가
author: rick-anderson
description: 간단한 ASP.NET Core MVC 앱에 보기 추가
ms.author: riande
ms.date: 03/04/2017
uid: tutorials/first-mvc-app/adding-view
ms.openlocfilehash: 70c96b4838b37ea2c193c328f00a410642338369
ms.sourcegitcommit: 28a2874765cefe9eaa068dceb989a978ba2096aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67167076"
---
# <a name="add-a-view-to-an-aspnet-core-mvc-app"></a>ASP.NET Core MVC 앱에 보기 추가

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

이 섹션에서는 [Razor](xref:mvc/views/razor) 보기 파일을 사용하도록 `HelloWorldController` 클래스를 수정하여 클라이언트에 HTML 응답을 생성하는 프로세스를 명확하게 캡슐화합니다.

Razor를 사용하여 뷰 템플릿 파일을 만듭니다. Razor 기반 뷰 템플릿에는 *.cshtml* 파일 확장이 있습니다. C#으로 HTML 출력을 만들 수 있는 세련된 방법을 제공합니다.

현재 `Index` 메서드는 컨트롤러 클래스에서 하드 코딩된 메시지 문자열을 반환합니다. `HelloWorldController` 클래스에서 `Index` 메서드를 다음 코드로 바꿉니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

앞의 코드는 컨트롤러의 <xref:Microsoft.AspNetCore.Mvc.Controller.View*> 메서드를 호출합니다. HTML 응답을 생성하기 위해 뷰 템플릿을 사용합니다. 위의 `Index` 메서드와 같은 컨트롤러 메서드(‘동작 메서드’라고도 함)는 일반적으로 `string`과 같은 형식이 아닌 <xref:Microsoft.AspNetCore.Mvc.IActionResult>(또는 <xref:Microsoft.AspNetCore.Mvc.ActionResult>에서 파생된 클래스)를 반환합니다. 

## <a name="add-a-view"></a>보기 추가

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 마우스 오른쪽 단추로 *Views* 폴더, **추가 > 새 폴더**를 차례로 클릭하고 폴더의 이름을 *HelloWorld*로 지정합니다.

* 마우스 오른쪽 단추로 *Views/HelloWorld* 폴더, **추가 > 새 항목**을 차례로 클릭합니다.

* **새 항목 추가 - MvcMovie** 대화 상자에서

  * 오른쪽 위의 검색 상자에서 *보기* 입력

  * **Razor 보기** 선택

  * *이름* 상자 값 **Index.cshtml**을 유지하세요.

  * **추가** 선택

![새 항목 추가 대화 상자](adding-view/_static/add_view.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

`HelloWorldController`에 대한 `Index` 보기를 추가합니다.

* *Views/HelloWorld*라는 새 폴더를 추가합니다.
* *Views/HelloWorld* 폴더에 새 파일을 추가하고 *Index.cshtml*의 이름을 지정합니다.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 마우스 오른쪽 단추로 *Views* 폴더, **추가 > 새 폴더**를 차례로 클릭하고 폴더의 이름을 *HelloWorld*로 지정합니다.
* 마우스 오른쪽 단추로 *Views/HelloWorld* 폴더, **추가 > 새 파일**을 차례로 클릭합니다.
* **새 파일** 대화 상자에서:

  * 왼쪽 창에서 **웹**을 선택합니다.
  * 가운데 창에서 **빈 HTML 파일**을 선택합니다.
  * **이름** 상자에 *Index.cshtml*을 입력합니다.
  * **새로 만들기**를 선택합니다.

![새 항목 추가 대화 상자](adding-view/_static/add_view.png)

---

*Views/HelloWorld/Index.cshtml* Razor 보기 파일의 콘텐츠를 다음으로 대체합니다.

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

`https://localhost:xxxx/HelloWorld`로 이동합니다. `HelloWorldController`에서 `Index` 메서드는 많은 작업을 수행하지 않았습니다. `return View();` 문을 실행했습니다. 해당 문은 메서드가 브라우저에 대한 응답을 렌더링하기 위해 보기 템플릿 파일을 사용해야 함을 지정했습니다. 보기 템플릿 파일 이름을 지정하지 않았기 때문에 MVC는 기본적으로 기본 보기 파일을 사용하도록 설정되었습니다. 기본 보기 파일은 메서드(`Index`)와 이름이 같으므로 */Views/HelloWorld/Index.cshtml*이 사용됩니다. 아래 이미지는 보기에서 하드 코드된 “Hello from our View Template!” 문자열을 보여줍니다.

![브라우저 창](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a>보기 및 레이아웃 페이지 변경

메뉴 링크를 선택합니다(**MvcMovie**, **홈** 및 **개인 정보**). 각 페이지는 동일한 메뉴 레이아웃을 표시합니다. 메뉴 레이아웃은 *Views/Shared/_Layout.cshtml* 파일에서 구현됩니다. *Views/Shared/_Layout.cshtml* 파일을 엽니다.

[레이아웃](xref:mvc/views/layout) 템플릿을 사용하면 한 곳에서 사이트의 HTML 컨테이너 레이아웃을 지정한 다음 사이트에서 여러 페이지에 걸쳐 적용할 수 있습니다. `@RenderBody()` 줄을 찾습니다. `RenderBody`는 사용자가 만드는 모든 보기 전용 페이지가 표시되는 자리 표시자이며 레이아웃 페이지에서 *래핑됩니다*. 예를 들어 **개인 정보** 링크를 선택하는 경우 **Views/Home/Privacy.cshtml** 보기는 `RenderBody` 메서드 내에서 렌더링됩니다.

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a>레이아웃 파일의 제목, 바닥글 및 메뉴 링크 변경

* 제목 및 바닥글 요소에서 `MvcMovie`를 `Movie App`으로 변경합니다.
* 앵커 요소 `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>`를 `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`으로 변경합니다.

다음 태그는 강조 표시된 변경 내용을 표시합니다.

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

위의 태그에서 이 앱이 [Areas](xref:mvc/controllers/areas)를 사용하지 않기 때문에 `asp-area` [앵커 태그 도우미 특성](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)이 생략되었습니다.

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

**참고**: `Movies` 컨트롤러가 구현되지 않았습니다. 이 시점에서 `Movie App` 링크는 작동하지 않습니다.

변경 내용을 저장하고 **개인 정보** 링크를 선택합니다. 브라우저 탭의 제목에서 **개인정보처리방침 - Mvc 동영상** 대신 **개인정보처리방침 - 동영상 앱**을 표시하는 방법을 확인합니다.

![개인 정보 탭](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

**홈** 링크를 선택하고 제목 및 앵커 텍스트가 **동영상 앱**을 표시하는지도 확인합니다. 레이아웃 템플릿에 변경 사항을 한 번 적용할 수 있었으며 사이트의 모든 페이지에 새 링크 텍스트 및 새 제목을 반영했습니다.

*Views/_ViewStart.cshtml* 파일을 검사합니다.

```HTML
@{
    Layout = "_Layout";
}
```

*Views/_ViewStart.cshtml* 파일에서 각 보기에 대한 *Views/Shared/_Layout.cshtml* 파일을 가져 옵니다. `Layout` 속성을 사용하여 다른 레이아웃 보기를 설정하거나 레이아웃 파일을 사용하지 않도록 `null`로 설정할 수 있습니다.

*Views/HelloWorld/Index.cshtml* 보기 파일의 제목과 `<h2>` 요소를 변경합니다.

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

제목과 `<h2>` 요소가 약간 다르기 때문에 어떤 코드가 표시를 변경하는지 확인할 수 있습니다.

위의 코드에서 `ViewData["Title"] = "Movie List";`는 `ViewData` 사전의 `Title` 속성을 "Movie List"로 설정합니다. `Title` 속성은 레이아웃 페이지의 `<title>` HTML 요소에서 사용됩니다.

```HTML
<title>@ViewData["Title"] - Movie App</title>
   ```

변경 내용을 저장하고 `https://localhost:xxxx/HelloWorld`로 이동합니다. 브라우저 제목, 기본 제목 및 작은 제목이 변경된 것을 확인합니다. (브라우저에서 변경 내용을 확인할 수 없는 경우 캐시된 콘텐츠를 보고 있을 수도 있습니다. 브라우저에서 Ctrl+F5 키를 눌러 로드될 서버에서 응답을 강제로 적용합니다.) 브라우저 제목은 *Index.cshtml* 보기 템플릿에서 설정한 `ViewData["Title"]` 및 레이아웃 파일에 추가된 추가 "- Movie App"으로 만들어집니다.

또한 *Index.cshtml* 보기 템플릿에서 콘텐츠가 *Views/Shared/_Layout.cshtml* 보기 템플릿으로 병합되고 단일 HTML 응답이 브라우저로 전송된 방식을 확인합니다. 레이아웃 템플릿을 사용하면 애플리케이션의 모든 페이지에 걸쳐 적용되는 변경 내용을 쉽게 만들 수 있습니다. 자세한 내용은 [레이아웃](xref:mvc/views/layout)을 참조하세요.

![동영상 목록 보기](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

그렇지만 일부 “데이터”(이 경우 “Hello from our View Template!” 메시지)는 하드 코드되었습니다. MVC 애플리케이션에는 "V"(보기)가 있으며 "C"(컨트롤러)가 있지만 "M"(모델)은 아직 없습니다.

## <a name="passing-data-from-the-controller-to-the-view"></a>컨트롤러에서 보기로 데이터 전달

컨트롤러 작업은 들어오는 URL 요청에 대한 응답으로 호출됩니다. 컨트롤러 클래스는 들어오는 브라우저 요청을 처리하는 코드가 작성된 위치입니다. 컨트롤러는 데이터 원본에서 데이터를 검색하고 브라우저에 다시 전송할 응답의 유형을 결정합니다. 보기 템플릿은 브라우저에 대한 HTML 응답을 만들고 서식 지정하도록 컨트롤러에서 사용될 수 있습니다.

컨트롤러는 보기 템플릿에서 응답을 렌더링하기 위해 필요한 데이터를 제공할 책임이 있습니다. 모범 사례: 보기 템플릿은 비즈니스 논리를 수행하거나 데이터베이스와 직접 상호 작용하지 **않아야** 합니다. 대신 보기 템플릿은 컨트롤러에 의해 제공되는 데이터와만 작동해야 합니다. 이 "문제의 분리"를 유지 관리하면 코드를 깨끗하고, 테스트 가능하고 유지 관리 가능하게 유지할 수 있습니다.

현재 `HelloWorldController` 클래스에서 `Welcome` 메서드는 `name` 및 `ID` 매개 변수를 가져온 다음 값을 브라우저로 직접 출력합니다. 컨트롤러가 이 응답을 문자열로 렌더링하게 하는 대신, 보기 템플릿을 사용하도록 컨트롤러를 변경합니다. 보기 템플릿은 응답을 생성합니다. 즉, 응답을 생성하기 위해 컨트롤러에서 보기로 일부 적절한 데이터를 전달해야 합니다. 보기 템플릿에서 액세스할 수 있도록 보기 템플릿이 `ViewData` 사전에서 필요한 동적 데이터(매개 변수)를 컨트롤러에서 배치하도록 하여 이를 수행합니다.

*HelloWorldController.cs*에서 `Message` 및 `NumTimes` 값을 `ViewData` 사전에 추가하도록 `Welcome` 메서드를 변경합니다. `ViewData` 사전은 동적 개체로 어떤 유형이든 사용할 수 있음을 의미합니다. 어떤 것을 내부에 넣을 때까지 `ViewData` 개체에는 정의된 속성이 없습니다. [MVC 모델 바인딩 시스템](xref:mvc/models/model-binding)은 주소 표시줄의 쿼리 문자열에서 메서드의 매개 변수로 명명된 매개 변수(`name` 및 `numTimes`)를 자동으로 매핑합니다. 전체 *HelloWorldController.cs* 파일은 다음과 같습니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

`ViewData` 사전 개체는 보기에 전달되는 데이터를 포함합니다.

*Views/HelloWorld/Welcome.cshtml*이라는 시작 보기 템플릿을 만듭니다.

"Hello" `NumTimes`를 표시하는 *Welcome.cshtml* 보기 템플릿에 반복을 만듭니다. *Views/HelloWorld/Welcome.cshtml*의 콘텐츠를 다음으로 대체합니다.

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

변경 내용을 저장하고 다음 URL로 이동합니다.

`https://localhost:xxxx/HelloWorld/Welcome?name=Rick&numtimes=4`

URL에서 데이터를 가져오고 [MVC 모델 바인더](xref:mvc/models/model-binding)를 사용하여 컨트롤러에 전달됩니다. 컨트롤러는 데이터를 `ViewData` 사전으로 패키징하고 해당 개체를 보기에 전달합니다. 그런 다음 보기는 데이터를 HTML로 브라우저에 렌더링합니다.

![시작 레이블과 네 번 표시되는 구 Hello Rick을 보여주는 개인 정보 보기](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

위의 샘플에서 컨트롤러에서 보기로 데이터를 전달하는 데 `ViewData` 사전이 사용되었습니다. 자습서의 뒷부분에서 보기 모델은 컨트롤러에서 보기로 데이터를 전달하는 데 사용됩니다. 데이터를 전달하는 보기 모델 방법은 일반적으로 `ViewData` 사전 접근 방법보다 훨씬 많이 사용됩니다. 자세한 내용은 [When to use ViewBag, ViewData, or TempData](http://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/)(ViewBag, ViewData 또는 TempData를 사용하는 경우)를 참조하세요.

다음 자습서에서는 영화 데이터베이스가 만들어집니다.

> [!div class="step-by-step"]
> [이전](adding-controller.md)
> [다음](adding-model.md)
