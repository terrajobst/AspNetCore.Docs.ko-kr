---
title: ASP.NET Core의 Razor 페이지 소개
author: Rick-Anderson
description: 페이지 코딩 중심의 시나리오에서 ASP.NET Core의 Razor 페이지를 사용하면 MVC를 사용할 때보다 어떻게 더 쉽고 생산적인지 알아봅니다.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 04/06/2019
uid: razor-pages/index
ms.openlocfilehash: 419355d670536fef1a38fbcb8ce1fd880c0e9b0d
ms.sourcegitcommit: d6e51c60439f03a8992bda70cc982ddb15d3f100
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555728"
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a><span data-ttu-id="78f9c-103">ASP.NET Core의 Razor 페이지 소개</span><span class="sxs-lookup"><span data-stu-id="78f9c-103">Introduction to Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="78f9c-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="78f9c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="78f9c-105">Razor 페이지는 페이지 코딩 중심의 시나리오를 보다 쉽고 생산적으로 만들어주는 ASP.NET Core MVC의 새로운 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-105">Razor Pages is a new aspect of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="78f9c-106">모델-뷰-컨트롤러 방식을 사용하는 자습서를 찾고 있다면 [ASP.NET Core MVC 시작하기](xref:tutorials/first-mvc-app/start-mvc)를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="78f9c-107">이 문서에서는 Razor 페이지에 대한 소개를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="78f9c-108">단계별 자습서가 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="78f9c-109">Visual Studio를 사용하여 Razor 페이지 프로젝트를 만드는 방법에 대한 자세한 내용은 [Razor 페이지 시작하기](xref:tutorials/razor-pages/razor-pages-start)를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="78f9c-110">ASP.NET Core에 대한 개요는 [ASP.NET Core 소개](xref:index)를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="78f9c-111">전제 조건</span><span class="sxs-lookup"><span data-stu-id="78f9c-111">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="78f9c-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="78f9c-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="78f9c-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="78f9c-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="78f9c-114">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="78f9c-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="78f9c-115">Razor Pages 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="78f9c-115">Create a Razor Pages project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="78f9c-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="78f9c-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="78f9c-117">Razor Pages 프로젝트를 만드는 방법에 대한 자세한 내용은 [Razor Pages 시작](xref:tutorials/razor-pages/razor-pages-start)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="78f9c-117">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="78f9c-118">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="78f9c-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="78f9c-119">명령줄에서 `dotnet new webapp`을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-119">Run `dotnet new webapp` from the command line.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="78f9c-120">명령줄에서 `dotnet new razor`을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-120">Run `dotnet new razor` from the command line.</span></span>

::: moniker-end

<span data-ttu-id="78f9c-121">Mac용 Visual Studio에서 생성된 *.csproj* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-121">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="78f9c-122">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="78f9c-122">Visual Studio Code</span></span>](#tab/visual-studio-code)

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="78f9c-123">명령줄에서 `dotnet new webapp`을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-123">Run `dotnet new webapp` from the command line.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="78f9c-124">명령줄에서 `dotnet new razor`을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-124">Run `dotnet new razor` from the command line.</span></span>

::: moniker-end

---

## <a name="razor-pages"></a><span data-ttu-id="78f9c-125">Razor 페이지</span><span class="sxs-lookup"><span data-stu-id="78f9c-125">Razor Pages</span></span>

