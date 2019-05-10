---
title: ASP.NET Core의 클레임 기반 권한 부여
author: rick-anderson
description: ASP.NET Core 응용 프로그램에서 권한 부여에 대한 클레임 검사를 추가하는 방법을 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/claims
ms.openlocfilehash: 6b60ae5515819b017ab577f655ed91ee4d8ed0dd
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65086147"
---
# <a name="claims-based-authorization-in-aspnet-core"></a>ASP.NET Core의 클레임 기반 권한 부여

<a name="security-authorization-claims-based"></a>

신원(Identity)을 생성할 때 신뢰할 수 있는 당사자가 발급하는 하나 이상의 클레임을 부여할 수 있습니다. 클레임(Claim)은 주체가 수행할 수 있는 작업이 아닌, 주체가 무엇인지를 표현하는 이름 값의 쌍입니다. 예를 들어, 여러분은 지역 운전면허 발급기관이 발급한 운전 면허증을 갖고 있을 수 있습니다. 운전 면허증에는 생년월일이 기재되어 있을 것입니다. 이 경우, 클레임 이름은 `DateOfBirth` 이고, 클레임 값은 `8th June 1970` 같은 생년월일, 그리고 발급자는 운전면허 발급기관입니다. 간단한 방식의 클레임 기반 권한 부여에서는 클레임 값을 검사한 다음, 그 값을 기반으로 리소스에 대한 접근을 허용합니다. 예를 들어, 나이트 클럽에 들어가려고 하면 다음 절차가 진행될 것입니다.

나이트 클럽의 출입문 보안 담당자는 접근을 허용하기 전에, 먼저 생년월일 클레임의 값과 발급자(운전면허 발급기관)를 신뢰할 수 있는지 여부부터 평가합니다.

특정 신원은 여러 값을 갖고 있는 여러 클레임을 포함할 수 있으며, 동일한 유형의 클레임을 다수 포함할 수도 있습니다.

## <a name="adding-claims-checks"></a>클레임 검사 추가하기

클레임 기반 권한 부여의 검사는 선언적으로 구성됩니다. 개발자는 컨트롤러나 컨트롤러 내의 개별 액션에 대해 현재 사용자가 요청한 리소스에 접근하기 위해 반드시 갖고 있어야 하는 클레임을, 그리고 필요에 따라 해당 클레임이 갖고 있어야 하는 값을 지정해서 이를 코드 내부에 포함시킵니다. 클레임 요구 사항은 정책을 기반으로 수행되기 때문에 개발자는 먼저 클레임 요구 사항을 표현하는 정책을 만들어서 등록해야 합니다.

가장 간단한 유형의 클레임 정책은 클레임이 존재하는지 여부만 확인하고 값은 확인하지 않습니다.

먼저 해야 할 일은 정책을 작성하고 등록하는 것입니다. 이 작업은 Authorization 서비스 구성의 일부로 수행되며, 일반적으로 *Startup.cs* 파일의 `ConfigureServices()` 메서드에 작성됩니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber"));
    });
}
```

이 경우, `EmployeeOnly` 정책은 현재 신원에 `EmployeeNumber` 클레임이 존재하는지 여부를 확인합니다.

그런 다음, `AuthorizeAttribute` 특성의 `Policy` 속성에 정책 이름을 지정해서 이 정책을 적용할 수 있습니다.

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult VacationBalance()
{
    return View();
}
```

컨트롤러 전체에 `AuthorizeAttribute` 특성을 적용할 수도 있으며, 이 경우 정책과 일치하는 신원들만 해당 컨트롤러의 모든 액션에 접근이 허용됩니다.

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }
}
```

`AuthorizeAttribute` 특성으로 보호되는 컨트롤러가 존재하지만, 특정 액션에 대한 익명 접근은 허용하고 싶다면, 해당 액션에 `AllowAnonymousAttribute` 특성을 적용합니다.

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }

    [AllowAnonymous]
    public ActionResult VacationPolicy()
    {
    }
}
```

대부분의 클레임은 값과 함께 제공됩니다. 정책을 생성할 때 허용되는 값들의 목록을 함께 지정할 수 있습니다. 다음 예제의 정책은 사번이 1, 2, 3, 4, 5 인 직원들에 대해서만 성공합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("Founders", policy =>
                          policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
}
```

### <a name="add-a-generic-claim-check"></a>일반 클레임 검사 추가하기

클레임 값이 단일 값이 아니거나 변환이 필요하다면 [RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion)을 사용합니다. 자세한 내용은 [func를 이용해서 정책 구성하기](xref:security/authorization/policies#using-a-func-to-fulfill-a-policy)를 참고하시기 바랍니다.

## <a name="multiple-policy-evaluation"></a>다중 정책 평가

만약 컨트롤러나 액션에 여러 정책을 적용했다면 모든 정책을 만족해야만 접근이 허용됩니다. 예를 들어:

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class SalaryController : Controller
{
    public ActionResult Payslip()
    {
    }

    [Authorize(Policy = "HumanResources")]
    public ActionResult UpdateSalary()
    {
    }
}
```

위의 예제에서는 컨트롤러에 적용된 정책에 따라, `EmployeeOnly` 정책을 만족하는 모든 신원이 `Payslip` 액션에 접근할 수 있습니다. 그러나 `UpdateSalary` 액션을 호출할 수 있으려면, 신원이 `EmployeeOnly` 정책과 `HumanResources` 정책을 *모두* 만족해야만 합니다.

만약, 생년월일 클레임의 날짜를 가져와서 나이를 계산하고 21살 이상인지 확인하는 것 같은 복잡한 정책이 필요하다면 [사용자 지정 정책 처리기](xref:security/authorization/policies)를 작성해야 합니다.
