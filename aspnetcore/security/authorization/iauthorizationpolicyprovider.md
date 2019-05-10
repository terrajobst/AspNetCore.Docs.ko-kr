---
title: ASP.NET Core에서 사용자 지정 권한 부여 정책 공급자
author: mjrousos
description: ASP.NET Core 앱에서 사용자 지정 IAuthorizationPolicyProvider를 사용 하 여 동적으로 권한 부여 정책을 생성 하는 방법에 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: e17372bb0ec9091c385a70b1e907eaa3cff24003
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896360"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a>ASP.NET Core에서 IAuthorizationPolicyProvider를 사용 하 여 사용자 지정 권한 부여 정책 공급자 

[Mike Rousos](https://github.com/mjrousos)

일반적으로 사용 하는 경우 [정책 기반 권한 부여](xref:security/authorization/policies), 정책을 호출 하 여 등록 된 `AuthorizationOptions.AddPolicy` 권한 부여 서비스 구성의 일부분으로 합니다. 일부 시나리오에서는 없을 수 (또는 바람직하지) 권한 부여 정책을 모두 이러한 방식으로 등록 합니다. 이러한 경우 사용자 지정을 사용할 수 있습니다 `IAuthorizationPolicyProvider` 권한 부여 정책을 제공 하는 방법을 제어 합니다.

예제 시나리오의 경우 사용자 지정 [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) 유용할 수 있습니다 포함:

* 외부 서비스를 사용 하 여 정책 평가 제공 합니다.
* (다른 방 번호 또는 예를 들어 연령대)에 광범위 한 정책 사용 하므로 것은 의미가 없습니다 사용 하 여 각 개별 권한 부여 정책을 추가 하는 `AuthorizationOptions.AddPolicy` 호출 합니다.
* 외부 데이터 원본 (예: 데이터베이스)에 대 한 정보를 기반으로 하는 런타임 시 정책 만들기 또는 다른 메커니즘을 통해 권한 부여 요구 사항을 동적으로 결정 합니다.

[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) 에서 합니다 [AspNetCore GitHub 리포지토리](https://github.com/aspnet/AspNetCore)합니다. Aspnet/AspNetCore 리포지토리 ZIP 파일을 다운로드 합니다. 파일을 압축을 풉니다. 로 이동 합니다 *src/Security/샘플/CustomPolicyProvider* 프로젝트 폴더입니다.

## <a name="customize-policy-retrieval"></a>정책 검색을 사용자 지정

ASP.NET Core 앱의 구현을 사용 합니다 `IAuthorizationPolicyProvider` 권한 부여 정책을 검색 하는 인터페이스입니다. 기본적으로 [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) 등록 되 고 사용 합니다. `DefaultAuthorizationPolicyProvider` 정책을 반환 합니다.는 `AuthorizationOptions` 에서 제공 되는 `IServiceCollection.AddAuthorization` 호출 합니다.

다른 등록 하 여이 동작을 사용자 지정할 수 있습니다 `IAuthorizationPolicyProvider` 앱의 구현을 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너입니다. 

`IAuthorizationPolicyProvider` 인터페이스 두 Api가 포함 됩니다.

* [GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) 지정 된 이름에 대 한 권한 부여 정책을 반환 합니다.
* [GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) 기본 권한 부여 정책을 반환 합니다 (에 사용 된 정책 `[Authorize]` 지정 된 정책 없이 특성). 

이러한 두 가지 Api를 구현 하 여 권한 부여 정책을 제공 하는 방식을 사용자 지정할 수 있습니다.

## <a name="parameterized-authorize-attribute-example"></a>매개 변수가 특성 예제 권한 부여

시나리오 중 하나는 `IAuthorizationPolicyProvider` 유용 사용자 지정이 사용 가능 하도록 `[Authorize]` 특성 요구 사항이 있는 매개 변수에 따라 달라 집니다. 예를 들어 [정책 기반 권한 부여](xref:security/authorization/policies) 설명서에는 보존 기간 기반 ("AtLeast21") 정책 샘플을 사용 했습니다. 하는 경우 다른 컨트롤러 작업 앱에서 사용할 수 있어야 사용자에 게 *다른* 연령에 많은 다른 보존 기간 기반 정책이 유용할 수 있습니다. 모든 다른 보존 기간 기반 되는 정책 응용 프로그램의 필요를 등록 하는 대신 `AuthorizationOptions`, 정책을 사용자 지정 동적으로 생성할 수 있습니다 `IAuthorizationPolicyProvider`합니다. 사용 하 여 정책을 쉽게, 같은 사용자 지정 권한 부여 특성을 사용 하 여 작업 주석을 달 수 `[MinimumAgeAuthorize(20)]`입니다.

## <a name="custom-authorization-attributes"></a>사용자 지정 권한 부여 특성

권한 부여 정책 이름으로 식별 됩니다. 사용자 지정 `MinimumAgeAuthorizeAttribute` 설명 이전에 해당 하는 권한 부여 정책을 검색 하는 문자열에 인수를 매핑하는 데 필요한 합니다. 파생 하 여이 수행할 수 있습니다 `AuthorizeAttribute` 줄어들고 합니다 `Age` 속성 줄 바꿈 합니다 `AuthorizeAttribute.Policy` 속성입니다.

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

이 특성 형식에는 `Policy` 하드 코드 된 접두사를 바탕으로 한 문자열 (`"MinimumAge"`) 생성자를 통해 전달 된 정수 및 합니다.

동일한 방식으로 다른 작업에 적용할 수 있습니다 `Authorize` 매개 변수로 정수 소요 되는 점을 제외 하 고 특성입니다.

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a>사용자 지정 IAuthorizationPolicyProvider

사용자 지정 `MinimumAgeAuthorizeAttribute` 쉽게 요청 권한 부여 정책에 필요한 모든 최소 보존 기간에 대 한 합니다. 다음 문제를 해결 하는 이러한 다른 연령대의 모든 권한 부여 정책을 사용할 수 있는지 것입니다. 이 경우는 `IAuthorizationPolicyProvider` 유용 합니다.

사용 하는 경우 `MinimumAgeAuthorizationAttribute`, 권한 부여 정책 이름 패턴을 따릅니다 `"MinimumAge" + Age`이므로 사용자 지정 `IAuthorizationPolicyProvider` 권한 부여 정책을 생성 해야 합니다.

* 정책 이름에서 나 구문 분석 합니다.
* 사용 하 여 `AuthorizationPolicyBuilder` 새로 만들려면 `AuthorizationPolicy`
* 사용 하 여 보존 기간에 따라 정책에 요구 사항 추가 `AuthorizationPolicyBuilder.AddRequirements`합니다. 다른 시나리오에서는 사용할 수 있습니다 `RequireClaim`하십시오 `RequireRole`, 또는 `RequireUserName` 대신 합니다.

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
            var policy = new AuthorizationPolicyBuilder();
            policy.AddRequirements(new MinimumAgeRequirement(age));
            return Task.FromResult(policy.Build());
        }

        return Task.FromResult<AuthorizationPolicy>(null);
    }
}
```

## <a name="multiple-authorization-policy-providers"></a>여러 권한 부여 정책 공급자

사용자 지정을 사용 하는 경우 `IAuthorizationPolicyProvider` 구현에서는 ASP.NET Core의 인스턴스 하나에 사용 하 여 있다는 사실을 잊지 마십시오 `IAuthorizationPolicyProvider`합니다. 사용자 지정 공급자를 사용 되는 모든 정책 이름에 대 한 권한 부여 정책을 제공할 수 없으면 백업 공급자에 다시 포함 되어야 합니다. 

예를 들어, 사용자 지정 보존 기간 정책 및 기존의 역할 기반 정책 검색 하는 응용 프로그램을 고려 합니다. 이러한 앱을 사용자 지정 권한 부여 정책 공급자를 사용할 수 있는:

* 정책 이름을 구문 분석 하려고 시도 합니다. 
* 다른 정책 공급자에 대 한 호출 (같은 `DefaultAuthorizationPolicyProvider`) 정책 이름이 나 포함 되지 않은 경우.

이 예제에서는 `IAuthorizationPolicyProvider` 위에서 보여드린 구현은 사용 하도록 업데이트할 수 있습니다는 `DefaultAuthorizationPolicyProvider` (하는 데 정책 이름이 예상된 패턴 'MinimumAge' + 시대에 일치 하지 않습니다 하는 경우) 생성자에서 대체 (fallback) 정책 공급자를 만들어 합니다.

```csharp
private DefaultAuthorizationPolicyProvider FallbackPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    FallbackPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

