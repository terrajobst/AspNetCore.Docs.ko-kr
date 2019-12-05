---
title: ASP.NET Core에 대 한 gRPC의 인증 및 권한 부여
author: jamesnk
description: ASP.NET Core에 대 한 gRPC에서 인증 및 권한 부여를 사용 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/13/2019
uid: grpc/authn-and-authz
ms.openlocfilehash: 84903ee781588ff525d1dfce6a313e3867794762
ms.sourcegitcommit: 76d7fff62014c3db02564191ab768acea00f1b26
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74852703"
---
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a>ASP.NET Core에 대 한 gRPC의 인증 및 권한 부여

별 [뉴턴-킹](https://twitter.com/jamesnk)

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(다운로드 방법)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-calling-a-grpc-service"></a>GRPC 서비스를 호출 하는 사용자 인증

gRPC는 [ASP.NET Core 인증과](xref:security/authentication/identity) 함께 사용 하 여 각 호출에 사용자를 연결할 수 있습니다.

다음은 gRPC 및 ASP.NET Core 인증을 사용 하는 `Startup.Configure`의 예입니다.

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseRouting();
    
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        routes.MapGrpcService<GreeterService>();
    });
}
```

> [!NOTE]
> ASP.NET Core 인증 미들웨어를 등록 하는 순서는 중요 합니다. `UseRouting` 후와 `UseEndpoints`하기 전에 항상 `UseAuthentication` 및 `UseAuthorization`를 호출 합니다.

호출 하는 동안 앱에서 사용 하는 인증 메커니즘을 구성 해야 합니다. 인증 구성은 `Startup.ConfigureServices`에 추가 되 고 앱이 사용 하는 인증 메커니즘에 따라 달라질 수 있습니다. ASP.NET Core 앱을 보호 하는 방법에 대 한 예제는 [인증 샘플](xref:security/authentication/samples)을 참조 하세요.

인증이 설정 되 면 사용자는 `ServerCallContext`를 통해 gRPC 서비스 메서드에서 액세스할 수 있습니다.

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a>전달자 토큰 인증

클라이언트는 인증을 위해 액세스 토큰을 제공할 수 있습니다. 서버는 토큰의 유효성을 검사하고 이를 이용해서 사용자를 식별합니다.

서버에서 전달자 토큰 인증은 [JWT 전달자 미들웨어](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer)를 사용하여 구성합니다.

.NET gRPC 클라이언트에서 토큰을 헤더로 호출 하 여 보낼 수 있습니다.

```csharp
public bool DoAuthenticatedCall(
    Ticketer.TicketerClient client, string token)
{
    var headers = new Metadata();
    headers.Add("Authorization", $"Bearer {token}");

    var request = new BuyTicketsRequest { Count = 1 };
    var response = await client.BuyTicketsAsync(request, headers);

    return response.Success;
}
```

채널에 `ChannelCredentials`를 구성 하는 방법은 gRPC 호출을 사용 하 여 서비스에 토큰을 전송 하는 대체 방법입니다. 자격 증명은 gRPC 호출이 수행 될 때마다 실행 되므로 토큰을 직접 전달 하는 여러 위치에서 코드를 작성할 필요가 없습니다.

다음 예제의 자격 증명은 모든 gRPC 호출로 토큰을 보내도록 채널을 구성 합니다.

```csharp
private static GrpcChannel CreateAuthenticatedChannel(string address)
{
    var credentials = CallCredentials.FromInterceptor((context, metadata) =>
    {
        if (!string.IsNullOrEmpty(_token))
        {
            metadata.Add("Authorization", $"Bearer {_token}");
        }
        return Task.CompletedTask;
    });

    // SslCredentials is used here because this channel is using TLS.
    // CallCredentials can't be used with ChannelCredentials.Insecure on non-TLS channels.
    var channel = GrpcChannel.ForAddress(address, new GrpcChannelOptions
    {
        Credentials = ChannelCredentials.Create(new SslCredentials(), credentials)
    });
    return channel;
}
```

### <a name="client-certificate-authentication"></a>클라이언트 인증서 인증

클라이언트는 인증을 위해 클라이언트 인증서를 제공할 수도 있습니다. [인증서 인증은](https://tools.ietf.org/html/rfc5246#section-7.4.4) ASP.NET Core 하기 전에는 매우 긴 TLS 수준에서 발생 합니다. 요청이 ASP.NET Core 들어가면 [클라이언트 인증서 인증 패키지](xref:security/authentication/certauth) 를 사용 하 여 `ClaimsPrincipal`인증서를 확인할 수 있습니다.

> [!NOTE]
> 클라이언트 인증서를 허용 하도록 호스트를 구성 해야 합니다. Kestrel, IIS 및 Azure에서 클라이언트 인증서를 허용 하는 방법에 대 한 정보는 [인증서를 요구 하도록 호스트 구성](xref:security/authentication/certauth#configure-your-host-to-require-certificates) 을 참조 하세요.

.NET gRPC 클라이언트에서 gRPC 클라이언트를 만드는 데 사용 되는 `HttpClientHandler`에 클라이언트 인증서가 추가 됩니다.

```csharp
public Ticketer.TicketerClient CreateClientWithCert(
    string baseAddress,
    X509Certificate2 certificate)
{
    // Add client cert to the handler
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(certificate);

    // Create the gRPC channel
    var channel = GrpcChannel.ForAddress(baseAddress, new GrpcChannelOptions
    {
        HttpClient = new HttpClient(handler)
    });

    return new Ticketer.TicketerClient(channel);
}
```

### <a name="other-authentication-mechanisms"></a>기타 인증 메커니즘

많은 ASP.NET Core 지원 되는 인증 메커니즘은 gRPC에서 작동 합니다.

* Azure Active Directory
* 클라이언트 인증서
* IdentityServer
* JWT 토큰
* OAuth 2.0
* OpenID Connect
* WS-Federation

서버에서 인증을 구성 하는 방법에 대 한 자세한 내용은 [ASP.NET Core 인증](xref:security/authentication/identity)을 참조 하세요.

인증을 사용 하도록 gRPC 클라이언트를 구성 하는 것은 사용 하는 인증 메커니즘에 따라 다릅니다. 이전 전달자 토큰 및 클라이언트 인증서 예제는 grpc 클라이언트를 사용 하 여 gRPC 호출로 인증 메타 데이터를 보내도록 구성할 수 있는 몇 가지 방법을 보여 줍니다.

* 강력한 형식의 gRPC 클라이언트는 내부적으로 `HttpClient`를 사용 합니다. [`HttpClientHandler`](/dotnet/api/system.net.http.httpclienthandler)에 대 한 인증을 구성 하거나 사용자 지정 [`HttpMessageHandler`](/dotnet/api/system.net.http.httpmessagehandler) 인스턴스를 `HttpClient`에 추가할 수 있습니다.
* 각 gRPC 호출에는 선택적 `CallOptions` 인수가 있습니다. 옵션의 헤더 컬렉션을 사용 하 여 사용자 지정 헤더를 보낼 수 있습니다.

> [!NOTE]
> Windows 인증 (NTLM/Kerberos/Negotiate)은 gRPC와 함께 사용할 수 없습니다. gRPC에는 h t t p/2가 필요 하며, HTTP/2는 Windows 인증을 지원 하지 않습니다.

## <a name="authorize-users-to-access-services-and-service-methods"></a>사용자에 게 서비스 및 서비스 메서드에 액세스할 수 있는 권한 부여

기본적으로 인증 되지 않은 사용자가 서비스의 모든 메서드를 호출할 수 있습니다. 인증을 요구 하려면 [[권한 부여]](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 특성을 서비스에 적용 합니다.

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

`[Authorize]` 특성의 생성자 인수 및 속성을 이용해서 특정 [권한 부여 정책](xref:security/authorization/policies)을 만족하는 사용자만 접근할 수 있도록 제한할 수 있습니다. 예를 들어 `MyAuthorizationPolicy`이라는 사용자 지정 권한 부여 정책을 사용 하는 경우 해당 정책과 일치 하는 사용자만 다음 코드를 사용 하 여 서비스에 액세스할 수 있도록 합니다.

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

개별 서비스 메서드는 `[Authorize]` 특성도 적용할 수 있습니다. 현재 사용자가 메서드와 클래스 **모두** 에 적용 된 정책과 일치 하지 않는 경우 호출자에 게 오류가 반환 됩니다.

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
    public override Task<AvailableTicketsResponse> GetAvailableTickets(
        Empty request, ServerCallContext context)
    {
        // ... buy tickets for the current user ...
    }

    [Authorize("Administrators")]
    public override Task<BuyTicketsResponse> RefundTickets(
        BuyTicketsRequest request, ServerCallContext context)
    {
        // ... refund tickets (something only Administrators can do) ..
    }
}
```

## <a name="additional-resources"></a>추가 자료

* [ASP.NET Core에서 전달자 토큰 인증](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [ASP.NET Core에서 클라이언트 인증서 인증 구성](xref:security/authentication/certauth)
