---
title: ASP.NET Core의 정책 기반 권한 부여
author: rick-anderson
description: ASP.NET Core 응용 프로그램에서 사용자 지정 권한 정책 처리기를 작성 및 사용해서 권한 부여 요구 사항을 적용하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 10/05/2019
uid: security/authorization/policies
ms.openlocfilehash: eeb5ddd63ef8177325b35e5a666aa5e9ab047057
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828960"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="98e66-103">ASP.NET Core의 정책 기반 권한 부여</span><span class="sxs-lookup"><span data-stu-id="98e66-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="98e66-104">[역할 기반 권한 부여](xref:security/authorization/roles) 및 [클레임 기반 권한 부여](xref:security/authorization/claims) 는 내부적으로 요구 사항, 요구 사항 처리기, 그리고 미리 구성된 정책을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="98e66-105">이런 빌딩 블록들은 권한 부여 평가를 코드로 표현할 수 있는 기능을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="98e66-106">결과적으로 보다 풍부하고 재사용 가능하며 테스트 가능한 권한 부여 구조를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="98e66-107">권한 부여 정책은 하나 이상의 요구 사항으로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="98e66-108">그리고 `Startup.ConfigureServices`에서 권한 부여 서비스 구성의 일부로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="98e66-109">위의 예제는 "AtLeast21"이라는 정책을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="98e66-110">이 정책은 요구 사항의 &mdash; 매개 변수로 제공되는, 최소 연령을 뜻하는 단일 요구 사항을 갖습니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="98e66-111">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="98e66-111">IAuthorizationService</span></span> 

<span data-ttu-id="98e66-112">권한 부여의 성공 여부를 결정 하는 기본 서비스는 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="98e66-113">위의 코드는 [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)의 두 메서드를 강조 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="98e66-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>는 메서드가 없고 권한 부여의 성공 여부를 추적 하는 메커니즘을 포함 하는 표식 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="98e66-115">각 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler>는 요구 사항이 충족 되었는지 확인 하는 일을 담당 합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="98e66-116"><xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> 클래스는 처리기에서 요구 사항이 충족 되었는지 여부를 표시 하는 데 사용 하는 내용입니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="98e66-117">다음 코드는 권한 부여 서비스의 간소화 된 (주석으로 주석이 추가 됨) 기본 구현을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="98e66-118">다음 코드에서는 일반적인 `ConfigureServices`를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-118">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="98e66-119">권한 부여를 위해 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> 또는 `[Authorize(Policy = "Something")]`를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="98e66-120">MVC 컨트롤러에 정책 적용</span><span class="sxs-lookup"><span data-stu-id="98e66-120">Applying policies to MVC controllers</span></span>

