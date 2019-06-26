---
title: ASP.NET Core에서 razor 페이지 단위 테스트
author: guardrex
description: Razor 페이지 앱에 대 한 단위 테스트를 만드는 방법에 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 11/27/2017
uid: test/razor-pages-tests
ms.openlocfilehash: f0e47f975579dc114eaeda375028ec62696f58ed
ms.sourcegitcommit: 763af2cbdab0da62d1f1cfef4bcf787f251dfb5c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/26/2019
ms.locfileid: "67394734"
---
# <a name="razor-pages-unit-tests-in-aspnet-core"></a>ASP.NET Core에서 razor 페이지 단위 테스트

[Luke Latham](https://github.com/guardrex)으로

ASP.NET Core Razor 페이지 앱의 단위 테스트를 지원합니다. 테스트 데이터의 DAL (계층)에 액세스 및 페이지 모델 수 있도록 합니다.

* 앱 생성 하는 동안 요금과 함께 하나의 단위로 작동 하는 Razor 페이지 앱의 일부입니다.
* 클래스 및 메서드 책임의 범위 제한 됩니다.
* 추가 설명서는 앱 동작 방식에 있습니다.
* 재발 된 오류 코드에 대 한 업데이트를 통해 자동화 된 빌드 및 배포 하는 동안 발견 됩니다.

이 항목에서는 Razor 페이지 앱 및 단위 테스트는 기본적인 이해가 있다고 가정 합니다. Razor 페이지 앱 또는 테스트 개념을 잘 모르는 경우 다음 항목을 참조 합니다.

* [Razor 페이지 소개](xref:razor-pages/index)
* [Razor 페이지 시작](xref:tutorials/razor-pages/razor-pages-start)
* [Dotnet test 및 xUnit을 사용 하 여.NET Core에서 C# 테스트 단위](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

샘플 프로젝트는 두 개의 앱으로 구성 됩니다.

| 앱         | 프로젝트 폴더                        | 설명 |
| ----------- | ------------------------------------- | ----------- |
| 메시지 앱 | *src/RazorPagesTestSample*            | 추가, 하나를 삭제, all, 삭제 및 메시지를 분석할 수 있습니다. |
| 테스트 앱    | *tests/RazorPagesTestSample.Tests*    | 메시지 앱 단위 테스트 하는 데 사용 합니다. DAL (데이터 액세스 계층) 및 인덱스 페이지 모델입니다. |

와 같은 기본 제공 테스트에 대 한 기능의 IDE 사용 하 여 테스트를 실행할 수 있습니다 [Visual Studio](https://visualstudio.microsoft.com)합니다. 사용 하는 경우 [Visual Studio Code](https://code.visualstudio.com/) 또는 명령줄에서 명령 프롬프트에서 다음 명령을 실행 합니다 *tests/RazorPagesTestSample.Tests* 폴더:

```console
dotnet test
```

## <a name="message-app-organization"></a>메시지 앱 조직

메시지 앱은 다음 특성을 가진 간단한 Razor 페이지 메시지 시스템:

* 앱의 인덱스 페이지 (*pages/Index.cshtml* 하 고 *Pages/Index.cshtml.cs*) UI 및 페이지 추가, 삭제 및 메시지 (메시지 당 평균 단어) 분석을 제어 하려면 모델 메서드 제공 .
* 메시지에서 설명 합니다 `Message` 클래스 (*Data/Message.cs*) 두 가지 속성을 사용 하 여: `Id` (키) 및 `Text` (메시지). `Text` 속성 필요 하 고 200 자로 제한 됩니다.
* 메시지를 사용 하 여 저장 됩니다 [Entity Framework의 메모리 내 데이터베이스](/ef/core/providers/in-memory/)&#8224;합니다.
* 해당 데이터베이스 컨텍스트 클래스의를 DAL (데이터 액세스 계층)를 포함 하는 앱 `AppDbContext` (*Data/AppDbContext.cs*). DAL 메서드 표시 되는 `virtual`, 테스트에서 사용할 메서드를 모의 수 있습니다.
* 데이터베이스 앱 시작 시 비어 있으면 메시지 저장소는 세 개의 메시지를 사용 하 여 초기화 됩니다. 이러한 *메시지 시드* 테스트에도 사용 됩니다.

&#8224;EF 항목인 [inmemory 테스트](/ef/core/miscellaneous/testing/in-memory), MSTest 사용한 테스트에 대 한 메모리 내 데이터베이스를 사용 하는 방법에 설명 합니다. 이 항목에서는 사용 된 [xUnit](https://xunit.github.io/) 테스트 프레임 워크입니다. 테스트 개념 다른 테스트 프레임 워크에서 구현 테스트와 유사 하지만 동일 하지입니다.

앱 리포지토리 패턴을 사용 하지 않습니다 하 고 효과적인 예가 없습니다 있지만 합니다 [작업 단위 (UoW) 패턴](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor 페이지는 이러한 패턴을 개발을 지원 합니다. 자세한 내용은 [인프라 지 속성 계층 디자인](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) 하 고 [컨트롤러 논리 테스트](/aspnet/core/mvc/controllers/testing) (샘플 리포지토리 패턴을 구현 하는 데 사용).

## <a name="test-app-organization"></a>테스트 앱 구성

테스트 앱 내에서 콘솔 앱은는 *tests/RazorPagesTestSample.Tests* 폴더입니다.

| 테스트 앱 폴더 | 설명 |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs* DAL에 대 한 단위 테스트를 포함 합니다.</li><li>*IndexPageTests.cs* 인덱스 페이지 모델에 대 한 단위 테스트를 포함 합니다.</li></ul> |
| *유틸리티*     | 포함 된 `TestDbContextOptions` 데이터베이스를 각 테스트에 대 한 초기 상태로 다시 설정 됩니다 있도록 새 데이터베이스 각 DAL 단위 테스트에 대 한 상황에 맞는 옵션을 만드는 데 사용 되는 메서드. |

테스트 프레임 워크 [xUnit](https://xunit.github.io/)합니다. 모의 프레임 워크 개체가 [Moq](https://github.com/moq/moq4)합니다.

## <a name="unit-tests-of-the-data-access-layer-dal"></a>단위 테스트 데이터의 DAL (계층)에 액세스

메시지 앱에 포함 된 네 가지 메서드를 사용 하 여는 DAL에는 `AppDbContext` 클래스 (*src/RazorPagesTestSample/Data/AppDbContext.cs*). 각 메서드 테스트 응용 프로그램에 하나 또는 두 개의 단위 테스트

| DAL 메서드               | 함수                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | 가져옵니다를 `List<Message>` 별로 정렬 된 데이터베이스에서의 `Text` 속성입니다. |
| `AddMessageAsync`        | 추가 된 `Message` 데이터베이스에 있습니다.                                          |
| `DeleteAllMessagesAsync` | 모든 삭제 `Message` 데이터베이스에서 항목입니다.                           |
| `DeleteMessageAsync`     | 단일 삭제 `Message` 하 여 데이터베이스에서 `Id`합니다.                      |

DAL의 단위 테스트에 필요한 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 새로 만들 때 `AppDbContext` 각 테스트에 대 한 합니다. 만드는 한 가지 방법은 합니다 `DbContextOptions` 각 테스트를 사용 하는 것을 [DbContextOptionsBuilder](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptionsbuilder):

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

이 방식의 문제는 각 테스트는이 어떤 상태 이전 테스트 왼쪽에서 데이터베이스에 수신 하는 경우 이 서로 간섭 하지 않는 원자성 단위 테스트를 작성 하려고 할 때 문제가 될 수 있습니다. 적용할 합니다 `AppDbContext` 각 테스트에 대 한 새 데이터베이스 컨텍스트를 사용 하려면 제공를 `DbContextOptions` 새 서비스 공급자를 기반으로 하는 인스턴스. 테스트 응용 프로그램을 사용 하는 방법을 보여 줍니다 해당 `Utilities` 클래스 메서드에 `TestDbContextOptions` (*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

사용 하는 `DbContextOptions` DAL 단위 테스트 하면 각 테스트를 새 데이터베이스 인스턴스를 사용 하 여 원자 단위로 실행 합니다.

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

각 테스트 메서드는 `DataAccessLayerTest` 클래스 (*UnitTests/DataAccessLayerTest.cs*) 정렬 Act Assert 비슷한 패턴을 따릅니다.

1. 정렬 합니다. 테스트에 대해 구성 된 데이터베이스 및/또는 예상된 결과 정의
1. Act: 테스트 실행 됩니다.
1. 어설션 됩니다. 테스트 결과 성공 인지 확인 하려면 어설션이 됩니다.

예를 들어 합니다 `DeleteMessageAsync` 로 식별 되는 단일 메시지를 제거 하는 것에 대 한 메서드는 해당 `Id` (*src/RazorPagesTestSample/Data/AppDbContext.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

이 메서드에 대 한 두 테스트가 있습니다. 하나의 테스트 메서드는 메시지는 데이터베이스에 있는 경우 메시지를 삭제를 확인 합니다. 데이터베이스는 경우 변경 되지 않는 다른 메서드 테스트 메시지 `Id` 삭제 존재 하지 않습니다. `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` 메서드는 다음과 같습니다.

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

먼저, 메서드는 작업 단계에 대 한 준비 이루어집니다 정렬 단계를 수행 합니다. 시드 메시지를 얻고에 보관 된 `seedMessages`합니다. 시드 메시지는 데이터베이스에 저장 됩니다. 메시지를 `Id` 의 `1` 삭제에 대해 설정 됩니다. 경우는 `DeleteMessageAsync` 메서드가 실행 되 고, 필요한 메시지의 모든 메시지를 제외 하 고 있어야를 `Id` 의 `1`합니다. `expectedMessages` 변수가 예상된 결과 나타냅니다.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

메서드는 역할: 합니다 `DeleteMessageAsync` 전달 메서드를 실행 합니다 `recId` 의 `1`:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

마지막으로 메서드를 가져옵니다 합니다 `Messages` 컨텍스트에서 비교는 `expectedMessages` 둘이 같으면는 어설션:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

비교를 위해 두 `List<Message>` 동일 합니다.

* 메시지의 기준으로 정렬 된 `Id`합니다.
* 메시지 쌍의 비교는 `Text` 속성입니다.

비슷한 테스트 메서드, `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` 존재 하지 않는 메시지를 삭제 하는 결과 확인 합니다. 이 경우 데이터베이스에서 예상된 메시지 같아야 후 실제 메시지는 `DeleteMessageAsync` 메서드를 실행 합니다. 데이터베이스의 내용이 변경 되지 않았습니다 있어야 합니다.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>페이지 모델 메서드의 단위 테스트

다른 단위 테스트 집합의 페이지 모델 메서드 테스트 담당합니다. 메시지 앱에서 인덱스 페이지 모델에서 발견 되는 `IndexModel` 클래스의 *src/RazorPagesTestSample/Pages/Index.cshtml.cs*합니다.

| 페이지 모델 메서드 | 함수 |
| ----------------- | -------- |
| `OnGetAsync` | 사용 하 여 UI에 대 한 DAL에서 메시지를 가져옵니다는 `GetMessagesAsync` 메서드. |
| `OnPostAddMessageAsync` | 경우는 `ModelState` 유효, 호출 `AddMessageAsync` 데이터베이스로 메시지를 추가 하려면. |
| `OnPostDeleteAllMessagesAsync` | 호출 `DeleteAllMessagesAsync` 모든 데이터베이스에 메시지를 삭제 합니다. |
| `OnPostDeleteMessageAsync` | 실행 `DeleteMessageAsync` 사용 하 여 메시지를 삭제 하는 `Id` 지정 합니다. |
| `OnPostAnalyzeMessagesAsync` | 하나 이상의 메시지를 데이터베이스에 있는 경우 메시지당 단어의 평균을 계산 합니다. |

페이지 모델 메서드 테스트에서 7 개의 테스트를 사용 하 여 `IndexPageTests` 클래스 (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*). 테스트는 친숙 한 정렬 Assert Act 패턴을 사용 합니다. 이러한 테스트에 집중 합니다.

* 메서드 올바른 동작을 수행 하는 경우 결정 때는 `ModelState` 올바르지 않습니다.
* 올바른 생성 메서드 확인 `IActionResult`합니다.
* 속성 값 할당 내용이 확인 합니다.

이 그룹의 테스트에는 종종 페이지 모델 메서드 실행 되는 작업 단계에 대 한 예상 되는 데이터를 생성 하기 위해 DAL의 메서드를 모방 합니다. 예를 들어, 합니다 `GetMessagesAsync` 메서드는 `AppDbContext` 출력을 생성 하기 위해 모의 합니다. 이 메서드를 실행 하는 페이지 모델 메서드를 모의 결과 반환 합니다. 데이터를 데이터베이스에서 가져와야 하지 않습니다. 이 DAL을 사용 하 여 페이지 모델 테스트에서에 대 한 예측 가능 하 고 신뢰할 수 있는 테스트 조건을 만듭니다.

합니다 `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` 테스트 보여 줍니다 방법을 `GetMessagesAsync` 메서드는 페이지 모델에 대 한 모의:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

경우는 `OnGetAsync` 메서드는 작업 단계에서 실행 되 고, 페이지 모델의 호출 `GetMessagesAsync` 메서드.

단위 테스트 작업 단계 (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage` 페이지 모델 `OnGetAsync` 메서드 (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

`GetMessagesAsync` DAL 메서드가이 메서드 호출에 대 한 결과 반환 하지 않습니다. 모의 버전의 메서드 결과 반환합니다.

에 `Assert` 단계에서 실제 메시지 (`actualMessages`)에서 할당 됩니다는 `Messages` 페이지 모델의 속성입니다. 메시지 할당 된 경우에 형식 검사를 수행 됩니다. 예상 및 실제 메시지를 비교 하 여 해당 `Text` 속성입니다. 테스트 임을 어설션 두 `List<Message>` 인스턴스가 동일한 메시지를 포함 합니다.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

이 그룹의 다른 테스트 페이지를 포함 하는 모델 개체 만들기를 `DefaultHttpContext`는 `ModelStateDictionary`, `ActionContext` 설정 하는 `PageContext`, `ViewDataDictionary`, 및 `PageContext`합니다. 이러한 테스트를 수행 하는에 유용 합니다. 메시지 앱을 설정 하는 예를 들어를 `ModelState` 오류로 `AddModelError` 있는지 여부를 확인 하려면 유효한 `PageResult` 이 반환 됩니다 `OnPostAddMessageAsync` 실행:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>추가 자료

* [Dotnet test 및 xUnit을 사용 하 여.NET Core에서 C# 테스트 단위](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* [테스트 컨트롤러](xref:mvc/controllers/testing)
* [코드 단위 테스트](/visualstudio/test/unit-test-your-code) (Visual Studio)
* [통합 테스트](xref:test/integration-tests)
* [xUnit.net](https://xunit.github.io/)
* [XUnit.net (.NET core/asp.net Core)를 사용 하 여 시작](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq](https://github.com/moq/moq4)
* [Moq 빠른 시작](https://github.com/Moq/moq4/wiki/Quickstart)
