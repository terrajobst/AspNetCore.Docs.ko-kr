---
title: ASP.NET Core에서 ObjectPool 사용 하 여 개체 다시 사용
author: rick-anderson
description: ObjectPool를 사용 하 여 ASP.NET Core 앱의 성능을 높이기 위한 팁입니다.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 4/11/2019
uid: performance/ObjectPool
ms.openlocfilehash: 92106d5add7dbda36e451614429baa0db420f0e8
ms.sourcegitcommit: bee530454ae2b3c25dc7ffebf93536f479a14460
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67724837"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a><span data-ttu-id="7273e-103">ASP.NET Core에서 ObjectPool 사용 하 여 개체 다시 사용</span><span class="sxs-lookup"><span data-stu-id="7273e-103">Object reuse with ObjectPool in ASP.NET Core</span></span>

<span data-ttu-id="7273e-104">하 여 [Steve Gordon](https://twitter.com/stevejgordon)하십시오 [Ryan Nowak](https://github.com/rynowak), 및 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7273e-104">By [Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="7273e-105"><xref:Microsoft.Extensions.ObjectPool> 수집 될 개체를 허용 하지 않고 개체 그룹을 다시 사용 하기 위해 메모리에 유지를 지 원하는 ASP.NET Core 인프라의 일부가입니다.</span><span class="sxs-lookup"><span data-stu-id="7273e-105"><xref:Microsoft.Extensions.ObjectPool> is part of the ASP.NET Core infrastructure that supports keeping a group of objects in memory for reuse rather than allowing the objects to be garbage collected.</span></span>

<span data-ttu-id="7273e-106">개체가 관리 되는 경우 개체 풀을 사용 하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="7273e-106">You might want to use the object pool if the objects that are being managed are:</span></span>

- <span data-ttu-id="7273e-107">할당/초기화 비용이 저렴 합니다.</span><span class="sxs-lookup"><span data-stu-id="7273e-107">Expensive to allocate/initialize.</span></span>
- <span data-ttu-id="7273e-108">일부 제한 된 리소스를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="7273e-108">Represent some limited resource.</span></span>
- <span data-ttu-id="7273e-109">예측 가능 하 고 자주 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7273e-109">Used predictably and frequently.</span></span>

<span data-ttu-id="7273e-110">예를 들어, ASP.NET Core 프레임 워크를 사용 하 여 개체 풀 일부 위치에서 다시 사용할 <xref:System.Text.StringBuilder> 인스턴스.</span><span class="sxs-lookup"><span data-stu-id="7273e-110">For example, the ASP.NET Core framework uses the object pool in some places to reuse <xref:System.Text.StringBuilder> instances.</span></span> <span data-ttu-id="7273e-111">`StringBuilder` 할당 하 고 문자 데이터를 저장할 버퍼 자체를 관리 합니다.</span><span class="sxs-lookup"><span data-stu-id="7273e-111">`StringBuilder` allocates and manages its own buffers to hold character data.</span></span> <span data-ttu-id="7273e-112">ASP.NET Core 정기적으로 사용 하 여 `StringBuilder` 기능을 구현 하기 위해 및 다시 사용할 성능상 이점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7273e-112">ASP.NET Core regularly uses `StringBuilder` to implement features, and reusing them provides a performance benefit.</span></span>

<span data-ttu-id="7273e-113">개체 풀링이 항상 성능이 향상 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7273e-113">Object pooling doesn't always improve performance:</span></span>

- <span data-ttu-id="7273e-114">개체의 초기화 비용이 높은 아닌 경우에 일반적으로 느립니다 풀에서 개체를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="7273e-114">Unless the initialization cost of an object is high, it's usually slower to get the object from the pool.</span></span>
- <span data-ttu-id="7273e-115">풀 할당 취소 될 때까지 풀에서 관리 되는 개체가 할당 취소 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7273e-115">Objects managed by the pool aren't de-allocated until the pool is de-allocated.</span></span>

<span data-ttu-id="7273e-116">실제 시나리오를 사용 하 여 앱 또는 라이브러리에 대 한 성능 데이터 수집 후에 개체 풀링을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7273e-116">Use object pooling only after collecting performance data using realistic scenarios for your app or library.</span></span>

<span data-ttu-id="7273e-117">**경고: 합니다 `ObjectPool` 구현 하지 않는 `IDisposable`합니다. 사용 하 여 삭제 해야 하는 형식과 권장 하지 않습니다.**</span><span class="sxs-lookup"><span data-stu-id="7273e-117">**WARNING: The `ObjectPool` doesn't implement `IDisposable`. We don't recommend using it with types that need disposal.**</span></span>

<span data-ttu-id="7273e-118">**참고: ObjectPool를 할당 하는 개체의 수에 한계를 설정 하지 않습니다, 그리고은 유지 하는 개체의 수에 제한을 부과 합니다.**</span><span class="sxs-lookup"><span data-stu-id="7273e-118">**NOTE: The ObjectPool doesn't place a limit on the number of objects that it will allocate, it places a limit on the number of objects it will retain.**</span></span>

## <a name="concepts"></a><span data-ttu-id="7273e-119">개념</span><span class="sxs-lookup"><span data-stu-id="7273e-119">Concepts</span></span>

<span data-ttu-id="7273e-120"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> -기본 개체 풀 추상화 합니다.</span><span class="sxs-lookup"><span data-stu-id="7273e-120"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> - the basic object pool abstraction.</span></span> <span data-ttu-id="7273e-121">가져올 개체를 반환 하는 데 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7273e-121">Used to get and return objects.</span></span>

<span data-ttu-id="7273e-122"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> -개체를 만드는 방법을 하는 방법 및 사용자 지정 하려면이 구현 *재설정* 풀으로 반환 하는 경우.</span><span class="sxs-lookup"><span data-stu-id="7273e-122"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> - implement this to customize how an object is created and how it is *reset* when returned to the pool.</span></span> <span data-ttu-id="7273e-123">이 직접 생성 하는 개체 풀에 전달할 수 있습니다... 또는</span><span class="sxs-lookup"><span data-stu-id="7273e-123">This can be passed into an object pool that you construct directly.... OR</span></span>

<span data-ttu-id="7273e-124"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> 개체 풀을 만들기 위한 팩터리 역할을 합니다.</span><span class="sxs-lookup"><span data-stu-id="7273e-124"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> acts as a factory for creating object pools.</span></span>
<!-- REview, there is no ObjectPoolProvider<T> -->

<span data-ttu-id="7273e-125">ObjectPool 여러 가지 방법으로 앱에서 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7273e-125">The ObjectPool can be used in an app in multiple ways:</span></span>

* <span data-ttu-id="7273e-126">풀을 인스턴스화.</span><span class="sxs-lookup"><span data-stu-id="7273e-126">Instantiating a pool.</span></span>
* <span data-ttu-id="7273e-127">풀에서 등록할 [종속성 주입](xref:fundamentals/dependency-injection) (DI) 인스턴스로.</span><span class="sxs-lookup"><span data-stu-id="7273e-127">Registering a pool in [Dependency injection](xref:fundamentals/dependency-injection) (DI) as an instance.</span></span>
* <span data-ttu-id="7273e-128">등록 된 `ObjectPoolProvider<>` DI 및 팩터리로 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7273e-128">Registering the `ObjectPoolProvider<>` in DI and using it as a factory.</span></span>

## <a name="how-to-use-objectpool"></a><span data-ttu-id="7273e-129">ObjectPool를 사용 하는 방법</span><span class="sxs-lookup"><span data-stu-id="7273e-129">How to use ObjectPool</span></span>

<span data-ttu-id="7273e-130">호출 <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> 개체를 가져오려는 및 <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> 개체를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="7273e-130">Call <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> to get an object and <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> to return the object.</span></span>  <span data-ttu-id="7273e-131">모든 개체를 반환 하는 사항은 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7273e-131">There's no requirement that you return every object.</span></span> <span data-ttu-id="7273e-132">개체를 반환 하지 않습니다, 경우 가비지 수집 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7273e-132">If you don't return an object, it will be garbage collected.</span></span>

## <a name="objectpool-sample"></a><span data-ttu-id="7273e-133">ObjectPool 샘플</span><span class="sxs-lookup"><span data-stu-id="7273e-133">ObjectPool sample</span></span>

<span data-ttu-id="7273e-134">다음 예를 참조하십시오.</span><span class="sxs-lookup"><span data-stu-id="7273e-134">The following code:</span></span>

* <span data-ttu-id="7273e-135">추가 `ObjectPoolProvider` 에 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너 (DI).</span><span class="sxs-lookup"><span data-stu-id="7273e-135">Adds `ObjectPoolProvider` to the [Dependency injection](xref:fundamentals/dependency-injection) (DI) container.</span></span>
* <span data-ttu-id="7273e-136">추가 하 고 구성 `ObjectPool<StringBuilder>` DI 컨테이너에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7273e-136">Adds and configures `ObjectPool<StringBuilder>` to the DI container.</span></span>
* <span data-ttu-id="7273e-137">추가 된 `BirthdayMiddleware`합니다.</span><span class="sxs-lookup"><span data-stu-id="7273e-137">Adds the `BirthdayMiddleware`.</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

<span data-ttu-id="7273e-138">다음 코드 구현 `BirthdayMiddleware`</span><span class="sxs-lookup"><span data-stu-id="7273e-138">The following code implements `BirthdayMiddleware`</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]
