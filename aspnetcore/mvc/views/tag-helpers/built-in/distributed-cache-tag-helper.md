---
title: ASP.NET Core의 분산 캐시 태그 도우미
author: pkellner
description: 분산 캐시 태그 도우미를 사용하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
uid: mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper
ms.openlocfilehash: 4e4d383bac67c73bad8b0a31b9ceb9452251761b
ms.sourcegitcommit: 7a40c56bf6a6aaa63a7ee83a2cac9b3a1d77555e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67856195"
---
# <a name="distributed-cache-tag-helper-in-aspnet-core"></a>ASP.NET Core의 분산 캐시 태그 도우미

작성자: [Peter Kellner](https://peterkellner.net) 및 [Luke Latham](https://github.com/guardrex)

분산 캐시 태그 도우미는 콘텐츠를 분산 캐시 원본에 캐싱하여 ASP.NET Core 앱 성능을 획기적으로 개선하는 기능을 제공합니다.

태그 도우미에 대한 개요는 <xref:mvc/views/tag-helpers/intro>를 참조하세요.

분산 캐시 태그 도우미는 캐시 태그 도우미와 동일한 기본 클래스에서 상속합니다. 모든 [캐시 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper) 특성이 분산 태그 도우미에 제공됩니다.

분산 캐시 태그 도우미는 [생성자 주입](xref:fundamentals/dependency-injection#constructor-injection-behavior)을 사용합니다. <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 인터페이스는 분산 캐시 태그 도우미의 생성자에 전달됩니다. `IDistributedCache`의 구체적인 구현이 `Startup.ConfigureServices`(*Startup.cs*)에 생성되지 않은 경우 분산 캐시 태그 도우미는 동일한 메모리 내 공급자를 사용하여 캐시된 데이터를 [캐시 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)로 저장합니다.

## <a name="distributed-cache-tag-helper-attributes"></a>분산 캐시 태그 도우미 특성

### <a name="attributes-shared-with-the-cache-tag-helper"></a>캐시 태그 도우미와 공유되는 특성

* `enabled`
* `expires-on`
* `expires-after`
* `expires-sliding`
* `vary-by-header`
* `vary-by-query`
* `vary-by-route`
* `vary-by-cookie`
* `vary-by-user`
* `vary-by priority`

분산 캐시 태그 도우미는 캐시 태그 도우미와 동일한 클래스에서 상속합니다. 이러한 특성에 대한 설명은 [캐시 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)를 참조하세요.

### <a name="name"></a>name

| 특성 유형 | 예                               |
| -------------- | ------------------------------------- |
| 문자열         | `my-distributed-cache-unique-key-101` |

`name`은 필수입니다. `name` 특성은 저장된 각 캐시 인스턴스의 키로 사용됩니다. Razor 페이지의 Razor 페이지 이름 및 위치를 기준으로 각 인스턴스에 캐시 키를 할당하는 캐시 태그 도우미와는 달리, 분산 캐시 태그 도우미는 `name` 특성의 키만을 기준으로 합니다.

예제:

```cshtml
<distributed-cache name="my-distributed-cache-unique-key-101">
    Time Inside Cache Tag Helper: @DateTime.Now
</distributed-cache>
```

## <a name="distributed-cache-tag-helper-idistributedcache-implementations"></a>분산 캐시 태그 도우미 IDistributedCache 구현

ASP.NET Core에 두 가지 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 구현이 기본적으로 제공됩니다. 하나는 SQL Server 기반이고 다른 하나는 Redis 기반입니다. 이러한 구현의 세부 정보는 <xref:performance/caching/distributed>에서 찾을 수 있습니다. 두 구현 모두 `Startup`에 `IDistributedCache` 인스턴스를 설정해야 합니다.

특정 `IDistributedCache` 구현 사용과 특별히 관련된 태그 특성은 없습니다.

## <a name="additional-resources"></a>추가 자료

* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:fundamentals/dependency-injection>
* <xref:performance/caching/distributed>
* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
