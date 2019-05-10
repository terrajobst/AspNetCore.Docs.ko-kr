---
title: ASP.NET Core MVC에서 뷰 기반 권한 부여
author: rick-anderson
description: 이 문서에 삽입 된 ASP.NET Core Razor 뷰 내에서 권한 부여 서비스를 활용 하는 방법을 보여 줍니다.
ms.author: riande
ms.date: 10/30/2017
uid: security/authorization/views
ms.openlocfilehash: e497c41d4dca29fed8733f18cf727804e3f06d8c
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892060"
---
# <a name="view-based-authorization-in-aspnet-core-mvc"></a><span data-ttu-id="a1cbb-103">ASP.NET Core MVC에서 뷰 기반 권한 부여</span><span class="sxs-lookup"><span data-stu-id="a1cbb-103">View-based authorization in ASP.NET Core MVC</span></span>

<span data-ttu-id="a1cbb-104">하려는 개발자는 종종 표시, 숨기기 또는 그렇지 않은 경우 현재 사용자 id를 기반으로 UI를 수정 합니다.</span><span class="sxs-lookup"><span data-stu-id="a1cbb-104">A developer often wants to show, hide, or otherwise modify a UI based on the current user identity.</span></span> <span data-ttu-id="a1cbb-105">권한 부여 서비스를 통해 MVC 보기 내에서 액세스할 수 있습니다 [종속성 주입](xref:fundamentals/dependency-injection)합니다.</span><span class="sxs-lookup"><span data-stu-id="a1cbb-105">You can access the authorization service within MVC views via [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="a1cbb-106">사용 권한 부여 서비스를 Razor 보기에 삽입할는 `@inject` 지시문:</span><span class="sxs-lookup"><span data-stu-id="a1cbb-106">To inject the authorization service into a Razor view, use the `@inject` directive:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService
```

<span data-ttu-id="a1cbb-107">모든 보기에서 권한 부여 서비스를 사용 하도록 하려는 경우 배치를 `@inject` 지시문에 *_ViewImports.cshtml* 의 파일을 *뷰* 디렉터리입니다.</span><span class="sxs-lookup"><span data-stu-id="a1cbb-107">If you want the authorization service in every view, place the `@inject` directive into the *_ViewImports.cshtml* file of the *Views* directory.</span></span> <span data-ttu-id="a1cbb-108">자세한 내용은 [보기에 종속성 주입](xref:mvc/views/dependency-injection)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="a1cbb-108">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

<span data-ttu-id="a1cbb-109">삽입 된 권한 부여 서비스를 사용 하 여 호출할 `AuthorizeAsync` 정확히 같은 방식 중 선택 [리소스 기반 권한 부여](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span><span class="sxs-lookup"><span data-stu-id="a1cbb-109">Use the injected authorization service to invoke `AuthorizeAsync` in exactly the same way you would check during [resource-based authorization](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="a1cbb-110">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="a1cbb-110">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, "PolicyName")).Succeeded)
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="a1cbb-111">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="a1cbb-111">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```cshtml
@if (await AuthorizationService.AuthorizeAsync(User, "PolicyName"))
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

---

<span data-ttu-id="a1cbb-112">경우에 따라 리소스 보기 모델 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a1cbb-112">In some cases, the resource will be your view model.</span></span> <span data-ttu-id="a1cbb-113">호출할 `AuthorizeAsync` 정확히 같은 방식 중 선택 [리소스 기반 권한 부여](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span><span class="sxs-lookup"><span data-stu-id="a1cbb-113">Invoke `AuthorizeAsync` in exactly the same way you would check during [resource-based authorization](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="a1cbb-114">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="a1cbb-114">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit)).Succeeded)
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="a1cbb-115">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="a1cbb-115">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```cshtml
@if (await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit))
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

---

<span data-ttu-id="a1cbb-116">위의 코드에서 모델을 고려해 야 정책 평가가 수행 해야 하는 리소스로 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="a1cbb-116">In the preceding code, the model is passed as a resource the policy evaluation should take into consideration.</span></span>

> [!WARNING]
> <span data-ttu-id="a1cbb-117">앱의 UI 요소의 토글 표시 유형 유일한 권한 부여 확인으로 의존 하지 마십시오.</span><span class="sxs-lookup"><span data-stu-id="a1cbb-117">Don't rely on toggling visibility of your app's UI elements as the sole authorization check.</span></span> <span data-ttu-id="a1cbb-118">UI 요소를 숨기면 방해 하지 않을 수 완전히 액세스 해당 연결 된 컨트롤러 작업입니다.</span><span class="sxs-lookup"><span data-stu-id="a1cbb-118">Hiding a UI element may not completely prevent access to its associated controller action.</span></span> <span data-ttu-id="a1cbb-119">예를 들어 앞의 코드 조각에 있는 단추를 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="a1cbb-119">For example, consider the button in the preceding code snippet.</span></span> <span data-ttu-id="a1cbb-120">사용자가 호출할 수는 `Edit` 상대 리소스를 자신이 알고 있는 경우 동작 메서드에 URL이 */Document/Edit/1*합니다.</span><span class="sxs-lookup"><span data-stu-id="a1cbb-120">A user can invoke the `Edit` action method if he or she knows the relative resource URL is */Document/Edit/1*.</span></span> <span data-ttu-id="a1cbb-121">이러한 이유로 `Edit` 작업 메서드 자체 권한 부여 확인을 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="a1cbb-121">For this reason, the `Edit` action method should perform its own authorization check.</span></span>
