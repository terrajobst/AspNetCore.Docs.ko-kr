---
title: ASP.NET Core의 정책 기반 권한 부여
author: rick-anderson
description: ASP.NET Core 응용 프로그램에서 사용자 지정 권한 정책 처리기를 작성 및 사용해서 권한 부여 요구 사항을 적용하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 10/05/2019
uid: security/authorization/policies
ms.openlocfilehash: eeb5ddd63ef8177325b35e5a666aa5e9ab047057
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652977"
---
# <a name="policy-based-authorization-in-aspnet-core"></a>ASP.NET Core의 정책 기반 권한 부여

::: moniker range=">= aspnetcore-3.0"

내부적으로 [역할 기반 권한 부여](xref:security/authorization/roles) 및 [클레임 기반 권한 부여](xref:security/authorization/claims) 는 요구 사항, 요구 사항 처리기 및 미리 구성 된 정책을 사용 합니다. 이런 빌딩 블록들은 권한 부여 평가를 코드로 표현할 수 있는 기능을 지원합니다. 결과적으로 보다 풍부하고 재사용 가능하며 테스트 가능한 권한 부여 구조를 만들 수 있습니다.

권한 부여 정책은 하나 이상의 요구 사항으로 구성됩니다. `Startup.ConfigureServices` 메서드에서 권한 부여 서비스 구성의 일부로 등록 됩니다.

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

위의 예제는 "AtLeast21"이라는 정책을 생성합니다. 요구 사항에 대 한 매개 변수로 제공 되는 최소 age&mdash;단일 요구 사항이 있습니다.

## <a name="iauthorizationservice"></a>IAuthorizationService 

권한 부여의 성공 여부를 결정 하는 기본 서비스는 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>합니다.

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

위의 코드는 [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)의 두 메서드를 강조 표시 합니다.

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>는 메서드가 없고 권한 부여의 성공 여부를 추적 하는 메커니즘을 포함 하는 표식 서비스입니다.

각 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler>는 요구 사항이 충족 되었는지 확인 하는 일을 담당 합니다.
<!--The following code is a copy/paste from 
https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

<xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> 클래스는 처리기에서 요구 사항이 충족 되었는지 여부를 표시 하는 데 사용 하는 내용입니다.

```csharp
 context.Succeed(requirement)
```

다음 코드는 권한 부여 서비스의 간소화 된 (주석으로 주석이 추가 됨) 기본 구현을 보여 줍니다.

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

