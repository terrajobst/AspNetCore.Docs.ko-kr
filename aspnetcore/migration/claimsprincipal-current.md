---
title: ClaimsPrincipal에서 마이그레이션
author: mjrousos
description: ASP.NET Core에서 현재 인증 된 사용자의 id 및 클레임을 검색 하기 위해 ClaimsPrincipal에서 다른 곳으로 마이그레이션하는 방법에 대해 알아봅니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 03/26/2019
uid: migration/claimsprincipal-current
ms.openlocfilehash: f7472f5b851d3869da3d26b881e276ce4ca004fb
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651873"
---
# <a name="migrate-from-claimsprincipalcurrent"></a>ClaimsPrincipal에서 마이그레이션

ASP.NET 4.x 프로젝트에서는 [ClaimsPrincipal](/dotnet/api/system.security.claims.claimsprincipal.current) 를 사용 하 여 현재 인증 된 사용자의 id 및 클레임을 검색 하는 것이 일반적 이었습니다. ASP.NET Core에서이 속성은 더 이상 설정 되지 않습니다. 이에 종속 된 코드는 다른 수단을 통해 현재 인증 된 사용자의 id를 가져오기 위해 업데이트 해야 합니다.

## <a name="context-specific-data-instead-of-static-data"></a>정적 데이터 대신 컨텍스트별 데이터

ASP.NET Core를 사용 하는 경우 `ClaimsPrincipal.Current` 및 `Thread.CurrentPrincipal`의 값이 모두 설정 되지 않습니다. 이러한 속성은 모두 정적 상태를 나타내므로 일반적으로 ASP.NET Core 하지 않습니다. 대신 ASP.NET Core의 아키텍처는 사용자의 [종속성 주입](xref:fundamentals/dependency-injection) (DI) 모델을 사용 하 여 컨텍스트별 서비스 컬렉션에서 현재 사용자의 id와 같은 종속성을 검색 하는 것입니다. `Thread.CurrentPrincipal`은 스레드 정적 이므로 일부 비동기 시나리오에서 변경 내용이 유지 되지 않을 수 있습니다 (기본적으로 `Thread.CurrentPrincipal`를 호출 `ClaimsPrincipal.Current`).

정적 멤버가 비동기 시나리오로 이어질 수 있는 문제의 종류를 이해 하려면 다음 코드 조각을 참조 하십시오.

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

위의 샘플 코드는 `Thread.CurrentPrincipal`를 설정 하 고 비동기 호출을 대기 하기 전후에 해당 값을 확인 합니다. `Thread.CurrentPrincipal`은이 메서드가 설정 된 *스레드에* 고유 하며, 대기 후 다른 스레드에서 실행을 다시 시작할 수 있습니다. 따라서 `Thread.CurrentPrincipal`는 처음 확인 될 때 제공 되지만 `await Task.Yield()`를 호출한 후에는 null입니다.

테스트 id를 쉽게 삽입할 수 있으므로 앱의 DI 서비스 컬렉션에서 현재 사용자의 id를 가져오는 것도 더 쉽게 테스트할 수 있습니다.

## <a name="retrieve-the-current-user-in-an-aspnet-core-app"></a>ASP.NET Core 앱에서 현재 사용자 검색

`ClaimsPrincipal.Current`대신 ASP.NET Core에서 현재 인증 된 사용자의 `ClaimsPrincipal`을 검색 하는 몇 가지 옵션이 있습니다.

* **Controllerbase. User**. MVC 컨트롤러는 [사용자](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.user) 속성을 사용 하 여 현재 인증 된 사용자에 액세스할 수 있습니다.
* **HttpContext**. 현재 `HttpContext`에 액세스할 수 있는 구성 요소 (예: 미들웨어)는 [httpcontext.current](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user)에서 현재 사용자의 `ClaimsPrincipal`를 가져올 수 있습니다.
* **호출자에 게 전달**됩니다. 현재 `HttpContext`에 대 한 액세스 권한이 없는 라이브러리는 종종 컨트롤러 또는 미들웨어 구성 요소에서 호출 되며 현재 사용자의 id를 인수로 전달할 수 있습니다.
* **IHttpContextAccessor**. ASP.NET Core로 마이그레이션되는 프로젝트가 너무 커서 현재 사용자의 id를 필요한 모든 위치로 쉽게 전달할 수 없습니다. 이러한 경우에는 [IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) 를 해결 방법으로 사용할 수 있습니다. `IHttpContextAccessor`은 현재 `HttpContext` (있는 경우)에 액세스할 수 있습니다. DI를 사용 하는 경우 <xref:fundamentals/httpcontext>를 참조 하세요. ASP.NET Core의 DI 기반 아키텍처와 함께 작동 하도록 아직 업데이트 되지 않은 코드에서 현재 사용자의 id를 얻는 단기 솔루션은 다음과 같습니다.

  * `Startup.ConfigureServices`에서 [Addhttpcontextaccessor](https://github.com/aspnet/Hosting/issues/793) 를 호출 하 여 DI 컨테이너에서 `IHttpContextAccessor`를 사용할 수 있도록 합니다.
  * 시작 하는 동안 `IHttpContextAccessor`의 인스턴스를 가져와 정적 변수에 저장 합니다. 이전에 정적 속성에서 현재 사용자를 검색 한 코드에서 인스턴스를 사용할 수 있습니다.
  * `HttpContextAccessor.HttpContext?.User`를 사용 하 여 현재 사용자의 `ClaimsPrincipal` 검색 합니다. 이 코드를 HTTP 요청의 컨텍스트 외부에서 사용 하는 경우 `HttpContext`은 null입니다.

정적 변수에 저장 된 `IHttpContextAccessor` 인스턴스를 사용 하는 최종 옵션은 ASP.NET Core 원리 (정적 종속성에 삽입 된 종속성 사용)와 반대입니다. 대신 종속성 주입에서 `IHttpContextAccessor` 인스턴스를 검색 하도록 계획 합니다. 정적 도우미는 이전에 `ClaimsPrincipal.Current`를 사용 하 던 대량 기존 ASP.NET 앱을 마이그레이션할 때 유용한 브리지가 될 수 있습니다.
