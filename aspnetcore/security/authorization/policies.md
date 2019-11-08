---
title: ASP.NET Core에서 정책 기반 권한 부여
author: rick-anderson
description: ASP.NET Core 앱에서 권한 부여 요구 사항을 적용 하기 위한 권한 부여 정책 처리기를 만들고 사용 하는 방법에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 10/05/2019
uid: security/authorization/policies
ms.openlocfilehash: e3929fb0f45d4ba28f46a6b42b653940de0badb0
ms.sourcegitcommit: 6628cd23793b66e4ce88788db641a5bbf470c3c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73761044"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="36646-103">ASP.NET Core에서 정책 기반 권한 부여</span><span class="sxs-lookup"><span data-stu-id="36646-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="36646-104">내부적으로 [역할 기반 권한 부여](xref:security/authorization/roles) 및 [클레임 기반 권한 부여](xref:security/authorization/claims) 는 요구 사항, 요구 사항 처리기 및 미리 구성 된 정책을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="36646-105">이러한 구성 요소는 코드에서 권한 부여 평가 식을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="36646-106">그 결과는 더 풍부 하 고 재사용 가능한 테스트 가능한 권한 부여 구조입니다.</span><span class="sxs-lookup"><span data-stu-id="36646-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="36646-107">권한 부여 정책은 하나 이상의 요구 사항으로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36646-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="36646-108">`Startup.ConfigureServices` 메서드에서 권한 부여 서비스 구성의 일부로 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36646-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="36646-109">위의 예에서는 "AtLeast21" 정책이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="36646-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="36646-110">요구 사항에 대 한 매개 변수로 제공 되는 최소 age&mdash;단일 요구 사항이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="36646-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="36646-111">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="36646-111">IAuthorizationService</span></span> 