다음 코드에서는 일반적인 `ConfigureServices`를 보여 줍니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddControllersWithViews();
    services.AddRazorPages();
}
```

권한 부여를 위해 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> 또는 `[Authorize(Policy = "Something")]`를 사용 합니다.

## <a name="applying-policies-to-mvc-controllers"></a>MVC 컨트롤러에 정책 적용

Razor Pages 사용 하는 경우이 문서의 [Razor Pages에 정책 적용](#applying-policies-to-razor-pages) 을 참조 하세요.

정책은 정책 이름과 함께 `[Authorize]` 특성을 사용 하 여 컨트롤러에 적용 됩니다. 다음은 그 예입니다.

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a>Razor Pages에 정책 적용

정책은 정책 이름과 함께 `[Authorize]` 특성을 사용 하 여 Razor Pages에 적용 됩니다. 다음은 그 예입니다.

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

[권한 부여 규칙](xref:security/authorization/razor-pages-authorization)을 사용 하 여 Razor Pages에 정책을 적용할 수도 있습니다.

## <a name="requirements"></a>요구 사항

권한 부여 요구 사항은 정책에서 현재 사용자 보안 주체를 평가 하는 데 사용할 수 있는 데이터 매개 변수의 컬렉션입니다. "AtLeast21" 정책에서 요구 사항은 최소 기간&mdash;단일 매개 변수입니다. 요구 사항은 빈 표식 인터페이스인 [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)을 구현 합니다. 매개 변수가 있는 최소 기간 요구 사항은 다음과 같이 구현할 수 있습니다.

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

권한 부여 정책에 여러 권한 부여 요구 사항이 포함 된 경우 정책 평가가 성공 하려면 모든 요구 사항이 통과 해야 합니다. 즉, 단일 권한 부여 정책에 추가 된 여러 권한 부여 요구 사항은 **및** 기준으로 처리 됩니다.

> [!NOTE]
> 요구 사항이 데이터나 속성을 가져야 할 필요는 없습니다.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>권한 부여 처리기

권한 부여 처리기는 요구 사항의 속성을 평가하는 역할을 담당합니다. 권한 부여 처리기는 제공 된 [Authorizationhandlercontext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) 에 대 한 요구 사항을 평가 하 여 액세스가 허용 되는지 확인 합니다.

요구 사항에는 [여러 처리기](#security-authorization-policies-based-multiple-handlers)가 있을 수 있습니다. 처리기는 [>\<TRequirement](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)사항에 대 한 authorizationhandler를 상속할 수 있습니다. 여기서 `TRequirement`은 처리 해야 하는 요구 사항입니다. 또는 처리기가 [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) 을 구현 하 여 둘 이상의 요구 사항을 처리할 수 있습니다.

### <a name="use-a-handler-for-one-requirement"></a>한 가지 요구 사항에 대한 처리기 사용하기

<a name="security-authorization-handler-example"></a>

다음은 최소 연령 처리기에서 단일 요구 사항을 사용하는 일대일 관계의 예제입니다.

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

이 코드는 먼저 현재 사용자의 신원이 우리가 알고 있고 신뢰할 수 있는 발급자로부터 발급된 생년월일 클레임을 갖고 있는지부터 확인합니다. 만약 클레임이 누락되었다면 권한을 부여할 수 없으므로 완료된 작업이 반환됩니다. 반면 클레임이 존재할 경우, 사용자의 나이가 계산됩니다. 그리고 나이가 요구 사항에 의해 정의된 최소 연령을 만족할 경우 권한 부여가 성공한 것으로 간주됩니다. 권한 부여가 성공 하면 해당 하는 요구 사항을 유일한 매개 변수로 사용 하 여 `context.Succeed` 호출 됩니다.

### <a name="use-a-handler-for-multiple-requirements"></a>여러 요구 사항에 대한 처리기 사용하기

다음은 권한 처리기가 세 가지 종류의 요구 사항을 처리할 수 있는 일 대 다 관계의 예입니다.

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

위의 코드는 성공으로 표시 되지 않은 요구 사항이 포함 된 속성&mdash;[Pendingrequirements 사항을](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) 트래버스 합니다. `ReadPermission` 요구 사항에 대해 사용자는 요청 된 리소스에 액세스 하기 위한 소유자 또는 스폰서 여야 합니다. `EditPermission` 또는 `DeletePermission` 요구 사항의 경우 요청 된 리소스에 액세스 하려면 소유자 여야 합니다.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>처리기 등록하기

처리기는 구성 과정 중 서비스 컬렉션에 등록됩니다. 다음은 그 예입니다.

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

위의 코드는 `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`를 호출 하 여 `MinimumAgeHandler`를 singleton으로 등록 합니다. 기본 제공 [서비스 수명](xref:fundamentals/dependency-injection#service-lifetimes)중 하나를 사용 하 여 처리기를 등록할 수 있습니다.

## <a name="what-should-a-handler-return"></a>처리기가 반환해야 하는 결과는?

[처리기 예제의](#security-authorization-handler-example) `Handle` 메서드는 값을 반환 하지 않습니다. 그렇다면 성공 혹은 실패 여부는 어떻게 확인할 수 있을까요?

* 처리기는 성공적으로 유효성을 검사 한 요구 사항을 전달 하 여 `context.Succeed(IAuthorizationRequirement requirement)`를 호출 하 여 성공을 나타냅니다.

* 일반적으로 처리기는 실패를 처리할 필요가 없는데, 동일한 요구 사항에 대한 다른 처리기가 성공할 수도 있기 때문입니다.

* 오류를 보장 하기 위해 다른 요구 사항 처리기가 성공 하는 경우에도 `context.Fail`를 호출 합니다.

처리기가 `context.Succeed` 또는 `context.Fail`를 호출 하는 경우 다른 모든 처리기가 계속 호출 됩니다. 이렇게 하면 다른 처리기가 성공적으로 유효성을 검사 하거나 실패 한 경우에도 발생 하는 로깅 등의 부작용이 발생할 수 있습니다. `false`로 설정 된 경우 ASP.NET Core 1.1 이상에서 사용 가능) [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) 속성은 `context.Fail` 호출 될 때 처리기의 실행을 짧은 회로 합니다. `InvokeHandlersAfterFailure` 기본값은 `true`이며이 경우 모든 처리기가 호출 됩니다.

> [!NOTE]
> 인증이 실패 하는 경우에도 권한 부여 처리기가 호출 됩니다.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>요구 사항에 대해 여러 개의 처기리가 필요한 이유는?

평가를 **또는** 기준으로 하려는 경우 단일 요구 사항에 대 한 여러 처리기를 구현 합니다. 예를 들어, Microsoft에는 키 카드로만 열 수 있는 문이 있습니다. 만약 키 카드를 집에 두고 왔다면 접수원이 임시 스티커를 인쇄하고 대신 문을 열어줍니다. 이 시나리오에서는 단일 요구 사항이 *BuildingEntry*하나는 단일 요구 사항을 검사 합니다.

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

두 처리기가 모두 [등록](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)되었는지 확인 합니다. 정책이 `BuildingEntryRequirement`을 평가할 때 두 처리기가 모두 성공 하면 정책 평가가 성공 합니다.

## <a name="using-a-func-to-fulfill-a-policy"></a>func를 이용해서 정책 구성하기

코드로 정책을 표현해서 구성하는 편이 더 간단한 경우도 있습니다. `RequireAssertion` 정책 작성기를 사용 하 여 정책을 구성할 때 `Func<AuthorizationHandlerContext, bool>`를 제공할 수 있습니다.

예를 들어 이전 `BadgeEntryHandler`은 다음과 같이 다시 작성할 수 있습니다.

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="accessing-mvc-request-context-in-handlers"></a>처리기에서 MVC 요청 컨텍스트 접근하기

권한 부여 처리기에서 구현 하는 `HandleRequirementAsync` 메서드에는 두 개의 매개 변수, 즉 `AuthorizationHandlerContext`와 처리 하는 `TRequirement`이 있습니다. MVC 또는 Jabbr와 같은 프레임 워크는 추가 정보를 전달 하기 위해 `AuthorizationHandlerContext`의 `Resource` 속성에 개체를 자유롭게 추가할 수 있습니다.

예를 들어 MVC는 `Resource` 속성에서 [Authorizationfiltercontext](/dotnet/api/?term=AuthorizationFilterContext) 의 인스턴스를 전달 합니다. 이 속성은 MVC 및 Razor Pages에서 제공 하는 `HttpContext`, `RouteData`및 기타 모든 항목에 대 한 액세스를 제공 합니다.

`Resource` 속성을 사용 하는 것은 프레임 워크에 특정 합니다. `Resource` 속성의 정보를 사용 하면 권한 부여 정책이 특정 프레임 워크로 제한 됩니다. `is` 키워드를 사용 하 여 `Resource` 속성을 캐스팅 한 다음, 다른 프레임 워크에서 실행 될 때 코드가 `InvalidCastException`와 충돌 하지 않도록 캐스팅이 성공 했는지 확인 해야 합니다.

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"

내부적으로 [역할 기반 권한 부여](xref:security/authorization/roles) 및 [클레임 기반 권한 부여](xref:security/authorization/claims) 는 요구 사항, 요구 사항 처리기 및 미리 구성 된 정책을 사용 합니다. 이런 빌딩 블록들은 권한 부여 평가를 코드로 표현할 수 있는 기능을 지원합니다. 결과적으로 보다 풍부하고 재사용 가능하며 테스트 가능한 권한 부여 구조를 만들 수 있습니다.

권한 부여 정책은 하나 이상의 요구 사항으로 구성됩니다. `Startup.ConfigureServices` 메서드에서 권한 부여 서비스 구성의 일부로 등록 됩니다.

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

위의 예제는 "AtLeast21"이라는 정책을 생성합니다. 요구 사항에 대 한 매개 변수로 제공 되는 최소 age&mdash;단일 요구 사항이 있습니다.

## <a name="iauthorizationservice"></a>IAuthorizationService 

권한 부여의 성공 여부를 결정 하는 기본 서비스는 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>합니다.

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

위의 코드는 [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)의 두 메서드를 강조 표시 합니다.

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>는 메서드가 없고 권한 부여의 성공 여부를 추적 하는 메커니즘을 포함 하는 표식 서비스입니다.

각 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler>는 요구 사항이 충족 되었는지 확인 하는 일을 담당 합니다.
<!--The following code is a copy/paste from 
https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

<xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> 클래스는 처리기에서 요구 사항이 충족 되었는지 여부를 표시 하는 데 사용 하는 내용입니다.

```csharp
 context.Succeed(requirement)
