---
title: ASP.NET Core 구성에 대 한 gRPC
author: jamesnk
description: GRPC ASP.NET Core 앱을 구성 하는 방법에 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 04/09/2019
uid: grpc/configuration
ms.openlocfilehash: 66dfb9ec136616f10c1b7aaad766e18813b87de4
ms.sourcegitcommit: eb784a68219b4829d8e50c8a334c38d4b94e0cfa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59983135"
---
# <a name="grpc-for-aspnet-core-configuration"></a>ASP.NET Core 구성에 대 한 gRPC

## <a name="configure-services-options"></a>서비스 옵션 구성

다음 표에서 gRPC 서비스 구성에 대 한 옵션을 보여 줍니다.

| 옵션 | 기본값 | 설명 |
| ------ | ------------- | ----------- |
| `SendMaxMessageSize` | `null` | 서버에서 전송 될 수 있는 바이트의 최대 메시지 크기입니다. 예외에서 구성 된 최대 메시지 크기 결과 초과 하는 메시지를 보내려고 시도 합니다. |
| `ReceiveMaxMessageSize` | 4MB | 서버에서 받을 수 있는 바이트의 최대 메시지 크기입니다. 서버에서이 제한을 초과 하는 메시지를 수신 하는 경우 예외가 throw 됩니다. 이 값을 늘리면 서버가 더 큰 메시지를 수신할 수 있지만 메모리 소비에 부정적인 영향을 줄 수 있습니다. |
| `EnableDetailedErrors` | `false` | 경우 `true`자세한 서비스 메서드에서 예외가 throw 될 때 예외 메시지가 클라이언트로 반환 됩니다. 기본값은 `false`입니다. 이 값을 설정 `true` 중요 한 정보를 누출 할 수 있습니다. |
| `CompressionProviders` | Gzip | 압축 메시지를 압축 하는 데 사용 되는 압축 공급자의 컬렉션입니다. 사용자 지정 압축 공급자 생성 되어 컬렉션에 추가할 수 있습니다. 기본 구성 공급자에서 지 원하는 **gzip** 압축 합니다. |
| `ResponseCompressionAlgorithm` | `null` | 서버에서 전송 된 메시지를 압축 하는 데 압축 알고리즘입니다. 알고리즘에서 압축 공급자와 일치 해야 합니다 `CompressionProviders`합니다. 응답을 압축 하려면 algorthm에 대 한 클라이언트 전송 하 여 알고리즘을 지 원하는 지정 해야 합니다 **grpc-허용 인코딩** 헤더입니다. |
| `ResponseCompressionLevel` | `null` | 서버에서 전송 된 메시지를 압축 하는 데 사용 된 압축 수준입니다. |

하는 옵션 대리자를 제공 하 여 모든 서비스에 대 한 옵션을 구성할 수 있습니다 합니다 `AddGrpc` 에서 호출 `Startup.ConfigureServices`합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.EnableDetailedErrors = true;
    });
}
```

단일 서비스에 대 한 옵션에서 제공 하는 전역 옵션을 재정의 `AddGrpc` 를 사용 하 여 구성할 수 있습니다 및 `AddServiceOptions<TService>`:

```csharp
services.AddGrpc().AddServiceOptions<MyService>(options =>
{
    options.ReceiveMaxMessageSize = 10 * 1024 * 1024; // 10 megabytes
});
```

## <a name="configure-kestrel-options"></a>Kestrel 옵션 구성

Kestrel 서버에 ASP.NET 용 gRPC의 동작에 영향을 주는 구성 옵션이 있습니다.

### <a name="request-body-data-rate-limit"></a>요청 본문 데이터 속도 제한

기본적으로 Kestrel 서버 적용을 [최소 요청 본문 데이터 속도](
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>)합니다. 스트리밍 호출이 이중와 스트리밍 클라이언트에 대 한이 속도 충족할 수 있습니다 하 고 연결 시간이 초과 될 수 있습니다. 최소 요청 본문 데이터 속도 제한 gRPC 서비스 스트리밍 클라이언트 및 이중 교환 패턴 스트리밍 호출이 포함 된 경우 비활성화 해야 합니다.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
         Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
        webBuilder.ConfigureKestrel((context, options) =>
        {
            options.Limits.MinRequestBodyDataRate = null;
        });
    });
}
```

## <a name="additional-resources"></a>추가 자료

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
