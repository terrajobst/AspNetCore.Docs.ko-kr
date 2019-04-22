---
title: 확장을 위해 ASP.NET Core SignalR 백플레인으로 redis
author: bradygaster
description: Redis 백플레인 ASP.NET Core SignalR 앱에 대 한 확장을 사용 하도록 설정 하는 방법에 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/28/2018
uid: signalr/redis-backplane
ms.openlocfilehash: 9d2a942dba6abe669126efee7f2b3cdd6560658e
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59012645"
---
# <a name="set-up-a-redis-backplane-for-aspnet-core-signalr-scale-out"></a>ASP.NET Core SignalR 확장에 대 한를 Redis 백플레인으로 설정

하 여 [Andrew Stanton-nurse](https://twitter.com/anurse)하십시오 [Brady Gaster](https://twitter.com/bradygaster), 및 [Tom Dykstra](https://github.com/tdykstra),

이 문서에서는 설정의 SignalR 특정 측면을 설명 된 [Redis](https://redis.io/) 는 ASP.NET Core SignalR 앱 크기 조정에 사용할 서버를 합니다.

## <a name="set-up-a-redis-backplane"></a>Redis 백플레인 설정

* Redis 서버를 배포 합니다.

  > [!IMPORTANT] 
  > 프로덕션 사용에 대 한 Redis 백플레인 SignalR 앱과 동일한 데이터 센터에서 실행 하는 경우에이 좋습니다. 그렇지 않은 경우 네트워크 대기 시간 성능 저하 됩니다. SignalR 앱은 Azure 클라우드에서 실행 중인 경우 Azure SignalR Service를 Redis 백플레인으로 대신 권장 합니다. Azure Redis Cache Service를 사용 하 여 개발 및 테스트 환경 수 있습니다.

  자세한 내용은 다음 리소스를 참조하세요.

  * <xref:signalr/scale>
  * [Redis 설명서](https://redis.io/)
  * [Azure Redis Cache 설명서](https://docs.microsoft.com/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* SignalR 앱을 설치 합니다 `Microsoft.AspNetCore.SignalR.Redis` NuGet 패키지. (또한는 `Microsoft.AspNetCore.SignalR.StackExchangeRedis` 패키지, 하지만 하나는 ASP.NET Core 2.2 이상용.)

* 에 `Startup.ConfigureServices` 메서드를 호출 `AddRedis` 후 `AddSignalR`:

  ```csharp
  services.AddSignalR().AddRedis("<your_Redis_connection_string>");
  ```

* 필요에 따라 옵션을 구성 합니다.
 
  연결 문자열 또는 대부분의 옵션을 설정할 수 있습니다 합니다 [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) 개체입니다. 에 지정 된 옵션 `ConfigurationOptions` 연결 문자열에 설정 하는 것을 재정의 합니다.

  다음 예제에서 옵션을 설정 하는 방법을 보여 줍니다는 `ConfigurationOptions` 개체입니다. 이 예제에서는 채널 접두사를 여러 앱에서 동일한 Redis 인스턴스를 공유할 수 있도록 다음 단계에 설명 된 대로

  ```csharp
  services.AddSignalR()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  위의 코드에서 `options.Configuration` 연결 문자열에 지정 된 항목으로 초기화 됩니다.

::: moniker-end

::: moniker range="> aspnetcore-2.1"

* SignalR 앱에서 다음 NuGet 패키지 중 하나를 설치 합니다.

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis` -StackExchange.Redis 2.x.x로 지정 되었지만에 따라 달라 집니다. ASP.NET Core 2.2 이상용 권장 되는 패키지입니다.
  * `Microsoft.AspNetCore.SignalR.Redis` -StackExchange.Redis 1.X.X에 따라 달라 집니다. ASP.NET Core 3.0에서이 패키지를 발송 되지 됩니다.

* 에 `Startup.ConfigureServices` 메서드를 호출 `AddStackExchangeRedis` 후 `AddSignalR`:

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

* 필요에 따라 옵션을 구성 합니다.
 
  연결 문자열 또는 대부분의 옵션을 설정할 수 있습니다 합니다 [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) 개체입니다. 에 지정 된 옵션 `ConfigurationOptions` 연결 문자열에 설정 하는 것을 재정의 합니다.

  다음 예제에서 옵션을 설정 하는 방법을 보여 줍니다는 `ConfigurationOptions` 개체입니다. 이 예제에서는 채널 접두사를 여러 앱에서 동일한 Redis 인스턴스를 공유할 수 있도록 다음 단계에 설명 된 대로

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  위의 코드에서 `options.Configuration` 연결 문자열에 지정 된 항목으로 초기화 됩니다.

  Redis 옵션에 대 한 자세한 내용은 참조는 [StackExchange Redis 설명서](https://stackexchange.github.io/StackExchange.Redis/Configuration.html)합니다.

::: moniker-end

* Redis 서버 하나를 여러 SignalR 앱에 대 한 사용 하는 경우 각 SignalR 앱에 대 한 다양 한 채널 접두사를 사용 합니다.

  다른 채널 접두사를 사용 하는 다른 사용자 로부터 하나의 SignalR 앱을 격리 채널 접두사를 설정 합니다. 서로 다른 접두사를 할당 하지 않으면, 모든 자체 클라이언트로 하나의 앱에서 보낸 메시지를 백플레인으로 Redis 서버를 사용 하는 모든 앱의 모든 클라이언트에 전환 됩니다.

* 서버 팜 부하 분산 고정 세션에 대 한 소프트웨어를 구성 합니다. 그렇게 하는 방법에 대 한 설명서의 몇 가지 예는 다음과 같습니다.

  * [IIS](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)
  * [HAProxy](https://www.haproxy.com/blog/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/)
  * [Nginx](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
  * [pfSense](https://www.netgate.com/docs/pfsense/loadbalancing/inbound-load-balancing.html#sticky-connections)

## <a name="redis-server-errors"></a>Redis 서버 오류

Redis 서버 다운 되 면 SignalR 메시지를 배달할 수 없습니다를 나타내는 예외를 throw 합니다. 몇 가지 일반적인 예외 메시지:

* *실패 한 쓰기 메시지*
* *'MethodName' 허브 메서드를 호출 하지 못했습니다.*
* *Redis에 연결 하지 못했습니다.*

SignalR은 서버가 다시 작동 하는 경우 보낼 메시지를 버퍼링 하지 않습니다. Redis 서버가 다운 된 동안 전송 된 모든 메시지는 손실 됩니다.

SignalR에는 Redis 서버를 다시 사용할 때 자동으로 다시 연결 합니다.

### <a name="custom-behavior-for-connection-failures"></a>연결 실패에 대 한 사용자 지정 동작

Redis 연결 실패 이벤트를 처리 하는 방법을 보여 주는 예제는 다음과 같습니다.

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

## <a name="clustering"></a>클러스터링

클러스터링은 여러 Redis 서버를 사용 하 여 고가용성을 달성 하는 방법입니다. 공식적으로 지원 되지 않으면 클러스터링 하지만 작동 될 수 있습니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 리소스를 참조하세요.

* <xref:signalr/scale>
* [Redis 설명서](https://redis.io/documentation)
* [StackExchange Redis 설명서](https://stackexchange.github.io/StackExchange.Redis/)
* [Azure Redis Cache 설명서](https://docs.microsoft.com/azure/redis-cache/)