<span data-ttu-id="78f9c-126">Razor 페이지는 *Startup.cs*에서 사용할 수 있게 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-126">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="78f9c-127">기본적인 페이지를 살펴보겠습니다. <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="78f9c-127">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="78f9c-128">앞의 코드는 컨트롤러 및 뷰가 포함된 ASP.NET Core 앱에서 사용되는 [Razor 뷰 파일](xref:tutorials/first-mvc-app/adding-view)과 매우 유사합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-128">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="78f9c-129">차이점은 `@page` 지시문입니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-129">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="78f9c-130">`@page`는 파일을 MVC 액션으로 만드는데, 이 말은 컨트롤러를 거치지 않고 이 파일이 요청을 직접 처리한다는 뜻입니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-130">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="78f9c-131">`@page`는 페이지의 첫 번째 Razor 지시문이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-131">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="78f9c-132">`@page`는 다른 Razor 구조의 동작에 영향을 줍니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-132">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="78f9c-133">다음 두 파일은 `PageModel` 클래스를 사용하는 비슷한 페이지를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-133">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="78f9c-134">*Pages/Index2.cshtml* 파일은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-134">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="78f9c-135">*Pages/Index2.cshtml.cs* 페이지 모델은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-135">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="78f9c-136">규약에 따라 `PageModel` 클래스 파일의 이름은 *.cs*가 추가된 Razor 페이지 파일의 이름과 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-136">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="78f9c-137">예를 들어 위의 Razor 페이지는 *Pages/Index2.cshtml*입니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-137">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="78f9c-138">그리고 `PageModel` 클래스가 포함된 파일의 이름은 *Pages/Index2.cshtml.cs*입니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-138">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="78f9c-139">페이지에 대한 URL 경로 연결은 파일 시스템 상의 페이지 위치에 따라 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-139">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="78f9c-140">다음 표는 Razor 페이지 경로 및 그와 일치하는 URL을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-140">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="78f9c-141">파일 이름 및 경로</span><span class="sxs-lookup"><span data-stu-id="78f9c-141">File name and path</span></span>               | <span data-ttu-id="78f9c-142">일치하는 URL</span><span class="sxs-lookup"><span data-stu-id="78f9c-142">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="78f9c-143">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="78f9c-143">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="78f9c-144">`/` 또는 `/Index`</span><span class="sxs-lookup"><span data-stu-id="78f9c-144">`/` or `/Index`</span></span> |
| <span data-ttu-id="78f9c-145">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="78f9c-145">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="78f9c-146">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="78f9c-146">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="78f9c-147">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="78f9c-147">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="78f9c-148">`/Store` 또는 `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="78f9c-148">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="78f9c-149">메모:</span><span class="sxs-lookup"><span data-stu-id="78f9c-149">Notes:</span></span>

* <span data-ttu-id="78f9c-150">런타임은 기본적으로 *Pages* 폴더에서 Razor 페이지 파일을 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-150">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="78f9c-151">URL에 페이지가 지정되어 있지 않을 경우 `Index`가 기본 페이지입니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-151">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="78f9c-152">기본적인 양식 작성하기</span><span class="sxs-lookup"><span data-stu-id="78f9c-152">Write a basic form</span></span>

<span data-ttu-id="78f9c-153">Razor 페이지는 앱을 만들때 웹 브라우저에서 사용되는 일반적인 패턴을 손쉽게 구현할 수 있도록 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-153">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="78f9c-154">[모델 바인딩](xref:mvc/models/model-binding), [태그 도우미](xref:mvc/views/tag-helpers/intro) 및 HTML 도우미는 모두 Razor 페이지 클래스에 정의된 속성을 통해서 *정확하게 동작*합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-154">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="78f9c-155">`Contact` 모델에 대한 기본적인 "연락처" 양식을 구현하는 페이지를 생각해보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-155">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="78f9c-156">이 문서의 예제에서 `DbContext`는 [Startup.cs](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) 파일에서 초기화됩니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-156">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="78f9c-157">데이터 모델은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-157">The data model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="78f9c-158">db 컨텍스트는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-158">The db context:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="78f9c-159">*Pages/Create.cshtml* 뷰 파일은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-159">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="78f9c-160">*Pages/Create.cshtml.cs* 페이지 모델은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-160">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="78f9c-161">규약에 따라 `PageModel` 클래스의 이름은 `<PageName>Model`로 지정하며 페이지와 동일한 네임스페이스에 위치합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-161">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="78f9c-162">`PageModel` 클래스를 사용하면 페이지의 논리를 페이지의 표현으로부터 분리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-162">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="78f9c-163">이 클래스는 페이지로 전송된 요청에 대한 페이지 처리기와 페이지를 렌더링하기 위해 사용되는 데이터를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-163">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="78f9c-164">이렇게 분리함으로써 [종속성 주입](xref:fundamentals/dependency-injection)을 통해서 페이지의 종속성을 관리할 수 있고 페이지를 [단위 테스트](xref:test/razor-pages-tests) 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-164">This separation allows you to manage page dependencies through [dependency injection](xref:fundamentals/dependency-injection) and to [unit test](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="78f9c-165">이 페이지는 `POST` 요청 시(사용자가 양식을 게시할 때) 실행되는 `OnPostAsync` *처리기 메서드*를 갖고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-165">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="78f9c-166">모든 HTTP 동사에 대한 처리기 메서드를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-166">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="78f9c-167">가장 일반적인 처리기는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-167">The most common handlers are:</span></span>

* <span data-ttu-id="78f9c-168">`OnGet`: 페이지에 필요한 상태를 초기화합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-168">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="78f9c-169">[OnGet](#OnGet) 예제.</span><span class="sxs-lookup"><span data-stu-id="78f9c-169">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="78f9c-170">`OnPost`: 양식 제출을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-170">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="78f9c-171">`Async` 명명 접미사는 선택 사항이지만 비동기 함수에 대한 규약으로 자주 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-171">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="78f9c-172">위의 예제에 사용된 `OnPostAsync`의 코드는 일반적으로 컨트롤러에서 작성하던 코드와 비슷해 보입니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-172">The `OnPostAsync` code in the preceding example looks similar to what you would normally write in a controller.</span></span> <span data-ttu-id="78f9c-173">위의 코드는 Razor 페이지의 일반적인 코드입니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-173">The preceding code is typical for Razor Pages.</span></span> <span data-ttu-id="78f9c-174">[모델 바인딩](xref:mvc/models/model-binding), [유효성 검사](xref:mvc/models/validation) 및 액션 결과 같은 MVC의 기본적인 기능들이 대부분 공유됩니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-174">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results are shared.</span></span>  <!-- Review: Ryan, can we get a list of what is shared and what isn't? -->

<span data-ttu-id="78f9c-175">위의 `OnPostAsync` 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-175">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="78f9c-176">그리고 `OnPostAsync`의 기본적인 흐름은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-176">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="78f9c-177">유효성 검사 오류를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-177">Check for validation errors.</span></span>

* <span data-ttu-id="78f9c-178">오류가 없는 경우 데이터를 저장하고 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-178">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="78f9c-179">오류가 있을 경우 유효성 검사 메시지가 포함된 페이지를 다시 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-179">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="78f9c-180">클라이언트 측 유효성 검사는 기존의 ASP.NET Core MVC 애플리케이션과 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-180">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="78f9c-181">대부분의 경우 유효성 검사 오류는 클라이언트에서 감지되어 서버에는 제출되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-181">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="78f9c-182">데이터가 성공적으로 입력되면 `OnPostAsync` 처리기 메서드가 `RedirectToPage` 도우미 메서드를 호출하여 `RedirectToPageResult`의 인스턴스를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-182">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="78f9c-183">`RedirectToPage`는 `RedirectToAction`이나 `RedirectToRoute`와 비슷하지만 페이지에 맞춰진 새로운 액션 결과입니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-183">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="78f9c-184">위의 예제에서 이 메서드는 루트 인덱스 페이지(`/Index`)로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-184">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="78f9c-185">`RedirectToPage`는 [페이지에 대한 URL 생성](#url_gen) 섹션에서 자세히 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-185">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="78f9c-186">서버로 전달된 제출 양식에 유효성 검사 오류가 존재할 경우 `OnPostAsync` 처리기 메서드가 `Page` 도우미 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-186">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="78f9c-187">`Page`는 `PageResult`의 인스턴스를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-187">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="78f9c-188">`Page`를 반환하는 것은 컨트롤의 액션에서 `View`를 반환하는 것과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-188">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="78f9c-189">`PageResult`는</span><span class="sxs-lookup"><span data-stu-id="78f9c-189">`PageResult` is the default</span></span> <!-- Review  --> <span data-ttu-id="78f9c-190">처리기 메서드의 기본 반환 형식입니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-190">return type for a handler method.</span></span> <span data-ttu-id="78f9c-191">`void`를 반환하는 처리기 메서드는 페이지를 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-191">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="78f9c-192">`Customer` 속성은 `[BindProperty]` 특성을 이용해서 모델 바인딩 대상으로 명시적으로 지정(opt in)합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-192">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="78f9c-193">Razor 페이지는 기본적으로 비 GET 동사에 대해서만 속성을 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-193">Razor Pages, by default, bind properties only with non-GET verbs.</span></span> <span data-ttu-id="78f9c-194">속성을 바인딩하면 작성해야 하는 코드의 양을 줄일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-194">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="78f9c-195">바인딩은 양식 필드 렌더링할 때와 (`<input asp-for="Customer.Name">`) 입력을 받아들일 때 동일한 속성을 사용하여 코드를 줄입니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-195">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="78f9c-196">홈페이지(*Index.cshtml*)는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-196">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="78f9c-197">연결된 `PageModel` 클래스(*Index.cshtml.cs*)는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-197">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="78f9c-198">*Index.cshtml* 파일에는 각 연락처에 대한 편집 링크를 만들기 위한 다음의 태그가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-198">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="78f9c-199">이 [앵커 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)는 Edit 페이지에 대한 링크를 생성하기 위해 `asp-route-{value}` 특성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-199">The [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="78f9c-200">링크에는 연락처 ID와 함께 경로 데이터가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-200">The link contains route data with the contact ID.</span></span> <span data-ttu-id="78f9c-201">예를 들어, `http://localhost:5000/Edit/1`을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-201">For example, `http://localhost:5000/Edit/1`.</span></span> <span data-ttu-id="78f9c-202">`asp-area` 특성을 사용하여 영역을 지정하세요.</span><span class="sxs-lookup"><span data-stu-id="78f9c-202">Use the `asp-area` attribute to specify an area.</span></span> <span data-ttu-id="78f9c-203">자세한 내용은 <xref:mvc/controllers/areas>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="78f9c-203">For more information, see <xref:mvc/controllers/areas>.</span></span>

