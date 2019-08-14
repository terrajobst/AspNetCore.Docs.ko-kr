---
title: ASP.NET Core에서 Razor Pages 권한 부여 규칙
author: guardrex
description: 사용자에 게 권한을 부여 하 고 익명 사용자가 페이지 또는 페이지의 폴더에 액세스 하도록 허용 하는 규칙으로 페이지에 대 한 액세스를 제어 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2019
uid: security/authorization/razor-pages-authorization
ms.openlocfilehash: e0102ff64921a83f0330acb6f5d9bfd90f64ca7a
ms.sourcegitcommit: 89fcc6cb3e12790dca2b8b62f86609bed6335be9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68994029"
---
# <a name="razor-pages-authorization-conventions-in-aspnet-core"></a><span data-ttu-id="ff882-103">ASP.NET Core에서 Razor Pages 권한 부여 규칙</span><span class="sxs-lookup"><span data-stu-id="ff882-103">Razor Pages authorization conventions in ASP.NET Core</span></span>

<span data-ttu-id="ff882-104">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="ff882-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ff882-105">Razor Pages 앱에서 액세스를 제어 하는 한 가지 방법은 시작할 때 권한 부여 규칙을 사용 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-105">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="ff882-106">이러한 규칙을 통해 사용자에 게 권한을 부여 하 고 익명 사용자가 페이지의 개별 페이지 또는 폴더에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-106">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="ff882-107">이 항목에서 설명 하는 규칙은 액세스를 제어 하는 [권한 부여 필터](xref:mvc/controllers/filters#authorization-filters) 를 자동으로 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-107">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="ff882-108">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ff882-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="ff882-109">샘플 앱은 [ASP.NET Core id 없이 쿠키 인증](xref:security/authentication/cookie)을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-109">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="ff882-110">이 항목에 나와 있는 개념 및 예제는 ASP.NET Core Id를 사용 하는 앱에 동일 하 게 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-110">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="ff882-111">ASP.NET Core Id를 사용 하려면의 <xref:security/authentication/identity>지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="ff882-111">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="ff882-112">페이지에 액세스 하기 위한 권한 부여 필요</span><span class="sxs-lookup"><span data-stu-id="ff882-112">Require authorization to access a page</span></span>

<span data-ttu-id="ff882-113">를 통해 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 규칙을 사용 하 여 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 지정 된 경로에 있는 페이지에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-113">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

<span data-ttu-id="ff882-114">지정 된 경로는 확장명이 없고 슬래시만 포함 하는 Razor Pages 루트 상대 경로인 뷰 엔진 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-114">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="ff882-115">[권한 부여 정책을](xref:security/authorization/policies)지정 하려면 [AuthorizePage 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-115">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="ff882-116">는 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> `[Authorize]` 필터 특성을 사용 하 여 페이지 모델 클래스에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-116">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="ff882-117">자세한 내용은 [권한 부여 필터 특성](xref:razor-pages/filter#authorize-filter-attribute)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ff882-117">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="ff882-118">페이지의 폴더에 액세스 하기 위한 권한 부여 필요</span><span class="sxs-lookup"><span data-stu-id="ff882-118">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="ff882-119">를 통해 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 규칙을 사용 하 여 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 지정 된 경로에 있는 폴더의 모든 페이지에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-119">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

<span data-ttu-id="ff882-120">지정 된 경로는 Razor Pages 루트 상대 경로인 뷰 엔진 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-120">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="ff882-121">[권한 부여 정책을](xref:security/authorization/policies)지정 하려면 [AuthorizeFolder 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-121">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="ff882-122">영역 페이지에 액세스 하기 위한 권한 부여 필요</span><span class="sxs-lookup"><span data-stu-id="ff882-122">Require authorization to access an area page</span></span>

<span data-ttu-id="ff882-123">를 통해 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 규칙을 사용 하 여 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 지정 된 경로의 영역 페이지에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-123">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="ff882-124">페이지 이름은 지정 된 영역에 대 한 페이지 루트 디렉터리에 상대적인 확장명이 없는 파일의 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-124">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="ff882-125">예를 들어 파일 *영역/i d/페이지/관리/계정. cshtml* 의 페이지 이름은 */Manage/Accounts*입니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-125">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts*.</span></span>

<span data-ttu-id="ff882-126">[권한 부여 정책을](xref:security/authorization/policies)지정 하려면 [AuthorizeAreaPage 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-126">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="ff882-127">영역 폴더에 액세스 하기 위한 권한 부여 필요</span><span class="sxs-lookup"><span data-stu-id="ff882-127">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="ff882-128">를 통해 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 규칙을 사용 하 여 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 지정 된 경로에 있는 폴더의 모든 영역에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-128">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="ff882-129">폴더 경로는 지정 된 영역에 대 한 페이지 루트 디렉터리를 기준으로 하는 폴더의 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-129">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="ff882-130">예를 들어 *영역/i d/페이지/관리/관리/* 관리에서 파일의 폴더 경로는/ *관리*입니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-130">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage*.</span></span>

<span data-ttu-id="ff882-131">[권한 부여 정책을](xref:security/authorization/policies)지정 하려면 [AuthorizeAreaFolder 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-131">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="ff882-132">페이지에 대 한 익명 액세스 허용</span><span class="sxs-lookup"><span data-stu-id="ff882-132">Allow anonymous access to a page</span></span>

<span data-ttu-id="ff882-133">를 통해 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 규칙을 사용 하 여 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 지정 된 경로에 있는 페이지에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-133">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

<span data-ttu-id="ff882-134">지정 된 경로는 확장명이 없고 슬래시만 포함 하는 Razor Pages 루트 상대 경로인 뷰 엔진 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-134">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="ff882-135">페이지의 폴더에 대 한 익명 액세스 허용</span><span class="sxs-lookup"><span data-stu-id="ff882-135">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="ff882-136">를 통해 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 규칙을 사용 하 여 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 지정 된 경로에 있는 폴더의 모든 페이지에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-136">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

<span data-ttu-id="ff882-137">지정 된 경로는 Razor Pages 루트 상대 경로인 뷰 엔진 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-137">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="ff882-138">권한 있는 액세스 및 익명 액세스 결합에 대 한 참고 사항</span><span class="sxs-lookup"><span data-stu-id="ff882-138">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="ff882-139">권한 부여가 필요한 페이지의 폴더를 지정 하 고 해당 폴더의 페이지에서 익명 액세스를 허용 하도록 지정 하는 것은 유효 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-139">It's valid to specify that a folder of pages that require authorization and than specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="ff882-140">그러나 반대의 경우는 유효 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-140">The reverse, however, isn't valid.</span></span> <span data-ttu-id="ff882-141">익명 액세스를 위해 페이지의 폴더를 선언한 다음 권한 부여가 필요한 해당 폴더 내에 페이지를 지정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-141">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="ff882-142">개인 페이지에 대 한 권한 부여가 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-142">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="ff882-143"><xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 및<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 가 모두 페이지<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 에 적용 되 면가 우선 하 고 액세스를 제어 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-143">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ff882-144">추가 자료</span><span class="sxs-lookup"><span data-stu-id="ff882-144">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ff882-145">Razor Pages 앱에서 액세스를 제어 하는 한 가지 방법은 시작할 때 권한 부여 규칙을 사용 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-145">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="ff882-146">이러한 규칙을 통해 사용자에 게 권한을 부여 하 고 익명 사용자가 페이지의 개별 페이지 또는 폴더에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-146">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="ff882-147">이 항목에서 설명 하는 규칙은 액세스를 제어 하는 [권한 부여 필터](xref:mvc/controllers/filters#authorization-filters) 를 자동으로 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-147">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="ff882-148">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ff882-148">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="ff882-149">샘플 앱은 [ASP.NET Core id 없이 쿠키 인증](xref:security/authentication/cookie)을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-149">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="ff882-150">이 항목에 나와 있는 개념 및 예제는 ASP.NET Core Id를 사용 하는 앱에 동일 하 게 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-150">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="ff882-151">ASP.NET Core Id를 사용 하려면의 <xref:security/authentication/identity>지침을 따르세요.</span><span class="sxs-lookup"><span data-stu-id="ff882-151">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="ff882-152">페이지에 액세스 하기 위한 권한 부여 필요</span><span class="sxs-lookup"><span data-stu-id="ff882-152">Require authorization to access a page</span></span>

<span data-ttu-id="ff882-153">를 통해 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 규칙을 사용 하 여 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 지정 된 경로에 있는 페이지에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-153">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

<span data-ttu-id="ff882-154">지정 된 경로는 확장명이 없고 슬래시만 포함 하는 Razor Pages 루트 상대 경로인 뷰 엔진 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-154">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="ff882-155">[권한 부여 정책을](xref:security/authorization/policies)지정 하려면 [AuthorizePage 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-155">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="ff882-156">는 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> `[Authorize]` 필터 특성을 사용 하 여 페이지 모델 클래스에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-156">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="ff882-157">자세한 내용은 [권한 부여 필터 특성](xref:razor-pages/filter#authorize-filter-attribute)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="ff882-157">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="ff882-158">페이지의 폴더에 액세스 하기 위한 권한 부여 필요</span><span class="sxs-lookup"><span data-stu-id="ff882-158">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="ff882-159">를 통해 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 규칙을 사용 하 여 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 지정 된 경로에 있는 폴더의 모든 페이지에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-159">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

<span data-ttu-id="ff882-160">지정 된 경로는 Razor Pages 루트 상대 경로인 뷰 엔진 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-160">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="ff882-161">[권한 부여 정책을](xref:security/authorization/policies)지정 하려면 [AuthorizeFolder 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-161">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="ff882-162">영역 페이지에 액세스 하기 위한 권한 부여 필요</span><span class="sxs-lookup"><span data-stu-id="ff882-162">Require authorization to access an area page</span></span>

<span data-ttu-id="ff882-163">를 통해 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 규칙을 사용 하 여 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 지정 된 경로의 영역 페이지에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-163">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="ff882-164">페이지 이름은 지정 된 영역에 대 한 페이지 루트 디렉터리에 상대적인 확장명이 없는 파일의 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-164">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="ff882-165">예를 들어 파일 *영역/i d/페이지/관리/계정. cshtml* 의 페이지 이름은 */Manage/Accounts*입니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-165">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts*.</span></span>

<span data-ttu-id="ff882-166">[권한 부여 정책을](xref:security/authorization/policies)지정 하려면 [AuthorizeAreaPage 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-166">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="ff882-167">영역 폴더에 액세스 하기 위한 권한 부여 필요</span><span class="sxs-lookup"><span data-stu-id="ff882-167">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="ff882-168">를 통해 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 규칙을 사용 하 여 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 지정 된 경로에 있는 폴더의 모든 영역에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-168">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="ff882-169">폴더 경로는 지정 된 영역에 대 한 페이지 루트 디렉터리를 기준으로 하는 폴더의 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-169">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="ff882-170">예를 들어 *영역/i d/페이지/관리/관리/* 관리에서 파일의 폴더 경로는/ *관리*입니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-170">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage*.</span></span>

<span data-ttu-id="ff882-171">[권한 부여 정책을](xref:security/authorization/policies)지정 하려면 [AuthorizeAreaFolder 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*)를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-171">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="ff882-172">페이지에 대 한 익명 액세스 허용</span><span class="sxs-lookup"><span data-stu-id="ff882-172">Allow anonymous access to a page</span></span>

<span data-ttu-id="ff882-173">를 통해 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 규칙을 사용 하 여 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 지정 된 경로에 있는 페이지에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-173">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

<span data-ttu-id="ff882-174">지정 된 경로는 확장명이 없고 슬래시만 포함 하는 Razor Pages 루트 상대 경로인 뷰 엔진 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-174">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="ff882-175">페이지의 폴더에 대 한 익명 액세스 허용</span><span class="sxs-lookup"><span data-stu-id="ff882-175">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="ff882-176">를 통해 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 규칙을 사용 하 여 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 지정 된 경로에 있는 폴더의 모든 페이지에를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-176">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

<span data-ttu-id="ff882-177">지정 된 경로는 Razor Pages 루트 상대 경로인 뷰 엔진 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-177">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="ff882-178">권한 있는 액세스 및 익명 액세스 결합에 대 한 참고 사항</span><span class="sxs-lookup"><span data-stu-id="ff882-178">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="ff882-179">권한 부여가 필요한 페이지의 폴더를 지정 하 고 해당 폴더의 페이지에서 익명 액세스를 허용 하도록 지정 하는 것은 유효 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-179">It's valid to specify that a folder of pages that require authorization and than specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="ff882-180">그러나 반대의 경우는 유효 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-180">The reverse, however, isn't valid.</span></span> <span data-ttu-id="ff882-181">익명 액세스를 위해 페이지의 폴더를 선언한 다음 권한 부여가 필요한 해당 폴더 내에 페이지를 지정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-181">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="ff882-182">개인 페이지에 대 한 권한 부여가 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-182">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="ff882-183"><xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 및<xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 가 모두 페이지<xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 에 적용 되 면가 우선 하 고 액세스를 제어 합니다.</span><span class="sxs-lookup"><span data-stu-id="ff882-183">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ff882-184">추가 자료</span><span class="sxs-lookup"><span data-stu-id="ff882-184">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end
