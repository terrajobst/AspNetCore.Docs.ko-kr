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
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="236ec-103">ASP.NET Core의 정책 기반 권한 부여</span><span class="sxs-lookup"><span data-stu-id="236ec-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="236ec-104">내부적으로 [역할 기반 권한 부여](xref:security/authorization/roles) 및 [클레임 기반 권한 부여](xref:security/authorization/claims) 는 요구 사항, 요구 사항 처리기 및 미리 구성 된 정책을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="236ec-105">이런 빌딩 블록들은 권한 부여 평가를 코드로 표현할 수 있는 기능을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="236ec-106">결과적으로 보다 풍부하고 재사용 가능하며 테스트 가능한 권한 부여 구조를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="236ec-107">권한 부여 정책은 하나 이상의 요구 사항으로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="236ec-108">`Startup.ConfigureServices` 메서드에서 권한 부여 서비스 구성의 일부로 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="236ec-109">위의 예제는 "AtLeast21"이라는 정책을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="236ec-110">요구 사항에 대 한 매개 변수로 제공 되는 최소 age&mdash;단일 요구 사항이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="236ec-111">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="236ec-111">IAuthorizationService</span></span> 

<span data-ttu-id="236ec-112">권한 부여의 성공 여부를 결정 하는 기본 서비스는 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="236ec-113">위의 코드는 [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)의 두 메서드를 강조 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="236ec-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>는 메서드가 없고 권한 부여의 성공 여부를 추적 하는 메커니즘을 포함 하는 표식 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="236ec-115">각 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler>는 요구 사항이 충족 되었는지 확인 하는 일을 담당 합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="236ec-116"><xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> 클래스는 처리기에서 요구 사항이 충족 되었는지 여부를 표시 하는 데 사용 하는 내용입니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="236ec-117">다음 코드는 권한 부여 서비스의 간소화 된 (주석으로 주석이 추가 됨) 기본 구현을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="236ec-118">다음 코드에서는 일반적인 `ConfigureServices`를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-118">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="236ec-119">권한 부여를 위해 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> 또는 `[Authorize(Policy = "Something")]`를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="236ec-120">MVC 컨트롤러에 정책 적용</span><span class="sxs-lookup"><span data-stu-id="236ec-120">Applying policies to MVC controllers</span></span>

