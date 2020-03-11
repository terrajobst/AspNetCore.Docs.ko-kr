---
title: ASP.NET Core ObjectPool에서 개체 다시 사용
author: rick-anderson
description: ObjectPool을 사용 하 여 ASP.NET Core 앱의 성능을 향상 시키기 위한 팁입니다.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
uid: performance/ObjectPool
ms.openlocfilehash: 771f19e54a908b8b2cd85ff72f368f16e94a2310
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654381"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a><span data-ttu-id="6bcc4-103">ASP.NET Core ObjectPool에서 개체 다시 사용</span><span class="sxs-lookup"><span data-stu-id="6bcc4-103">Object reuse with ObjectPool in ASP.NET Core</span></span>

<span data-ttu-id="6bcc4-104">작성자, [Steve Gordon](https://twitter.com/stevejgordon), [Ryan nowak](https://github.com/rynowak)및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6bcc4-104">By [Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="6bcc4-105"><xref:Microsoft.Extensions.ObjectPool>는 개체의 가비지 수집을 허용 하는 대신 다시 사용 하기 위해 메모리의 개체 그룹을 유지 하는 것을 지 원하는 ASP.NET Core 인프라의 일부입니다.</span><span class="sxs-lookup"><span data-stu-id="6bcc4-105"><xref:Microsoft.Extensions.ObjectPool> is part of the ASP.NET Core infrastructure that supports keeping a group of objects in memory for reuse rather than allowing the objects to be garbage collected.</span></span>

<span data-ttu-id="6bcc4-106">관리 중인 개체가 다음과 같은 경우 개체 풀을 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="6bcc4-106">You might want to use the object pool if the objects that are being managed are:</span></span>

- <span data-ttu-id="6bcc4-107">할당/초기화 비용이 많이 듭니다.</span><span class="sxs-lookup"><span data-stu-id="6bcc4-107">Expensive to allocate/initialize.</span></span>
- <span data-ttu-id="6bcc4-108">제한 된 리소스를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="6bcc4-108">Represent some limited resource.</span></span>
- <span data-ttu-id="6bcc4-109">예측 가능 하 고 자주 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bcc4-109">Used predictably and frequently.</span></span>

<span data-ttu-id="6bcc4-110">예를 들어 ASP.NET Core 프레임 워크는 일부 위치에서 개체 풀을 사용 하 여 <xref:System.Text.StringBuilder> 인스턴스를 다시 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcc4-110">For example, the ASP.NET Core framework uses the object pool in some places to reuse <xref:System.Text.StringBuilder> instances.</span></span> <span data-ttu-id="6bcc4-111">`StringBuilder`는 문자 데이터를 저장 하기 위해 자체 버퍼를 할당 하 고 관리 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcc4-111">`StringBuilder` allocates and manages its own buffers to hold character data.</span></span> <span data-ttu-id="6bcc4-112">ASP.NET Core는 정기적으로 `StringBuilder`를 사용 하 여 기능을 구현 하 고 재사용 하면 성능상의 이점을 얻을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bcc4-112">ASP.NET Core regularly uses `StringBuilder` to implement features, and reusing them provides a performance benefit.</span></span>

<span data-ttu-id="6bcc4-113">개체 풀링은 항상 성능을 향상 시 키 지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6bcc4-113">Object pooling doesn't always improve performance:</span></span>

- <span data-ttu-id="6bcc4-114">개체의 초기화 비용이 높을 때를 제외 하 고는 일반적으로 풀에서 개체를 가져오는 속도가 느립니다.</span><span class="sxs-lookup"><span data-stu-id="6bcc4-114">Unless the initialization cost of an object is high, it's usually slower to get the object from the pool.</span></span>
- <span data-ttu-id="6bcc4-115">풀에서 관리 하는 개체는 풀이 할당 취소 될 때까지 할당 취소 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6bcc4-115">Objects managed by the pool aren't de-allocated until the pool is de-allocated.</span></span>

<span data-ttu-id="6bcc4-116">응용 프로그램 또는 라이브러리에 대해 현실적인 시나리오를 사용 하 여 성능 데이터를 수집한 후에만 개체 풀링을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcc4-116">Use object pooling only after collecting performance data using realistic scenarios for your app or library.</span></span>

<span data-ttu-id="6bcc4-117">**경고: `ObjectPool` `IDisposable`를 구현 하지 않습니다. 삭제 해야 하는 형식으로는 사용 하지 않는 것이 좋습니다.**</span><span class="sxs-lookup"><span data-stu-id="6bcc4-117">**WARNING: The `ObjectPool` doesn't implement `IDisposable`. We don't recommend using it with types that need disposal.**</span></span>

<span data-ttu-id="6bcc4-118">**참고: ObjectPool은 할당할 개체 수에 대 한 제한을 두지 않습니다. 그러면 개체 수에 대 한 제한이 유지 됩니다.**</span><span class="sxs-lookup"><span data-stu-id="6bcc4-118">**NOTE: The ObjectPool doesn't place a limit on the number of objects that it will allocate, it places a limit on the number of objects it will retain.**</span></span>

## <a name="concepts"></a><span data-ttu-id="6bcc4-119">개념</span><span class="sxs-lookup"><span data-stu-id="6bcc4-119">Concepts</span></span>

<span data-ttu-id="6bcc4-120"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1>-기본 개체 풀 추상화입니다.</span><span class="sxs-lookup"><span data-stu-id="6bcc4-120"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> - the basic object pool abstraction.</span></span> <span data-ttu-id="6bcc4-121">개체를 가져오고 반환 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bcc4-121">Used to get and return objects.</span></span>

<span data-ttu-id="6bcc4-122"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601>-개체가 만들어지는 방법과 풀로 반환 될 때 *다시 설정* 되는 방법을 사용자 지정 하려면이 메서드를 구현 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcc4-122"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> - implement this to customize how an object is created and how it is *reset* when returned to the pool.</span></span> <span data-ttu-id="6bcc4-123">직접 생성 하는 개체 풀에 전달할 수 있습니다. 디스크나</span><span class="sxs-lookup"><span data-stu-id="6bcc4-123">This can be passed into an object pool that you construct directly.... OR</span></span>

<span data-ttu-id="6bcc4-124"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*>는 개체 풀을 만들기 위한 팩터리 역할을 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcc4-124"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> acts as a factory for creating object pools.</span></span>
<!-- REview, there is no ObjectPoolProvider<T> -->

<span data-ttu-id="6bcc4-125">ObjectPool은 여러 가지 방법으로 앱에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bcc4-125">The ObjectPool can be used in an app in multiple ways:</span></span>

* <span data-ttu-id="6bcc4-126">풀 인스턴스화.</span><span class="sxs-lookup"><span data-stu-id="6bcc4-126">Instantiating a pool.</span></span>
* <span data-ttu-id="6bcc4-127">DI ( [종속성 주입](xref:fundamentals/dependency-injection) )의 풀을 인스턴스로 등록 하는 중입니다.</span><span class="sxs-lookup"><span data-stu-id="6bcc4-127">Registering a pool in [Dependency injection](xref:fundamentals/dependency-injection) (DI) as an instance.</span></span>
* <span data-ttu-id="6bcc4-128">`ObjectPoolProvider<>`를 DI에 등록 하 고 공장으로 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcc4-128">Registering the `ObjectPoolProvider<>` in DI and using it as a factory.</span></span>

## <a name="how-to-use-objectpool"></a><span data-ttu-id="6bcc4-129">ObjectPool 사용 방법</span><span class="sxs-lookup"><span data-stu-id="6bcc4-129">How to use ObjectPool</span></span>

<span data-ttu-id="6bcc4-130"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1>를 호출 하 여 개체를 가져오고 <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> 개체를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcc4-130">Call <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> to get an object and <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> to return the object.</span></span>  <span data-ttu-id="6bcc4-131">모든 개체를 반환 하는 요구 사항은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="6bcc4-131">There's no requirement that you return every object.</span></span> <span data-ttu-id="6bcc4-132">개체를 반환 하지 않으면 가비지 수집 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bcc4-132">If you don't return an object, it will be garbage collected.</span></span>

## <a name="objectpool-sample"></a><span data-ttu-id="6bcc4-133">ObjectPool 샘플</span><span class="sxs-lookup"><span data-stu-id="6bcc4-133">ObjectPool sample</span></span>

<span data-ttu-id="6bcc4-134">코드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="6bcc4-134">The following code:</span></span>

* <span data-ttu-id="6bcc4-135">DI ( [종속성 주입](xref:fundamentals/dependency-injection) ) 컨테이너에 `ObjectPoolProvider`를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcc4-135">Adds `ObjectPoolProvider` to the [Dependency injection](xref:fundamentals/dependency-injection) (DI) container.</span></span>
* <span data-ttu-id="6bcc4-136">DI 컨테이너에 `ObjectPool<StringBuilder>`를 추가 하 고 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcc4-136">Adds and configures `ObjectPool<StringBuilder>` to the DI container.</span></span>
* <span data-ttu-id="6bcc4-137">`BirthdayMiddleware`를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcc4-137">Adds the `BirthdayMiddleware`.</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

<span data-ttu-id="6bcc4-138">다음 코드는를 구현 `BirthdayMiddleware`</span><span class="sxs-lookup"><span data-stu-id="6bcc4-138">The following code implements `BirthdayMiddleware`</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]
