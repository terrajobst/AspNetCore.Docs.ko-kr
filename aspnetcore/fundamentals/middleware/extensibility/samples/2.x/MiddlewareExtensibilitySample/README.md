---
ms.openlocfilehash: 41021e30ae85dd0ae42cbe6f1606727e21bd7707
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59705525"
---
# <a name="aspnet-core-middleware-extensibility-sample"></a>ASP.NET Core 미들웨어 확장성 샘플

이 샘플에서는 [ASP.NET Core의 팩터리 기반 미들웨어 활성화](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/middleware-extensibility)에 설명된 시나리오를 보여줍니다.

샘플 앱은 다음에 의해 활성화되는 미들웨어를 보여 줍니다.

* 규칙. 규칙에 따른 미들웨어 활성화에 대한 자세한 내용은 [미들웨어](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/) 항목을 참조하세요.
* [IMiddleware](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddleware) 구현입니다. 기본 [IMiddlewareFactory 클래스](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory)가 미들웨어를 활성화합니다.

미들웨어 구현은 동일하게 작동하며, 쿼리 문자열 매개 변수(`key`)에서 제공한 값을 기록합니다. 미들웨어는 주입된 데이터베이스 컨텍스트(범위가 지정된 서비스)를 사용하여 메모리 내 데이터베이스에 쿼리 문자열 값을 기록합니다.
