---
title: ASP.NET Core의 사용자 지정 권한 부여 정책 공급자
author: mjrousos
description: ASP.NET Core 앱에서 사용자 지정 IAuthorizationPolicyProvider를 사용 하 여 권한 부여 정책을 동적으로 생성 하는 방법에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: b11f7f94e1042e65d5f2ff8ab0fe9ea7838bebeb
ms.sourcegitcommit: b1e480e1736b0fe0e4d8dce4a4cf5c8e47fc2101
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71108056"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a><span data-ttu-id="8345a-103">ASP.NET Core에서 IAuthorizationPolicyProvider를 사용 하는 사용자 지정 권한 부여 정책 공급자</span><span class="sxs-lookup"><span data-stu-id="8345a-103">Custom Authorization Policy Providers using IAuthorizationPolicyProvider in ASP.NET Core</span></span> 

<span data-ttu-id="8345a-104">[Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="8345a-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="8345a-105">일반적으로 [정책 기반 권한 부여](xref:security/authorization/policies)를 사용 하는 경우 권한 부여 `AuthorizationOptions.AddPolicy` 서비스 구성의 일부로를 호출 하 여 정책을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-105">Typically when using [policy-based authorization](xref:security/authorization/policies), policies are registered by calling `AuthorizationOptions.AddPolicy` as part of authorization service configuration.</span></span> <span data-ttu-id="8345a-106">일부 시나리오에서는 이러한 방식으로 모든 권한 부여 정책을 등록 하는 것이 가능 하지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-106">In some scenarios, it may not be possible (or desirable) to register all authorization policies in this way.</span></span> <span data-ttu-id="8345a-107">이러한 경우 사용자 지정 `IAuthorizationPolicyProvider` 을 사용 하 여 권한 부여 정책이 제공 되는 방식을 제어할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-107">In those cases, you can use a custom `IAuthorizationPolicyProvider` to control how authorization policies are supplied.</span></span>

<span data-ttu-id="8345a-108">사용자 지정 [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) 유용 하 게 사용할 수 있는 시나리오의 예는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-108">Examples of scenarios where a custom [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) may be useful include:</span></span>

* <span data-ttu-id="8345a-109">외부 서비스를 사용 하 여 정책 평가를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-109">Using an external service to provide policy evaluation.</span></span>
* <span data-ttu-id="8345a-110">많은 범위의 정책 (예: 방 번호 또는 연령)을 사용 하 여 각 개별 권한 부여 정책을 `AuthorizationOptions.AddPolicy` 호출에 추가 하는 것은 의미가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-110">Using a large range of policies (for different room numbers or ages, for example), so it doesn’t make sense to add each individual authorization policy with an `AuthorizationOptions.AddPolicy` call.</span></span>
* <span data-ttu-id="8345a-111">외부 데이터 원본 (예: 데이터베이스)의 정보를 기반으로 런타임에 정책을 만들거나 다른 메커니즘을 통해 권한 부여 요구 사항을 동적으로 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-111">Creating policies at runtime based on information in an external data source (like a database) or determining authorization requirements dynamically through another mechanism.</span></span>

<span data-ttu-id="8345a-112">[AspNetCore GitHub 리포지토리에서](https://github.com/aspnet/AspNetCore) [샘플 코드를 보거나 다운로드](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) 합니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-112">[View or download sample code](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) from the [AspNetCore GitHub repository](https://github.com/aspnet/AspNetCore).</span></span> <span data-ttu-id="8345a-113">Aspnet/AspNetCore 리포지토리 ZIP 파일을 다운로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-113">Download the aspnet/AspNetCore repository ZIP file.</span></span> <span data-ttu-id="8345a-114">파일의 압축을 풉니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-114">Unzip the file.</span></span> <span data-ttu-id="8345a-115">*Src/Security/samples/CustomPolicyProvider* 프로젝트 폴더로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-115">Navigate to the *src/Security/samples/CustomPolicyProvider* project folder.</span></span>

## <a name="customize-policy-retrieval"></a><span data-ttu-id="8345a-116">정책 검색 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="8345a-116">Customize policy retrieval</span></span>

<span data-ttu-id="8345a-117">ASP.NET Core apps는 `IAuthorizationPolicyProvider` 인터페이스 구현을 사용 하 여 권한 부여 정책을 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-117">ASP.NET Core apps use an implementation of the `IAuthorizationPolicyProvider` interface to retrieve authorization policies.</span></span> <span data-ttu-id="8345a-118">기본적으로 [Defaultauthorizationpolicyprovider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) 가 등록 되 고 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-118">By default, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) is registered and used.</span></span> <span data-ttu-id="8345a-119">`DefaultAuthorizationPolicyProvider`호출에서 제공 된 `AuthorizationOptions` 정책을 반환 합니다. `IServiceCollection.AddAuthorization`</span><span class="sxs-lookup"><span data-stu-id="8345a-119">`DefaultAuthorizationPolicyProvider` returns policies from the `AuthorizationOptions` provided in an `IServiceCollection.AddAuthorization` call.</span></span>

<span data-ttu-id="8345a-120">앱의 `IAuthorizationPolicyProvider` [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너에 다른 구현을 등록 하 여이 동작을 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-120">You can customize this behavior by registering a different `IAuthorizationPolicyProvider` implementation in the app’s [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> 

<span data-ttu-id="8345a-121">인터페이스 `IAuthorizationPolicyProvider` 에는 다음과 같은 두 가지 api가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-121">The `IAuthorizationPolicyProvider` interface contains two APIs:</span></span>

* <span data-ttu-id="8345a-122">[Getpolicyasync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) 는 지정 된 이름에 대 한 권한 부여 정책을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) returns an authorization policy for a given name.</span></span>
* <span data-ttu-id="8345a-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) 는 기본 권한 부여 정책 (정책이 지정 되지 않은 `[Authorize]` 특성에 사용 되는 정책)을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) returns the default authorization policy (the policy used for `[Authorize]` attributes without a policy specified).</span></span> 

<span data-ttu-id="8345a-124">이러한 두 Api를 구현 하 여 권한 부여 정책이 제공 되는 방식을 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-124">By implementing these two APIs, you can customize how authorization policies are provided.</span></span>

## <a name="parameterized-authorize-attribute-example"></a><span data-ttu-id="8345a-125">매개 변수가 있는 권한 부여 특성 예제</span><span class="sxs-lookup"><span data-stu-id="8345a-125">Parameterized authorize attribute example</span></span>

<span data-ttu-id="8345a-126">`IAuthorizationPolicyProvider` 가 유용한 시나리오 중 하나는 요구 사항이 `[Authorize]` 매개 변수에 따라 달라 지는 사용자 지정 특성을 사용 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-126">One scenario where `IAuthorizationPolicyProvider` is useful is enabling custom `[Authorize]` attributes whose requirements depend on a parameter.</span></span> <span data-ttu-id="8345a-127">예를 들어 [정책 기반 권한 부여](xref:security/authorization/policies) 설명서에서 사용 기간 기반 ("AtLeast21") 정책은 샘플로 사용 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-127">For example, in [policy-based authorization](xref:security/authorization/policies) documentation, an age-based (“AtLeast21”) policy was used as a sample.</span></span> <span data-ttu-id="8345a-128">앱의 다른 컨트롤러 작업을 *다른* 연령의 사용자가 사용할 수 있도록 설정 해야 하는 경우 다양 한 연령 기반 정책을 사용 하는 것이 유용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-128">If different controller actions in an app should be made available to users of *different* ages, it might be useful to have many different age-based policies.</span></span> <span data-ttu-id="8345a-129">응용 프로그램에 필요한 `AuthorizationOptions`다른 모든 연령 기반 정책을 등록 하는 대신 사용자 지정 `IAuthorizationPolicyProvider`을 사용 하 여 정책을 동적으로 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-129">Instead of registering all the different age-based policies that the application will need in `AuthorizationOptions`, you can generate the policies dynamically with a custom `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="8345a-130">정책을 더 쉽게 사용 하기 위해와 같은 `[MinimumAgeAuthorize(20)]`사용자 지정 권한 부여 특성을 사용 하 여 작업에 주석을 달 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-130">To make using the policies easier, you can annotate actions with custom authorization attribute like `[MinimumAgeAuthorize(20)]`.</span></span>

## <a name="custom-authorization-attributes"></a><span data-ttu-id="8345a-131">사용자 지정 권한 부여 특성</span><span class="sxs-lookup"><span data-stu-id="8345a-131">Custom Authorization attributes</span></span>

<span data-ttu-id="8345a-132">권한 부여 정책은 이름으로 식별 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-132">Authorization policies are identified by their names.</span></span> <span data-ttu-id="8345a-133">앞에서 `MinimumAgeAuthorizeAttribute` 설명한 사용자 지정은 인수를 해당 권한 부여 정책을 검색 하는 데 사용할 수 있는 문자열에 매핑해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-133">The custom `MinimumAgeAuthorizeAttribute` described previously needs to map arguments into a string that can be used to retrieve the corresponding authorization policy.</span></span> <span data-ttu-id="8345a-134">에서 `AuthorizeAttribute` 파생 시켜 속성을 래핑하 `AuthorizeAttribute.Policy` 는 `Age` 방법으로이 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-134">You can do this by deriving from `AuthorizeAttribute` and making the `Age` property wrap the `AuthorizeAttribute.Policy` property.</span></span>

```csharp
internal class MinimumAgeAuthorizeAttribute : AuthorizeAttribute
{
    const string POLICY_PREFIX = "MinimumAge";

    public MinimumAgeAuthorizeAttribute(int age) => Age = age;

    // Get or set the Age property by manipulating the underlying Policy property
    public int Age
    {
        get
        {
            if (int.TryParse(Policy.Substring(POLICY_PREFIX.Length), out var age))
            {
                return age;
            }
            return default(int);
        }
        set
        {
            Policy = $"{POLICY_PREFIX}{value.ToString()}";
        }
    }
}
```

<span data-ttu-id="8345a-135">이 특성 형식에는 `Policy` 하드 코드 된 접두사 (`"MinimumAge"`)를 기반으로 하는 문자열과 생성자를 통해 전달 된 정수가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-135">This attribute type has a `Policy` string based on the hard-coded prefix (`"MinimumAge"`) and an integer passed in via the constructor.</span></span>

<span data-ttu-id="8345a-136">정수를 매개 변수로 사용 한다는 점을 제외 하 고 다른 `Authorize` 특성과 동일한 방식으로 작업에 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-136">You can apply it to actions in the same way as other `Authorize` attributes except that it takes an integer as a parameter.</span></span>

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a><span data-ttu-id="8345a-137">사용자 지정 IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="8345a-137">Custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="8345a-138">사용자 지정 `MinimumAgeAuthorizeAttribute` 을 사용 하면 원하는 최소 사용 기간에 대 한 권한 부여 정책을 쉽게 요청할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-138">The custom `MinimumAgeAuthorizeAttribute` makes it easy to request authorization policies for any minimum age desired.</span></span> <span data-ttu-id="8345a-139">다음에 해결 해야 하는 문제는 다른 모든 연령에 대해 권한 부여 정책을 사용할 수 있는지 확인 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-139">The next problem to solve is making sure that authorization policies are available for all of those different ages.</span></span> <span data-ttu-id="8345a-140">여기서 `IAuthorizationPolicyProvider` 는 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-140">This is where an `IAuthorizationPolicyProvider` is useful.</span></span>

<span data-ttu-id="8345a-141">를 사용 `MinimumAgeAuthorizationAttribute`하는 경우 권한 부여 정책 이름이 패턴 `"MinimumAge" + Age`을 따르기 때문에 사용자 `IAuthorizationPolicyProvider` 지정은 다음을 통해 권한 부여 정책을 생성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-141">When using `MinimumAgeAuthorizationAttribute`, the authorization policy names will follow the pattern `"MinimumAge" + Age`, so the custom `IAuthorizationPolicyProvider` should generate authorization policies by:</span></span>

* <span data-ttu-id="8345a-142">정책 이름에서 나이를 구문 분석 합니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-142">Parsing the age from the policy name.</span></span>
* <span data-ttu-id="8345a-143">을 `AuthorizationPolicyBuilder` 사용 하 여 새을 만듭니다.`AuthorizationPolicy`</span><span class="sxs-lookup"><span data-stu-id="8345a-143">Using `AuthorizationPolicyBuilder` to create a new `AuthorizationPolicy`</span></span>
* <span data-ttu-id="8345a-144">다음 예제에서는 사용자가 쿠키를 통해 인증 된 것으로 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-144">In this and following examples it will be assumed that the user is authenticated via a cookie.</span></span> <span data-ttu-id="8345a-145">는 `AuthorizationPolicyBuilder` 하나 이상의 인증 체계 이름을 사용 하 여 생성 되거나 항상 성공 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-145">The `AuthorizationPolicyBuilder` should either be constructed with at least one authorization scheme name or always succeed.</span></span> <span data-ttu-id="8345a-146">그렇지 않으면 사용자에 게 챌린지를 제공 하는 방법에 대 한 정보가 없으므로 예외가 throw 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-146">Otherwise there is no information on how to provide a challenge to the user and an exception will be thrown.</span></span>
* <span data-ttu-id="8345a-147">사용 `AuthorizationPolicyBuilder.AddRequirements`기간에 따라 정책에 요구 사항을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-147">Adding requirements to the policy based on the age with `AuthorizationPolicyBuilder.AddRequirements`.</span></span> <span data-ttu-id="8345a-148">다른 시나리오에서는, `RequireClaim` `RequireRole`또는 `RequireUserName` 를 대신 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-148">In other scenarios, you might use `RequireClaim`, `RequireRole`, or `RequireUserName` instead.</span></span>

```csharp
internal class MinimumAgePolicyProvider : IAuthorizationPolicyProvider
{
    const string POLICY_PREFIX = "MinimumAge";

    // Policies are looked up by string name, so expect 'parameters' (like age)
    // to be embedded in the policy names. This is abstracted away from developers
    // by the more strongly-typed attributes derived from AuthorizeAttribute
    // (like [MinimumAgeAuthorize()] in this sample)
    public Task<AuthorizationPolicy> GetPolicyAsync(string policyName)
    {
        if (policyName.StartsWith(POLICY_PREFIX, StringComparison.OrdinalIgnoreCase) &&
            int.TryParse(policyName.Substring(POLICY_PREFIX.Length), out var age))
        {
            var policy = new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme);
            policy.AddRequirements(new MinimumAgeRequirement(age));
            return Task.FromResult(policy.Build());
        }

        return Task.FromResult<AuthorizationPolicy>(null);
    }
}
```

## <a name="multiple-authorization-policy-providers"></a><span data-ttu-id="8345a-149">여러 권한 부여 정책 공급자</span><span class="sxs-lookup"><span data-stu-id="8345a-149">Multiple authorization policy providers</span></span>

<span data-ttu-id="8345a-150">사용자 지정 `IAuthorizationPolicyProvider` 구현을 사용 하는 경우 ASP.NET Core는 `IAuthorizationPolicyProvider`인스턴스를 하나만 사용 한다는 점에 유의 하세요.</span><span class="sxs-lookup"><span data-stu-id="8345a-150">When using custom `IAuthorizationPolicyProvider` implementations, keep in mind that ASP.NET Core only uses one instance of `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="8345a-151">사용자 지정 공급자가 사용할 모든 정책 이름에 대해 권한 부여 정책을 제공할 수 없는 경우 백업 공급자로 대체 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-151">If a custom provider isn't able to provide authorization policies for all policy names that will be used, it should fall back to a backup provider.</span></span> 