<span data-ttu-id="78f9c-204">*Pages/Edit.cshtml* 파일은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-204">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="78f9c-205">첫 번째 줄에는 `@page "{id:int}"` 지시문이 작성되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-205">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="78f9c-206">라우팅 제약 조건인 `"{id:int}"`는 `int` 경로 데이터가 포함된 이 페이지에 대한 요청만 허용하도록 페이지에 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-206">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="78f9c-207">페이지에 대한 요청에 `int`로 변환할 수 있는 경로 데이터가 없으면 런타임이 HTTP 404 (찾을 수 없음) 오류를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-207">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="78f9c-208">ID를 옵션으로 설정하려면 경로 제약 조건에 `?`를 추가하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-208">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="78f9c-209">*Pages/Edit.cshtml.cs* 파일은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-209">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="78f9c-210">또한 *Index.cshtml* 파일에는 각 고객 연락처에 대한 삭제 버튼을 만들기 위한 태그가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-210">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="78f9c-211">삭제 단추가 HTML로 렌더링될 때 해당 태그의 `formaction`에는 다음에 관한 매개 변수가 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-211">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="78f9c-212">`asp-route-id` 특성으로 지정된 고객 연락처의 ID.</span><span class="sxs-lookup"><span data-stu-id="78f9c-212">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="78f9c-213">`asp-page-handler` 특성으로 지정된 `handler`</span><span class="sxs-lookup"><span data-stu-id="78f9c-213">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="78f9c-214">고객 연락처 ID `1`에 대해 렌더링된 삭제 단추의 예는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-214">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="78f9c-215">단추를 선택하면 양식의 `POST` 요청이 서버로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-215">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="78f9c-216">규약에 따라 처리기 메서드의 이름은 `handler` 매개 변수의 값을 기반으로 `OnPost[handler]Async` 체계에 의해 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-216">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="78f9c-217">이번 예제에서는 `handler`가 `delete`이므로 `POST` 요청을 처리하기 위해 `OnPostDeleteAsync` 처리기 메서드가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-217">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="78f9c-218">`asp-page-handler`가 `remove` 같은 다른 값으로 설정되면 `OnPostRemoveAsync`라는 이름의 페이지 처리기 메서드가 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-218">If the `asp-page-handler` is set to a different value, such as `remove`, a page handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="78f9c-219">`OnPostDeleteAsync` 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-219">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="78f9c-220">쿼리 문자열에서 `id`를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-220">Accepts the `id` from the query string.</span></span>
* <span data-ttu-id="78f9c-221">`FindAsync`를 사용하여 데이터베이스에서 고객 연락처를 쿼리합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-221">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="78f9c-222">고객 연락처를 찾으면 고객 연락처 목록에서 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-222">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="78f9c-223">데이터베이스를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-223">The database is updated.</span></span>
* <span data-ttu-id="78f9c-224">`RedirectToPage`를 호출하여 루트 인덱스 페이지(`/Index`)로 리디렉션합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-224">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="78f9c-225">페이지 속성을 필수로 표시하기</span><span class="sxs-lookup"><span data-stu-id="78f9c-225">Mark page properties as required</span></span>

