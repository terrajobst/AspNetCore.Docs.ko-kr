---
title: ASP.NET Core SignalR의 인증 및 권한 부여
author: bradygaster
description: ASP.NET Core SignalR에서 인증 및 권한 부여를 사용 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- SignalR
uid: signalr/authn-and-authz
ms.openlocfilehash: 091cc9b2adc1f6a8fac79519884695d1c1725d2a
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880413"
---
# <a name="authentication-and-authorization-in-aspnet-core-opno-locsignalr"></a><span data-ttu-id="7a4bf-103">ASP.NET Core SignalR의 인증 및 권한 부여</span><span class="sxs-lookup"><span data-stu-id="7a4bf-103">Authentication and authorization in ASP.NET Core SignalR</span></span>

<span data-ttu-id="7a4bf-104">작성자: [Andrew Stanton-Nurse](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="7a4bf-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="7a4bf-105">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(다운로드 방법)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="7a4bf-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-connecting-to-a-opno-locsignalr-hub"></a><span data-ttu-id="7a4bf-106">SignalR 허브에 연결 하는 사용자 인증</span><span class="sxs-lookup"><span data-stu-id="7a4bf-106">Authenticate users connecting to a SignalR hub</span></span>

SignalR<span data-ttu-id="7a4bf-107">를 [ASP.NET Core 인증과](xref:security/authentication/identity) 함께 사용 하 여 사용자를 각 연결에 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-107"> can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each connection.</span></span> <span data-ttu-id="7a4bf-108">허브에서는 [HubConnectionContext](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) 속성을 사용 하 여 인증 데이터에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-108">In a hub, authentication data can be accessed from the [HubConnectionContext.User](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) property.</span></span> <span data-ttu-id="7a4bf-109">인증을 통해 허브는 사용자와 연결 된 모든 연결에서 메서드를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-109">Authentication allows the hub to call methods on all connections associated with a user.</span></span> <span data-ttu-id="7a4bf-110">자세한 내용은 [SignalR에서 사용자 및 그룹 관리 ](xref:signalr/groups)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-110">For more information, see [Manage users and groups in SignalR](xref:signalr/groups).</span></span> <span data-ttu-id="7a4bf-111">단일 사용자에게 다수의 연결을 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-111">Multiple connections may be associated with a single user.</span></span>

<span data-ttu-id="7a4bf-112">다음은 SignalR 및 ASP.NET Core 인증을 사용 하는 `Startup.Configure`의 예입니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-112">The following is an example of `Startup.Configure` which uses SignalR and ASP.NET Core authentication:</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseRouting();

    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chat");
        endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseAuthentication();

    app.UseSignalR(hubs =>
    {
        hubs.MapHub<ChatHub>("/chat");
    });

    app.UseMvc(routes =>
    {
        routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

> [!NOTE]
> <span data-ttu-id="7a4bf-113">SignalR를 등록 하 고 인증 미들웨어를 ASP.NET Core 하는 순서는 중요 합니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-113">The order in which you register the SignalR and ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="7a4bf-114">`HttpContext`에 대 한 사용자가 SignalR 있도록 `UseSignalR` 하기 전에 항상 `UseAuthentication`를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-114">Always call `UseAuthentication` before `UseSignalR` so that SignalR has a user on the `HttpContext`.</span></span>

::: moniker-end

### <a name="cookie-authentication"></a><span data-ttu-id="7a4bf-115">쿠키 인증</span><span class="sxs-lookup"><span data-stu-id="7a4bf-115">Cookie authentication</span></span>

<span data-ttu-id="7a4bf-116">브라우저 기반 앱에서 쿠키 인증을 사용 하면 기존 사용자 자격 증명이 SignalR 연결로 자동으로 전달 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-116">In a browser-based app, cookie authentication allows your existing user credentials to automatically flow to SignalR connections.</span></span> <span data-ttu-id="7a4bf-117">브라우저 클라이언트를 사용하면 추가적인 구성이 필요 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-117">When using the browser client, no additional configuration is needed.</span></span> <span data-ttu-id="7a4bf-118">사용자가 앱에 로그인 하면 SignalR 연결이 자동으로이 인증을 상속 합니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-118">If the user is logged in to your app, the SignalR connection automatically inherits this authentication.</span></span>

<span data-ttu-id="7a4bf-119">쿠키는 액세스 토큰을 전송하는 브라우저 고유의 방식이지만 비 브라우저 클라이언트에서도 쿠키를 전송할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-119">Cookies are a browser-specific way to send access tokens, but non-browser clients can send them.</span></span> <span data-ttu-id="7a4bf-120">[.Net 클라이언트](xref:signalr/dotnet-client)를 사용 하는 경우 `.WithUrl` 호출에서 `Cookies` 속성을 구성 하 여 쿠키를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-120">When using the [.NET Client](xref:signalr/dotnet-client), the `Cookies` property can be configured in the `.WithUrl` call to provide a cookie.</span></span> <span data-ttu-id="7a4bf-121">그러나 .NET 클라이언트에서 쿠키 인증을 사용 하려면 앱이 쿠키에 대 한 인증 데이터를 교환 하는 API를 제공 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-121">However, using cookie authentication from the .NET client requires the app to provide an API to exchange authentication data for a cookie.</span></span>

### <a name="bearer-token-authentication"></a><span data-ttu-id="7a4bf-122">전달자 토큰 인증</span><span class="sxs-lookup"><span data-stu-id="7a4bf-122">Bearer token authentication</span></span>

<span data-ttu-id="7a4bf-123">클라이언트는 쿠키를 사용하는 대신 액세스 토큰을 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-123">The client can provide an access token instead of using a cookie.</span></span> <span data-ttu-id="7a4bf-124">서버는 토큰의 유효성을 검사하고 이를 이용해서 사용자를 식별합니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-124">The server validates the token and uses it to identify the user.</span></span> <span data-ttu-id="7a4bf-125">이 유효성 검사는 연결이 설정될 때만 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-125">This validation is done only when the connection is established.</span></span> <span data-ttu-id="7a4bf-126">서버는 연결이 유지되는 동안 토큰이 해지되었는지를 확인하기 위해 자동으로 유효성을 재검사하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-126">During the life of the connection, the server doesn't automatically revalidate to check for token revocation.</span></span>

<span data-ttu-id="7a4bf-127">서버에서 전달자 토큰 인증은 [JWT 전달자 미들웨어](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer)를 사용하여 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-127">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="7a4bf-128">JavaScript 클라이언트에서는 [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) 옵션을 통해서 토큰을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-128">In the JavaScript client, the token can be provided using the [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) option.</span></span>

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=52-55)]

<span data-ttu-id="7a4bf-129">.NET 클라이언트에는 토큰을 구성 하는 데 사용할 수 있는 유사한 [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-129">In the .NET client, there's a similar [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) property that can be used to configure the token:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="7a4bf-130">사용자가 제공 하는 액세스 토큰 함수는 SignalR에서 수행 하는 **모든** HTTP 요청 전에 호출 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-130">The access token function you provide is called before **every** HTTP request made by SignalR.</span></span> <span data-ttu-id="7a4bf-131">연결을 활성 상태로 유지하기 위해 토큰을 갱신해야 할 경우 (연결 중 만료될 수 있으므로), 이 함수 내에서 작업을 수행하고 갱신된 토큰을 반환해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-131">If you need to renew the token in order to keep the connection active (because it may expire during the connection), do so from within this function and return the updated token.</span></span>

<span data-ttu-id="7a4bf-132">표준 웹 API에서 전달자 토큰은 HTTP 헤더로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-132">In standard web APIs, bearer tokens are sent in an HTTP header.</span></span> <span data-ttu-id="7a4bf-133">그러나 일부 전송을 사용 하는 경우 SignalR는 브라우저에서 이러한 헤더를 설정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-133">However, SignalR is unable to set these headers in browsers when using some transports.</span></span> <span data-ttu-id="7a4bf-134">WebSockets 및 서버-전송 이벤트를 사용할 경우 토큰은 쿼리 문자열 매개 변수로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-134">When using WebSockets and Server-Sent Events, the token is transmitted as a query string parameter.</span></span> <span data-ttu-id="7a4bf-135">서버에서이를 지원 하려면 추가 구성이 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-135">To support this on the server, additional configuration is required:</span></span>

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="7a4bf-136">쿼리 문자열은 브라우저 API 제한으로 인해 Websocket 및 서버에서 전송한 이벤트에 연결할 때 브라우저에서 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-136">The query string is used on browsers when connecting with WebSockets and Server-Sent Events due to browser API limitations.</span></span> <span data-ttu-id="7a4bf-137">HTTPS를 사용 하는 경우 쿼리 문자열 값은 TLS 연결을 통해 보안이 유지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-137">When using HTTPS, query string values are secured by the TLS connection.</span></span> <span data-ttu-id="7a4bf-138">그러나 많은 서버에서 쿼리 문자열 값을 기록 합니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-138">However, many servers log query string values.</span></span> <span data-ttu-id="7a4bf-139">자세한 내용은 [ASP.NET Core SignalR의 보안 고려 사항 ](xref:signalr/security)을 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-139">For more information, see [Security considerations in ASP.NET Core SignalR](xref:signalr/security).</span></span> SignalR<span data-ttu-id="7a4bf-140">는 헤더를 사용 하 여 토큰 (예: .NET 및 Java 클라이언트)을 지 원하는 환경에서 토큰을 전송 합니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-140"> uses headers to transmit tokens in environments which support them (such as the .NET and Java clients).</span></span>

### <a name="cookies-vs-bearer-tokens"></a><span data-ttu-id="7a4bf-141">쿠키 대 전달자 토큰</span><span class="sxs-lookup"><span data-stu-id="7a4bf-141">Cookies vs. bearer tokens</span></span> 

<span data-ttu-id="7a4bf-142">쿠키는 브라우저에만 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-142">Cookies are specific to browsers.</span></span> <span data-ttu-id="7a4bf-143">다른 종류의 클라이언트에서 전송 하면 전달자 토큰 전송과 비교 하 여 복잡성이 증가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-143">Sending them from other kinds of clients adds complexity compared to sending bearer tokens.</span></span> <span data-ttu-id="7a4bf-144">따라서 앱이 브라우저 클라이언트에서 사용자를 인증 해야 하는 경우를 제외 하 고는 쿠키 인증을 권장 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-144">Consequently, cookie authentication isn't recommended unless the app only needs to authenticate users from the browser client.</span></span> <span data-ttu-id="7a4bf-145">브라우저 클라이언트 이외의 클라이언트도 사용할 경우 전달자 토큰 인증이 권장되는 방식입니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-145">Bearer token authentication is the recommended approach when using clients other than the browser client.</span></span>

### <a name="windows-authentication"></a><span data-ttu-id="7a4bf-146">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="7a4bf-146">Windows authentication</span></span>

<span data-ttu-id="7a4bf-147">앱에서 [Windows 인증](xref:security/authentication/windowsauth) 을 구성 하는 경우 해당 id를 사용 하 여 허브를 보호할 수 SignalR.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-147">If [Windows authentication](xref:security/authentication/windowsauth) is configured in your app, SignalR can use that identity to secure hubs.</span></span> <span data-ttu-id="7a4bf-148">그러나 메시지를 개별 사용자에 게 보내려면 사용자 지정 사용자 ID 공급자를 추가 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-148">However, to send messages to individual users, you need to add a custom User ID provider.</span></span> <span data-ttu-id="7a4bf-149">Windows 인증 시스템이 "이름 식별자" 클레임을 제공 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-149">The Windows authentication system doesn't provide the "Name Identifier" claim.</span></span> SignalR<span data-ttu-id="7a4bf-150">는 클레임을 사용 하 여 사용자 이름을 결정 합니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-150"> uses the claim to determine the user name.</span></span>

<span data-ttu-id="7a4bf-151">`IUserIdProvider`를 구현하는 새 클래스를 추가하고 사용자로부터 식별자로 사용할 클레임 중 하나를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-151">Add a new class that implements `IUserIdProvider` and retrieve one of the claims from the user to use as the identifier.</span></span> <span data-ttu-id="7a4bf-152">예를 들어, "Name" 클레임(`[Domain]\[Username]` 형태의 Windows 사용자 이름)을 사용하려면 다음과 같은 클래스를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-152">For example, to use the "Name" claim (which is the Windows username in the form `[Domain]\[Username]`), create the following class:</span></span>

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

<span data-ttu-id="7a4bf-153">`ClaimTypes.Name`하는 대신 `User`의 모든 값 (예: Windows SID 식별자 등)을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-153">Rather than `ClaimTypes.Name`, you can use any value from the `User` (such as the Windows SID identifier, and so on).</span></span>

> [!NOTE]
> <span data-ttu-id="7a4bf-154">선택한 값은 시스템의 모든 사용자 간에 고유해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-154">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="7a4bf-155">그렇지 않으면 특정 사용자를 대상으로 한 메시지가 다른 사용자에게 전달될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-155">Otherwise, a message intended for one user could end up going to a different user.</span></span>

<span data-ttu-id="7a4bf-156">`Startup.ConfigureServices` 메서드에이 구성 요소를 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-156">Register this component in your `Startup.ConfigureServices` method.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

<span data-ttu-id="7a4bf-157">.NET 클라이언트에서는 [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) 속성을 설정해서 Windows 인증을 활성화시켜야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-157">In the .NET Client, Windows Authentication must be enabled by setting the [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) property:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

<span data-ttu-id="7a4bf-158">Windows 인증은 Microsoft Internet Explorer 또는 Microsoft Edge를 사용하는 경우에만 브라우저 클라이언트에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-158">Windows Authentication is only supported by the browser client when using Microsoft Internet Explorer or Microsoft Edge.</span></span>

### <a name="use-claims-to-customize-identity-handling"></a><span data-ttu-id="7a4bf-159">클레임을 사용 하 여 id 처리 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="7a4bf-159">Use claims to customize identity handling</span></span>

<span data-ttu-id="7a4bf-160">사용자를 인증 하는 앱은 사용자 클레임에서 사용자 Id SignalR 파생 시킬 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-160">An app that authenticates users can derive SignalR user IDs from user claims.</span></span> <span data-ttu-id="7a4bf-161">SignalR 사용자 Id를 만드는 방법을 지정 하려면 `IUserIdProvider`를 구현 하 고 구현을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-161">To specify how SignalR creates user IDs, implement `IUserIdProvider` and register the implementation.</span></span>

<span data-ttu-id="7a4bf-162">샘플 코드는 클레임을 사용 하 여 사용자의 이메일 주소를 식별 속성으로 선택 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-162">The sample code demonstrates how you would use claims to select the user's email address as the identifying property.</span></span> 

> [!NOTE]
> <span data-ttu-id="7a4bf-163">선택한 값은 시스템의 모든 사용자 간에 고유해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-163">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="7a4bf-164">그렇지 않으면 특정 사용자를 대상으로 한 메시지가 다른 사용자에게 전달될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-164">Otherwise, a message intended for one user could end up going to a different user.</span></span>

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

<span data-ttu-id="7a4bf-165">계정 등록은 ASP.NET id 데이터베이스에 `ClaimsTypes.Email` 유형의 클레임을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-165">The account registration adds a claim with type `ClaimsTypes.Email` to the ASP.NET identity database.</span></span>

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

<span data-ttu-id="7a4bf-166">이 구성 요소를 `Startup.ConfigureServices`에 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-166">Register this component in your `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a><span data-ttu-id="7a4bf-167">허브 및 허브 메서드 접근에 대한 사용자 권한 부여</span><span class="sxs-lookup"><span data-stu-id="7a4bf-167">Authorize users to access hubs and hub methods</span></span>

<span data-ttu-id="7a4bf-168">기본적으로, 인증되지 않은 사용자도 허브의 모든 메서드를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-168">By default, all methods in a hub can be called by an unauthenticated user.</span></span> <span data-ttu-id="7a4bf-169">인증을 요구 하려면 [권한 부여](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) 특성을 허브에 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-169">To require authentication, apply the [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) attribute to the hub:</span></span>

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

<span data-ttu-id="7a4bf-170">`[Authorize]` 특성의 생성자 인수 및 속성을 이용해서 특정 [권한 부여 정책](xref:security/authorization/policies)을 만족하는 사용자만 접근할 수 있도록 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-170">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="7a4bf-171">예를 들어 `MyAuthorizationPolicy`라는 사용자 지정 권한 부여 정책이 존재할 경우, 다음과 같은 코드를 사용해서 해당 정책을 만족하는 사용자만 허브에 접근할 수 있도록 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-171">For example, if you have a custom authorization policy called `MyAuthorizationPolicy` you can ensure that only users matching that policy can access the hub using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub : Hub
{
}
```

<span data-ttu-id="7a4bf-172">각각의 허브 메서드에도 `[Authorize]` 특성을 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-172">Individual hub methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="7a4bf-173">현재 사용자가 메서드에 적용된 정책을 만족하지 않으면 호출자에게 오류가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-173">If the current user doesn't match the policy applied to the method, an error is returned to the caller:</span></span>

```csharp
[Authorize]
public class ChatHub : Hub
{
    public async Task Send(string message)
    {
        // ... send a message to all users ...
    }

    [Authorize("Administrators")]
    public void BanUser(string userName)
    {
        // ... ban a user from the chat room (something only Administrators can do) ...
    }
}
```

::: moniker range=">= aspnetcore-3.0"

### <a name="use-authorization-handlers-to-customize-hub-method-authorization"></a><span data-ttu-id="7a4bf-174">권한 부여 처리기를 사용 하 여 허브 메서드 권한 부여 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="7a4bf-174">Use authorization handlers to customize hub method authorization</span></span>

SignalR<span data-ttu-id="7a4bf-175">는 허브 메서드가 권한 부여를 요구할 때 권한 부여 처리기에 사용자 지정 리소스를 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-175"> provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="7a4bf-176">리소스가 `HubInvocationContext`의 인스턴스인 경우</span><span class="sxs-lookup"><span data-stu-id="7a4bf-176">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="7a4bf-177">`HubInvocationContext`는 `HubCallerContext`, 호출 되는 허브 메서드의 이름 및 허브 메서드에 대 한 인수를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-177">The `HubInvocationContext` includes the `HubCallerContext`, the name of the hub method being invoked, and the arguments to the hub method.</span></span>

<span data-ttu-id="7a4bf-178">Azure Active Directory를 통해 여러 조직 로그인을 허용 하는 대화방의 예를 생각해 보세요.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-178">Consider the example of a chat room allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="7a4bf-179">Microsoft 계정 있는 사용자는 채팅에 로그인 할 수 있지만 소유 조직의 구성원만 사용자를 금지 하거나 사용자의 채팅 기록을 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-179">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization should be able to ban users or view users' chat histories.</span></span> <span data-ttu-id="7a4bf-180">또한 특정 사용자의 특정 기능을 제한 해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-180">Furthermore, we might want to restrict certain functionality from certain users.</span></span> <span data-ttu-id="7a4bf-181">ASP.NET Core 3.0의 업데이트 된 기능을 사용 하는 것이 가능 합니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-181">Using the updated features in ASP.NET Core 3.0, this is entirely possible.</span></span> <span data-ttu-id="7a4bf-182">`DomainRestrictedRequirement` 사용자 지정 `IAuthorizationRequirement`으로 사용 되는 방법을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-182">Note how the `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="7a4bf-183">`HubInvocationContext` 리소스 매개 변수가 전달 되 고 있으므로 내부 논리는 허브가 호출 되는 컨텍스트를 검사 하 고 사용자가 개별 허브 메서드를 실행 하도록 허용 하는 결정을 내릴 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-183">Now that the `HubInvocationContext` resource parameter is being passed in, the internal logic can inspect the context in which the Hub is being called and make decisions on allowing the user to execute individual Hub methods.</span></span>

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}

public class DomainRestrictedRequirement : 
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>, 
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement, 
        HubInvocationContext resource)
    {
        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name) && 
            context.User.Identity.Name.EndsWith("@microsoft.com"))
        {
            context.Succeed(requirement);
        }
        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName,
        string currentUsername)
    {
        return !(currentUsername.Equals("asdf42@microsoft.com") && 
            hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase));
    }
}
```

<span data-ttu-id="7a4bf-184">`Startup.ConfigureServices`에서 사용자 지정 `DomainRestrictedRequirement` 요구 사항을 매개 변수로 제공 하 여 `DomainRestricted` 정책을 만드는 새 정책을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-184">In `Startup.ConfigureServices`, add the new policy, providing the custom `DomainRestrictedRequirement` requirement as a parameter to create the `DomainRestricted` policy.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services
        .AddAuthorization(options =>
        {
            options.AddPolicy("DomainRestricted", policy =>
            {
                policy.Requirements.Add(new DomainRestrictedRequirement());
            });
        });
}
```

