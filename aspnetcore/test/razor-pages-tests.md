---
title: ASP.NET Core에서 razor 페이지 단위 테스트
author: guardrex
description: Razor 페이지 앱에 대 한 단위 테스트를 만드는 방법에 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 11/27/2017
uid: test/razor-pages-tests
ms.openlocfilehash: f1526b8803f43ec8cbe77c1d2c100d9daf6cd316
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64893720"
---
# <a name="razor-pages-unit-tests-in-aspnet-core"></a><span data-ttu-id="cd901-103">ASP.NET Core에서 razor 페이지 단위 테스트</span><span class="sxs-lookup"><span data-stu-id="cd901-103">Razor Pages unit tests in ASP.NET Core</span></span>

<span data-ttu-id="cd901-104">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="cd901-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="cd901-105">ASP.NET Core Razor 페이지 앱의 단위 테스트를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-105">ASP.NET Core supports unit tests of Razor Pages apps.</span></span> <span data-ttu-id="cd901-106">테스트 데이터의 DAL (계층)에 액세스 및 페이지 모델 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-106">Tests of the data access layer (DAL) and page models help ensure:</span></span>

* <span data-ttu-id="cd901-107">앱 생성 하는 동안 요금과 함께 하나의 단위로 작동 하는 Razor 페이지 앱의 일부입니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-107">Parts of a Razor Pages app work independently and together as a unit during app construction.</span></span>
* <span data-ttu-id="cd901-108">클래스 및 메서드 책임의 범위 제한 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-108">Classes and methods have limited scopes of responsibility.</span></span>
* <span data-ttu-id="cd901-109">추가 설명서는 앱 동작 방식에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-109">Additional documentation exists on how the app should behave.</span></span>
* <span data-ttu-id="cd901-110">재발 된 오류 코드에 대 한 업데이트를 통해 자동화 된 빌드 및 배포 하는 동안 발견 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-110">Regressions, which are errors brought about by updates to the code, are found during automated building and deployment.</span></span>

<span data-ttu-id="cd901-111">이 항목에서는 Razor 페이지 앱 및 단위 테스트는 기본적인 이해가 있다고 가정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-111">This topic assumes that you have a basic understanding of Razor Pages apps and unit tests.</span></span> <span data-ttu-id="cd901-112">Razor 페이지 앱 또는 테스트 개념을 잘 모르는 경우 다음 항목을 참조 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-112">If you're unfamiliar with Razor Pages apps or test concepts, see the following topics:</span></span>

