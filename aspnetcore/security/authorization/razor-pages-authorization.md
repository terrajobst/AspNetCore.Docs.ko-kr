---
title: ASP.NET Core에서 razor 페이지 권한 부여 규칙
author: guardrex
description: 사용자 권한 부여 및 익명 사용자가 페이지 또는 페이지의 폴더에 액세스 하도록 허용 하는 규칙을 사용 하 여 페이지에 대 한 액세스를 제어 하는 방법에 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2019
uid: security/authorization/razor-pages-authorization
ms.openlocfilehash: ff061f96f30cd893b903403de760a172c924cf06
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64895420"
---
# <a name="razor-pages-authorization-conventions-in-aspnet-core"></a><span data-ttu-id="43d25-103">ASP.NET Core에서 razor 페이지 권한 부여 규칙</span><span class="sxs-lookup"><span data-stu-id="43d25-103">Razor Pages authorization conventions in ASP.NET Core</span></span>

<span data-ttu-id="43d25-104">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="43d25-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="43d25-105">Razor 페이지 앱에 대 한 액세스를 제어 하는 한 가지 방법은 시작 시 권한 부여 규칙을 사용 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="43d25-105">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="43d25-106">이러한 규칙을 사용 하면 사용자 권한을 부여 하 고 익명 사용자가 개별 페이지 또는 페이지의 폴더에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="43d25-106">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="43d25-107">자동으로이 항목에서 설명 하는 규칙이 적용 [권한 부여 필터](xref:mvc/controllers/filters#authorization-filters) 액세스 제어 합니다.</span><span class="sxs-lookup"><span data-stu-id="43d25-107">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="43d25-108">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="43d25-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="43d25-109">샘플 앱에서는 [ASP.NET Core Id 없이 쿠키 인증](xref:security/authentication/cookie)합니다.</span><span class="sxs-lookup"><span data-stu-id="43d25-109">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="43d25-110">개념 및이 항목에 표시 하는 예제 ASP.NET Core Id를 사용 하는 앱에 동일 하 게 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="43d25-110">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="43d25-111">ASP.NET Core Id를 사용 하려면의 지침에 따라 <xref:security/authentication/identity>합니다.</span><span class="sxs-lookup"><span data-stu-id="43d25-111">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="43d25-112">페이지에 액세스 권한 부여 필요</span><span class="sxs-lookup"><span data-stu-id="43d25-112">Require authorization to access a page</span></span>

<span data-ttu-id="43d25-113">사용 합니다 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> 규칙을 통해 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 추가할는 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 지정된 된 경로에서 페이지:</span><span class="sxs-lookup"><span data-stu-id="43d25-113">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

