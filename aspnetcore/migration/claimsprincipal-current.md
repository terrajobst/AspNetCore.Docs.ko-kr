---
title: ClaimsPrincipal.Current에서 마이그레이션
author: mjrousos
description: 현재 인증 된 사용자의 id 및 ASP.NET Core에서 클레임을 검색할 ClaimsPrincipal.Current에서 마이그레이션하는 방법에 알아봅니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 03/26/2019
uid: migration/claimsprincipal-current
ms.openlocfilehash: 526cc3cf3a58a656e2a1b162cfaccacc7694dc51
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64894790"
---
# <a name="migrate-from-claimsprincipalcurrent"></a>ClaimsPrincipal.Current에서 마이그레이션

ASP.NET 4.x 프로젝트에서 사용 하도록 공통적으로 적용 되었습니다 [ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current) 현재 검색할 사용자의 id 및 클레임을 인증 합니다. ASP.NET Core에서 더 이상이 속성이 설정 됩니다. 종속 되었던는 코드를 다른 수단을 통해 현재 인증 된 사용자의 id를 가져오려면 업데이트 해야 합니다.

## <a name="context-specific-data-instead-of-static-data"></a>정적 데이터 대신 상황에 맞는 데이터

ASP.NET Core의 값이 모두를 사용 하는 경우 `ClaimsPrincipal.Current` 고 `Thread.CurrentPrincipal` 설정 되지 않습니다. 이러한 두 속성은 ASP.NET Core는 일반적으로 방지 하는 정적 상태를 나타냅니다. ASP.NET Core의 아키텍처 상황에 맞는 서비스 컬렉션에서 종속성 (예: 현재 사용자의 id)를 검색 하는 것 대신 (사용 하 여 해당 [종속성 주입](xref:fundamentals/dependency-injection) (DI) 모델). 더 많은 이란 `Thread.CurrentPrincipal` 이므로 스레드 정적 일부 비동기 시나리오에서 변경 내용을 유지 되지 않을 수 있습니다 (및 `ClaimsPrincipal.Current` 호출 `Thread.CurrentPrincipal` 기본적으로).

문제 스레드의 종류를 이해 하려면 정적 멤버는 비동기 시나리오에서 다음 코드 조각을 고려해 야 할 발생할 수 있습니다.

```csharp
// Create a ClaimsPrincipal and set Thread.CurrentPrincipal
var identity = new ClaimsIdentity();
identity.AddClaim(new Claim(ClaimTypes.Name, "User1"));
Thread.CurrentPrincipal = new ClaimsPrincipal(identity);

// Check the current user
Console.WriteLine($"Current user: {Thread.CurrentPrincipal?.Identity.Name}");

// For the method to complete asynchronously
await Task.Yield();

// Check the current user after
Console.WriteLine($"Current user: {Thread.CurrentPrincipal?.Identity.Name}");
```

위의 샘플 코드 집합 `Thread.CurrentPrincipal` 전과 비동기 호출을 기다린 후 해당 값을 확인 합니다. `Thread.CurrentPrincipal` 관련이 합니다 *스레드* 는 속성을 설정 하 고 메서드는 await 뒤 다른 스레드에서 실행을 다시 시작할 수 있습니다. 따라서 `Thread.CurrentPrincipal` 은 present를 먼저 검사 하지만를 호출한 후 null 경우 `await Task.Yield()`합니다.

앱의 DI 서비스 컬렉션에서 현재 사용자의 id를 가져오는 이므로 더 테스트도 테스트 identities를 쉽게 삽입할 수입니다.

## <a name="retrieve-the-current-user-in-an-aspnet-core-app"></a>ASP.NET Core 앱에서 현재 사용자를 검색 합니다.

현재 인증된 된 사용자를 검색 하는 방법은 여러 가지 `ClaimsPrincipal` 대신 ASP.NET Core에서 `ClaimsPrincipal.Current`:

* **ControllerBase.User**. MVC 컨트롤러는 현재 인증 된 사용자로 액세스할 수 있는 해당 [사용자](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.user) 속성입니다.
* **HttpContext.User**. 현재에 대 한 액세스를 사용 하 여 구성 요소 `HttpContext` (예: 미들웨어)는 현재 사용자의 가져올 수 있습니다 `ClaimsPrincipal` 에서 [HttpContext.User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user)합니다.
* **호출자에서 전달 된**합니다. 현재에 액세스 하지 않고 라이브러리 `HttpContext` 컨트롤러 또는 미들웨어 구성 요소에서 이라고 하 고 인수로 전달 하는 현재 사용자의 id를 가질 수 있습니다.
* **IHttpContextAccessor**. ASP.NET Core로 마이그레이션되는 프로젝트는 너무 커서 모든 필요한 위치에 현재 사용자의 id를 쉽게 전달할 수 있습니다. 이러한 경우 [IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) 대 안으로 사용할 수 있습니다. `IHttpContextAccessor` 현재 액세스할 수 `HttpContext` (있는 경우). 단기적인 해결책을 ASP.NET Core DI 기반 아키텍처를 사용 하 여 작동 하도록 아직 업데이트 되지 않은 코드에서 현재 사용자의 id를 가져오는 것입니다.

  * 확인 `IHttpContextAccessor` 를 호출 하 여 DI 컨테이너에서 사용 가능한 [AddHttpContextAccessor](https://github.com/aspnet/Hosting/issues/793) 에서 `Startup.ConfigureServices`합니다.
  * 인스턴스를 가져올 `IHttpContextAccessor` 시작 하는 동안 정적 변수에 저장 합니다. 인스턴스를 이전에 정적 속성에서 현재 사용자를 검색 하는 코드를 사용할 수 있습니다.
  * 현재 사용자의 검색 `ClaimsPrincipal` 를 사용 하 여 `HttpContextAccessor.HttpContext?.User`입니다. 이 코드는 HTTP 요청 컨텍스트 외부에서 사용 되는 경우는 `HttpContext` null입니다.

마지막 옵션을 사용 하 여는 `IHttpContextAccessor` 인스턴스 정적 변수에 저장, (정적 종속성 삽입 된 종속성을 선호) ASP.NET Core 원칙을 반대 됩니다. 결과적으로 검색 하려면 `IHttpContextAccessor` 종속성 주입에서 인스턴스를 대신 합니다. 정적 도우미 수 하는 유용한 브리지 하지만 이전에 사용 하는 많은 기존 ASP.NET 앱을 마이그레이션하는 경우 `ClaimsPrincipal.Current`합니다.
