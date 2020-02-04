---
title: ASP.NET Core의 분산 캐시 태그 도우미
author: pkellner
description: 분산 캐시 태그 도우미를 사용하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 01/24/2020
uid: mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper
ms.openlocfilehash: e5100d7244600358186b653073990985f48434a7
ms.sourcegitcommit: b5ceb0a46d0254cc3425578116e2290142eec0f0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76809057"
---
# <a name="distributed-cache-tag-helper-in-aspnet-core"></a><span data-ttu-id="11971-103">ASP.NET Core의 분산 캐시 태그 도우미</span><span class="sxs-lookup"><span data-stu-id="11971-103">Distributed Cache Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="11971-104">작성자: [Peter Kellner](https://peterkellner.net) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="11971-104">By [Peter Kellner](https://peterkellner.net) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="11971-105">분산 캐시 태그 도우미는 콘텐츠를 분산 캐시 원본에 캐싱하여 ASP.NET Core 앱 성능을 획기적으로 개선하는 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="11971-105">The Distributed Cache Tag Helper provides the ability to dramatically improve the performance of your ASP.NET Core app by caching its content to a distributed cache source.</span></span>

<span data-ttu-id="11971-106">태그 도우미에 대한 개요는 <xref:mvc/views/tag-helpers/intro>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="11971-106">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="11971-107">분산 캐시 태그 도우미는 캐시 태그 도우미와 동일한 기본 클래스에서 상속합니다.</span><span class="sxs-lookup"><span data-stu-id="11971-107">The Distributed Cache Tag Helper inherits from the same base class as the Cache Tag Helper.</span></span> <span data-ttu-id="11971-108">모든 [캐시 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper) 특성이 분산 태그 도우미에 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="11971-108">All of the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper) attributes are available to the Distributed Tag Helper.</span></span>

<span data-ttu-id="11971-109">분산 캐시 태그 도우미는 [생성자 주입](xref:fundamentals/dependency-injection#constructor-injection-behavior)을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="11971-109">The Distributed Cache Tag Helper uses [constructor injection](xref:fundamentals/dependency-injection#constructor-injection-behavior).</span></span> <span data-ttu-id="11971-110"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 인터페이스는 분산 캐시 태그 도우미의 생성자에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="11971-110">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface is passed into the Distributed Cache Tag Helper's constructor.</span></span> <span data-ttu-id="11971-111">`IDistributedCache`의 구체적인 구현이 `Startup.ConfigureServices`(*Startup.cs*)에 생성되지 않은 경우 분산 캐시 태그 도우미는 동일한 메모리 내 공급자를 사용하여 캐시된 데이터를 [캐시 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)로 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="11971-111">If no concrete implementation of `IDistributedCache` is created in `Startup.ConfigureServices` (*Startup.cs*), the Distributed Cache Tag Helper uses the same in-memory provider for storing cached data as the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span></span>

## <a name="distributed-cache-tag-helper-attributes"></a><span data-ttu-id="11971-112">분산 캐시 태그 도우미 특성</span><span class="sxs-lookup"><span data-stu-id="11971-112">Distributed Cache Tag Helper Attributes</span></span>

### <a name="attributes-shared-with-the-cache-tag-helper"></a><span data-ttu-id="11971-113">캐시 태그 도우미와 공유되는 특성</span><span class="sxs-lookup"><span data-stu-id="11971-113">Attributes shared with the Cache Tag Helper</span></span>

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

<span data-ttu-id="11971-114">분산 캐시 태그 도우미는 캐시 태그 도우미와 동일한 클래스에서 상속합니다.</span><span class="sxs-lookup"><span data-stu-id="11971-114">The Distributed Cache Tag Helper inherits from the same class as Cache Tag Helper.</span></span> <span data-ttu-id="11971-115">이러한 특성에 대한 설명은 [캐시 태그 도우미](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="11971-115">For descriptions of these attributes, see the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span></span>

### <a name="name"></a><span data-ttu-id="11971-116">name</span><span class="sxs-lookup"><span data-stu-id="11971-116">name</span></span>

| <span data-ttu-id="11971-117">특성 유형</span><span class="sxs-lookup"><span data-stu-id="11971-117">Attribute Type</span></span> | <span data-ttu-id="11971-118">예제</span><span class="sxs-lookup"><span data-stu-id="11971-118">Example</span></span>                               |
| -------------- | ------------------------------------- |
| <span data-ttu-id="11971-119">문자열</span><span class="sxs-lookup"><span data-stu-id="11971-119">String</span></span>         | `my-distributed-cache-unique-key-101` |

<span data-ttu-id="11971-120">`name`은 필수입니다.</span><span class="sxs-lookup"><span data-stu-id="11971-120">`name` is required.</span></span> <span data-ttu-id="11971-121">`name` 특성은 저장된 각 캐시 인스턴스의 키로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="11971-121">The `name` attribute is used as a key for each stored cache instance.</span></span> <span data-ttu-id="11971-122">Razor 페이지의 Razor 페이지 이름 및 위치를 기준으로 각 인스턴스에 캐시 키를 할당하는 캐시 태그 도우미와는 달리, 분산 캐시 태그 도우미는 `name` 특성의 키만을 기준으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="11971-122">Unlike the Cache Tag Helper that assigns a cache key to each instance based on the Razor page name and location in the Razor page, the Distributed Cache Tag Helper only bases its key on the attribute `name`.</span></span>

<span data-ttu-id="11971-123">예:</span><span class="sxs-lookup"><span data-stu-id="11971-123">Example:</span></span>

```cshtml
<distributed-cache name="my-distributed-cache-unique-key-101">
    Time Inside Cache Tag Helper: @DateTime.Now
</distributed-cache>
```

## <a name="distributed-cache-tag-helper-idistributedcache-implementations"></a><span data-ttu-id="11971-124">분산 캐시 태그 도우미 IDistributedCache 구현</span><span class="sxs-lookup"><span data-stu-id="11971-124">Distributed Cache Tag Helper IDistributedCache implementations</span></span>

<span data-ttu-id="11971-125">ASP.NET Core에 두 가지 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 구현이 기본적으로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="11971-125">There are two implementations of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> built in to ASP.NET Core.</span></span> <span data-ttu-id="11971-126">하나는 SQL Server 기반이고 다른 하나는 Redis 기반입니다.</span><span class="sxs-lookup"><span data-stu-id="11971-126">One is based on SQL Server, and the other is based on Redis.</span></span> <span data-ttu-id="11971-127">[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html)와 같은 타사 구현도 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11971-127">Third-party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html).</span></span> <span data-ttu-id="11971-128">이러한 구현의 세부 정보는 <xref:performance/caching/distributed>에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="11971-128">Details of these implementations can be found at <xref:performance/caching/distributed>.</span></span> <span data-ttu-id="11971-129">두 구현 모두 `Startup`에 `IDistributedCache` 인스턴스를 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="11971-129">Both implementations involve setting an instance of `IDistributedCache` in `Startup`.</span></span>

<span data-ttu-id="11971-130">특정 `IDistributedCache` 구현 사용과 특별히 관련된 태그 특성은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="11971-130">There are no tag attributes specifically associated with using any specific implementation of `IDistributedCache`.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="11971-131">추가 자료</span><span class="sxs-lookup"><span data-stu-id="11971-131">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:fundamentals/dependency-injection>
* <xref:performance/caching/distributed>
* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