<span data-ttu-id="78f9c-226">`PageModel`의 속성에는 [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) 특성을 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-226">Properties on a `PageModel` can be decorated with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-cs[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="78f9c-227">자세한 내용은 [모델 유효성 검사](xref:mvc/models/validation)를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-227">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="manage-head-requests-with-the-onget-handler"></a><span data-ttu-id="78f9c-228">OnGet 처리기로 HEAD 요청 관리하기</span><span class="sxs-lookup"><span data-stu-id="78f9c-228">Manage HEAD requests with the OnGet handler</span></span>

<span data-ttu-id="78f9c-229">HEAD 요청을 사용하면 특정 리소스의 헤더를 검색할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-229">HEAD requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="78f9c-230">GET 요청과는 달리 HEAD 요청은 응답 본문을 반환하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-230">Unlike GET requests, HEAD requests don't return a response body.</span></span>

<span data-ttu-id="78f9c-231">일반적으로 HEAD 요청에 대한 HEAD 처리기를 만들고 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-231">Ordinarily, a HEAD handler is created and called for HEAD requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="78f9c-232">만약 정의된 HEAD 처리기(`OnHead`)가 없다면 ASP.NET Core 2.1 이상에서는 Razor 페이지가 GET 페이지 처리기(`OnGet`) 호출로 대체합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-232">If no HEAD handler (`OnHead`) is defined, Razor Pages falls back to calling the GET page handler (`OnGet`) in ASP.NET Core 2.1 or later.</span></span> <span data-ttu-id="78f9c-233">ASP.NET Core 2.1 및 2.2에서 이 동작은 `Startup.Configure`의 [SetCompatibilityVersion](xref:mvc/compatibility-version)에 의해서 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-233">In ASP.NET Core 2.1 and 2.2, this behavior occurs with the [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.Configure`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="78f9c-234">기본 템플릿은 ASP.NET Core 2.1 및 2.2에서 `SetCompatibilityVersion` 호출을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-234">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span>

<span data-ttu-id="78f9c-235">`SetCompatibilityVersion`은 효과적으로 Razor 페이지 옵션 `AllowMappingHeadRequestsToGetHandler`를 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-235">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="78f9c-236">`SetCompatibilityVersion`을 사용하여 2.1의 모든 동작을 허용하는 대신 명시적으로 특정 동작을 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-236">Rather than opting into all 2.1 behaviors with `SetCompatibilityVersion`, you can explicitly opt-in to specific behaviors.</span></span> <span data-ttu-id="78f9c-237">다음 코드는 HEAD 요청을 GET 처리기로 매핑하는 것을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-237">The following code opts into the mapping HEAD requests to the GET handler.</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