<span data-ttu-id="236ec-121">Razor Pages 사용 하는 경우이 문서의 [Razor Pages에 정책 적용](#applying-policies-to-razor-pages) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="236ec-121">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="236ec-122">정책은 정책 이름과 함께 `[Authorize]` 특성을 사용 하 여 컨트롤러에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="236ec-123">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="236ec-124">Razor Pages에 정책 적용</span><span class="sxs-lookup"><span data-stu-id="236ec-124">Applying policies to Razor Pages</span></span>

<span data-ttu-id="236ec-125">정책은 정책 이름과 함께 `[Authorize]` 특성을 사용 하 여 Razor Pages에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="236ec-126">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="236ec-127">[권한 부여 규칙](xref:security/authorization/razor-pages-authorization)을 사용 하 여 Razor Pages에 정책을 적용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-127">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="236ec-128">요구 사항</span><span class="sxs-lookup"><span data-stu-id="236ec-128">Requirements</span></span>

<span data-ttu-id="236ec-129">권한 부여 요구 사항은 정책에서 현재 사용자 보안 주체를 평가 하는 데 사용할 수 있는 데이터 매개 변수의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-129">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="236ec-130">"AtLeast21" 정책에서 요구 사항은 최소 기간&mdash;단일 매개 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-130">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="236ec-131">요구 사항은 빈 표식 인터페이스인 [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)을 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-131">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="236ec-132">매개 변수가 있는 최소 기간 요구 사항은 다음과 같이 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-132">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="236ec-133">권한 부여 정책에 여러 권한 부여 요구 사항이 포함 된 경우 정책 평가가 성공 하려면 모든 요구 사항이 통과 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-133">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="236ec-134">즉, 단일 권한 부여 정책에 추가 된 여러 권한 부여 요구 사항은 **및** 기준으로 처리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-134">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="236ec-135">요구 사항이 데이터나 속성을 가져야 할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-135">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="236ec-136">권한 부여 처리기</span><span class="sxs-lookup"><span data-stu-id="236ec-136">Authorization handlers</span></span>

<span data-ttu-id="236ec-137">권한 부여 처리기는 요구 사항의 속성을 평가하는 역할을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-137">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="236ec-138">권한 부여 처리기는 제공 된 [Authorizationhandlercontext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) 에 대 한 요구 사항을 평가 하 여 액세스가 허용 되는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-138">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="236ec-139">요구 사항에는 [여러 처리기](#security-authorization-policies-based-multiple-handlers)가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-139">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="236ec-140">처리기는 [>\<TRequirement](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)사항에 대 한 authorizationhandler를 상속할 수 있습니다. 여기서 `TRequirement`은 처리 해야 하는 요구 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-140">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="236ec-141">또는 처리기가 [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) 을 구현 하 여 둘 이상의 요구 사항을 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-141">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="236ec-142">한 가지 요구 사항에 대한 처리기 사용하기</span><span class="sxs-lookup"><span data-stu-id="236ec-142">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="236ec-143">다음은 최소 연령 처리기에서 단일 요구 사항을 사용하는 일대일 관계의 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-143">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="236ec-144">이 코드는 먼저 현재 사용자의 신원이 우리가 알고 있고 신뢰할 수 있는 발급자로부터 발급된 생년월일 클레임을 갖고 있는지부터 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-144">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="236ec-145">만약 클레임이 누락되었다면 권한을 부여할 수 없으므로 완료된 작업이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-145">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="236ec-146">반면 클레임이 존재할 경우, 사용자의 나이가 계산됩니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-146">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="236ec-147">그리고 나이가 요구 사항에 의해 정의된 최소 연령을 만족할 경우 권한 부여가 성공한 것으로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-147">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="236ec-148">권한 부여가 성공 하면 해당 하는 요구 사항을 유일한 매개 변수로 사용 하 여 `context.Succeed` 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-148">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="236ec-149">여러 요구 사항에 대한 처리기 사용하기</span><span class="sxs-lookup"><span data-stu-id="236ec-149">Use a handler for multiple requirements</span></span>

<span data-ttu-id="236ec-150">다음은 권한 처리기가 세 가지 종류의 요구 사항을 처리할 수 있는 일 대 다 관계의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-150">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="236ec-151">위의 코드는 성공으로 표시 되지 않은 요구 사항이 포함 된 속성&mdash;[Pendingrequirements 사항을](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) 트래버스 합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-151">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="236ec-152">`ReadPermission` 요구 사항에 대해 사용자는 요청 된 리소스에 액세스 하기 위한 소유자 또는 스폰서 여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-152">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="236ec-153">`EditPermission` 또는 `DeletePermission` 요구 사항의 경우 요청 된 리소스에 액세스 하려면 소유자 여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-153">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="236ec-154">처리기 등록하기</span><span class="sxs-lookup"><span data-stu-id="236ec-154">Handler registration</span></span>

<span data-ttu-id="236ec-155">처리기는 구성 과정 중 서비스 컬렉션에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-155">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="236ec-156">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-156">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="236ec-157">위의 코드는 `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`를 호출 하 여 `MinimumAgeHandler`를 singleton으로 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-157">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="236ec-158">기본 제공 [서비스 수명](xref:fundamentals/dependency-injection#service-lifetimes)중 하나를 사용 하 여 처리기를 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-158">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="236ec-159">처리기가 반환해야 하는 결과는?</span><span class="sxs-lookup"><span data-stu-id="236ec-159">What should a handler return?</span></span>

<span data-ttu-id="236ec-160">[처리기 예제의](#security-authorization-handler-example) `Handle` 메서드는 값을 반환 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-160">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="236ec-161">그렇다면 성공 혹은 실패 여부는 어떻게 확인할 수 있을까요?</span><span class="sxs-lookup"><span data-stu-id="236ec-161">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="236ec-162">처리기는 성공적으로 유효성을 검사 한 요구 사항을 전달 하 여 `context.Succeed(IAuthorizationRequirement requirement)`를 호출 하 여 성공을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-162">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="236ec-163">일반적으로 처리기는 실패를 처리할 필요가 없는데, 동일한 요구 사항에 대한 다른 처리기가 성공할 수도 있기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-163">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="236ec-164">오류를 보장 하기 위해 다른 요구 사항 처리기가 성공 하는 경우에도 `context.Fail`를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-164">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="236ec-165">처리기가 `context.Succeed` 또는 `context.Fail`를 호출 하는 경우 다른 모든 처리기가 계속 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-165">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="236ec-166">이렇게 하면 다른 처리기가 성공적으로 유효성을 검사 하거나 실패 한 경우에도 발생 하는 로깅 등의 부작용이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-166">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="236ec-167">`false`로 설정 된 경우 ASP.NET Core 1.1 이상에서 사용 가능) [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) 속성은 `context.Fail` 호출 될 때 처리기의 실행을 짧은 회로 합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-167">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="236ec-168">`InvokeHandlersAfterFailure` 기본값은 `true`이며이 경우 모든 처리기가 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-168">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="236ec-169">인증이 실패 하는 경우에도 권한 부여 처리기가 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-169">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="236ec-170">요구 사항에 대해 여러 개의 처기리가 필요한 이유는?</span><span class="sxs-lookup"><span data-stu-id="236ec-170">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="236ec-171">평가를 **또는** 기준으로 하려는 경우 단일 요구 사항에 대 한 여러 처리기를 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-171">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="236ec-172">예를 들어, Microsoft에는 키 카드로만 열 수 있는 문이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-172">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="236ec-173">만약 키 카드를 집에 두고 왔다면 접수원이 임시 스티커를 인쇄하고 대신 문을 열어줍니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-173">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="236ec-174">이 시나리오에서는 단일 요구 사항이 *BuildingEntry*하나는 단일 요구 사항을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-174">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="236ec-175">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="236ec-175">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="236ec-176">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="236ec-176">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="236ec-177">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="236ec-177">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="236ec-178">두 처리기가 모두 [등록](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)되었는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-178">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="236ec-179">정책이 `BuildingEntryRequirement`을 평가할 때 두 처리기가 모두 성공 하면 정책 평가가 성공 합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-179">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="236ec-180">func를 이용해서 정책 구성하기</span><span class="sxs-lookup"><span data-stu-id="236ec-180">Using a func to fulfill a policy</span></span>

<span data-ttu-id="236ec-181">코드로 정책을 표현해서 구성하는 편이 더 간단한 경우도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-181">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="236ec-182">`RequireAssertion` 정책 작성기를 사용 하 여 정책을 구성할 때 `Func<AuthorizationHandlerContext, bool>`를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-182">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="236ec-183">예를 들어 이전 `BadgeEntryHandler`은 다음과 같이 다시 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-183">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="236ec-184">처리기에서 MVC 요청 컨텍스트 접근하기</span><span class="sxs-lookup"><span data-stu-id="236ec-184">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="236ec-185">권한 부여 처리기에서 구현 하는 `HandleRequirementAsync` 메서드에는 두 개의 매개 변수, 즉 `AuthorizationHandlerContext`와 처리 하는 `TRequirement`이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-185">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="236ec-186">MVC 또는 Jabbr와 같은 프레임 워크는 추가 정보를 전달 하기 위해 `AuthorizationHandlerContext`의 `Resource` 속성에 개체를 자유롭게 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-186">Frameworks such as MVC or Jabbr are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="236ec-187">예를 들어 MVC는 `Resource` 속성에서 [Authorizationfiltercontext](/dotnet/api/?term=AuthorizationFilterContext) 의 인스턴스를 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-187">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="236ec-188">이 속성은 MVC 및 Razor Pages에서 제공 하는 `HttpContext`, `RouteData`및 기타 모든 항목에 대 한 액세스를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-188">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="236ec-189">`Resource` 속성을 사용 하는 것은 프레임 워크에 특정 합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-189">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="236ec-190">`Resource` 속성의 정보를 사용 하면 권한 부여 정책이 특정 프레임 워크로 제한 됩니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-190">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="236ec-191">`is` 키워드를 사용 하 여 `Resource` 속성을 캐스팅 한 다음, 다른 프레임 워크에서 실행 될 때 코드가 `InvalidCastException`와 충돌 하지 않도록 캐스팅이 성공 했는지 확인 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-191">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

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

<span data-ttu-id="236ec-192">내부적으로 [역할 기반 권한 부여](xref:security/authorization/roles) 및 [클레임 기반 권한 부여](xref:security/authorization/claims) 는 요구 사항, 요구 사항 처리기 및 미리 구성 된 정책을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-192">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="236ec-193">이런 빌딩 블록들은 권한 부여 평가를 코드로 표현할 수 있는 기능을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-193">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="236ec-194">결과적으로 보다 풍부하고 재사용 가능하며 테스트 가능한 권한 부여 구조를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-194">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="236ec-195">권한 부여 정책은 하나 이상의 요구 사항으로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-195">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="236ec-196">`Startup.ConfigureServices` 메서드에서 권한 부여 서비스 구성의 일부로 등록 됩니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-196">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="236ec-197">위의 예제는 "AtLeast21"이라는 정책을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-197">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="236ec-198">요구 사항에 대 한 매개 변수로 제공 되는 최소 age&mdash;단일 요구 사항이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-198">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="236ec-199">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="236ec-199">IAuthorizationService</span></span> 

<span data-ttu-id="236ec-200">권한 부여의 성공 여부를 결정 하는 기본 서비스는 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-200">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="236ec-201">위의 코드는 [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)의 두 메서드를 강조 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-201">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="236ec-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>는 메서드가 없고 권한 부여의 성공 여부를 추적 하는 메커니즘을 포함 하는 표식 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="236ec-203">각 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler>는 요구 사항이 충족 되었는지 확인 하는 일을 담당 합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-203">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="236ec-204"><xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> 클래스는 처리기에서 요구 사항이 충족 되었는지 여부를 표시 하는 데 사용 하는 내용입니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-204">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="236ec-205">다음 코드는 권한 부여 서비스의 간소화 된 (주석으로 주석이 추가 됨) 기본 구현을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-205">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="236ec-206">다음 코드에서는 일반적인 `ConfigureServices`를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-206">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="236ec-207">권한 부여를 위해 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> 또는 `[Authorize(Policy = "Something")]`를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-207">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="236ec-208">MVC 컨트롤러에 정책 적용</span><span class="sxs-lookup"><span data-stu-id="236ec-208">Applying policies to MVC controllers</span></span>

<span data-ttu-id="236ec-209">Razor Pages 사용 하는 경우이 문서의 [Razor Pages에 정책 적용](#applying-policies-to-razor-pages) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="236ec-209">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="236ec-210">정책은 정책 이름과 함께 `[Authorize]` 특성을 사용 하 여 컨트롤러에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-210">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="236ec-211">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-211">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="236ec-212">Razor Pages에 정책 적용</span><span class="sxs-lookup"><span data-stu-id="236ec-212">Applying policies to Razor Pages</span></span>

<span data-ttu-id="236ec-213">정책은 정책 이름과 함께 `[Authorize]` 특성을 사용 하 여 Razor Pages에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-213">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="236ec-214">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-214">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="236ec-215">[권한 부여 규칙](xref:security/authorization/razor-pages-authorization)을 사용 하 여 Razor Pages에 정책을 적용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-215">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="236ec-216">요구 사항</span><span class="sxs-lookup"><span data-stu-id="236ec-216">Requirements</span></span>

<span data-ttu-id="236ec-217">권한 부여 요구 사항은 정책에서 현재 사용자 보안 주체를 평가 하는 데 사용할 수 있는 데이터 매개 변수의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-217">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="236ec-218">"AtLeast21" 정책에서 요구 사항은 최소 기간&mdash;단일 매개 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-218">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="236ec-219">요구 사항은 빈 표식 인터페이스인 [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)을 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-219">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="236ec-220">매개 변수가 있는 최소 기간 요구 사항은 다음과 같이 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-220">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="236ec-221">권한 부여 정책에 여러 권한 부여 요구 사항이 포함 된 경우 정책 평가가 성공 하려면 모든 요구 사항이 통과 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-221">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="236ec-222">즉, 단일 권한 부여 정책에 추가 된 여러 권한 부여 요구 사항은 **및** 기준으로 처리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-222">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="236ec-223">요구 사항이 데이터나 속성을 가져야 할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-223">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="236ec-224">권한 부여 처리기</span><span class="sxs-lookup"><span data-stu-id="236ec-224">Authorization handlers</span></span>

<span data-ttu-id="236ec-225">권한 부여 처리기는 요구 사항의 속성을 평가하는 역할을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-225">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="236ec-226">권한 부여 처리기는 제공 된 [Authorizationhandlercontext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) 에 대 한 요구 사항을 평가 하 여 액세스가 허용 되는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-226">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="236ec-227">요구 사항에는 [여러 처리기](#security-authorization-policies-based-multiple-handlers)가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-227">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="236ec-228">처리기는 [>\<TRequirement](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)사항에 대 한 authorizationhandler를 상속할 수 있습니다. 여기서 `TRequirement`은 처리 해야 하는 요구 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-228">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="236ec-229">또는 처리기가 [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) 을 구현 하 여 둘 이상의 요구 사항을 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-229">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="236ec-230">한 가지 요구 사항에 대한 처리기 사용하기</span><span class="sxs-lookup"><span data-stu-id="236ec-230">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="236ec-231">다음은 최소 연령 처리기에서 단일 요구 사항을 사용하는 일대일 관계의 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-231">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="236ec-232">이 코드는 먼저 현재 사용자의 신원이 우리가 알고 있고 신뢰할 수 있는 발급자로부터 발급된 생년월일 클레임을 갖고 있는지부터 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-232">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="236ec-233">만약 클레임이 누락되었다면 권한을 부여할 수 없으므로 완료된 작업이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-233">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="236ec-234">반면 클레임이 존재할 경우, 사용자의 나이가 계산됩니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-234">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="236ec-235">그리고 나이가 요구 사항에 의해 정의된 최소 연령을 만족할 경우 권한 부여가 성공한 것으로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-235">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="236ec-236">권한 부여가 성공 하면 해당 하는 요구 사항을 유일한 매개 변수로 사용 하 여 `context.Succeed` 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-236">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="236ec-237">여러 요구 사항에 대한 처리기 사용하기</span><span class="sxs-lookup"><span data-stu-id="236ec-237">Use a handler for multiple requirements</span></span>

<span data-ttu-id="236ec-238">다음은 권한 처리기가 세 가지 종류의 요구 사항을 처리할 수 있는 일 대 다 관계의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-238">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="236ec-239">위의 코드는 성공으로 표시 되지 않은 요구 사항이 포함 된 속성&mdash;[Pendingrequirements 사항을](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) 트래버스 합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-239">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="236ec-240">`ReadPermission` 요구 사항에 대해 사용자는 요청 된 리소스에 액세스 하기 위한 소유자 또는 스폰서 여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-240">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="236ec-241">`EditPermission` 또는 `DeletePermission` 요구 사항의 경우 요청 된 리소스에 액세스 하려면 소유자 여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-241">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="236ec-242">처리기 등록하기</span><span class="sxs-lookup"><span data-stu-id="236ec-242">Handler registration</span></span>

<span data-ttu-id="236ec-243">처리기는 구성 과정 중 서비스 컬렉션에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-243">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="236ec-244">다음은 그 예입니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-244">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="236ec-245">위의 코드는 `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`를 호출 하 여 `MinimumAgeHandler`를 singleton으로 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-245">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="236ec-246">기본 제공 [서비스 수명](xref:fundamentals/dependency-injection#service-lifetimes)중 하나를 사용 하 여 처리기를 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-246">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="236ec-247">처리기가 반환해야 하는 결과는?</span><span class="sxs-lookup"><span data-stu-id="236ec-247">What should a handler return?</span></span>

<span data-ttu-id="236ec-248">[처리기 예제의](#security-authorization-handler-example) `Handle` 메서드는 값을 반환 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-248">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="236ec-249">그렇다면 성공 혹은 실패 여부는 어떻게 확인할 수 있을까요?</span><span class="sxs-lookup"><span data-stu-id="236ec-249">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="236ec-250">처리기는 성공적으로 유효성을 검사 한 요구 사항을 전달 하 여 `context.Succeed(IAuthorizationRequirement requirement)`를 호출 하 여 성공을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-250">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="236ec-251">일반적으로 처리기는 실패를 처리할 필요가 없는데, 동일한 요구 사항에 대한 다른 처리기가 성공할 수도 있기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-251">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="236ec-252">오류를 보장 하기 위해 다른 요구 사항 처리기가 성공 하는 경우에도 `context.Fail`를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-252">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="236ec-253">처리기가 `context.Succeed` 또는 `context.Fail`를 호출 하는 경우 다른 모든 처리기가 계속 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-253">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="236ec-254">이렇게 하면 다른 처리기가 성공적으로 유효성을 검사 하거나 실패 한 경우에도 발생 하는 로깅 등의 부작용이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-254">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="236ec-255">`false`로 설정 된 경우 ASP.NET Core 1.1 이상에서 사용 가능) [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) 속성은 `context.Fail` 호출 될 때 처리기의 실행을 짧은 회로 합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-255">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="236ec-256">`InvokeHandlersAfterFailure` 기본값은 `true`이며이 경우 모든 처리기가 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-256">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="236ec-257">인증이 실패 하는 경우에도 권한 부여 처리기가 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-257">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="236ec-258">요구 사항에 대해 여러 개의 처기리가 필요한 이유는?</span><span class="sxs-lookup"><span data-stu-id="236ec-258">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="236ec-259">평가를 **또는** 기준으로 하려는 경우 단일 요구 사항에 대 한 여러 처리기를 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-259">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="236ec-260">예를 들어, Microsoft에는 키 카드로만 열 수 있는 문이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-260">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="236ec-261">만약 키 카드를 집에 두고 왔다면 접수원이 임시 스티커를 인쇄하고 대신 문을 열어줍니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-261">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="236ec-262">이 시나리오에서는 단일 요구 사항이 *BuildingEntry*하나는 단일 요구 사항을 검사 합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-262">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="236ec-263">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="236ec-263">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="236ec-264">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="236ec-264">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="236ec-265">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="236ec-265">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="236ec-266">두 처리기가 모두 [등록](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)되었는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-266">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="236ec-267">정책이 `BuildingEntryRequirement`을 평가할 때 두 처리기가 모두 성공 하면 정책 평가가 성공 합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-267">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="236ec-268">func를 이용해서 정책 구성하기</span><span class="sxs-lookup"><span data-stu-id="236ec-268">Using a func to fulfill a policy</span></span>

<span data-ttu-id="236ec-269">코드로 정책을 표현해서 구성하는 편이 더 간단한 경우도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-269">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="236ec-270">`RequireAssertion` 정책 작성기를 사용 하 여 정책을 구성할 때 `Func<AuthorizationHandlerContext, bool>`를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-270">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="236ec-271">예를 들어 이전 `BadgeEntryHandler`은 다음과 같이 다시 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-271">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="236ec-272">처리기에서 MVC 요청 컨텍스트 접근하기</span><span class="sxs-lookup"><span data-stu-id="236ec-272">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="236ec-273">권한 부여 처리기에서 구현 하는 `HandleRequirementAsync` 메서드에는 두 개의 매개 변수, 즉 `AuthorizationHandlerContext`와 처리 하는 `TRequirement`이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-273">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="236ec-274">MVC 또는 Jabbr와 같은 프레임 워크는 추가 정보를 전달 하기 위해 `AuthorizationHandlerContext`의 `Resource` 속성에 개체를 자유롭게 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-274">Frameworks such as MVC or Jabbr are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="236ec-275">예를 들어 MVC는 `Resource` 속성에서 [Authorizationfiltercontext](/dotnet/api/?term=AuthorizationFilterContext) 의 인스턴스를 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-275">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="236ec-276">이 속성은 MVC 및 Razor Pages에서 제공 하는 `HttpContext`, `RouteData`및 기타 모든 항목에 대 한 액세스를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-276">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="236ec-277">`Resource` 속성을 사용 하는 것은 프레임 워크에 특정 합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-277">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="236ec-278">`Resource` 속성의 정보를 사용 하면 권한 부여 정책이 특정 프레임 워크로 제한 됩니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-278">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="236ec-279">`is` 키워드를 사용 하 여 `Resource` 속성을 캐스팅 한 다음, 다른 프레임 워크에서 실행 될 때 코드가 `InvalidCastException`와 충돌 하지 않도록 캐스팅이 성공 했는지 확인 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="236ec-279">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end