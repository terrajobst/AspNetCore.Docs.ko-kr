---
title: ASP.NET Core MVC 앱에 컨트롤러 추가
author: rick-anderson
description: 간단한 ASP.NET Core MVC 앱에 컨트롤러를 추가하는 방법을 배웁니다.
ms.author: riande
ms.date: 08/05/2017
uid: tutorials/first-mvc-app/adding-controller
ms.openlocfilehash: fb670902b0dafa7dce2b3372e550095387844936
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78650775"
---
# <a name="add-a-controller-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="56a5a-103">ASP.NET Core MVC 앱에 컨트롤러 추가</span><span class="sxs-lookup"><span data-stu-id="56a5a-103">Add a controller to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="56a5a-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="56a5a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="56a5a-105">MVC( Model-View-Controller) 아키텍처 패턴은 다음 세 가지 주요 구성 요소로 앱을 구분합니다. **M**odel, **V**iew 및 **C**ontroller.</span><span class="sxs-lookup"><span data-stu-id="56a5a-105">The Model-View-Controller (MVC) architectural pattern separates an app into three main components: **M**odel, **V**iew, and **C**ontroller.</span></span> <span data-ttu-id="56a5a-106">MVC 패턴을 통해 기존 모놀리식 응용 프로그램보다 쉽게 테스트 가능하고 업데이트할 수 있는 앱을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-106">The MVC pattern helps you create apps that are more testable and easier to update than traditional monolithic apps.</span></span> <span data-ttu-id="56a5a-107">MVC 기반 앱에는 다음이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-107">MVC-based apps contain:</span></span>

* <span data-ttu-id="56a5a-108">**M**odels: 앱의 데이터를 나타내는 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-108">**M**odels: Classes that represent the data of the app.</span></span> <span data-ttu-id="56a5a-109">모델 클래스는 유효성 검사 논리를 사용하여 해당 데이터에 대한 비즈니스 규칙을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-109">The model classes use validation logic to enforce business rules for that data.</span></span> <span data-ttu-id="56a5a-110">일반적으로 모델 개체는 데이터베이스에서 모델 상태를 검색하고 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-110">Typically, model objects retrieve and store model state in a database.</span></span> <span data-ttu-id="56a5a-111">이 자습서에서 `Movie` 모델은 데이터베이스에서 영화 데이터를 검색하고 이를 뷰에 제공하거나 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-111">In this tutorial, a `Movie` model retrieves movie data from a database, provides it to the view or updates it.</span></span> <span data-ttu-id="56a5a-112">수정된 데이터는 데이터베이스에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-112">Updated data is written to a database.</span></span>

* <span data-ttu-id="56a5a-113">**V**iews: 보기는 앱의 UI(사용자 인터페이스)를 표시하는 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-113">**V**iews: Views are the components that display the app's user interface (UI).</span></span> <span data-ttu-id="56a5a-114">일반적으로 UI는 모델 데이터를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-114">Generally, this UI displays the model data.</span></span>

* <span data-ttu-id="56a5a-115">**C**ontrollers: 브라우저 요청을 처리하는 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-115">**C**ontrollers: Classes that handle browser requests.</span></span> <span data-ttu-id="56a5a-116">모델 데이터를 검색하고 응답을 반환하는 보기 템플릿을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-116">They retrieve model data and call view templates that return a response.</span></span> <span data-ttu-id="56a5a-117">MVC 앱에서 보기는 단지 정보만 표시하며, 사용자 입력 및 상호 작용은 컨트롤러가 처리하고 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-117">In an MVC app, the view only displays information; the controller handles and responds to user input and interaction.</span></span> <span data-ttu-id="56a5a-118">예를 들어 컨트롤러는 경로 데이터 및 쿼리 문자열 값을 처리하고 모델에 이러한 값을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-118">For example, the controller handles route data and query-string values, and passes these values to the model.</span></span> <span data-ttu-id="56a5a-119">모델은 이러한 값을 사용하여 데이터베이스를 쿼리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-119">The model might use these values to query the database.</span></span> <span data-ttu-id="56a5a-120">예를 들어 `https://localhost:5001/Home/Privacy`에는 `Home`(컨트롤러) 및 `Privacy`(호출할 홈 컨트롤러의 작업 메서드)라는 경로 데이터가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-120">For example, `https://localhost:5001/Home/Privacy` has route data of `Home` (the controller) and `Privacy` (the action method to call on the home controller).</span></span> <span data-ttu-id="56a5a-121">`https://localhost:5001/Movies/Edit/5`는 영화 컨트롤러를 사용하여 ID=5인 영화를 편집하기 위한 요청입니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-121">`https://localhost:5001/Movies/Edit/5` is a request to edit the movie with ID=5 using the movie controller.</span></span> <span data-ttu-id="56a5a-122">경로 데이터는 자습서의 뒷 부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-122">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="56a5a-123">MVC 패턴을 사용하면 앱의 다양한 측면(입력 논리, 비즈니스 논리 및 UI 논리)을 분리하면서 이러한 요소 간에 느슨한 결합을 제공하는 앱을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-123">The MVC pattern helps you create apps that separate the different aspects of the app (input logic, business logic, and UI logic), while providing a loose coupling between these elements.</span></span> <span data-ttu-id="56a5a-124">이 패턴은 각 종류의 논리가 앱에 있어야 하는 위치를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-124">The pattern specifies where each kind of logic should be located in the app.</span></span> <span data-ttu-id="56a5a-125">UI 논리는 보기에 속합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-125">The UI logic belongs in the view.</span></span> <span data-ttu-id="56a5a-126">입력 논리는 컨트롤러에 속합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-126">Input logic belongs in the controller.</span></span> <span data-ttu-id="56a5a-127">비즈니스 논리는 모델에 속합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-127">Business logic belongs in the model.</span></span> <span data-ttu-id="56a5a-128">이렇게 분리하면 다른 코드에 영향을 주지 않고 한 번에 한 구현 측면에서 작업할 수 있기 때문에 앱을 만들 때 복잡성을 관리하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-128">This separation helps you manage complexity when you build an app, because it enables you to work on one aspect of the implementation at a time without impacting the code of another.</span></span> <span data-ttu-id="56a5a-129">예를 들어 비즈니스 논리 코드와 무관하게 보기 코드를 작업할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-129">For example, you can work on the view code without depending on the business logic code.</span></span>

