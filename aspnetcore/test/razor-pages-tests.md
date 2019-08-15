---
title: ASP.NET Core에서 단위 테스트 Razor Pages
author: guardrex
description: Razor Pages 앱에 대 한 단위 테스트를 만드는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/14/2019
uid: test/razor-pages-tests
ms.openlocfilehash: 35feb5dd95fa79ceca7ff03523cef30d29ccbdd3
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022568"
---
# <a name="razor-pages-unit-tests-in-aspnet-core"></a>ASP.NET Core에서 단위 테스트 Razor Pages

[Luke Latham](https://github.com/guardrex)으로

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core Razor Pages 앱의 단위 테스트를 지원 합니다. DAL (데이터 액세스 계층) 및 페이지 모델의 테스트를 통해 다음을 확인할 수 있습니다.

* Razor Pages 앱의 일부는 앱 생성 중에 독립적으로 또는 하나의 단위로 작동 합니다.
* 클래스 및 메서드의 책임 범위는 제한 되어 있습니다.
* 앱이 작동 하는 방식에 대 한 추가 설명서가 있습니다.
* 코드 업데이트에 의해 발생 하는 오류인 재발은 자동화 된 빌드 및 배포 중에 발견 됩니다.

이 항목에서는 Razor Pages 앱 및 단위 테스트에 대해 기본적으로 이해 하 고 있다고 가정 합니다. Razor Pages 앱 또는 테스트 개념에 익숙하지 않은 경우 다음 항목을 참조 하세요.

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [Dotnet 테스트 C# 및 xunit을 사용 하 여 .net Core에서 유닛 테스트](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

샘플 프로젝트는 다음과 같은 두 개의 앱으로 구성 됩니다.

| 앱         | 프로젝트 폴더                     | 설명 |
| ----------- | ---------------------------------- | ----------- |
| 메시지 앱 | *src/RazorPagesTestSample*         | 사용자가 메시지를 추가 하 고, 메시지 하나를 삭제 하 고, 모든 메시지를 삭제 하 고, 메시지를 분석할 수 있도록 허용 합니다 (메시지당 평균 단어 수를 확인). |
| 응용 프로그램 테스트    | *tests/RazorPagesTestSample.Tests* | 메시지 앱의 DAL 및 인덱스 페이지 모델을 단위 테스트 하는 데 사용 됩니다. |

[Visual Studio](/visualstudio/test/unit-test-your-code) 또는 [MAC용 VISUAL STUDIO](/dotnet/core/tutorials/using-on-mac-vs-full-solution)와 같은 IDE의 기본 제공 테스트 기능을 사용 하 여 테스트를 실행할 수 있습니다. [Visual Studio Code](https://code.visualstudio.com/) 또는 명령줄을 사용 하는 경우 *테스트/RazorPagesTestSample. 테스트* 폴더의 명령 프롬프트에서 다음 명령을 실행 합니다.

```console
dotnet test
```

## <a name="message-app-organization"></a>메시지 앱 구성

메시지 앱은 다음과 같은 특징을 가진 Razor Pages 메시지 시스템입니다.

* 앱의 인덱스 페이지 (*Pages/index. cshtml* 및 *pages/인덱스인*)는 메시지의 추가, 삭제 및 분석을 제어 하는 UI 및 페이지 모델 메서드를 제공 합니다 (메시지당 평균 단어 수 찾기).
* 메시지는 두 속성 `Message` `Id` (키) 및 `Text` (메시지)를 사용 하 여 클래스 (*데이터/메시지 .cs*)에서 설명 합니다. `Text` 속성은 필수 이며 200 자로 제한 됩니다.
* 메시지는 [Entity Framework의 메모리 내 데이터베이스](/ef/core/providers/in-memory/)&#8224;를 사용 하 여 저장 됩니다.
* 앱은 데이터베이스 컨텍스트 클래스 `AppDbContext` (*Data/AppDbContext*)에 DAL을 포함 합니다. DAL 메서드는 테스트에서 `virtual`사용 하기 위한 메서드를 mock 할 수 있도록 표시 됩니다.
* 응용 프로그램이 시작 될 때 데이터베이스가 비어 있는 경우 메시지 저장소는 세 개의 메시지로 초기화 됩니다. 이러한 *시드 된 메시지* 는 테스트에도 사용 됩니다.

&#8224;EF 항목인 [InMemory 테스트](/ef/core/miscellaneous/testing/in-memory)는 MSTest를 사용 하 여 테스트에 메모리 내 데이터베이스를 사용 하는 방법을 설명 합니다. 이 항목에서는 [Xunit](https://xunit.github.io/) 테스트 프레임 워크를 사용 합니다. 여러 테스트 프레임 워크에서 테스트 개념 및 테스트 구현은 비슷하지만 동일 하지는 않습니다.

샘플 앱은 리포지토리 패턴을 사용 하지 않으며 [UoW (Unit Of Work) 패턴](https://martinfowler.com/eaaCatalog/unitOfWork.html)의 효과적인 예는 아니지만 Razor Pages 이러한 개발 패턴을 지원 합니다. 자세한 내용은 [인프라 지 속성 계층 디자인](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) 및 <xref:mvc/controllers/testing> (샘플에서는 리포지토리 패턴 구현)을 참조 하세요.

## <a name="test-app-organization"></a>앱 구성 테스트

테스트 앱은 테스트 */Razorpagestestestsample. 테스트* 폴더 내의 콘솔 앱입니다.

| 테스트 앱 폴더 | Description |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs* 에는 DAL에 대 한 단위 테스트가 포함 되어 있습니다.</li><li>*IndexPageTests.cs* 에는 인덱스 페이지 모델에 대 한 단위 테스트가 포함 되어 있습니다.</li></ul> |
| *유틸리티로*     | 각 DAL `TestDbContextOptions` 단위 테스트에 대 한 새 데이터베이스 컨텍스트 옵션을 만드는 데 사용 되는 메서드를 포함 하 여 각 테스트에 대 한 기준선 조건으로 데이터베이스를 다시 설정 합니다. |

테스트 프레임 워크는 [Xunit](https://xunit.github.io/)입니다. 개체 모의 프레임 워크는 [Moq](https://github.com/moq/moq4)입니다.

## <a name="unit-tests-of-the-data-access-layer-dal"></a>DAL (데이터 액세스 계층)의 단위 테스트

메시지 앱에는 `AppDbContext` 클래스 (*src/razorpagestestsample/Data/AppDbContext*)에 포함 된 네 개의 메서드가 있는 DAL이 있습니다. 각 메서드에는 테스트 앱에 하나 또는 두 개의 단위 테스트가 있습니다.

| DAL 메서드               | 함수                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | 속성`Text` 을 `List<Message>` 기준으로 정렬 된 데이터베이스에서을 가져옵니다. |
| `AddMessageAsync`        | 데이터베이스에 `Message` 를 추가 합니다.                                          |
| `DeleteAllMessagesAsync` | 데이터베이스에서 `Message` 모든 항목을 삭제 합니다.                           |
| `DeleteMessageAsync`     | `Message` 에서`Id`데이터베이스의 단일를 삭제 합니다.                      |

DAL의 단위 테스트는 각 <xref:Microsoft.EntityFrameworkCore.DbContextOptions> 테스트에 대해 새 `AppDbContext` 을 만들 때 필요 합니다. 각 테스트 `DbContextOptions` 에 대해를 만드는 한 가지 방법은를 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>사용 하는 것입니다.

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

이 방법의 문제는 각 테스트에서 이전 테스트를 떠난 모든 상태에서 데이터베이스를 수신 한다는 것입니다. 이는 서로 방해 하지 않는 원자 단위 테스트를 작성 하려고 할 때 문제가 될 수 있습니다. 에서 `AppDbContext` 각 테스트에 대해 새 데이터베이스 컨텍스트를 사용 하도록 하려면 새 서비스 공급자 `DbContextOptions` 를 기반으로 하는 인스턴스를 제공 합니다. 테스트 앱은 해당 `Utilities` 클래스 메서드 `TestDbContextOptions` 를 사용 하 여이 작업을 수행 하는 방법을 보여 줍니다 (테스트/r o*zorpagestestsample. 테스트/유틸리티/유틸리티 .cs*).

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

DAL 단위 `DbContextOptions` 테스트에서를 사용 하면 새 데이터베이스 인스턴스로 각 테스트를 원자 단위로 실행할 수 있습니다.

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

`DataAccessLayerTest` 클래스의 각 테스트 메서드 (단위*테스트/DataAccessLayerTest*)는 유사한 정렬 동작 어설션 패턴을 따릅니다.

1. 정렬 데이터베이스는 테스트를 위해 구성 되 고 예상 된 결과가 정의 됩니다.
1. 역할 테스트가 실행 됩니다.
1. 어설션하 테스트 결과가 성공 인지 여부를 확인 하기 위해 어설션이 생성 됩니다.

예를 들어 메서드 `DeleteMessageAsync` 는 `Id` (*src/razorpagestestsample/Data/AppDbContext*)로 식별 되는 단일 메시지를 제거 해야 합니다.

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

이 메서드에는 두 가지 테스트가 있습니다. 데이터베이스가 데이터베이스에 있는 경우에는 메서드가 메시지를 삭제 하는 테스트를 확인 합니다. 다른 메서드는 삭제에 대 한 메시지가 `Id` 존재 하지 않는 경우 데이터베이스가 변경 되지 않는지 테스트 합니다. 메서드 `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` 는 다음과 같습니다.

[!code-csharp[](razor-pages-tests/samples_snapshot/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

첫째,이 메서드는 Act 단계가 준비 되는 정렬 단계를 수행 합니다. 시드 메시지는에서 `seedMessages`가져오고 보유 합니다. 시드 메시지는 데이터베이스에 저장 됩니다. 가 인 `Id` `1` 메시지는 삭제를 위해 설정 됩니다. 메서드가 실행 될 때 예상 되는 메시지에는의 `1`가 `Id` 있는 메시지를 제외한 모든 메시지가 있어야 합니다. `DeleteMessageAsync` 변수 `expectedMessages` 는이 예상 결과를 나타냅니다.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

메서드는 다음과 같이 동작 합니다. 메서드 `DeleteMessageAsync` 는 `recId` 의`1`를 전달 하 여 실행 됩니다.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

마지막으로, 메서드는 컨텍스트에서 `Messages` 를 가져온 다음 두 항목이 같은지를 어설션 `expectedMessages` 하는 것과 비교 합니다.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

두 가지 `List<Message>` 를 비교 하기 위해 다음과 같이 합니다.

* 메시지는를 기준으로 `Id`정렬 됩니다.
* 메시지 쌍은 `Text` 속성에서 비교 됩니다.

유사한 테스트 메서드 `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` 는 존재 하지 않는 메시지의 삭제를 시도한 결과를 확인 합니다. 이 경우 데이터베이스에서 예상 되는 메시지는 `DeleteMessageAsync` 메서드가 실행 된 후의 실제 메시지와 같아야 합니다. 데이터베이스의 내용은 다음과 같이 변경 되지 않아야 합니다.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>페이지 모델 메서드의 단위 테스트

다른 단위 테스트 집합은 페이지 모델 메서드의 테스트를 담당 합니다. 메시지 앱에서 인덱스 페이지 모델은 `IndexModel` *src/razorpagestestsample/Pages/Index. cshtml*의 클래스에서 찾을 수 있습니다.

| 페이지 모델 메서드 | 함수 |
| ----------------- | -------- |
| `OnGetAsync` | 메서드를 `GetMessagesAsync` 사용 하 여 UI에 대 한 DAL에서 메시지를 가져옵니다. |
| `OnPostAddMessageAsync` | [Modelstate](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) 가 올바르면를 호출 `AddMessageAsync` 하 여 데이터베이스에 메시지를 추가 합니다. |
| `OnPostDeleteAllMessagesAsync` | 을 `DeleteAllMessagesAsync` 호출 하 여 데이터베이스의 모든 메시지를 삭제 합니다. |
| `OnPostDeleteMessageAsync` | `Id` 지정 `DeleteMessageAsync` 된을 사용 하 여 메시지를 삭제 하려면를 실행 합니다. |
| `OnPostAnalyzeMessagesAsync` | 하나 이상의 메시지가 데이터베이스에 있으면는 메시지당 평균 단어 수를 계산 합니다. |

페이지 모델 메서드는 `IndexPageTests` 클래스의 7 가지 테스트를 사용 하 여 테스트 됩니다 (테스트/t o*zorpagestestsample. 테스트*/단위 테스트/IndexPageTests). 테스트는 친숙 한 정렬 동작 패턴을 사용 합니다. 이러한 테스트는 다음에 중점을 둡니다.

* [Modelstate](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) 가 올바르지 않은 경우 메서드가 올바른 동작을 따르는지 확인 합니다.
* 메서드가 올바른 <xref:Microsoft.AspNetCore.Mvc.IActionResult>를 생성 하는 것을 확인 합니다.
* 속성 값 할당이 올바르게 수행 되었는지 확인 하는 중입니다.

이 테스트 그룹은 종종 DAL의 메서드를 통해 페이지 모델 메서드가 실행 되는 Act 단계에 필요한 데이터를 생성 합니다. 예를 들어 `GetMessagesAsync` `AppDbContext` 의 메서드는 출력을 생성 하는 모의입니다. 페이지 모델 메서드에서이 메서드를 실행 하면 모의 결과가 반환 됩니다. 데이터베이스에서 데이터를 가져오지 않습니다. 이렇게 하면 페이지 모델 테스트에서 DAL을 사용 하기 위한 예측 가능 하 고 신뢰할 수 있는 테스트 조건이 생성 됩니다.

이 `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` 테스트는 `GetMessagesAsync` 메서드가 페이지 모델에 대해 모의 방법을 보여 줍니다.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

메서드가 Act 단계에서 실행 되 면 페이지 모델의 `GetMessagesAsync` 메서드를 호출 합니다. `OnGetAsync`

단위 테스트 작업 단계 (*테스트/RazorPagesTestSample. 테스트/단위 테스트/IndexPageTests*):

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage`페이지 모델의 `OnGetAsync` 메서드 (*src/razorpagestestsample/Pages/Index. cshtml*):

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

DAL `GetMessagesAsync` 의 메서드는이 메서드 호출에 대 한 결과를 반환 하지 않습니다. 메서드의 모의 버전은 결과를 반환 합니다.

이 단계에서 실제 메시지 (`actualMessages` `Messages` )는 페이지 모델의 속성에서 할당 됩니다. `Assert` 메시지를 할당 하는 경우에도 유형 검사가 수행 됩니다. 예상 및 실제 메시지는 해당 `Text` 속성과 비교 됩니다. 테스트에서는 두 `List<Message>` 인스턴스에 동일한 메시지가 포함 되어 있음을 어설션 합니다.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

이 그룹의 다른 테스트에서는, 및 <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>를 설정 <xref:Microsoft.AspNetCore.Mvc.ActionContext> `ViewDataDictionary` <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> `PageContext`하는,, 및를 포함 하는 페이지 모델 개체를 만듭니다 `PageContext`. 이는 테스트를 수행 하는 데 유용 합니다. 예를 들어 메시지 앱은가 실행 `ModelState` 될 때 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> `OnPostAddMessageAsync` 유효한 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> 가 반환 되는지 확인 하기 위해 오류를 설정 합니다.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>추가 자료

* [Dotnet 테스트 C# 및 xunit을 사용 하 여 .net Core에서 유닛 테스트](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [코드 단위 테스트](/visualstudio/test/unit-test-your-code) (Visual Studio)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [Visual Studio for Mac을 사용하여 macOS에서 완전한 .NET Core 솔루션 빌드](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [XUnit.net 시작 하기: .Net SDK 명령줄을 사용 하 여 .NET Core 사용](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq](https://github.com/moq/moq4)
* [Moq 빠른 시작](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core Razor Pages 앱의 단위 테스트를 지원 합니다. DAL (데이터 액세스 계층) 및 페이지 모델의 테스트를 통해 다음을 확인할 수 있습니다.

* Razor Pages 앱의 일부는 앱 생성 중에 독립적으로 또는 하나의 단위로 작동 합니다.
* 클래스 및 메서드의 책임 범위는 제한 되어 있습니다.
* 앱이 작동 하는 방식에 대 한 추가 설명서가 있습니다.
* 코드 업데이트에 의해 발생 하는 오류인 재발은 자동화 된 빌드 및 배포 중에 발견 됩니다.

이 항목에서는 Razor Pages 앱 및 단위 테스트에 대해 기본적으로 이해 하 고 있다고 가정 합니다. Razor Pages 앱 또는 테스트 개념에 익숙하지 않은 경우 다음 항목을 참조 하세요.

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [Dotnet 테스트 C# 및 xunit을 사용 하 여 .net Core에서 유닛 테스트](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

샘플 프로젝트는 다음과 같은 두 개의 앱으로 구성 됩니다.

| 앱         | 프로젝트 폴더                     | Description |
| ----------- | ---------------------------------- | ----------- |
| 메시지 앱 | *src/RazorPagesTestSample*         | 사용자가 메시지를 추가 하 고, 메시지 하나를 삭제 하 고, 모든 메시지를 삭제 하 고, 메시지를 분석할 수 있도록 허용 합니다 (메시지당 평균 단어 수를 확인). |
| 응용 프로그램 테스트    | *tests/RazorPagesTestSample.Tests* | 메시지 앱의 DAL 및 인덱스 페이지 모델을 단위 테스트 하는 데 사용 됩니다. |

[Visual Studio](/visualstudio/test/unit-test-your-code) 또는 [MAC용 VISUAL STUDIO](/dotnet/core/tutorials/using-on-mac-vs-full-solution)와 같은 IDE의 기본 제공 테스트 기능을 사용 하 여 테스트를 실행할 수 있습니다. [Visual Studio Code](https://code.visualstudio.com/) 또는 명령줄을 사용 하는 경우 *테스트/RazorPagesTestSample. 테스트* 폴더의 명령 프롬프트에서 다음 명령을 실행 합니다.

```console
dotnet test
```

## <a name="message-app-organization"></a>메시지 앱 구성

메시지 앱은 다음과 같은 특징을 가진 Razor Pages 메시지 시스템입니다.

* 앱의 인덱스 페이지 (*Pages/index. cshtml* 및 *pages/인덱스인*)는 메시지의 추가, 삭제 및 분석을 제어 하는 UI 및 페이지 모델 메서드를 제공 합니다 (메시지당 평균 단어 수 찾기).
* 메시지는 두 속성 `Message` `Id` (키) 및 `Text` (메시지)를 사용 하 여 클래스 (*데이터/메시지 .cs*)에서 설명 합니다. `Text` 속성은 필수 이며 200 자로 제한 됩니다.
* 메시지는 [Entity Framework의 메모리 내 데이터베이스](/ef/core/providers/in-memory/)&#8224;를 사용 하 여 저장 됩니다.
* 앱은 데이터베이스 컨텍스트 클래스 `AppDbContext` (*Data/AppDbContext*)에 DAL을 포함 합니다. DAL 메서드는 테스트에서 `virtual`사용 하기 위한 메서드를 mock 할 수 있도록 표시 됩니다.
* 응용 프로그램이 시작 될 때 데이터베이스가 비어 있는 경우 메시지 저장소는 세 개의 메시지로 초기화 됩니다. 이러한 *시드 된 메시지* 는 테스트에도 사용 됩니다.

&#8224;EF 항목인 [InMemory 테스트](/ef/core/miscellaneous/testing/in-memory)는 MSTest를 사용 하 여 테스트에 메모리 내 데이터베이스를 사용 하는 방법을 설명 합니다. 이 항목에서는 [Xunit](https://xunit.github.io/) 테스트 프레임 워크를 사용 합니다. 여러 테스트 프레임 워크에서 테스트 개념 및 테스트 구현은 비슷하지만 동일 하지는 않습니다.

샘플 앱은 리포지토리 패턴을 사용 하지 않으며 [UoW (Unit Of Work) 패턴](https://martinfowler.com/eaaCatalog/unitOfWork.html)의 효과적인 예는 아니지만 Razor Pages 이러한 개발 패턴을 지원 합니다. 자세한 내용은 [인프라 지 속성 계층 디자인](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) 및 <xref:mvc/controllers/testing> (샘플에서는 리포지토리 패턴 구현)을 참조 하세요.

## <a name="test-app-organization"></a>앱 구성 테스트

테스트 앱은 테스트 */Razorpagestestestsample. 테스트* 폴더 내의 콘솔 앱입니다.

| 테스트 앱 폴더 | 설명 |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs* 에는 DAL에 대 한 단위 테스트가 포함 되어 있습니다.</li><li>*IndexPageTests.cs* 에는 인덱스 페이지 모델에 대 한 단위 테스트가 포함 되어 있습니다.</li></ul> |
| *유틸리티로*     | 각 DAL `TestDbContextOptions` 단위 테스트에 대 한 새 데이터베이스 컨텍스트 옵션을 만드는 데 사용 되는 메서드를 포함 하 여 각 테스트에 대 한 기준선 조건으로 데이터베이스를 다시 설정 합니다. |

테스트 프레임 워크는 [Xunit](https://xunit.github.io/)입니다. 개체 모의 프레임 워크는 [Moq](https://github.com/moq/moq4)입니다.

## <a name="unit-tests-of-the-data-access-layer-dal"></a>DAL (데이터 액세스 계층)의 단위 테스트

메시지 앱에는 `AppDbContext` 클래스 (*src/razorpagestestsample/Data/AppDbContext*)에 포함 된 네 개의 메서드가 있는 DAL이 있습니다. 각 메서드에는 테스트 앱에 하나 또는 두 개의 단위 테스트가 있습니다.

| DAL 메서드               | 함수                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | 속성`Text` 을 `List<Message>` 기준으로 정렬 된 데이터베이스에서을 가져옵니다. |
| `AddMessageAsync`        | 데이터베이스에 `Message` 를 추가 합니다.                                          |
| `DeleteAllMessagesAsync` | 데이터베이스에서 `Message` 모든 항목을 삭제 합니다.                           |
| `DeleteMessageAsync`     | `Message` 에서`Id`데이터베이스의 단일를 삭제 합니다.                      |

DAL의 단위 테스트는 각 <xref:Microsoft.EntityFrameworkCore.DbContextOptions> 테스트에 대해 새 `AppDbContext` 을 만들 때 필요 합니다. 각 테스트 `DbContextOptions` 에 대해를 만드는 한 가지 방법은를 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>사용 하는 것입니다.

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

이 방법의 문제는 각 테스트에서 이전 테스트를 떠난 모든 상태에서 데이터베이스를 수신 한다는 것입니다. 이는 서로 방해 하지 않는 원자 단위 테스트를 작성 하려고 할 때 문제가 될 수 있습니다. 에서 `AppDbContext` 각 테스트에 대해 새 데이터베이스 컨텍스트를 사용 하도록 하려면 새 서비스 공급자 `DbContextOptions` 를 기반으로 하는 인스턴스를 제공 합니다. 테스트 앱은 해당 `Utilities` 클래스 메서드 `TestDbContextOptions` 를 사용 하 여이 작업을 수행 하는 방법을 보여 줍니다 (테스트/r o*zorpagestestsample. 테스트/유틸리티/유틸리티 .cs*).

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

DAL 단위 `DbContextOptions` 테스트에서를 사용 하면 새 데이터베이스 인스턴스로 각 테스트를 원자 단위로 실행할 수 있습니다.

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

`DataAccessLayerTest` 클래스의 각 테스트 메서드 (단위*테스트/DataAccessLayerTest*)는 유사한 정렬 동작 어설션 패턴을 따릅니다.

1. 정렬 데이터베이스는 테스트를 위해 구성 되 고 예상 된 결과가 정의 됩니다.
1. 역할 테스트가 실행 됩니다.
1. 어설션하 테스트 결과가 성공 인지 여부를 확인 하기 위해 어설션이 생성 됩니다.

예를 들어 메서드 `DeleteMessageAsync` 는 `Id` (*src/razorpagestestsample/Data/AppDbContext*)로 식별 되는 단일 메시지를 제거 해야 합니다.

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

이 메서드에는 두 가지 테스트가 있습니다. 데이터베이스가 데이터베이스에 있는 경우에는 메서드가 메시지를 삭제 하는 테스트를 확인 합니다. 다른 메서드는 삭제에 대 한 메시지가 `Id` 존재 하지 않는 경우 데이터베이스가 변경 되지 않는지 테스트 합니다. 메서드 `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` 는 다음과 같습니다.

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

첫째,이 메서드는 Act 단계가 준비 되는 정렬 단계를 수행 합니다. 시드 메시지는에서 `seedMessages`가져오고 보유 합니다. 시드 메시지는 데이터베이스에 저장 됩니다. 가 인 `Id` `1` 메시지는 삭제를 위해 설정 됩니다. 메서드가 실행 될 때 예상 되는 메시지에는의 `1`가 `Id` 있는 메시지를 제외한 모든 메시지가 있어야 합니다. `DeleteMessageAsync` 변수 `expectedMessages` 는이 예상 결과를 나타냅니다.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

메서드는 다음과 같이 동작 합니다. 메서드 `DeleteMessageAsync` 는 `recId` 의`1`를 전달 하 여 실행 됩니다.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

마지막으로, 메서드는 컨텍스트에서 `Messages` 를 가져온 다음 두 항목이 같은지를 어설션 `expectedMessages` 하는 것과 비교 합니다.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

두 가지 `List<Message>` 를 비교 하기 위해 다음과 같이 합니다.

* 메시지는를 기준으로 `Id`정렬 됩니다.
* 메시지 쌍은 `Text` 속성에서 비교 됩니다.

유사한 테스트 메서드 `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` 는 존재 하지 않는 메시지의 삭제를 시도한 결과를 확인 합니다. 이 경우 데이터베이스에서 예상 되는 메시지는 `DeleteMessageAsync` 메서드가 실행 된 후의 실제 메시지와 같아야 합니다. 데이터베이스의 내용은 다음과 같이 변경 되지 않아야 합니다.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>페이지 모델 메서드의 단위 테스트

다른 단위 테스트 집합은 페이지 모델 메서드의 테스트를 담당 합니다. 메시지 앱에서 인덱스 페이지 모델은 `IndexModel` *src/razorpagestestsample/Pages/Index. cshtml*의 클래스에서 찾을 수 있습니다.

| 페이지 모델 메서드 | 함수 |
| ----------------- | -------- |
| `OnGetAsync` | 메서드를 `GetMessagesAsync` 사용 하 여 UI에 대 한 DAL에서 메시지를 가져옵니다. |
| `OnPostAddMessageAsync` | [Modelstate](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) 가 올바르면를 호출 `AddMessageAsync` 하 여 데이터베이스에 메시지를 추가 합니다. |
| `OnPostDeleteAllMessagesAsync` | 을 `DeleteAllMessagesAsync` 호출 하 여 데이터베이스의 모든 메시지를 삭제 합니다. |
| `OnPostDeleteMessageAsync` | `Id` 지정 `DeleteMessageAsync` 된을 사용 하 여 메시지를 삭제 하려면를 실행 합니다. |
| `OnPostAnalyzeMessagesAsync` | 하나 이상의 메시지가 데이터베이스에 있으면는 메시지당 평균 단어 수를 계산 합니다. |

페이지 모델 메서드는 `IndexPageTests` 클래스의 7 가지 테스트를 사용 하 여 테스트 됩니다 (테스트/t o*zorpagestestsample. 테스트*/단위 테스트/IndexPageTests). 테스트는 친숙 한 정렬 동작 패턴을 사용 합니다. 이러한 테스트는 다음에 중점을 둡니다.

* [Modelstate](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) 가 올바르지 않은 경우 메서드가 올바른 동작을 따르는지 확인 합니다.
* 메서드가 올바른 <xref:Microsoft.AspNetCore.Mvc.IActionResult>를 생성 하는 것을 확인 합니다.
* 속성 값 할당이 올바르게 수행 되었는지 확인 하는 중입니다.

이 테스트 그룹은 종종 DAL의 메서드를 통해 페이지 모델 메서드가 실행 되는 Act 단계에 필요한 데이터를 생성 합니다. 예를 들어 `GetMessagesAsync` `AppDbContext` 의 메서드는 출력을 생성 하는 모의입니다. 페이지 모델 메서드에서이 메서드를 실행 하면 모의 결과가 반환 됩니다. 데이터베이스에서 데이터를 가져오지 않습니다. 이렇게 하면 페이지 모델 테스트에서 DAL을 사용 하기 위한 예측 가능 하 고 신뢰할 수 있는 테스트 조건이 생성 됩니다.

이 `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` 테스트는 `GetMessagesAsync` 메서드가 페이지 모델에 대해 모의 방법을 보여 줍니다.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

메서드가 Act 단계에서 실행 되 면 페이지 모델의 `GetMessagesAsync` 메서드를 호출 합니다. `OnGetAsync`

단위 테스트 작업 단계 (*테스트/RazorPagesTestSample. 테스트/단위 테스트/IndexPageTests*):

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage`페이지 모델의 `OnGetAsync` 메서드 (*src/razorpagestestsample/Pages/Index. cshtml*):

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

DAL `GetMessagesAsync` 의 메서드는이 메서드 호출에 대 한 결과를 반환 하지 않습니다. 메서드의 모의 버전은 결과를 반환 합니다.

이 단계에서 실제 메시지 (`actualMessages` `Messages` )는 페이지 모델의 속성에서 할당 됩니다. `Assert` 메시지를 할당 하는 경우에도 유형 검사가 수행 됩니다. 예상 및 실제 메시지는 해당 `Text` 속성과 비교 됩니다. 테스트에서는 두 `List<Message>` 인스턴스에 동일한 메시지가 포함 되어 있음을 어설션 합니다.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

이 그룹의 다른 테스트에서는, 및 <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>를 설정 <xref:Microsoft.AspNetCore.Mvc.ActionContext> `ViewDataDictionary` <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> `PageContext`하는,, 및를 포함 하는 페이지 모델 개체를 만듭니다 `PageContext`. 이는 테스트를 수행 하는 데 유용 합니다. 예를 들어 메시지 앱은가 실행 `ModelState` 될 때 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> `OnPostAddMessageAsync` 유효한 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> 가 반환 되는지 확인 하기 위해 오류를 설정 합니다.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>추가 자료

* [Dotnet 테스트 C# 및 xunit을 사용 하 여 .net Core에서 유닛 테스트](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* [코드 단위 테스트](/visualstudio/test/unit-test-your-code) (Visual Studio)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [Visual Studio for Mac을 사용하여 macOS에서 완전한 .NET Core 솔루션 빌드](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [XUnit.net 시작 하기: .Net SDK 명령줄을 사용 하 여 .NET Core 사용](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq](https://github.com/moq/moq4)
* [Moq 빠른 시작](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end
