---
title: ASP.NET Core SignalR의 인증 및 권한 부여
author: bradygaster
description: ASP.NET Core SignalR에서 인증 및 권한 부여를 사용하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 05/09/2019
uid: signalr/authn-and-authz
ms.openlocfilehash: e8f9dc48be780fb91bdec6ea4d579f5e4f16197b
ms.sourcegitcommit: 3376f224b47a89acf329b2d2f9260046a372f924
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65516950"
---
# <a name="authentication-and-authorization-in-aspnet-core-signalr"></a><span data-ttu-id="89bfd-103">ASP.NET Core SignalR의 인증 및 권한 부여</span><span class="sxs-lookup"><span data-stu-id="89bfd-103">Authentication and authorization in ASP.NET Core SignalR</span></span>

<span data-ttu-id="89bfd-104">작성자: [Andrew Stanton-Nurse](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="89bfd-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="89bfd-105">[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(다운로드 방법)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="89bfd-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-connecting-to-a-signalr-hub"></a><span data-ttu-id="89bfd-106">SignalR 허브에 연결하는 사용자 인증하기</span><span class="sxs-lookup"><span data-stu-id="89bfd-106">Authenticate users connecting to a SignalR hub</span></span>

<span data-ttu-id="89bfd-107">SignalR을 [ASP.NET Core 인증](xref:security/authentication/identity)과 함께 사용하여 각 연결에 사용자를 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-107">SignalR can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each connection.</span></span> <span data-ttu-id="89bfd-108">허브에서는 [`HubConnectionContext.User`](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) 속성을 통해서 인증 데이터에 접근할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-108">In a hub, authentication data can be accessed from the [`HubConnectionContext.User`](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) property.</span></span> <span data-ttu-id="89bfd-109">인증을 사용하면 허브가 특정 사용자와 관련된 모든 연결에서 메서드를 호출할 수 있습니다(자세한 정보는 [SignalR의 사용자 및 그룹 관리](xref:signalr/groups)를 참고하시기 바랍니다).</span><span class="sxs-lookup"><span data-stu-id="89bfd-109">Authentication allows the hub to call methods on all connections associated with a user (See [Manage users and groups in SignalR](xref:signalr/groups) for more information).</span></span> <span data-ttu-id="89bfd-110">단일 사용자에게 다수의 연결을 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-110">Multiple connections may be associated with a single user.</span></span>

<span data-ttu-id="89bfd-111">다음은 예가 `Startup.Configure` SignalR 및 ASP.NET Core 인증을 사용 하는:</span><span class="sxs-lookup"><span data-stu-id="89bfd-111">The following is an example of `Startup.Configure` which uses SignalR and ASP.NET Core authentication:</span></span>

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
> <span data-ttu-id="89bfd-112">SignalR 및 ASP.NET Core 인증 미들웨어를 등록 하는 순서가 중요 합니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-112">The order in which you register the SignalR and ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="89bfd-113">항상 호출 `UseAuthentication` 하기 전에 `UseSignalR` SignalR 사용자에 있도록는 `HttpContext`합니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-113">Always call `UseAuthentication` before `UseSignalR` so that SignalR has a user on the `HttpContext`.</span></span>

### <a name="cookie-authentication"></a><span data-ttu-id="89bfd-114">쿠키 인증</span><span class="sxs-lookup"><span data-stu-id="89bfd-114">Cookie authentication</span></span>

<span data-ttu-id="89bfd-115">브라우저 기반의 앱에서 쿠키 인증을 사용할 경우 기존의 사용자 자격 증명을 자동으로 SignalR 연결로 전달할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-115">In a browser-based app, cookie authentication allows your existing user credentials to automatically flow to SignalR connections.</span></span> <span data-ttu-id="89bfd-116">브라우저 클라이언트를 사용하면 추가적인 구성이 필요 없습니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-116">When using the browser client, no additional configuration is needed.</span></span> <span data-ttu-id="89bfd-117">사용자가 앱에 로그인하면 자동으로 SignalR 연결이 해당 인증을 상속받습니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-117">If the user is logged in to your app, the SignalR connection automatically inherits this authentication.</span></span>

<span data-ttu-id="89bfd-118">쿠키는 액세스 토큰을 전송하는 브라우저 고유의 방식이지만 비 브라우저 클라이언트에서도 쿠키를 전송할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-118">Cookies are a browser-specific way to send access tokens, but non-browser clients can send them.</span></span> <span data-ttu-id="89bfd-119">[.NET 클라이언트](xref:signalr/dotnet-client)를 사용할 경우 `.WithUrl` 호출에서 `Cookies` 속성을 구성하여 쿠키를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-119">When using the [.NET Client](xref:signalr/dotnet-client), the `Cookies` property can be configured in the `.WithUrl` call in order to provide a cookie.</span></span> <span data-ttu-id="89bfd-120">그러나 .NET 클라이언트에서 쿠키 인증을 사용하려면 앱이 쿠키에 대한 인증 데이터를 교환하는 API를 제공해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-120">However, using cookie authentication from the .NET Client requires the app to provide an API to exchange authentication data for a cookie.</span></span>

### <a name="bearer-token-authentication"></a><span data-ttu-id="89bfd-121">전달자 토큰 인증</span><span class="sxs-lookup"><span data-stu-id="89bfd-121">Bearer token authentication</span></span>

<span data-ttu-id="89bfd-122">클라이언트는 쿠키를 사용하는 대신 액세스 토큰을 제공할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-122">The client can provide an access token instead of using a cookie.</span></span> <span data-ttu-id="89bfd-123">서버는 토큰의 유효성을 검사하고 이를 이용해서 사용자를 식별합니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-123">The server validates the token and uses it to identify the user.</span></span> <span data-ttu-id="89bfd-124">이 유효성 검사는 연결이 설정될 때만 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-124">This validation is done only when the connection is established.</span></span> <span data-ttu-id="89bfd-125">서버는 연결이 유지되는 동안 토큰이 해지되었는지를 확인하기 위해 자동으로 유효성을 재검사하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-125">During the life of the connection, the server doesn't automatically revalidate to check for token revocation.</span></span>

<span data-ttu-id="89bfd-126">서버에서 전달자 토큰 인증은 [JWT 전달자 미들웨어](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer)를 사용하여 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-126">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="89bfd-127">JavaScript 클라이언트에서는 [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) 옵션을 통해서 토큰을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-127">In the JavaScript client, the token can be provided using the [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) option.</span></span>

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=63-65)]