<span data-ttu-id="56a5a-130">이 자습서 시리즈에서는 이러한 개념을 설명하고 영화 앱을 만들기 위해 사용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-130">We cover these concepts in this tutorial series and show you how to use them to build a movie app.</span></span> <span data-ttu-id="56a5a-131">MVC 프로젝트는 *컨트롤러* 및 *보기*를 위한 폴더를 포함하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-131">The MVC project contains folders for the *Controllers* and *Views*.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="56a5a-132">컨트롤러 추가</span><span class="sxs-lookup"><span data-stu-id="56a5a-132">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="56a5a-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56a5a-133">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="56a5a-134">**솔루션 탐색기** 에서 **컨트롤러 > 추가 > 컨트롤러**
  ![바로 가기 메뉴](adding-controller/_static/add_controller.png)를 오른쪽 단추로 클릭</span><span class="sxs-lookup"><span data-stu-id="56a5a-134">In **Solution Explorer**, right-click **Controllers > Add > Controller**
![Contextual menu](adding-controller/_static/add_controller.png)</span></span>

* <span data-ttu-id="56a5a-135">**스캐폴드 추가** 대화 상자에서 **MVC 컨트롤러 - 비어 있음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-135">In the **Add Scaffold** dialog box, select **MVC Controller - Empty**</span></span>

  ![MVC 컨트롤러 추가 및 이름 지정](adding-controller/_static/ac.png)