```

다음 코드는 권한 부여 서비스의 간소화 된 (주석으로 주석이 추가 됨) 기본 구현을 보여 줍니다.

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

다음 코드에서는 일반적인 `ConfigureServices`를 보여 줍니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
}
```

권한 부여를 위해 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> 또는 `[Authorize(Policy = "Something")]`를 사용 합니다.

## <a name="applying-policies-to-mvc-controllers"></a>MVC 컨트롤러에 정책 적용

Razor Pages 사용 하는 경우이 문서의 [Razor Pages에 정책 적용](#applying-policies-to-razor-pages) 을 참조 하세요.

정책은 정책 이름과 함께 `[Authorize]` 특성을 사용 하 여 컨트롤러에 적용 됩니다. 다음은 그 예입니다.

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a>Razor Pages에 정책 적용

정책은 정책 이름과 함께 `[Authorize]` 특성을 사용 하 여 Razor Pages에 적용 됩니다. 다음은 그 예입니다.

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

[권한 부여 규칙](xref:security/authorization/razor-pages-authorization)을 사용 하 여 Razor Pages에 정책을 적용할 수도 있습니다.

## <a name="requirements"></a>요구 사항

권한 부여 요구 사항은 정책에서 현재 사용자 보안 주체를 평가 하는 데 사용할 수 있는 데이터 매개 변수의 컬렉션입니다. "AtLeast21" 정책에서 요구 사항은 최소 기간&mdash;단일 매개 변수입니다. 요구 사항은 빈 표식 인터페이스인 [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)을 구현 합니다. 매개 변수가 있는 최소 기간 요구 사항은 다음과 같이 구현할 수 있습니다.

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

권한 부여 정책에 여러 권한 부여 요구 사항이 포함 된 경우 정책 평가가 성공 하려면 모든 요구 사항이 통과 해야 합니다. 즉, 단일 권한 부여 정책에 추가 된 여러 권한 부여 요구 사항은 **및** 기준으로 처리 됩니다.

> [!NOTE]
> 요구 사항이 데이터나 속성을 가져야 할 필요는 없습니다.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>권한 부여 처리기

권한 부여 처리기는 요구 사항의 속성을 평가하는 역할을 담당합니다. 권한 부여 처리기는 제공 된 [Authorizationhandlercontext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) 에 대 한 요구 사항을 평가 하 여 액세스가 허용 되는지 확인 합니다.

요구 사항에는 [여러 처리기](#security-authorization-policies-based-multiple-handlers)가 있을 수 있습니다. 처리기는 [>\<TRequirement](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)사항에 대 한 authorizationhandler를 상속할 수 있습니다. 여기서 `TRequirement`은 처리 해야 하는 요구 사항입니다. 또는 처리기가 [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) 을 구현 하 여 둘 이상의 요구 사항을 처리할 수 있습니다.

### <a name="use-a-handler-for-one-requirement"></a>한 가지 요구 사항에 대한 처리기 사용하기

<a name="security-authorization-handler-example"></a>

다음은 최소 연령 처리기에서 단일 요구 사항을 사용하는 일대일 관계의 예제입니다.

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

이 코드는 먼저 현재 사용자의 신원이 우리가 알고 있고 신뢰할 수 있는 발급자로부터 발급된 생년월일 클레임을 갖고 있는지부터 확인합니다. 만약 클레임이 누락되었다면 권한을 부여할 수 없으므로 완료된 작업이 반환됩니다. 반면 클레임이 존재할 경우, 사용자의 나이가 계산됩니다. 그리고 나이가 요구 사항에 의해 정의된 최소 연령을 만족할 경우 권한 부여가 성공한 것으로 간주됩니다. 권한 부여가 성공 하면 해당 하는 요구 사항을 유일한 매개 변수로 사용 하 여 `context.Succeed` 호출 됩니다.

### <a name="use-a-handler-for-multiple-requirements"></a>여러 요구 사항에 대한 처리기 사용하기

다음은 권한 처리기가 세 가지 종류의 요구 사항을 처리할 수 있는 일 대 다 관계의 예입니다.

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

위의 코드는 성공으로 표시 되지 않은 요구 사항이 포함 된 속성&mdash;[Pendingrequirements 사항을](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) 트래버스 합니다. `ReadPermission` 요구 사항에 대해 사용자는 요청 된 리소스에 액세스 하기 위한 소유자 또는 스폰서 여야 합니다. `EditPermission` 또는 `DeletePermission` 요구 사항의 경우 요청 된 리소스에 액세스 하려면 소유자 여야 합니다.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>처리기 등록하기

처리기는 구성 과정 중 서비스 컬렉션에 등록됩니다. 다음은 그 예입니다.

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

위의 코드는 `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`를 호출 하 여 `MinimumAgeHandler`를 singleton으로 등록 합니다. 기본 제공 [서비스 수명](xref:fundamentals/dependency-injection#service-lifetimes)중 하나를 사용 하 여 처리기를 등록할 수 있습니다.

## <a name="what-should-a-handler-return"></a>처리기가 반환해야 하는 결과는?

[처리기 예제의](#security-authorization-handler-example) `Handle` 메서드는 값을 반환 하지 않습니다. 그렇다면 성공 혹은 실패 여부는 어떻게 확인할 수 있을까요?

* 처리기는 성공적으로 유효성을 검사 한 요구 사항을 전달 하 여 `context.Succeed(IAuthorizationRequirement requirement)`를 호출 하 여 성공을 나타냅니다.

* 일반적으로 처리기는 실패를 처리할 필요가 없는데, 동일한 요구 사항에 대한 다른 처리기가 성공할 수도 있기 때문입니다.

* 오류를 보장 하기 위해 다른 요구 사항 처리기가 성공 하는 경우에도 `context.Fail`를 호출 합니다.

처리기가 `context.Succeed` 또는 `context.Fail`를 호출 하는 경우 다른 모든 처리기가 계속 호출 됩니다. 이렇게 하면 다른 처리기가 성공적으로 유효성을 검사 하거나 실패 한 경우에도 발생 하는 로깅 등의 부작용이 발생할 수 있습니다. `false`로 설정 된 경우 ASP.NET Core 1.1 이상에서 사용 가능) [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) 속성은 `context.Fail` 호출 될 때 처리기의 실행을 짧은 회로 합니다. `InvokeHandlersAfterFailure` 기본값은 `true`이며이 경우 모든 처리기가 호출 됩니다.

> [!NOTE]
> 인증이 실패 하는 경우에도 권한 부여 처리기가 호출 됩니다.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>요구 사항에 대해 여러 개의 처기리가 필요한 이유는?

평가를 **또는** 기준으로 하려는 경우 단일 요구 사항에 대 한 여러 처리기를 구현 합니다. 예를 들어, Microsoft에는 키 카드로만 열 수 있는 문이 있습니다. 만약 키 카드를 집에 두고 왔다면 접수원이 임시 스티커를 인쇄하고 대신 문을 열어줍니다. 이 시나리오에서는 단일 요구 사항이 *BuildingEntry*하나는 단일 요구 사항을 검사 합니다.

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

두 처리기가 모두 [등록](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)되었는지 확인 합니다. 정책이 `BuildingEntryRequirement`을 평가할 때 두 처리기가 모두 성공 하면 정책 평가가 성공 합니다.

## <a name="using-a-func-to-fulfill-a-policy"></a>func를 이용해서 정책 구성하기

코드로 정책을 표현해서 구성하는 편이 더 간단한 경우도 있습니다. `RequireAssertion` 정책 작성기를 사용 하 여 정책을 구성할 때 `Func<AuthorizationHandlerContext, bool>`를 제공할 수 있습니다.

예를 들어 이전 `BadgeEntryHandler`은 다음과 같이 다시 작성할 수 있습니다.

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a>처리기에서 MVC 요청 컨텍스트 접근하기

권한 부여 처리기에서 구현 하는 `HandleRequirementAsync` 메서드에는 두 개의 매개 변수, 즉 `AuthorizationHandlerContext`와 처리 하는 `TRequirement`이 있습니다. MVC 또는 Jabbr와 같은 프레임 워크는 추가 정보를 전달 하기 위해 `AuthorizationHandlerContext`의 `Resource` 속성에 개체를 자유롭게 추가할 수 있습니다.

예를 들어 MVC는 `Resource` 속성에서 [Authorizationfiltercontext](/dotnet/api/?term=AuthorizationFilterContext) 의 인스턴스를 전달 합니다. 이 속성은 MVC 및 Razor Pages에서 제공 하는 `HttpContext`, `RouteData`및 기타 모든 항목에 대 한 액세스를 제공 합니다.

`Resource` 속성을 사용 하는 것은 프레임 워크에 특정 합니다. `Resource` 속성의 정보를 사용 하면 권한 부여 정책이 특정 프레임 워크로 제한 됩니다. `is` 키워드를 사용 하 여 `Resource` 속성을 캐스팅 한 다음, 다른 프레임 워크에서 실행 될 때 코드가 `InvalidCastException`와 충돌 하지 않도록 캐스팅이 성공 했는지 확인 해야 합니다.

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end