---
title: Microsoft.Extensions.Logging 2.1을 2.2 또는 3.0에서 마이그레이션
author: pakrym
description: 2.2 또는 3.0 2.1에서 Microsoft.Extensions.Logging을 사용 하는 비-ASP.NET Core 응용 프로그램을 마이그레이션하는 방법에 알아봅니다.
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: 2519ddc02cee5978483bcaef4341a52aad3ba2a6
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892460"
---
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="43128-103">Microsoft.Extensions.Logging 2.1을 2.2 또는 3.0에서 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="43128-103">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="43128-104">이 문서를 사용 하는 비-ASP.NET Core 응용 프로그램을 마이그레이션하기 위한 일반적인 단계를 간략하게 설명 `Microsoft.Extensions.Logging` 2.1을 2.2 또는 3.0에서.</span><span class="sxs-lookup"><span data-stu-id="43128-104">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="43128-105">2.1-2.2</span><span class="sxs-lookup"><span data-stu-id="43128-105">2.1 to 2.2</span></span>

<span data-ttu-id="43128-106">수동으로 만들 `ServiceCollection` 호출 `AddLogging`합니다.</span><span class="sxs-lookup"><span data-stu-id="43128-106">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="43128-107">2.1 예제:</span><span class="sxs-lookup"><span data-stu-id="43128-107">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="43128-108">2.2 예제:</span><span class="sxs-lookup"><span data-stu-id="43128-108">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="43128-109">2.1-3.0</span><span class="sxs-lookup"><span data-stu-id="43128-109">2.1 to 3.0</span></span>

<span data-ttu-id="43128-110">3.0에서 사용 하 여 `LoggingFactory.Create`입니다.</span><span class="sxs-lookup"><span data-stu-id="43128-110">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="43128-111">2.1 예제:</span><span class="sxs-lookup"><span data-stu-id="43128-111">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="43128-112">3.0 예제:</span><span class="sxs-lookup"><span data-stu-id="43128-112">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="43128-113">추가 자료</span><span class="sxs-lookup"><span data-stu-id="43128-113">Additional resources</span></span>

<xref:fundamentals/logging/index>