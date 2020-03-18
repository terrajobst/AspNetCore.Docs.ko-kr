---
title: ASP.NET Core의 Razor Pages 단위 테스트
author: rick-anderson
description: Razor Pages 앱용 단위 테스트를 만드는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/14/2019
uid: test/razor-pages-tests
ms.openlocfilehash: 0e217b6b7f15519a3da44f5d074cf80fa96a3b3a
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78649575"
---
# <a name="razor-pages-unit-tests-in-aspnet-core"></a><span data-ttu-id="b71fe-103">ASP.NET Core의 Razor Pages 단위 테스트</span><span class="sxs-lookup"><span data-stu-id="b71fe-103">Razor Pages unit tests in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b71fe-104">ASP.NET Core는 Razor Pages 앱의 단위 테스트를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-104">ASP.NET Core supports unit tests of Razor Pages apps.</span></span> <span data-ttu-id="b71fe-105">DAL(데이터 액세스 계층) 및 페이지 모델을 테스트하여 다음을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-105">Tests of the data access layer (DAL) and page models help ensure:</span></span>

* <span data-ttu-id="b71fe-106">Razor Pages 앱의 부분은 앱 생성 중에 별도로, 하나의 단위로 함께 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-106">Parts of a Razor Pages app work independently and together as a unit during app construction.</span></span>
* <span data-ttu-id="b71fe-107">클래스 및 메서드의 책임 범위는 제한되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-107">Classes and methods have limited scopes of responsibility.</span></span>
* <span data-ttu-id="b71fe-108">앱이 동작하는 방식에 대한 추가 설명서가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-108">Additional documentation exists on how the app should behave.</span></span>
* <span data-ttu-id="b71fe-109">코드 업데이트로 인해 오류가 발생하게 되는 회귀는 자동화된 빌드 및 배포 중에 발견됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-109">Regressions, which are errors brought about by updates to the code, are found during automated building and deployment.</span></span>

