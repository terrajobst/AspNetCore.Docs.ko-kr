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
# <a name="simple-authorization-in-aspnet-core"></a>ASP.NET Core의 단순 권한 부여

<a name="security-authorization-simple"></a>

MVC의 권한 부여는 `AuthorizeAttribute` 특성 및 해당 하는 다양 한 매개 변수를 통해 제어 됩니다. 가장 간단한 방법으로, 컨트롤러 또는 작업에 `AuthorizeAttribute` 특성을 적용 하면 인증 된 사용자에 대 한 컨트롤러나 동작에 대 한 액세스가 제한 됩니다.

예를 들어 다음 코드는 `AccountController`에 대 한 액세스를 인증 된 사용자로 제한 합니다.

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

컨트롤러가 아닌 작업에 권한 부여를 적용 하려면 작업 자체에 `AuthorizeAttribute` 특성을 적용 합니다.

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

이제 인증 된 사용자만 `Logout` 함수에 액세스할 수 있습니다.

`AllowAnonymous` 특성을 사용 하 여 개별 작업에 대해 인증 되지 않은 사용자의 액세스를 허용할 수도 있습니다. 다음은 그 예입니다.

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

이렇게 하면 인증 된 사용자 또는 인증 되지 않은/익명 상태와 관계 없이 모든 사용자가 액세스할 수 있는 `Login` 작업을 제외 하 고는 `AccountController`에 대해 인증 된 사용자만 허용 됩니다.

> [!WARNING]
> `[AllowAnonymous]`는 모든 권한 부여 문을 무시 합니다. `[AllowAnonymous]` 및 `[Authorize]` 특성을 결합 하는 경우 `[Authorize]` 특성이 무시 됩니다. 예를 들어 `[AllowAnonymous]`를 컨트롤러 수준에서 적용 하는 경우 동일한 컨트롤러의 모든 `[Authorize]` 특성 (또는 그 안의 모든 작업)은 무시 됩니다.
