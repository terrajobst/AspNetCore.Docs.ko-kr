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
# <a name="razor-pages-unit-tests-in-aspnet-core"></a><span data-ttu-id="fb866-103">ASP.NET Core에서 단위 테스트 Razor Pages</span><span class="sxs-lookup"><span data-stu-id="fb866-103">Razor Pages unit tests in ASP.NET Core</span></span>

<span data-ttu-id="fb866-104">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="fb866-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fb866-105">ASP.NET Core Razor Pages 앱의 단위 테스트를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-105">ASP.NET Core supports unit tests of Razor Pages apps.</span></span> <span data-ttu-id="fb866-106">DAL (데이터 액세스 계층) 및 페이지 모델의 테스트를 통해 다음을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-106">Tests of the data access layer (DAL) and page models help ensure:</span></span>

* <span data-ttu-id="fb866-107">Razor Pages 앱의 일부는 앱 생성 중에 독립적으로 또는 하나의 단위로 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-107">Parts of a Razor Pages app work independently and together as a unit during app construction.</span></span>
* <span data-ttu-id="fb866-108">클래스 및 메서드의 책임 범위는 제한 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-108">Classes and methods have limited scopes of responsibility.</span></span>
* <span data-ttu-id="fb866-109">앱이 작동 하는 방식에 대 한 추가 설명서가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-109">Additional documentation exists on how the app should behave.</span></span>
* <span data-ttu-id="fb866-110">코드 업데이트에 의해 발생 하는 오류인 재발은 자동화 된 빌드 및 배포 중에 발견 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-110">Regressions, which are errors brought about by updates to the code, are found during automated building and deployment.</span></span>

