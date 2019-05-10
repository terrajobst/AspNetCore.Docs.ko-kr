---
title: ASP.NET Core의 단순 권한 부여
author: rick-anderson
description: Authorize 특성을 사용하여 ASP.NET Core의 컨트롤러 및 액션에 대한 접근을 제한하는 방법을 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/simple
ms.openlocfilehash: 6409def0508b855d3d2a4a1f4d3a3d15bfe5dd32
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897680"
---
# <a name="simple-authorization-in-aspnet-core"></a>ASP.NET Core의 단순 권한 부여

<a name="security-authorization-simple"></a>

MVC의 권한 부여는 `AuthorizeAttribute` 특성과 이 특성의 다양한 매개 변수를 통해서 제어됩니다. 가장 간단한 방법으로, 컨트롤러나 액션에 `AuthorizeAttribute` 특성을 적용하면 인증된 사용자만 해당 컨트롤러나 액션에 접근할 수 있게 제한됩니다.

예를 들어 다음 코드는 인증된 사용자만 `AccountController`에 접근할 수 있게 제한합니다.

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

컨트롤러가 아닌 액션에 권한 부여를 적용하고 싶다면 `AuthorizeAttribute` 특성을 액션 자체에 적용합니다.

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

이제 인증된 사용자만 `Logout` 함수에 접근할 수 있습니다.

또는 `AllowAnonymous` 특성을 사용하여 인증되지 않은 사용자가 개별 액션에 접근할 수 있게 허용할 수도 있습니다. 예:

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

이렇게 특성을 적용하면 인증 여부 및 익명 상태와 관계없이 모든 사용자가 접근할 수 있는 `Login` 액션을 제외하고, `AccountController`에는 인증된 사용자만 접근할 수 있습니다.

> [!WARNING]
> `[AllowAnonymous]`는 모든 권한 부여 문을 건너뜁니다. `[AllowAnonymous]` 및 `[Authorize]` 특성을 결합하면 `[Authorize]` 특성은 무시됩니다. 예를 들어 컨트롤러 수준에 `[AllowAnonymous]`를 적용하면 해당 컨트롤러의 (또는 그 내부의 모든 액션의) `[Authorize]` 특성은 무시됩니다.
