---
title: 확장을 위해 ASP.NET Core SignalR 백플레인으로 redis
author: bradygaster
description: Redis 백플레인 ASP.NET Core SignalR 앱에 대 한 확장을 사용 하도록 설정 하는 방법에 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/28/2018
uid: signalr/redis-backplane
ms.openlocfilehash: adf9bbce1353fd811a4044e173533f76bc4193de
ms.sourcegitcommit: 4ef0362ef8b6e5426fc5af18f22734158fe587e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67152921"
---
# <a name="set-up-a-redis-backplane-for-aspnet-core-signalr-scale-out"></a><span data-ttu-id="15707-103">ASP.NET Core SignalR 확장에 대 한를 Redis 백플레인으로 설정</span><span class="sxs-lookup"><span data-stu-id="15707-103">Set up a Redis backplane for ASP.NET Core SignalR scale-out</span></span>

<span data-ttu-id="15707-104">하 여 [Andrew Stanton-nurse](https://twitter.com/anurse)하십시오 [Brady Gaster](https://twitter.com/bradygaster), 및 [Tom Dykstra](https://github.com/tdykstra),</span><span class="sxs-lookup"><span data-stu-id="15707-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra),</span></span>

<span data-ttu-id="15707-105">이 문서에서는 설정의 SignalR 특정 측면을 설명 된 [Redis](https://redis.io/) 는 ASP.NET Core SignalR 앱 크기 조정에 사용할 서버를 합니다.</span><span class="sxs-lookup"><span data-stu-id="15707-105">This article explains SignalR-specific aspects of setting up a [Redis](https://redis.io/) server to use for scaling out an ASP.NET Core SignalR app.</span></span>

## <a name="set-up-a-redis-backplane"></a><span data-ttu-id="15707-106">Redis 백플레인 설정</span><span class="sxs-lookup"><span data-stu-id="15707-106">Set up a Redis backplane</span></span>

* <span data-ttu-id="15707-107">Redis 서버를 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="15707-107">Deploy a Redis server.</span></span>

  > [!IMPORTANT] 
  > <span data-ttu-id="15707-108">프로덕션 사용에 대 한 Redis 백플레인 SignalR 앱과 동일한 데이터 센터에서 실행 하는 경우에이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="15707-108">For production use, a Redis backplane is recommended only when it runs in the same data center as the SignalR app.</span></span> <span data-ttu-id="15707-109">그렇지 않은 경우 네트워크 대기 시간 성능 저하 됩니다.</span><span class="sxs-lookup"><span data-stu-id="15707-109">Otherwise, network latency degrades performance.</span></span> <span data-ttu-id="15707-110">SignalR 앱은 Azure 클라우드에서 실행 중인 경우 Azure SignalR Service를 Redis 백플레인으로 대신 권장 합니다.</span><span class="sxs-lookup"><span data-stu-id="15707-110">If your SignalR app is running in the Azure cloud, we recommend Azure SignalR Service instead of a Redis backplane.</span></span> <span data-ttu-id="15707-111">Azure Redis Cache Service를 사용 하 여 개발 및 테스트 환경 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="15707-111">You can use the Azure Redis Cache Service for development and test environments.</span></span>

  <span data-ttu-id="15707-112">자세한 내용은 다음 리소스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="15707-112">For more information, see the following resources:</span></span>

  * <xref:signalr/scale>
  * [<span data-ttu-id="15707-113">Redis 설명서</span><span class="sxs-lookup"><span data-stu-id="15707-113">Redis documentation</span></span>](https://redis.io/)
  * [<span data-ttu-id="15707-114">Azure Redis Cache 설명서</span><span class="sxs-lookup"><span data-stu-id="15707-114">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* <span data-ttu-id="15707-115">SignalR 앱을 설치 합니다 `Microsoft.AspNetCore.SignalR.Redis` NuGet 패키지.</span><span class="sxs-lookup"><span data-stu-id="15707-115">In the SignalR app, install the `Microsoft.AspNetCore.SignalR.Redis` NuGet package.</span></span> <span data-ttu-id="15707-116">(또한는 `Microsoft.AspNetCore.SignalR.StackExchangeRedis` 패키지, 하지만 하나는 ASP.NET Core 2.2 이상용.)</span><span class="sxs-lookup"><span data-stu-id="15707-116">(There is also a `Microsoft.AspNetCore.SignalR.StackExchangeRedis` package, but that one is for ASP.NET Core 2.2 and later.)</span></span>

* <span data-ttu-id="15707-117">에 `Startup.ConfigureServices` 메서드를 호출 `AddRedis` 후 `AddSignalR`:</span><span class="sxs-lookup"><span data-stu-id="15707-117">In the `Startup.ConfigureServices` method, call `AddRedis` after `AddSignalR`:</span></span>

  ```csharp
  services.AddSignalR().AddRedis("<your_Redis_connection_string>");
  ```

* <span data-ttu-id="15707-118">필요에 따라 옵션을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="15707-118">Configure options as needed:</span></span>
 
  <span data-ttu-id="15707-119">연결 문자열 또는 대부분의 옵션을 설정할 수 있습니다 합니다 [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="15707-119">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="15707-120">에 지정 된 옵션 `ConfigurationOptions` 연결 문자열에 설정 하는 것을 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="15707-120">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="15707-121">다음 예제에서 옵션을 설정 하는 방법을 보여 줍니다는 `ConfigurationOptions` 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="15707-121">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="15707-122">이 예제에서는 채널 접두사를 여러 앱에서 동일한 Redis 인스턴스를 공유할 수 있도록 다음 단계에 설명 된 대로</span><span class="sxs-lookup"><span data-stu-id="15707-122">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="15707-123">위의 코드에서 `options.Configuration` 연결 문자열에 지정 된 항목으로 초기화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="15707-123">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.1"

* <span data-ttu-id="15707-124">SignalR 앱에서 다음 NuGet 패키지 중 하나를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="15707-124">In the SignalR app, install one of the following NuGet packages:</span></span>

  * <span data-ttu-id="15707-125">`Microsoft.AspNetCore.SignalR.StackExchangeRedis` -StackExchange.Redis 2.x.x로 지정 되었지만에 따라 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="15707-125">`Microsoft.AspNetCore.SignalR.StackExchangeRedis` - Depends on StackExchange.Redis 2.X.X.</span></span> <span data-ttu-id="15707-126">ASP.NET Core 2.2 이상용 권장 되는 패키지입니다.</span><span class="sxs-lookup"><span data-stu-id="15707-126">This is the recommended package for ASP.NET Core 2.2 and later.</span></span>
  * <span data-ttu-id="15707-127">`Microsoft.AspNetCore.SignalR.Redis` -StackExchange.Redis 1.X.X에 따라 달라 집니다.</span><span class="sxs-lookup"><span data-stu-id="15707-127">`Microsoft.AspNetCore.SignalR.Redis` - Depends on StackExchange.Redis 1.X.X.</span></span> <span data-ttu-id="15707-128">ASP.NET Core 3.0에서이 패키지를 발송 되지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="15707-128">This package will not be shipping in ASP.NET Core 3.0.</span></span>

* <span data-ttu-id="15707-129">에 `Startup.ConfigureServices` 메서드를 호출 `AddStackExchangeRedis` 후 `AddSignalR`:</span><span class="sxs-lookup"><span data-stu-id="15707-129">In the `Startup.ConfigureServices` method, call `AddStackExchangeRedis` after `AddSignalR`:</span></span>

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

* <span data-ttu-id="15707-130">필요에 따라 옵션을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="15707-130">Configure options as needed:</span></span>
 
  <span data-ttu-id="15707-131">연결 문자열 또는 대부분의 옵션을 설정할 수 있습니다 합니다 [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="15707-131">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="15707-132">에 지정 된 옵션 `ConfigurationOptions` 연결 문자열에 설정 하는 것을 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="15707-132">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="15707-133">다음 예제에서 옵션을 설정 하는 방법을 보여 줍니다는 `ConfigurationOptions` 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="15707-133">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="15707-134">이 예제에서는 채널 접두사를 여러 앱에서 동일한 Redis 인스턴스를 공유할 수 있도록 다음 단계에 설명 된 대로</span><span class="sxs-lookup"><span data-stu-id="15707-134">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="15707-135">위의 코드에서 `options.Configuration` 연결 문자열에 지정 된 항목으로 초기화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="15707-135">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

  <span data-ttu-id="15707-136">Redis 옵션에 대 한 자세한 내용은 참조는 [StackExchange Redis 설명서](https://stackexchange.github.io/StackExchange.Redis/Configuration.html)합니다.</span><span class="sxs-lookup"><span data-stu-id="15707-136">For information about Redis options, see the [StackExchange Redis documentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span></span>

::: moniker-end

* <span data-ttu-id="15707-137">Redis 서버 하나를 여러 SignalR 앱에 대 한 사용 하는 경우 각 SignalR 앱에 대 한 다양 한 채널 접두사를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="15707-137">If you're using one Redis server for multiple SignalR apps, use a different channel prefix for each SignalR app.</span></span>

  <span data-ttu-id="15707-138">다른 채널 접두사를 사용 하는 다른 사용자 로부터 하나의 SignalR 앱을 격리 채널 접두사를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="15707-138">Setting a channel prefix isolates one SignalR app from others that use different channel prefixes.</span></span> <span data-ttu-id="15707-139">서로 다른 접두사를 할당 하지 않으면, 모든 자체 클라이언트로 하나의 앱에서 보낸 메시지를 백플레인으로 Redis 서버를 사용 하는 모든 앱의 모든 클라이언트에 전환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="15707-139">If you don't assign different prefixes, a message sent from one app to all of its own clients will go to all clients of all apps that use the Redis server as a backplane.</span></span>

* <span data-ttu-id="15707-140">서버 팜 부하 분산 고정 세션에 대 한 소프트웨어를 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="15707-140">Configure your server farm load balancing software for sticky sessions.</span></span> <span data-ttu-id="15707-141">그렇게 하는 방법에 대 한 설명서의 몇 가지 예는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="15707-141">Here are some examples of documentation on how to do that:</span></span>

  * [<span data-ttu-id="15707-142">IIS</span><span class="sxs-lookup"><span data-stu-id="15707-142">IIS</span></span>](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)
  * [<span data-ttu-id="15707-143">HAProxy</span><span class="sxs-lookup"><span data-stu-id="15707-143">HAProxy</span></span>](https://www.haproxy.com/blog/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/)
  * [<span data-ttu-id="15707-144">Nginx</span><span class="sxs-lookup"><span data-stu-id="15707-144">Nginx</span></span>](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
  * [<span data-ttu-id="15707-145">pfSense</span><span class="sxs-lookup"><span data-stu-id="15707-145">pfSense</span></span>](https://www.netgate.com/docs/pfsense/loadbalancing/inbound-load-balancing.html#sticky-connections)

## <a name="redis-server-errors"></a><span data-ttu-id="15707-146">Redis 서버 오류</span><span class="sxs-lookup"><span data-stu-id="15707-146">Redis server errors</span></span>

<span data-ttu-id="15707-147">Redis 서버 다운 되 면 SignalR 메시지를 배달할 수 없습니다를 나타내는 예외를 throw 합니다.</span><span class="sxs-lookup"><span data-stu-id="15707-147">When a Redis server goes down, SignalR throws exceptions that indicate messages won't be delivered.</span></span> <span data-ttu-id="15707-148">몇 가지 일반적인 예외 메시지:</span><span class="sxs-lookup"><span data-stu-id="15707-148">Some typical exception messages:</span></span>

* <span data-ttu-id="15707-149">*실패 한 쓰기 메시지*</span><span class="sxs-lookup"><span data-stu-id="15707-149">*Failed writing message*</span></span>
* <span data-ttu-id="15707-150">*'MethodName' 허브 메서드를 호출 하지 못했습니다.*</span><span class="sxs-lookup"><span data-stu-id="15707-150">*Failed to invoke hub method 'MethodName'*</span></span>
* <span data-ttu-id="15707-151">*Redis에 연결 하지 못했습니다.*</span><span class="sxs-lookup"><span data-stu-id="15707-151">*Connection to Redis failed*</span></span>

<span data-ttu-id="15707-152">SignalR은 서버가 다시 작동 하는 경우 보낼 메시지를 버퍼링 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="15707-152">SignalR doesn't buffer messages to send them when the server comes back up.</span></span> <span data-ttu-id="15707-153">Redis 서버가 다운 된 동안 전송 된 모든 메시지는 손실 됩니다.</span><span class="sxs-lookup"><span data-stu-id="15707-153">Any messages sent while the Redis server is down are lost.</span></span>

<span data-ttu-id="15707-154">SignalR에는 Redis 서버를 다시 사용할 때 자동으로 다시 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="15707-154">SignalR automatically reconnects when the Redis server is available again.</span></span>

### <a name="custom-behavior-for-connection-failures"></a><span data-ttu-id="15707-155">연결 실패에 대 한 사용자 지정 동작</span><span class="sxs-lookup"><span data-stu-id="15707-155">Custom behavior for connection failures</span></span>

<span data-ttu-id="15707-156">Redis 연결 실패 이벤트를 처리 하는 방법을 보여 주는 예제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="15707-156">Here's an example that shows how to handle Redis connection failure events.</span></span>

::: moniker range="= aspnetcore-2.1"

```csharp
services.AddSignalR()
        .AddRedis(o =>
        {
            o.ConnectionFactory = async writer =>
            {
                var config = new ConfigurationOptions
                {
                    AbortOnConnectFail = false
                };
                config.EndPoints.Add(IPAddress.Loopback, 0);
                config.SetDefaultPorts();
                var connection = await ConnectionMultiplexer.ConnectAsync(config, writer);
                connection.ConnectionFailed += (_, e) =>
                {
                    Console.WriteLine("Connection to Redis failed.");
                };

                if (!connection.IsConnected)
                {
                    Console.WriteLine("Did not connect to Redis.");
                }

                return connection;
            };
        });
```

::: moniker-end

::: moniker range="> aspnetcore-2.1"

```csharp
services.AddSignalR()
        .AddMessagePackProtocol()
        .AddStackExchangeRedis(o =>
        {
            o.ConnectionFactory = async writer =>
            {
                var config = new ConfigurationOptions
                {
                    AbortOnConnectFail = false
                };
                config.EndPoints.Add(IPAddress.Loopback, 0);
                config.SetDefaultPorts();
                var connection = await ConnectionMultiplexer.ConnectAsync(config, writer);
                connection.ConnectionFailed += (_, e) =>
                {
                    Console.WriteLine("Connection to Redis failed.");
                };

                if (!connection.IsConnected)
                {
                    Console.WriteLine("Did not connect to Redis.");
                }

                return connection;
            };
        });
```

::: moniker-end

## <a name="redis-clustering"></a><span data-ttu-id="15707-157">Redis 클러스터링</span><span class="sxs-lookup"><span data-stu-id="15707-157">Redis Clustering</span></span>

<span data-ttu-id="15707-158">[Redis 클러스터링](https://redis.io/topics/cluster-spec) 여러 Redis 서버를 사용 하 여 고가용성을 달성 하기 위한 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="15707-158">[Redis Clustering](https://redis.io/topics/cluster-spec) is a method for achieving high availability by using multiple Redis servers.</span></span> <span data-ttu-id="15707-159">공식적으로 지원 되지 않으면 클러스터링 하지만 작동 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="15707-159">Clustering isn't officially supported, but it might work.</span></span>

## <a name="next-steps"></a><span data-ttu-id="15707-160">다음 단계</span><span class="sxs-lookup"><span data-stu-id="15707-160">Next steps</span></span>

<span data-ttu-id="15707-161">자세한 내용은 다음 리소스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="15707-161">For more information, see the following resources:</span></span>

* <xref:signalr/scale>
* [<span data-ttu-id="15707-162">Redis 설명서</span><span class="sxs-lookup"><span data-stu-id="15707-162">Redis documentation</span></span>](https://redis.io/documentation)
* [<span data-ttu-id="15707-163">StackExchange Redis 설명서</span><span class="sxs-lookup"><span data-stu-id="15707-163">StackExchange Redis documentation</span></span>](https://stackexchange.github.io/StackExchange.Redis/)
* [<span data-ttu-id="15707-164">Azure Redis Cache 설명서</span><span class="sxs-lookup"><span data-stu-id="15707-164">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/azure/redis-cache/)