<span data-ttu-id="43d25-114">지정된 된 경로 경로인 뷰 엔진에만 슬래시를 포함 하 고 확장 없이 Razor 페이지 루트 상대 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="43d25-114">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="43d25-115">지정 하는 [권한 부여 정책](xref:security/authorization/policies)를 사용 하 여는 [AuthorizePage 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span><span class="sxs-lookup"><span data-stu-id="43d25-115">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="43d25-116"><xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 사용 하 여 페이지 모델 클래스에 적용할 수는 `[Authorize]` 필터 특성입니다.</span><span class="sxs-lookup"><span data-stu-id="43d25-116">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="43d25-117">자세한 내용은 [권한 부여 필터 특성](xref:razor-pages/filter#authorize-filter-attribute)합니다.</span><span class="sxs-lookup"><span data-stu-id="43d25-117">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="43d25-118">페이지 폴더 액세스 권한 부여 필요</span><span class="sxs-lookup"><span data-stu-id="43d25-118">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="43d25-119">사용 된 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> 규칙을 통해 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 추가할는 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 지정된 된 경로에 폴더에 있는 페이지의 모든:</span><span class="sxs-lookup"><span data-stu-id="43d25-119">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

<span data-ttu-id="43d25-120">지정된 된 경로 경로인 뷰 엔진, Razor 페이지 루트 상대 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="43d25-120">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="43d25-121">지정 하는 [권한 부여 정책](xref:security/authorization/policies)를 사용 하 여는 [AuthorizeFolder 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span><span class="sxs-lookup"><span data-stu-id="43d25-121">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="43d25-122">영역 페이지에 액세스할 수 있는 인증 필요</span><span class="sxs-lookup"><span data-stu-id="43d25-122">Require authorization to access an area page</span></span>

<span data-ttu-id="43d25-123">사용 된 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> 규칙을 통해 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 추가할는 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 지정 된 경로의 영역 페이지에:</span><span class="sxs-lookup"><span data-stu-id="43d25-123">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="43d25-124">페이지 이름은 지정된 된 영역에 대 한 페이지 루트 디렉터리에 상대적인 확장명 없이 파일의 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="43d25-124">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="43d25-125">예를 들어 파일의 페이지 이름을 *Areas/Identity/Pages/Manage/Accounts.cshtml* 됩니다 */관리/계정*합니다.</span><span class="sxs-lookup"><span data-stu-id="43d25-125">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts*.</span></span>

<span data-ttu-id="43d25-126">지정 하는 [권한 부여 정책](xref:security/authorization/policies)를 사용 하 여는 [AuthorizeAreaPage 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span><span class="sxs-lookup"><span data-stu-id="43d25-126">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="43d25-127">영역의 폴더 액세스 권한 부여 필요</span><span class="sxs-lookup"><span data-stu-id="43d25-127">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="43d25-128">사용 합니다 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> 규칙을 통해 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 추가할는 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 모든 지정된 된 경로에 폴더에서:</span><span class="sxs-lookup"><span data-stu-id="43d25-128">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="43d25-129">폴더 경로가 지정된 된 영역에 대 한 페이지 루트 디렉터리에 상대적인 폴더의 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="43d25-129">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="43d25-130">예를 들어, 아래에 있는 파일에 대 한 폴더 경로 *영역/Identity/페이지/관리/* 됩니다 */관리*합니다.</span><span class="sxs-lookup"><span data-stu-id="43d25-130">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage*.</span></span>

<span data-ttu-id="43d25-131">지정 하는 [권한 부여 정책](xref:security/authorization/policies)를 사용 하 여는 [AuthorizeAreaFolder 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span><span class="sxs-lookup"><span data-stu-id="43d25-131">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="43d25-132">페이지에 대 한 익명 액세스를 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="43d25-132">Allow anonymous access to a page</span></span>

<span data-ttu-id="43d25-133">사용 합니다 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> 규칙을 통해 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 추가할는 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 지정 된 경로의 페이지:</span><span class="sxs-lookup"><span data-stu-id="43d25-133">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

<span data-ttu-id="43d25-134">지정된 된 경로 경로인 뷰 엔진에만 슬래시를 포함 하 고 확장 없이 Razor 페이지 루트 상대 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="43d25-134">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="43d25-135">페이지의 폴더에 대 한 익명 액세스를 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="43d25-135">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="43d25-136">사용 된 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> 규칙을 통해 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 추가할는 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 지정된 된 경로에 폴더에 있는 페이지의 모든:</span><span class="sxs-lookup"><span data-stu-id="43d25-136">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

<span data-ttu-id="43d25-137">지정된 된 경로 경로인 뷰 엔진, Razor 페이지 루트 상대 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="43d25-137">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="43d25-138">익명 액세스 및 권한이 결합에 대 한 참고</span><span class="sxs-lookup"><span data-stu-id="43d25-138">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="43d25-139">지정 하는 유효한 권한 부여를 요구 하는 페이지의 폴더 및 해당 폴더 내의 페이지 익명 액세스를 허용 하는지 지정 하는 보다:</span><span class="sxs-lookup"><span data-stu-id="43d25-139">It's valid to specify that a folder of pages that require authorization and than specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="43d25-140">그러나 반대의 경우 유효 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="43d25-140">The reverse, however, isn't valid.</span></span> <span data-ttu-id="43d25-141">익명 액세스에 대 한 페이지의 폴더를 선언 하 고 권한 부여는 해당 폴더 내의 페이지를 지정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="43d25-141">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="43d25-142">개인 페이지에 필요한 권한 부여에 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="43d25-142">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="43d25-143">경우 모두를 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 및 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 페이지에 적용 되는 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 우선적으로 적용 하 고 액세스를 제어 합니다.</span><span class="sxs-lookup"><span data-stu-id="43d25-143">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="43d25-144">추가 자료</span><span class="sxs-lookup"><span data-stu-id="43d25-144">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>
