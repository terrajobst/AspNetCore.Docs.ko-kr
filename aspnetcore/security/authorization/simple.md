---
title: ASP.NET Core의 단순 권한 부여
author: rick-anderson
description: Authorize 특성을 사용하여 ASP.NET Core의 컨트롤러 및 액션에 대한 접근을 제한하는 방법을 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/simple
ms.openlocfilehash: 6409def0508b855d3d2a4a1f4d3a3d15bfe5dd32
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653559"
---
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="7e6ba-103">ASP.NET Core의 단순 권한 부여</span><span class="sxs-lookup"><span data-stu-id="7e6ba-103">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="7e6ba-104">MVC의 권한 부여는 `AuthorizeAttribute` 특성 및 해당 하는 다양 한 매개 변수를 통해 제어 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e6ba-104">Authorization in MVC is controlled through the `AuthorizeAttribute` attribute and its various parameters.</span></span> <span data-ttu-id="7e6ba-105">가장 간단한 방법으로, 컨트롤러 또는 작업에 `AuthorizeAttribute` 특성을 적용 하면 인증 된 사용자에 대 한 컨트롤러나 동작에 대 한 액세스가 제한 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e6ba-105">At its simplest, applying the `AuthorizeAttribute` attribute to a controller or action limits access to the controller or action to any authenticated user.</span></span>

<span data-ttu-id="7e6ba-106">예를 들어 다음 코드는 `AccountController`에 대 한 액세스를 인증 된 사용자로 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6ba-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

```csharp
[Authorize]
public class AccountController : Controller
{
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

<span data-ttu-id="7e6ba-107">컨트롤러가 아닌 작업에 권한 부여를 적용 하려면 작업 자체에 `AuthorizeAttribute` 특성을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6ba-107">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

```csharp
public class AccountController : Controller
{
   public ActionResult Login()
   {
   }

   [Authorize]
   public ActionResult Logout()
   {
   }
}
```

<span data-ttu-id="7e6ba-108">이제 인증 된 사용자만 `Logout` 함수에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6ba-108">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="7e6ba-109">`AllowAnonymous` 특성을 사용 하 여 개별 작업에 대해 인증 되지 않은 사용자의 액세스를 허용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7e6ba-109">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="7e6ba-110">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="7e6ba-110">For example:</span></span>

```csharp
[Authorize]
public class AccountController : Controller
{
    [AllowAnonymous]
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

<span data-ttu-id="7e6ba-111">이렇게 하면 인증 된 사용자 또는 인증 되지 않은/익명 상태와 관계 없이 모든 사용자가 액세스할 수 있는 `Login` 작업을 제외 하 고는 `AccountController`에 대해 인증 된 사용자만 허용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e6ba-111">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="7e6ba-112">`[AllowAnonymous]`는 모든 권한 부여 문을 무시 합니다.</span><span class="sxs-lookup"><span data-stu-id="7e6ba-112">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="7e6ba-113">`[AllowAnonymous]` 및 `[Authorize]` 특성을 결합 하는 경우 `[Authorize]` 특성이 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e6ba-113">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="7e6ba-114">예를 들어 `[AllowAnonymous]`를 컨트롤러 수준에서 적용 하는 경우 동일한 컨트롤러의 모든 `[Authorize]` 특성 (또는 그 안의 모든 작업)은 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7e6ba-114">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>
