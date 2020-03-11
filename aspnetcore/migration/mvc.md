---
title: ASP.NET MVC에서 ASP.NET Core MVC로 마이그레이션
author: ardalis
description: ASP.NET MVC 프로젝트를 ASP.NET Core MVC로 마이그레이션하는 방법에 대해 알아봅니다.
ms.author: riande
ms.date: 04/06/2019
uid: migration/mvc
ms.openlocfilehash: 6c9449fb43960d05db8aa6dcba64d3d830834cdb
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652551"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a><span data-ttu-id="13941-103">ASP.NET MVC에서 ASP.NET Core MVC로 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="13941-103">Migrate from ASP.NET MVC to ASP.NET Core MVC</span></span>

<span data-ttu-id="13941-104">작성자, [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/)및 [Scott addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="13941-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/), and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="13941-105">이 문서에서는 ASP.NET MVC 프로젝트를 [ASP.NET CORE mvc](../mvc/overview.md)로 마이그레이션하기 시작 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="13941-105">This article shows how to get started migrating an ASP.NET MVC project to [ASP.NET Core MVC](../mvc/overview.md).</span></span> <span data-ttu-id="13941-106">이 프로세스에서는 ASP.NET MVC에서 변경 된 많은 사항을 강조 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="13941-106">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="13941-107">ASP.NET MVC에서 마이그레이션은 다중 단계 프로세스 이며,이 문서에서는 초기 설정, 기본 컨트롤러 및 뷰, 정적 콘텐츠 및 클라이언트 쪽 종속성에 대해 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="13941-107">Migrating from ASP.NET MVC is a multiple step process and this article covers the initial setup, basic controllers and views, static content, and client-side dependencies.</span></span> <span data-ttu-id="13941-108">추가 문서에서는 많은 ASP.NET MVC 프로젝트에 있는 구성 및 id 코드를 마이그레이션하는 방법을 다룹니다.</span><span class="sxs-lookup"><span data-stu-id="13941-108">Additional articles cover migrating configuration and identity code found in many ASP.NET MVC projects.</span></span>

> [!NOTE]
> <span data-ttu-id="13941-109">샘플의 버전 번호는 최신 버전이 아닐 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="13941-109">The version numbers in the samples might not be current.</span></span> <span data-ttu-id="13941-110">프로젝트를 적절 하 게 업데이트 해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="13941-110">You may need to update your projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="13941-111">스타터 ASP.NET MVC 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="13941-111">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="13941-112">업그레이드를 시연 하기 위해 먼저 ASP.NET MVC 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="13941-112">To demonstrate the upgrade, we'll start by creating a ASP.NET MVC app.</span></span> <span data-ttu-id="13941-113">*WebApp1* 이름으로 만듭니다. 그러면 네임 스페이스는 다음 단계에서 만드는 ASP.NET Core 프로젝트와 일치 합니다.</span><span class="sxs-lookup"><span data-stu-id="13941-113">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project we create in the next step.</span></span>

![Visual Studio 새 프로젝트 대화 상자](mvc/_static/new-project.png)

![새 웹 응용 프로그램 대화 상자: ASP.NET 템플릿 패널에서 선택 된 MVC 프로젝트 템플릿](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="13941-116">*선택 사항:* 솔루션 이름을 *WebApp1* 에서 *Mvc5*로 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="13941-116">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5*.</span></span> <span data-ttu-id="13941-117">Visual Studio에서 새 솔루션 이름 (*Mvc5*)을 표시 합니다 .이를 통해 다음 프로젝트에서이 프로젝트를 더 쉽게 알 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="13941-117">Visual Studio displays the new solution name (*Mvc5*), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="13941-118">ASP.NET Core 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="13941-118">Create the ASP.NET Core project</span></span>

<span data-ttu-id="13941-119">두 프로젝트의 네임 스페이스가 일치 하도록 이전 프로젝트와 동일한 이름을 사용 하 여 *비어* 있는 ASP.NET Core 웹 앱 (*WebApp1*)을 새로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="13941-119">Create a new *empty* ASP.NET Core web app with the same name as the previous project (*WebApp1*) so the namespaces in the two projects match.</span></span> <span data-ttu-id="13941-120">동일한 네임 스페이스를 사용 하면 두 프로젝트 간에 코드를 쉽게 복사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="13941-120">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="13941-121">동일한 이름을 사용 하려면 이전 프로젝트가 아닌 다른 디렉터리에이 프로젝트를 만들어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="13941-121">You'll have to create this project in a different directory than the previous project to use the same name.</span></span>

![새 프로젝트 대화 상자](mvc/_static/new_core.png)

![New ASP.NET 웹 응용 프로그램 대화 상자: ASP.NET Core 템플릿 패널에서 빈 프로젝트 템플릿을 선택 했습니다.](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="13941-124">*선택 사항:* *웹 응용 프로그램* 프로젝트 템플릿을 사용 하 여 새 ASP.NET Core 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="13941-124">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="13941-125">프로젝트 이름을 *WebApp1*로 하 고 **개별 사용자 계정**에 대 한 인증 옵션을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="13941-125">Name the project *WebApp1*, and select an authentication option of **Individual User Accounts**.</span></span> <span data-ttu-id="13941-126">이 앱의 이름을 *FullAspNetCore*로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="13941-126">Rename this app to *FullAspNetCore*.</span></span> <span data-ttu-id="13941-127">이 프로젝트를 만들면 변환 시 시간이 절약 됩니다.</span><span class="sxs-lookup"><span data-stu-id="13941-127">Creating this project saves you time in the conversion.</span></span> <span data-ttu-id="13941-128">템플릿 생성 코드를 살펴보면 최종 결과를 보거나 변환 프로젝트에 코드를 복사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="13941-128">You can look at the template-generated code to see the end result or to copy code to the conversion project.</span></span> <span data-ttu-id="13941-129">템플릿 생성 프로젝트와 비교할 변환 단계에서 중단 되는 경우에도 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="13941-129">It's also helpful when you get stuck on a conversion step to compare with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="13941-130">MVC를 사용 하도록 사이트 구성</span><span class="sxs-lookup"><span data-stu-id="13941-130">Configure the site to use MVC</span></span>

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="13941-131">.NET Core를 대상으로 지정 하는 경우 [AspNetCore 메타 패키지](xref:fundamentals/metapackage-app) 가 기본적으로 참조 됩니다.</span><span class="sxs-lookup"><span data-stu-id="13941-131">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="13941-132">이 패키지는 MVC 앱에서 일반적으로 사용 하는 패키지를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="13941-132">This package contains packages commonly used by MVC apps.</span></span> <span data-ttu-id="13941-133">.NET Framework 대상으로 지정 하는 경우 패키지 참조는 프로젝트 파일에 개별적으로 나열 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="13941-133">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="13941-134">.NET Core를 대상으로 지정 하는 경우 [AspNetCore 메타 패키지](xref:fundamentals/metapackage) 는 기본적으로 참조 됩니다.</span><span class="sxs-lookup"><span data-stu-id="13941-134">When targeting .NET Core, the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) is referenced by default.</span></span> <span data-ttu-id="13941-135">이 패키지는 MVC 앱에서 일반적으로 사용 하는 패키지를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="13941-135">This package contains packages commonly used packages by MVC apps.</span></span> <span data-ttu-id="13941-136">.NET Framework 대상으로 지정 하는 경우 패키지 참조는 프로젝트 파일에 개별적으로 나열 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="13941-136">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* <span data-ttu-id="13941-137">.NET Core 또는 .NET Framework를 대상으로 지정 하는 경우 MVC 앱에서 자주 사용 하는 패키지는 프로젝트 파일에 개별적으로 나열 됩니다.</span><span class="sxs-lookup"><span data-stu-id="13941-137">When targeting .NET Core or .NET Framework, packages commonly used packages by MVC apps are listed individually in the project file.</span></span>

::: moniker-end

<span data-ttu-id="13941-138">`Microsoft.AspNetCore.Mvc`은 ASP.NET Core MVC 프레임 워크입니다.</span><span class="sxs-lookup"><span data-stu-id="13941-138">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="13941-139">`Microsoft.AspNetCore.StaticFiles`은 정적 파일 처리기입니다.</span><span class="sxs-lookup"><span data-stu-id="13941-139">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="13941-140">ASP.NET Core 런타임은 모듈식 이며 정적 파일을 제공 하기 위해 명시적으로 옵트인 해야 합니다 ( [정적 파일](xref:fundamentals/static-files)참조).</span><span class="sxs-lookup"><span data-stu-id="13941-140">The ASP.NET Core runtime is modular, and you must explicitly opt in to serve static files (see [Static files](xref:fundamentals/static-files)).</span></span>

* <span data-ttu-id="13941-141">*Startup.cs* 파일을 열고 다음 코드와 일치 하도록 코드를 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="13941-141">Open the *Startup.cs* file and change the code to match the following:</span></span>

  [!code-csharp[](mvc/sample/Startup.cs?highlight=13,26-31)]

<span data-ttu-id="13941-142">`UseStaticFiles` 확장 메서드는 정적 파일 처리기를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="13941-142">The `UseStaticFiles` extension method adds the static file handler.</span></span> <span data-ttu-id="13941-143">앞서 언급 했 듯이 ASP.NET 런타임은 모듈식 이며 정적 파일을 제공 하기 위해 명시적으로 옵트인 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="13941-143">As mentioned previously, the ASP.NET runtime is modular, and you must explicitly opt in to serve static files.</span></span> <span data-ttu-id="13941-144">`UseMvc` 확장 메서드는 라우팅을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="13941-144">The `UseMvc` extension method adds routing.</span></span> <span data-ttu-id="13941-145">자세한 내용은 [응용 프로그램 시작](xref:fundamentals/startup) 및 [라우팅](xref:fundamentals/routing)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="13941-145">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="13941-146">컨트롤러 및 뷰 추가</span><span class="sxs-lookup"><span data-stu-id="13941-146">Add a controller and view</span></span>

<span data-ttu-id="13941-147">이 섹션에서는 다음 섹션에서 마이그레이션할 ASP.NET MVC 컨트롤러 및 보기에 대 한 자리 표시자 역할을 하는 최소 컨트롤러 및 뷰를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="13941-147">In this section, you'll add a minimal controller and view to serve as placeholders for the ASP.NET MVC controller and views you'll migrate in the next section.</span></span>

* <span data-ttu-id="13941-148">*Controllers* 폴더를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="13941-148">Add a *Controllers* folder.</span></span>

* <span data-ttu-id="13941-149">*HomeController.cs* 라는 **컨트롤러 클래스** 를 *Controllers* 폴더에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="13941-149">Add a **Controller Class** named *HomeController.cs* to the *Controllers* folder.</span></span>

![새 항목 추가 대화 상자](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="13941-151">*Views* 폴더를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="13941-151">Add a *Views* folder.</span></span>

* <span data-ttu-id="13941-152">*보기/홈* 폴더를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="13941-152">Add a *Views/Home* folder.</span></span>

* <span data-ttu-id="13941-153">*Index. cshtml* 이라는 **Razor 뷰** 를 *Views/Home* 폴더에 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="13941-153">Add a **Razor View** named *Index.cshtml* to the *Views/Home* folder.</span></span>

![새 항목 추가 대화 상자](mvc/_static/view.png)

<span data-ttu-id="13941-155">프로젝트 구조는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="13941-155">The project structure is shown below:</span></span>

![WebApp1의 파일 및 폴더를 표시 하는 솔루션 탐색기](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="13941-157">*Views/Home/Index. cshtml* 파일의 내용을 다음으로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="13941-157">Replace the contents of the *Views/Home/Index.cshtml* file with the following:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="13941-158">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="13941-158">Run the app.</span></span>

![Microsoft Edge에서 웹 앱 열기](mvc/_static/hello-world.png)

<span data-ttu-id="13941-160">자세한 내용은 [컨트롤러](xref:mvc/controllers/actions) 및 [뷰](xref:mvc/views/overview) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="13941-160">See [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview) for more information.</span></span>

<span data-ttu-id="13941-161">이제 최소한의 작업 ASP.NET Core 프로젝트가 있으므로 ASP.NET MVC 프로젝트에서 기능 마이그레이션을 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="13941-161">Now that we have a minimal working ASP.NET Core project, we can start migrating functionality from the ASP.NET MVC project.</span></span> <span data-ttu-id="13941-162">다음을 이동 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="13941-162">We need to move the following:</span></span>

* <span data-ttu-id="13941-163">클라이언트 쪽 콘텐츠 (CSS, 글꼴 및 스크립트)</span><span class="sxs-lookup"><span data-stu-id="13941-163">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="13941-164">controllers</span><span class="sxs-lookup"><span data-stu-id="13941-164">controllers</span></span>

* <span data-ttu-id="13941-165">뷰</span><span class="sxs-lookup"><span data-stu-id="13941-165">views</span></span>

* <span data-ttu-id="13941-166">모델</span><span class="sxs-lookup"><span data-stu-id="13941-166">models</span></span>

* <span data-ttu-id="13941-167">묶음</span><span class="sxs-lookup"><span data-stu-id="13941-167">bundling</span></span>

* <span data-ttu-id="13941-168">filters</span><span class="sxs-lookup"><span data-stu-id="13941-168">filters</span></span>

* <span data-ttu-id="13941-169">로그인/로그 아웃, Id (다음 자습서에서 수행 됨)</span><span class="sxs-lookup"><span data-stu-id="13941-169">Log in/out, Identity (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="13941-170">컨트롤러 및 뷰</span><span class="sxs-lookup"><span data-stu-id="13941-170">Controllers and views</span></span>

* <span data-ttu-id="13941-171">ASP.NET MVC `HomeController`의 각 메서드를 새 `HomeController`에 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="13941-171">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="13941-172">ASP.NET MVC에서 기본 제공 템플릿의 컨트롤러 동작 메서드 반환 형식은 [Actionresult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx);입니다. ASP.NET Core MVC에서 작업 메서드는 대신 `IActionResult`을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="13941-172">Note that in ASP.NET MVC, the built-in template's controller action method return type is [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="13941-173">`ActionResult`에서 `IActionResult`를 구현 하므로 작업 메서드의 반환 형식을 변경할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="13941-173">`ActionResult` implements `IActionResult`, so there's no need to change the return type of your action methods.</span></span>

* <span data-ttu-id="13941-174">ASP.NET MVC 프로젝트에서 MVC ASP.NET Core 프로젝트에 *대 한* *파일을* *복사 합니다.*</span><span class="sxs-lookup"><span data-stu-id="13941-174">Copy the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

* <span data-ttu-id="13941-175">ASP.NET Core 앱을 실행 하 고 각 메서드를 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="13941-175">Run the ASP.NET Core app and test each method.</span></span> <span data-ttu-id="13941-176">레이아웃 파일이 나 스타일을 아직 마이그레이션하지 않았으므로 렌더링 된 뷰에는 뷰 파일의 내용만 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="13941-176">We haven't migrated the layout file or styles yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="13941-177">`About` 및 `Contact` 뷰에 대 한 레이아웃 파일 생성 링크가 없으므로 브라우저에서 호출 해야 합니다. **4492** 은 프로젝트에서 사용 되는 포트 번호로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="13941-177">You won't have the layout file generated links for the `About` and `Contact` views, so you'll have to invoke them from the browser (replace **4492** with the port number used in your project).</span></span>

  * `http://localhost:4492/home/about`

  * `http://localhost:4492/home/contact`

![연락처 페이지](mvc/_static/contact-page.png)

<span data-ttu-id="13941-179">스타일 지정 및 메뉴 항목이 없다는 점에 유의 하십시오.</span><span class="sxs-lookup"><span data-stu-id="13941-179">Note the lack of styling and menu items.</span></span> <span data-ttu-id="13941-180">다음 섹션에서 이 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="13941-180">We'll fix that in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="13941-181">정적 콘텐츠</span><span class="sxs-lookup"><span data-stu-id="13941-181">Static content</span></span>

<span data-ttu-id="13941-182">이전 버전의 ASP.NET MVC에서 정적 콘텐츠는 웹 프로젝트의 루트에서 호스팅되며 서버 쪽 파일과 혼합 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="13941-182">In previous versions of ASP.NET MVC, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="13941-183">ASP.NET Core에서 정적 콘텐츠는 *wwwroot* 폴더에 호스팅됩니다.</span><span class="sxs-lookup"><span data-stu-id="13941-183">In ASP.NET Core, static content is hosted in the *wwwroot* folder.</span></span> <span data-ttu-id="13941-184">이전 ASP.NET MVC 앱의 정적 콘텐츠를 ASP.NET Core 프로젝트의 *wwwroot* 폴더로 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="13941-184">You'll want to copy the static content from your old ASP.NET MVC app to the *wwwroot* folder in your ASP.NET Core project.</span></span> <span data-ttu-id="13941-185">이 샘플 변환에서:</span><span class="sxs-lookup"><span data-stu-id="13941-185">In this sample conversion:</span></span>

* <span data-ttu-id="13941-186">이전 MVC 프로젝트의 *favicon* 파일을 ASP.NET Core 프로젝트의 *wwwroot* 폴더로 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="13941-186">Copy the *favicon.ico* file from the old MVC project to the *wwwroot* folder in the ASP.NET Core project.</span></span>

<span data-ttu-id="13941-187">이전 ASP.NET MVC 프로젝트는 해당 스타일에 대해 [부트스트랩](https://getbootstrap.com/) 을 사용 하 고 *Content* 및 *Scripts* 폴더에 부트스트랩 파일을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="13941-187">The old ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* folders.</span></span> <span data-ttu-id="13941-188">이전 ASP.NET MVC 프로젝트를 생성 한 템플릿이 레이아웃 파일 (*Views/Shared/_Layout cshtml*)의 부트스트랩을 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="13941-188">The template, which generated the old ASP.NET MVC project, references Bootstrap in the layout file (*Views/Shared/_Layout.cshtml*).</span></span> <span data-ttu-id="13941-189">ASP.NET MVC 프로젝트에서 새 프로젝트의 *wwwroot* 폴더로 *부트스트랩 .js* 및 *부트스트랩 .css* 파일을 복사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="13941-189">You could copy the *bootstrap.js* and *bootstrap.css* files from the ASP.NET MVC project to the *wwwroot* folder in the new project.</span></span> <span data-ttu-id="13941-190">대신, 다음 섹션에서 CDNs를 사용 하 여 부트스트랩 (및 기타 클라이언트 쪽 라이브러리)에 대 한 지원을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="13941-190">Instead, we'll add support for Bootstrap (and other client-side libraries) using CDNs in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="13941-191">레이아웃 파일 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="13941-191">Migrate the layout file</span></span>

* <span data-ttu-id="13941-192">이전 ASP.NET MVC 프로젝트의 *views* 폴더에 있는 *_ViewStart cshtml* 파일을 ASP.NET Core 프로젝트의 *views* 폴더에 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="13941-192">Copy the *_ViewStart.cshtml* file from the old ASP.NET MVC project's *Views* folder into the ASP.NET Core project's *Views* folder.</span></span> <span data-ttu-id="13941-193">*_ViewStart cshtml* 파일은 ASP.NET Core MVC에서 변경 되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="13941-193">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="13941-194">*Views/Shared* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="13941-194">Create a *Views/Shared* folder.</span></span>

* <span data-ttu-id="13941-195">*선택 사항:* *FullAspNetCore* MVC 프로젝트의 *views* 폴더에 있는 *_ViewImports* 를 ASP.NET Core 프로젝트의 *views* 폴더에 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="13941-195">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* folder into the ASP.NET Core project's *Views* folder.</span></span> <span data-ttu-id="13941-196">*_ViewImports cshtml* 파일에서 네임 스페이스 선언을 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="13941-196">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="13941-197">*_ViewImports cshtml* 파일은 모든 뷰 파일의 네임 스페이스를 제공 하 고 [태그 도우미](xref:mvc/views/tag-helpers/intro)에 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="13941-197">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="13941-198">태그 도우미는 새 레이아웃 파일에 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="13941-198">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="13941-199">*_ViewImports* 파일은 ASP.NET Core의 새로운 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="13941-199">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="13941-200">이전 ASP.NET MVC 프로젝트의 *views/shared* 폴더에 있는 *_Layout cshtml* 파일을 ASP.NET Core 프로젝트의 *views/shared* 폴더에 복사 합니다.</span><span class="sxs-lookup"><span data-stu-id="13941-200">Copy the *_Layout.cshtml* file from the old ASP.NET MVC project's *Views/Shared* folder into the ASP.NET Core project's *Views/Shared* folder.</span></span>

<span data-ttu-id="13941-201">*_Layout cshtml* 파일을 열고 다음과 같이 변경 합니다 (완성 된 코드는 아래에 표시 됨).</span><span class="sxs-lookup"><span data-stu-id="13941-201">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="13941-202">`@Styles.Render("~/Content/css")`을 `<link>` 요소로 대체 하 여 *부트스트랩* 을 로드 합니다 (아래 참조).</span><span class="sxs-lookup"><span data-stu-id="13941-202">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="13941-203">`@Scripts.Render("~/bundles/modernizr")`를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="13941-203">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="13941-204">`@Html.Partial("_LoginPartial")` 줄을 주석으로 처리 합니다 (`@*...*@`로 줄 포함).</span><span class="sxs-lookup"><span data-stu-id="13941-204">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="13941-205">자세한 내용은 [ASP.NET Core 인증 및 Id 마이그레이션을](xref:migration/identity) 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="13941-205">For more information see [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="13941-206">`@Scripts.Render("~/bundles/jquery")`를 `<script>` 요소로 바꿉니다 (아래 참조).</span><span class="sxs-lookup"><span data-stu-id="13941-206">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="13941-207">`@Scripts.Render("~/bundles/bootstrap")`를 `<script>` 요소로 바꿉니다 (아래 참조).</span><span class="sxs-lookup"><span data-stu-id="13941-207">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="13941-208">부트스트랩 CSS 포함에 대 한 대체 태그는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="13941-208">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="13941-209">JQuery 및 부트스트랩 JavaScript 포함에 대 한 대체 태그는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="13941-209">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="13941-210">업데이트 된 *_Layout cshtml* 파일은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="13941-210">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/sample/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="13941-211">브라우저에서 사이트를 봅니다.</span><span class="sxs-lookup"><span data-stu-id="13941-211">View the site in the browser.</span></span> <span data-ttu-id="13941-212">이제 올바른 스타일을 적절히 로드 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="13941-212">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="13941-213">*선택 사항:* 새 레이아웃 파일을 사용해 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="13941-213">*Optional:* You might want to try using the new layout file.</span></span> <span data-ttu-id="13941-214">이 프로젝트의 경우 *FullAspNetCore* 프로젝트에서 레이아웃 파일을 복사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="13941-214">For this project you can copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="13941-215">새 레이아웃 파일은 [태그 도우미](xref:mvc/views/tag-helpers/intro) 를 사용 하 고 다른 향상 된 기능을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="13941-215">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="13941-216">번들링 및 축소 구성하기</span><span class="sxs-lookup"><span data-stu-id="13941-216">Configure bundling and minification</span></span>

<span data-ttu-id="13941-217">묶음 및 축소를 구성 하는 방법에 대 한 자세한 내용은 [묶음 및 축소](../client-side/bundling-and-minification.md)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="13941-217">For information about how to configure bundling and minification, see [Bundling and Minification](../client-side/bundling-and-minification.md).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="13941-218">HTTP 500 오류 해결</span><span class="sxs-lookup"><span data-stu-id="13941-218">Solve HTTP 500 errors</span></span>

<span data-ttu-id="13941-219">문제의 원인에 대 한 정보를 포함 하지 않는 HTTP 500 오류 메시지를 발생 시킬 수 있는 많은 문제가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="13941-219">There are many problems that can cause a HTTP 500 error message that contain no information on the source of the problem.</span></span> <span data-ttu-id="13941-220">예를 들어 *Views/_ViewImports cshtml* 파일에 프로젝트에 없는 네임 스페이스가 포함 된 경우 HTTP 500 오류가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="13941-220">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in your project, you'll get a HTTP 500 error.</span></span> <span data-ttu-id="13941-221">기본적으로 ASP.NET Core apps에서는 `UseDeveloperExceptionPage` 확장이 `IApplicationBuilder`에 추가 되 고 구성이 *개발*될 때 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="13941-221">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development*.</span></span> <span data-ttu-id="13941-222">다음 코드에 자세히 설명 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="13941-222">This is detailed in the following code:</span></span>

[!code-csharp[](mvc/sample/Startup.cs?highlight=19-22)]

<span data-ttu-id="13941-223">ASP.NET Core 웹 앱에서 처리 되지 않은 예외를 HTTP 500 오류 응답으로 변환 합니다.</span><span class="sxs-lookup"><span data-stu-id="13941-223">ASP.NET Core converts unhandled exceptions in a web app into HTTP 500 error responses.</span></span> <span data-ttu-id="13941-224">일반적으로 서버에 대 한 잠재적으로 중요 한 정보를 노출 하지 않도록 하기 위해 오류 정보는 이러한 응답에 포함 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="13941-224">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="13941-225">자세한 내용은 [오류 처리](../fundamentals/error-handling.md) 에서 **개발자 예외 페이지 사용** 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="13941-225">See **Using the Developer Exception Page** in [Handle errors](../fundamentals/error-handling.md) for more information.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="13941-226">추가 리소스</span><span class="sxs-lookup"><span data-stu-id="13941-226">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>