<span data-ttu-id="89bfd-128">.NET 클라이언트에는 토큰을 구성하기 위해 사용할 수 있는 비슷한 [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) 속성이 존재합니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-128">In the .NET client, there is a similar [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) property that can be used to configure the token:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="89bfd-129">지정한 액세스 토큰 함수는 SignalR이 만들어내는 **모든** HTTP 요청 전에 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-129">The access token function you provide is called before **every** HTTP request made by SignalR.</span></span> <span data-ttu-id="89bfd-130">연결을 활성 상태로 유지하기 위해 토큰을 갱신해야 할 경우 (연결 중 만료될 수 있으므로), 이 함수 내에서 작업을 수행하고 갱신된 토큰을 반환해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-130">If you need to renew the token in order to keep the connection active (because it may expire during the connection), do so from within this function and return the updated token.</span></span>

<span data-ttu-id="89bfd-131">표준 웹 API에서 전달자 토큰은 HTTP 헤더로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-131">In standard web APIs, bearer tokens are sent in an HTTP header.</span></span> <span data-ttu-id="89bfd-132">그러나 일부 전송을 사용하는 경우에는 SignalR이 브라우저에서 이런 헤더를 설정할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-132">However, SignalR is unable to set these headers in browsers when using some transports.</span></span> <span data-ttu-id="89bfd-133">WebSockets 및 서버-전송 이벤트를 사용할 경우 토큰은 쿼리 문자열 매개 변수로 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-133">When using WebSockets and Server-Sent Events, the token is transmitted as a query string parameter.</span></span> <span data-ttu-id="89bfd-134">서버에서 이를 지원하려면 추가적인 구성이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-134">In order to support this on the server, additional configuration is required:</span></span>

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