<span data-ttu-id="b71fe-110">이 항목에서는 Razor Pages 앱 및 단위 테스트에 대해 기본적으로 이해하고 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-110">This topic assumes that you have a basic understanding of Razor Pages apps and unit tests.</span></span> <span data-ttu-id="b71fe-111">Razor Pages 앱 또는 테스트 개념에 익숙하지 않은 경우 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b71fe-111">If you're unfamiliar with Razor Pages apps or test concepts, see the following topics:</span></span>

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [<span data-ttu-id="b71fe-112">dotnet 테스트 및 xUnit을 사용하여 .NET Core에서 C# 단위 테스트</span><span class="sxs-lookup"><span data-stu-id="b71fe-112">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

<span data-ttu-id="b71fe-113">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b71fe-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b71fe-114">샘플 프로젝트는 다음 두 앱으로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-114">The sample project is composed of two apps:</span></span>

| <span data-ttu-id="b71fe-115">앱</span><span class="sxs-lookup"><span data-stu-id="b71fe-115">App</span></span>         | <span data-ttu-id="b71fe-116">프로젝트 폴더</span><span class="sxs-lookup"><span data-stu-id="b71fe-116">Project folder</span></span>                     | <span data-ttu-id="b71fe-117">설명</span><span class="sxs-lookup"><span data-stu-id="b71fe-117">Description</span></span> |
| ----------- | ---------------------------------- | ----------- |
| <span data-ttu-id="b71fe-118">메시지 앱</span><span class="sxs-lookup"><span data-stu-id="b71fe-118">Message app</span></span> | <span data-ttu-id="b71fe-119">*src/RazorPagesTestSample*</span><span class="sxs-lookup"><span data-stu-id="b71fe-119">*src/RazorPagesTestSample*</span></span>         | <span data-ttu-id="b71fe-120">사용자가 메시지를 추가하고, 메시지 하나를 삭제하고, 모든 메시지를 삭제하고, 메시지를 분석할 수 있도록 허용합니다(메시지당 평균 단어 수 확인).</span><span class="sxs-lookup"><span data-stu-id="b71fe-120">Allows a user to add a message, delete one message, delete all messages, and analyze messages (find the average number of words per message).</span></span> |
| <span data-ttu-id="b71fe-121">앱 테스트</span><span class="sxs-lookup"><span data-stu-id="b71fe-121">Test app</span></span>    | <span data-ttu-id="b71fe-122">*tests/RazorPagesTestSample.Tests*</span><span class="sxs-lookup"><span data-stu-id="b71fe-122">*tests/RazorPagesTestSample.Tests*</span></span> | <span data-ttu-id="b71fe-123">메시지 앱의 DAL 및 인덱스 페이지 모델을 단위 테스트하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-123">Used to unit test the DAL and Index page model of the message app.</span></span> |

<span data-ttu-id="b71fe-124">[Visual Studio](/visualstudio/test/unit-test-your-code) 또는 [Mac용 Visual Studio](/dotnet/core/tutorials/using-on-mac-vs-full-solution)와 같은 IDE의 기본 제공 테스트 기능을 사용하여 테스트를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-124">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](/visualstudio/test/unit-test-your-code) or [Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution).</span></span> <span data-ttu-id="b71fe-125">[Visual Studio Code](https://code.visualstudio.com/) 또는 명령줄을 사용하는 경우 *tests/RazorPagesTestSample.Tests* 폴더의 명령 프롬프트에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-125">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesTestSample.Tests* folder:</span></span>

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a><span data-ttu-id="b71fe-126">메시지 앱 구성</span><span class="sxs-lookup"><span data-stu-id="b71fe-126">Message app organization</span></span>

<span data-ttu-id="b71fe-127">메시지 앱은 다음과 같은 특징을 가진 Razor Pages 메시지 시스템입니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-127">The message app is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="b71fe-128">앱의 인덱스 페이지(*Pages/Index.cshtml* 및 *Pages/Index.cshtml.cs*)는 메시지의 추가, 삭제 및 분석을 제어하기 위한 UI 및 페이지 모델 메서드를 제공합니다(메시지당 평균 단어 수 확인).</span><span class="sxs-lookup"><span data-stu-id="b71fe-128">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (find the average number of words per message).</span></span>
* <span data-ttu-id="b71fe-129">메시지는 `Id`(키) 및 `Text`(메시지)의 두 가지 속성을 사용하여 `Message` 클래스(*Data/Message.cs*)에서 설명됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-129">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="b71fe-130">`Text` 속성은 필수이며 200자로 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-130">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="b71fe-131">메시지는 [Entity Framework의 메모리 내 데이터베이스](/ef/core/providers/in-memory/)를 사용하여 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-131">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="b71fe-132">앱은 데이터베이스 컨텍스트 클래스 `AppDbContext`(*Data/AppDbContext*)에 DAL을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-132">The app contains a DAL in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span> <span data-ttu-id="b71fe-133">DAL 메서드는 `virtual`로 표시되므로 테스트에서 메서드를 모의로 사용해볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-133">The DAL methods are marked `virtual`, which allows mocking the methods for use in the tests.</span></span>
* <span data-ttu-id="b71fe-134">앱 시작 시 데이터베이스가 비어 있는 경우 메시지 저장소는 세 개의 메시지로 초기화됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-134">If the database is empty on app startup, the message store is initialized with three messages.</span></span> <span data-ttu-id="b71fe-135">이러한 *시드된 메시지*는 테스트에서도 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-135">These *seeded messages* are also used in tests.</span></span>

<span data-ttu-id="b71fe-136">EF 항목 [InMemory로 테스트](/ef/core/miscellaneous/testing/in-memory)에서는 MSTest를 사용하여 테스트에 메모리 내 데이터베이스를 사용하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-136">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="b71fe-137">이 항목에서는 [xUnit](https://xunit.github.io/) 테스트 프레임워크를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-137">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="b71fe-138">여러 테스트 프레임워크의 테스트 개념 및 테스트 구현은 비슷하지만 동일하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-138">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="b71fe-139">샘플 앱은 리포지토리 패턴을 사용하지 않고 [UoW(작업 단위) 패턴](https://martinfowler.com/eaaCatalog/unitOfWork.html)의 효과적인 예가 아니지만 Razor Pages는 이러한 개발 패턴을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-139">Although the sample app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="b71fe-140">자세한 내용은 [인프라 지속성 계층 디자인](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) 및 <xref:mvc/controllers/testing>(샘플에서 리포지토리 패턴 구현)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b71fe-140">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and <xref:mvc/controllers/testing> (the sample implements the repository pattern).</span></span>

## <a name="test-app-organization"></a><span data-ttu-id="b71fe-141">테스트 앱 구성</span><span class="sxs-lookup"><span data-stu-id="b71fe-141">Test app organization</span></span>

<span data-ttu-id="b71fe-142">테스트 앱은 *tests/RazorPagesTestSample.Tests* 폴더에 있는 콘솔 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-142">The test app is a console app inside the *tests/RazorPagesTestSample.Tests* folder.</span></span>

| <span data-ttu-id="b71fe-143">테스트 앱 폴더</span><span class="sxs-lookup"><span data-stu-id="b71fe-143">Test app folder</span></span> | <span data-ttu-id="b71fe-144">설명</span><span class="sxs-lookup"><span data-stu-id="b71fe-144">Description</span></span> |
| --------------- | ----------- |
| <span data-ttu-id="b71fe-145">*UnitTests*</span><span class="sxs-lookup"><span data-stu-id="b71fe-145">*UnitTests*</span></span>     | <ul><li><span data-ttu-id="b71fe-146">*DataAccessLayerTest.cs*에는 DAL에 대한 단위 테스트가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-146">*DataAccessLayerTest.cs* contains the unit tests for the DAL.</span></span></li><li><span data-ttu-id="b71fe-147">*IndexPageTests.cs*에는 인덱스 페이지 모델에 대한 단위 테스트가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-147">*IndexPageTests.cs* contains the unit tests for the Index page model.</span></span></li></ul> |
| <span data-ttu-id="b71fe-148">*유틸리티*</span><span class="sxs-lookup"><span data-stu-id="b71fe-148">*Utilities*</span></span>     | <span data-ttu-id="b71fe-149">데이터베이스가 각 테스트에 대한 기준 조건으로 다시 설정되도록 하기 위해 각 DAL 단위 테스트에 대한 새 데이터베이스 컨텍스트 옵션을 만드는 데 사용되는 `TestDbContextOptions` 메서드가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-149">Contains the `TestDbContextOptions` method used to create new database context options for each DAL unit test so that the database is reset to its baseline condition for each test.</span></span> |

<span data-ttu-id="b71fe-150">테스트 프레임워크는 [xUnit](https://xunit.github.io/)입니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-150">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="b71fe-151">개체 모의 프레임워크는 [Moq](https://github.com/moq/moq4)입니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-151">The object mocking framework is [Moq](https://github.com/moq/moq4).</span></span>

## <a name="unit-tests-of-the-data-access-layer-dal"></a><span data-ttu-id="b71fe-152">DAL(데이터 액세스 계층)의 단위 테스트</span><span class="sxs-lookup"><span data-stu-id="b71fe-152">Unit tests of the data access layer (DAL)</span></span>

<span data-ttu-id="b71fe-153">메시지 앱의 `AppDbContext` 클래스(*src/RazorPagesTestSample/Data/AppDbContext*)에는 4가지 메서드를 포함하는 DAL이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-153">The message app has a DAL with four methods contained in the `AppDbContext` class (*src/RazorPagesTestSample/Data/AppDbContext.cs*).</span></span> <span data-ttu-id="b71fe-154">테스트 앱의 각 메서드에는 하나 또는 두 개의 단위 테스트가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-154">Each method has one or two unit tests in the test app.</span></span>

| <span data-ttu-id="b71fe-155">DAL 메서드</span><span class="sxs-lookup"><span data-stu-id="b71fe-155">DAL method</span></span>               | <span data-ttu-id="b71fe-156">기능</span><span class="sxs-lookup"><span data-stu-id="b71fe-156">Function</span></span>                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | <span data-ttu-id="b71fe-157">`Text` 속성을 기준으로 정렬된 데이터베이스의 `List<Message>`를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-157">Obtains a `List<Message>` from the database sorted by the `Text` property.</span></span> |
| `AddMessageAsync`        | <span data-ttu-id="b71fe-158">데이터베이스에 `Message`를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-158">Adds a `Message` to the database.</span></span>                                          |
| `DeleteAllMessagesAsync` | <span data-ttu-id="b71fe-159">데이터베이스에서 모든 `Message` 항목을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-159">Deletes all `Message` entries from the database.</span></span>                           |
| `DeleteMessageAsync`     | <span data-ttu-id="b71fe-160">데이터베이스에서 `Id`별로 단일 `Message`를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-160">Deletes a single `Message` from the database by `Id`.</span></span>                      |

<span data-ttu-id="b71fe-161">각 테스트에 대해 새 `AppDbContext`를 만들 때 DAL의 단위 테스트에는 <xref:Microsoft.EntityFrameworkCore.DbContextOptions>가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-161">Unit tests of the DAL require <xref:Microsoft.EntityFrameworkCore.DbContextOptions> when creating a new `AppDbContext` for each test.</span></span> <span data-ttu-id="b71fe-162">각 테스트에 대해 `DbContextOptions`를 만드는 한 가지 방법은 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>를 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-162">One approach to creating the `DbContextOptions` for each test is to use a <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>:</span></span>

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="b71fe-163">이 방법의 문제는 이전 테스트가 어떤 상태이든 관계없이 각 테스트가 데이터베이스를 수신한다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-163">The problem with this approach is that each test receives the database in whatever state the previous test left it.</span></span> <span data-ttu-id="b71fe-164">서로 방해하지 않는 원자 단위 테스트를 작성하려고 할 때 문제가 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-164">This can be problematic when trying to write atomic unit tests that don't interfere with each other.</span></span> <span data-ttu-id="b71fe-165">`AppDbContext`에서 각 테스트에 대해 강제로 새 데이터베이스 컨텍스트를 사용하도록 하려면 새 서비스 공급자를 기준으로 하는 `DbContextOptions` 인스턴스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-165">To force the `AppDbContext` to use a new database context for each test, supply a `DbContextOptions` instance that's based on a new service provider.</span></span> <span data-ttu-id="b71fe-166">테스트 앱은 해당 `Utilities` 클래스 메서드`TestDbContextOptions`(*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*)를 사용하여 이 작업을 수행하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-166">The test app shows how to do this using its `Utilities` class method `TestDbContextOptions` (*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

<span data-ttu-id="b71fe-167">DAL 단위 테스트에서 `DbContextOptions`를 사용하면 각 테스트는 새 데이터베이스 인스턴스에서 원자 단위로 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-167">Using the `DbContextOptions` in the DAL unit tests allows each test to run atomically with a fresh database instance:</span></span>

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="b71fe-168">`DataAccessLayerTest` 클래스(*UnitTests/DataAccessLayerTest.cs*)의 각 테스트 메서드는 비슷한 정렬-실행-어설션 패턴을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-168">Each test method in the `DataAccessLayerTest` class (*UnitTests/DataAccessLayerTest.cs*) follows a similar Arrange-Act-Assert pattern:</span></span>

1. <span data-ttu-id="b71fe-169">정렬: 데이터베이스는 테스트를 위해 구성되고 예상된 결과가 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-169">Arrange: The database is configured for the test and/or the expected outcome is defined.</span></span>
1. <span data-ttu-id="b71fe-170">실행: 테스트가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-170">Act: The test is executed.</span></span>
1. <span data-ttu-id="b71fe-171">어설션: 테스트 결과가 성공인지 여부를 확인하기 위해 어설션이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-171">Assert: Assertions are made to determine if the test result is a success.</span></span>

<span data-ttu-id="b71fe-172">예를 들어, `DeleteMessageAsync` 메서드는 해당 `Id`(*src/RazorPagesTestSample/Data/AppDbContext.cs*)로 식별되는 단일 메시지를 제거하는 일을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-172">For example, the `DeleteMessageAsync` method is responsible for removing a single message identified by its `Id` (*src/RazorPagesTestSample/Data/AppDbContext.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

<span data-ttu-id="b71fe-173">이 메서드에 대한 2가지 테스트가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-173">There are two tests for this method.</span></span> <span data-ttu-id="b71fe-174">한 가지 테스트는 데이터베이스에 메시지가 있는 경우 메서드가 메시지를 삭제하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-174">One test checks that the method deletes a message when the message is present in the database.</span></span> <span data-ttu-id="b71fe-175">다른 메서드는 삭제를 위해 메시지 `Id`가 존재하지 않는 경우 데이터베이스가 변경되지 않는지 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-175">The other method tests that the database doesn't change if the message `Id` for deletion doesn't exist.</span></span> <span data-ttu-id="b71fe-176">`DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-176">The `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` method is shown below:</span></span>

[!code-csharp[](razor-pages-tests/samples_snapshot/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="b71fe-177">첫째, 이 메서드는 실행 단계가 준비되는 정렬 단계를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-177">First, the method performs the Arrange step, where preparation for the Act step takes place.</span></span> <span data-ttu-id="b71fe-178">시드 메시지를 가져온 후 `seedMessages`에 보관합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-178">The seeding messages are obtained and held in `seedMessages`.</span></span> <span data-ttu-id="b71fe-179">시드 메시지는 데이터베이스에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-179">The seeding messages are saved into the database.</span></span> <span data-ttu-id="b71fe-180">`Id`가 `1`인 메시지는 삭제용으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-180">The message with an `Id` of `1` is set for deletion.</span></span> <span data-ttu-id="b71fe-181">`DeleteMessageAsync` 메서드가 실행될 때 예상되는 메시지에는 `Id`가 `1`인 경우를 제외한 모든 메시지가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-181">When the `DeleteMessageAsync` method is executed, the expected messages should have all of the messages except for the one with an `Id` of `1`.</span></span> <span data-ttu-id="b71fe-182">`expectedMessages` 변수는 이 예상 결과를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-182">The `expectedMessages` variable represents this expected outcome.</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="b71fe-183">이 메서드는 다음과 같이 동작합니다. `DeleteMessageAsync` 메서드는 `recId` `1`를 전달하여 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-183">The method acts: The `DeleteMessageAsync` method is executed passing in the `recId` of `1`:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

<span data-ttu-id="b71fe-184">마지막으로, 이 메서드는 컨텍스트에서 `Messages`를 가져온 다음, `expectedMessages`와 비교하여 두 항목이 같다고 어설션합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-184">Finally, the method obtains the `Messages` from the context and compares it to the `expectedMessages` asserting that the two are equal:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

<span data-ttu-id="b71fe-185">두 `List<Message>`가 동일한지 비교하기 위해 다음이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-185">In order to compare that the two `List<Message>` are the same:</span></span>

* <span data-ttu-id="b71fe-186">메시지는 `Id`별로 정렬됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-186">The messages are ordered by `Id`.</span></span>
* <span data-ttu-id="b71fe-187">메시지 쌍의 `Text` 속성이 비교됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-187">Message pairs are compared on the `Text` property.</span></span>

<span data-ttu-id="b71fe-188">유사한 테스트 메서드인 `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound`는 존재하지 않는 메시지를 삭제하려고 시도한 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-188">A similar test method, `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` checks the result of attempting to delete a message that doesn't exist.</span></span> <span data-ttu-id="b71fe-189">이 경우 데이터베이스의 예상 메시지는 `DeleteMessageAsync` 메서드가 실행된 후의 실제 메시지와 같아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-189">In this case, the expected messages in the database should be equal to the actual messages after the `DeleteMessageAsync` method is executed.</span></span> <span data-ttu-id="b71fe-190">데이터베이스의 내용은 다음과 같이 변경되지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-190">There should be no change to the database's content:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a><span data-ttu-id="b71fe-191">페이지 모델 메서드의 단위 테스트</span><span class="sxs-lookup"><span data-stu-id="b71fe-191">Unit tests of the page model methods</span></span>

<span data-ttu-id="b71fe-192">다른 단위 테스트 세트는 페이지 모델 메서드의 테스트를 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-192">Another set of unit tests is responsible for tests of page model methods.</span></span> <span data-ttu-id="b71fe-193">메시지 앱에서 인덱스 페이지 모델은 *src/RazorPagesTestSample/Pages/Index. cshtml*의 `IndexModel` 클래스에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-193">In the message app, the Index page models are found in the `IndexModel` class in *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.</span></span>

| <span data-ttu-id="b71fe-194">페이지 모델 메서드</span><span class="sxs-lookup"><span data-stu-id="b71fe-194">Page model method</span></span> | <span data-ttu-id="b71fe-195">기능</span><span class="sxs-lookup"><span data-stu-id="b71fe-195">Function</span></span> |
| ----------------- | -------- |
| `OnGetAsync` | <span data-ttu-id="b71fe-196">`GetMessagesAsync` 메서드를 사용하여 UI에 대한 DAL에서 메시지를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-196">Obtains the messages from the DAL for the UI using the `GetMessagesAsync` method.</span></span> |
| `OnPostAddMessageAsync` | <span data-ttu-id="b71fe-197">[ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)가 유효한 경우 `AddMessageAsync`를 호출하여 데이터베이스에 메시지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-197">If the [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) is valid, calls `AddMessageAsync` to add a message to the database.</span></span> |
| `OnPostDeleteAllMessagesAsync` | <span data-ttu-id="b71fe-198">`DeleteAllMessagesAsync`를 호출하여 데이터베이스의 모든 메시지를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-198">Calls `DeleteAllMessagesAsync` to delete all of the messages in the database.</span></span> |
| `OnPostDeleteMessageAsync` | <span data-ttu-id="b71fe-199">`DeleteMessageAsync`를 실행하여 지정된 `Id`의 메시지를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-199">Executes `DeleteMessageAsync` to delete a message with the `Id` specified.</span></span> |
| `OnPostAnalyzeMessagesAsync` | <span data-ttu-id="b71fe-200">하나 이상의 메시지가 데이터베이스에 있으면 메시지당 평균 단어 수를 계산합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-200">If one or more messages are in the database, calculates the average number of words per message.</span></span> |

<span data-ttu-id="b71fe-201">페이지 모델 메서드는 `IndexPageTests` 클래스(*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*)의 7가지 테스트를 사용하여 테스트됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-201">The page model methods are tested using seven tests in the `IndexPageTests` class (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*).</span></span> <span data-ttu-id="b71fe-202">테스트는 친숙한 정렬-어셜선-실행 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-202">The tests use the familiar Arrange-Assert-Act pattern.</span></span> <span data-ttu-id="b71fe-203">이러한 테스트는 다음에 중점을 둡니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-203">These tests focus on:</span></span>

* <span data-ttu-id="b71fe-204">[ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)가 잘못된 경우 메서드가 올바른 동작을 따르는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-204">Determining if the methods follow the correct behavior when the [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) is invalid.</span></span>
* <span data-ttu-id="b71fe-205">메서드를 확인하면 올바른 <xref:Microsoft.AspNetCore.Mvc.IActionResult>가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-205">Confirming the methods produce the correct <xref:Microsoft.AspNetCore.Mvc.IActionResult>.</span></span>
* <span data-ttu-id="b71fe-206">해당 속성 값 할당에 대한 확인이 올바르게 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-206">Checking that property value assignments are made correctly.</span></span>

<span data-ttu-id="b71fe-207">이 테스트 그룹은 종종 DAL 메서드의 모의 버전을 만들어 페이지 모델 메서드가 실행되는 실행 단계를 위한 예상 데이터를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-207">This group of tests often mock the methods of the DAL to produce expected data for the Act step where a page model method is executed.</span></span> <span data-ttu-id="b71fe-208">예를 들어, `AppDbContext`의 `GetMessagesAsync` 메서드의 모의 메서드를 만들어 출력을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-208">For example, the `GetMessagesAsync` method of the `AppDbContext` is mocked to produce output.</span></span> <span data-ttu-id="b71fe-209">페이지 모델 메서드에서 이 메서드를 실행하면 모의 버전이 결과를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-209">When a page model method executes this method, the mock returns the result.</span></span> <span data-ttu-id="b71fe-210">이 데이터는 데이터베이스에서 제공되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-210">The data doesn't come from the database.</span></span> <span data-ttu-id="b71fe-211">이렇게 하면 페이지 모델 테스트에서 DAL을 사용하기 위한 예측 가능하고 신뢰할 수 있는 테스트 조건이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-211">This creates predictable, reliable test conditions for using the DAL in the page model tests.</span></span>

<span data-ttu-id="b71fe-212">`OnGetAsync_PopulatesThePageModel_WithAListOfMessages` 테스트는 페이지 모델에 대한 `GetMessagesAsync` 메서드의 모의 버전을 생성하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-212">The `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` test shows how the `GetMessagesAsync` method is mocked for the page model:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

<span data-ttu-id="b71fe-213">실행 단계에서 `OnGetAsync` 메서드를 수행하면 페이지 모델의 `GetMessagesAsync` 메서드가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-213">When the `OnGetAsync` method is executed in the Act step, it calls the page model's `GetMessagesAsync` method.</span></span>

<span data-ttu-id="b71fe-214">단위 테스트 실행 단계(*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):</span><span class="sxs-lookup"><span data-stu-id="b71fe-214">Unit test Act step (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="b71fe-215">`IndexPage` 페이지 모델의 `OnGetAsync` 메서드(*src/RazorPagesTestSample/Pages/Index. cshtml*):</span><span class="sxs-lookup"><span data-stu-id="b71fe-215">`IndexPage` page model's `OnGetAsync` method (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

<span data-ttu-id="b71fe-216">DAL의 `GetMessagesAsync` 메서드는 이 메서드 호출에 대한 결과를 반환하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-216">The `GetMessagesAsync` method in the DAL doesn't return the result for this method call.</span></span> <span data-ttu-id="b71fe-217">이 메서드의 모의 버전이 결과를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-217">The mocked version of the method returns the result.</span></span>

<span data-ttu-id="b71fe-218">`Assert` 단계에서 실제 메시지(`actualMessages`)가 페이지 모델의 `Messages` 속성에서 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-218">In the `Assert` step, the actual messages (`actualMessages`) are assigned from the `Messages` property of the page model.</span></span> <span data-ttu-id="b71fe-219">메시지가 할당될 때 형식 검사도 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-219">A type check is also performed when the messages are assigned.</span></span> <span data-ttu-id="b71fe-220">예상 및 실제 메시지는 해당 `Text` 속성과 비교됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-220">The expected and actual messages are compared by their `Text` properties.</span></span> <span data-ttu-id="b71fe-221">이 테스트는 두 개의 `List<Message>` 인스턴스에 동일한 메시지가 포함되어 있음을 어설션합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-221">The test asserts that the two `List<Message>` instances contain the same messages.</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

<span data-ttu-id="b71fe-222">이 그룹의 다른 테스트에서는 <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>, the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>, <xref:Microsoft.AspNetCore.Mvc.ActionContext>를 포함하는 페이지 모델 개체를 만들어 `PageContext`, `ViewDataDictionary` 및 `PageContext`를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-222">Other tests in this group create page model objects that include the <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>, the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>, an <xref:Microsoft.AspNetCore.Mvc.ActionContext> to establish the `PageContext`, a `ViewDataDictionary`, and a `PageContext`.</span></span> <span data-ttu-id="b71fe-223">이러한 기능은 테스트를 수행하는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-223">These are useful in conducting tests.</span></span> <span data-ttu-id="b71fe-224">예를 들어, 메시지 앱은 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*>로 `ModelState` 오류를 설정하여 `OnPostAddMessageAsync`가 실행될 때 유효한 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>가 반환되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-224">For example, the message app establishes a `ModelState` error with <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> to check that a valid <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> is returned when `OnPostAddMessageAsync` is executed:</span></span>

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a><span data-ttu-id="b71fe-225">추가 자료</span><span class="sxs-lookup"><span data-stu-id="b71fe-225">Additional resources</span></span>

* [<span data-ttu-id="b71fe-226">dotnet 테스트 및 xUnit을 사용하여 .NET Core에서 C# 단위 테스트</span><span class="sxs-lookup"><span data-stu-id="b71fe-226">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* <span data-ttu-id="b71fe-227">[코드 단위 테스트](/visualstudio/test/unit-test-your-code)(Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="b71fe-227">[Unit Test Your Code](/visualstudio/test/unit-test-your-code) (Visual Studio)</span></span>
* <xref:test/integration-tests>
* [<span data-ttu-id="b71fe-228">xUnit.net</span><span class="sxs-lookup"><span data-stu-id="b71fe-228">xUnit.net</span></span>](https://xunit.github.io/)
* [<span data-ttu-id="b71fe-229">Visual Studio for Mac을 사용하여 macOS에서 완전한 .NET Core 솔루션 빌드</span><span class="sxs-lookup"><span data-stu-id="b71fe-229">Building a complete .NET Core solution on macOS using Visual Studio for Mac</span></span>](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [<span data-ttu-id="b71fe-230">xUnit.net 시작: .NET SDK 명령줄에서 .NET Core 사용</span><span class="sxs-lookup"><span data-stu-id="b71fe-230">Getting started with xUnit.net: Using .NET Core with the .NET SDK command line</span></span>](https://xunit.github.io/docs/getting-started-dotnet-core)
* [<span data-ttu-id="b71fe-231">Moq</span><span class="sxs-lookup"><span data-stu-id="b71fe-231">Moq</span></span>](https://github.com/moq/moq4)
* [<span data-ttu-id="b71fe-232">Moq 빠른 시작</span><span class="sxs-lookup"><span data-stu-id="b71fe-232">Moq Quickstart</span></span>](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b71fe-233">ASP.NET Core는 Razor Pages 앱의 단위 테스트를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-233">ASP.NET Core supports unit tests of Razor Pages apps.</span></span> <span data-ttu-id="b71fe-234">DAL(데이터 액세스 계층) 및 페이지 모델을 테스트하여 다음을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-234">Tests of the data access layer (DAL) and page models help ensure:</span></span>

* <span data-ttu-id="b71fe-235">Razor Pages 앱의 부분은 앱 생성 중에 별도로, 하나의 단위로 함께 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-235">Parts of a Razor Pages app work independently and together as a unit during app construction.</span></span>
* <span data-ttu-id="b71fe-236">클래스 및 메서드의 책임 범위는 제한되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-236">Classes and methods have limited scopes of responsibility.</span></span>
* <span data-ttu-id="b71fe-237">앱이 동작하는 방식에 대한 추가 설명서가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-237">Additional documentation exists on how the app should behave.</span></span>
* <span data-ttu-id="b71fe-238">코드 업데이트로 인해 오류가 발생하게 되는 회귀는 자동화된 빌드 및 배포 중에 발견됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-238">Regressions, which are errors brought about by updates to the code, are found during automated building and deployment.</span></span>

<span data-ttu-id="b71fe-239">이 항목에서는 Razor Pages 앱 및 단위 테스트에 대해 기본적으로 이해하고 있다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-239">This topic assumes that you have a basic understanding of Razor Pages apps and unit tests.</span></span> <span data-ttu-id="b71fe-240">Razor Pages 앱 또는 테스트 개념에 익숙하지 않은 경우 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b71fe-240">If you're unfamiliar with Razor Pages apps or test concepts, see the following topics:</span></span>

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [<span data-ttu-id="b71fe-241">dotnet 테스트 및 xUnit을 사용하여 .NET Core에서 C# 단위 테스트</span><span class="sxs-lookup"><span data-stu-id="b71fe-241">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

<span data-ttu-id="b71fe-242">[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b71fe-242">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b71fe-243">샘플 프로젝트는 다음 두 앱으로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-243">The sample project is composed of two apps:</span></span>

| <span data-ttu-id="b71fe-244">앱</span><span class="sxs-lookup"><span data-stu-id="b71fe-244">App</span></span>         | <span data-ttu-id="b71fe-245">프로젝트 폴더</span><span class="sxs-lookup"><span data-stu-id="b71fe-245">Project folder</span></span>                     | <span data-ttu-id="b71fe-246">설명</span><span class="sxs-lookup"><span data-stu-id="b71fe-246">Description</span></span> |
| ----------- | ---------------------------------- | ----------- |
| <span data-ttu-id="b71fe-247">메시지 앱</span><span class="sxs-lookup"><span data-stu-id="b71fe-247">Message app</span></span> | <span data-ttu-id="b71fe-248">*src/RazorPagesTestSample*</span><span class="sxs-lookup"><span data-stu-id="b71fe-248">*src/RazorPagesTestSample*</span></span>         | <span data-ttu-id="b71fe-249">사용자가 메시지를 추가하고, 메시지 하나를 삭제하고, 모든 메시지를 삭제하고, 메시지를 분석할 수 있도록 허용합니다(메시지당 평균 단어 수 확인).</span><span class="sxs-lookup"><span data-stu-id="b71fe-249">Allows a user to add a message, delete one message, delete all messages, and analyze messages (find the average number of words per message).</span></span> |
| <span data-ttu-id="b71fe-250">앱 테스트</span><span class="sxs-lookup"><span data-stu-id="b71fe-250">Test app</span></span>    | <span data-ttu-id="b71fe-251">*tests/RazorPagesTestSample.Tests*</span><span class="sxs-lookup"><span data-stu-id="b71fe-251">*tests/RazorPagesTestSample.Tests*</span></span> | <span data-ttu-id="b71fe-252">메시지 앱의 DAL 및 인덱스 페이지 모델을 단위 테스트하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-252">Used to unit test the DAL and Index page model of the message app.</span></span> |

<span data-ttu-id="b71fe-253">[Visual Studio](/visualstudio/test/unit-test-your-code) 또는 [Mac용 Visual Studio](/dotnet/core/tutorials/using-on-mac-vs-full-solution)와 같은 IDE의 기본 제공 테스트 기능을 사용하여 테스트를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-253">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](/visualstudio/test/unit-test-your-code) or [Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution).</span></span> <span data-ttu-id="b71fe-254">[Visual Studio Code](https://code.visualstudio.com/) 또는 명령줄을 사용하는 경우 *tests/RazorPagesTestSample.Tests* 폴더의 명령 프롬프트에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-254">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesTestSample.Tests* folder:</span></span>

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a><span data-ttu-id="b71fe-255">메시지 앱 구성</span><span class="sxs-lookup"><span data-stu-id="b71fe-255">Message app organization</span></span>

<span data-ttu-id="b71fe-256">메시지 앱은 다음과 같은 특징을 가진 Razor Pages 메시지 시스템입니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-256">The message app is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="b71fe-257">앱의 인덱스 페이지(*Pages/Index.cshtml* 및 *Pages/Index.cshtml.cs*)는 메시지의 추가, 삭제 및 분석을 제어하기 위한 UI 및 페이지 모델 메서드를 제공합니다(메시지당 평균 단어 수 확인).</span><span class="sxs-lookup"><span data-stu-id="b71fe-257">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (find the average number of words per message).</span></span>
* <span data-ttu-id="b71fe-258">메시지는 `Id`(키) 및 `Text`(메시지)의 두 가지 속성을 사용하여 `Message` 클래스(*Data/Message.cs*)에서 설명됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-258">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="b71fe-259">`Text` 속성은 필수이며 200자로 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-259">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="b71fe-260">메시지는 [Entity Framework의 메모리 내 데이터베이스](/ef/core/providers/in-memory/)를 사용하여 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-260">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="b71fe-261">앱은 데이터베이스 컨텍스트 클래스 `AppDbContext`(*Data/AppDbContext*)에 DAL을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-261">The app contains a DAL in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span> <span data-ttu-id="b71fe-262">DAL 메서드는 `virtual`로 표시되므로 테스트에서 메서드를 모의로 사용해볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-262">The DAL methods are marked `virtual`, which allows mocking the methods for use in the tests.</span></span>
* <span data-ttu-id="b71fe-263">앱 시작 시 데이터베이스가 비어 있는 경우 메시지 저장소는 세 개의 메시지로 초기화됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-263">If the database is empty on app startup, the message store is initialized with three messages.</span></span> <span data-ttu-id="b71fe-264">이러한 *시드된 메시지*는 테스트에서도 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-264">These *seeded messages* are also used in tests.</span></span>

<span data-ttu-id="b71fe-265">EF 항목 [InMemory로 테스트](/ef/core/miscellaneous/testing/in-memory)에서는 MSTest를 사용하여 테스트에 메모리 내 데이터베이스를 사용하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-265">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="b71fe-266">이 항목에서는 [xUnit](https://xunit.github.io/) 테스트 프레임워크를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-266">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="b71fe-267">여러 테스트 프레임워크의 테스트 개념 및 테스트 구현은 비슷하지만 동일하지는 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-267">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="b71fe-268">샘플 앱은 리포지토리 패턴을 사용하지 않고 [UoW(작업 단위) 패턴](https://martinfowler.com/eaaCatalog/unitOfWork.html)의 효과적인 예가 아니지만 Razor Pages는 이러한 개발 패턴을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-268">Although the sample app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="b71fe-269">자세한 내용은 [인프라 지속성 계층 디자인](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) 및 <xref:mvc/controllers/testing>(샘플에서 리포지토리 패턴 구현)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b71fe-269">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and <xref:mvc/controllers/testing> (the sample implements the repository pattern).</span></span>

## <a name="test-app-organization"></a><span data-ttu-id="b71fe-270">테스트 앱 구성</span><span class="sxs-lookup"><span data-stu-id="b71fe-270">Test app organization</span></span>

<span data-ttu-id="b71fe-271">테스트 앱은 *tests/RazorPagesTestSample.Tests* 폴더에 있는 콘솔 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-271">The test app is a console app inside the *tests/RazorPagesTestSample.Tests* folder.</span></span>

| <span data-ttu-id="b71fe-272">테스트 앱 폴더</span><span class="sxs-lookup"><span data-stu-id="b71fe-272">Test app folder</span></span> | <span data-ttu-id="b71fe-273">설명</span><span class="sxs-lookup"><span data-stu-id="b71fe-273">Description</span></span> |
| --------------- | ----------- |
| <span data-ttu-id="b71fe-274">*UnitTests*</span><span class="sxs-lookup"><span data-stu-id="b71fe-274">*UnitTests*</span></span>     | <ul><li><span data-ttu-id="b71fe-275">*DataAccessLayerTest.cs*에는 DAL에 대한 단위 테스트가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-275">*DataAccessLayerTest.cs* contains the unit tests for the DAL.</span></span></li><li><span data-ttu-id="b71fe-276">*IndexPageTests.cs*에는 인덱스 페이지 모델에 대한 단위 테스트가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-276">*IndexPageTests.cs* contains the unit tests for the Index page model.</span></span></li></ul> |
| <span data-ttu-id="b71fe-277">*유틸리티*</span><span class="sxs-lookup"><span data-stu-id="b71fe-277">*Utilities*</span></span>     | <span data-ttu-id="b71fe-278">데이터베이스가 각 테스트에 대한 기준 조건으로 다시 설정되도록 하기 위해 각 DAL 단위 테스트에 대한 새 데이터베이스 컨텍스트 옵션을 만드는 데 사용되는 `TestDbContextOptions` 메서드가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-278">Contains the `TestDbContextOptions` method used to create new database context options for each DAL unit test so that the database is reset to its baseline condition for each test.</span></span> |

<span data-ttu-id="b71fe-279">테스트 프레임워크는 [xUnit](https://xunit.github.io/)입니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-279">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="b71fe-280">개체 모의 프레임워크는 [Moq](https://github.com/moq/moq4)입니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-280">The object mocking framework is [Moq](https://github.com/moq/moq4).</span></span>

## <a name="unit-tests-of-the-data-access-layer-dal"></a><span data-ttu-id="b71fe-281">DAL(데이터 액세스 계층)의 단위 테스트</span><span class="sxs-lookup"><span data-stu-id="b71fe-281">Unit tests of the data access layer (DAL)</span></span>

<span data-ttu-id="b71fe-282">메시지 앱의 `AppDbContext` 클래스(*src/RazorPagesTestSample/Data/AppDbContext*)에는 4가지 메서드를 포함하는 DAL이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-282">The message app has a DAL with four methods contained in the `AppDbContext` class (*src/RazorPagesTestSample/Data/AppDbContext.cs*).</span></span> <span data-ttu-id="b71fe-283">테스트 앱의 각 메서드에는 하나 또는 두 개의 단위 테스트가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-283">Each method has one or two unit tests in the test app.</span></span>

| <span data-ttu-id="b71fe-284">DAL 메서드</span><span class="sxs-lookup"><span data-stu-id="b71fe-284">DAL method</span></span>               | <span data-ttu-id="b71fe-285">기능</span><span class="sxs-lookup"><span data-stu-id="b71fe-285">Function</span></span>                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | <span data-ttu-id="b71fe-286">`Text` 속성을 기준으로 정렬된 데이터베이스의 `List<Message>`를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-286">Obtains a `List<Message>` from the database sorted by the `Text` property.</span></span> |
| `AddMessageAsync`        | <span data-ttu-id="b71fe-287">데이터베이스에 `Message`를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-287">Adds a `Message` to the database.</span></span>                                          |
| `DeleteAllMessagesAsync` | <span data-ttu-id="b71fe-288">데이터베이스에서 모든 `Message` 항목을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-288">Deletes all `Message` entries from the database.</span></span>                           |
| `DeleteMessageAsync`     | <span data-ttu-id="b71fe-289">데이터베이스에서 `Id`별로 단일 `Message`를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-289">Deletes a single `Message` from the database by `Id`.</span></span>                      |

<span data-ttu-id="b71fe-290">각 테스트에 대해 새 `AppDbContext`를 만들 때 DAL의 단위 테스트에는 <xref:Microsoft.EntityFrameworkCore.DbContextOptions>가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-290">Unit tests of the DAL require <xref:Microsoft.EntityFrameworkCore.DbContextOptions> when creating a new `AppDbContext` for each test.</span></span> <span data-ttu-id="b71fe-291">각 테스트에 대해 `DbContextOptions`를 만드는 한 가지 방법은 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>를 사용하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-291">One approach to creating the `DbContextOptions` for each test is to use a <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>:</span></span>

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="b71fe-292">이 방법의 문제는 이전 테스트가 어떤 상태이든 관계없이 각 테스트가 데이터베이스를 수신한다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-292">The problem with this approach is that each test receives the database in whatever state the previous test left it.</span></span> <span data-ttu-id="b71fe-293">서로 방해하지 않는 원자 단위 테스트를 작성하려고 할 때 문제가 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-293">This can be problematic when trying to write atomic unit tests that don't interfere with each other.</span></span> <span data-ttu-id="b71fe-294">`AppDbContext`에서 각 테스트에 대해 강제로 새 데이터베이스 컨텍스트를 사용하도록 하려면 새 서비스 공급자를 기준으로 하는 `DbContextOptions` 인스턴스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-294">To force the `AppDbContext` to use a new database context for each test, supply a `DbContextOptions` instance that's based on a new service provider.</span></span> <span data-ttu-id="b71fe-295">테스트 앱은 해당 `Utilities` 클래스 메서드`TestDbContextOptions`(*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*)를 사용하여 이 작업을 수행하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-295">The test app shows how to do this using its `Utilities` class method `TestDbContextOptions` (*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

<span data-ttu-id="b71fe-296">DAL 단위 테스트에서 `DbContextOptions`를 사용하면 각 테스트는 새 데이터베이스 인스턴스에서 원자 단위로 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-296">Using the `DbContextOptions` in the DAL unit tests allows each test to run atomically with a fresh database instance:</span></span>

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

<span data-ttu-id="b71fe-297">`DataAccessLayerTest` 클래스(*UnitTests/DataAccessLayerTest.cs*)의 각 테스트 메서드는 비슷한 정렬-실행-어설션 패턴을 따릅니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-297">Each test method in the `DataAccessLayerTest` class (*UnitTests/DataAccessLayerTest.cs*) follows a similar Arrange-Act-Assert pattern:</span></span>

1. <span data-ttu-id="b71fe-298">정렬: 데이터베이스는 테스트를 위해 구성되고 예상된 결과가 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-298">Arrange: The database is configured for the test and/or the expected outcome is defined.</span></span>
1. <span data-ttu-id="b71fe-299">실행: 테스트가 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-299">Act: The test is executed.</span></span>
1. <span data-ttu-id="b71fe-300">어설션: 테스트 결과가 성공인지 여부를 확인하기 위해 어설션이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-300">Assert: Assertions are made to determine if the test result is a success.</span></span>

<span data-ttu-id="b71fe-301">예를 들어, `DeleteMessageAsync` 메서드는 해당 `Id`(*src/RazorPagesTestSample/Data/AppDbContext.cs*)로 식별되는 단일 메시지를 제거하는 일을 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-301">For example, the `DeleteMessageAsync` method is responsible for removing a single message identified by its `Id` (*src/RazorPagesTestSample/Data/AppDbContext.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

<span data-ttu-id="b71fe-302">이 메서드에 대한 2가지 테스트가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-302">There are two tests for this method.</span></span> <span data-ttu-id="b71fe-303">한 가지 테스트는 데이터베이스에 메시지가 있는 경우 메서드가 메시지를 삭제하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-303">One test checks that the method deletes a message when the message is present in the database.</span></span> <span data-ttu-id="b71fe-304">다른 메서드는 삭제를 위해 메시지 `Id`가 존재하지 않는 경우 데이터베이스가 변경되지 않는지 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-304">The other method tests that the database doesn't change if the message `Id` for deletion doesn't exist.</span></span> <span data-ttu-id="b71fe-305">`DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-305">The `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` method is shown below:</span></span>

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="b71fe-306">첫째, 이 메서드는 실행 단계가 준비되는 정렬 단계를 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-306">First, the method performs the Arrange step, where preparation for the Act step takes place.</span></span> <span data-ttu-id="b71fe-307">시드 메시지를 가져온 후 `seedMessages`에 보관합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-307">The seeding messages are obtained and held in `seedMessages`.</span></span> <span data-ttu-id="b71fe-308">시드 메시지는 데이터베이스에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-308">The seeding messages are saved into the database.</span></span> <span data-ttu-id="b71fe-309">`Id`가 `1`인 메시지는 삭제용으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-309">The message with an `Id` of `1` is set for deletion.</span></span> <span data-ttu-id="b71fe-310">`DeleteMessageAsync` 메서드가 실행될 때 예상되는 메시지에는 `Id`가 `1`인 경우를 제외한 모든 메시지가 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-310">When the `DeleteMessageAsync` method is executed, the expected messages should have all of the messages except for the one with an `Id` of `1`.</span></span> <span data-ttu-id="b71fe-311">`expectedMessages` 변수는 이 예상 결과를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-311">The `expectedMessages` variable represents this expected outcome.</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

<span data-ttu-id="b71fe-312">이 메서드는 다음과 같이 동작합니다. `DeleteMessageAsync` 메서드는 `recId` `1`를 전달하여 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-312">The method acts: The `DeleteMessageAsync` method is executed passing in the `recId` of `1`:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

<span data-ttu-id="b71fe-313">마지막으로, 이 메서드는 컨텍스트에서 `Messages`를 가져온 다음, `expectedMessages`와 비교하여 두 항목이 같다고 어설션합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-313">Finally, the method obtains the `Messages` from the context and compares it to the `expectedMessages` asserting that the two are equal:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

<span data-ttu-id="b71fe-314">두 `List<Message>`가 동일한지 비교하기 위해 다음이 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-314">In order to compare that the two `List<Message>` are the same:</span></span>

* <span data-ttu-id="b71fe-315">메시지는 `Id`별로 정렬됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-315">The messages are ordered by `Id`.</span></span>
* <span data-ttu-id="b71fe-316">메시지 쌍의 `Text` 속성이 비교됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-316">Message pairs are compared on the `Text` property.</span></span>

<span data-ttu-id="b71fe-317">유사한 테스트 메서드인 `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound`는 존재하지 않는 메시지를 삭제하려고 시도한 결과를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-317">A similar test method, `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` checks the result of attempting to delete a message that doesn't exist.</span></span> <span data-ttu-id="b71fe-318">이 경우 데이터베이스의 예상 메시지는 `DeleteMessageAsync` 메서드가 실행된 후의 실제 메시지와 같아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-318">In this case, the expected messages in the database should be equal to the actual messages after the `DeleteMessageAsync` method is executed.</span></span> <span data-ttu-id="b71fe-319">데이터베이스의 내용은 다음과 같이 변경되지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-319">There should be no change to the database's content:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a><span data-ttu-id="b71fe-320">페이지 모델 메서드의 단위 테스트</span><span class="sxs-lookup"><span data-stu-id="b71fe-320">Unit tests of the page model methods</span></span>

<span data-ttu-id="b71fe-321">다른 단위 테스트 세트는 페이지 모델 메서드의 테스트를 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-321">Another set of unit tests is responsible for tests of page model methods.</span></span> <span data-ttu-id="b71fe-322">메시지 앱에서 인덱스 페이지 모델은 *src/RazorPagesTestSample/Pages/Index. cshtml*의 `IndexModel` 클래스에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-322">In the message app, the Index page models are found in the `IndexModel` class in *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.</span></span>

| <span data-ttu-id="b71fe-323">페이지 모델 메서드</span><span class="sxs-lookup"><span data-stu-id="b71fe-323">Page model method</span></span> | <span data-ttu-id="b71fe-324">기능</span><span class="sxs-lookup"><span data-stu-id="b71fe-324">Function</span></span> |
| ----------------- | -------- |
| `OnGetAsync` | <span data-ttu-id="b71fe-325">`GetMessagesAsync` 메서드를 사용하여 UI에 대한 DAL에서 메시지를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-325">Obtains the messages from the DAL for the UI using the `GetMessagesAsync` method.</span></span> |
| `OnPostAddMessageAsync` | <span data-ttu-id="b71fe-326">[ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)가 유효한 경우 `AddMessageAsync`를 호출하여 데이터베이스에 메시지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-326">If the [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) is valid, calls `AddMessageAsync` to add a message to the database.</span></span> |
| `OnPostDeleteAllMessagesAsync` | <span data-ttu-id="b71fe-327">`DeleteAllMessagesAsync`를 호출하여 데이터베이스의 모든 메시지를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-327">Calls `DeleteAllMessagesAsync` to delete all of the messages in the database.</span></span> |
| `OnPostDeleteMessageAsync` | <span data-ttu-id="b71fe-328">`DeleteMessageAsync`를 실행하여 지정된 `Id`의 메시지를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-328">Executes `DeleteMessageAsync` to delete a message with the `Id` specified.</span></span> |
| `OnPostAnalyzeMessagesAsync` | <span data-ttu-id="b71fe-329">하나 이상의 메시지가 데이터베이스에 있으면 메시지당 평균 단어 수를 계산합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-329">If one or more messages are in the database, calculates the average number of words per message.</span></span> |

<span data-ttu-id="b71fe-330">페이지 모델 메서드는 `IndexPageTests` 클래스(*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*)의 7가지 테스트를 사용하여 테스트됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-330">The page model methods are tested using seven tests in the `IndexPageTests` class (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*).</span></span> <span data-ttu-id="b71fe-331">테스트는 친숙한 정렬-어셜선-실행 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-331">The tests use the familiar Arrange-Assert-Act pattern.</span></span> <span data-ttu-id="b71fe-332">이러한 테스트는 다음에 중점을 둡니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-332">These tests focus on:</span></span>

* <span data-ttu-id="b71fe-333">[ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)가 잘못된 경우 메서드가 올바른 동작을 따르는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-333">Determining if the methods follow the correct behavior when the [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) is invalid.</span></span>
* <span data-ttu-id="b71fe-334">메서드를 확인하면 올바른 <xref:Microsoft.AspNetCore.Mvc.IActionResult>가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-334">Confirming the methods produce the correct <xref:Microsoft.AspNetCore.Mvc.IActionResult>.</span></span>
* <span data-ttu-id="b71fe-335">해당 속성 값 할당에 대한 확인이 올바르게 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-335">Checking that property value assignments are made correctly.</span></span>

<span data-ttu-id="b71fe-336">이 테스트 그룹은 종종 DAL 메서드의 모의 버전을 만들어 페이지 모델 메서드가 실행되는 실행 단계를 위한 예상 데이터를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-336">This group of tests often mock the methods of the DAL to produce expected data for the Act step where a page model method is executed.</span></span> <span data-ttu-id="b71fe-337">예를 들어, `AppDbContext`의 `GetMessagesAsync` 메서드의 모의 메서드를 만들어 출력을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-337">For example, the `GetMessagesAsync` method of the `AppDbContext` is mocked to produce output.</span></span> <span data-ttu-id="b71fe-338">페이지 모델 메서드에서 이 메서드를 실행하면 모의 버전이 결과를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-338">When a page model method executes this method, the mock returns the result.</span></span> <span data-ttu-id="b71fe-339">이 데이터는 데이터베이스에서 제공되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-339">The data doesn't come from the database.</span></span> <span data-ttu-id="b71fe-340">이렇게 하면 페이지 모델 테스트에서 DAL을 사용하기 위한 예측 가능하고 신뢰할 수 있는 테스트 조건이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-340">This creates predictable, reliable test conditions for using the DAL in the page model tests.</span></span>

<span data-ttu-id="b71fe-341">`OnGetAsync_PopulatesThePageModel_WithAListOfMessages` 테스트는 페이지 모델에 대한 `GetMessagesAsync` 메서드의 모의 버전을 생성하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-341">The `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` test shows how the `GetMessagesAsync` method is mocked for the page model:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

<span data-ttu-id="b71fe-342">실행 단계에서 `OnGetAsync` 메서드를 수행하면 페이지 모델의 `GetMessagesAsync` 메서드가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-342">When the `OnGetAsync` method is executed in the Act step, it calls the page model's `GetMessagesAsync` method.</span></span>

<span data-ttu-id="b71fe-343">단위 테스트 실행 단계(*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):</span><span class="sxs-lookup"><span data-stu-id="b71fe-343">Unit test Act step (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="b71fe-344">`IndexPage` 페이지 모델의 `OnGetAsync` 메서드(*src/RazorPagesTestSample/Pages/Index. cshtml*):</span><span class="sxs-lookup"><span data-stu-id="b71fe-344">`IndexPage` page model's `OnGetAsync` method (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

<span data-ttu-id="b71fe-345">DAL의 `GetMessagesAsync` 메서드는 이 메서드 호출에 대한 결과를 반환하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-345">The `GetMessagesAsync` method in the DAL doesn't return the result for this method call.</span></span> <span data-ttu-id="b71fe-346">이 메서드의 모의 버전이 결과를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-346">The mocked version of the method returns the result.</span></span>

<span data-ttu-id="b71fe-347">`Assert` 단계에서 실제 메시지(`actualMessages`)가 페이지 모델의 `Messages` 속성에서 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-347">In the `Assert` step, the actual messages (`actualMessages`) are assigned from the `Messages` property of the page model.</span></span> <span data-ttu-id="b71fe-348">메시지가 할당될 때 형식 검사도 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-348">A type check is also performed when the messages are assigned.</span></span> <span data-ttu-id="b71fe-349">예상 및 실제 메시지는 해당 `Text` 속성과 비교됩니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-349">The expected and actual messages are compared by their `Text` properties.</span></span> <span data-ttu-id="b71fe-350">이 테스트는 두 개의 `List<Message>` 인스턴스에 동일한 메시지가 포함되어 있음을 어설션합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-350">The test asserts that the two `List<Message>` instances contain the same messages.</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

<span data-ttu-id="b71fe-351">이 그룹의 다른 테스트에서는 <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>, the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>, <xref:Microsoft.AspNetCore.Mvc.ActionContext>를 포함하는 페이지 모델 개체를 만들어 `PageContext`, `ViewDataDictionary` 및 `PageContext`를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-351">Other tests in this group create page model objects that include the <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>, the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>, an <xref:Microsoft.AspNetCore.Mvc.ActionContext> to establish the `PageContext`, a `ViewDataDictionary`, and a `PageContext`.</span></span> <span data-ttu-id="b71fe-352">이러한 기능은 테스트를 수행하는 데 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-352">These are useful in conducting tests.</span></span> <span data-ttu-id="b71fe-353">예를 들어, 메시지 앱은 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*>로 `ModelState` 오류를 설정하여 `OnPostAddMessageAsync`가 실행될 때 유효한 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>가 반환되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="b71fe-353">For example, the message app establishes a `ModelState` error with <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> to check that a valid <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> is returned when `OnPostAddMessageAsync` is executed:</span></span>

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a><span data-ttu-id="b71fe-354">추가 자료</span><span class="sxs-lookup"><span data-stu-id="b71fe-354">Additional resources</span></span>

* [<span data-ttu-id="b71fe-355">dotnet 테스트 및 xUnit을 사용하여 .NET Core에서 C# 단위 테스트</span><span class="sxs-lookup"><span data-stu-id="b71fe-355">Unit testing C# in .NET Core using dotnet test and xUnit</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* <span data-ttu-id="b71fe-356">[코드 단위 테스트](/visualstudio/test/unit-test-your-code)(Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="b71fe-356">[Unit Test Your Code](/visualstudio/test/unit-test-your-code) (Visual Studio)</span></span>
* <xref:test/integration-tests>
* [<span data-ttu-id="b71fe-357">xUnit.net</span><span class="sxs-lookup"><span data-stu-id="b71fe-357">xUnit.net</span></span>](https://xunit.github.io/)
* [<span data-ttu-id="b71fe-358">Visual Studio for Mac을 사용하여 macOS에서 완전한 .NET Core 솔루션 빌드</span><span class="sxs-lookup"><span data-stu-id="b71fe-358">Building a complete .NET Core solution on macOS using Visual Studio for Mac</span></span>](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [<span data-ttu-id="b71fe-359">xUnit.net 시작: .NET SDK 명령줄에서 .NET Core 사용</span><span class="sxs-lookup"><span data-stu-id="b71fe-359">Getting started with xUnit.net: Using .NET Core with the .NET SDK command line</span></span>](https://xunit.github.io/docs/getting-started-dotnet-core)
* [<span data-ttu-id="b71fe-360">Moq</span><span class="sxs-lookup"><span data-stu-id="b71fe-360">Moq</span></span>](https://github.com/moq/moq4)
* [<span data-ttu-id="b71fe-361">Moq 빠른 시작</span><span class="sxs-lookup"><span data-stu-id="b71fe-361">Moq Quickstart</span></span>](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end
