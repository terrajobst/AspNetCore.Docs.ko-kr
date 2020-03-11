---
title: ASP.NET Core의 역할 기반 권한 부여
author: rick-anderson
description: Authorize 특성에 역할을 전달하여 ASP.NET Core의 컨트롤러 및 액션에 대한 접근을 제한하는 방법을 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/roles
ms.openlocfilehash: 28aa3df6aa661d0b762df78fe611cd827af43f75
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651639"
---
# <a name="role-based-authorization-in-aspnet-core"></a><span data-ttu-id="b4b2e-103">ASP.NET Core의 역할 기반 권한 부여</span><span class="sxs-lookup"><span data-stu-id="b4b2e-103">Role-based authorization in ASP.NET Core</span></span>

<a name="security-authorization-role-based"></a>

<span data-ttu-id="b4b2e-104">신원(Identity)이 생성될 때 해당 신원은 하나 이상의 역할에 속할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4b2e-104">When an identity is created it may belong to one or more roles.</span></span> <span data-ttu-id="b4b2e-105">예를 들어 Tracy는 Administrator 및 User 역할에 모두 속하지만 Scott은 User 역할에만 속할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4b2e-105">For example, Tracy may belong to the Administrator and User roles whilst Scott may only belong to the User role.</span></span> <span data-ttu-id="b4b2e-106">이런 역할이 생성되고 관리되는 방식은 권한 부여 프로세스에 사용되는 백업 저장소에 따라서 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="b4b2e-106">How these roles are created and managed depends on the backing store of the authorization process.</span></span> <span data-ttu-id="b4b2e-107">역할은 [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) 클래스의 [IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole) 메서드를 통해 개발자에 게 노출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b4b2e-107">Roles are exposed to the developer through the [IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole) method on the [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) class.</span></span>

## <a name="adding-role-checks"></a><span data-ttu-id="b4b2e-108">역할 검사 추가하기</span><span class="sxs-lookup"><span data-stu-id="b4b2e-108">Adding role checks</span></span>

<span data-ttu-id="b4b2e-109">역할 기반 권한 부여 검사는 개발자가 코드 내에이를 포함 하 여, 컨트롤러 또는 컨트롤러 내의 작업에 대해 포함 하 여 요청 된 리소스에 액세스 하기 위해 현재 사용자가 멤버 여야 하는 역할을 지정 하&mdash;합니다.</span><span class="sxs-lookup"><span data-stu-id="b4b2e-109">Role-based authorization checks are declarative&mdash;the developer embeds them within their code, against a controller or an action within a controller, specifying roles which the current user must be a member of to access the requested resource.</span></span>

<span data-ttu-id="b4b2e-110">예를 들어 다음 코드는 `Administrator` 역할의 멤버인 사용자에 대 한 `AdministrationController` 작업에 대 한 액세스를 제한 합니다.</span><span class="sxs-lookup"><span data-stu-id="b4b2e-110">For example, the following code limits access to any actions on the `AdministrationController` to users who are a member of the `Administrator` role:</span></span>

```csharp
[Authorize(Roles = "Administrator")]
public class AdministrationController : Controller
{
}
```

<span data-ttu-id="b4b2e-111">쉼표로 구분된 목록을 지정해서 여러 역할을 지정할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4b2e-111">You can specify multiple roles as a comma separated list:</span></span>

```csharp
[Authorize(Roles = "HRManager,Finance")]
public class SalaryController : Controller
{
}
```

<span data-ttu-id="b4b2e-112">이 컨트롤러는 `HRManager` 역할 또는 `Finance` 역할의 멤버인 사용자만 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4b2e-112">This controller would be only accessible by users who are members of the `HRManager` role or the `Finance` role.</span></span>

<span data-ttu-id="b4b2e-113">여러 특성을 적용 하는 경우 액세스 하는 사용자는 지정 된 모든 역할의 멤버 여야 합니다. 다음 샘플을 사용 하려면 사용자가 `PowerUser` 및 `ControlPanelUser` 역할의 멤버 여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b4b2e-113">If you apply multiple attributes then an accessing user must be a member of all the roles specified; the following sample requires that a user must be a member of both the `PowerUser` and `ControlPanelUser` role.</span></span>

```csharp
[Authorize(Roles = "PowerUser")]
[Authorize(Roles = "ControlPanelUser")]
public class ControlPanelController : Controller
{
}
```

<span data-ttu-id="b4b2e-114">액션 수준에 역할 권한 부여 특성을 추가로 적용해서 접근을 더욱 제한할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4b2e-114">You can further limit access by applying additional role authorization attributes at the action level:</span></span>

```csharp
[Authorize(Roles = "Administrator, PowerUser")]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [Authorize(Roles = "Administrator")]
    public ActionResult ShutDown()
    {
    }
}
```