* <span data-ttu-id="56a5a-137">**빈 MVC 컨트롤러 추가 대화 상자**에 **HelloWorldController**를 입력하고 **추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-137">In the **Add Empty MVC Controller dialog**, enter **HelloWorldController** and select **ADD**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="56a5a-138">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="56a5a-138">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="56a5a-139">**탐색기** 아이콘을 선택한 다음, **컨트롤러 > 새 파일**을 컨트롤 클릭(마우스 오른쪽 단추로 클릭)하고 새 파일의 이름을 *HelloWorldController.cs*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-139">Select the **EXPLORER** icon and then control-click (right-click) **Controllers > New File** and name the new file *HelloWorldController.cs*.</span></span>

  ![상황에 맞는 메뉴](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_file.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="56a5a-141">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56a5a-141">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="56a5a-142">**솔루션 탐색기** 에서 **컨트롤러 > 추가 > 새 파일** 을 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-142">In **Solution Explorer**, right-click **Controllers > Add > New File**.</span></span>
<span data-ttu-id="56a5a-143">![상황에 맞는 메뉴](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)</span><span class="sxs-lookup"><span data-stu-id="56a5a-143">![Contextual menu](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)</span></span>

<span data-ttu-id="56a5a-144">**ASP.NET Core** 및 **MVC 컨트롤러 클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-144">Select **ASP.NET Core** and **MVC Controller Class**.</span></span>

<span data-ttu-id="56a5a-145">컨트롤러의 이름을 **HelloWorldController**로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-145">Name the controller **HelloWorldController**.</span></span>

![MVC 컨트롤러 추가 및 이름 지정](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

<span data-ttu-id="56a5a-147">*Controllers/HelloWorldController.cs*의 내용을 다음으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-147">Replace the contents of *Controllers/HelloWorldController.cs* with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

<span data-ttu-id="56a5a-148">컨트롤러의 모든 `public` 메서드는 HTTP 엔드포인트로 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-148">Every `public` method in a controller is callable as an HTTP endpoint.</span></span> <span data-ttu-id="56a5a-149">위의 샘플에서 메서드는 모두 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-149">In the sample above, both methods return a string.</span></span> <span data-ttu-id="56a5a-150">각 메서드 앞의 주석에 주목하세요.</span><span class="sxs-lookup"><span data-stu-id="56a5a-150">Note the comments preceding each method.</span></span>

<span data-ttu-id="56a5a-151">HTTP 엔드포인트는 웹 응용 프로그램에서 대상으로 지정 가능한 URL(예: `https://localhost:5001/HelloWorld`)로써, 사용되는 프로토콜(`HTTPS`), 웹 서버의 네트워크 위치(`localhost:5001`, TCP 포트 포함) 및 대상 URI인 `HelloWorld`를 결합합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-151">An HTTP endpoint is a targetable URL in the web application, such as `https://localhost:5001/HelloWorld`, and combines the protocol used: `HTTPS`, the network location of the web server (including the TCP port): `localhost:5001` and the target URI `HelloWorld`.</span></span>

<span data-ttu-id="56a5a-152">첫 번째 주석은 이 항목이 기본 URL에 `/HelloWorld/`를 추가하여 호출되는 [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) 메서드라고 명시합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-152">The first comment states this is an [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) method that's invoked by appending `/HelloWorld/` to the base URL.</span></span> <span data-ttu-id="56a5a-153">두 번째 주석은 URL에 `/HelloWorld/Welcome/`을 추가하여 호출되는 [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) 메서드를 명시합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-153">The second comment specifies an [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) method that's invoked by appending `/HelloWorld/Welcome/` to the URL.</span></span> <span data-ttu-id="56a5a-154">나중에 이 자습서에서는 스캐폴딩 엔진을 사용하여 데이터를 수정하는 `HTTP POST` 메서드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-154">Later on in the tutorial the scaffolding engine is used to generate `HTTP POST` methods which update data.</span></span>

<span data-ttu-id="56a5a-155">비 디버그 모드로 앱을 실행하고 주소 표시줄의 경로에 "HelloWorld"를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-155">Run the app in non-debug mode and append "HelloWorld" to the path in the address bar.</span></span> <span data-ttu-id="56a5a-156">`Index` 메서드가 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-156">The `Index` method returns a string.</span></span>

![브라우저 창에 This is my default action이라는 애플리케이션 응답이 표시됩니다.](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

<span data-ttu-id="56a5a-158">MVC는 들어오는 URL에 따라 컨트롤러 클래스(및 해당 클래스의 작업 메서드)를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-158">MVC invokes controller classes (and the action methods within them) depending on the incoming URL.</span></span> <span data-ttu-id="56a5a-159">MVC에서 사용하는 기본 [URL 라우팅 논리](xref:mvc/controllers/routing)는 다음과 같은 형식을 사용하여 호출할 코드를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-159">The default [URL routing logic](xref:mvc/controllers/routing) used by MVC uses a format like this to determine what code to invoke:</span></span>

`/[Controller]/[ActionName]/[Parameters]`

<span data-ttu-id="56a5a-160">라우팅 서식은 *Startup.cs* 파일의 `Configure` 메서드에서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-160">The routing format is set in the `Configure` method in *Startup.cs* file.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

<span data-ttu-id="56a5a-161">앱으로 이동할 때 URL 세그먼트를 제공하지 않으면 위에 강조 표시된 템플릿 줄에 지정된 "Home" 컨트롤러 및 "Index" 메서드가 기본값으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-161">When you browse to the app and don't supply any URL segments, it defaults to the "Home" controller and the "Index" method specified in the template line highlighted above.</span></span>

<span data-ttu-id="56a5a-162">첫 번째 URL 세그먼트는 실행할 컨트롤러 클래스를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-162">The first URL segment determines the controller class to run.</span></span> <span data-ttu-id="56a5a-163">따라서 `localhost:{PORT}/HelloWorld`는 **HelloWorld**Controller 클래스에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-163">So `localhost:{PORT}/HelloWorld` maps to the **HelloWorld**Controller class.</span></span> <span data-ttu-id="56a5a-164">URL 세그먼트의 두 번째 부분은 클래스의 작업 메서드를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-164">The second part of the URL segment determines the action method on the class.</span></span> <span data-ttu-id="56a5a-165">따라서 `localhost:{PORT}/HelloWorld/Index`는 `HelloWorldController` 클래스의 `Index` 메서드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-165">So `localhost:{PORT}/HelloWorld/Index` would cause the `Index` method of the `HelloWorldController` class to run.</span></span> <span data-ttu-id="56a5a-166">`localhost:{PORT}/HelloWorld`로만 이동했음에도 기본적으로 `Index` 메서드가 호출되었음에 주의하세요.</span><span class="sxs-lookup"><span data-stu-id="56a5a-166">Notice that you only had to browse to `localhost:{PORT}/HelloWorld` and the `Index` method was called by default.</span></span> <span data-ttu-id="56a5a-167">이는 `Index`가 메서드 이름이 명시적으로 지정되지 않으면 컨트롤러에서 호출되는 기본 메서드이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-167">That's because `Index` is the default method that will be called on a controller if a method name isn't explicitly specified.</span></span> <span data-ttu-id="56a5a-168">URL 세그먼트의 세 번째 부분(`id`)은 경로 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-168">The third part of the URL segment ( `id`) is for route data.</span></span> <span data-ttu-id="56a5a-169">경로 데이터는 자습서의 뒷 부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-169">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="56a5a-170">`https://localhost:{PORT}/HelloWorld/Welcome`으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-170">Browse to `https://localhost:{PORT}/HelloWorld/Welcome`.</span></span> <span data-ttu-id="56a5a-171">`Welcome` 메서드가 실행되고 문자열 `This is the Welcome action method...`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-171">The `Welcome` method runs and returns the string `This is the Welcome action method...`.</span></span> <span data-ttu-id="56a5a-172">이 URL의 경우 컨트롤러는 `HelloWorld`이고 `Welcome`이 작업 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-172">For this URL, the controller is `HelloWorld` and `Welcome` is the action method.</span></span> <span data-ttu-id="56a5a-173">아직 URL의 `[Parameters]` 부분을 사용하지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-173">You haven't used the `[Parameters]` part of the URL yet.</span></span>

![브라우저 창에 This is the Welcome action method라는 애플리케이션 응답이 표시됩니다.](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

<span data-ttu-id="56a5a-175">URL에서 컨트롤러에 일부 매개 변수 정보를 전달하도록 코드를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-175">Modify the code to pass some parameter information from the URL to the controller.</span></span> <span data-ttu-id="56a5a-176">예: `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span><span class="sxs-lookup"><span data-stu-id="56a5a-176">For example, `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span></span> <span data-ttu-id="56a5a-177">다음 코드와 같이 `Welcome` 메서드가 두 개의 매개 변수를 포함하도록 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-177">Change the `Welcome` method to include two parameters as shown in the following code.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

<span data-ttu-id="56a5a-178">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="56a5a-178">The preceding code:</span></span>

* <span data-ttu-id="56a5a-179">C#의 선택적 매개 변수 기능을 사용하여 `numTimes` 매개 변수에 대해 전달된 값이 없는 경우 해당 매개 변수의 기본값이 1임을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-179">Uses the C# optional-parameter feature to indicate that the `numTimes` parameter defaults to 1 if no value is passed for that parameter.</span></span> <!-- remove for simplified -->
* <span data-ttu-id="56a5a-180">`HtmlEncoder.Default.Encode`를 사용하여 악의적인 입력(예: JavaScript)으로부터 응용 프로그램을 보호합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-180">Uses `HtmlEncoder.Default.Encode` to protect the app from malicious input (namely JavaScript).</span></span>
* <span data-ttu-id="56a5a-181">`$"Hello {name}, NumTimes is: {numTimes}"`에서 [보간된 문자열](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-181">Uses [Interpolated Strings](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) in `$"Hello {name}, NumTimes is: {numTimes}"`.</span></span> <!-- remove for simplified -->

<span data-ttu-id="56a5a-182">앱을 실행하고 다음으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-182">Run the app and browse to:</span></span>

   `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="56a5a-183">(`{PORT}`를 포트 번호로 바꿉니다.) URL에서 `name` 및 `numtimes`에 다른 값을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-183">(Replace `{PORT}` with your port number.) You can try different values for `name` and `numtimes` in the URL.</span></span> <span data-ttu-id="56a5a-184">MVC [모델 바인딩](xref:mvc/models/model-binding) 시스템은 주소 표시줄의 쿼리 문자열에서 메서드의 매개 변수로 명명된 매개 변수를 자동으로 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-184">The MVC [model binding](xref:mvc/models/model-binding) system automatically maps the named parameters from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="56a5a-185">자세한 정보는 [모델 바인딩](xref:mvc/models/model-binding)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="56a5a-185">See [Model Binding](xref:mvc/models/model-binding) for more information.</span></span>

![Hello Rick, NumTimes의 애플리케이션 응답을 보여주는 브라우저 창\: 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

<span data-ttu-id="56a5a-187">위의 이미지에서는 URL 세그먼트(`Parameters`)를 사용하지 않고 `name` 및 `numTimes` 매개 변수가 [쿼리 문자열](https://wikipedia.org/wiki/Query_string)로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-187">In the image above, the URL segment (`Parameters`) isn't used, the `name` and `numTimes` parameters are passed in the [query string](https://wikipedia.org/wiki/Query_string).</span></span> <span data-ttu-id="56a5a-188">위 URL에서 `?`(물음표)는 구분 기호이며, 다음에 쿼리 문자열이 옵니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-188">The `?` (question mark) in the above URL is a separator, and the query string follows.</span></span> <span data-ttu-id="56a5a-189">`&` 문자는 필드-값 쌍을 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-189">The `&` character separates field-value pairs.</span></span>

<span data-ttu-id="56a5a-190">`Welcome` 메서드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-190">Replace the `Welcome` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

<span data-ttu-id="56a5a-191">앱을 실행하고 `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick` URL을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-191">Run the app and enter the following URL: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span></span>

<span data-ttu-id="56a5a-192">이번에는 세 번째 URL 세그먼트가 `id` 경로 매개 변수와 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-192">This time the third URL segment matched the route parameter `id`.</span></span> <span data-ttu-id="56a5a-193">`Welcome` 메서드는 `MapControllerRoute` 메서드의 URL 템플릿과 일치하는 `id` 매개 변수를 포함하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-193">The `Welcome` method contains a parameter `id` that matched the URL template in the `MapControllerRoute` method.</span></span> <span data-ttu-id="56a5a-194">후행 `?`(`id?`의)는 `id` 매개 변수가 선택 사항임을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-194">The trailing `?` (in `id?`) indicates the `id` parameter is optional.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

<span data-ttu-id="56a5a-195">지금까지의 예제에서는 컨트롤러가 MVC의 “VC” 부분, 즉 **V**iew(보기) 및 **C**ontroller(컨트롤러) 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-195">In these examples the controller has been doing the "VC" portion of MVC - that is, the **V**iew and the **C**ontroller work.</span></span> <span data-ttu-id="56a5a-196">컨트롤러가 HTML을 직접 반환하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-196">The controller is returning HTML directly.</span></span> <span data-ttu-id="56a5a-197">코드 및 유지 관리가 매우 복잡해지므로 일반적으로 컨트롤러에서 직접 HTML을 반환하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-197">Generally you don't want controllers returning HTML directly, since that becomes very cumbersome to code and maintain.</span></span> <span data-ttu-id="56a5a-198">대신 별도의 Razor 보기 템플릿 파일을 사용하여 HTML 응답을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-198">Instead you typically use a separate Razor view template file to generate the HTML response.</span></span> <span data-ttu-id="56a5a-199">다음 자습서에서는 해당 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-199">You do that in the next tutorial.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="56a5a-200">[이전](start-mvc.md)
> [다음](adding-view.md)</span><span class="sxs-lookup"><span data-stu-id="56a5a-200">[Previous](start-mvc.md)
[Next](adding-view.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="56a5a-201">MVC( Model-View-Controller) 아키텍처 패턴은 다음 세 가지 주요 구성 요소로 앱을 구분합니다. **M**odel, **V**iew 및 **C**ontroller.</span><span class="sxs-lookup"><span data-stu-id="56a5a-201">The Model-View-Controller (MVC) architectural pattern separates an app into three main components: **M**odel, **V**iew, and **C**ontroller.</span></span> <span data-ttu-id="56a5a-202">MVC 패턴을 통해 기존 모놀리식 응용 프로그램보다 쉽게 테스트 가능하고 업데이트할 수 있는 앱을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-202">The MVC pattern helps you create apps that are more testable and easier to update than traditional monolithic apps.</span></span> <span data-ttu-id="56a5a-203">MVC 기반 앱에는 다음이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-203">MVC-based apps contain:</span></span>

* <span data-ttu-id="56a5a-204">**M**odels: 앱의 데이터를 나타내는 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-204">**M**odels: Classes that represent the data of the app.</span></span> <span data-ttu-id="56a5a-205">모델 클래스는 유효성 검사 논리를 사용하여 해당 데이터에 대한 비즈니스 규칙을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-205">The model classes use validation logic to enforce business rules for that data.</span></span> <span data-ttu-id="56a5a-206">일반적으로 모델 개체는 데이터베이스에서 모델 상태를 검색하고 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-206">Typically, model objects retrieve and store model state in a database.</span></span> <span data-ttu-id="56a5a-207">이 자습서에서 `Movie` 모델은 데이터베이스에서 영화 데이터를 검색하고 이를 뷰에 제공하거나 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-207">In this tutorial, a `Movie` model retrieves movie data from a database, provides it to the view or updates it.</span></span> <span data-ttu-id="56a5a-208">수정된 데이터는 데이터베이스에 기록됩니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-208">Updated data is written to a database.</span></span>

* <span data-ttu-id="56a5a-209">**V**iews: 보기는 앱의 UI(사용자 인터페이스)를 표시하는 구성 요소입니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-209">**V**iews: Views are the components that display the app's user interface (UI).</span></span> <span data-ttu-id="56a5a-210">일반적으로 UI는 모델 데이터를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-210">Generally, this UI displays the model data.</span></span>

* <span data-ttu-id="56a5a-211">**C**ontrollers: 브라우저 요청을 처리하는 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-211">**C**ontrollers: Classes that handle browser requests.</span></span> <span data-ttu-id="56a5a-212">모델 데이터를 검색하고 응답을 반환하는 보기 템플릿을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-212">They retrieve model data and call view templates that return a response.</span></span> <span data-ttu-id="56a5a-213">MVC 앱에서 보기는 단지 정보만 표시하며, 사용자 입력 및 상호 작용은 컨트롤러가 처리하고 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-213">In an MVC app, the view only displays information; the controller handles and responds to user input and interaction.</span></span> <span data-ttu-id="56a5a-214">예를 들어 컨트롤러는 경로 데이터 및 쿼리 문자열 값을 처리하고 모델에 이러한 값을 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-214">For example, the controller handles route data and query-string values, and passes these values to the model.</span></span> <span data-ttu-id="56a5a-215">모델은 이러한 값을 사용하여 데이터베이스를 쿼리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-215">The model might use these values to query the database.</span></span> <span data-ttu-id="56a5a-216">예를 들어 `https://localhost:5001/Home/About`에는 `Home`(컨트롤러) 및 `About`(호출할 홈 컨트롤러의 작업 메서드)라는 경로 데이터가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-216">For example, `https://localhost:5001/Home/About` has route data of `Home` (the controller) and `About` (the action method to call on the home controller).</span></span> <span data-ttu-id="56a5a-217">`https://localhost:5001/Movies/Edit/5`는 영화 컨트롤러를 사용하여 ID=5인 영화를 편집하기 위한 요청입니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-217">`https://localhost:5001/Movies/Edit/5` is a request to edit the movie with ID=5 using the movie controller.</span></span> <span data-ttu-id="56a5a-218">경로 데이터는 자습서의 뒷 부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-218">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="56a5a-219">MVC 패턴을 사용하면 앱의 다양한 측면(입력 논리, 비즈니스 논리 및 UI 논리)을 분리하면서 이러한 요소 간에 느슨한 결합을 제공하는 앱을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-219">The MVC pattern helps you create apps that separate the different aspects of the app (input logic, business logic, and UI logic), while providing a loose coupling between these elements.</span></span> <span data-ttu-id="56a5a-220">이 패턴은 각 종류의 논리가 앱에 있어야 하는 위치를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-220">The pattern specifies where each kind of logic should be located in the app.</span></span> <span data-ttu-id="56a5a-221">UI 논리는 보기에 속합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-221">The UI logic belongs in the view.</span></span> <span data-ttu-id="56a5a-222">입력 논리는 컨트롤러에 속합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-222">Input logic belongs in the controller.</span></span> <span data-ttu-id="56a5a-223">비즈니스 논리는 모델에 속합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-223">Business logic belongs in the model.</span></span> <span data-ttu-id="56a5a-224">이렇게 분리하면 다른 코드에 영향을 주지 않고 한 번에 한 구현 측면에서 작업할 수 있기 때문에 앱을 만들 때 복잡성을 관리하는 데 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-224">This separation helps you manage complexity when you build an app, because it enables you to work on one aspect of the implementation at a time without impacting the code of another.</span></span> <span data-ttu-id="56a5a-225">예를 들어 비즈니스 논리 코드와 무관하게 보기 코드를 작업할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-225">For example, you can work on the view code without depending on the business logic code.</span></span>

<span data-ttu-id="56a5a-226">이 자습서 시리즈에서는 이러한 개념을 설명하고 영화 앱을 만들기 위해 사용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-226">We cover these concepts in this tutorial series and show you how to use them to build a movie app.</span></span> <span data-ttu-id="56a5a-227">MVC 프로젝트는 *컨트롤러* 및 *보기*를 위한 폴더를 포함하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-227">The MVC project contains folders for the *Controllers* and *Views*.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="56a5a-228">컨트롤러 추가</span><span class="sxs-lookup"><span data-stu-id="56a5a-228">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="56a5a-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56a5a-229">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="56a5a-230">**솔루션 탐색기** 에서 **컨트롤러 > 추가 > 컨트롤러**
  ![바로 가기 메뉴](adding-controller/_static/add_controller.png)를 오른쪽 단추로 클릭</span><span class="sxs-lookup"><span data-stu-id="56a5a-230">In **Solution Explorer**, right-click **Controllers > Add > Controller**
![Contextual menu](adding-controller/_static/add_controller.png)</span></span>

* <span data-ttu-id="56a5a-231">**스캐폴드 추가** 대화 상자에서 **MVC 컨트롤러 - 비어 있음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-231">In the **Add Scaffold** dialog box, select **MVC Controller - Empty**</span></span>

  ![MVC 컨트롤러 추가 및 이름 지정](adding-controller/_static/ac.png)

* <span data-ttu-id="56a5a-233">**빈 MVC 컨트롤러 추가 대화 상자**에 **HelloWorldController**를 입력하고 **추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-233">In the **Add Empty MVC Controller dialog**, enter **HelloWorldController** and select **ADD**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="56a5a-234">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="56a5a-234">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="56a5a-235">**탐색기** 아이콘을 선택한 다음, **컨트롤러 > 새 파일**을 컨트롤 클릭(마우스 오른쪽 단추로 클릭)하고 새 파일의 이름을 *HelloWorldController.cs*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-235">Select the **EXPLORER** icon and then control-click (right-click) **Controllers > New File** and name the new file *HelloWorldController.cs*.</span></span>

  ![상황에 맞는 메뉴](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_file.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="56a5a-237">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56a5a-237">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="56a5a-238">**솔루션 탐색기** 에서 **컨트롤러 > 추가 > 새 파일** 을 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-238">In **Solution Explorer**, right-click **Controllers > Add > New File**.</span></span>
<span data-ttu-id="56a5a-239">![상황에 맞는 메뉴](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)</span><span class="sxs-lookup"><span data-stu-id="56a5a-239">![Contextual menu](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)</span></span>

<span data-ttu-id="56a5a-240">**ASP.NET Core** 및 **MVC 컨트롤러 클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-240">Select **ASP.NET Core** and **MVC Controller Class**.</span></span>

<span data-ttu-id="56a5a-241">컨트롤러의 이름을 **HelloWorldController**로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-241">Name the controller **HelloWorldController**.</span></span>

![MVC 컨트롤러 추가 및 이름 지정](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

<span data-ttu-id="56a5a-243">*Controllers/HelloWorldController.cs*의 내용을 다음으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-243">Replace the contents of *Controllers/HelloWorldController.cs* with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

<span data-ttu-id="56a5a-244">컨트롤러의 모든 `public` 메서드는 HTTP 엔드포인트로 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-244">Every `public` method in a controller is callable as an HTTP endpoint.</span></span> <span data-ttu-id="56a5a-245">위의 샘플에서 메서드는 모두 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-245">In the sample above, both methods return a string.</span></span> <span data-ttu-id="56a5a-246">각 메서드 앞의 주석에 주목하세요.</span><span class="sxs-lookup"><span data-stu-id="56a5a-246">Note the comments preceding each method.</span></span>

<span data-ttu-id="56a5a-247">HTTP 엔드포인트는 웹 응용 프로그램에서 대상으로 지정 가능한 URL(예: `https://localhost:5001/HelloWorld`)로써, 사용되는 프로토콜(`HTTPS`), 웹 서버의 네트워크 위치(`localhost:5001`, TCP 포트 포함) 및 대상 URI인 `HelloWorld`를 결합합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-247">An HTTP endpoint is a targetable URL in the web application, such as `https://localhost:5001/HelloWorld`, and combines the protocol used: `HTTPS`, the network location of the web server (including the TCP port): `localhost:5001` and the target URI `HelloWorld`.</span></span>

<span data-ttu-id="56a5a-248">첫 번째 주석은 이 항목이 기본 URL에 `/HelloWorld/`를 추가하여 호출되는 [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) 메서드라고 명시합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-248">The first comment states this is an [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) method that's invoked by appending `/HelloWorld/` to the base URL.</span></span> <span data-ttu-id="56a5a-249">두 번째 주석은 URL에 `/HelloWorld/Welcome/`을 추가하여 호출되는 [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) 메서드를 명시합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-249">The second comment specifies an [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) method that's invoked by appending `/HelloWorld/Welcome/` to the URL.</span></span> <span data-ttu-id="56a5a-250">나중에 이 자습서에서는 스캐폴딩 엔진을 사용하여 데이터를 수정하는 `HTTP POST` 메서드를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-250">Later on in the tutorial the scaffolding engine is used to generate `HTTP POST` methods which update data.</span></span>

<span data-ttu-id="56a5a-251">비 디버그 모드로 앱을 실행하고 주소 표시줄의 경로에 "HelloWorld"를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-251">Run the app in non-debug mode and append "HelloWorld" to the path in the address bar.</span></span> <span data-ttu-id="56a5a-252">`Index` 메서드가 문자열을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-252">The `Index` method returns a string.</span></span>

![브라우저 창에 This is my default action이라는 애플리케이션 응답이 표시됩니다.](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

<span data-ttu-id="56a5a-254">MVC는 들어오는 URL에 따라 컨트롤러 클래스(및 해당 클래스의 작업 메서드)를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-254">MVC invokes controller classes (and the action methods within them) depending on the incoming URL.</span></span> <span data-ttu-id="56a5a-255">MVC에서 사용하는 기본 [URL 라우팅 논리](xref:mvc/controllers/routing)는 다음과 같은 형식을 사용하여 호출할 코드를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-255">The default [URL routing logic](xref:mvc/controllers/routing) used by MVC uses a format like this to determine what code to invoke:</span></span>

`/[Controller]/[ActionName]/[Parameters]`

<span data-ttu-id="56a5a-256">라우팅 서식은 *Startup.cs* 파일의 `Configure` 메서드에서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-256">The routing format is set in the `Configure` method in *Startup.cs* file.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<!-- 
Add link to explain lambda.
Remove link for simplified tutorial.
-->

<span data-ttu-id="56a5a-257">앱으로 이동할 때 URL 세그먼트를 제공하지 않으면 위에 강조 표시된 템플릿 줄에 지정된 "Home" 컨트롤러 및 "Index" 메서드가 기본값으로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-257">When you browse to the app and don't supply any URL segments, it defaults to the "Home" controller and the "Index" method specified in the template line highlighted above.</span></span>

<span data-ttu-id="56a5a-258">첫 번째 URL 세그먼트는 실행할 컨트롤러 클래스를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-258">The first URL segment determines the controller class to run.</span></span> <span data-ttu-id="56a5a-259">따라서 `localhost:{PORT}/HelloWorld`은 `HelloWorldController` 클래스에 매핑됩니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-259">So `localhost:{PORT}/HelloWorld` maps to the `HelloWorldController` class.</span></span> <span data-ttu-id="56a5a-260">URL 세그먼트의 두 번째 부분은 클래스의 작업 메서드를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-260">The second part of the URL segment determines the action method on the class.</span></span> <span data-ttu-id="56a5a-261">따라서 `localhost:{PORT}/HelloWorld/Index`는 `HelloWorldController` 클래스의 `Index` 메서드를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-261">So `localhost:{PORT}/HelloWorld/Index` would cause the `Index` method of the `HelloWorldController` class to run.</span></span> <span data-ttu-id="56a5a-262">`localhost:{PORT}/HelloWorld`로만 이동했음에도 기본적으로 `Index` 메서드가 호출되었음에 주의하세요.</span><span class="sxs-lookup"><span data-stu-id="56a5a-262">Notice that you only had to browse to `localhost:{PORT}/HelloWorld` and the `Index` method was called by default.</span></span> <span data-ttu-id="56a5a-263">메서드 이름이 명시적으로 지정되지 않으면 `Index`가 컨트롤러에서 호출되는 기본 메서드이기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-263">This is because `Index` is the default method that will be called on a controller if a method name isn't explicitly specified.</span></span> <span data-ttu-id="56a5a-264">URL 세그먼트의 세 번째 부분(`id`)은 경로 데이터입니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-264">The third part of the URL segment ( `id`) is for route data.</span></span> <span data-ttu-id="56a5a-265">경로 데이터는 자습서의 뒷 부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-265">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="56a5a-266">`https://localhost:{PORT}/HelloWorld/Welcome`으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-266">Browse to `https://localhost:{PORT}/HelloWorld/Welcome`.</span></span> <span data-ttu-id="56a5a-267">`Welcome` 메서드가 실행되고 문자열 `This is the Welcome action method...`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-267">The `Welcome` method runs and returns the string `This is the Welcome action method...`.</span></span> <span data-ttu-id="56a5a-268">이 URL의 경우 컨트롤러는 `HelloWorld`이고 `Welcome`이 작업 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-268">For this URL, the controller is `HelloWorld` and `Welcome` is the action method.</span></span> <span data-ttu-id="56a5a-269">아직 URL의 `[Parameters]` 부분을 사용하지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-269">You haven't used the `[Parameters]` part of the URL yet.</span></span>

![브라우저 창에 This is the Welcome action method라는 애플리케이션 응답이 표시됩니다.](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

<span data-ttu-id="56a5a-271">URL에서 컨트롤러에 일부 매개 변수 정보를 전달하도록 코드를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-271">Modify the code to pass some parameter information from the URL to the controller.</span></span> <span data-ttu-id="56a5a-272">예: `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span><span class="sxs-lookup"><span data-stu-id="56a5a-272">For example, `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span></span> <span data-ttu-id="56a5a-273">다음 코드와 같이 `Welcome` 메서드가 두 개의 매개 변수를 포함하도록 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-273">Change the `Welcome` method to include two parameters as shown in the following code.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

<span data-ttu-id="56a5a-274">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="56a5a-274">The preceding code:</span></span>

* <span data-ttu-id="56a5a-275">C#의 선택적 매개 변수 기능을 사용하여 `numTimes` 매개 변수에 대해 전달된 값이 없는 경우 해당 매개 변수의 기본값이 1임을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-275">Uses the C# optional-parameter feature to indicate that the `numTimes` parameter defaults to 1 if no value is passed for that parameter.</span></span> <!-- remove for simplified -->
* <span data-ttu-id="56a5a-276">`HtmlEncoder.Default.Encode`를 사용하여 악의적인 입력(예: JavaScript)으로부터 응용 프로그램을 보호합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-276">Uses `HtmlEncoder.Default.Encode` to protect the app from malicious input (namely JavaScript).</span></span>
* <span data-ttu-id="56a5a-277">`$"Hello {name}, NumTimes is: {numTimes}"`에서 [보간된 문자열](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-277">Uses [Interpolated Strings](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) in `$"Hello {name}, NumTimes is: {numTimes}"`.</span></span> <!-- remove for simplified -->

<span data-ttu-id="56a5a-278">앱을 실행하고 다음으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-278">Run the app and browse to:</span></span>

   `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="56a5a-279">(`{PORT}`를 포트 번호로 바꿉니다.) URL에서 `name` 및 `numtimes`에 다른 값을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-279">(Replace `{PORT}` with your port number.) You can try different values for `name` and `numtimes` in the URL.</span></span> <span data-ttu-id="56a5a-280">MVC [모델 바인딩](xref:mvc/models/model-binding) 시스템은 주소 표시줄의 쿼리 문자열에서 메서드의 매개 변수로 명명된 매개 변수를 자동으로 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-280">The MVC [model binding](xref:mvc/models/model-binding) system automatically maps the named parameters from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="56a5a-281">자세한 정보는 [모델 바인딩](xref:mvc/models/model-binding)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="56a5a-281">See [Model Binding](xref:mvc/models/model-binding) for more information.</span></span>

![Hello Rick, NumTimes의 애플리케이션 응답을 보여주는 브라우저 창\: 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

<span data-ttu-id="56a5a-283">위의 이미지에서는 URL 세그먼트(`Parameters`)를 사용하지 않고 `name` 및 `numTimes` 매개 변수가 [쿼리 문자열](https://wikipedia.org/wiki/Query_string)로 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-283">In the image above, the URL segment (`Parameters`) isn't used, the `name` and `numTimes` parameters are passed in the [query string](https://wikipedia.org/wiki/Query_string).</span></span> <span data-ttu-id="56a5a-284">위 URL에서 `?`(물음표)는 구분 기호이며, 다음에 쿼리 문자열이 옵니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-284">The `?` (question mark) in the above URL is a separator, and the query string follows.</span></span> <span data-ttu-id="56a5a-285">`&` 문자는 필드-값 쌍을 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-285">The `&` character separates field-value pairs.</span></span>

<span data-ttu-id="56a5a-286">`Welcome` 메서드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-286">Replace the `Welcome` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

<span data-ttu-id="56a5a-287">앱을 실행하고 `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick` URL을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-287">Run the app and enter the following URL: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span></span>

<span data-ttu-id="56a5a-288">이번에는 세 번째 URL 세그먼트가 `id` 경로 매개 변수와 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-288">This time the third URL segment matched the route parameter `id`.</span></span> <span data-ttu-id="56a5a-289">`Welcome` 메서드는 `MapRoute` 메서드의 URL 템플릿과 일치하는 `id` 매개 변수를 포함하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-289">The `Welcome` method contains a parameter `id` that matched the URL template in the `MapRoute` method.</span></span> <span data-ttu-id="56a5a-290">후행 `?`(`id?`의)는 `id` 매개 변수가 선택 사항임을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-290">The trailing `?` (in `id?`) indicates the `id` parameter is optional.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<span data-ttu-id="56a5a-291">이러한 예제에서 컨트롤러는 MVC의 "VC" 부분을 사용했습니다. 즉, 보기 및 컨트롤러 작업입니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-291">In these examples the controller has been doing the "VC" portion of MVC - that is, the view and controller work.</span></span> <span data-ttu-id="56a5a-292">컨트롤러가 HTML을 직접 반환하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-292">The controller is returning HTML directly.</span></span> <span data-ttu-id="56a5a-293">코드 및 유지 관리가 매우 복잡해지므로 일반적으로 컨트롤러에서 직접 HTML을 반환하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-293">Generally you don't want controllers returning HTML directly, since that becomes very cumbersome to code and maintain.</span></span> <span data-ttu-id="56a5a-294">대신 일반적으로 별도의 Razor 보기 템플릿 파일을 사용하여 HTML 응답을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-294">Instead you typically use a separate Razor view template file to help generate the HTML response.</span></span> <span data-ttu-id="56a5a-295">다음 자습서에서는 해당 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="56a5a-295">You do that in the next tutorial.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="56a5a-296">[이전](start-mvc.md)
> [다음](adding-view.md)</span><span class="sxs-lookup"><span data-stu-id="56a5a-296">[Previous](start-mvc.md)
[Next](adding-view.md)</span></span>

::: moniker-end
