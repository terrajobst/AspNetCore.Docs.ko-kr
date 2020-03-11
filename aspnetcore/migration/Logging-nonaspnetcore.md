---
title: Microsoft Extensions. 로깅 2.1에서 2.2 또는 3.0로 마이그레이션
author: pakrym
description: 2\.1에서 2.2 또는 3.0로 non-ASP.NET를 사용 하는 Core 응용 프로그램을 마이그레이션하는 방법에 대해 알아봅니다.
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: 2519ddc02cee5978483bcaef4341a52aad3ba2a6
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651879"
---
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a>Microsoft Extensions. 로깅 2.1에서 2.2 또는 3.0로 마이그레이션

이 문서에서는 `Microsoft.Extensions.Logging`를 사용 하는 non-ASP.NET Core 응용 프로그램을 2.1에서 2.2 또는 3.0로 마이그레이션하는 일반적인 단계를 간략하게 설명 합니다.

## <a name="21-to-22"></a>2.1-2.2

`ServiceCollection`를 수동으로 만들고 `AddLogging`를 호출 합니다.

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

## <a name="21-to-30"></a>2.1 ~ 3.0

3\.0에서는 `LoggingFactory.Create`을 사용 합니다.

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

## <a name="additional-resources"></a>추가 리소스

<xref:fundamentals/logging/index>