* [<span data-ttu-id="cd901-113">Razor 페이지 소개</span><span class="sxs-lookup"><span data-stu-id="cd901-113">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="cd901-114">Razor 페이지 시작</span><span class="sxs-lookup"><span data-stu-id="cd901-114">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="cd901-115">Dotnet test 및 xUnit을 사용 하 여.NET Core에서 C# 테스트 단위</span><span class="sxs-lookup"><span data-stu-id="cd901-115">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

<span data-ttu-id="cd901-116">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cd901-116">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="cd901-117">샘플 프로젝트는 두 개의 앱으로 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-117">The sample project is composed of two apps:</span></span>

| <span data-ttu-id="cd901-118">앱</span><span class="sxs-lookup"><span data-stu-id="cd901-118">App</span></span>         | <span data-ttu-id="cd901-119">프로젝트 폴더</span><span class="sxs-lookup"><span data-stu-id="cd901-119">Project folder</span></span>                        | <span data-ttu-id="cd901-120">설명</span><span class="sxs-lookup"><span data-stu-id="cd901-120">Description</span></span> |
| ----------- | ------------------------------------- | ----------- |
| <span data-ttu-id="cd901-121">메시지 앱</span><span class="sxs-lookup"><span data-stu-id="cd901-121">Message app</span></span> | <span data-ttu-id="cd901-122">*src/RazorPagesTestSample*</span><span class="sxs-lookup"><span data-stu-id="cd901-122">*src/RazorPagesTestSample*</span></span>            | <span data-ttu-id="cd901-123">추가, 하나를 삭제, all, 삭제 및 메시지를 분석할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-123">Allows a user to add, delete one, delete all, and analyze messages.</span></span> |
| <span data-ttu-id="cd901-124">테스트 앱</span><span class="sxs-lookup"><span data-stu-id="cd901-124">Test app</span></span>    | <span data-ttu-id="cd901-125">*tests/RazorPagesTestSample.Tests*</span><span class="sxs-lookup"><span data-stu-id="cd901-125">*tests/RazorPagesTestSample.Tests*</span></span>    | <span data-ttu-id="cd901-126">메시지 앱 단위 테스트 하는 데 사용 합니다. DAL (데이터 액세스 계층) 및 인덱스 페이지 모델입니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-126">Used to unit test the message app: Data access layer (DAL) and Index page model.</span></span> |

<span data-ttu-id="cd901-127">와 같은 기본 제공 테스트에 대 한 기능의 IDE 사용 하 여 테스트를 실행할 수 있습니다 [Visual Studio](https://visualstudio.microsoft.com)합니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-127">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="cd901-128">사용 하는 경우 [Visual Studio Code](https://code.visualstudio.com/) 또는 명령줄에서 명령 프롬프트에서 다음 명령을 실행 합니다 *tests/RazorPagesTestSample.Tests* 폴더:</span><span class="sxs-lookup"><span data-stu-id="cd901-128">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesTestSample.Tests* folder:</span></span>

```console
dotnet test
```

## <a name="message-app-organization"></a><span data-ttu-id="cd901-129">메시지 앱 조직</span><span class="sxs-lookup"><span data-stu-id="cd901-129">Message app organization</span></span>

<span data-ttu-id="cd901-130">메시지 앱은 다음 특성을 가진 간단한 Razor 페이지 메시지 시스템:</span><span class="sxs-lookup"><span data-stu-id="cd901-130">The message app is a simple Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="cd901-131">앱의 인덱스 페이지 (*pages/Index.cshtml* 하 고 *Pages/Index.cshtml.cs*) UI 및 페이지 추가, 삭제 및 메시지 (메시지 당 평균 단어) 분석을 제어 하려면 모델 메서드 제공 .</span><span class="sxs-lookup"><span data-stu-id="cd901-131">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="cd901-132">메시지에서 설명 합니다 `Message` 클래스 (*Data/Message.cs*) 두 가지 속성을 사용 하 여: `Id` (키) 및 `Text` (메시지).</span><span class="sxs-lookup"><span data-stu-id="cd901-132">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="cd901-133">`Text` 속성 필요 하 고 200 자로 제한 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-133">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="cd901-134">메시지를 사용 하 여 저장 됩니다 [Entity Framework의 메모리 내 데이터베이스](/ef/core/providers/in-memory/)&#8224;합니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-134">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="cd901-135">해당 데이터베이스 컨텍스트 클래스의를 DAL (데이터 액세스 계층)를 포함 하는 앱 `AppDbContext` (*Data/AppDbContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="cd901-135">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span> <span data-ttu-id="cd901-136">DAL 메서드 표시 되는 `virtual`, 테스트에서 사용할 메서드를 모의 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-136">The DAL methods are marked `virtual`, which allows mocking the methods for use in the tests.</span></span>
* <span data-ttu-id="cd901-137">데이터베이스 앱 시작 시 비어 있으면 메시지 저장소는 세 개의 메시지를 사용 하 여 초기화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-137">If the database is empty on app startup, the message store is initialized with three messages.</span></span> <span data-ttu-id="cd901-138">이러한 *메시지 시드* 테스트에도 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-138">These *seeded messages* are also used in tests.</span></span>

<span data-ttu-id="cd901-139">&#8224;EF 항목인 [inmemory 테스트](/ef/core/miscellaneous/testing/in-memory), MSTest 사용한 테스트에 대 한 메모리 내 데이터베이스를 사용 하는 방법에 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-139">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="cd901-140">이 항목에서는 사용 된 [xUnit](https://xunit.github.io/) 테스트 프레임 워크입니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-140">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="cd901-141">테스트 개념 다른 테스트 프레임 워크에서 구현 테스트와 유사 하지만 동일 하지입니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-141">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="cd901-142">앱 리포지토리 패턴을 사용 하지 않습니다 하 고 효과적인 예가 없습니다 있지만 합니다 [작업 단위 (UoW) 패턴](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor 페이지는 이러한 패턴을 개발을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-142">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="cd901-143">자세한 내용은 [인프라 지 속성 계층 디자인](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) 하 고 [컨트롤러 논리 테스트](/aspnet/core/mvc/controllers/testing) (샘플 리포지토리 패턴을 구현 하는 데 사용).</span><span class="sxs-lookup"><span data-stu-id="cd901-143">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

## <a name="test-app-organization"></a><span data-ttu-id="cd901-144">테스트 앱 구성</span><span class="sxs-lookup"><span data-stu-id="cd901-144">Test app organization</span></span>

<span data-ttu-id="cd901-145">테스트 앱 내에서 콘솔 앱은는 *tests/RazorPagesTestSample.Tests* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-145">The test app is a console app inside the *tests/RazorPagesTestSample.Tests* folder.</span></span>

| <span data-ttu-id="cd901-146">테스트 앱 폴더</span><span class="sxs-lookup"><span data-stu-id="cd901-146">Test app folder</span></span> | <span data-ttu-id="cd901-147">설명</span><span class="sxs-lookup"><span data-stu-id="cd901-147">Description</span></span> |
| --------------- | ----------- |
| <span data-ttu-id="cd901-148">*UnitTests*</span><span class="sxs-lookup"><span data-stu-id="cd901-148">*UnitTests*</span></span>     | <ul><li><span data-ttu-id="cd901-149">*DataAccessLayerTest.cs* DAL에 대 한 단위 테스트를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-149">*DataAccessLayerTest.cs* contains the unit tests for the DAL.</span></span></li><li><span data-ttu-id="cd901-150">*IndexPageTests.cs* 인덱스 페이지 모델에 대 한 단위 테스트를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-150">*IndexPageTests.cs* contains the unit tests for the Index page model.</span></span></li></ul> |
| <span data-ttu-id="cd901-151">*유틸리티*</span><span class="sxs-lookup"><span data-stu-id="cd901-151">*Utilities*</span></span>     | <span data-ttu-id="cd901-152">포함 된 `TestingDbContextOptions` 데이터베이스를 각 테스트에 대 한 초기 상태로 다시 설정 됩니다 있도록 새 데이터베이스 각 DAL 단위 테스트에 대 한 상황에 맞는 옵션을 만드는 데 사용 되는 메서드.</span><span class="sxs-lookup"><span data-stu-id="cd901-152">Contains the `TestingDbContextOptions` method used to create new database context options for each DAL unit test so that the database is reset to its baseline condition for each test.</span></span> |

<span data-ttu-id="cd901-153">테스트 프레임 워크 [xUnit](https://xunit.github.io/)합니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-153">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="cd901-154">모의 프레임 워크 개체가 [Moq](https://github.com/moq/moq4)합니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-154">The object mocking framework is [Moq](https://github.com/moq/moq4).</span></span>

## <a name="unit-tests-of-the-data-access-layer-dal"></a><span data-ttu-id="cd901-155">단위 테스트 데이터의 DAL (계층)에 액세스</span><span class="sxs-lookup"><span data-stu-id="cd901-155">Unit tests of the data access layer (DAL)</span></span>

<span data-ttu-id="cd901-156">메시지 앱에 포함 된 네 가지 메서드를 사용 하 여는 DAL에는 `AppDbContext` 클래스 (*src/RazorPagesTestSample/Data/AppDbContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="cd901-156">The message app has a DAL with four methods contained in the `AppDbContext` class (*src/RazorPagesTestSample/Data/AppDbContext.cs*).</span></span> <span data-ttu-id="cd901-157">각 메서드 테스트 응용 프로그램에 하나 또는 두 개의 단위 테스트</span><span class="sxs-lookup"><span data-stu-id="cd901-157">Each method has one or two unit tests in the test app.</span></span>

| <span data-ttu-id="cd901-158">DAL 메서드</span><span class="sxs-lookup"><span data-stu-id="cd901-158">DAL method</span></span>               | <span data-ttu-id="cd901-159">함수</span><span class="sxs-lookup"><span data-stu-id="cd901-159">Function</span></span>                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | <span data-ttu-id="cd901-160">가져옵니다를 `List<Message>` 별로 정렬 된 데이터베이스에서의 `Text` 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-160">Obtains a `List<Message>` from the database sorted by the `Text` property.</span></span> |
| `AddMessageAsync`        | <span data-ttu-id="cd901-161">추가 된 `Message` 데이터베이스에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-161">Adds a `Message` to the database.</span></span>                                          |
| `DeleteAllMessagesAsync` | <span data-ttu-id="cd901-162">모든 삭제 `Message` 데이터베이스에서 항목입니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-162">Deletes all `Message` entries from the database.</span></span>                           |
| `DeleteMessageAsync`     | <span data-ttu-id="cd901-163">단일 삭제 `Message` 하 여 데이터베이스에서 `Id`합니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-163">Deletes a single `Message` from the database by `Id`.</span></span>                      |

<span data-ttu-id="cd901-164">DAL의 단위 테스트에 필요한 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 새로 만들 때 `AppDbContext` 각 테스트에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-164">Unit tests of the DAL require [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) when creating a new `AppDbContext` for each test.</span></span> <span data-ttu-id="cd901-165">만드는 한 가지 방법은 합니다 `DbContextOptions` 각 테스트를 사용 하는 것을 [DbContextOptionsBuilder](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptionsbuilder):</span><span class="sxs-lookup"><span data-stu-id="cd901-165">One approach to creating the `DbContextOptions` for each test is to use a [DbContextOptionsBuilder](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptionsbuilder):</span></span>

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="cd901-166">이 방식의 문제는 각 테스트는이 어떤 상태 이전 테스트 왼쪽에서 데이터베이스에 수신 하는 경우</span><span class="sxs-lookup"><span data-stu-id="cd901-166">The problem with this approach is that each test receives the database in whatever state the previous test left it.</span></span> <span data-ttu-id="cd901-167">이 서로 간섭 하지 않는 원자성 단위 테스트를 작성 하려고 할 때 문제가 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-167">This can be problematic when trying to write atomic unit tests that don't interfere with each other.</span></span> <span data-ttu-id="cd901-168">적용할 합니다 `AppDbContext` 각 테스트에 대 한 새 데이터베이스 컨텍스트를 사용 하려면 제공를 `DbContextOptions` 새 서비스 공급자를 기반으로 하는 인스턴스.</span><span class="sxs-lookup"><span data-stu-id="cd901-168">To force the `AppDbContext` to use a new database context for each test, supply a `DbContextOptions` instance that's based on a new service provider.</span></span> <span data-ttu-id="cd901-169">테스트 응용 프로그램을 사용 하는 방법을 보여 줍니다 해당 `Utilities` 클래스 메서드에 `TestingDbContextOptions` (*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*):</span><span class="sxs-lookup"><span data-stu-id="cd901-169">The test app shows how to do this using its `Utilities` class method `TestingDbContextOptions` (*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

<span data-ttu-id="cd901-170">사용 하는 `DbContextOptions` DAL 단위 테스트 하면 각 테스트를 새 데이터베이스 인스턴스를 사용 하 여 원자 단위로 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-170">Using the `DbContextOptions` in the DAL unit tests allows each test to run atomically with a fresh database instance:</span></span>

```csharp
using (var db = new AppDbContext(Utilities.TestingDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="cd901-171">각 테스트 메서드는 `DataAccessLayerTest` 클래스 (*UnitTests/DataAccessLayerTest.cs*) 정렬 Act Assert 비슷한 패턴을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-171">Each test method in the `DataAccessLayerTest` class (*UnitTests/DataAccessLayerTest.cs*) follows a similar Arrange-Act-Assert pattern:</span></span>

1. <span data-ttu-id="cd901-172">정렬 합니다. 테스트에 대해 구성 된 데이터베이스 및/또는 예상된 결과 정의</span><span class="sxs-lookup"><span data-stu-id="cd901-172">Arrange: The database is configured for the test and/or the expected outcome is defined.</span></span>
1. <span data-ttu-id="cd901-173">Act: 테스트 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-173">Act: The test is executed.</span></span>
1. <span data-ttu-id="cd901-174">어설션 됩니다. 테스트 결과 성공 인지 확인 하려면 어설션이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-174">Assert: Assertions are made to determine if the test result is a success.</span></span>

<span data-ttu-id="cd901-175">예를 들어 합니다 `DeleteMessageAsync` 로 식별 되는 단일 메시지를 제거 하는 것에 대 한 메서드는 해당 `Id` (*src/RazorPagesTestSample/Data/AppDbContext.cs*):</span><span class="sxs-lookup"><span data-stu-id="cd901-175">For example, the `DeleteMessageAsync` method is responsible for removing a single message identified by its `Id` (*src/RazorPagesTestSample/Data/AppDbContext.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

<span data-ttu-id="cd901-176">이 메서드에 대 한 두 테스트가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-176">There are two tests for this method.</span></span> <span data-ttu-id="cd901-177">하나의 테스트 메서드는 메시지는 데이터베이스에 있는 경우 메시지를 삭제를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-177">One test checks that the method deletes a message when the message is present in the database.</span></span> <span data-ttu-id="cd901-178">데이터베이스는 경우 변경 되지 않는 다른 메서드 테스트 메시지 `Id` 삭제 존재 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-178">The other method tests that the database doesn't change if the message `Id` for deletion doesn't exist.</span></span> <span data-ttu-id="cd901-179">`DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-179">The `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` method is shown below:</span></span>

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="cd901-180">먼저, 메서드는 작업 단계에 대 한 준비 이루어집니다 정렬 단계를 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-180">First, the method performs the Arrange step, where preparation for the Act step takes place.</span></span> <span data-ttu-id="cd901-181">시드 메시지를 얻고에 보관 된 `seedMessages`합니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-181">The seeding messages are obtained and held in `seedMessages`.</span></span> <span data-ttu-id="cd901-182">시드 메시지는 데이터베이스에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-182">The seeding messages are saved into the database.</span></span> <span data-ttu-id="cd901-183">메시지를 `Id` 의 `1` 삭제에 대해 설정 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-183">The message with an `Id` of `1` is set for deletion.</span></span> <span data-ttu-id="cd901-184">경우는 `DeleteMessageAsync` 메서드가 실행 되 고, 필요한 메시지의 모든 메시지를 제외 하 고 있어야를 `Id` 의 `1`합니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-184">When the `DeleteMessageAsync` method is executed, the expected messages should have all of the messages except for the one with an `Id` of `1`.</span></span> <span data-ttu-id="cd901-185">`expectedMessages` 변수가 예상된 결과 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-185">The `expectedMessages` variable represents this expected outcome.</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="cd901-186">메서드는 역할: 합니다 `DeleteMessageAsync` 전달 메서드를 실행 합니다 `recId` 의 `1`:</span><span class="sxs-lookup"><span data-stu-id="cd901-186">The method acts: The `DeleteMessageAsync` method is executed passing in the `recId` of `1`:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

<span data-ttu-id="cd901-187">마지막으로 메서드를 가져옵니다 합니다 `Messages` 컨텍스트에서 비교는 `expectedMessages` 둘이 같으면는 어설션:</span><span class="sxs-lookup"><span data-stu-id="cd901-187">Finally, the method obtains the `Messages` from the context and compares it to the `expectedMessages` asserting that the two are equal:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

<span data-ttu-id="cd901-188">비교를 위해 두 `List<Message>` 동일 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-188">In order to compare that the two `List<Message>` are the same:</span></span>

* <span data-ttu-id="cd901-189">메시지의 기준으로 정렬 된 `Id`합니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-189">The messages are ordered by `Id`.</span></span>
* <span data-ttu-id="cd901-190">메시지 쌍의 비교는 `Text` 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-190">Message pairs are compared on the `Text` property.</span></span>

<span data-ttu-id="cd901-191">비슷한 테스트 메서드, `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` 존재 하지 않는 메시지를 삭제 하는 결과 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-191">A similar test method, `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` checks the result of attempting to delete a message that doesn't exist.</span></span> <span data-ttu-id="cd901-192">이 경우 데이터베이스에서 예상된 메시지 같아야 후 실제 메시지는 `DeleteMessageAsync` 메서드를 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-192">In this case, the expected messages in the database should be equal to the actual messages after the `DeleteMessageAsync` method is executed.</span></span> <span data-ttu-id="cd901-193">데이터베이스의 내용이 변경 되지 않았습니다 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-193">There should be no change to the database's content:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a><span data-ttu-id="cd901-194">페이지 모델 메서드의 단위 테스트</span><span class="sxs-lookup"><span data-stu-id="cd901-194">Unit tests of the page model methods</span></span>

<span data-ttu-id="cd901-195">다른 단위 테스트 집합의 페이지 모델 메서드 테스트 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-195">Another set of unit tests is responsible for tests of page model methods.</span></span> <span data-ttu-id="cd901-196">메시지 앱에서 인덱스 페이지 모델에서 발견 되는 `IndexModel` 클래스의 *src/RazorPagesTestSample/Pages/Index.cshtml.cs*합니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-196">In the message app, the Index page models are found in the `IndexModel` class in *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.</span></span>

| <span data-ttu-id="cd901-197">페이지 모델 메서드</span><span class="sxs-lookup"><span data-stu-id="cd901-197">Page model method</span></span> | <span data-ttu-id="cd901-198">함수</span><span class="sxs-lookup"><span data-stu-id="cd901-198">Function</span></span> |
| ----------------- | -------- |
| `OnGetAsync` | <span data-ttu-id="cd901-199">사용 하 여 UI에 대 한 DAL에서 메시지를 가져옵니다는 `GetMessagesAsync` 메서드.</span><span class="sxs-lookup"><span data-stu-id="cd901-199">Obtains the messages from the DAL for the UI using the `GetMessagesAsync` method.</span></span> |
| `OnPostAddMessageAsync` | <span data-ttu-id="cd901-200">경우는 `ModelState` 유효, 호출 `AddMessageAsync` 데이터베이스로 메시지를 추가 하려면.</span><span class="sxs-lookup"><span data-stu-id="cd901-200">If the `ModelState` is valid, calls `AddMessageAsync` to add a message to the database.</span></span> |
| `OnPostDeleteAllMessagesAsync` | <span data-ttu-id="cd901-201">호출 `DeleteAllMessagesAsync` 모든 데이터베이스에 메시지를 삭제 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-201">Calls `DeleteAllMessagesAsync` to delete all of the messages in the database.</span></span> |
| `OnPostDeleteMessageAsync` | <span data-ttu-id="cd901-202">실행 `DeleteMessageAsync` 사용 하 여 메시지를 삭제 하는 `Id` 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-202">Executes `DeleteMessageAsync` to delete a message with the `Id` specified.</span></span> |
| `OnPostAnalyzeMessagesAsync` | <span data-ttu-id="cd901-203">하나 이상의 메시지를 데이터베이스에 있는 경우 메시지당 단어의 평균을 계산 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-203">If one or more messages are in the database, calculates the average number of words per message.</span></span> |

<span data-ttu-id="cd901-204">페이지 모델 메서드 테스트에서 7 개의 테스트를 사용 하 여 `IndexPageTests` 클래스 (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*).</span><span class="sxs-lookup"><span data-stu-id="cd901-204">The page model methods are tested using seven tests in the `IndexPageTests` class (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*).</span></span> <span data-ttu-id="cd901-205">테스트는 친숙 한 정렬 Assert Act 패턴을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-205">The tests use the familiar Arrange-Assert-Act pattern.</span></span> <span data-ttu-id="cd901-206">이러한 테스트에 집중 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-206">These tests focus on:</span></span>

* <span data-ttu-id="cd901-207">메서드 올바른 동작을 수행 하는 경우 결정 때는 `ModelState` 올바르지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-207">Determining if the methods follow the correct behavior when the `ModelState` is invalid.</span></span>
* <span data-ttu-id="cd901-208">올바른 생성 메서드 확인 `IActionResult`합니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-208">Confirming the methods produce the correct `IActionResult`.</span></span>
* <span data-ttu-id="cd901-209">속성 값 할당 내용이 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-209">Checking that property value assignments are made correctly.</span></span>

<span data-ttu-id="cd901-210">이 그룹의 테스트에는 종종 페이지 모델 메서드 실행 되는 작업 단계에 대 한 예상 되는 데이터를 생성 하기 위해 DAL의 메서드를 모방 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-210">This group of tests often mock the methods of the DAL to produce expected data for the Act step where a page model method is executed.</span></span> <span data-ttu-id="cd901-211">예를 들어, 합니다 `GetMessagesAsync` 메서드는 `AppDbContext` 출력을 생성 하기 위해 모의 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-211">For example, the `GetMessagesAsync` method of the `AppDbContext` is mocked to produce output.</span></span> <span data-ttu-id="cd901-212">이 메서드를 실행 하는 페이지 모델 메서드를 모의 결과 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-212">When a page model method executes this method, the mock returns the result.</span></span> <span data-ttu-id="cd901-213">데이터를 데이터베이스에서 가져와야 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-213">The data doesn't come from the database.</span></span> <span data-ttu-id="cd901-214">이 DAL을 사용 하 여 페이지 모델 테스트에서에 대 한 예측 가능 하 고 신뢰할 수 있는 테스트 조건을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-214">This creates predictable, reliable test conditions for using the DAL in the page model tests.</span></span>

<span data-ttu-id="cd901-215">합니다 `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` 테스트 보여 줍니다 방법을 `GetMessagesAsync` 메서드는 페이지 모델에 대 한 모의:</span><span class="sxs-lookup"><span data-stu-id="cd901-215">The `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` test shows how the `GetMessagesAsync` method is mocked for the page model:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

<span data-ttu-id="cd901-216">경우는 `OnGetAsync` 메서드는 작업 단계에서 실행 되 고, 페이지 모델의 호출 `GetMessagesAsync` 메서드.</span><span class="sxs-lookup"><span data-stu-id="cd901-216">When the `OnGetAsync` method is executed in the Act step, it calls the page model's `GetMessagesAsync` method.</span></span>

<span data-ttu-id="cd901-217">단위 테스트 작업 단계 (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):</span><span class="sxs-lookup"><span data-stu-id="cd901-217">Unit test Act step (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="cd901-218">`IndexPage` 페이지 모델 `OnGetAsync` 메서드 (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="cd901-218">`IndexPage` page model's `OnGetAsync` method (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

<span data-ttu-id="cd901-219">`GetMessagesAsync` DAL 메서드가이 메서드 호출에 대 한 결과 반환 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-219">The `GetMessagesAsync` method in the DAL doesn't return the result for this method call.</span></span> <span data-ttu-id="cd901-220">모의 버전의 메서드 결과 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-220">The mocked version of the method returns the result.</span></span>

<span data-ttu-id="cd901-221">에 `Assert` 단계에서 실제 메시지 (`actualMessages`)에서 할당 됩니다는 `Messages` 페이지 모델의 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-221">In the `Assert` step, the actual messages (`actualMessages`) are assigned from the `Messages` property of the page model.</span></span> <span data-ttu-id="cd901-222">메시지 할당 된 경우에 형식 검사를 수행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-222">A type check is also performed when the messages are assigned.</span></span> <span data-ttu-id="cd901-223">예상 및 실제 메시지를 비교 하 여 해당 `Text` 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-223">The expected and actual messages are compared by their `Text` properties.</span></span> <span data-ttu-id="cd901-224">테스트 임을 어설션 두 `List<Message>` 인스턴스가 동일한 메시지를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-224">The test asserts that the two `List<Message>` instances contain the same messages.</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

<span data-ttu-id="cd901-225">이 그룹의 다른 테스트 페이지를 포함 하는 모델 개체 만들기를 `DefaultHttpContext`는 `ModelStateDictionary`, `ActionContext` 설정 하는 `PageContext`, `ViewDataDictionary`, 및 `PageContext`합니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-225">Other tests in this group create page model objects that include the `DefaultHttpContext`, the `ModelStateDictionary`, an `ActionContext` to establish the `PageContext`, a `ViewDataDictionary`, and a `PageContext`.</span></span> <span data-ttu-id="cd901-226">이러한 테스트를 수행 하는에 유용 합니다.</span><span class="sxs-lookup"><span data-stu-id="cd901-226">These are useful in conducting tests.</span></span> <span data-ttu-id="cd901-227">메시지 앱을 설정 하는 예를 들어를 `ModelState` 오류로 `AddModelError` 있는지 여부를 확인 하려면 유효한 `PageResult` 이 반환 됩니다 `OnPostAddMessageAsync` 실행:</span><span class="sxs-lookup"><span data-stu-id="cd901-227">For example, the message app establishes a `ModelState` error with `AddModelError` to check that a valid `PageResult` is returned when `OnPostAddMessageAsync` is executed:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a><span data-ttu-id="cd901-228">추가 자료</span><span class="sxs-lookup"><span data-stu-id="cd901-228">Additional resources</span></span>

* [<span data-ttu-id="cd901-229">Dotnet test 및 xUnit을 사용 하 여.NET Core에서 C# 테스트 단위</span><span class="sxs-lookup"><span data-stu-id="cd901-229">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* [<span data-ttu-id="cd901-230">테스트 컨트롤러</span><span class="sxs-lookup"><span data-stu-id="cd901-230">Test controllers</span></span>](xref:mvc/controllers/testing)
* <span data-ttu-id="cd901-231">[코드 단위 테스트](/visualstudio/test/unit-test-your-code) (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="cd901-231">[Unit Test Your Code](/visualstudio/test/unit-test-your-code) (Visual Studio)</span></span>
* [<span data-ttu-id="cd901-232">통합 테스트</span><span class="sxs-lookup"><span data-stu-id="cd901-232">Integration tests</span></span>](xref:test/integration-tests)
* [<span data-ttu-id="cd901-233">xUnit.net</span><span class="sxs-lookup"><span data-stu-id="cd901-233">xUnit.net</span></span>](https://xunit.github.io/)
* [<span data-ttu-id="cd901-234">XUnit.net (.NET core/asp.net Core)를 사용 하 여 시작</span><span class="sxs-lookup"><span data-stu-id="cd901-234">Getting started with xUnit.net (.NET Core/ASP.NET Core)</span></span>](https://xunit.github.io/docs/getting-started-dotnet-core)
* [<span data-ttu-id="cd901-235">Moq</span><span class="sxs-lookup"><span data-stu-id="cd901-235">Moq</span></span>](https://github.com/moq/moq4)
* [<span data-ttu-id="cd901-236">Moq 빠른 시작</span><span class="sxs-lookup"><span data-stu-id="cd901-236">Moq Quickstart</span></span>](https://github.com/Moq/moq4/wiki/Quickstart)