<span data-ttu-id="36646-112">권한 부여의 성공 여부를 결정 하는 기본 서비스는 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="36646-113">위의 코드는 [IAuthorizationService](https://github.com/aspnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)의 두 메서드를 강조 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/aspnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="36646-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>는 메서드가 없고 권한 부여의 성공 여부를 추적 하는 메커니즘을 포함 하는 표식 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="36646-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="36646-115">각 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler>는 요구 사항이 충족 되었는지 확인 하는 일을 담당 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
<!--The following code is a copy/paste from 
https://github.com/aspnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

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

<span data-ttu-id="36646-116"><xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> 클래스는 처리기에서 요구 사항이 충족 되었는지 여부를 표시 하는 데 사용 하는 내용입니다.</span><span class="sxs-lookup"><span data-stu-id="36646-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="36646-117">다음 코드는 권한 부여 서비스의 간소화 된 (주석으로 주석이 추가 됨) 기본 구현을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="36646-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="36646-118">다음 코드에서는 일반적인 `ConfigureServices`를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="36646-118">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="36646-119">권한 부여를 위해 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> 또는 `[Authorize(Policy = "Something")]`를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="36646-120">MVC 컨트롤러에 정책 적용</span><span class="sxs-lookup"><span data-stu-id="36646-120">Applying policies to MVC controllers</span></span>

<span data-ttu-id="36646-121">Razor Pages 사용 하는 경우이 문서의 [Razor Pages에 정책 적용](#applying-policies-to-razor-pages) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="36646-121">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="36646-122">정책은 정책 이름과 함께 `[Authorize]` 특성을 사용 하 여 컨트롤러에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36646-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="36646-123">예를 들면,</span><span class="sxs-lookup"><span data-stu-id="36646-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="36646-124">Razor Pages에 정책 적용</span><span class="sxs-lookup"><span data-stu-id="36646-124">Applying policies to Razor Pages</span></span>

<span data-ttu-id="36646-125">정책은 정책 이름과 함께 `[Authorize]` 특성을 사용 하 여 Razor Pages에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36646-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="36646-126">예를 들면,</span><span class="sxs-lookup"><span data-stu-id="36646-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="36646-127">[권한 부여 규칙](xref:security/authorization/razor-pages-authorization)을 사용 하 여 Razor Pages에 정책을 적용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="36646-127">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="36646-128">요구 사항</span><span class="sxs-lookup"><span data-stu-id="36646-128">Requirements</span></span>

<span data-ttu-id="36646-129">권한 부여 요구 사항은 정책에서 현재 사용자 보안 주체를 평가 하는 데 사용할 수 있는 데이터 매개 변수의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="36646-129">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="36646-130">"AtLeast21" 정책에서 요구 사항은 최소 기간&mdash;단일 매개 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="36646-130">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="36646-131">요구 사항은 빈 표식 인터페이스인 [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)을 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-131">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="36646-132">매개 변수가 있는 최소 기간 요구 사항은 다음과 같이 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="36646-132">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="36646-133">권한 부여 정책에 여러 권한 부여 요구 사항이 포함 된 경우 정책 평가가 성공 하려면 모든 요구 사항이 통과 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-133">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="36646-134">즉, 단일 권한 부여 정책에 추가 된 여러 권한 부여 요구 사항은 **및** 기준으로 처리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36646-134">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="36646-135">요구 사항에는 데이터 또는 속성이 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="36646-135">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="36646-136">권한 부여 처리기</span><span class="sxs-lookup"><span data-stu-id="36646-136">Authorization handlers</span></span>

<span data-ttu-id="36646-137">권한 부여 처리기는 요구 사항의 속성을 평가 하는 일을 담당 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-137">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="36646-138">권한 부여 처리기는 제공 된 [Authorizationhandlercontext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) 에 대 한 요구 사항을 평가 하 여 액세스가 허용 되는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-138">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="36646-139">요구 사항에는 [여러 처리기](#security-authorization-policies-based-multiple-handlers)가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="36646-139">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="36646-140">처리기는 [\<TRequirement](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)사항에 대 한 authorizationhandler를 상속할 수 있습니다. 여기서 `TRequirement`은 처리 해야 하는 요구 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="36646-140">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="36646-141">또는 처리기가 [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) 을 구현 하 여 둘 이상의 요구 사항을 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="36646-141">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="36646-142">한 가지 요구 사항에 대 한 처리기 사용</span><span class="sxs-lookup"><span data-stu-id="36646-142">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="36646-143">다음은 최소 사용 기간 처리기에서 단일 요구 사항을 활용 하는 일 대 일 관계의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="36646-143">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="36646-144">위의 코드는 현재 사용자 보안 주체에 알려진 발급자와 신뢰할 수 있는 발급자가 발급 한 날짜가 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-144">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="36646-145">클레임이 없는 경우 권한 부여를 수행할 수 없습니다 .이 경우 완료 된 작업이 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36646-145">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="36646-146">클레임이 있는 경우 사용자의 나이가 계산 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36646-146">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="36646-147">사용자가 요구 사항에 정의 된 최소 보존 기간을 충족 하면 권한 부여가 성공으로 간주 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36646-147">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="36646-148">권한 부여가 성공 하면 해당 하는 요구 사항을 유일한 매개 변수로 사용 하 여 `context.Succeed` 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36646-148">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="36646-149">여러 요구 사항에 대 한 처리기 사용</span><span class="sxs-lookup"><span data-stu-id="36646-149">Use a handler for multiple requirements</span></span>

<span data-ttu-id="36646-150">다음은 권한 처리기가 세 가지 종류의 요구 사항을 처리할 수 있는 일 대 다 관계의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="36646-150">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="36646-151">위의 코드는 성공으로 표시 되지 않은 요구 사항이 포함 된 속성&mdash;[Pendingrequirements 사항을](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) 트래버스 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-151">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="36646-152">`ReadPermission` 요구 사항에 대해 사용자는 요청 된 리소스에 액세스 하기 위한 소유자 또는 스폰서 여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-152">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="36646-153">`EditPermission` 또는 `DeletePermission` 요구 사항의 경우 요청 된 리소스에 액세스 하려면 소유자 여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-153">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="36646-154">처리기 등록</span><span class="sxs-lookup"><span data-stu-id="36646-154">Handler registration</span></span>

<span data-ttu-id="36646-155">처리기는 구성 하는 동안 서비스 컬렉션에 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36646-155">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="36646-156">예를 들면,</span><span class="sxs-lookup"><span data-stu-id="36646-156">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="36646-157">위의 코드는 `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`를 호출 하 여 `MinimumAgeHandler`를 singleton으로 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-157">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="36646-158">기본 제공 [서비스 수명](xref:fundamentals/dependency-injection#service-lifetimes)중 하나를 사용 하 여 처리기를 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="36646-158">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="36646-159">처리기에서 반환 해야 하는 작업</span><span class="sxs-lookup"><span data-stu-id="36646-159">What should a handler return?</span></span>

<span data-ttu-id="36646-160">[처리기 예제의](#security-authorization-handler-example) `Handle` 메서드는 값을 반환 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="36646-160">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="36646-161">성공 또는 실패의 상태는 어떻게 표시 되나요?</span><span class="sxs-lookup"><span data-stu-id="36646-161">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="36646-162">처리기는 성공적으로 유효성을 검사 한 요구 사항을 전달 하 여 `context.Succeed(IAuthorizationRequirement requirement)`를 호출 하 여 성공을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="36646-162">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="36646-163">같은 요구 사항에 대 한 다른 처리기가 성공할 수 있으므로 처리기는 일반적으로 오류를 처리할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="36646-163">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="36646-164">오류를 보장 하기 위해 다른 요구 사항 처리기가 성공 하는 경우에도 `context.Fail`를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-164">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="36646-165">처리기가 `context.Succeed` 또는 `context.Fail`를 호출 하는 경우 다른 모든 처리기가 계속 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36646-165">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="36646-166">이렇게 하면 다른 처리기가 성공적으로 유효성을 검사 하거나 실패 한 경우에도 발생 하는 로깅 등의 부작용이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="36646-166">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="36646-167">`false`로 설정 된 경우 ASP.NET Core 1.1 이상에서 사용 가능) [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) 속성은 `context.Fail` 호출 될 때 처리기의 실행을 짧은 회로 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-167">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="36646-168">`InvokeHandlersAfterFailure` 기본값은 `true`이며이 경우 모든 처리기가 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36646-168">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="36646-169">인증이 실패 하는 경우에도 권한 부여 처리기가 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36646-169">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="36646-170">요구 사항에 대 한 여러 처리기가 필요한 이유는 무엇 인가요?</span><span class="sxs-lookup"><span data-stu-id="36646-170">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="36646-171">평가를 **또는** 기준으로 하려는 경우 단일 요구 사항에 대 한 여러 처리기를 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-171">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="36646-172">예를 들어 Microsoft에는 키 카드만 여는 문이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="36646-172">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="36646-173">키 카드를 집에서 벗어나면 접수원는 임시 스티커를 인쇄 하 고 도어를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="36646-173">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="36646-174">이 시나리오에서는 단일 요구 사항이 *BuildingEntry*하나는 단일 요구 사항을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-174">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="36646-175">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="36646-175">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="36646-176">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="36646-176">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="36646-177">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="36646-177">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="36646-178">두 처리기가 모두 [등록](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)되었는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-178">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="36646-179">정책이 `BuildingEntryRequirement`을 평가할 때 두 처리기가 모두 성공 하면 정책 평가가 성공 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-179">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="36646-180">Func를 사용 하 여 정책 수행</span><span class="sxs-lookup"><span data-stu-id="36646-180">Using a func to fulfill a policy</span></span>

<span data-ttu-id="36646-181">정책을 처리 하는 것이 간단 하 게 코드를 표현할 수 있는 경우가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="36646-181">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="36646-182">`RequireAssertion` 정책 작성기를 사용 하 여 정책을 구성할 때 `Func<AuthorizationHandlerContext, bool>`를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="36646-182">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="36646-183">예를 들어 이전 `BadgeEntryHandler`은 다음과 같이 다시 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="36646-183">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="36646-184">처리기의 MVC 요청 컨텍스트 액세스</span><span class="sxs-lookup"><span data-stu-id="36646-184">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="36646-185">권한 부여 처리기에서 구현 하는 `HandleRequirementAsync` 메서드에는 두 개의 매개 변수, 즉 `AuthorizationHandlerContext`와 처리 하는 `TRequirement`이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="36646-185">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="36646-186">MVC 또는 Jabbr와 같은 프레임 워크는 추가 정보를 전달 하기 위해 `AuthorizationHandlerContext`의 `Resource` 속성에 개체를 자유롭게 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="36646-186">Frameworks such as MVC or Jabbr are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="36646-187">예를 들어 MVC는 `Resource` 속성에서 [Authorizationfiltercontext](/dotnet/api/?term=AuthorizationFilterContext) 의 인스턴스를 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-187">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="36646-188">이 속성은 MVC 및 Razor Pages에서 제공 하는 `HttpContext`, `RouteData`및 기타 모든 항목에 대 한 액세스를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-188">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="36646-189">`Resource` 속성을 사용 하는 것은 프레임 워크에 특정 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-189">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="36646-190">`Resource` 속성의 정보를 사용 하면 권한 부여 정책이 특정 프레임 워크로 제한 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36646-190">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="36646-191">`is` 키워드를 사용 하 여 `Resource` 속성을 캐스팅 한 다음, 다른 프레임 워크에서 실행 될 때 코드가 `InvalidCastException`와 충돌 하지 않도록 캐스팅이 성공 했는지 확인 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-191">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

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

<span data-ttu-id="36646-192">내부적으로 [역할 기반 권한 부여](xref:security/authorization/roles) 및 [클레임 기반 권한 부여](xref:security/authorization/claims) 는 요구 사항, 요구 사항 처리기 및 미리 구성 된 정책을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-192">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="36646-193">이러한 구성 요소는 코드에서 권한 부여 평가 식을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-193">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="36646-194">그 결과는 더 풍부 하 고 재사용 가능한 테스트 가능한 권한 부여 구조입니다.</span><span class="sxs-lookup"><span data-stu-id="36646-194">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="36646-195">권한 부여 정책은 하나 이상의 요구 사항으로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36646-195">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="36646-196">`Startup.ConfigureServices` 메서드에서 권한 부여 서비스 구성의 일부로 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36646-196">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="36646-197">위의 예에서는 "AtLeast21" 정책이 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="36646-197">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="36646-198">요구 사항에 대 한 매개 변수로 제공 되는 최소 age&mdash;단일 요구 사항이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="36646-198">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="36646-199">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="36646-199">IAuthorizationService</span></span> 

<span data-ttu-id="36646-200">권한 부여의 성공 여부를 결정 하는 기본 서비스는 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-200">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="36646-201">위의 코드는 [IAuthorizationService](https://github.com/aspnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)의 두 메서드를 강조 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-201">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/aspnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="36646-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>는 메서드가 없고 권한 부여의 성공 여부를 추적 하는 메커니즘을 포함 하는 표식 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="36646-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="36646-203">각 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler>는 요구 사항이 충족 되었는지 확인 하는 일을 담당 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-203">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
<!--The following code is a copy/paste from 
https://github.com/aspnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

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

<span data-ttu-id="36646-204"><xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> 클래스는 처리기에서 요구 사항이 충족 되었는지 여부를 표시 하는 데 사용 하는 내용입니다.</span><span class="sxs-lookup"><span data-stu-id="36646-204">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="36646-205">다음 코드는 권한 부여 서비스의 간소화 된 (주석으로 주석이 추가 됨) 기본 구현을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="36646-205">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="36646-206">다음 코드에서는 일반적인 `ConfigureServices`를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="36646-206">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="36646-207">권한 부여를 위해 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> 또는 `[Authorize(Policy = "Something")]`를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-207">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="36646-208">MVC 컨트롤러에 정책 적용</span><span class="sxs-lookup"><span data-stu-id="36646-208">Applying policies to MVC controllers</span></span>

<span data-ttu-id="36646-209">Razor Pages 사용 하는 경우이 문서의 [Razor Pages에 정책 적용](#applying-policies-to-razor-pages) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="36646-209">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="36646-210">정책은 정책 이름과 함께 `[Authorize]` 특성을 사용 하 여 컨트롤러에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36646-210">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="36646-211">예를 들면,</span><span class="sxs-lookup"><span data-stu-id="36646-211">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="36646-212">Razor Pages에 정책 적용</span><span class="sxs-lookup"><span data-stu-id="36646-212">Applying policies to Razor Pages</span></span>

<span data-ttu-id="36646-213">정책은 정책 이름과 함께 `[Authorize]` 특성을 사용 하 여 Razor Pages에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36646-213">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="36646-214">예를 들면,</span><span class="sxs-lookup"><span data-stu-id="36646-214">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="36646-215">[권한 부여 규칙](xref:security/authorization/razor-pages-authorization)을 사용 하 여 Razor Pages에 정책을 적용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="36646-215">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="36646-216">요구 사항</span><span class="sxs-lookup"><span data-stu-id="36646-216">Requirements</span></span>

<span data-ttu-id="36646-217">권한 부여 요구 사항은 정책에서 현재 사용자 보안 주체를 평가 하는 데 사용할 수 있는 데이터 매개 변수의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="36646-217">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="36646-218">"AtLeast21" 정책에서 요구 사항은 최소 기간&mdash;단일 매개 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="36646-218">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="36646-219">요구 사항은 빈 표식 인터페이스인 [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)을 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-219">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="36646-220">매개 변수가 있는 최소 기간 요구 사항은 다음과 같이 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="36646-220">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="36646-221">권한 부여 정책에 여러 권한 부여 요구 사항이 포함 된 경우 정책 평가가 성공 하려면 모든 요구 사항이 통과 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-221">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="36646-222">즉, 단일 권한 부여 정책에 추가 된 여러 권한 부여 요구 사항은 **및** 기준으로 처리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36646-222">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="36646-223">요구 사항에는 데이터 또는 속성이 필요 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="36646-223">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="36646-224">권한 부여 처리기</span><span class="sxs-lookup"><span data-stu-id="36646-224">Authorization handlers</span></span>

<span data-ttu-id="36646-225">권한 부여 처리기는 요구 사항의 속성을 평가 하는 일을 담당 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-225">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="36646-226">권한 부여 처리기는 제공 된 [Authorizationhandlercontext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) 에 대 한 요구 사항을 평가 하 여 액세스가 허용 되는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-226">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="36646-227">요구 사항에는 [여러 처리기](#security-authorization-policies-based-multiple-handlers)가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="36646-227">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="36646-228">처리기는 [\<TRequirement](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)사항에 대 한 authorizationhandler를 상속할 수 있습니다. 여기서 `TRequirement`은 처리 해야 하는 요구 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="36646-228">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="36646-229">또는 처리기가 [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) 을 구현 하 여 둘 이상의 요구 사항을 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="36646-229">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="36646-230">한 가지 요구 사항에 대 한 처리기 사용</span><span class="sxs-lookup"><span data-stu-id="36646-230">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="36646-231">다음은 최소 사용 기간 처리기에서 단일 요구 사항을 활용 하는 일 대 일 관계의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="36646-231">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="36646-232">위의 코드는 현재 사용자 보안 주체에 알려진 발급자와 신뢰할 수 있는 발급자가 발급 한 날짜가 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-232">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="36646-233">클레임이 없는 경우 권한 부여를 수행할 수 없습니다 .이 경우 완료 된 작업이 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36646-233">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="36646-234">클레임이 있는 경우 사용자의 나이가 계산 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36646-234">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="36646-235">사용자가 요구 사항에 정의 된 최소 보존 기간을 충족 하면 권한 부여가 성공으로 간주 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36646-235">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="36646-236">권한 부여가 성공 하면 해당 하는 요구 사항을 유일한 매개 변수로 사용 하 여 `context.Succeed` 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36646-236">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="36646-237">여러 요구 사항에 대 한 처리기 사용</span><span class="sxs-lookup"><span data-stu-id="36646-237">Use a handler for multiple requirements</span></span>

<span data-ttu-id="36646-238">다음은 권한 처리기가 세 가지 종류의 요구 사항을 처리할 수 있는 일 대 다 관계의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="36646-238">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="36646-239">위의 코드는 성공으로 표시 되지 않은 요구 사항이 포함 된 속성&mdash;[Pendingrequirements 사항을](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) 트래버스 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-239">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="36646-240">`ReadPermission` 요구 사항에 대해 사용자는 요청 된 리소스에 액세스 하기 위한 소유자 또는 스폰서 여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-240">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="36646-241">`EditPermission` 또는 `DeletePermission` 요구 사항의 경우 요청 된 리소스에 액세스 하려면 소유자 여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-241">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="36646-242">처리기 등록</span><span class="sxs-lookup"><span data-stu-id="36646-242">Handler registration</span></span>

<span data-ttu-id="36646-243">처리기는 구성 하는 동안 서비스 컬렉션에 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36646-243">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="36646-244">예를 들면,</span><span class="sxs-lookup"><span data-stu-id="36646-244">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="36646-245">위의 코드는 `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`를 호출 하 여 `MinimumAgeHandler`를 singleton으로 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-245">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="36646-246">기본 제공 [서비스 수명](xref:fundamentals/dependency-injection#service-lifetimes)중 하나를 사용 하 여 처리기를 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="36646-246">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="36646-247">처리기에서 반환 해야 하는 작업</span><span class="sxs-lookup"><span data-stu-id="36646-247">What should a handler return?</span></span>

<span data-ttu-id="36646-248">[처리기 예제의](#security-authorization-handler-example) `Handle` 메서드는 값을 반환 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="36646-248">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="36646-249">성공 또는 실패의 상태는 어떻게 표시 되나요?</span><span class="sxs-lookup"><span data-stu-id="36646-249">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="36646-250">처리기는 성공적으로 유효성을 검사 한 요구 사항을 전달 하 여 `context.Succeed(IAuthorizationRequirement requirement)`를 호출 하 여 성공을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="36646-250">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="36646-251">같은 요구 사항에 대 한 다른 처리기가 성공할 수 있으므로 처리기는 일반적으로 오류를 처리할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="36646-251">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="36646-252">오류를 보장 하기 위해 다른 요구 사항 처리기가 성공 하는 경우에도 `context.Fail`를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-252">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="36646-253">처리기가 `context.Succeed` 또는 `context.Fail`를 호출 하는 경우 다른 모든 처리기가 계속 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36646-253">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="36646-254">이렇게 하면 다른 처리기가 성공적으로 유효성을 검사 하거나 실패 한 경우에도 발생 하는 로깅 등의 부작용이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="36646-254">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="36646-255">`false`로 설정 된 경우 ASP.NET Core 1.1 이상에서 사용 가능) [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) 속성은 `context.Fail` 호출 될 때 처리기의 실행을 짧은 회로 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-255">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="36646-256">`InvokeHandlersAfterFailure` 기본값은 `true`이며이 경우 모든 처리기가 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36646-256">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="36646-257">인증이 실패 하는 경우에도 권한 부여 처리기가 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36646-257">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="36646-258">요구 사항에 대 한 여러 처리기가 필요한 이유는 무엇 인가요?</span><span class="sxs-lookup"><span data-stu-id="36646-258">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="36646-259">평가를 **또는** 기준으로 하려는 경우 단일 요구 사항에 대 한 여러 처리기를 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-259">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="36646-260">예를 들어 Microsoft에는 키 카드만 여는 문이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="36646-260">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="36646-261">키 카드를 집에서 벗어나면 접수원는 임시 스티커를 인쇄 하 고 도어를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="36646-261">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="36646-262">이 시나리오에서는 단일 요구 사항이 *BuildingEntry*하나는 단일 요구 사항을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-262">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="36646-263">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="36646-263">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="36646-264">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="36646-264">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="36646-265">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="36646-265">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="36646-266">두 처리기가 모두 [등록](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)되었는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-266">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="36646-267">정책이 `BuildingEntryRequirement`을 평가할 때 두 처리기가 모두 성공 하면 정책 평가가 성공 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-267">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="36646-268">Func를 사용 하 여 정책 수행</span><span class="sxs-lookup"><span data-stu-id="36646-268">Using a func to fulfill a policy</span></span>

<span data-ttu-id="36646-269">정책을 처리 하는 것이 간단 하 게 코드를 표현할 수 있는 경우가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="36646-269">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="36646-270">`RequireAssertion` 정책 작성기를 사용 하 여 정책을 구성할 때 `Func<AuthorizationHandlerContext, bool>`를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="36646-270">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="36646-271">예를 들어 이전 `BadgeEntryHandler`은 다음과 같이 다시 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="36646-271">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="36646-272">처리기의 MVC 요청 컨텍스트 액세스</span><span class="sxs-lookup"><span data-stu-id="36646-272">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="36646-273">권한 부여 처리기에서 구현 하는 `HandleRequirementAsync` 메서드에는 두 개의 매개 변수, 즉 `AuthorizationHandlerContext`와 처리 하는 `TRequirement`이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="36646-273">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="36646-274">MVC 또는 Jabbr와 같은 프레임 워크는 추가 정보를 전달 하기 위해 `AuthorizationHandlerContext`의 `Resource` 속성에 개체를 자유롭게 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="36646-274">Frameworks such as MVC or Jabbr are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="36646-275">예를 들어 MVC는 `Resource` 속성에서 [Authorizationfiltercontext](/dotnet/api/?term=AuthorizationFilterContext) 의 인스턴스를 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-275">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="36646-276">이 속성은 MVC 및 Razor Pages에서 제공 하는 `HttpContext`, `RouteData`및 기타 모든 항목에 대 한 액세스를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-276">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="36646-277">`Resource` 속성을 사용 하는 것은 프레임 워크에 특정 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-277">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="36646-278">`Resource` 속성의 정보를 사용 하면 권한 부여 정책이 특정 프레임 워크로 제한 됩니다.</span><span class="sxs-lookup"><span data-stu-id="36646-278">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="36646-279">`is` 키워드를 사용 하 여 `Resource` 속성을 캐스팅 한 다음, 다른 프레임 워크에서 실행 될 때 코드가 `InvalidCastException`와 충돌 하지 않도록 캐스팅이 성공 했는지 확인 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="36646-279">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end