::: moniker-end

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="78f9c-238">XSRF/CSRF 및 Razor 페이지</span><span class="sxs-lookup"><span data-stu-id="78f9c-238">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="78f9c-239">[위조 방지 유효성 검사](xref:security/anti-request-forgery)에 대한 어떠한 코드도 작성할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-239">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="78f9c-240">위조 방지 토큰 생성 및 유효성 검사는 Razor 페이지에 자동으로 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-240">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="78f9c-241">Razor 페이지에서 레이아웃, 부분 뷰, 템플릿 및 태그 도우미 사용하기</span><span class="sxs-lookup"><span data-stu-id="78f9c-241">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="78f9c-242">페이지는 Razor 뷰 엔진의 모든 기능과 함께 동작합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-242">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="78f9c-243">레이아웃, 부분 뷰, 템플릿, 태그 도우미, *_ViewStart.cshtml*, *_ViewImports.cshtml*은 기존의 Razor 뷰와 동일한 방식으로 동작합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-243">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="78f9c-244">이 기능들 중 일부를 활용하여 페이지를 개선해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-244">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="78f9c-245">[레이아웃 페이지](xref:mvc/views/layout)를 *Pages/Shared/_Layout.cshtml*에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-245">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="78f9c-246">[레이아웃 페이지](xref:mvc/views/layout)를 *Pages/_Layout.cshtml*에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-246">Add a [layout page](xref:mvc/views/layout) to *Pages/_Layout.cshtml*:</span></span>

::: moniker-end

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="78f9c-247">[레이아웃](xref:mvc/views/layout)은 다음과 같은 역할을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-247">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="78f9c-248">각 페이지의 레이아웃을 제어합니다(페이지가 명시적으로 레이아웃을 사용하지 않는 경우 제외).</span><span class="sxs-lookup"><span data-stu-id="78f9c-248">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="78f9c-249">JavaScript 및 스타일시트 같은 HTML 구조를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-249">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="78f9c-250">자세한 내용은 [레이아웃 페이지](xref:mvc/views/layout)를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-250">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="78f9c-251">[Layout](xref:mvc/views/layout#specifying-a-layout) 속성은 *Pages/_ViewStart.cshtml*에서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-251">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="78f9c-252">레이아웃은 *Pages/Shared* 폴더에 위치합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-252">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="78f9c-253">페이지는 현재 페이지와 동일한 폴더에서부터 시작하여 계층적으로 다른 뷰(레이아웃, 템플릿, 부분 뷰)들을 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-253">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="78f9c-254">*Pages/Shared* 폴더의 레이아웃은 *Pages* 폴더 하위의 모든 Razor 페이지에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-254">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="78f9c-255">레이아웃 파일은 *Pages/Shared* 폴더에 위치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-255">The layout file should go in the *Pages/Shared* folder.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="78f9c-256">레이아웃은 *Pages* 폴더에 위치합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-256">The layout is in the *Pages* folder.</span></span> <span data-ttu-id="78f9c-257">페이지는 현재 페이지와 동일한 폴더에서부터 시작하여 계층적으로 다른 뷰들(레이아웃, 템플릿, 부분 뷰)을 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-257">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="78f9c-258">*Pages* 폴더의 레이아웃은 *Pages* 폴더 하위의 모든 Razor 페이지에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-258">A layout in the *Pages* folder can be used from any Razor page under the *Pages* folder.</span></span>

::: moniker-end