### <a name="cookies-vs-bearer-tokens"></a><span data-ttu-id="89bfd-135">쿠키 대 전달자 토큰</span><span class="sxs-lookup"><span data-stu-id="89bfd-135">Cookies vs. bearer tokens</span></span> 

<span data-ttu-id="89bfd-136">쿠키는 브라우저에 한정되기 때문에 다른 유형의 클라이언트에서 쿠키를 전송하는 작업은 전달자 토큰 전송에 비해 복잡성이 증가합니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-136">Because cookies are specific to browsers, sending them from other kinds of clients adds complexity compared to sending bearer tokens.</span></span> <span data-ttu-id="89bfd-137">따라서 앱이 브라우저 클라이언트에서만 사용자 인증이 필요한 것이 아니라면 쿠키 인증은 권장되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-137">For this reason, cookie authentication isn't recommended unless the app only needs to authenticate users from the browser client.</span></span> <span data-ttu-id="89bfd-138">브라우저 클라이언트 이외의 클라이언트도 사용할 경우 전달자 토큰 인증이 권장되는 방식입니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-138">Bearer token authentication is the recommended approach when using clients other than the browser client.</span></span>

### <a name="windows-authentication"></a><span data-ttu-id="89bfd-139">Windows 인증</span><span class="sxs-lookup"><span data-stu-id="89bfd-139">Windows authentication</span></span>

<span data-ttu-id="89bfd-140">앱에 [Windows 인증](xref:security/authentication/windowsauth)이 구성되어 있을 경우 SignalR은 해당 신원을 이용해서 허브를 보호할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-140">If [Windows authentication](xref:security/authentication/windowsauth) is configured in your app, SignalR can use that identity to secure hubs.</span></span> <span data-ttu-id="89bfd-141">그러나 개별 사용자에게 메시지를 전송하려면 사용자 지정 사용자 ID 공급자를 추가해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-141">However, in order to send messages to individual users, you need to add a custom User ID provider.</span></span> <span data-ttu-id="89bfd-142">그 이유는 Windows 인증 시스템이 SignalR이 사용자 이름을 결정하는 데 사용하는 "이름 식별자" 클레임을 제공하지 않기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-142">This is because the Windows authentication system doesn't provide the "Name Identifier" claim that SignalR uses to determine the user name.</span></span>

<span data-ttu-id="89bfd-143">`IUserIdProvider`를 구현하는 새 클래스를 추가하고 사용자로부터 식별자로 사용할 클레임 중 하나를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-143">Add a new class that implements `IUserIdProvider` and retrieve one of the claims from the user to use as the identifier.</span></span> <span data-ttu-id="89bfd-144">예를 들어, "Name" 클레임(`[Domain]\[Username]` 형태의 Windows 사용자 이름)을 사용하려면 다음과 같은 클래스를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-144">For example, to use the "Name" claim (which is the Windows username in the form `[Domain]\[Username]`), create the following class:</span></span>

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

<span data-ttu-id="89bfd-145">`ClaimTypes.Name` 대신 `User`의 다른 모든 값(예: Windows SID 식별자 등)을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-145">Rather than `ClaimTypes.Name`, you can use any value from the `User` (such as the Windows SID identifier, etc.).</span></span>

> [!NOTE]
> <span data-ttu-id="89bfd-146">선택한 값은 시스템의 모든 사용자 간에 고유해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-146">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="89bfd-147">그렇지 않으면 특정 사용자를 대상으로 한 메시지가 다른 사용자에게 전달될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-147">Otherwise, a message intended for one user could end up going to a different user.</span></span>

<span data-ttu-id="89bfd-148">이 구성 요소를 등록 하면 `Startup.ConfigureServices` 메서드.</span><span class="sxs-lookup"><span data-stu-id="89bfd-148">Register this component in your `Startup.ConfigureServices` method.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

<span data-ttu-id="89bfd-149">.NET 클라이언트에서는 [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) 속성을 설정해서 Windows 인증을 활성화시켜야 합니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-149">In the .NET Client, Windows Authentication must be enabled by setting the [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) property:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

<span data-ttu-id="89bfd-150">Windows 인증은 Microsoft Internet Explorer 또는 Microsoft Edge를 사용하는 경우에만 브라우저 클라이언트에서 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-150">Windows Authentication is only supported by the browser client when using Microsoft Internet Explorer or Microsoft Edge.</span></span>

