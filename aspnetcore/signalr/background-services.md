---
title: 백그라운드 서비스의 호스트 ASP.NET Core SignalR
author: bradygaster
description: .NET Core BackgroundService 클래스에서 SignalR 클라이언트에 메시지를 보내는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/background-services
ms.openlocfilehash: 324592759af79d1229eb147fb4551e97c678ef64
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75358680"
---
# <a name="host-aspnet-core-opno-locsignalr-in-background-services"></a>백그라운드 서비스의 호스트 ASP.NET Core SignalR

[Brady Gaster](https://twitter.com/bradygaster)

이 문서에서는 다음에 대 한 지침을 제공 합니다.

* ASP.NET Core에서 호스트 되는 백그라운드 작업자 프로세스를 사용 하 여 SignalR 허브를 호스팅합니다.
* .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService)내에서 연결 된 클라이언트에 메시지를 보냅니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(다운로드 방법)](xref:index#how-to-download-a-sample)

## <a name="enable-opno-locsignalr-in-startup"></a>시작할 때 SignalR 사용

::: moniker range=">= aspnetcore-3.0"

백그라운드 작업자 프로세스의 컨텍스트에서 ASP.NET Core SignalR 허브를 호스트 하는 것은 ASP.NET Core 웹 앱에서 허브를 호스트 하는 것과 동일 합니다. `Startup.ConfigureServices` 메서드에서는 `services.AddSignalR`를 호출 하 여 SignalR를 지원 하기 위해 필수 서비스를 DI (ASP.NET Core 종속성 주입) 계층에 추가 합니다. `Startup.Configure`에서 `UseEndpoints` 콜백에서 `MapHub` 메서드를 호출 하 여 ASP.NET Core 요청 파이프라인의 허브 끝점을 연결 합니다.

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

백그라운드 작업자 프로세스의 컨텍스트에서 ASP.NET Core SignalR 허브를 호스트 하는 것은 ASP.NET Core 웹 앱에서 허브를 호스트 하는 것과 동일 합니다. `Startup.ConfigureServices` 메서드에서는 `services.AddSignalR`를 호출 하 여 SignalR를 지원 하기 위해 필수 서비스를 DI (ASP.NET Core 종속성 주입) 계층에 추가 합니다. `Startup.Configure`에서 ASP.NET Core 요청 파이프라인의 허브 끝점을 연결 하기 위해 `UseSignalR` 메서드가 호출 됩니다.

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

::: moniker-end

위의 예제에서 `ClockHub` 클래스는 `Hub<T>` 클래스를 구현 하 여 강력한 형식의 허브를 만듭니다. `ClockHub`는 끝점 `/hubs/clock`에서 요청에 응답 하도록 `Startup` 클래스에서 구성 되었습니다.

강력한 형식의 허브에 대 한 자세한 내용은 [ASP.NET Core에 대 한 SignalR에서 허브 사용](xref:signalr/hubs#strongly-typed-hubs)을 참조 하세요.

> [!NOTE]
> 이 기능은 [허브\<t >](xref:Microsoft.AspNetCore.SignalR.Hub`1) 클래스로 제한 되지 않습니다. [Dynamichub](xref:Microsoft.AspNetCore.SignalR.DynamicHub)와 같은 [허브](xref:Microsoft.AspNetCore.SignalR.Hub)에서 상속 되는 모든 클래스도 작동 합니다.

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

강력한 형식의 `ClockHub`에서 사용 하는 인터페이스는 `IClock` 인터페이스입니다.

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-opno-locsignalr-hub-from-a-background-service"></a>백그라운드 서비스에서 SignalR 허브 호출

시작 하는 동안 `AddHostedService`를 사용 하 여 `BackgroundService``Worker` 클래스를 사용할 수 있습니다.

```csharp
services.AddHostedService<Worker>();
```

`Startup` 단계 중에도 SignalR 사용 하도록 설정 되므로 각 허브는 ASP.NET Core의 HTTP 요청 파이프라인에서 개별 끝점에 연결 됩니다. 각 허브는 서버에서 `IHubContext<T>` 표시 됩니다. ASP.NET Core의 DI 기능을 사용 하면 `BackgroundService` 클래스, MVC 컨트롤러 클래스 또는 Razor 페이지 모델과 같이 호스팅 계층에서 인스턴스화된 다른 클래스가 생성 중에 `IHubContext<ClockHub, IClock>` 인스턴스를 수락 하 여 서버 쪽 허브에 대 한 참조를 가져올 수 있습니다.

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

백그라운드 서비스에서 `ExecuteAsync` 메서드가 반복적으로 호출 되 면 서버의 현재 날짜와 시간이 `ClockHub`를 사용 하 여 연결 된 클라이언트에 전송 됩니다.

## <a name="react-to-opno-locsignalr-events-with-background-services"></a>백그라운드 서비스를 사용 하 여 SignalR 이벤트에 대응

SignalR 또는 .NET 데스크톱 응용 프로그램에서 JavaScript 클라이언트를 사용 하는 단일 페이지 앱 처럼 <xref:signalr/dotnet-client>를 사용 하 여를 수행할 수 있습니다. `BackgroundService` 또는 `IHostedService` 구현은 SignalR 허브에 연결 하 고 이벤트에 응답 하는 데에도 사용할 수 있습니다.

`ClockHubClient` 클래스는 `IClock` 인터페이스와 `IHostedService` 인터페이스를 둘 다 구현 합니다. 이렇게 하면 `Startup` 하는 동안 사용 하도록 설정 하 여 서버에서 허브 이벤트에 대 한 응답을 지속적으로 실행할 수 있습니다.

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

초기화 하는 동안 `ClockHubClient` `HubConnection` 인스턴스를 만들고 `IClock.ShowTime` 메서드를 허브의 `ShowTime` 이벤트 처리기로 사용 하도록 설정 합니다.

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

`IHostedService.StartAsync` 구현에서는 `HubConnection` 비동기식으로 시작 됩니다.

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

`IHostedService.StopAsync` 메서드 중에는 `HubConnection`가 비동기적으로 삭제 됩니다.

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a>추가 자료

* [시작](xref:tutorials/signalr)
* [허브](xref:signalr/hubs)
* [Azure에 게시](xref:signalr/publish-to-azure-web-app)
* [강력한 형식의 허브](xref:signalr/hubs#strongly-typed-hubs)