<span data-ttu-id="b4b2e-115">위의 코드 조각에서 `Administrator` 역할 또는 `PowerUser` 역할의 멤버는 컨트롤러 및 `SetTime` 작업에 액세스할 수 있지만 `Administrator` 역할의 멤버만이 `ShutDown` 동작에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4b2e-115">In the previous code snippet members of the `Administrator` role or the `PowerUser` role can access the controller and the `SetTime` action, but only members of the `Administrator` role can access the `ShutDown` action.</span></span>

<span data-ttu-id="b4b2e-116">또한, 컨트롤러를 잠글 수도 있고 개별 작업에 대한 익명의 인증되지 않은 액세스를 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4b2e-116">You can also lock down a controller but allow anonymous, unauthenticated access to individual actions.</span></span>

```csharp
[Authorize]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [AllowAnonymous]
    public ActionResult Login()
    {
    }
}
```

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="b4b2e-117">Razor Pages의 경우 다음 중 하나를 수행 하 여 `AuthorizeAttribute`을 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4b2e-117">For Razor Pages, the `AuthorizeAttribute` can be applied by either:</span></span>

* <span data-ttu-id="b4b2e-118">[규칙](xref:razor-pages/razor-pages-conventions#page-model-action-conventions)사용 또는</span><span class="sxs-lookup"><span data-stu-id="b4b2e-118">Using a [convention](xref:razor-pages/razor-pages-conventions#page-model-action-conventions), or</span></span>
* <span data-ttu-id="b4b2e-119">`PageModel` 인스턴스에 `AuthorizeAttribute` 적용:</span><span class="sxs-lookup"><span data-stu-id="b4b2e-119">Applying the `AuthorizeAttribute` to the `PageModel` instance:</span></span>

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public class UpdateModel : PageModel
{
    public ActionResult OnPost()
    {
    }
}
```

> [!IMPORTANT]
> <span data-ttu-id="b4b2e-120">`AuthorizeAttribute`를 포함 한 필터 특성은 PageModel에만 적용 될 수 있으며 특정 페이지 처리기 메서드에 적용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b4b2e-120">Filter attributes, including `AuthorizeAttribute`, can only be applied to PageModel and cannot be applied to specific page handler methods.</span></span>
::: moniker-end

<a name="security-authorization-role-policy"></a>

## <a name="policy-based-role-checks"></a><span data-ttu-id="b4b2e-121">정책 기반 역할 검사</span><span class="sxs-lookup"><span data-stu-id="b4b2e-121">Policy based role checks</span></span>

<span data-ttu-id="b4b2e-122">역할 요구 사항은 개발자가 응용 프로그램 시작 시 Authorization 서비스 구성의 일부로 정책을 등록하는 새로운 Policy 구문을 사용해서 표현할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4b2e-122">Role requirements can also be expressed using the new Policy syntax, where a developer registers a policy at startup as part of the Authorization service configuration.</span></span> <span data-ttu-id="b4b2e-123">이는 일반적으로 *Startup.cs* 파일의 `ConfigureServices()`에 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="b4b2e-123">This normally occurs in `ConfigureServices()` in your *Startup.cs* file.</span></span>

::: moniker range=">= aspnetcore-3.0"
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireAdministratorRole",
             policy => policy.RequireRole("Administrator"));
    });
}
```
::: moniker-end

::: moniker range="< aspnetcore-3.0"
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireAdministratorRole",
             policy => policy.RequireRole("Administrator"));
    });
}
```
::: moniker-end

<span data-ttu-id="b4b2e-124">정책은 `AuthorizeAttribute` 특성의 `Policy` 속성을 사용 하 여 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b4b2e-124">Policies are applied using the `Policy` property on the `AuthorizeAttribute` attribute:</span></span>

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

<span data-ttu-id="b4b2e-125">요구 사항에서 허용 되는 역할을 여러 개 지정 하려면 `RequireRole` 메서드에 대 한 매개 변수로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b4b2e-125">If you want to specify multiple allowed roles in a requirement then you can specify them as parameters to the `RequireRole` method:</span></span>

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

<span data-ttu-id="b4b2e-126">이 예에서는 `Administrator`, `PowerUser` 또는 `BackupAdministrator` 역할에 속한 사용자에 게 권한을 부여 합니다.</span><span class="sxs-lookup"><span data-stu-id="b4b2e-126">This example authorizes users who belong to the `Administrator`, `PowerUser` or `BackupAdministrator` roles.</span></span>

### <a name="add-role-services-to-identity"></a><span data-ttu-id="b4b2e-127">Id에 역할 서비스 추가</span><span class="sxs-lookup"><span data-stu-id="b4b2e-127">Add Role services to Identity</span></span>

<span data-ttu-id="b4b2e-128">역할 서비스를 추가 하려면 [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) 를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="b4b2e-128">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](roles/samples/3_0/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

::: moniker range="< aspnetcore-3.0"
[!code-csharp[](roles/samples/2_2/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

