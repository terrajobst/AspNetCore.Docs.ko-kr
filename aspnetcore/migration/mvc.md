---
title: ASP.NET MVC에서 ASP.NET Core MVC로 마이그레이션
author: ardalis
description: ASP.NET MVC 프로젝트를 ASP.NET Core MVC로 시작 하는 방법에 알아봅니다.
ms.author: riande
ms.date: 04/06/2019
uid: migration/mvc
ms.openlocfilehash: a9e2b41b933ed04a23515564892ed1694a4ac4f8
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64893500"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a><span data-ttu-id="69cf7-103">ASP.NET MVC에서 ASP.NET Core MVC로 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="69cf7-103">Migrate from ASP.NET MVC to ASP.NET Core MVC</span></span>

<span data-ttu-id="69cf7-104">하 여 [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27)하십시오 [Steve Smith](https://ardalis.com/), 및 [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="69cf7-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/), and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="69cf7-105">이 문서에서는 마이그레이션하는 ASP.NET MVC 프로젝트를 시작 하는 방법을 보여 줍니다 [ASP.NET Core MVC](../mvc/overview.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-105">This article shows how to get started migrating an ASP.NET MVC project to [ASP.NET Core MVC](../mvc/overview.md).</span></span> <span data-ttu-id="69cf7-106">프로세스에서 강조 표시는 여러 가지 ASP.NET MVC에서 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-106">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="69cf7-107">ASP.NET MVC에서 마이그레이션하는 여러 단계의 프로세스 이며이 문서에서는 초기 설치, 기본 컨트롤러 및 뷰, 정적 콘텐츠 및 클라이언트 쪽 종속성을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-107">Migrating from ASP.NET MVC is a multiple step process and this article covers the initial setup, basic controllers and views, static content, and client-side dependencies.</span></span> <span data-ttu-id="69cf7-108">추가 문서는 마이그레이션 구성 및 많은 ASP.NET MVC 프로젝트에 있는 id 코드를 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-108">Additional articles cover migrating configuration and identity code found in many ASP.NET MVC projects.</span></span>

> [!NOTE]
> <span data-ttu-id="69cf7-109">샘플에서 버전 번호는 현재 일 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-109">The version numbers in the samples might not be current.</span></span> <span data-ttu-id="69cf7-110">프로젝트를 적절 하 게 업데이트 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-110">You may need to update your projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="69cf7-111">시작 ASP.NET MVC 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="69cf7-111">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="69cf7-112">업그레이드를 보여 주기 위해 ASP.NET MVC 앱을 만들어 시작 됩니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-112">To demonstrate the upgrade, we'll start by creating a ASP.NET MVC app.</span></span> <span data-ttu-id="69cf7-113">이름으로 만들 *WebApp1* 네임 스페이스에는 다음 단계에서 만드는 ASP.NET Core 프로젝트를 일치 하도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-113">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project we create in the next step.</span></span>

![Visual Studio 새 프로젝트 대화 상자](mvc/_static/new-project.png)

![새 웹 응용 프로그램 대화 상자: ASP.NET 템플릿 창에서 선택한 MVC 프로젝트 템플릿](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="69cf7-116">*선택 사항:* 솔루션의 이름을 변경할 *WebApp1* 하 *Mvc5*합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-116">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5*.</span></span> <span data-ttu-id="69cf7-117">Visual Studio에 새 솔루션 이름이 표시 됩니다 (*Mvc5*), 다음 프로젝트에서이 프로젝트에 하기가 쉽습니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-117">Visual Studio displays the new solution name (*Mvc5*), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="69cf7-118">ASP.NET Core 프로젝트를 만들려면</span><span class="sxs-lookup"><span data-stu-id="69cf7-118">Create the ASP.NET Core project</span></span>

<span data-ttu-id="69cf7-119">새 *빈* 이전 프로젝트와 동일한 이름 사용 하 여 ASP.NET Core 웹 앱 (*WebApp1*) 되므로 두 프로젝트의 네임 스페이스와 일치 합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-119">Create a new *empty* ASP.NET Core web app with the same name as the previous project (*WebApp1*) so the namespaces in the two projects match.</span></span> <span data-ttu-id="69cf7-120">동일한 네임 스페이스가 있으면 쉽게 두 프로젝트 간에 코드를 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-120">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="69cf7-121">동일한 이름을 사용 하 여 이전 프로젝트 이외의 다른 디렉터리에서이 프로젝트를 만들려면 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-121">You'll have to create this project in a different directory than the previous project to use the same name.</span></span>

![새 프로젝트 대화 상자](mvc/_static/new_core.png)

![새 ASP.NET 웹 응용 프로그램 대화 상자: ASP.NET Core 템플릿 창에서 선택한 빈 프로젝트 템플릿](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="69cf7-124">*선택 사항:* 사용 하 여 새 ASP.NET Core 앱을 *웹 응용 프로그램* 프로젝트 템플릿.</span><span class="sxs-lookup"><span data-stu-id="69cf7-124">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="69cf7-125">프로젝트 이름을 *WebApp1*의 인증 옵션을 선택 하 고 **개별 사용자 계정**합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-125">Name the project *WebApp1*, and select an authentication option of **Individual User Accounts**.</span></span> <span data-ttu-id="69cf7-126">이 앱을 이름 바꾸기 *FullAspNetCore*합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-126">Rename this app to *FullAspNetCore*.</span></span> <span data-ttu-id="69cf7-127">변환에서이 프로젝트 시간을 절약할 수를 만드는 중입니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-127">Creating this project saves you time in the conversion.</span></span> <span data-ttu-id="69cf7-128">최종 결과를 보려면 하거나 변환 프로젝트에 코드를 복사 하는 템플릿에서 생성 된 코드를 살펴볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-128">You can look at the template-generated code to see the end result or to copy code to the conversion project.</span></span> <span data-ttu-id="69cf7-129">또한 템플릿에서 생성 된 프로젝트를 사용 하 여 비교 하는 변환 단계에서 멈출 때에 도움이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-129">It's also helpful when you get stuck on a conversion step to compare with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="69cf7-130">MVC를 사용 하도록 사이트 구성</span><span class="sxs-lookup"><span data-stu-id="69cf7-130">Configure the site to use MVC</span></span>

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="69cf7-131">.NET Core를 대상으로 할 때 합니다 [Microsoft.AspNetCore.App 메타 패키지](xref:fundamentals/metapackage-app) 기본적으로 참조 됩니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-131">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="69cf7-132">이 패키지는 MVC 앱에서 일반적으로 사용 되는 패키지의 패키지를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-132">This package contains packages commonly used packages by MVC apps.</span></span> <span data-ttu-id="69cf7-133">.NET Framework를 대상으로 하는 경우 프로젝트 파일의 패키지 참조를 개별적으로 나열 합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-133">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="69cf7-134">.NET Core를 대상으로 할 때 합니다 [Microsoft.AspNetCore.All 메타 패키지](xref:fundamentals/metapackage) 기본적으로 참조 됩니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-134">When targeting .NET Core, the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) is referenced by default.</span></span> <span data-ttu-id="69cf7-135">이 패키지는 MVC 앱에서 일반적으로 사용 되는 패키지의 패키지를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-135">This package contains packages commonly used packages by MVC apps.</span></span> <span data-ttu-id="69cf7-136">.NET Framework를 대상으로 하는 경우 프로젝트 파일의 패키지 참조를 개별적으로 나열 합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-136">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* <span data-ttu-id="69cf7-137">.NET Core 또는.NET Framework를 대상으로 하는 경우 MVC 앱에서 일반적으로 사용 되는 패키지 패키지는 프로젝트 파일에 개별적으로 나열 됩니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-137">When targeting .NET Core or .NET Framework, packages commonly used packages by MVC apps are listed individually in the project file.</span></span>

::: moniker-end

<span data-ttu-id="69cf7-138">`Microsoft.AspNetCore.Mvc` ASP.NET Core MVC 프레임 워크가입니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-138">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="69cf7-139">`Microsoft.AspNetCore.StaticFiles` 정적 파일 처리기가입니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-139">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="69cf7-140">ASP.NET Core 런타임이 모듈식 이며 정적 파일 제공에 명시적으로 선택 해야 합니다 (참조 [정적 파일](xref:fundamentals/static-files)).</span><span class="sxs-lookup"><span data-stu-id="69cf7-140">The ASP.NET Core runtime is modular, and you must explicitly opt in to serve static files (see [Static files](xref:fundamentals/static-files)).</span></span>

* <span data-ttu-id="69cf7-141">열기는 *Startup.cs* 파일과 다음과 일치 하도록 코드를 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-141">Open the *Startup.cs* file and change the code to match the following:</span></span>

  [!code-csharp[](mvc/sample/Startup.cs?highlight=13,26-31)]

<span data-ttu-id="69cf7-142">`UseStaticFiles` 확장 메서드는 정적 파일 처리기를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-142">The `UseStaticFiles` extension method adds the static file handler.</span></span> <span data-ttu-id="69cf7-143">이전에 언급 했 듯이 ASP.NET 런타임이 모듈식 이며 정적 파일 제공에 명시적으로 선택 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-143">As mentioned previously, the ASP.NET runtime is modular, and you must explicitly opt in to serve static files.</span></span> <span data-ttu-id="69cf7-144">`UseMvc` 라우팅 확장 메서드를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-144">The `UseMvc` extension method adds routing.</span></span> <span data-ttu-id="69cf7-145">자세한 내용은 [응용 프로그램 시작](xref:fundamentals/startup) 하 고 [라우팅](xref:fundamentals/routing)합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-145">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="69cf7-146">컨트롤러 및 뷰를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-146">Add a controller and view</span></span>

<span data-ttu-id="69cf7-147">이 섹션에서는 최소한의 컨트롤러 및 뷰를 ASP.NET MVC 컨트롤러에 대 한 자리 표시자로 사용 및 다음 섹션에서 마이그레이션할 수 있습니다 하는 뷰를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-147">In this section, you'll add a minimal controller and view to serve as placeholders for the ASP.NET MVC controller and views you'll migrate in the next section.</span></span>

* <span data-ttu-id="69cf7-148">추가 된 *컨트롤러* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-148">Add a *Controllers* folder.</span></span>

* <span data-ttu-id="69cf7-149">추가 **컨트롤러 클래스** 라는 *HomeController.cs* 하는 *컨트롤러* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-149">Add a **Controller Class** named *HomeController.cs* to the *Controllers* folder.</span></span>

![새 항목 추가 대화 상자](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="69cf7-151">추가 된 *뷰* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-151">Add a *Views* folder.</span></span>

* <span data-ttu-id="69cf7-152">추가 된 *Views/Home* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-152">Add a *Views/Home* folder.</span></span>

* <span data-ttu-id="69cf7-153">추가 **Razor 뷰** 라는 *Index.cshtml* 하는 *Views/Home* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-153">Add a **Razor View** named *Index.cshtml* to the *Views/Home* folder.</span></span>

![새 항목 추가 대화 상자](mvc/_static/view.png)

<span data-ttu-id="69cf7-155">프로젝트 구조는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-155">The project structure is shown below:</span></span>

![파일 및 WebApp1의 폴더를 보여 주는 솔루션 탐색기](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="69cf7-157">내용을 대체 합니다 *Views/Home/Index.cshtml* 다음 파일:</span><span class="sxs-lookup"><span data-stu-id="69cf7-157">Replace the contents of the *Views/Home/Index.cshtml* file with the following:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="69cf7-158">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-158">Run the app.</span></span>

![Microsoft Edge에서 열린 웹 앱](mvc/_static/hello-world.png)

<span data-ttu-id="69cf7-160">참조 [컨트롤러](xref:mvc/controllers/actions) 하 고 [뷰](xref:mvc/views/overview) 자세한 내용은 합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-160">See [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview) for more information.</span></span>

<span data-ttu-id="69cf7-161">최소한의 작업 ASP.NET Core 프로젝트를 만들었으므로 ASP.NET MVC 프로젝트에서 기능을 마이그레이션 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-161">Now that we have a minimal working ASP.NET Core project, we can start migrating functionality from the ASP.NET MVC project.</span></span> <span data-ttu-id="69cf7-162">다음 이동 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-162">We need to move the following:</span></span>

* <span data-ttu-id="69cf7-163">클라이언트 쪽 콘텐츠 (CSS, 글꼴 및 스크립트)</span><span class="sxs-lookup"><span data-stu-id="69cf7-163">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="69cf7-164">컨트롤러</span><span class="sxs-lookup"><span data-stu-id="69cf7-164">controllers</span></span>

* <span data-ttu-id="69cf7-165">뷰</span><span class="sxs-lookup"><span data-stu-id="69cf7-165">views</span></span>

* <span data-ttu-id="69cf7-166">모델</span><span class="sxs-lookup"><span data-stu-id="69cf7-166">models</span></span>

* <span data-ttu-id="69cf7-167">번들</span><span class="sxs-lookup"><span data-stu-id="69cf7-167">bundling</span></span>

* <span data-ttu-id="69cf7-168">필터</span><span class="sxs-lookup"><span data-stu-id="69cf7-168">filters</span></span>

* <span data-ttu-id="69cf7-169">입/출력 로그, Id (이렇게 다음 자습서에서 합니다.)</span><span class="sxs-lookup"><span data-stu-id="69cf7-169">Log in/out, Identity (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="69cf7-170">컨트롤러 및 보기</span><span class="sxs-lookup"><span data-stu-id="69cf7-170">Controllers and views</span></span>

* <span data-ttu-id="69cf7-171">ASP.NET MVC에서 복사 된 각 메서드의 `HomeController` 새 `HomeController`합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-171">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="69cf7-172">ASP.NET mvc에서는 기본 제공 템플릿의 컨트롤러 동작 메서드 반환 형식입니다 [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); ASP.NET Core mvc에서 반환 하는 작업 메서드 `IActionResult` 대신 합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-172">Note that in ASP.NET MVC, the built-in template's controller action method return type is [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="69cf7-173">`ActionResult` 구현 `IActionResult`이므로 작업 메서드의 반환 형식을 변경할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-173">`ActionResult` implements `IActionResult`, so there's no need to change the return type of your action methods.</span></span>

* <span data-ttu-id="69cf7-174">복사 합니다 *About.cshtml*를 *Contact.cshtml*, 및 *Index.cshtml* ASP.NET Core 프로젝트에 ASP.NET MVC 프로젝트에서 Razor 뷰 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-174">Copy the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

* <span data-ttu-id="69cf7-175">ASP.NET Core 앱을 실행 하 고 각 메서드를 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-175">Run the ASP.NET Core app and test each method.</span></span> <span data-ttu-id="69cf7-176">아직 마이그레이션할 레이아웃 파일 또는 스타일을 아직 렌더링된 된 보기에만 뷰 파일의 내용이 포함 되도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-176">We haven't migrated the layout file or styles yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="69cf7-177">에 대 한 레이아웃 파일 생성 링크 하지 않아도 합니다 `About` 및 `Contact` 브라우저에서이 호출 해야 하므로 뷰 (대체 **4492** 프로젝트에서 사용 하는 포트 번호를 사용 하 여).</span><span class="sxs-lookup"><span data-stu-id="69cf7-177">You won't have the layout file generated links for the `About` and `Contact` views, so you'll have to invoke them from the browser (replace **4492** with the port number used in your project).</span></span>

  * `http://localhost:4492/home/about`

  * `http://localhost:4492/home/contact`

![연락처 페이지](mvc/_static/contact-page.png)

<span data-ttu-id="69cf7-179">스타일 지정 및 메뉴 항목의 부족을 note 합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-179">Note the lack of styling and menu items.</span></span> <span data-ttu-id="69cf7-180">다음 섹션에서 이 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-180">We'll fix that in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="69cf7-181">정적 콘텐츠</span><span class="sxs-lookup"><span data-stu-id="69cf7-181">Static content</span></span>

<span data-ttu-id="69cf7-182">ASP.NET MVC의 이전 버전에서는 정적 콘텐츠 웹 프로젝트의 루트에서 호스트 되 및 서버 쪽 파일을 사용 하 여 결합 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-182">In previous versions of ASP.NET MVC, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="69cf7-183">ASP.NET Core에서 정적 콘텐츠에서 호스트 되는 *wwwroot* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-183">In ASP.NET Core, static content is hosted in the *wwwroot* folder.</span></span> <span data-ttu-id="69cf7-184">이전 ASP.NET MVC 앱에서 정적 콘텐츠를 복사 하는 것이 좋습니다 합니다 *wwwroot* ASP.NET Core 프로젝트의 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-184">You'll want to copy the static content from your old ASP.NET MVC app to the *wwwroot* folder in your ASP.NET Core project.</span></span> <span data-ttu-id="69cf7-185">이 샘플 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-185">In this sample conversion:</span></span>

* <span data-ttu-id="69cf7-186">복사 합니다 *favicon.ico* 이전 MVC 프로젝트의 파일을 *wwwroot* ASP.NET Core 프로젝트의 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-186">Copy the *favicon.ico* file from the old MVC project to the *wwwroot* folder in the ASP.NET Core project.</span></span>

<span data-ttu-id="69cf7-187">이전 ASP.NET MVC 프로젝트에서 사용 [부트스트랩](https://getbootstrap.com/) 부트스트랩 파일 스타일 지정 및 저장 합니다 *콘텐츠* 및 *스크립트* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-187">The old ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* folders.</span></span> <span data-ttu-id="69cf7-188">부트스트랩 레이아웃 파일에서 참조 하는 기존 ASP.NET MVC 프로젝트를 생성 하는 템플릿을 (*Views/Shared/_Layout.cshtml*).</span><span class="sxs-lookup"><span data-stu-id="69cf7-188">The template, which generated the old ASP.NET MVC project, references Bootstrap in the layout file (*Views/Shared/_Layout.cshtml*).</span></span> <span data-ttu-id="69cf7-189">복사할 수 있습니다는 *bootstrap.js* 및 *bootstrap.css* 프로젝트를 ASP.NET MVC에서 파일을 *wwwroot* 새 프로젝트의 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-189">You could copy the *bootstrap.js* and *bootstrap.css* files from the ASP.NET MVC project to the *wwwroot* folder in the new project.</span></span> <span data-ttu-id="69cf7-190">대신, 다음 섹션에서 Cdn을 사용 하 여 부트스트랩에 대 한 지원 (및 기타 클라이언트 쪽 라이브러리) 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-190">Instead, we'll add support for Bootstrap (and other client-side libraries) using CDNs in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="69cf7-191">레이아웃 파일 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="69cf7-191">Migrate the layout file</span></span>

* <span data-ttu-id="69cf7-192">복사 합니다 *_ViewStart.cshtml* 이전 ASP.NET MVC 프로젝트에서 파일 *뷰* ASP.NET Core 프로젝트의 폴더 *뷰* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-192">Copy the *_ViewStart.cshtml* file from the old ASP.NET MVC project's *Views* folder into the ASP.NET Core project's *Views* folder.</span></span> <span data-ttu-id="69cf7-193">합니다 *_ViewStart.cshtml* ASP.NET Core MVC에서 파일 변경 되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-193">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="69cf7-194">만들기는 *Views/Shared* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-194">Create a *Views/Shared* folder.</span></span>

* <span data-ttu-id="69cf7-195">*선택 사항:* 복사본 *_ViewImports.cshtml* 에서 합니다 *FullAspNetCore* MVC 프로젝트 *뷰* ASP.NET Core 프로젝트의 폴더 *뷰* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-195">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* folder into the ASP.NET Core project's *Views* folder.</span></span> <span data-ttu-id="69cf7-196">모든 네임 스페이스 선언을 제거 합니다 *_ViewImports.cshtml* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-196">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="69cf7-197">합니다 *_ViewImports.cshtml* 파일 모든 보기 파일에 대 한 네임 스페이스를 제공 하 고는 [태그 도우미](xref:mvc/views/tag-helpers/intro)합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-197">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="69cf7-198">태그 도우미는 새 레이아웃 파일에서 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-198">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="69cf7-199">합니다 *_ViewImports.cshtml* 파일은 ASP.NET Core의 새로운 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-199">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="69cf7-200">복사 합니다 *_Layout.cshtml* 이전 ASP.NET MVC 프로젝트에서 파일 *Views/Shared* ASP.NET Core 프로젝트의 폴더 *Views/Shared* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-200">Copy the *_Layout.cshtml* file from the old ASP.NET MVC project's *Views/Shared* folder into the ASP.NET Core project's *Views/Shared* folder.</span></span>

<span data-ttu-id="69cf7-201">오픈 *_Layout.cshtml* 파일과 (완성 된 코드는 아래 참조) 다음과 같이 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-201">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="69cf7-202">바꿉니다 `@Styles.Render("~/Content/css")` 사용 하 여는 `<link>` 로드 하는 요소 *bootstrap.css* (아래 참조).</span><span class="sxs-lookup"><span data-stu-id="69cf7-202">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="69cf7-203">`@Scripts.Render("~/bundles/modernizr")`를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-203">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="69cf7-204">주석으로 처리 합니다 `@Html.Partial("_LoginPartial")` 줄 (줄을 둘러쌉니다 `@*...*@`).</span><span class="sxs-lookup"><span data-stu-id="69cf7-204">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="69cf7-205">자세한 내용은 참조 하세요. [인증 및 ASP.NET core Id 마이그레이션](xref:migration/identity)</span><span class="sxs-lookup"><span data-stu-id="69cf7-205">For more information see [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="69cf7-206">바꿉니다 `@Scripts.Render("~/bundles/jquery")` 사용 하 여는 `<script>` 요소 (아래 참조).</span><span class="sxs-lookup"><span data-stu-id="69cf7-206">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="69cf7-207">바꿉니다 `@Scripts.Render("~/bundles/bootstrap")` 사용 하 여는 `<script>` 요소 (아래 참조).</span><span class="sxs-lookup"><span data-stu-id="69cf7-207">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="69cf7-208">부트스트랩 CSS 포함에 대 한 대체 태그:</span><span class="sxs-lookup"><span data-stu-id="69cf7-208">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="69cf7-209">JQuery 및 부트스트랩 JavaScript 포함에 대 한 대체 태그:</span><span class="sxs-lookup"><span data-stu-id="69cf7-209">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="69cf7-210">업데이트 된 *_Layout.cshtml* 파일은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-210">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/sample/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="69cf7-211">브라우저에서 사이트를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-211">View the site in the browser.</span></span> <span data-ttu-id="69cf7-212">현재 위치에서 예상된 스타일을 사용 하 여는 올바르게 로드 이제 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-212">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="69cf7-213">*선택 사항:* 새 레이아웃 파일을 사용해이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-213">*Optional:* You might want to try using the new layout file.</span></span> <span data-ttu-id="69cf7-214">이 프로젝트에 대 한 레이아웃 파일을 복사할 수는 있지만 합니다 *FullAspNetCore* 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-214">For this project you can copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="69cf7-215">새 레이아웃 파일을 사용 하 여 [태그 도우미](xref:mvc/views/tag-helpers/intro) 있고 다른 향상 된 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-215">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="69cf7-216">번들링 및 축소 구성하기</span><span class="sxs-lookup"><span data-stu-id="69cf7-216">Configure bundling and minification</span></span>

<span data-ttu-id="69cf7-217">묶음 및 축소를 구성 하는 방법에 대 한 정보를 참조 하세요 [묶음 및 축소](../client-side/bundling-and-minification.md)합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-217">For information about how to configure bundling and minification, see [Bundling and Minification](../client-side/bundling-and-minification.md).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="69cf7-218">HTTP 500 오류를 해결 합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-218">Solve HTTP 500 errors</span></span>

<span data-ttu-id="69cf7-219">문제의 원인의 정보를 포함 하는 HTTP 500 오류 메시지를 발생 시킬 수 있는 많은 문제가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-219">There are many problems that can cause a HTTP 500 error message that contain no information on the source of the problem.</span></span> <span data-ttu-id="69cf7-220">예를 들어 경우는 *views/_viewimports.cshtml* 프로젝트에 존재 하지 않는 네임 스페이스를 포함 하는 파일, HTTP 500 오류를 받게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-220">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in your project, you'll get a HTTP 500 error.</span></span> <span data-ttu-id="69cf7-221">ASP.NET Core 앱에서 기본적으로는 `UseDeveloperExceptionPage` 에 확장을 추가 합니다 `IApplicationBuilder` 구성 된 경우 실행 *개발*합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-221">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development*.</span></span> <span data-ttu-id="69cf7-222">이 다음 코드에 자세히 설명 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-222">This is detailed in the following code:</span></span>

[!code-csharp[](mvc/sample/Startup.cs?highlight=19-22)]

<span data-ttu-id="69cf7-223">ASP.NET Core 웹 앱에서 처리 되지 않은 예외를 HTTP 500 오류 응답 변환.</span><span class="sxs-lookup"><span data-stu-id="69cf7-223">ASP.NET Core converts unhandled exceptions in a web app into HTTP 500 error responses.</span></span> <span data-ttu-id="69cf7-224">일반적으로 오류 정보는 서버에 대 한 잠재적으로 중요 한 정보의 노출을 방지 하기 위해 이러한 응답에 포함 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-224">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="69cf7-225">참조 **개발자 예외 페이지를 사용 하 여** 에 [오류를 처리](../fundamentals/error-handling.md) 자세한 내용은 합니다.</span><span class="sxs-lookup"><span data-stu-id="69cf7-225">See **Using the Developer Exception Page** in [Handle errors](../fundamentals/error-handling.md) for more information.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="69cf7-226">추가 자료</span><span class="sxs-lookup"><span data-stu-id="69cf7-226">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>