그런 다음, `GetPolicyAsync` 메서드를 사용 하도록 업데이트할 수는 `FallbackPolicyProvider` null을 반환 하는 대신:

```csharp
...
return FallbackPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a>기본 정책

명명 된 권한 부여 정책에 사용자 지정을 제공 하는 것 외에도 `IAuthorizationPolicyProvider` 를 구현 해야 `GetDefaultPolicyAsync` 에 대 한 권한 부여 정책을 제공 `[Authorize]` 정책 이름 지정 하지 않고 특성입니다.

대부분의 경우이 권한 부여 특성 하기만 인증된 된 사용자에 대 한 호출을 사용 하 여 필요한 정책을 있도록 `RequireAuthenticatedUser`:

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder().RequireAuthenticatedUser().Build());
```

사용자 지정의 모든 측면으로 `IAuthorizationPolicyProvider`를 사용자 지정할 수 있습니다 따라서 필요에 따라 합니다. 일부 경우에는 대체 (fallback)에서 기본 정책을 검색 하는 것이 바람직 수 `IAuthorizationPolicyProvider`입니다.

## <a name="required-policy"></a>필요한 정책

사용자 지정 `IAuthorizationPolicyProvider` 를 구현 해야 `GetRequiredPolicyAsync` 를 필요에 따라 항상 필요한 것은 하는 정책을 제공 합니다. 경우 `GetRequiredPolicyAsync` null이 아닌 정책을 반환 합니다. 해당 정책이 다른 (명명 된 또는 기본값)와 결합 하는 요청 되는 정책입니다.

필요한 정책 없음, 필요한 경우 공급자 null을 반환 하거나 대체 (fallback) 공급자에 게 맡기는 것 수 있습니다.

```csharp
public Task<AuthorizationPolicy> GetRequiredPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a>사용자 지정 IAuthorizationPolicyProvider 사용

사용자 지정 정책을 사용 하는 `IAuthorizationPolicyProvider`를 수행 해야 합니다.

* 적절 한 등록 `AuthorizationHandler` 종속성 주입을 사용 하 여 형식 (에서 설명한 [정책 기반 권한 부여](xref:security/authorization/policies#authorization-handlers)), 모든 정책 기반 권한 부여 시나리오와 마찬가지로 합니다.
* 사용자 지정 등록 `IAuthorizationPolicyProvider` 앱의 종속성 주입 서비스 컬렉션의 형식 (에서 `Startup.ConfigureServices`) 기본 정책 공급자를 교체할 수 있습니다.

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

전체 사용자 지정 `IAuthorizationPolicyProvider` 샘플에서 사용할 수는 [aspnet/AuthSamples GitHub 리포지토리](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)합니다.
