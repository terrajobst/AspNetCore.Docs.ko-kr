---
title: ASP.NET Core 앱의 Details 및 Delete 메서드 검토
author: rick-anderson
description: 기본적인 ASP.NET Core MVC 앱에서 Details 컨트롤러 메서드 및 보기에 대해 알아봅니다.
ms.author: riande
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/details
ms.openlocfilehash: d19e8cdb63da2bb9c66db1943dfcec183d432401
ms.sourcegitcommit: 2719c70cd15a430479ab4007ff3e197fbf5dfee0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68862979"
---
# <a name="examine-the-details-and-delete-methods-of-an-aspnet-core-app"></a>ASP.NET Core 앱의 Details 및 Delete 메서드 검토

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

Movie 컨트롤러를 열고 `Details` 메서드를 검토합니다.

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_details)]

이 작업 메서드를 만든 MVC 스캐폴딩 엔진은 메서드를 호출하는 HTTP 요청을 보여주는 주석을 추가합니다. 이 경우 `Movies` 컨트롤러, `Details` 메서드 및 `id` 값의 세 가지 URL 세그먼트를 가진 GET 요청입니다. 이러한 세그먼트는 *Startup.cs*에서 정의되었다는 점을 기억하세요.

[!code-csharp[](start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

EF를 사용하면 `FirstOrDefaultAsync` 메서드를 이용하여 데이터를 쉽게 검색할 수 있습니다. 메서드에 기본 구성된 중요한 보안 기능은 영화를 사용하여 무언가 작업을 시도하기 전에 검색 메서드가 영화를 발견했는지부터 코드에서 확인하는 것입니다. 예를 들어 해커는 링크에서 만든 URL을 `http://localhost:{PORT}/Movies/Details/1`에서 `http://localhost:{PORT}/Movies/Details/12345`(또는 실제 영화를 나타내지 않는 다른 값)와 같은 URL로 변경하여 사이트에 오류를 발생시킬 수 있습니다. Null 영화를 검사하지 않으면 앱은 예외를 던집니다.

`Delete` 및 `DeleteConfirmed` 메서드를 검토합니다.

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_delete)]

`HTTP GET Delete` 메서드는 지정한 영화를 삭제하지 않고 삭제를 제출(HttpPost)할 수 있는 영화의 보기를 반환한다는 점에 유의하세요. GET 요청에 대한 응답에서 삭제 작업을 수행하는 것은(또는 해당 문제에 대한 편집 작업, 만들기 작업 또는 데이터를 변경하는 기타 작업을 수행하는 것은) 보안 허점을 야기합니다.

데이터를 삭제하는 `[HttpPost]` 메서드의 이름은 HTTP POST 메서드에 고유한 서명 또는 이름을 부여하기 위해 `DeleteConfirmed`로 지정됩니다. 두 메서드의 서명은 다음과 같습니다.

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete2)]

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete3)]

CLR(공용 언어 런타임)은 오버로드된 메서드에 고유한 매개 변수 서명(메서드 이름은 동일하지만 매개 변수 목록은 다른)을 필요로 합니다. 그러나 지금은 두 개의 `Delete` 메서드가 필요합니다. 하나는 GET에 대한 메서드이고 다른 하나는 POST에 대한 메서드입니다. 두 메서드는 동일한 매개 변수 서명을 갖습니다. (모두 매개 변수로 단일 정수를 받습니다.)

이 문제에는 두 가지 처리 방법이 있는데, 한 가지는 메서드에 서로 다른 이름을 지정하는 것입니다. 이전 예제에서 스캐폴딩 메커니즘이 수행한 방법입니다. 그러나 이 방벙은 작은 문제를 야기합니다. ASP.NET은 URL의 세그먼트를 이름으로 작업 메서드에 매핑하며 메서드 이름을 바꾸면 일반적으로 라우팅이 해당 메서드를 찾을 수 없게 됩니다. 해결 방안은 예제에서 볼 수 있는 것처럼, `ActionName("Delete")` 특성을 `DeleteConfirmed` 메서드에 추가하는 것입니다. 해당 특성은 POST 요청에 대한 /Delete/를 포함한 URL이 `DeleteConfirmed` 메서드를 찾도록 라우팅 시스템에 대한 매핑을 수행합니다.

동일한 이름 및 서명을 가진 메서드에 대한 또 다른 일반적인 해결 방법은 별도의 매개 변수(사용되지 않는)를 포함하도록 POST 메서드의 서명을 인위적으로 변경하는 것입니다. 이는 이전 게시에서 `notUsed` 매개 변수를 추가했을 때 수행했던 작업입니다. 여기에서 `[HttpPost] Delete` 메서드에 대해 동일한 작업을 수행할 수 있습니다.

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
