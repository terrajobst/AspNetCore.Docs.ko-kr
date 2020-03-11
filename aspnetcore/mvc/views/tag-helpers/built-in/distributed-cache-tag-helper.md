---
title: ASP.NET Core의 분산 캐시 태그 도우미
author: pkellner
description: 분산 캐시 태그 도우미를 사용하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 01/24/2020
uid: mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper
ms.openlocfilehash: f5957adf3cef8966812a1bf0cbc6b2627d19d026
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653793"
---
# <a name="distributed-cache-tag-helper-in-aspnet-core"></a>ASP.NET Core의 분산 캐시 태그 도우미

작성자: [Peter Kellner](https://peterkellner.net)

분산 캐시 태그 도우미는 콘텐츠를 분산 캐시 원본에 캐싱하여 ASP.NET Core 앱 성능을 획기적으로 개선하는 기능을 제공합니다.

태그 도우미에 대한 개요는 <xref:mvc/views/tag-helpers/intro>를 참조하세요.

분산 캐시 태그 도우미는 캐시 태그 도우미와 동일한 기본 클래스에서 상속합니다. 모든 [캐시 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper) 특성이 분산 태그 도우미에 제공됩니다.

분산 캐시 태그 도우미는 [생성자 주입](xref:fundamentals/dependency-injection#constructor-injection-behavior)을 사용합니다. 분산 캐시 태그 도우미의 생성자에는 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 인터페이스가 전달됩니다. `IDistributedCache`의 구체적인 구현이 `Startup.ConfigureServices`(*Startup.cs*)에 생성되지 않은 경우 분산 캐시 태그 도우미는 동일한 메모리 내 공급자를 사용하여 캐시된 데이터를 [캐시 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)로 저장합니다.

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

분산 캐시 태그 도우미는 캐시 태그 도우미와 동일한 클래스를 상속받습니다. 이러한 특성에 대한 설명은 [캐시 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)를 참조하세요.

### <a name="name"></a>name

| 특성 유형 | 예제                               |
| -------------- | ------------------------------------- |
| String         | `my-distributed-cache-unique-key-101` |

`name`은 필수입니다. `name` 특성은 저장된 각 캐시 인스턴스의 키로 사용됩니다. Razor 페이지의 Razor 페이지 이름 및 위치를 기준으로 각 인스턴스에 캐시 키를 할당하는 캐시 태그 도우미와는 달리, 분산 캐시 태그 도우미는 `name` 특성의 키만을 기준으로 합니다.

예제:

```cshtml
<distributed-cache name="my-distributed-cache-unique-key-101">
    Time Inside Cache Tag Helper: @DateTime.Now
</distributed-cache>
```

## <a name="distributed-cache-tag-helper-idistributedcache-implementations"></a>분산 캐시 태그 도우미 IDistributedCache 구현

ASP.NET Core에 두 가지 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 구현이 기본적으로 제공됩니다. 하나는 SQL Server 기반이고 다른 하나는 Redis 기반입니다. [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html)와 같은 타사 구현도 사용할 수 있습니다. 이러한 구현의 세부 정보는 <xref:performance/caching/distributed>에서 찾을 수 있습니다. 두 구현 모두 `IDistributedCache`에 `Startup` 인스턴스를 설정해야 합니다.

특정 `IDistributedCache` 구현 사용과 특별히 관련된 태그 특성은 없습니다.

## <a name="additional-resources"></a>추가 리소스

* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:fundamentals/dependency-injection>
* <xref:performance/caching/distributed>
* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