### <a name="use-claims-to-customize-identity-handling"></a><span data-ttu-id="89bfd-151">사용 하 여 클레임 id 처리를 사용자 지정 하려면</span><span class="sxs-lookup"><span data-stu-id="89bfd-151">Use claims to customize identity handling</span></span>

<span data-ttu-id="89bfd-152">앱 사용자를 인증 하는 사용자 클레임에서 SignalR 사용자 Id를 파생할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-152">An app that authenticates users can derive SignalR user IDs from user claims.</span></span> <span data-ttu-id="89bfd-153">SignalR 사용자 Id를 생성 하는 방법을 사용 하지 않으려면 구현 `IUserIdProvider` 구현을 등록 합니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-153">To specify how SignalR creates user IDs, implement `IUserIdProvider` and register the implementation.</span></span>

<span data-ttu-id="89bfd-154">샘플 코드를 사용 하는 클레임을 사용 하는 식별 속성으로 사용자의 전자 메일 주소를 선택 하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-154">The sample code demonstrates how you would use claims to select the user's email address as the identifying property.</span></span> 

> [!NOTE]
> <span data-ttu-id="89bfd-155">선택한 값은 시스템의 모든 사용자 간에 고유해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-155">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="89bfd-156">그렇지 않으면 특정 사용자를 대상으로 한 메시지가 다른 사용자에게 전달될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-156">Otherwise, a message intended for one user could end up going to a different user.</span></span>

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

<span data-ttu-id="89bfd-157">형식 사용 하 여 클레임을 추가 하는 계정 등록 `ClaimsTypes.Email` ASP.NET identity 데이터베이스에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-157">The account registration adds a claim with type `ClaimsTypes.Email` to the ASP.NET identity database.</span></span>

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

<span data-ttu-id="89bfd-158">이 구성 요소를 등록 하면 `Startup.ConfigureServices`합니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-158">Register this component in your `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a><span data-ttu-id="89bfd-159">허브 및 허브 메서드 접근에 대한 사용자 권한 부여</span><span class="sxs-lookup"><span data-stu-id="89bfd-159">Authorize users to access hubs and hub methods</span></span>

<span data-ttu-id="89bfd-160">기본적으로, 인증되지 않은 사용자도 허브의 모든 메서드를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-160">By default, all methods in a hub can be called by an unauthenticated user.</span></span> <span data-ttu-id="89bfd-161">인증을 요구하려면 허브에 [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) 특성을 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-161">In order to require authentication, apply the [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) attribute to the hub:</span></span>

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

<span data-ttu-id="89bfd-162">`[Authorize]` 특성의 생성자 인수 및 속성을 이용해서 특정 [권한 부여 정책](xref:security/authorization/policies)을 만족하는 사용자만 접근할 수 있도록 제한할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-162">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="89bfd-163">예를 들어 `MyAuthorizationPolicy`라는 사용자 지정 권한 부여 정책이 존재할 경우, 다음과 같은 코드를 사용해서 해당 정책을 만족하는 사용자만 허브에 접근할 수 있도록 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-163">For example, if you have a custom authorization policy called `MyAuthorizationPolicy` you can ensure that only users matching that policy can access the hub using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub: Hub
{
}
```

<span data-ttu-id="89bfd-164">각각의 허브 메서드에도 `[Authorize]` 특성을 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-164">Individual hub methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="89bfd-165">현재 사용자가 메서드에 적용된 정책을 만족하지 않으면 호출자에게 오류가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="89bfd-165">If the current user doesn't match the policy applied to the method, an error is returned to the caller:</span></span>

```csharp
[Authorize]
public class ChatHub: Hub
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

## <a name="additional-resources"></a><span data-ttu-id="89bfd-166">추가 자료</span><span class="sxs-lookup"><span data-stu-id="89bfd-166">Additional resources</span></span>

* [<span data-ttu-id="89bfd-167">ASP.NET Core에서 전달자 토큰 인증</span><span class="sxs-lookup"><span data-stu-id="89bfd-167">Bearer Token Authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
