---
title: ASP.NET Core 앱의 Details 및 Delete 메서드 검사
author: rick-anderson
description: 기본적인 ASP.NET Core MVC 앱에서 Details 컨트롤러 메서드 및 보기에 대해 알아봅니다.
ms.author: riande
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/details
ms.openlocfilehash: 056017ea4f4073fa0b1cd747d06775b2a33616cf
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59012671"
---
# <a name="examine-the-details-and-delete-methods-of-an-aspnet-core-app"></a>ASP.NET Core 앱의 Details 및 Delete 메서드 검사

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

Movie 컨트롤러를 열고 `Details` 메서드를 검사합니다.

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_details)]

이 작업 메서드를 만든 MVC 스캐폴딩 엔진은 메서드를 호출하는 HTTP 요청을 보여 주는 설명을 추가합니다. 이 경우 3개의 URL 세그먼트인 `Movies` 컨트롤러, `Details` 메서드 및 `id` 값을 가진 GET 요청입니다. 이러한 세그먼트 회수는 *Startup.cs*에서 정의됩니다.

[!code-csharp[](start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

EF를 통해 `FirstOrDefaultAsync` 메서드를 사용하는 데이터를 쉽게 검색할 수 있습니다. 메서드에 기본 구성된 중요한 보안 기능은 검색 메서드가 이를 사용하여 어떠한 작업을 시도하기 전에 동영상을 발견했는지 코드에서 확인하는 것입니다. 예를 들어 해커는 링크에서 만든 URL을 `http://localhost:xxxx/Movies/Details/1`에서 `http://localhost:xxxx/Movies/Details/12345`(또는 실제 동영상을 표시하지 않는 다른 값)와 같은 URL로 변경하여 사이트에 오류를 발생시킬 수 있습니다. Null 동영상을 검사하지 않은 경우 앱은 예외를 throw합니다.

`Delete` 및 `DeleteConfirmed` 메서드를 검사합니다.

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_delete)]

`HTTP GET Delete` 메서드는 지정된 동영상을 삭제하지 않고 삭제를 제출(HttpPost)할 수 있는 동영상의 보기를 반환합니다. GET 요청에 대한 응답에서 삭제 작업 수행(또는 해당 문제를 위해 편집 작업 수행, 작업 만들기 또는 데이터를 변경하는 기타 작업)은 보안 허점을 야기합니다.

데이터를 삭제하는 `[HttpPost]` 메서드는 HTTP POST 메서드에 고유한 서명 또는 이름을 제공하기 위해 `DeleteConfirmed`로 이름이 지정됩니다. 두 개의 메서드 서명은 다음과 같습니다.

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete2)]

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete3)]

CLR(공용 언어 런타임)은 고유한 매개 변수 서명을 갖기 위해 오버로드된 메서드가 필요합니다(동일한 메서드 이름이지만 다른 매개 변수의 목록). 그러나 여기에서 두 개의 `Delete` 메서드가 필요합니다. 하나는 GET에 대한 것이며 다른 하나는 POST에 대한 것입니다. 두 메서드에는 동일한 매개 변수 서명이 있습니다. (모두 매개 변수로 단일 정수를 허용해야 합니다.)

이 문제에 대한 두 가지의 방법이 있습니다. 하나는 메서드에 서로 다른 이름을 지정하는 것입니다. 앞의 예에서 스캐폴딩 메커니즘이 수행한 것입니다. 그러나 이는 작은 문제를 야기합니다. ASP.NET은 URL의 세그먼트를 이름으로 작업 메서드에 매핑하고 메서드의 이름을 바꾸면 정상적으로 라우팅하여 해당 메서드를 찾을 수 없게 됩니다. 솔루션은 예제에서 확인한 것으로, `ActionName("Delete")` 특성을 `DeleteConfirmed` 메서드에 추가하는 것입니다. 해당 특성은 POST 요청에 대한 /Delete/를 포함하는 URL이 `DeleteConfirmed` 메서드를 찾도록 라우팅 시스템에 대한 매핑을 수행합니다.

동일한 이름 및 서명을 가진 메서드에 대한 또 다른 일반적인 해결책은 POST 메서드의 서명을 추가(사용되지 않는) 매개 변수를 포함하도록 인위적으로 변경하는 것입니다. 즉, `notUsed` 매개 변수를 추가했을 때 이전 게시에서 수행했던 작업입니다. `[HttpPost] Delete` 메서드에 대해 여기에서 동일한 작업을 수행할 수 있습니다.

```csharp
// POST: Movies/Delete/6
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Delete(int id, bool notUsed)
```

### <a name="publish-to-azure"></a>Azure에 게시

Azure에 배포하는 방법에 대한 자세한 내용은 [자습서: Azure App Service에서 .NET Core 및 SQL Database 웹앱 빌드](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)를 참조하세요.

> [!div class="step-by-step"]
> [이전](validation.md)