<span data-ttu-id="98e66-121">Razor Pages 사용 하는 경우이 문서의 [Razor Pages에 정책 적용](#applying-policies-to-razor-pages) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="98e66-121">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="98e66-122">정책은 정책 이름과 함께 `[Authorize]` 특성을 사용 하 여 컨트롤러에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="98e66-123">예를 들면 다음과 같습니다.:</span><span class="sxs-lookup"><span data-stu-id="98e66-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="98e66-124">Razor Pages에 정책 적용</span><span class="sxs-lookup"><span data-stu-id="98e66-124">Applying policies to Razor Pages</span></span>

<span data-ttu-id="98e66-125">정책은 정책 이름과 함께 `[Authorize]` 특성을 사용 하 여 Razor Pages에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="98e66-126">예를 들면 다음과 같습니다.:</span><span class="sxs-lookup"><span data-stu-id="98e66-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="98e66-127">[권한 부여 규칙](xref:security/authorization/razor-pages-authorization)을 사용 하 여 Razor Pages에 정책을 적용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-127">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="98e66-128">요구 사항</span><span class="sxs-lookup"><span data-stu-id="98e66-128">Requirements</span></span>

<span data-ttu-id="98e66-129">권한 부여 요구 사항은 정책에서 현재 사용자 보안 주체를 평가 하는 데 사용할 수 있는 데이터 매개 변수의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-129">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="98e66-130">"AtLeast21" 정책에서 요구 사항은 최소 기간&mdash;단일 매개 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-130">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="98e66-131">요구 사항은 빈 표식 인터페이스인 [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)을 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-131">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="98e66-132">매개 변수가 있는 최소 기간 요구 사항은 다음과 같이 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-132">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="98e66-133">권한 부여 정책에 여러 권한 부여 요구 사항이 포함 된 경우 정책 평가가 성공 하려면 모든 요구 사항이 통과 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-133">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="98e66-134">즉, 단일 권한 부여 정책에 추가 된 여러 권한 부여 요구 사항은 **및** 기준으로 처리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-134">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="98e66-135">요구 사항이 데이터나 속성을 가져야 할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-135">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="98e66-136">권한 부여 처리기</span><span class="sxs-lookup"><span data-stu-id="98e66-136">Authorization handlers</span></span>

<span data-ttu-id="98e66-137">권한 부여 처리기는 요구 사항의 속성을 평가하는 역할을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-137">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="98e66-138">권한 부여 처리기는 제공된 [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext)에 대해 요구 사항을 평가하여 접근 허용 여부를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-138">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="98e66-139">하나의 요구 사항에 [여러 개의 처리기](#security-authorization-policies-based-multiple-handlers) 가 존재할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-139">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="98e66-140">처리기는 [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)를 상속받으며, 여기서 `TRequirement` 는 처리해야 할 요구 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-140">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="98e66-141">또는 처리기가 [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) 을 구현 하 여 둘 이상의 요구 사항을 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-141">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="98e66-142">한 가지 요구 사항에 대한 처리기 사용하기</span><span class="sxs-lookup"><span data-stu-id="98e66-142">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="98e66-143">다음은 최소 연령 처리기에서 단일 요구 사항을 사용하는 일대일 관계의 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-143">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="98e66-144">이 코드는 먼저 현재 사용자의 신원이 우리가 알고 있고 신뢰할 수 있는 발급자로부터 발급된 생년월일 클레임을 갖고 있는지부터 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-144">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="98e66-145">만약 클레임이 누락되었다면 권한을 부여할 수 없으므로 완료된 작업이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-145">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="98e66-146">반면 클레임이 존재할 경우, 사용자의 나이가 계산됩니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-146">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="98e66-147">그리고 나이가 요구 사항에 의해 정의된 최소 연령을 만족할 경우 권한 부여가 성공한 것으로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-147">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="98e66-148">권한 부여에 성공하면 만족한 요구 사항을 유일한 매개 변수로 전달하여 `context.Succeed`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-148">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="98e66-149">여러 요구 사항에 대한 처리기 사용하기</span><span class="sxs-lookup"><span data-stu-id="98e66-149">Use a handler for multiple requirements</span></span>

<span data-ttu-id="98e66-150">다음은 권한 처리기가 세 가지 종류의 요구 사항을 처리할 수 있는 일 대 다 관계의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-150">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="98e66-151">위의 코드는 성공으로 표시 되지 않은 요구 사항이 포함 된 속성&mdash;[Pendingrequirements 사항을](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) 트래버스 합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-151">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="98e66-152">`ReadPermission` 요구 사항에 대해 사용자는 요청 된 리소스에 액세스 하기 위한 소유자 또는 스폰서 여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-152">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="98e66-153">`EditPermission` 또는 `DeletePermission` 요구 사항의 경우 요청 된 리소스에 액세스 하려면 소유자 여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-153">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="98e66-154">처리기 등록하기</span><span class="sxs-lookup"><span data-stu-id="98e66-154">Handler registration</span></span>

<span data-ttu-id="98e66-155">처리기는 구성 과정 중 서비스 컬렉션에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-155">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="98e66-156">예를 들면 다음과 같습니다.:</span><span class="sxs-lookup"><span data-stu-id="98e66-156">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="98e66-157">위의 코드는 `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`를 호출 하 여 `MinimumAgeHandler`를 singleton으로 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-157">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="98e66-158">기본 제공 [서비스 수명](xref:fundamentals/dependency-injection#service-lifetimes)중 하나를 사용 하 여 처리기를 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-158">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="98e66-159">처리기가 반환해야 하는 결과는?</span><span class="sxs-lookup"><span data-stu-id="98e66-159">What should a handler return?</span></span>

<span data-ttu-id="98e66-160">본문의 [처리기 예제](#security-authorization-handler-example)에서 `Handle` 메서드가 아무런 값도 반환하지 않는다는 점에 주목하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-160">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="98e66-161">그렇다면 성공 혹은 실패 여부는 어떻게 확인할 수 있을까요?</span><span class="sxs-lookup"><span data-stu-id="98e66-161">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="98e66-162">처리기는 성공적으로 검증된 요구 사항을 `context.Succeed(IAuthorizationRequirement requirement)`에 매개 변수로 전달하여 호출함으로써 성공했음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-162">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="98e66-163">일반적으로 처리기는 실패를 처리할 필요가 없는데, 동일한 요구 사항에 대한 다른 처리기가 성공할 수도 있기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-163">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="98e66-164">다른 처리기의 성공 여부와 관계없이 무조건 실패한 것으로 나타내려면 `context.Fail`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-164">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="98e66-165">처리기가 `context.Succeed` 또는 `context.Fail`를 호출 하는 경우 다른 모든 처리기가 계속 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-165">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="98e66-166">이렇게 하면 다른 처리기가 성공적으로 유효성을 검사 하거나 실패 한 경우에도 발생 하는 로깅 등의 부작용이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-166">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="98e66-167">[InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) 속성이 (ASP.NET Core 1.1 이상에서 사용 가능) `false`로 설정되면 `context.Fail`이 호출될 경우 나머지 처리기들의 실행이 중단되고 즉시 빠져나갑니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-167">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="98e66-168">`InvokeHandlersAfterFailure`의 기본값은 `true`로, 이 경우 모든 처리기가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-168">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="98e66-169">인증이 실패 하는 경우에도 권한 부여 처리기가 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-169">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="98e66-170">요구 사항에 대해 여러 개의 처기리가 필요한 이유는?</span><span class="sxs-lookup"><span data-stu-id="98e66-170">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="98e66-171">만약 **OR** 기반의 평가를 수행하고 싶다면 단일 요구 사항에 대해 여러 개의 처리기를 구현해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-171">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="98e66-172">예를 들어, Microsoft에는 키 카드로만 열 수 있는 문이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-172">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="98e66-173">만약 키 카드를 집에 두고 왔다면 접수원이 임시 스티커를 인쇄하고 대신 문을 열어줍니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-173">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="98e66-174">이 시나리오의 경우, 요구 사항은 *BuildingEntry* 하나지만 여러 처리기가 단일 요구 사항을 각각 개별적으로 검토하게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-174">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="98e66-175">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="98e66-175">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="98e66-176">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="98e66-176">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="98e66-177">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="98e66-177">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="98e66-178">두 처리기가 모두 [등록되어 있는지](xref:security/authorization/policies#security-authorization-policies-based-handler-registration) 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-178">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="98e66-179">정책이 `BuildingEntryRequirement` 를 평가할 때, 두 처리기 중 하나라도 성공하면 정책 평가가 성공한 것으로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-179">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="98e66-180">func를 이용해서 정책 구성하기</span><span class="sxs-lookup"><span data-stu-id="98e66-180">Using a func to fulfill a policy</span></span>

<span data-ttu-id="98e66-181">코드로 정책을 표현해서 구성하는 편이 더 간단한 경우도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-181">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="98e66-182">정책을 구성할 때 `RequireAssertion` 정책 빌더에 `Func<AuthorizationHandlerContext, bool>`을 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-182">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="98e66-183">예를 들어 이전 `BadgeEntryHandler`를 다음과 같이 다시 작성할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-183">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="98e66-184">처리기에서 MVC 요청 컨텍스트 접근하기</span><span class="sxs-lookup"><span data-stu-id="98e66-184">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="98e66-185">권한 부여 처리기에서 구현해야 하는 `HandleRequirementAsync` 메서드에는 `AuthorizationHandlerContext`와 처리해야 할 대상인 `TRequirement`라는 두 개의 매개 변수가 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-185">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="98e66-186">MVC나 Jabbr 같은 프레임워크는 `AuthorizationHandlerContext`의 `Resource` 속성에 자유롭게 개체를 추가해서 추가적인 정보를 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-186">Frameworks such as MVC or Jabbr are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="98e66-187">예를 들어, MVC는 `Resource` 속성에 [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext)의 인스턴스를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-187">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="98e66-188">이 속성은 `HttpContext`나 `RouteData`를 비롯한, MVC 및 Razor 페이지가 제공하는 다양한 정보들에 대한 접근을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-188">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="98e66-189">`Resource` 속성을 사용하는 방식은 프레임워크에 따라서 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-189">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="98e66-190">따라서 `Resource` 속성의 정보를 사용할 경우 권한 부여 정책이 특정 프레임워크를 대상으로 제한될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-190">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="98e66-191">`is` 키워드를 사용 하 여 `Resource` 속성을 캐스팅 한 다음, 다른 프레임 워크에서 실행 될 때 코드가 `InvalidCastException`와 충돌 하지 않도록 캐스팅이 성공 했는지 확인 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-191">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

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

<span data-ttu-id="98e66-192">[역할 기반 권한 부여](xref:security/authorization/roles) 및 [클레임 기반 권한 부여](xref:security/authorization/claims) 는 내부적으로 요구 사항, 요구 사항 처리기, 그리고 미리 구성된 정책을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-192">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="98e66-193">이런 빌딩 블록들은 권한 부여 평가를 코드로 표현할 수 있는 기능을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-193">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="98e66-194">결과적으로 보다 풍부하고 재사용 가능하며 테스트 가능한 권한 부여 구조를 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-194">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="98e66-195">권한 부여 정책은 하나 이상의 요구 사항으로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-195">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="98e66-196">그리고 `Startup.ConfigureServices`에서 권한 부여 서비스 구성의 일부로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-196">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="98e66-197">위의 예제는 "AtLeast21"이라는 정책을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-197">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="98e66-198">이 정책은 요구 사항의 &mdash; 매개 변수로 제공되는, 최소 연령을 뜻하는 단일 요구 사항을 갖습니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-198">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="98e66-199">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="98e66-199">IAuthorizationService</span></span> 

<span data-ttu-id="98e66-200">권한 부여의 성공 여부를 결정 하는 기본 서비스는 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-200">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="98e66-201">위의 코드는 [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)의 두 메서드를 강조 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-201">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="98e66-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>는 메서드가 없고 권한 부여의 성공 여부를 추적 하는 메커니즘을 포함 하는 표식 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="98e66-203">각 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler>는 요구 사항이 충족 되었는지 확인 하는 일을 담당 합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-203">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="98e66-204"><xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> 클래스는 처리기에서 요구 사항이 충족 되었는지 여부를 표시 하는 데 사용 하는 내용입니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-204">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="98e66-205">다음 코드는 권한 부여 서비스의 간소화 된 (주석으로 주석이 추가 됨) 기본 구현을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-205">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="98e66-206">다음 코드에서는 일반적인 `ConfigureServices`를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-206">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="98e66-207">권한 부여를 위해 <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> 또는 `[Authorize(Policy = "Something")]`를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-207">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="98e66-208">MVC 컨트롤러에 정책 적용</span><span class="sxs-lookup"><span data-stu-id="98e66-208">Applying policies to MVC controllers</span></span>

<span data-ttu-id="98e66-209">Razor Pages 사용 하는 경우이 문서의 [Razor Pages에 정책 적용](#applying-policies-to-razor-pages) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="98e66-209">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="98e66-210">정책은 정책 이름과 함께 `[Authorize]` 특성을 사용 하 여 컨트롤러에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-210">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="98e66-211">예를 들면 다음과 같습니다.:</span><span class="sxs-lookup"><span data-stu-id="98e66-211">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="98e66-212">Razor Pages에 정책 적용</span><span class="sxs-lookup"><span data-stu-id="98e66-212">Applying policies to Razor Pages</span></span>

<span data-ttu-id="98e66-213">정책은 정책 이름과 함께 `[Authorize]` 특성을 사용 하 여 Razor Pages에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-213">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="98e66-214">예를 들면 다음과 같습니다.:</span><span class="sxs-lookup"><span data-stu-id="98e66-214">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="98e66-215">[권한 부여 규칙](xref:security/authorization/razor-pages-authorization)을 사용 하 여 Razor Pages에 정책을 적용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-215">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="98e66-216">요구 사항</span><span class="sxs-lookup"><span data-stu-id="98e66-216">Requirements</span></span>

<span data-ttu-id="98e66-217">권한 부여 요구 사항은 정책에서 현재 사용자 보안 주체를 평가 하는 데 사용할 수 있는 데이터 매개 변수의 컬렉션입니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-217">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="98e66-218">"AtLeast21" 정책에서 요구 사항은 최소 기간&mdash;단일 매개 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-218">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="98e66-219">요구 사항은 빈 표식 인터페이스인 [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)을 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-219">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="98e66-220">매개 변수가 있는 최소 기간 요구 사항은 다음과 같이 구현할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-220">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="98e66-221">권한 부여 정책에 여러 권한 부여 요구 사항이 포함 된 경우 정책 평가가 성공 하려면 모든 요구 사항이 통과 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-221">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="98e66-222">즉, 단일 권한 부여 정책에 추가 된 여러 권한 부여 요구 사항은 **및** 기준으로 처리 됩니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-222">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="98e66-223">요구 사항이 데이터나 속성을 가져야 할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-223">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="98e66-224">권한 부여 처리기</span><span class="sxs-lookup"><span data-stu-id="98e66-224">Authorization handlers</span></span>

<span data-ttu-id="98e66-225">권한 부여 처리기는 요구 사항의 속성을 평가하는 역할을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-225">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="98e66-226">권한 부여 처리기는 제공된 [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext)에 대해 요구 사항을 평가하여 접근 허용 여부를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-226">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="98e66-227">하나의 요구 사항에 [여러 개의 처리기](#security-authorization-policies-based-multiple-handlers) 가 존재할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-227">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="98e66-228">처리기는 [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)를 상속받으며, 여기서 `TRequirement` 는 처리해야 할 요구 사항입니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-228">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="98e66-229">또는 처리기가 [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) 을 구현 하 여 둘 이상의 요구 사항을 처리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-229">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="98e66-230">한 가지 요구 사항에 대한 처리기 사용하기</span><span class="sxs-lookup"><span data-stu-id="98e66-230">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="98e66-231">다음은 최소 연령 처리기에서 단일 요구 사항을 사용하는 일대일 관계의 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-231">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="98e66-232">이 코드는 먼저 현재 사용자의 신원이 우리가 알고 있고 신뢰할 수 있는 발급자로부터 발급된 생년월일 클레임을 갖고 있는지부터 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-232">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="98e66-233">만약 클레임이 누락되었다면 권한을 부여할 수 없으므로 완료된 작업이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-233">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="98e66-234">반면 클레임이 존재할 경우, 사용자의 나이가 계산됩니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-234">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="98e66-235">그리고 나이가 요구 사항에 의해 정의된 최소 연령을 만족할 경우 권한 부여가 성공한 것으로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-235">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="98e66-236">권한 부여에 성공하면 만족한 요구 사항을 유일한 매개 변수로 전달하여 `context.Succeed`를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-236">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="98e66-237">여러 요구 사항에 대한 처리기 사용하기</span><span class="sxs-lookup"><span data-stu-id="98e66-237">Use a handler for multiple requirements</span></span>

<span data-ttu-id="98e66-238">다음은 권한 처리기가 세 가지 종류의 요구 사항을 처리할 수 있는 일 대 다 관계의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-238">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="98e66-239">위의 코드는 성공으로 표시 되지 않은 요구 사항이 포함 된 속성&mdash;[Pendingrequirements 사항을](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) 트래버스 합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-239">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="98e66-240">`ReadPermission` 요구 사항에 대해 사용자는 요청 된 리소스에 액세스 하기 위한 소유자 또는 스폰서 여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-240">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="98e66-241">`EditPermission` 또는 `DeletePermission` 요구 사항의 경우 요청 된 리소스에 액세스 하려면 소유자 여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-241">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="98e66-242">처리기 등록하기</span><span class="sxs-lookup"><span data-stu-id="98e66-242">Handler registration</span></span>

<span data-ttu-id="98e66-243">처리기는 구성 과정 중 서비스 컬렉션에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-243">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="98e66-244">예를 들면 다음과 같습니다.:</span><span class="sxs-lookup"><span data-stu-id="98e66-244">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="98e66-245">위의 코드는 `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`를 호출 하 여 `MinimumAgeHandler`를 singleton으로 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-245">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="98e66-246">기본 제공 [서비스 수명](xref:fundamentals/dependency-injection#service-lifetimes)중 하나를 사용 하 여 처리기를 등록할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-246">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="98e66-247">처리기가 반환해야 하는 결과는?</span><span class="sxs-lookup"><span data-stu-id="98e66-247">What should a handler return?</span></span>

<span data-ttu-id="98e66-248">본문의 [처리기 예제](#security-authorization-handler-example)에서 `Handle` 메서드가 아무런 값도 반환하지 않는다는 점에 주목하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-248">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="98e66-249">그렇다면 성공 혹은 실패 여부는 어떻게 확인할 수 있을까요?</span><span class="sxs-lookup"><span data-stu-id="98e66-249">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="98e66-250">처리기는 성공적으로 검증된 요구 사항을 `context.Succeed(IAuthorizationRequirement requirement)`에 매개 변수로 전달하여 호출함으로써 성공했음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-250">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="98e66-251">일반적으로 처리기는 실패를 처리할 필요가 없는데, 동일한 요구 사항에 대한 다른 처리기가 성공할 수도 있기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-251">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="98e66-252">다른 처리기의 성공 여부와 관계없이 무조건 실패한 것으로 나타내려면 `context.Fail`을 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-252">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="98e66-253">처리기가 `context.Succeed` 또는 `context.Fail`를 호출 하는 경우 다른 모든 처리기가 계속 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-253">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="98e66-254">이렇게 하면 다른 처리기가 성공적으로 유효성을 검사 하거나 실패 한 경우에도 발생 하는 로깅 등의 부작용이 발생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-254">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="98e66-255">[InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) 속성이 (ASP.NET Core 1.1 이상에서 사용 가능) `false`로 설정되면 `context.Fail`이 호출될 경우 나머지 처리기들의 실행이 중단되고 즉시 빠져나갑니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-255">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="98e66-256">`InvokeHandlersAfterFailure`의 기본값은 `true`로, 이 경우 모든 처리기가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-256">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="98e66-257">인증이 실패 하는 경우에도 권한 부여 처리기가 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-257">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="98e66-258">요구 사항에 대해 여러 개의 처기리가 필요한 이유는?</span><span class="sxs-lookup"><span data-stu-id="98e66-258">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="98e66-259">만약 **OR** 기반의 평가를 수행하고 싶다면 단일 요구 사항에 대해 여러 개의 처리기를 구현해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-259">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="98e66-260">예를 들어, Microsoft에는 키 카드로만 열 수 있는 문이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-260">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="98e66-261">만약 키 카드를 집에 두고 왔다면 접수원이 임시 스티커를 인쇄하고 대신 문을 열어줍니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-261">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="98e66-262">이 시나리오의 경우, 요구 사항은 *BuildingEntry* 하나지만 여러 처리기가 단일 요구 사항을 각각 개별적으로 검토하게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-262">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="98e66-263">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="98e66-263">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="98e66-264">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="98e66-264">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="98e66-265">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="98e66-265">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="98e66-266">두 처리기가 모두 [등록되어 있는지](xref:security/authorization/policies#security-authorization-policies-based-handler-registration) 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-266">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="98e66-267">정책이 `BuildingEntryRequirement` 를 평가할 때, 두 처리기 중 하나라도 성공하면 정책 평가가 성공한 것으로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-267">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="98e66-268">func를 이용해서 정책 구성하기</span><span class="sxs-lookup"><span data-stu-id="98e66-268">Using a func to fulfill a policy</span></span>

<span data-ttu-id="98e66-269">코드로 정책을 표현해서 구성하는 편이 더 간단한 경우도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-269">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="98e66-270">정책을 구성할 때 `RequireAssertion` 정책 빌더에 `Func<AuthorizationHandlerContext, bool>`을 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-270">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="98e66-271">예를 들어 이전 `BadgeEntryHandler`를 다음과 같이 다시 작성할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-271">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="98e66-272">처리기에서 MVC 요청 컨텍스트 접근하기</span><span class="sxs-lookup"><span data-stu-id="98e66-272">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="98e66-273">권한 부여 처리기에서 구현해야 하는 `HandleRequirementAsync` 메서드에는 `AuthorizationHandlerContext`와 처리해야 할 대상인 `TRequirement`라는 두 개의 매개 변수가 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-273">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="98e66-274">MVC나 Jabbr 같은 프레임워크는 `AuthorizationHandlerContext`의 `Resource` 속성에 자유롭게 개체를 추가해서 추가적인 정보를 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-274">Frameworks such as MVC or Jabbr are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="98e66-275">예를 들어, MVC는 `Resource` 속성에 [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext)의 인스턴스를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-275">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="98e66-276">이 속성은 `HttpContext`나 `RouteData`를 비롯한, MVC 및 Razor 페이지가 제공하는 다양한 정보들에 대한 접근을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-276">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="98e66-277">`Resource` 속성을 사용하는 방식은 프레임워크에 따라서 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-277">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="98e66-278">따라서 `Resource` 속성의 정보를 사용할 경우 권한 부여 정책이 특정 프레임워크를 대상으로 제한될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-278">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="98e66-279">`is` 키워드를 사용 하 여 `Resource` 속성을 캐스팅 한 다음, 다른 프레임 워크에서 실행 될 때 코드가 `InvalidCastException`와 충돌 하지 않도록 캐스팅이 성공 했는지 확인 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="98e66-279">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end