---
title: 백그라운드 서비스에서 ASP.NET Core SignalR 호스트
author: bradygaster
description: .NET Core BackgroundService 클래스에서 SignalR 클라이언트에 메시지를 보내는 방법에 알아봅니다.
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 02/04/2019
uid: signalr/background-services
ms.openlocfilehash: dcd62f0c7056a3f987291b6c8bb8b87f94160865
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087750"
---
# <a name="host-aspnet-core-signalr-in-background-services"></a>백그라운드 서비스에서 ASP.NET Core SignalR 호스트

[Brady Gaster](https://twitter.com/bradygaster)

이 문서에서는 대 한 지침을 제공 합니다.

* ASP.NET Core를 사용 하 여 호스트 되는 백그라운드 작업자 프로세스를 사용 하 여 SignalR 허브를 호스팅합니다.
* .NET Core 내에서 클라이언트 연결에 메시지를 보낼 [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService)합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(다운로드 방법)](xref:index#how-to-download-a-sample)

## <a name="wire-up-signalr-during-startup"></a>시작 하는 동안 SignalR 연결

백그라운드 작업자 프로세스의 컨텍스트에서 ASP.NET Core SignalR 허브를 호스팅하는 허브를 ASP.NET Core 웹 앱을 호스트 하는 데는 것과 결과가 동일 합니다. 에 `Startup.ConfigureServices` 메서드를 호출 `services.AddSignalR` SignalR을 지원 하도록 ASP.NET Core DI (종속성 주입) 계층에 필요한 서비스를 추가 합니다. `Startup.Configure`, `UseSignalR` 메서드는 ASP.NET Core 요청 파이프라인에서 허브 끝점 연결 합니다.

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

앞의 예제에는 `ClockHub` 구현 클래스는 `Hub<T>` 강력한 형식의 Hub를 만드는 클래스. 합니다 `ClockHub` 에서 구성 된 합니다 `Startup` 끝점에 요청에 응답 하는 클래스 `/hubs/clock`합니다.

강력한 형식의 허브에 대 한 자세한 내용은 참조 하세요. [SignalR에서 허브를 사용 하 여 ASP.NET Core에 대 한](xref:signalr/hubs#strongly-typed-hubs)합니다.

> [!NOTE]
> 이 기능에는 제한 되지 않습니다.는 [허브\<T >](xref:Microsoft.AspNetCore.SignalR.Hub`1) 클래스입니다. 상속 되는 모든 클래스 [허브](xref:Microsoft.AspNetCore.SignalR.Hub)와 같은 [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), 에서도 작동 합니다.

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

사용 하는 인터페이스는 강력한 형식의 `ClockHub` 되는 `IClock` 인터페이스입니다.

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-signalr-hub-from-a-background-service"></a>백그라운드 서비스에서 SignalR 허브를 호출 합니다.

시작 하는 동안 합니다 `Worker` 클래스를 `BackgroundService`를 사용 하 여 연결 하는 `AddHostedService`합니다.

```csharp
services.AddHostedService<Worker>();
```

SignalR 동안 유선 수도 있으므로 합니다 `Startup` 단계를 각 허브에 연결 된 ASP.NET Core의 HTTP 요청 파이프라인에서 개별 끝점을 각 허브 표시 됩니다는 `IHubContext<T>` 서버의. ASP.NET Core를 사용 하 여 DI 기능을 다른 클래스와 같은 호스팅 계층에서 인스턴스화된 `BackgroundService` 클래스, MVC 컨트롤러 클래스 또는 Razor 페이지 모델의 인스턴스를 그대로 사용 하 여 서버 쪽 허브에 대 한 참조를 가져올 수 있습니다 `IHubContext<ClockHub, IClock>` 생성 중입니다.

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

로 `ExecuteAsync` 메서드가 백그라운드 서비스에서 반복적으로 호출 되는 서버의 현재 날짜 및 시간 보내집니다를 사용 하 여 연결 된 클라이언트에는 `ClockHub`합니다.

## <a name="react-to-signalr-events-with-background-services"></a>백그라운드 서비스를 사용 하 여 SignalR 이벤트에 대응

사용 하 여 SignalR 또는.NET 데스크톱 앱에서 수행할 수에 대 한 JavaScript 클라이언트를 사용 하 여 단일 페이지 앱 같은 합니다 <xref:signalr/dotnet-client>, `BackgroundService` 또는 `IHostedService` 구현 SignalR 허브에 연결 하 고 이벤트에 응답을 사용할 수도 있습니다.

`ClockHubClient` 클래스 모두 구현 합니다 `IClock` 인터페이스 및 `IHostedService` 인터페이스입니다. 하는 동안 구성 연결 될 수 있습니다 이러한 방식으로 `Startup` 지속적으로 실행 하 여 서버에서 허브 이벤트에 응답 합니다. 

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

초기화 하는 동안 합니다 `ClockHubClient` 의 인스턴스를 만듭니다를 `HubConnection` 를 합니다 `IClock.ShowTime` 허브에 대 한 메서드를 처리기로 `ShowTime` 이벤트입니다.

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

에 `IHostedService.StartAsync` 구현을 `HubConnection` 비동기적으로 시작 됩니다.

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

중 합니다 `IHostedService.StopAsync` 메서드는 `HubConnection` 비동기적으로 삭제 됩니다.

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a>추가 자료

* [시작](xref:tutorials/signalr)
* [허브](xref:signalr/hubs)
* [Azure에 게시하기](xref:signalr/publish-to-azure-web-app)
* [강력한 형식의 허브](xref:signalr/hubs#strongly-typed-hubs)
