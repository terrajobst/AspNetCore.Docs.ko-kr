---
title: ASP.NET Core의 역할 기반 권한 부여
author: rick-anderson
description: 권한 부여 특성에 역할을 전달하여 ASP.NET Core 컨트롤러 및 작업에 액세스를 제한하는 방법을 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/roles
ms.openlocfilehash: 28aa3df6aa661d0b762df78fe611cd827af43f75
ms.sourcegitcommit: 6628cd23793b66e4ce88788db641a5bbf470c3c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73660044"
---
# <a name="role-based-authorization-in-aspnet-core"></a>ASP.NET Core에서 역할 기반 권한 부여

<a name="security-authorization-role-based"></a>

ID가 생성되면 하나 이상의 역할에 속할 수 있습니다. 예를 들어 Tracy는 Administrator 및 User 역할에 속하지만 Scott은 User 역할에만 속할 수 있습니다. 이러한 역할을 만들고 관리하는 방법은 권한 부여 프로세스의 백업 저장소에 따라 달라집니다. 역할은 [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal) 클래스의 [IsInRole](/dotnet/api/system.security.principal.genericprincipal.isinrole) 메서드를 통해서 개발자에게 노출됩니다.

## <a name="adding-role-checks"></a>역할 검사 추가

역할 기반 권한 부여 검사는 선언적입니다. 개발자는 컨트롤러나 컨트롤러 내의 작업에 대해 현재 사용자가 요청한 리소스에 액세스하기 위해 반드시 멤버여야만 하는 역할을 지정하여 역할 기반 권한 부여 검사를 코드 내부에 포함시킵니다.

예를 들어 다음 코드는 `Administrator` 역할의 멤버인 사용자만 `AdministrationController`의 모든 작업에 액세스할 수 있도록 제한합니다.

```csharp
[Authorize(Roles = "Administrator")]
public class AdministrationController : Controller
{
}
```

쉼표로 구분된 목록을 지정하여 여러 역할을 지정할 수 있습니다.

```csharp
[Authorize(Roles = "HRManager,Finance")]
public class SalaryController : Controller
{
}
```

이 컨트롤러는 `HRManager` 역할 또는 `Finance` 역할의 멤버인 사용자만 액세스할 수 있습니다.

여러 개의 특성을 적용할 경우 액세스하는 사용자는 지정된 모든 역할의 멤버여야 합니다. 다음 예제에서 사용자는 `PowerUser` 및 `ControlPanelUser` 역할 양쪽의 멤버여야 합니다.

```csharp
[Authorize(Roles = "PowerUser")]
[Authorize(Roles = "ControlPanelUser")]
public class ControlPanelController : Controller
{
}
```

작업 수준에서 추가적인 역할 권한 부여 특성을 적용하여 액세스를 더욱 제한할 수 있습니다.

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

이전 코드 조각에서는 `Administrator` 역할 또는 `PowerUser` 역할의 멤버는 컨트롤러 및 `SetTime` 작업에 액세스할 수 있지만, `ShutDown` 작업은 `Administrator` 역할의 멤버만 액세스할 수 있습니다.

또한 컨트롤러는 잠그지만, 개별 작업에 대한 익명의 인증되지 않은 액세스는 허용할 수도 있습니다.

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

Razor Pages의 경우 다음 중 한 가지 방법을 통해서 `AuthorizeAttribute`를 적용할 수 있습니다.

* [규칙](xref:razor-pages/razor-pages-conventions#page-model-action-conventions)을 사용합니다. 또는
* `PageModel` 인스턴스에 `AuthorizeAttribute`를 적용합니다.

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
> `AuthorizeAttribute`를 비롯한 필터 특성은 PageModel에만 적용할 수 있으며 특정 페이지 처리기 메서드에는 적용할 수 없습니다.

::: moniker-end

<a name="security-authorization-role-policy"></a>

## <a name="policy-based-role-checks"></a>정책 기반 역할 검사

역할 요구 사항은 개발자가 시작 시 권한 부여 서비스 구성의 일부로 정책을 등록하는 새 Policy 구문을 사용하여 나타낼 수도 있습니다. 일반적으로 이 작업은 *Startup.cs* 파일의 `ConfigureServices()`에서 수행됩니다.

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

정책은 `AuthorizeAttribute` 특성의 `Policy` 속성을 사용하여 적용됩니다.

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

요구 사항에서 허용되는 역할을 여러 개 지정하려면 해당 역할들을 `RequireRole` 메서드에 매개 변수로 지정할 수 있습니다.

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

이 예제에서는 `Administrator`, `PowerUser` 또는 `BackupAdministrator` 역할에 속한 사용자에게 권한을 부여합니다.

### <a name="add-role-services-to-identity"></a>Identity에 역할 서비스 추가

역할 서비스를 추가하려면 [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1)를 추가합니다.

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](roles/samples/3_0/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

::: moniker range="< aspnetcore-3.0"
[!code-csharp[](roles/samples/2_2/Startup.cs?name=snippet&highlight=7)]
::: moniker-end