<span data-ttu-id="8345a-152">예를 들어 사용자 지정 사용 기간 정책과 기존의 역할 기반 정책 검색을 모두 필요로 하는 응용 프로그램을 가정해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-152">For example, consider an application that needs both custom age policies and more traditional role-based policy retrieval.</span></span> <span data-ttu-id="8345a-153">이러한 앱은 다음과 같은 사용자 지정 권한 부여 정책 공급자를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-153">Such an app could use a custom authorization policy provider that:</span></span>

* <span data-ttu-id="8345a-154">정책 이름을 구문 분석 하려고 합니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-154">Attempts to parse policy names.</span></span> 
* <span data-ttu-id="8345a-155">정책 이름에 age가 포함 되어 있지 `DefaultAuthorizationPolicyProvider`않으면 다른 정책 공급자 (예:)를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-155">Calls into a different policy provider (like `DefaultAuthorizationPolicyProvider`) if the policy name doesn't contain an age.</span></span>

<span data-ttu-id="8345a-156">위에 표시 `IAuthorizationPolicyProvider` 된 예제 구현은 해당 생성자에서 대체 정책 공급자 `DefaultAuthorizationPolicyProvider` 를 만들어를 사용 하도록 업데이트할 수 있습니다 (정책 이름이 ' 이상 보존 기간 ' + 연령의 예상 패턴과 일치 하지 않는 경우에 사용 됨).</span><span class="sxs-lookup"><span data-stu-id="8345a-156">The example `IAuthorizationPolicyProvider` implementation shown above can be updated to use the `DefaultAuthorizationPolicyProvider` by creating a fallback policy provider in its constructor (to be used in case the policy name doesn't match its expected pattern of 'MinimumAge' + age).</span></span>

```csharp
private DefaultAuthorizationPolicyProvider FallbackPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    FallbackPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

<span data-ttu-id="8345a-157">그런 다음 null을 반환 하는 대신를 `FallbackPolicyProvider` 사용 하도록 메서드를업데이트할수있습니다.`GetPolicyAsync`</span><span class="sxs-lookup"><span data-stu-id="8345a-157">Then, the `GetPolicyAsync` method can be updated to use the `FallbackPolicyProvider` instead of returning null:</span></span>

```csharp
...
return FallbackPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a><span data-ttu-id="8345a-158">기본 정책</span><span class="sxs-lookup"><span data-stu-id="8345a-158">Default policy</span></span>

<span data-ttu-id="8345a-159">명명 된 권한 부여 정책을 제공 하는 것 외 `IAuthorizationPolicyProvider` 에도 사용자 `GetDefaultPolicyAsync` 지정은를 구현 하 여 `[Authorize]` 정책 이름이 지정 되지 않은 특성에 대 한 권한 부여 정책을 제공 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-159">In addition to providing named authorization policies, a custom `IAuthorizationPolicyProvider` needs to implement `GetDefaultPolicyAsync` to provide an authorization policy for `[Authorize]` attributes without a policy name specified.</span></span>

<span data-ttu-id="8345a-160">대부분의 경우이 권한 부여 특성에는 인증 된 사용자만 필요 하므로를 `RequireAuthenticatedUser`호출 하 여 필요한 정책을 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-160">In many cases, this authorization attribute only requires an authenticated user, so you can make the necessary policy with a call to `RequireAuthenticatedUser`:</span></span>

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

<span data-ttu-id="8345a-161">사용자 지정 `IAuthorizationPolicyProvider`의 모든 측면과 마찬가지로 필요에 따라이를 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-161">As with all aspects of a custom `IAuthorizationPolicyProvider`, you can customize this, as needed.</span></span> <span data-ttu-id="8345a-162">경우에 따라 대체 (fallback) `IAuthorizationPolicyProvider`에서 기본 정책을 검색 하는 것이 좋을 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-162">In some cases, it may be desirable to retrieve the default policy from a fallback `IAuthorizationPolicyProvider`.</span></span>

## <a name="required-policy"></a><span data-ttu-id="8345a-163">필수 정책</span><span class="sxs-lookup"><span data-stu-id="8345a-163">Required policy</span></span>

<span data-ttu-id="8345a-164">사용자 지정 `IAuthorizationPolicyProvider` 은 필요한 경우 `GetRequiredPolicyAsync` 항상 필요한 정책을 제공 하 여 구현 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-164">A custom `IAuthorizationPolicyProvider` needs to implement `GetRequiredPolicyAsync` to, optionally, provide a policy that is always required.</span></span> <span data-ttu-id="8345a-165">에서 `GetRequiredPolicyAsync` null이 아닌 정책을 반환 하는 경우 해당 정책은 요청 된 다른 (명명 된 또는 기본) 정책과 함께 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-165">If `GetRequiredPolicyAsync` returns a non-null policy, that policy will be combined with any other (named or default) policy that is requested.</span></span>

<span data-ttu-id="8345a-166">필요한 정책이 필요 하지 않은 경우 공급자는 null을 반환 하거나 대체 (fallback) 공급자를 연기할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-166">If no required policy is needed, the provider can just return null or defer to the fallback provider:</span></span>

```csharp
public Task<AuthorizationPolicy> GetRequiredPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a><span data-ttu-id="8345a-167">사용자 지정 IAuthorizationPolicyProvider 사용</span><span class="sxs-lookup"><span data-stu-id="8345a-167">Use a custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="8345a-168">에서 `IAuthorizationPolicyProvider`사용자 지정 정책을 사용 하려면 다음을 수행 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-168">To use custom policies from an `IAuthorizationPolicyProvider`, you must:</span></span>

* <span data-ttu-id="8345a-169">모든 정책 기반 `AuthorizationHandler` 권한 부여 시나리오와 마찬가지로 종속성 주입 ( [정책 기반 권한 부여](xref:security/authorization/policies#authorization-handlers)에 설명)을 사용 하 여 적절 한 형식을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-169">Register the appropriate `AuthorizationHandler` types with dependency injection (described in [policy-based authorization](xref:security/authorization/policies#authorization-handlers)), as with all policy-based authorization scenarios.</span></span>
* <span data-ttu-id="8345a-170">응용 프로그램의 `IAuthorizationPolicyProvider` 종속성 주입 서비스 컬렉션 `Startup.ConfigureServices`()에 사용자 지정 형식을 등록 하 여 기본 정책 공급자를 대체 합니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-170">Register the custom `IAuthorizationPolicyProvider` type in the app's dependency injection service collection (in `Startup.ConfigureServices`) to replace the default policy provider.</span></span>

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

<span data-ttu-id="8345a-171">전체 사용자 지정 `IAuthorizationPolicyProvider` 샘플은 [aspnet/authsamples GitHub 리포지토리에서](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8345a-171">A complete custom `IAuthorizationPolicyProvider` sample is available in the [aspnet/AuthSamples GitHub repository](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider).</span></span>
