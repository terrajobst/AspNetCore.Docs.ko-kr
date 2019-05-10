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
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a>Microsoft.Extensions.Logging 2.1을 2.2 또는 3.0에서 마이그레이션

이 문서를 사용 하는 비-ASP.NET Core 응용 프로그램을 마이그레이션하기 위한 일반적인 단계를 간략하게 설명 `Microsoft.Extensions.Logging` 2.1을 2.2 또는 3.0에서.

## <a name="21-to-22"></a>2.1-2.2

수동으로 만들 `ServiceCollection` 호출 `AddLogging`합니다.

2.1 예제:

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

2.2 예제:

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a>2.1-3.0

3.0에서 사용 하 여 `LoggingFactory.Create`입니다.

2.1 예제:

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

3.0 예제:

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a>추가 자료

<xref:fundamentals/logging/index>