<span data-ttu-id="78f9c-259">레이아웃 파일은 *Views/Shared* 폴더에 두지 **않는** 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-259">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="78f9c-260">*Views/Shared*는 MVC 뷰 패턴입니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-260">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="78f9c-261">Razor 페이지는 경로 규약이 아닌 폴더 계층 구조를 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-261">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="78f9c-262">Razor 페이지의 뷰 검색에는 *Pages* 폴더가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-262">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="78f9c-263">MVC 컨트롤러 및 기존 Razor 뷰에서 사용 중인 레이아웃, 템플릿 및 부분 뷰는 *정상적으로 동작*합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-263">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="78f9c-264">*Pages/_ViewImports.cshtml* 파일을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-264">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="78f9c-265">`@namespace`는 잠시 뒤에 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-265">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="78f9c-266">`@addTagHelper` 지시문은 [기본 제공 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/Index)를 *Pages* 폴더의 모든 페이지에 도입합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-266">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="78f9c-267">`@namespace` 지시문을 페이지에서 명시적으로 사용하면 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-267">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="78f9c-268">이 지시문은 페이지에 대한 네임스페이스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-268">The directive sets the namespace for the page.</span></span> <span data-ttu-id="78f9c-269">이렇게 하면 `@model` 지시문은 네임스페이스를 포함할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-269">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="78f9c-270">`@namespace` 지시문이 *_ViewImports.cshtml*에 포함되어 있으면 지정된 네임스페이스가 `@namespace` 지시문을 가져오는 페이지에서 생성된 네임스페이스에 대한 접두사를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-270">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="78f9c-271">생성된 네임스페이스의 나머지 부분(접미사 부분)은 *_ViewImports.cshtml*이 위치한 폴더와 페이지가 위치한 폴더 간의 점으로 구분된 상대 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-271">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="78f9c-272">예를 들어 `PageModel` 클래스 *Pages/Customers/Edit.cshtml.cs*는 네임스페이스를 명시적으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-272">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="78f9c-273">*Pages/_ViewImports.cshtml* 파일에서는 다음과 같이 네임스페이스를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-273">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="78f9c-274">*Pages/Customers/Edit.cshtml* Razor 페이지에 대해 생성되는 네임스페이스는 `PageModel` 클래스와 동일합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-274">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="78f9c-275">`@namespace`*는 기존 Razor 뷰에서도 동작합니다.*</span><span class="sxs-lookup"><span data-stu-id="78f9c-275">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="78f9c-276">기존의 *Pages/Create.cshtml* 뷰 파일은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-276">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="78f9c-277">변경된 *Pages/Create.cshtml* 뷰 파일은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-277">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="78f9c-278">[Razor 페이지 시작 프로젝트](#rpvs17)에는 클라이언트 측 유효성 검사를 연결하는 *Pages/_ValidationScriptsPartial.cshtml*이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-278">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="78f9c-279">부분 뷰에 대한 자세한 내용은 <xref:mvc/views/partial>를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-279">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="78f9c-280">페이지에 대한 URL 생성하기</span><span class="sxs-lookup"><span data-stu-id="78f9c-280">URL generation for Pages</span></span>

<span data-ttu-id="78f9c-281">앞에서 살펴본 `Create` 페이지는 `RedirectToPage`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-281">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="78f9c-282">예제 앱은 다음과 같은 파일/폴더 구조를 갖고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-282">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="78f9c-283">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="78f9c-283">*/Pages*</span></span>

  * <span data-ttu-id="78f9c-284">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="78f9c-284">*Index.cshtml*</span></span>
  * <span data-ttu-id="78f9c-285">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="78f9c-285">*/Customers*</span></span>

    * <span data-ttu-id="78f9c-286">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="78f9c-286">*Create.cshtml*</span></span>
    * <span data-ttu-id="78f9c-287">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="78f9c-287">*Edit.cshtml*</span></span>
    * <span data-ttu-id="78f9c-288">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="78f9c-288">*Index.cshtml*</span></span>

<span data-ttu-id="78f9c-289">*Pages/Customers/Create.cshtml* 및 *Pages/Customers/Edit.cshtml* 페이지는 정상적으로 작업을 마친 후 *Pages/Index.cshtml*로 리디렉션됩니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-289">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="78f9c-290">문자열 `/Index`는 이전 페이지에 접근하기 위한 URI의 일부입니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-290">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="78f9c-291">문자열 `/Index`는 *Pages/Index.cshtml* 페이지에 대한 URI를 생성하기 위해 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-291">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="78f9c-292">예들 들어 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-292">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="78f9c-293">페이지 이름은 선행 `/`를 포함하는 루트 */Pages* 폴더로부터 시작되는 페이지에 대한 경로입니다(예: `/Index`).</span><span class="sxs-lookup"><span data-stu-id="78f9c-293">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="78f9c-294">위의 URL 생성 예제는 URL 하드 코딩에 비해 향상된 옵션과 기능적 성능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-294">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="78f9c-295">URL 생성에는 [라우팅](xref:mvc/controllers/routing)이 사용되며 경로가 대상 경로에서 정의되는 방식에 따라 매개 변수를 생성하고 인코딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-295">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="78f9c-296">페이지에 대한 URL 생성은 상대적 이름을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-296">URL generation for pages supports relative names.</span></span> <span data-ttu-id="78f9c-297">다음 표는 *Pages/Customers/Create.cshtml*에서 다른 `RedirectToPage` 매개 변수를 사용할 때 어떤 인덱스 페이지가 선택되는지를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-297">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="78f9c-298">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="78f9c-298">RedirectToPage(x)</span></span>| <span data-ttu-id="78f9c-299">페이지</span><span class="sxs-lookup"><span data-stu-id="78f9c-299">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="78f9c-300">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="78f9c-300">RedirectToPage("/Index")</span></span> | <span data-ttu-id="78f9c-301">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="78f9c-301">*Pages/Index*</span></span> |
| <span data-ttu-id="78f9c-302">RedirectToPage("./Index")</span><span class="sxs-lookup"><span data-stu-id="78f9c-302">RedirectToPage("./Index");</span></span> | <span data-ttu-id="78f9c-303">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="78f9c-303">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="78f9c-304">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="78f9c-304">RedirectToPage("../Index")</span></span> | <span data-ttu-id="78f9c-305">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="78f9c-305">*Pages/Index*</span></span> |
| <span data-ttu-id="78f9c-306">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="78f9c-306">RedirectToPage("Index")</span></span>  | <span data-ttu-id="78f9c-307">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="78f9c-307">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="78f9c-308">`RedirectToPage("Index")`, `RedirectToPage("./Index")` 및 `RedirectToPage("../Index")`는 *상대적 이름*입니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-308">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="78f9c-309">`RedirectToPage`의 매개 변수는 현재 페이지의 경로와 *결합*되어 대상 페이지의 이름을 컴퓨팅합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-309">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="78f9c-310">상대적 이름 연결은 구조가 복잡한 사이트를 만들때 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-310">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="78f9c-311">상대적 이름을 사용하여 한 폴더의 여러 페이지들을 연결하면 해당 폴더의 이름을 바꿀 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-311">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="78f9c-312">그래도 여전히 모든 링크는 동작합니다(링크에 폴더 이름이 포함되어 있지 않기 때문입니다).</span><span class="sxs-lookup"><span data-stu-id="78f9c-312">All the links still work (because they didn't include the folder name).</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="78f9c-313">다른 [영역](xref:mvc/controllers/areas)에서 페이지로 리디렉션하려면 영역을 지정하세요.</span><span class="sxs-lookup"><span data-stu-id="78f9c-313">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="78f9c-314">자세한 내용은 <xref:mvc/controllers/areas>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="78f9c-314">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="78f9c-315">ViewData 특성</span><span class="sxs-lookup"><span data-stu-id="78f9c-315">ViewData attribute</span></span>

<span data-ttu-id="78f9c-316">[ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute)를 사용하여 데이터를 페이지에 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-316">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="78f9c-317">`[ViewData]`가 지정된 컨트롤러나 Razor 페이지 모델의 속성은 [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary)에 값이 저장되고 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-317">Properties on controllers or Razor Page models decorated with `[ViewData]` have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="78f9c-318">다음 예제에서 `AboutModel`에는 `[ViewData]`가 지정된 `Title` 속성이 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-318">In the following example, the `AboutModel` contains a `Title` property decorated with `[ViewData]`.</span></span> <span data-ttu-id="78f9c-319">이 `Title` 속성은 About 페이지의 제목으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-319">The `Title` property is set to the title of the About page:</span></span>

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

<span data-ttu-id="78f9c-320">정보 페이지에서는 모델의 속성으로 `Title` 속성에 접근합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-320">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="78f9c-321">레이아웃에서는 ViewData 사전으로부터 이 제목을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-321">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

::: moniker-end

## <a name="tempdata"></a><span data-ttu-id="78f9c-322">TempData</span><span class="sxs-lookup"><span data-stu-id="78f9c-322">TempData</span></span>

<span data-ttu-id="78f9c-323">ASP.NET Core는 [컨트롤러](/dotnet/api/microsoft.aspnetcore.mvc.controller)에서 [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) 속성을 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-323">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="78f9c-324">이 속성은 해당 속성이 읽혀질 때까지만 데이터를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-324">This property stores data until it's read.</span></span> <span data-ttu-id="78f9c-325">`Keep` 및 `Peek` 메서드를 사용하면 삭제하지 않고도 데이터를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-325">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="78f9c-326">`TempData`는 두 단계 이상의 요청에 대한 데이터가 필요할 경우의 리디렉션에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-326">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="78f9c-327">`[TempData]` 특성은 ASP.NET Core 2.0의 새로운 기능으로 컨트롤러 및 페이지에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-327">The `[TempData]` attribute is new in ASP.NET Core 2.0 and is supported on controllers and pages.</span></span>

<span data-ttu-id="78f9c-328">다음 코드는 `TempData`를 사용하여 `Message`의 값을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-328">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="78f9c-329">*Pages/Customers/Index.cshtml* 파일의 다음 태그는 `TempData`를 사용하여 `Message` 값을 출력합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-329">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="78f9c-330">*Pages/Customers/Index.cshtml.cs* 페이지 모델은 `Message` 속성에 `[TempData]` 특성을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-330">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```cs
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="78f9c-331">자세한 내용은 [TempData](xref:fundamentals/app-state#tempdata)를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-331">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="78f9c-332">한 페이지에 대한 여러 처리기</span><span class="sxs-lookup"><span data-stu-id="78f9c-332">Multiple handlers per page</span></span>

<span data-ttu-id="78f9c-333">다음 페이지는 `asp-page-handler` 태그 도우미를 사용하여 두 개의 페이지 처리기에 대한 태그를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-333">The following page generates markup for two page handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="78f9c-334">위 예제의 양식에는 두 개의 제출 단추가 존재하며, 각 단추는 `FormActionTagHelper`를 사용하여 서로 다른 URL로 제출됩니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-334">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="78f9c-335">`asp-page-handler` 특성은 `asp-page`와 함께 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-335">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="78f9c-336">`asp-page-handler`는 페이지에 정의된 각 처리기 메서드로 제출되는 URL을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-336">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="78f9c-337">이 예제의 경우 현재 페이지로 연결므로 `asp-page`는 지정되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-337">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="78f9c-338">페이지 모델은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-338">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="78f9c-339">이 코드는 *명명된 처리기 메서드*를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-339">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="78f9c-340">명명된 처리기 메서드는 `On<HTTP Verb>` 뒤와 `Async`(있는 경우) 앞에 이름의 텍스트를 결합해서 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-340">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="78f9c-341">위의 예제에서 페이지 메서드는 OnPost**JoinList**Async와 OnPost**JoinListUC**Async입니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-341">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="78f9c-342">*OnPost* 및 *Async*가 제거된 처리기 이름은 `JoinList`와 `JoinListUC`입니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-342">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="78f9c-343">위의 코드를 사용할 경우 `OnPostJoinListAsync`에 제출되는 URL 경로는 `http://localhost:5000/Customers/CreateFATH?handler=JoinList`입니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-343">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `http://localhost:5000/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="78f9c-344">`OnPostJoinListUCAsync`에 제출되는 URL 경로는 `http://localhost:5000/Customers/CreateFATH?handler=JoinListUC`입니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-344">The URL path that submits to `OnPostJoinListUCAsync` is `http://localhost:5000/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="78f9c-345">사용자 지정 경로</span><span class="sxs-lookup"><span data-stu-id="78f9c-345">Custom routes</span></span>

<span data-ttu-id="78f9c-346">`@page` 지시문을 사용하여 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-346">Use the `@page` directive to:</span></span>

* <span data-ttu-id="78f9c-347">페이지에 대한 사용자 지정 경로를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-347">Specify a custom route to a page.</span></span> <span data-ttu-id="78f9c-348">예를 들어 `@page "/Some/Other/Path"`를 사용하여 About 페이지에 대한 경로를 `/Some/Other/Path`로 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-348">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="78f9c-349">페이지의 기본 경로에 세그먼트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-349">Append segments to a page's default route.</span></span> <span data-ttu-id="78f9c-350">예를 들어 `@page "item"`을 사용하여 페이지의 기본 경로에 "item" 세그먼트를 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-350">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="78f9c-351">페이지의 기본 경로에 매개 변수를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-351">Append parameters to a page's default route.</span></span> <span data-ttu-id="78f9c-352">예를 들어 `@page "{id}"`를 사용하여 ID 매개 변수 `id`를 페이지에 필수로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-352">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="78f9c-353">경로의 시작 부분에 물결표(`~`)로 지정된 루트 상대 경로가 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-353">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="78f9c-354">예를 들어 `@page "~/Some/Other/Path"`은 `@page "/Some/Other/Path"`과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-354">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="78f9c-355">경로 템플릿 `@page "{handler?}"`를 지정하여 URL의 쿼리 문자열 `?handler=JoinList`를 경로 세그먼트 `/JoinList`로 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-355">You can change the query string `?handler=JoinList` in the URL to a route segment `/JoinList` by specifying the route template `@page "{handler?}"`.</span></span>

<span data-ttu-id="78f9c-356">URL에서 쿼리 문자열 `?handler=JoinList`를 사용하지 않으려면 경로를 변경하여 처리기 이름을 URL의 경로 부분에 넣습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-356">If you don't like the query string `?handler=JoinList` in the URL, you can change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="78f9c-357">`@page` 지시문 뒤에 큰따옴표로 묶은 경로 템플릿을 추가하여 경로를 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-357">You can customize the route by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="78f9c-358">이 코드를 사용할 경우 `OnPostJoinListAsync`에 제출되는 URL 경로는 `http://localhost:5000/Customers/CreateFATH/JoinList`입니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-358">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `http://localhost:5000/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="78f9c-359">`OnPostJoinListUCAsync`에 제출되는 URL 경로는 `http://localhost:5000/Customers/CreateFATH/JoinListUC`입니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-359">The URL path that submits to `OnPostJoinListUCAsync` is `http://localhost:5000/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="78f9c-360">`handler` 뒤의 `?`는 경로 매개 변수가 선택 사항임을 의미합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-360">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="78f9c-361">구성 및 설정하기</span><span class="sxs-lookup"><span data-stu-id="78f9c-361">Configuration and settings</span></span>

<span data-ttu-id="78f9c-362">고급 옵션을 구성하려면 MVC 빌더에서 확장 메서드 `AddRazorPagesOptions`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-362">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="78f9c-363">현재 `RazorPagesOptions`를 사용하여 페이지의 루트 디렉터리를 설정하거나 페이지에 대한 애플리케이션 모델 규칙을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-363">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="78f9c-364">앞으로 이 방식으로 더 많은 확장성을 지원할 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-364">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="78f9c-365">뷰를 미리 컴파일하려면 [Razor 뷰 컴파일](xref:mvc/views/view-compilation)을 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-365">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="78f9c-366">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="78f9c-366">[Download or view sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="78f9c-367">본문의 소개에 따라 예제를 만들어보는 [Razor 페이지 시작하기](xref:tutorials/razor-pages/razor-pages-start)도 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-367">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="78f9c-368">Razor 페이지를 콘텐츠 루트로 지정하기</span><span class="sxs-lookup"><span data-stu-id="78f9c-368">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="78f9c-369">기본적으로 Razor 페이지의 루트 경로는 */Pages* 디렉터리입니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-369">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="78f9c-370">[AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_)에 [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot)를 추가하여 Razor 페이지를 앱의 콘텐츠 루트([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath))로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="78f9c-370">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the content root ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="78f9c-371">Razor 페이지가 사용자 지정 루트 디렉터리에 위치하도록 지정하기</span><span class="sxs-lookup"><span data-stu-id="78f9c-371">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="78f9c-372">[AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_)에 [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot)를 추가하여 Razor 페이지가 앱의 사용자 지정 루트 디렉터리에 있도록 지정합니다(상대 경로 제공).</span><span class="sxs-lookup"><span data-stu-id="78f9c-372">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="78f9c-373">추가 자료</span><span class="sxs-lookup"><span data-stu-id="78f9c-373">Additional resources</span></span>

* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
