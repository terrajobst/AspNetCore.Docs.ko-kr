---
title: ASP.NET Core에 대 한 gRPC의 인증 및 권한 부여
author: jamesnk
description: ASP.NET Core에 대 한 gRPC에서 인증 및 권한 부여를 사용 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 07/26/2019
uid: grpc/authn-and-authz
ms.openlocfilehash: 34f7f8a5a22159329b3d6c4524943434c460c7fb
ms.sourcegitcommit: 0efb9e219fef481dee35f7b763165e488aa6cf9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602432"
---
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a><span data-ttu-id="b6b31-103">ASP.NET Core에 대 한 gRPC의 인증 및 권한 부여</span><span class="sxs-lookup"><span data-stu-id="b6b31-103">Authentication and authorization in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="b6b31-104">별 [뉴턴-킹](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="b6b31-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="b6b31-105">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(다운로드 방법)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="b6b31-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-calling-a-grpc-service"></a><span data-ttu-id="b6b31-106">GRPC 서비스를 호출 하는 사용자 인증</span><span class="sxs-lookup"><span data-stu-id="b6b31-106">Authenticate users calling a gRPC service</span></span>

<span data-ttu-id="b6b31-107">gRPC는 [ASP.NET Core 인증과](xref:security/authentication/identity) 함께 사용 하 여 각 호출에 사용자를 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6b31-107">gRPC can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each call.</span></span>

<span data-ttu-id="b6b31-108">다음은 grpc 및 ASP.NET Core `Startup.Configure` 인증을 사용 하는 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="b6b31-108">The following is an example of `Startup.Configure` which uses gRPC and ASP.NET Core authentication:</span></span>

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
> <span data-ttu-id="b6b31-109">ASP.NET Core 인증 미들웨어를 등록 하는 순서는 중요 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6b31-109">The order in which you register the ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="b6b31-110">항상 `UseAuthentication` 및`UseAuthorization` 전후를 호출 합니다.`UseEndpoints` `UseRouting`</span><span class="sxs-lookup"><span data-stu-id="b6b31-110">Always call `UseAuthentication` and `UseAuthorization` after `UseRouting` and before `UseEndpoints`.</span></span>

<span data-ttu-id="b6b31-111">인증이 설정 된 후에는를 `ServerCallContext`통해 grpc 서비스 메서드에서 사용자에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6b31-111">Once authentication has been setup, the user can be accessed in a gRPC service methods via the `ServerCallContext`.</span></span>

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a><span data-ttu-id="b6b31-112">전달자 토큰 인증</span><span class="sxs-lookup"><span data-stu-id="b6b31-112">Bearer token authentication</span></span>

<span data-ttu-id="b6b31-113">클라이언트는 인증을 위해 액세스 토큰을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6b31-113">The client can provide an access token for authentication.</span></span> <span data-ttu-id="b6b31-114">서버는 토큰의 유효성을 검사하고 이를 이용해서 사용자를 식별합니다.</span><span class="sxs-lookup"><span data-stu-id="b6b31-114">The server validates the token and uses it to identify the user.</span></span>

<span data-ttu-id="b6b31-115">서버에서 전달자 토큰 인증은 [JWT 전달자 미들웨어](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer)를 사용하여 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="b6b31-115">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="b6b31-116">.NET gRPC 클라이언트에서 토큰을 헤더로 호출 하 여 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6b31-116">In the .NET gRPC client, the token can be sent with calls as a header:</span></span>

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

### <a name="client-certificate-authentication"></a><span data-ttu-id="b6b31-117">클라이언트 인증서 인증</span><span class="sxs-lookup"><span data-stu-id="b6b31-117">Client certificate authentication</span></span>

<span data-ttu-id="b6b31-118">클라이언트는 인증을 위해 클라이언트 인증서를 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6b31-118">A client could alternatively provide a client certificate for authentication.</span></span> <span data-ttu-id="b6b31-119">[인증서 인증은](https://tools.ietf.org/html/rfc5246#section-7.4.4) ASP.NET Core 하기 전에는 매우 긴 TLS 수준에서 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6b31-119">[Certificate authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="b6b31-120">요청이 ASP.NET Core 들어가면 [클라이언트 인증서 인증 패키지](xref:security/authentication/certauth) 를 사용 하 여에 대 한 `ClaimsPrincipal`인증서를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6b31-120">When the request enters ASP.NET Core, the [client certificate authentication package](xref:security/authentication/certauth) allows you to resolve the certificate to a `ClaimsPrincipal`.</span></span>

> [!NOTE]
> <span data-ttu-id="b6b31-121">클라이언트 인증서를 허용 하도록 호스트를 구성 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6b31-121">The host needs to be configured to accept client certificates.</span></span> <span data-ttu-id="b6b31-122">Kestrel, IIS 및 Azure에서 클라이언트 인증서를 허용 하는 방법에 대 한 정보는 [인증서를 요구 하도록 호스트 구성](xref:security/authentication/certauth#configure-your-host-to-require-certificates) 을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="b6b31-122">See [configure your host to require certificates](xref:security/authentication/certauth#configure-your-host-to-require-certificates) for information on accepting client certificates in Kestrel, IIS and Azure.</span></span>

<span data-ttu-id="b6b31-123">.Net grpc 클라이언트에서 클라이언트 인증서가에 `HttpClientHandler` 추가 된 후 grpc 클라이언트를 만드는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6b31-123">In the .NET gRPC client, the client certificate is added to `HttpClientHandler` that is then used to create the gRPC client:</span></span>

```csharp
public Ticketer.TicketerClient CreateClientWithCert(
    string baseAddress,
    X509Certificate2 certificate)
{
    // Add client cert to the handler
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(certificate);

    // Create the gRPC client
    var httpClient = new HttpClient(handler);
    httpClient.BaseAddress = new Uri(baseAddress);

    return GrpcClient.Create<Ticketer.TicketerClient>(httpClient);
}
```

### <a name="other-authentication-mechanisms"></a><span data-ttu-id="b6b31-124">기타 인증 메커니즘</span><span class="sxs-lookup"><span data-stu-id="b6b31-124">Other authentication mechanisms</span></span>

<span data-ttu-id="b6b31-125">많은 ASP.NET Core 지원 되는 인증 메커니즘은 gRPC에서 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6b31-125">Many ASP.NET Core supported authentication mechanisms work with gRPC:</span></span>

* <span data-ttu-id="b6b31-126">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="b6b31-126">Azure Active Directory</span></span>
* <span data-ttu-id="b6b31-127">클라이언트 인증서</span><span class="sxs-lookup"><span data-stu-id="b6b31-127">Client Certificate</span></span>
* <span data-ttu-id="b6b31-128">IdentityServer</span><span class="sxs-lookup"><span data-stu-id="b6b31-128">IdentityServer</span></span>
* <span data-ttu-id="b6b31-129">JWT 토큰</span><span class="sxs-lookup"><span data-stu-id="b6b31-129">JWT Token</span></span>
* <span data-ttu-id="b6b31-130">OAuth 2.0</span><span class="sxs-lookup"><span data-stu-id="b6b31-130">OAuth 2.0</span></span>
* <span data-ttu-id="b6b31-131">Openid connect 연결</span><span class="sxs-lookup"><span data-stu-id="b6b31-131">OpenID Connect</span></span>
* <span data-ttu-id="b6b31-132">WS-Federation</span><span class="sxs-lookup"><span data-stu-id="b6b31-132">WS-Federation</span></span>

<span data-ttu-id="b6b31-133">서버에서 인증을 구성 하는 방법에 대 한 자세한 내용은 [ASP.NET Core 인증](xref:security/authentication/identity)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="b6b31-133">For more information on configuring authentication on the server, see [ASP.NET Core authentication](xref:security/authentication/identity).</span></span>

<span data-ttu-id="b6b31-134">인증을 사용 하도록 gRPC 클라이언트를 구성 하는 것은 사용 하는 인증 메커니즘에 따라 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="b6b31-134">Configuring the gRPC client to use authentication will depend on the authentication mechanism you are using.</span></span> <span data-ttu-id="b6b31-135">이전 전달자 토큰 및 클라이언트 인증서 예제는 grpc 클라이언트를 사용 하 여 gRPC 호출로 인증 메타 데이터를 보내도록 구성할 수 있는 몇 가지 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="b6b31-135">The previous bearer token and client certificate examples show a couple of ways the gRPC client can be configured to send authentication metadata with gRPC calls:</span></span>

* <span data-ttu-id="b6b31-136">강력한 형식의 grpc 클라이언트는 `HttpClient` 내부적으로를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6b31-136">Strongly typed gRPC clients use `HttpClient` internally.</span></span> <span data-ttu-id="b6b31-137">인증은에 [`HttpClientHandler`](/dotnet/api/system.net.http.httpclienthandler)구성 하거나 사용자 지정 [`HttpMessageHandler`](/dotnet/api/system.net.http.httpmessagehandler) 인스턴스 `HttpClient`를에 추가 하 여 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6b31-137">Authentication can be configured on [`HttpClientHandler`](/dotnet/api/system.net.http.httpclienthandler), or by adding custom [`HttpMessageHandler`](/dotnet/api/system.net.http.httpmessagehandler) instances to the `HttpClient`.</span></span>
* <span data-ttu-id="b6b31-138">각 grpc 호출에는 선택적 `CallOptions` 인수가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6b31-138">Each gRPC call has an optional `CallOptions` argument.</span></span> <span data-ttu-id="b6b31-139">옵션의 헤더 컬렉션을 사용 하 여 사용자 지정 헤더를 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6b31-139">Custom headers can be sent using the option's headers collection.</span></span>

> [!NOTE]
> <span data-ttu-id="b6b31-140">Windows 인증 (NTLM/Kerberos/Negotiate)은 gRPC와 함께 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="b6b31-140">Windows Authentication (NTLM/Kerberos/Negotiate) can't be used with gRPC.</span></span> <span data-ttu-id="b6b31-141">gRPC에는 h t t p/2가 필요 하며, HTTP/2는 Windows 인증을 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b6b31-141">gRPC requires HTTP/2, and HTTP/2 doesn't support Windows Authentication.</span></span>

## <a name="authorize-users-to-access-services-and-service-methods"></a><span data-ttu-id="b6b31-142">사용자에 게 서비스 및 서비스 메서드에 액세스할 수 있는 권한 부여</span><span class="sxs-lookup"><span data-stu-id="b6b31-142">Authorize users to access services and service methods</span></span>

<span data-ttu-id="b6b31-143">기본적으로 인증 되지 않은 사용자가 서비스의 모든 메서드를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6b31-143">By default, all methods in a service can be called by unauthenticated users.</span></span> <span data-ttu-id="b6b31-144">인증을 요구 하려면 [[권한 부여]](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 특성을 서비스에 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6b31-144">To require authentication, apply the [[Authorize]](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the service:</span></span>

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="b6b31-145">`[Authorize]` 특성의 생성자 인수 및 속성을 이용해서 특정 [권한 부여 정책](xref:security/authorization/policies)을 만족하는 사용자만 접근할 수 있도록 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6b31-145">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="b6b31-146">예를 들어, 라는 `MyAuthorizationPolicy`사용자 지정 권한 부여 정책을 사용 하는 경우 해당 정책과 일치 하는 사용자만 다음 코드를 사용 하 여 서비스에 액세스할 수 있도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="b6b31-146">For example, if you have a custom authorization policy called `MyAuthorizationPolicy`, ensure that only users matching that policy can access the service using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="b6b31-147">개별 서비스 메서드에도 특성을 `[Authorize]` 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b6b31-147">Individual service methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="b6b31-148">현재 사용자가 메서드와 클래스 **모두** 에 적용 된 정책과 일치 하지 않는 경우 호출자에 게 오류가 반환 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b6b31-148">If the current user doesn't match the policies applied to **both** the method and the class, an error is returned to the caller:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="b6b31-149">추가 자료</span><span class="sxs-lookup"><span data-stu-id="b6b31-149">Additional resources</span></span>

* [<span data-ttu-id="b6b31-150">ASP.NET Core에서 전달자 토큰 인증</span><span class="sxs-lookup"><span data-stu-id="b6b31-150">Bearer Token authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="b6b31-151">ASP.NET Core에서 클라이언트 인증서 인증 구성</span><span class="sxs-lookup"><span data-stu-id="b6b31-151">Configure Client Certificate authentication in ASP.NET Core</span></span>](xref:security/authentication/certauth)