<span data-ttu-id="fb866-111">이 항목에서는 Razor Pages 앱 및 단위 테스트에 대해 기본적으로 이해 하 고 있다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-111">This topic assumes that you have a basic understanding of Razor Pages apps and unit tests.</span></span> <span data-ttu-id="fb866-112">Razor Pages 앱 또는 테스트 개념에 익숙하지 않은 경우 다음 항목을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="fb866-112">If you're unfamiliar with Razor Pages apps or test concepts, see the following topics:</span></span>

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [<span data-ttu-id="fb866-113">Dotnet 테스트 C# 및 xunit을 사용 하 여 .net Core에서 유닛 테스트</span><span class="sxs-lookup"><span data-stu-id="fb866-113">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

<span data-ttu-id="fb866-114">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fb866-114">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="fb866-115">샘플 프로젝트는 다음과 같은 두 개의 앱으로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-115">The sample project is composed of two apps:</span></span>

| <span data-ttu-id="fb866-116">앱</span><span class="sxs-lookup"><span data-stu-id="fb866-116">App</span></span>         | <span data-ttu-id="fb866-117">프로젝트 폴더</span><span class="sxs-lookup"><span data-stu-id="fb866-117">Project folder</span></span>                     | <span data-ttu-id="fb866-118">설명</span><span class="sxs-lookup"><span data-stu-id="fb866-118">Description</span></span> |
| ----------- | ---------------------------------- | ----------- |
| <span data-ttu-id="fb866-119">메시지 앱</span><span class="sxs-lookup"><span data-stu-id="fb866-119">Message app</span></span> | <span data-ttu-id="fb866-120">*src/RazorPagesTestSample*</span><span class="sxs-lookup"><span data-stu-id="fb866-120">*src/RazorPagesTestSample*</span></span>         | <span data-ttu-id="fb866-121">사용자가 메시지를 추가 하 고, 메시지 하나를 삭제 하 고, 모든 메시지를 삭제 하 고, 메시지를 분석할 수 있도록 허용 합니다 (메시지당 평균 단어 수를 확인).</span><span class="sxs-lookup"><span data-stu-id="fb866-121">Allows a user to add a message, delete one message, delete all messages, and analyze messages (find the average number of words per message).</span></span> |
| <span data-ttu-id="fb866-122">응용 프로그램 테스트</span><span class="sxs-lookup"><span data-stu-id="fb866-122">Test app</span></span>    | <span data-ttu-id="fb866-123">*tests/RazorPagesTestSample.Tests*</span><span class="sxs-lookup"><span data-stu-id="fb866-123">*tests/RazorPagesTestSample.Tests*</span></span> | <span data-ttu-id="fb866-124">메시지 앱의 DAL 및 인덱스 페이지 모델을 단위 테스트 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-124">Used to unit test the DAL and Index page model of the message app.</span></span> |

<span data-ttu-id="fb866-125">[Visual Studio](/visualstudio/test/unit-test-your-code) 또는 [MAC용 VISUAL STUDIO](/dotnet/core/tutorials/using-on-mac-vs-full-solution)와 같은 IDE의 기본 제공 테스트 기능을 사용 하 여 테스트를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-125">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](/visualstudio/test/unit-test-your-code) or [Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution).</span></span> <span data-ttu-id="fb866-126">[Visual Studio Code](https://code.visualstudio.com/) 또는 명령줄을 사용 하는 경우 *테스트/RazorPagesTestSample. 테스트* 폴더의 명령 프롬프트에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-126">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesTestSample.Tests* folder:</span></span>

```console
dotnet test
```

## <a name="message-app-organization"></a><span data-ttu-id="fb866-127">메시지 앱 구성</span><span class="sxs-lookup"><span data-stu-id="fb866-127">Message app organization</span></span>

<span data-ttu-id="fb866-128">메시지 앱은 다음과 같은 특징을 가진 Razor Pages 메시지 시스템입니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-128">The message app is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="fb866-129">앱의 인덱스 페이지 (*Pages/index. cshtml* 및 *pages/인덱스인*)는 메시지의 추가, 삭제 및 분석을 제어 하는 UI 및 페이지 모델 메서드를 제공 합니다 (메시지당 평균 단어 수 찾기).</span><span class="sxs-lookup"><span data-stu-id="fb866-129">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (find the average number of words per message).</span></span>
* <span data-ttu-id="fb866-130">메시지는 두 속성 `Message` `Id` (키) 및 `Text` (메시지)를 사용 하 여 클래스 (*데이터/메시지 .cs*)에서 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-130">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="fb866-131">`Text` 속성은 필수 이며 200 자로 제한 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-131">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="fb866-132">메시지는 [Entity Framework의 메모리 내 데이터베이스](/ef/core/providers/in-memory/)&#8224;를 사용 하 여 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-132">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="fb866-133">앱은 데이터베이스 컨텍스트 클래스 `AppDbContext` (*Data/AppDbContext*)에 DAL을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-133">The app contains a DAL in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span> <span data-ttu-id="fb866-134">DAL 메서드는 테스트에서 `virtual`사용 하기 위한 메서드를 mock 할 수 있도록 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-134">The DAL methods are marked `virtual`, which allows mocking the methods for use in the tests.</span></span>
* <span data-ttu-id="fb866-135">응용 프로그램이 시작 될 때 데이터베이스가 비어 있는 경우 메시지 저장소는 세 개의 메시지로 초기화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-135">If the database is empty on app startup, the message store is initialized with three messages.</span></span> <span data-ttu-id="fb866-136">이러한 *시드 된 메시지* 는 테스트에도 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-136">These *seeded messages* are also used in tests.</span></span>

<span data-ttu-id="fb866-137">&#8224;EF 항목인 [InMemory 테스트](/ef/core/miscellaneous/testing/in-memory)는 MSTest를 사용 하 여 테스트에 메모리 내 데이터베이스를 사용 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-137">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="fb866-138">이 항목에서는 [Xunit](https://xunit.github.io/) 테스트 프레임 워크를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-138">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="fb866-139">여러 테스트 프레임 워크에서 테스트 개념 및 테스트 구현은 비슷하지만 동일 하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-139">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="fb866-140">샘플 앱은 리포지토리 패턴을 사용 하지 않으며 [UoW (Unit Of Work) 패턴](https://martinfowler.com/eaaCatalog/unitOfWork.html)의 효과적인 예는 아니지만 Razor Pages 이러한 개발 패턴을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-140">Although the sample app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="fb866-141">자세한 내용은 [인프라 지 속성 계층 디자인](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) 및 <xref:mvc/controllers/testing> (샘플에서는 리포지토리 패턴 구현)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="fb866-141">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and <xref:mvc/controllers/testing> (the sample implements the repository pattern).</span></span>

## <a name="test-app-organization"></a><span data-ttu-id="fb866-142">앱 구성 테스트</span><span class="sxs-lookup"><span data-stu-id="fb866-142">Test app organization</span></span>

<span data-ttu-id="fb866-143">테스트 앱은 테스트 */Razorpagestestestsample. 테스트* 폴더 내의 콘솔 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-143">The test app is a console app inside the *tests/RazorPagesTestSample.Tests* folder.</span></span>

| <span data-ttu-id="fb866-144">테스트 앱 폴더</span><span class="sxs-lookup"><span data-stu-id="fb866-144">Test app folder</span></span> | <span data-ttu-id="fb866-145">Description</span><span class="sxs-lookup"><span data-stu-id="fb866-145">Description</span></span> |
| --------------- | ----------- |
| <span data-ttu-id="fb866-146">*UnitTests*</span><span class="sxs-lookup"><span data-stu-id="fb866-146">*UnitTests*</span></span>     | <ul><li><span data-ttu-id="fb866-147">*DataAccessLayerTest.cs* 에는 DAL에 대 한 단위 테스트가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-147">*DataAccessLayerTest.cs* contains the unit tests for the DAL.</span></span></li><li><span data-ttu-id="fb866-148">*IndexPageTests.cs* 에는 인덱스 페이지 모델에 대 한 단위 테스트가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-148">*IndexPageTests.cs* contains the unit tests for the Index page model.</span></span></li></ul> |
| <span data-ttu-id="fb866-149">*유틸리티로*</span><span class="sxs-lookup"><span data-stu-id="fb866-149">*Utilities*</span></span>     | <span data-ttu-id="fb866-150">각 DAL `TestDbContextOptions` 단위 테스트에 대 한 새 데이터베이스 컨텍스트 옵션을 만드는 데 사용 되는 메서드를 포함 하 여 각 테스트에 대 한 기준선 조건으로 데이터베이스를 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-150">Contains the `TestDbContextOptions` method used to create new database context options for each DAL unit test so that the database is reset to its baseline condition for each test.</span></span> |

<span data-ttu-id="fb866-151">테스트 프레임 워크는 [Xunit](https://xunit.github.io/)입니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-151">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="fb866-152">개체 모의 프레임 워크는 [Moq](https://github.com/moq/moq4)입니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-152">The object mocking framework is [Moq](https://github.com/moq/moq4).</span></span>

## <a name="unit-tests-of-the-data-access-layer-dal"></a><span data-ttu-id="fb866-153">DAL (데이터 액세스 계층)의 단위 테스트</span><span class="sxs-lookup"><span data-stu-id="fb866-153">Unit tests of the data access layer (DAL)</span></span>

<span data-ttu-id="fb866-154">메시지 앱에는 `AppDbContext` 클래스 (*src/razorpagestestsample/Data/AppDbContext*)에 포함 된 네 개의 메서드가 있는 DAL이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-154">The message app has a DAL with four methods contained in the `AppDbContext` class (*src/RazorPagesTestSample/Data/AppDbContext.cs*).</span></span> <span data-ttu-id="fb866-155">각 메서드에는 테스트 앱에 하나 또는 두 개의 단위 테스트가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-155">Each method has one or two unit tests in the test app.</span></span>

| <span data-ttu-id="fb866-156">DAL 메서드</span><span class="sxs-lookup"><span data-stu-id="fb866-156">DAL method</span></span>               | <span data-ttu-id="fb866-157">함수</span><span class="sxs-lookup"><span data-stu-id="fb866-157">Function</span></span>                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | <span data-ttu-id="fb866-158">속성`Text` 을 `List<Message>` 기준으로 정렬 된 데이터베이스에서을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-158">Obtains a `List<Message>` from the database sorted by the `Text` property.</span></span> |
| `AddMessageAsync`        | <span data-ttu-id="fb866-159">데이터베이스에 `Message` 를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-159">Adds a `Message` to the database.</span></span>                                          |
| `DeleteAllMessagesAsync` | <span data-ttu-id="fb866-160">데이터베이스에서 `Message` 모든 항목을 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-160">Deletes all `Message` entries from the database.</span></span>                           |
| `DeleteMessageAsync`     | <span data-ttu-id="fb866-161">`Message` 에서`Id`데이터베이스의 단일를 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-161">Deletes a single `Message` from the database by `Id`.</span></span>                      |

<span data-ttu-id="fb866-162">DAL의 단위 테스트는 각 <xref:Microsoft.EntityFrameworkCore.DbContextOptions> 테스트에 대해 새 `AppDbContext` 을 만들 때 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-162">Unit tests of the DAL require <xref:Microsoft.EntityFrameworkCore.DbContextOptions> when creating a new `AppDbContext` for each test.</span></span> <span data-ttu-id="fb866-163">각 테스트 `DbContextOptions` 에 대해를 만드는 한 가지 방법은를 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>사용 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-163">One approach to creating the `DbContextOptions` for each test is to use a <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>:</span></span>

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="fb866-164">이 방법의 문제는 각 테스트에서 이전 테스트를 떠난 모든 상태에서 데이터베이스를 수신 한다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-164">The problem with this approach is that each test receives the database in whatever state the previous test left it.</span></span> <span data-ttu-id="fb866-165">이는 서로 방해 하지 않는 원자 단위 테스트를 작성 하려고 할 때 문제가 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-165">This can be problematic when trying to write atomic unit tests that don't interfere with each other.</span></span> <span data-ttu-id="fb866-166">에서 `AppDbContext` 각 테스트에 대해 새 데이터베이스 컨텍스트를 사용 하도록 하려면 새 서비스 공급자 `DbContextOptions` 를 기반으로 하는 인스턴스를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-166">To force the `AppDbContext` to use a new database context for each test, supply a `DbContextOptions` instance that's based on a new service provider.</span></span> <span data-ttu-id="fb866-167">테스트 앱은 해당 `Utilities` 클래스 메서드 `TestDbContextOptions` 를 사용 하 여이 작업을 수행 하는 방법을 보여 줍니다 (테스트/r o*zorpagestestsample. 테스트/유틸리티/유틸리티 .cs*).</span><span class="sxs-lookup"><span data-stu-id="fb866-167">The test app shows how to do this using its `Utilities` class method `TestDbContextOptions` (*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

<span data-ttu-id="fb866-168">DAL 단위 `DbContextOptions` 테스트에서를 사용 하면 새 데이터베이스 인스턴스로 각 테스트를 원자 단위로 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-168">Using the `DbContextOptions` in the DAL unit tests allows each test to run atomically with a fresh database instance:</span></span>

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="fb866-169">`DataAccessLayerTest` 클래스의 각 테스트 메서드 (단위*테스트/DataAccessLayerTest*)는 유사한 정렬 동작 어설션 패턴을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-169">Each test method in the `DataAccessLayerTest` class (*UnitTests/DataAccessLayerTest.cs*) follows a similar Arrange-Act-Assert pattern:</span></span>

1. <span data-ttu-id="fb866-170">정렬 데이터베이스는 테스트를 위해 구성 되 고 예상 된 결과가 정의 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-170">Arrange: The database is configured for the test and/or the expected outcome is defined.</span></span>
1. <span data-ttu-id="fb866-171">역할 테스트가 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-171">Act: The test is executed.</span></span>
1. <span data-ttu-id="fb866-172">어설션하 테스트 결과가 성공 인지 여부를 확인 하기 위해 어설션이 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-172">Assert: Assertions are made to determine if the test result is a success.</span></span>

<span data-ttu-id="fb866-173">예를 들어 메서드 `DeleteMessageAsync` 는 `Id` (*src/razorpagestestsample/Data/AppDbContext*)로 식별 되는 단일 메시지를 제거 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-173">For example, the `DeleteMessageAsync` method is responsible for removing a single message identified by its `Id` (*src/RazorPagesTestSample/Data/AppDbContext.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

<span data-ttu-id="fb866-174">이 메서드에는 두 가지 테스트가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-174">There are two tests for this method.</span></span> <span data-ttu-id="fb866-175">데이터베이스가 데이터베이스에 있는 경우에는 메서드가 메시지를 삭제 하는 테스트를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-175">One test checks that the method deletes a message when the message is present in the database.</span></span> <span data-ttu-id="fb866-176">다른 메서드는 삭제에 대 한 메시지가 `Id` 존재 하지 않는 경우 데이터베이스가 변경 되지 않는지 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-176">The other method tests that the database doesn't change if the message `Id` for deletion doesn't exist.</span></span> <span data-ttu-id="fb866-177">메서드 `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` 는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-177">The `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` method is shown below:</span></span>

[!code-csharp[](razor-pages-tests/samples_snapshot/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="fb866-178">첫째,이 메서드는 Act 단계가 준비 되는 정렬 단계를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-178">First, the method performs the Arrange step, where preparation for the Act step takes place.</span></span> <span data-ttu-id="fb866-179">시드 메시지는에서 `seedMessages`가져오고 보유 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-179">The seeding messages are obtained and held in `seedMessages`.</span></span> <span data-ttu-id="fb866-180">시드 메시지는 데이터베이스에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-180">The seeding messages are saved into the database.</span></span> <span data-ttu-id="fb866-181">가 인 `Id` `1` 메시지는 삭제를 위해 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-181">The message with an `Id` of `1` is set for deletion.</span></span> <span data-ttu-id="fb866-182">메서드가 실행 될 때 예상 되는 메시지에는의 `1`가 `Id` 있는 메시지를 제외한 모든 메시지가 있어야 합니다. `DeleteMessageAsync`</span><span class="sxs-lookup"><span data-stu-id="fb866-182">When the `DeleteMessageAsync` method is executed, the expected messages should have all of the messages except for the one with an `Id` of `1`.</span></span> <span data-ttu-id="fb866-183">변수 `expectedMessages` 는이 예상 결과를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-183">The `expectedMessages` variable represents this expected outcome.</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="fb866-184">메서드는 다음과 같이 동작 합니다. 메서드 `DeleteMessageAsync` 는 `recId` 의`1`를 전달 하 여 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-184">The method acts: The `DeleteMessageAsync` method is executed passing in the `recId` of `1`:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

<span data-ttu-id="fb866-185">마지막으로, 메서드는 컨텍스트에서 `Messages` 를 가져온 다음 두 항목이 같은지를 어설션 `expectedMessages` 하는 것과 비교 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-185">Finally, the method obtains the `Messages` from the context and compares it to the `expectedMessages` asserting that the two are equal:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

<span data-ttu-id="fb866-186">두 가지 `List<Message>` 를 비교 하기 위해 다음과 같이 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-186">In order to compare that the two `List<Message>` are the same:</span></span>

* <span data-ttu-id="fb866-187">메시지는를 기준으로 `Id`정렬 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-187">The messages are ordered by `Id`.</span></span>
* <span data-ttu-id="fb866-188">메시지 쌍은 `Text` 속성에서 비교 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-188">Message pairs are compared on the `Text` property.</span></span>

<span data-ttu-id="fb866-189">유사한 테스트 메서드 `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` 는 존재 하지 않는 메시지의 삭제를 시도한 결과를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-189">A similar test method, `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` checks the result of attempting to delete a message that doesn't exist.</span></span> <span data-ttu-id="fb866-190">이 경우 데이터베이스에서 예상 되는 메시지는 `DeleteMessageAsync` 메서드가 실행 된 후의 실제 메시지와 같아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-190">In this case, the expected messages in the database should be equal to the actual messages after the `DeleteMessageAsync` method is executed.</span></span> <span data-ttu-id="fb866-191">데이터베이스의 내용은 다음과 같이 변경 되지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-191">There should be no change to the database's content:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a><span data-ttu-id="fb866-192">페이지 모델 메서드의 단위 테스트</span><span class="sxs-lookup"><span data-stu-id="fb866-192">Unit tests of the page model methods</span></span>

<span data-ttu-id="fb866-193">다른 단위 테스트 집합은 페이지 모델 메서드의 테스트를 담당 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-193">Another set of unit tests is responsible for tests of page model methods.</span></span> <span data-ttu-id="fb866-194">메시지 앱에서 인덱스 페이지 모델은 `IndexModel` *src/razorpagestestsample/Pages/Index. cshtml*의 클래스에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-194">In the message app, the Index page models are found in the `IndexModel` class in *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.</span></span>

| <span data-ttu-id="fb866-195">페이지 모델 메서드</span><span class="sxs-lookup"><span data-stu-id="fb866-195">Page model method</span></span> | <span data-ttu-id="fb866-196">함수</span><span class="sxs-lookup"><span data-stu-id="fb866-196">Function</span></span> |
| ----------------- | -------- |
| `OnGetAsync` | <span data-ttu-id="fb866-197">메서드를 `GetMessagesAsync` 사용 하 여 UI에 대 한 DAL에서 메시지를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-197">Obtains the messages from the DAL for the UI using the `GetMessagesAsync` method.</span></span> |
| `OnPostAddMessageAsync` | <span data-ttu-id="fb866-198">[Modelstate](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) 가 올바르면를 호출 `AddMessageAsync` 하 여 데이터베이스에 메시지를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-198">If the [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) is valid, calls `AddMessageAsync` to add a message to the database.</span></span> |
| `OnPostDeleteAllMessagesAsync` | <span data-ttu-id="fb866-199">을 `DeleteAllMessagesAsync` 호출 하 여 데이터베이스의 모든 메시지를 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-199">Calls `DeleteAllMessagesAsync` to delete all of the messages in the database.</span></span> |
| `OnPostDeleteMessageAsync` | <span data-ttu-id="fb866-200">`Id` 지정 `DeleteMessageAsync` 된을 사용 하 여 메시지를 삭제 하려면를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-200">Executes `DeleteMessageAsync` to delete a message with the `Id` specified.</span></span> |
| `OnPostAnalyzeMessagesAsync` | <span data-ttu-id="fb866-201">하나 이상의 메시지가 데이터베이스에 있으면는 메시지당 평균 단어 수를 계산 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-201">If one or more messages are in the database, calculates the average number of words per message.</span></span> |

<span data-ttu-id="fb866-202">페이지 모델 메서드는 `IndexPageTests` 클래스의 7 가지 테스트를 사용 하 여 테스트 됩니다 (테스트/t o*zorpagestestsample. 테스트*/단위 테스트/IndexPageTests).</span><span class="sxs-lookup"><span data-stu-id="fb866-202">The page model methods are tested using seven tests in the `IndexPageTests` class (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*).</span></span> <span data-ttu-id="fb866-203">테스트는 친숙 한 정렬 동작 패턴을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-203">The tests use the familiar Arrange-Assert-Act pattern.</span></span> <span data-ttu-id="fb866-204">이러한 테스트는 다음에 중점을 둡니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-204">These tests focus on:</span></span>

* <span data-ttu-id="fb866-205">[Modelstate](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) 가 올바르지 않은 경우 메서드가 올바른 동작을 따르는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-205">Determining if the methods follow the correct behavior when the [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) is invalid.</span></span>
* <span data-ttu-id="fb866-206">메서드가 올바른 <xref:Microsoft.AspNetCore.Mvc.IActionResult>를 생성 하는 것을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-206">Confirming the methods produce the correct <xref:Microsoft.AspNetCore.Mvc.IActionResult>.</span></span>
* <span data-ttu-id="fb866-207">속성 값 할당이 올바르게 수행 되었는지 확인 하는 중입니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-207">Checking that property value assignments are made correctly.</span></span>

<span data-ttu-id="fb866-208">이 테스트 그룹은 종종 DAL의 메서드를 통해 페이지 모델 메서드가 실행 되는 Act 단계에 필요한 데이터를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-208">This group of tests often mock the methods of the DAL to produce expected data for the Act step where a page model method is executed.</span></span> <span data-ttu-id="fb866-209">예를 들어 `GetMessagesAsync` `AppDbContext` 의 메서드는 출력을 생성 하는 모의입니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-209">For example, the `GetMessagesAsync` method of the `AppDbContext` is mocked to produce output.</span></span> <span data-ttu-id="fb866-210">페이지 모델 메서드에서이 메서드를 실행 하면 모의 결과가 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-210">When a page model method executes this method, the mock returns the result.</span></span> <span data-ttu-id="fb866-211">데이터베이스에서 데이터를 가져오지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-211">The data doesn't come from the database.</span></span> <span data-ttu-id="fb866-212">이렇게 하면 페이지 모델 테스트에서 DAL을 사용 하기 위한 예측 가능 하 고 신뢰할 수 있는 테스트 조건이 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-212">This creates predictable, reliable test conditions for using the DAL in the page model tests.</span></span>

<span data-ttu-id="fb866-213">이 `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` 테스트는 `GetMessagesAsync` 메서드가 페이지 모델에 대해 모의 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-213">The `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` test shows how the `GetMessagesAsync` method is mocked for the page model:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

<span data-ttu-id="fb866-214">메서드가 Act 단계에서 실행 되 면 페이지 모델의 `GetMessagesAsync` 메서드를 호출 합니다. `OnGetAsync`</span><span class="sxs-lookup"><span data-stu-id="fb866-214">When the `OnGetAsync` method is executed in the Act step, it calls the page model's `GetMessagesAsync` method.</span></span>

<span data-ttu-id="fb866-215">단위 테스트 작업 단계 (*테스트/RazorPagesTestSample. 테스트/단위 테스트/IndexPageTests*):</span><span class="sxs-lookup"><span data-stu-id="fb866-215">Unit test Act step (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="fb866-216">`IndexPage`페이지 모델의 `OnGetAsync` 메서드 (*src/razorpagestestsample/Pages/Index. cshtml*):</span><span class="sxs-lookup"><span data-stu-id="fb866-216">`IndexPage` page model's `OnGetAsync` method (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

<span data-ttu-id="fb866-217">DAL `GetMessagesAsync` 의 메서드는이 메서드 호출에 대 한 결과를 반환 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-217">The `GetMessagesAsync` method in the DAL doesn't return the result for this method call.</span></span> <span data-ttu-id="fb866-218">메서드의 모의 버전은 결과를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-218">The mocked version of the method returns the result.</span></span>

<span data-ttu-id="fb866-219">이 단계에서 실제 메시지 (`actualMessages` `Messages` )는 페이지 모델의 속성에서 할당 됩니다. `Assert`</span><span class="sxs-lookup"><span data-stu-id="fb866-219">In the `Assert` step, the actual messages (`actualMessages`) are assigned from the `Messages` property of the page model.</span></span> <span data-ttu-id="fb866-220">메시지를 할당 하는 경우에도 유형 검사가 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-220">A type check is also performed when the messages are assigned.</span></span> <span data-ttu-id="fb866-221">예상 및 실제 메시지는 해당 `Text` 속성과 비교 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-221">The expected and actual messages are compared by their `Text` properties.</span></span> <span data-ttu-id="fb866-222">테스트에서는 두 `List<Message>` 인스턴스에 동일한 메시지가 포함 되어 있음을 어설션 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-222">The test asserts that the two `List<Message>` instances contain the same messages.</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

<span data-ttu-id="fb866-223">이 그룹의 다른 테스트에서는, 및 <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>를 설정 <xref:Microsoft.AspNetCore.Mvc.ActionContext> `ViewDataDictionary` <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> `PageContext`하는,, 및를 포함 하는 페이지 모델 개체를 만듭니다 `PageContext`.</span><span class="sxs-lookup"><span data-stu-id="fb866-223">Other tests in this group create page model objects that include the <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>, the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>, an <xref:Microsoft.AspNetCore.Mvc.ActionContext> to establish the `PageContext`, a `ViewDataDictionary`, and a `PageContext`.</span></span> <span data-ttu-id="fb866-224">이는 테스트를 수행 하는 데 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-224">These are useful in conducting tests.</span></span> <span data-ttu-id="fb866-225">예를 들어 메시지 앱은가 실행 `ModelState` 될 때 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> `OnPostAddMessageAsync` 유효한 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> 가 반환 되는지 확인 하기 위해 오류를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-225">For example, the message app establishes a `ModelState` error with <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> to check that a valid <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> is returned when `OnPostAddMessageAsync` is executed:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a><span data-ttu-id="fb866-226">추가 자료</span><span class="sxs-lookup"><span data-stu-id="fb866-226">Additional resources</span></span>

* [<span data-ttu-id="fb866-227">Dotnet 테스트 C# 및 xunit을 사용 하 여 .net Core에서 유닛 테스트</span><span class="sxs-lookup"><span data-stu-id="fb866-227">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* <span data-ttu-id="fb866-228">[코드 단위 테스트](/visualstudio/test/unit-test-your-code) (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="fb866-228">[Unit Test Your Code](/visualstudio/test/unit-test-your-code) (Visual Studio)</span></span>
* <xref:test/integration-tests>
* [<span data-ttu-id="fb866-229">xUnit.net</span><span class="sxs-lookup"><span data-stu-id="fb866-229">xUnit.net</span></span>](https://xunit.github.io/)
* [<span data-ttu-id="fb866-230">Visual Studio for Mac을 사용하여 macOS에서 완전한 .NET Core 솔루션 빌드</span><span class="sxs-lookup"><span data-stu-id="fb866-230">Building a complete .NET Core solution on macOS using Visual Studio for Mac</span></span>](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [<span data-ttu-id="fb866-231">XUnit.net 시작 하기: .Net SDK 명령줄을 사용 하 여 .NET Core 사용</span><span class="sxs-lookup"><span data-stu-id="fb866-231">Getting started with xUnit.net: Using .NET Core with the .NET SDK command line</span></span>](https://xunit.github.io/docs/getting-started-dotnet-core)
* [<span data-ttu-id="fb866-232">Moq</span><span class="sxs-lookup"><span data-stu-id="fb866-232">Moq</span></span>](https://github.com/moq/moq4)
* [<span data-ttu-id="fb866-233">Moq 빠른 시작</span><span class="sxs-lookup"><span data-stu-id="fb866-233">Moq Quickstart</span></span>](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fb866-234">ASP.NET Core Razor Pages 앱의 단위 테스트를 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-234">ASP.NET Core supports unit tests of Razor Pages apps.</span></span> <span data-ttu-id="fb866-235">DAL (데이터 액세스 계층) 및 페이지 모델의 테스트를 통해 다음을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-235">Tests of the data access layer (DAL) and page models help ensure:</span></span>

* <span data-ttu-id="fb866-236">Razor Pages 앱의 일부는 앱 생성 중에 독립적으로 또는 하나의 단위로 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-236">Parts of a Razor Pages app work independently and together as a unit during app construction.</span></span>
* <span data-ttu-id="fb866-237">클래스 및 메서드의 책임 범위는 제한 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-237">Classes and methods have limited scopes of responsibility.</span></span>
* <span data-ttu-id="fb866-238">앱이 작동 하는 방식에 대 한 추가 설명서가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-238">Additional documentation exists on how the app should behave.</span></span>
* <span data-ttu-id="fb866-239">코드 업데이트에 의해 발생 하는 오류인 재발은 자동화 된 빌드 및 배포 중에 발견 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-239">Regressions, which are errors brought about by updates to the code, are found during automated building and deployment.</span></span>

<span data-ttu-id="fb866-240">이 항목에서는 Razor Pages 앱 및 단위 테스트에 대해 기본적으로 이해 하 고 있다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-240">This topic assumes that you have a basic understanding of Razor Pages apps and unit tests.</span></span> <span data-ttu-id="fb866-241">Razor Pages 앱 또는 테스트 개념에 익숙하지 않은 경우 다음 항목을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="fb866-241">If you're unfamiliar with Razor Pages apps or test concepts, see the following topics:</span></span>

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [<span data-ttu-id="fb866-242">Dotnet 테스트 C# 및 xunit을 사용 하 여 .net Core에서 유닛 테스트</span><span class="sxs-lookup"><span data-stu-id="fb866-242">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

<span data-ttu-id="fb866-243">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fb866-243">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="fb866-244">샘플 프로젝트는 다음과 같은 두 개의 앱으로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-244">The sample project is composed of two apps:</span></span>

| <span data-ttu-id="fb866-245">앱</span><span class="sxs-lookup"><span data-stu-id="fb866-245">App</span></span>         | <span data-ttu-id="fb866-246">프로젝트 폴더</span><span class="sxs-lookup"><span data-stu-id="fb866-246">Project folder</span></span>                     | <span data-ttu-id="fb866-247">Description</span><span class="sxs-lookup"><span data-stu-id="fb866-247">Description</span></span> |
| ----------- | ---------------------------------- | ----------- |
| <span data-ttu-id="fb866-248">메시지 앱</span><span class="sxs-lookup"><span data-stu-id="fb866-248">Message app</span></span> | <span data-ttu-id="fb866-249">*src/RazorPagesTestSample*</span><span class="sxs-lookup"><span data-stu-id="fb866-249">*src/RazorPagesTestSample*</span></span>         | <span data-ttu-id="fb866-250">사용자가 메시지를 추가 하 고, 메시지 하나를 삭제 하 고, 모든 메시지를 삭제 하 고, 메시지를 분석할 수 있도록 허용 합니다 (메시지당 평균 단어 수를 확인).</span><span class="sxs-lookup"><span data-stu-id="fb866-250">Allows a user to add a message, delete one message, delete all messages, and analyze messages (find the average number of words per message).</span></span> |
| <span data-ttu-id="fb866-251">응용 프로그램 테스트</span><span class="sxs-lookup"><span data-stu-id="fb866-251">Test app</span></span>    | <span data-ttu-id="fb866-252">*tests/RazorPagesTestSample.Tests*</span><span class="sxs-lookup"><span data-stu-id="fb866-252">*tests/RazorPagesTestSample.Tests*</span></span> | <span data-ttu-id="fb866-253">메시지 앱의 DAL 및 인덱스 페이지 모델을 단위 테스트 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-253">Used to unit test the DAL and Index page model of the message app.</span></span> |

<span data-ttu-id="fb866-254">[Visual Studio](/visualstudio/test/unit-test-your-code) 또는 [MAC용 VISUAL STUDIO](/dotnet/core/tutorials/using-on-mac-vs-full-solution)와 같은 IDE의 기본 제공 테스트 기능을 사용 하 여 테스트를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-254">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](/visualstudio/test/unit-test-your-code) or [Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution).</span></span> <span data-ttu-id="fb866-255">[Visual Studio Code](https://code.visualstudio.com/) 또는 명령줄을 사용 하는 경우 *테스트/RazorPagesTestSample. 테스트* 폴더의 명령 프롬프트에서 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-255">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesTestSample.Tests* folder:</span></span>

```console
dotnet test
```

## <a name="message-app-organization"></a><span data-ttu-id="fb866-256">메시지 앱 구성</span><span class="sxs-lookup"><span data-stu-id="fb866-256">Message app organization</span></span>

<span data-ttu-id="fb866-257">메시지 앱은 다음과 같은 특징을 가진 Razor Pages 메시지 시스템입니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-257">The message app is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="fb866-258">앱의 인덱스 페이지 (*Pages/index. cshtml* 및 *pages/인덱스인*)는 메시지의 추가, 삭제 및 분석을 제어 하는 UI 및 페이지 모델 메서드를 제공 합니다 (메시지당 평균 단어 수 찾기).</span><span class="sxs-lookup"><span data-stu-id="fb866-258">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (find the average number of words per message).</span></span>
* <span data-ttu-id="fb866-259">메시지는 두 속성 `Message` `Id` (키) 및 `Text` (메시지)를 사용 하 여 클래스 (*데이터/메시지 .cs*)에서 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-259">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="fb866-260">`Text` 속성은 필수 이며 200 자로 제한 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-260">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="fb866-261">메시지는 [Entity Framework의 메모리 내 데이터베이스](/ef/core/providers/in-memory/)&#8224;를 사용 하 여 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-261">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="fb866-262">앱은 데이터베이스 컨텍스트 클래스 `AppDbContext` (*Data/AppDbContext*)에 DAL을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-262">The app contains a DAL in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span> <span data-ttu-id="fb866-263">DAL 메서드는 테스트에서 `virtual`사용 하기 위한 메서드를 mock 할 수 있도록 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-263">The DAL methods are marked `virtual`, which allows mocking the methods for use in the tests.</span></span>
* <span data-ttu-id="fb866-264">응용 프로그램이 시작 될 때 데이터베이스가 비어 있는 경우 메시지 저장소는 세 개의 메시지로 초기화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-264">If the database is empty on app startup, the message store is initialized with three messages.</span></span> <span data-ttu-id="fb866-265">이러한 *시드 된 메시지* 는 테스트에도 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-265">These *seeded messages* are also used in tests.</span></span>

<span data-ttu-id="fb866-266">&#8224;EF 항목인 [InMemory 테스트](/ef/core/miscellaneous/testing/in-memory)는 MSTest를 사용 하 여 테스트에 메모리 내 데이터베이스를 사용 하는 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-266">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="fb866-267">이 항목에서는 [Xunit](https://xunit.github.io/) 테스트 프레임 워크를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-267">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="fb866-268">여러 테스트 프레임 워크에서 테스트 개념 및 테스트 구현은 비슷하지만 동일 하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-268">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="fb866-269">샘플 앱은 리포지토리 패턴을 사용 하지 않으며 [UoW (Unit Of Work) 패턴](https://martinfowler.com/eaaCatalog/unitOfWork.html)의 효과적인 예는 아니지만 Razor Pages 이러한 개발 패턴을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-269">Although the sample app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="fb866-270">자세한 내용은 [인프라 지 속성 계층 디자인](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) 및 <xref:mvc/controllers/testing> (샘플에서는 리포지토리 패턴 구현)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="fb866-270">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and <xref:mvc/controllers/testing> (the sample implements the repository pattern).</span></span>

## <a name="test-app-organization"></a><span data-ttu-id="fb866-271">앱 구성 테스트</span><span class="sxs-lookup"><span data-stu-id="fb866-271">Test app organization</span></span>

<span data-ttu-id="fb866-272">테스트 앱은 테스트 */Razorpagestestestsample. 테스트* 폴더 내의 콘솔 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-272">The test app is a console app inside the *tests/RazorPagesTestSample.Tests* folder.</span></span>

| <span data-ttu-id="fb866-273">테스트 앱 폴더</span><span class="sxs-lookup"><span data-stu-id="fb866-273">Test app folder</span></span> | <span data-ttu-id="fb866-274">설명</span><span class="sxs-lookup"><span data-stu-id="fb866-274">Description</span></span> |
| --------------- | ----------- |
| <span data-ttu-id="fb866-275">*UnitTests*</span><span class="sxs-lookup"><span data-stu-id="fb866-275">*UnitTests*</span></span>     | <ul><li><span data-ttu-id="fb866-276">*DataAccessLayerTest.cs* 에는 DAL에 대 한 단위 테스트가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-276">*DataAccessLayerTest.cs* contains the unit tests for the DAL.</span></span></li><li><span data-ttu-id="fb866-277">*IndexPageTests.cs* 에는 인덱스 페이지 모델에 대 한 단위 테스트가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-277">*IndexPageTests.cs* contains the unit tests for the Index page model.</span></span></li></ul> |
| <span data-ttu-id="fb866-278">*유틸리티로*</span><span class="sxs-lookup"><span data-stu-id="fb866-278">*Utilities*</span></span>     | <span data-ttu-id="fb866-279">각 DAL `TestDbContextOptions` 단위 테스트에 대 한 새 데이터베이스 컨텍스트 옵션을 만드는 데 사용 되는 메서드를 포함 하 여 각 테스트에 대 한 기준선 조건으로 데이터베이스를 다시 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-279">Contains the `TestDbContextOptions` method used to create new database context options for each DAL unit test so that the database is reset to its baseline condition for each test.</span></span> |

<span data-ttu-id="fb866-280">테스트 프레임 워크는 [Xunit](https://xunit.github.io/)입니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-280">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="fb866-281">개체 모의 프레임 워크는 [Moq](https://github.com/moq/moq4)입니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-281">The object mocking framework is [Moq](https://github.com/moq/moq4).</span></span>

## <a name="unit-tests-of-the-data-access-layer-dal"></a><span data-ttu-id="fb866-282">DAL (데이터 액세스 계층)의 단위 테스트</span><span class="sxs-lookup"><span data-stu-id="fb866-282">Unit tests of the data access layer (DAL)</span></span>

<span data-ttu-id="fb866-283">메시지 앱에는 `AppDbContext` 클래스 (*src/razorpagestestsample/Data/AppDbContext*)에 포함 된 네 개의 메서드가 있는 DAL이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-283">The message app has a DAL with four methods contained in the `AppDbContext` class (*src/RazorPagesTestSample/Data/AppDbContext.cs*).</span></span> <span data-ttu-id="fb866-284">각 메서드에는 테스트 앱에 하나 또는 두 개의 단위 테스트가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-284">Each method has one or two unit tests in the test app.</span></span>

| <span data-ttu-id="fb866-285">DAL 메서드</span><span class="sxs-lookup"><span data-stu-id="fb866-285">DAL method</span></span>               | <span data-ttu-id="fb866-286">함수</span><span class="sxs-lookup"><span data-stu-id="fb866-286">Function</span></span>                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | <span data-ttu-id="fb866-287">속성`Text` 을 `List<Message>` 기준으로 정렬 된 데이터베이스에서을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-287">Obtains a `List<Message>` from the database sorted by the `Text` property.</span></span> |
| `AddMessageAsync`        | <span data-ttu-id="fb866-288">데이터베이스에 `Message` 를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-288">Adds a `Message` to the database.</span></span>                                          |
| `DeleteAllMessagesAsync` | <span data-ttu-id="fb866-289">데이터베이스에서 `Message` 모든 항목을 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-289">Deletes all `Message` entries from the database.</span></span>                           |
| `DeleteMessageAsync`     | <span data-ttu-id="fb866-290">`Message` 에서`Id`데이터베이스의 단일를 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-290">Deletes a single `Message` from the database by `Id`.</span></span>                      |

<span data-ttu-id="fb866-291">DAL의 단위 테스트는 각 <xref:Microsoft.EntityFrameworkCore.DbContextOptions> 테스트에 대해 새 `AppDbContext` 을 만들 때 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-291">Unit tests of the DAL require <xref:Microsoft.EntityFrameworkCore.DbContextOptions> when creating a new `AppDbContext` for each test.</span></span> <span data-ttu-id="fb866-292">각 테스트 `DbContextOptions` 에 대해를 만드는 한 가지 방법은를 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>사용 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-292">One approach to creating the `DbContextOptions` for each test is to use a <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>:</span></span>

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="fb866-293">이 방법의 문제는 각 테스트에서 이전 테스트를 떠난 모든 상태에서 데이터베이스를 수신 한다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-293">The problem with this approach is that each test receives the database in whatever state the previous test left it.</span></span> <span data-ttu-id="fb866-294">이는 서로 방해 하지 않는 원자 단위 테스트를 작성 하려고 할 때 문제가 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-294">This can be problematic when trying to write atomic unit tests that don't interfere with each other.</span></span> <span data-ttu-id="fb866-295">에서 `AppDbContext` 각 테스트에 대해 새 데이터베이스 컨텍스트를 사용 하도록 하려면 새 서비스 공급자 `DbContextOptions` 를 기반으로 하는 인스턴스를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-295">To force the `AppDbContext` to use a new database context for each test, supply a `DbContextOptions` instance that's based on a new service provider.</span></span> <span data-ttu-id="fb866-296">테스트 앱은 해당 `Utilities` 클래스 메서드 `TestDbContextOptions` 를 사용 하 여이 작업을 수행 하는 방법을 보여 줍니다 (테스트/r o*zorpagestestsample. 테스트/유틸리티/유틸리티 .cs*).</span><span class="sxs-lookup"><span data-stu-id="fb866-296">The test app shows how to do this using its `Utilities` class method `TestDbContextOptions` (*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

<span data-ttu-id="fb866-297">DAL 단위 `DbContextOptions` 테스트에서를 사용 하면 새 데이터베이스 인스턴스로 각 테스트를 원자 단위로 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-297">Using the `DbContextOptions` in the DAL unit tests allows each test to run atomically with a fresh database instance:</span></span>

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="fb866-298">`DataAccessLayerTest` 클래스의 각 테스트 메서드 (단위*테스트/DataAccessLayerTest*)는 유사한 정렬 동작 어설션 패턴을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-298">Each test method in the `DataAccessLayerTest` class (*UnitTests/DataAccessLayerTest.cs*) follows a similar Arrange-Act-Assert pattern:</span></span>

1. <span data-ttu-id="fb866-299">정렬 데이터베이스는 테스트를 위해 구성 되 고 예상 된 결과가 정의 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-299">Arrange: The database is configured for the test and/or the expected outcome is defined.</span></span>
1. <span data-ttu-id="fb866-300">역할 테스트가 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-300">Act: The test is executed.</span></span>
1. <span data-ttu-id="fb866-301">어설션하 테스트 결과가 성공 인지 여부를 확인 하기 위해 어설션이 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-301">Assert: Assertions are made to determine if the test result is a success.</span></span>

<span data-ttu-id="fb866-302">예를 들어 메서드 `DeleteMessageAsync` 는 `Id` (*src/razorpagestestsample/Data/AppDbContext*)로 식별 되는 단일 메시지를 제거 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-302">For example, the `DeleteMessageAsync` method is responsible for removing a single message identified by its `Id` (*src/RazorPagesTestSample/Data/AppDbContext.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

<span data-ttu-id="fb866-303">이 메서드에는 두 가지 테스트가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-303">There are two tests for this method.</span></span> <span data-ttu-id="fb866-304">데이터베이스가 데이터베이스에 있는 경우에는 메서드가 메시지를 삭제 하는 테스트를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-304">One test checks that the method deletes a message when the message is present in the database.</span></span> <span data-ttu-id="fb866-305">다른 메서드는 삭제에 대 한 메시지가 `Id` 존재 하지 않는 경우 데이터베이스가 변경 되지 않는지 테스트 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-305">The other method tests that the database doesn't change if the message `Id` for deletion doesn't exist.</span></span> <span data-ttu-id="fb866-306">메서드 `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` 는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-306">The `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` method is shown below:</span></span>

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="fb866-307">첫째,이 메서드는 Act 단계가 준비 되는 정렬 단계를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-307">First, the method performs the Arrange step, where preparation for the Act step takes place.</span></span> <span data-ttu-id="fb866-308">시드 메시지는에서 `seedMessages`가져오고 보유 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-308">The seeding messages are obtained and held in `seedMessages`.</span></span> <span data-ttu-id="fb866-309">시드 메시지는 데이터베이스에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-309">The seeding messages are saved into the database.</span></span> <span data-ttu-id="fb866-310">가 인 `Id` `1` 메시지는 삭제를 위해 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-310">The message with an `Id` of `1` is set for deletion.</span></span> <span data-ttu-id="fb866-311">메서드가 실행 될 때 예상 되는 메시지에는의 `1`가 `Id` 있는 메시지를 제외한 모든 메시지가 있어야 합니다. `DeleteMessageAsync`</span><span class="sxs-lookup"><span data-stu-id="fb866-311">When the `DeleteMessageAsync` method is executed, the expected messages should have all of the messages except for the one with an `Id` of `1`.</span></span> <span data-ttu-id="fb866-312">변수 `expectedMessages` 는이 예상 결과를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-312">The `expectedMessages` variable represents this expected outcome.</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="fb866-313">메서드는 다음과 같이 동작 합니다. 메서드 `DeleteMessageAsync` 는 `recId` 의`1`를 전달 하 여 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-313">The method acts: The `DeleteMessageAsync` method is executed passing in the `recId` of `1`:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

<span data-ttu-id="fb866-314">마지막으로, 메서드는 컨텍스트에서 `Messages` 를 가져온 다음 두 항목이 같은지를 어설션 `expectedMessages` 하는 것과 비교 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-314">Finally, the method obtains the `Messages` from the context and compares it to the `expectedMessages` asserting that the two are equal:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

<span data-ttu-id="fb866-315">두 가지 `List<Message>` 를 비교 하기 위해 다음과 같이 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-315">In order to compare that the two `List<Message>` are the same:</span></span>

* <span data-ttu-id="fb866-316">메시지는를 기준으로 `Id`정렬 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-316">The messages are ordered by `Id`.</span></span>
* <span data-ttu-id="fb866-317">메시지 쌍은 `Text` 속성에서 비교 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-317">Message pairs are compared on the `Text` property.</span></span>

<span data-ttu-id="fb866-318">유사한 테스트 메서드 `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` 는 존재 하지 않는 메시지의 삭제를 시도한 결과를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-318">A similar test method, `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` checks the result of attempting to delete a message that doesn't exist.</span></span> <span data-ttu-id="fb866-319">이 경우 데이터베이스에서 예상 되는 메시지는 `DeleteMessageAsync` 메서드가 실행 된 후의 실제 메시지와 같아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-319">In this case, the expected messages in the database should be equal to the actual messages after the `DeleteMessageAsync` method is executed.</span></span> <span data-ttu-id="fb866-320">데이터베이스의 내용은 다음과 같이 변경 되지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-320">There should be no change to the database's content:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a><span data-ttu-id="fb866-321">페이지 모델 메서드의 단위 테스트</span><span class="sxs-lookup"><span data-stu-id="fb866-321">Unit tests of the page model methods</span></span>

<span data-ttu-id="fb866-322">다른 단위 테스트 집합은 페이지 모델 메서드의 테스트를 담당 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-322">Another set of unit tests is responsible for tests of page model methods.</span></span> <span data-ttu-id="fb866-323">메시지 앱에서 인덱스 페이지 모델은 `IndexModel` *src/razorpagestestsample/Pages/Index. cshtml*의 클래스에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-323">In the message app, the Index page models are found in the `IndexModel` class in *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.</span></span>

| <span data-ttu-id="fb866-324">페이지 모델 메서드</span><span class="sxs-lookup"><span data-stu-id="fb866-324">Page model method</span></span> | <span data-ttu-id="fb866-325">함수</span><span class="sxs-lookup"><span data-stu-id="fb866-325">Function</span></span> |
| ----------------- | -------- |
| `OnGetAsync` | <span data-ttu-id="fb866-326">메서드를 `GetMessagesAsync` 사용 하 여 UI에 대 한 DAL에서 메시지를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-326">Obtains the messages from the DAL for the UI using the `GetMessagesAsync` method.</span></span> |
| `OnPostAddMessageAsync` | <span data-ttu-id="fb866-327">[Modelstate](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) 가 올바르면를 호출 `AddMessageAsync` 하 여 데이터베이스에 메시지를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-327">If the [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) is valid, calls `AddMessageAsync` to add a message to the database.</span></span> |
| `OnPostDeleteAllMessagesAsync` | <span data-ttu-id="fb866-328">을 `DeleteAllMessagesAsync` 호출 하 여 데이터베이스의 모든 메시지를 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-328">Calls `DeleteAllMessagesAsync` to delete all of the messages in the database.</span></span> |
| `OnPostDeleteMessageAsync` | <span data-ttu-id="fb866-329">`Id` 지정 `DeleteMessageAsync` 된을 사용 하 여 메시지를 삭제 하려면를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-329">Executes `DeleteMessageAsync` to delete a message with the `Id` specified.</span></span> |
| `OnPostAnalyzeMessagesAsync` | <span data-ttu-id="fb866-330">하나 이상의 메시지가 데이터베이스에 있으면는 메시지당 평균 단어 수를 계산 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-330">If one or more messages are in the database, calculates the average number of words per message.</span></span> |

<span data-ttu-id="fb866-331">페이지 모델 메서드는 `IndexPageTests` 클래스의 7 가지 테스트를 사용 하 여 테스트 됩니다 (테스트/t o*zorpagestestsample. 테스트*/단위 테스트/IndexPageTests).</span><span class="sxs-lookup"><span data-stu-id="fb866-331">The page model methods are tested using seven tests in the `IndexPageTests` class (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*).</span></span> <span data-ttu-id="fb866-332">테스트는 친숙 한 정렬 동작 패턴을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-332">The tests use the familiar Arrange-Assert-Act pattern.</span></span> <span data-ttu-id="fb866-333">이러한 테스트는 다음에 중점을 둡니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-333">These tests focus on:</span></span>

* <span data-ttu-id="fb866-334">[Modelstate](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) 가 올바르지 않은 경우 메서드가 올바른 동작을 따르는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-334">Determining if the methods follow the correct behavior when the [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) is invalid.</span></span>
* <span data-ttu-id="fb866-335">메서드가 올바른 <xref:Microsoft.AspNetCore.Mvc.IActionResult>를 생성 하는 것을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-335">Confirming the methods produce the correct <xref:Microsoft.AspNetCore.Mvc.IActionResult>.</span></span>
* <span data-ttu-id="fb866-336">속성 값 할당이 올바르게 수행 되었는지 확인 하는 중입니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-336">Checking that property value assignments are made correctly.</span></span>

<span data-ttu-id="fb866-337">이 테스트 그룹은 종종 DAL의 메서드를 통해 페이지 모델 메서드가 실행 되는 Act 단계에 필요한 데이터를 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-337">This group of tests often mock the methods of the DAL to produce expected data for the Act step where a page model method is executed.</span></span> <span data-ttu-id="fb866-338">예를 들어 `GetMessagesAsync` `AppDbContext` 의 메서드는 출력을 생성 하는 모의입니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-338">For example, the `GetMessagesAsync` method of the `AppDbContext` is mocked to produce output.</span></span> <span data-ttu-id="fb866-339">페이지 모델 메서드에서이 메서드를 실행 하면 모의 결과가 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-339">When a page model method executes this method, the mock returns the result.</span></span> <span data-ttu-id="fb866-340">데이터베이스에서 데이터를 가져오지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-340">The data doesn't come from the database.</span></span> <span data-ttu-id="fb866-341">이렇게 하면 페이지 모델 테스트에서 DAL을 사용 하기 위한 예측 가능 하 고 신뢰할 수 있는 테스트 조건이 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-341">This creates predictable, reliable test conditions for using the DAL in the page model tests.</span></span>

<span data-ttu-id="fb866-342">이 `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` 테스트는 `GetMessagesAsync` 메서드가 페이지 모델에 대해 모의 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-342">The `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` test shows how the `GetMessagesAsync` method is mocked for the page model:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

<span data-ttu-id="fb866-343">메서드가 Act 단계에서 실행 되 면 페이지 모델의 `GetMessagesAsync` 메서드를 호출 합니다. `OnGetAsync`</span><span class="sxs-lookup"><span data-stu-id="fb866-343">When the `OnGetAsync` method is executed in the Act step, it calls the page model's `GetMessagesAsync` method.</span></span>

<span data-ttu-id="fb866-344">단위 테스트 작업 단계 (*테스트/RazorPagesTestSample. 테스트/단위 테스트/IndexPageTests*):</span><span class="sxs-lookup"><span data-stu-id="fb866-344">Unit test Act step (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="fb866-345">`IndexPage`페이지 모델의 `OnGetAsync` 메서드 (*src/razorpagestestsample/Pages/Index. cshtml*):</span><span class="sxs-lookup"><span data-stu-id="fb866-345">`IndexPage` page model's `OnGetAsync` method (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

<span data-ttu-id="fb866-346">DAL `GetMessagesAsync` 의 메서드는이 메서드 호출에 대 한 결과를 반환 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-346">The `GetMessagesAsync` method in the DAL doesn't return the result for this method call.</span></span> <span data-ttu-id="fb866-347">메서드의 모의 버전은 결과를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-347">The mocked version of the method returns the result.</span></span>

<span data-ttu-id="fb866-348">이 단계에서 실제 메시지 (`actualMessages` `Messages` )는 페이지 모델의 속성에서 할당 됩니다. `Assert`</span><span class="sxs-lookup"><span data-stu-id="fb866-348">In the `Assert` step, the actual messages (`actualMessages`) are assigned from the `Messages` property of the page model.</span></span> <span data-ttu-id="fb866-349">메시지를 할당 하는 경우에도 유형 검사가 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-349">A type check is also performed when the messages are assigned.</span></span> <span data-ttu-id="fb866-350">예상 및 실제 메시지는 해당 `Text` 속성과 비교 됩니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-350">The expected and actual messages are compared by their `Text` properties.</span></span> <span data-ttu-id="fb866-351">테스트에서는 두 `List<Message>` 인스턴스에 동일한 메시지가 포함 되어 있음을 어설션 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-351">The test asserts that the two `List<Message>` instances contain the same messages.</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

<span data-ttu-id="fb866-352">이 그룹의 다른 테스트에서는, 및 <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>를 설정 <xref:Microsoft.AspNetCore.Mvc.ActionContext> `ViewDataDictionary` <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> `PageContext`하는,, 및를 포함 하는 페이지 모델 개체를 만듭니다 `PageContext`.</span><span class="sxs-lookup"><span data-stu-id="fb866-352">Other tests in this group create page model objects that include the <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>, the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>, an <xref:Microsoft.AspNetCore.Mvc.ActionContext> to establish the `PageContext`, a `ViewDataDictionary`, and a `PageContext`.</span></span> <span data-ttu-id="fb866-353">이는 테스트를 수행 하는 데 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-353">These are useful in conducting tests.</span></span> <span data-ttu-id="fb866-354">예를 들어 메시지 앱은가 실행 `ModelState` 될 때 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> `OnPostAddMessageAsync` 유효한 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> 가 반환 되는지 확인 하기 위해 오류를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="fb866-354">For example, the message app establishes a `ModelState` error with <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> to check that a valid <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> is returned when `OnPostAddMessageAsync` is executed:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a><span data-ttu-id="fb866-355">추가 자료</span><span class="sxs-lookup"><span data-stu-id="fb866-355">Additional resources</span></span>

* [<span data-ttu-id="fb866-356">Dotnet 테스트 C# 및 xunit을 사용 하 여 .net Core에서 유닛 테스트</span><span class="sxs-lookup"><span data-stu-id="fb866-356">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* <span data-ttu-id="fb866-357">[코드 단위 테스트](/visualstudio/test/unit-test-your-code) (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="fb866-357">[Unit Test Your Code](/visualstudio/test/unit-test-your-code) (Visual Studio)</span></span>
* <xref:test/integration-tests>
* [<span data-ttu-id="fb866-358">xUnit.net</span><span class="sxs-lookup"><span data-stu-id="fb866-358">xUnit.net</span></span>](https://xunit.github.io/)
* [<span data-ttu-id="fb866-359">Visual Studio for Mac을 사용하여 macOS에서 완전한 .NET Core 솔루션 빌드</span><span class="sxs-lookup"><span data-stu-id="fb866-359">Building a complete .NET Core solution on macOS using Visual Studio for Mac</span></span>](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [<span data-ttu-id="fb866-360">XUnit.net 시작 하기: .Net SDK 명령줄을 사용 하 여 .NET Core 사용</span><span class="sxs-lookup"><span data-stu-id="fb866-360">Getting started with xUnit.net: Using .NET Core with the .NET SDK command line</span></span>](https://xunit.github.io/docs/getting-started-dotnet-core)
* [<span data-ttu-id="fb866-361">Moq</span><span class="sxs-lookup"><span data-stu-id="fb866-361">Moq</span></span>](https://github.com/moq/moq4)
* [<span data-ttu-id="fb866-362">Moq 빠른 시작</span><span class="sxs-lookup"><span data-stu-id="fb866-362">Moq Quickstart</span></span>](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end
