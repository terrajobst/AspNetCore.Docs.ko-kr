---
title: ASP.NET Core에 대 한 gRPC의 보안 고려 사항
author: jamesnk
description: ASP.NET Core에 대 한 gRPC의 보안 고려 사항에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/07/2019
uid: grpc/security
ms.openlocfilehash: f84bec0ef485b701b2be36384a2e49b9b28e473d
ms.sourcegitcommit: 8b36f75b8931ae3f656e2a8e63572080adc78513
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70310369"
---
# <a name="security-considerations-in-grpc-for-aspnet-core"></a>ASP.NET Core에 대 한 gRPC의 보안 고려 사항

별 [뉴턴-킹](https://twitter.com/jamesnk)

이 문서에서는 .NET Core로 gRPC를 보호 하는 방법에 대 한 정보를 제공 합니다.

## <a name="transport-security"></a>전송 보안

gRPC 메시지는 HTTP/2를 사용 하 여 전송 및 수신 됩니다. 다음을 권장 합니다.

* [TLS (전송 계층 보안)](https://tools.ietf.org/html/rfc5246) 는 프로덕션 grpc 앱에서 메시지를 보호 하는 데 사용 됩니다.
* gRPC 서비스는 보안 포트만 수신 하 고 응답 해야 합니다.

TLS는 Kestrel에서 구성 됩니다. Kestrel 끝점을 구성 하는 방법에 대 한 자세한 내용은 [Kestrel 끝점 구성](xref:fundamentals/servers/kestrel#endpoint-configuration)을 참조 하세요.

## <a name="exceptions"></a>예외

일반적으로 예외 메시지는 민감한 데이터로 간주되어 클라이언트에게 노출되어서는 안 됩니다. 기본적으로 gRPC는 gRPC 서비스에서 throw 되는 예외에 대 한 세부 정보를 클라이언트에 보내지 않습니다. 대신 클라이언트는 오류가 발생했음을 나타내는 일반적인 메시지를 수신합니다. [EnableDetailedErrors](xref:grpc/configuration#configure-services-options)를 사용 하 여 클라이언트에 대 한 예외 메시지 배달 (예: 개발 또는 테스트)을 재정의할 수 있습니다. 프로덕션 앱에서 예외 메시지를 클라이언트에 노출 해서는 안 됩니다.

## <a name="message-size-limits"></a>메시지 크기 제한

GRPC 클라이언트 및 서비스에 대 한 들어오는 메시지는 메모리에 로드 됩니다. 메시지 크기 제한은 gRPC가 과도 한 리소스를 사용 하는 것을 방지 하는 메커니즘입니다.

gRPC는 메시지당 크기 제한을 사용 하 여 들어오고 나가는 메시지를 관리 합니다. 기본적으로 gRPC는 들어오는 메시지를 4mb로 제한 합니다. 보내는 메시지에 대 한 제한은 없습니다.

서버에서 다음을 사용 하 여 `AddGrpc`앱의 모든 서비스에 대해 grpc 메시지 제한을 구성할 수 있습니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 1 * 1024 * 1024; // 1 MB
        options.MaxSendMessageSize = 1 * 1024 * 1024; // 1 MB
    });
}
```

를 사용 하 여 `AddServiceOptions<TService>`개별 서비스에 대해 제한을 구성할 수도 있습니다. 메시지 크기 제한을 구성 하는 방법에 대 한 자세한 내용은 [Grpc 구성](xref:grpc/configuration)을 참조 하세요.

## <a name="client-certificate-validation"></a>클라이언트 인증서 유효성 검사

[클라이언트 인증서](https://tools.ietf.org/html/rfc5246#section-7.4.4) 는 연결이 설정 될 때 초기에 유효성이 검사 됩니다. 기본적으로 Kestrel은 연결의 클라이언트 인증서에 대 한 추가 유효성 검사를 수행 하지 않습니다.

클라이언트 인증서로 보호 되는 gRPC 서비스를 사용 하는 것이 [AspNetCore](xref:security/authentication/certauth) 패키지를 사용 하는 것이 좋습니다. ASP.NET Core 인증 인증은 다음을 비롯 한 클라이언트 인증서에 대 한 추가 유효성 검사를 수행 합니다.

* 인증서에 유효한 EKU (확장 키 사용)가 있습니다.
* 유효 기간 내에 있습니다.
* 인증서 해지 확인