<span data-ttu-id="7a4bf-185">앞의 예제에서 `DomainRestrictedRequirement` 클래스는 해당 요구 사항에 대 한 `IAuthorizationRequirement` 및 자체 `AuthorizationHandler`입니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-185">In the preceding example, the `DomainRestrictedRequirement` class is both an `IAuthorizationRequirement` and its own `AuthorizationHandler` for that requirement.</span></span> <span data-ttu-id="7a4bf-186">이러한 두 구성 요소를 별도의 클래스로 분할 하 여 문제를 구분할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-186">It's acceptable to split these two components into separate classes to separate concerns.</span></span> <span data-ttu-id="7a4bf-187">예제의 방법의 장점은 요구 사항과 처리기가 동일 하기 때문에 시작 하는 동안 `AuthorizationHandler`를 주입할 필요가 없다는 점입니다.</span><span class="sxs-lookup"><span data-stu-id="7a4bf-187">A benefit of the example's approach is there's no need to inject the `AuthorizationHandler` during startup, as the requirement and the handler are the same thing.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="7a4bf-188">추가 자료</span><span class="sxs-lookup"><span data-stu-id="7a4bf-188">Additional resources</span></span>

* [<span data-ttu-id="7a4bf-189">ASP.NET Core에서 전달자 토큰 인증</span><span class="sxs-lookup"><span data-stu-id="7a4bf-189">Bearer Token Authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="7a4bf-190">리소스 기반 권한 부여</span><span class="sxs-lookup"><span data-stu-id="7a4bf-190">Resource-based Authorization</span></span>](xref:security/authorization/resourcebased)
