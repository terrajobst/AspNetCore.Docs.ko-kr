---
title: 백그라운드 서비스에서 호스트 ASP.NET Core SignalR
author: bradygaster
description: .NET Core BackgroundService 클래스에서 SignalR 클라이언트에 메시지를 보내는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 02/04/2019
uid: signalr/background-services
ms.openlocfilehash: 23a53f33a03ce3b76cf6846c3f214a6cad055209
ms.sourcegitcommit: f65d8765e4b7c894481db9b37aa6969abc625a48
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773945"
---
# <a name="host-aspnet-core-signalr-in-background-services"></a>백그라운드 서비스에서 호스트 ASP.NET Core SignalR

[Brady Gaster](https://twitter.com/bradygaster)

이 문서에서는 다음에 대 한 지침을 제공 합니다.

* ASP.NET Core로 호스트 되는 백그라운드 작업자 프로세스를 사용 하 여 SignalR Hubs를 호스팅합니다.
* .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService)내에서 연결 된 클라이언트에 메시지를 보냅니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(다운로드 방법)](xref:index#how-to-download-a-sample)

## <a name="wire-up-signalr-during-startup"></a>시작 하는 동안 SignalR 연결

::: moniker range=">= aspnetcore-3.0"

백그라운드 작업자 프로세스의 컨텍스트에서 ASP.NET Core SignalR Hubs를 호스트 하는 것은 ASP.NET Core 웹 앱에서 허브를 호스트 하는 것과 동일 합니다. `Startup.ConfigureServices` 메서드에서 호출은`services.AddSignalR` SignalR을 지원 하기 위해 필수 서비스를 DI (ASP.NET Core 종속성 주입) 계층에 추가 합니다. 에서 `Startup.Configure` 메서드는ASP.NETCore요청파이프라인에서허브끝점을연결하기위해콜백에서호출됩니다.`MapHub` `UseEndpoints`

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR();
        services.AddHostedService<Worker>();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseRouting();
        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHub<ClockHub>("/hubs/clock");
        });
    }
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

백그라운드 작업자 프로세스의 컨텍스트에서 ASP.NET Core SignalR Hubs를 호스트 하는 것은 ASP.NET Core 웹 앱에서 허브를 호스트 하는 것과 동일 합니다. `Startup.ConfigureServices` 메서드에서 호출은`services.AddSignalR` SignalR을 지원 하기 위해 필수 서비스를 DI (ASP.NET Core 종속성 주입) 계층에 추가 합니다. 에서 `Startup.Configure`메서드는ASP.NETCore 요청파이프라인의허브끝점을연결하기위해호출됩니다.`UseSignalR`

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

::: moniker-end

앞의 예제 `ClockHub` 에서 클래스는 `Hub<T>` 클래스를 구현 하 여 강력한 형식의 허브를 만듭니다. 는 `ClockHub` `Startup` 끝점 에서`/hubs/clock`요청에 응답 하도록 클래스에서 구성 되었습니다.

강력한 형식의 허브에 대 한 자세한 내용은 [ASP.NET Core에 허브 사용](xref:signalr/hubs#strongly-typed-hubs)을 참조 하세요.

> [!NOTE]
> 이 기능은 [\<허브 t >](xref:Microsoft.AspNetCore.SignalR.Hub`1) 클래스로 제한 되지 않습니다. [Dynamichub](xref:Microsoft.AspNetCore.SignalR.DynamicHub)와 같은 [허브](xref:Microsoft.AspNetCore.SignalR.Hub)에서 상속 되는 모든 클래스도 작동 합니다.

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

강력 하 게 형식화 `ClockHub` `IClock` 된에서 사용 하는 인터페이스는 인터페이스입니다.

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-signalr-hub-from-a-background-service"></a>백그라운드 서비스에서 SignalR Hub를 호출 합니다.

시작 `Worker` 중에는 `BackgroundService`를 사용 하 여 `AddHostedService`클래스를 연결 합니다.

```csharp
services.AddHostedService<Worker>();
```

SignalR는 각 허브가 ASP.NET Core의 HTTP 요청 `Startup` 파이프라인에서 개별 끝점에 연결 되는 단계 중에도 연결 되므로 각 허브는 서버 `IHubContext<T>` 에서로 표시 됩니다. ASP.NET Core의 DI 기능을 사용 하 여 클래스, MVC 컨트롤러 클래스 또는 Razor `BackgroundService` 페이지 모델과 같이 호스팅 계층에 의해 인스턴스화된 다른 클래스는 생성 중의 `IHubContext<ClockHub, IClock>` 인스턴스를 허용 하 여 서버 쪽 허브에 대 한 참조를 가져올 수 있습니다.

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

메서드가 백그라운드 서비스에서 반복적으로 호출 되 면 서버의 현재 날짜 및 시간이를 `ClockHub`사용 하 여 연결 된 클라이언트에 전송 됩니다. `ExecuteAsync`

## <a name="react-to-signalr-events-with-background-services"></a>백그라운드 서비스를 사용 하 여 SignalR 이벤트에 대응

SignalR 용 JavaScript 클라이언트 또는 .net 데스크톱 앱을 사용 하는 단일 페이지 앱과 마찬가지로를 사용 하 여를 <xref:signalr/dotnet-client>사용할 수 `BackgroundService` 있습니다 `IHostedService` . 또는 구현을 사용 하 여 SignalR 허브에 연결 하 고 이벤트에 응답할 수도 있습니다.

클래스 `ClockHubClient` 는 `IClock` 인터페이스와 `IHostedService` 인터페이스를 둘 다 구현 합니다. 이러한 방식으로를 실행 하는 동안 `Startup` 연결 하 여 서버에서 허브 이벤트에 응답할 수 있습니다.

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

초기화 중에는 `ClockHubClient` `HubConnection` 의 인스턴스를 만들고 허브 `ShowTime` 이벤트에 대 한 `IClock.ShowTime` 처리기로 메서드를 배선 합니다.

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

`IHostedService.StartAsync` 구현`HubConnection` 에서가 비동기적으로 시작 됩니다.

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

메서드를 실행 하는 `HubConnection` 동안은 비동기적으로 삭제 됩니다. `IHostedService.StopAsync`

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a>추가 자료

* [시작](xref:tutorials/signalr)
* [허브](xref:signalr/hubs)
* [Azure에 게시하기](xref:signalr/publish-to-azure-web-app)
* [강력한 형식의 허브](xref:signalr/hubs#strongly-typed-hubs)
