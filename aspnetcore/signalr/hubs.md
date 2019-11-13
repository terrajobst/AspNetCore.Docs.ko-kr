---
title: ASP.NET Core SignalR에서 허브 사용
author: bradygaster
description: ASP.NET Core SignalR에서 허브를 사용 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/hubs
ms.openlocfilehash: f95766cab84bddff2c7c62f30bce1e6d1e43deab
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963797"
---
# <a name="use-hubs-in-opno-locsignalr-for-aspnet-core"></a><span data-ttu-id="de6df-103">ASP.NET Core SignalR에서 허브 사용</span><span class="sxs-lookup"><span data-stu-id="de6df-103">Use hubs in SignalR for ASP.NET Core</span></span>

<span data-ttu-id="de6df-104">만든 사람 [Rachel Appel](https://twitter.com/rachelappel) 및 [Kevin Griffin](https://twitter.com/1kevgriff)</span><span class="sxs-lookup"><span data-stu-id="de6df-104">By [Rachel Appel](https://twitter.com/rachelappel) and [Kevin Griffin](https://twitter.com/1kevgriff)</span></span>

<span data-ttu-id="de6df-105">[샘플 코드 보기 또는 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(다운로드 방법)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="de6df-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="what-is-a-opno-locsignalr-hub"></a><span data-ttu-id="de6df-106">SignalR 허브 란?</span><span class="sxs-lookup"><span data-stu-id="de6df-106">What is a SignalR hub</span></span>

<span data-ttu-id="de6df-107">SignalR Hubs API를 사용 하면 서버에서 연결 된 클라이언트의 메서드를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-107">The SignalR Hubs API enables you to call methods on connected clients from the server.</span></span> <span data-ttu-id="de6df-108">서버 코드에서 클라이언트에 의해 호출 되는 메서드를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-108">In the server code, you define methods that are called by client.</span></span> <span data-ttu-id="de6df-109">클라이언트 코드에서는 서버에서 호출 되는 메서드를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-109">In the client code, you define methods that are called from the server.</span></span> SignalR<span data-ttu-id="de6df-110">은 실시간 클라이언트-서버 및 서버 간 통신을 가능 하 게 하는 모든 작업을 내부적으로 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-110"> takes care of everything behind the scenes that makes real-time client-to-server and server-to-client communications possible.</span></span>

## <a name="configure-opno-locsignalr-hubs"></a><span data-ttu-id="de6df-111">SignalR 허브 구성</span><span class="sxs-lookup"><span data-stu-id="de6df-111">Configure SignalR hubs</span></span>

<span data-ttu-id="de6df-112">SignalR 미들웨어에는 `services.AddSignalR`를 호출 하 여 구성 되는 일부 서비스가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-112">The SignalR middleware requires some services, which are configured by calling `services.AddSignalR`.</span></span>

[!code-csharp[Configure service](hubs/sample/startup.cs?range=38)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="de6df-113">ASP.NET Core 앱에 SignalR 기능을 추가 하는 경우 `Startup.Configure` 메서드의 `app.UseEndpoints` 콜백에서 `endpoint.MapHub`를 호출 하 여 SignalR 경로를 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-113">When adding SignalR functionality to an ASP.NET Core app, setup SignalR routes by calling `endpoint.MapHub` in the `Startup.Configure` method's `app.UseEndpoints` callback.</span></span>

```csharp
app.UseRouting();
app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/chathub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="de6df-114">ASP.NET Core 앱에 SignalR 기능을 추가 하는 경우 설치 프로그램은 `Startup.Configure` 메서드에서 `app.UseSignalR`를 호출 하 여 경로를 SignalR 합니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-114">When adding SignalR functionality to an ASP.NET Core app, setup SignalR routes by calling `app.UseSignalR` in the `Startup.Configure` method.</span></span>

[!code-csharp[Configure routes to hubs](hubs/sample/startup.cs?range=57-60)]

::: moniker-end

## <a name="create-and-use-hubs"></a><span data-ttu-id="de6df-115">허브 만들기 및 사용</span><span class="sxs-lookup"><span data-stu-id="de6df-115">Create and use hubs</span></span>

<span data-ttu-id="de6df-116">`Hub`에서 상속 되는 클래스를 선언 하 고 여기에 공용 메서드를 추가 하 여 허브를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-116">Create a hub by declaring a class that inherits from `Hub`, and add public methods to it.</span></span> <span data-ttu-id="de6df-117">클라이언트는 `public`으로 정의 된 메서드를 호출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-117">Clients can call methods that are defined as `public`.</span></span>

```csharp
public class ChatHub : Hub
{
    public Task SendMessage(string user, string message)
    {
        return Clients.All.SendAsync("ReceiveMessage", user, message);
    }
}
```

<span data-ttu-id="de6df-118">모든 C# 메서드에서와 같이 복합 형식 및 배열을 포함 하 여 반환 형식 및 매개 변수를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-118">You can specify a return type and parameters, including complex types and arrays, as you would in any C# method.</span></span> SignalR<span data-ttu-id="de6df-119">은 매개 변수와 반환 값의 복합 개체와 배열의 serialization 및 deserialization을 처리 합니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-119"> handles the serialization and deserialization of complex objects and arrays in your parameters and return values.</span></span>

> [!NOTE]
> <span data-ttu-id="de6df-120">허브는 일시적입니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-120">Hubs are transient:</span></span>
>
> * <span data-ttu-id="de6df-121">허브 클래스의 속성에 상태를 저장 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="de6df-121">Don't store state in a property on the hub class.</span></span> <span data-ttu-id="de6df-122">모든 허브 메서드 호출은 새 허브 인스턴스에서 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-122">Every hub method call is executed on a new hub instance.</span></span>
> * <span data-ttu-id="de6df-123">허브에 종속 된 비동기 메서드를 호출할 때 활성 상태로 유지 되는 `await`을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-123">Use `await` when calling asynchronous methods that depend on the hub staying alive.</span></span> <span data-ttu-id="de6df-124">예를 들어 `Clients.All.SendAsync(...)`와 같은 메서드를 `await` 없이 호출 하 고 `SendAsync` 완료 되기 전에 허브 메서드를 완료 하면 실패할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-124">For example, a method such as `Clients.All.SendAsync(...)` can fail if it's called without `await` and the hub method completes before `SendAsync` finishes.</span></span>

## <a name="the-context-object"></a><span data-ttu-id="de6df-125">Context 개체입니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-125">The Context object</span></span>

<span data-ttu-id="de6df-126">`Hub` 클래스에는 연결에 대 한 정보를 포함 하는 다음과 같은 속성을 포함 하는 `Context` 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-126">The `Hub` class has a `Context` property that contains the following properties with information about the connection:</span></span>

| <span data-ttu-id="de6df-127">속성</span><span class="sxs-lookup"><span data-stu-id="de6df-127">Property</span></span> | <span data-ttu-id="de6df-128">설명</span><span class="sxs-lookup"><span data-stu-id="de6df-128">Description</span></span> |
| ------ | ----------- |
| `ConnectionId` | <span data-ttu-id="de6df-129">SignalR에서 할당 한 연결의 고유 ID를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-129">Gets the unique ID for the connection, assigned by SignalR.</span></span> <span data-ttu-id="de6df-130">각 연결에 대해 하나의 연결 ID가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-130">There is one connection ID for each connection.</span></span>|
| `UserIdentifier` | <span data-ttu-id="de6df-131">[사용자 식별자](xref:signalr/groups)를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-131">Gets the [user identifier](xref:signalr/groups).</span></span> <span data-ttu-id="de6df-132">기본적으로 SignalR는 연결과 관련 된 `ClaimsPrincipal`의 `ClaimTypes.NameIdentifier`를 사용자 식별자로 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-132">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> |
| `User` | <span data-ttu-id="de6df-133">현재 사용자와 연결 된 `ClaimsPrincipal`를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-133">Gets the `ClaimsPrincipal` associated with the current user.</span></span> |
| `Items` | <span data-ttu-id="de6df-134">이 연결 범위 내에서 데이터를 공유 하는 데 사용할 수 있는 키/값 컬렉션을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-134">Gets a key/value collection that can be used to share data within the scope of this connection.</span></span> <span data-ttu-id="de6df-135">데이터는이 컬렉션에 저장 될 수 있으며 다른 허브 메서드 호출을 통해 연결에 대해 유지 됩니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-135">Data can be stored in this collection and it will persist for the connection across different hub method invocations.</span></span> |
| `Features` | <span data-ttu-id="de6df-136">연결에서 사용할 수 있는 기능 컬렉션을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-136">Gets the collection of features available on the connection.</span></span> <span data-ttu-id="de6df-137">지금은이 컬렉션이 대부분의 시나리오에서 필요 하지 않으므로 자세히 설명 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-137">For now, this collection isn't needed in most scenarios, so it isn't documented in detail yet.</span></span> |
| `ConnectionAborted` | <span data-ttu-id="de6df-138">연결이 중단 될 때 알림을 표시 하는 `CancellationToken`을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-138">Gets a `CancellationToken` that notifies when the connection is aborted.</span></span> |

<span data-ttu-id="de6df-139">또한 `Hub.Context`에는 다음과 같은 메서드가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-139">`Hub.Context` also contains the following methods:</span></span>

| <span data-ttu-id="de6df-140">메서드</span><span class="sxs-lookup"><span data-stu-id="de6df-140">Method</span></span> | <span data-ttu-id="de6df-141">설명</span><span class="sxs-lookup"><span data-stu-id="de6df-141">Description</span></span> |
| ------ | ----------- |
| `GetHttpContext` | <span data-ttu-id="de6df-142">연결에 대 한 `HttpContext`를 반환 하거나, 연결이 HTTP 요청과 연결 되지 않은 경우 `null`을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-142">Returns the `HttpContext` for the connection, or `null` if the connection is not associated with an HTTP request.</span></span> <span data-ttu-id="de6df-143">HTTP 연결의 경우이 메서드를 사용 하 여 HTTP 헤더 및 쿼리 문자열과 같은 정보를 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-143">For HTTP connections, you can use this method to get information such as HTTP headers and query strings.</span></span> |
| `Abort` | <span data-ttu-id="de6df-144">연결을 중단 합니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-144">Aborts the connection.</span></span> |

## <a name="the-clients-object"></a><span data-ttu-id="de6df-145">Clients 개체</span><span class="sxs-lookup"><span data-stu-id="de6df-145">The Clients object</span></span>

<span data-ttu-id="de6df-146">`Hub` 클래스에는 서버와 클라이언트 간의 통신에 대 한 다음 속성을 포함 하는 `Clients` 속성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-146">The `Hub` class has a `Clients` property that contains the following properties for communication between server and client:</span></span>

| <span data-ttu-id="de6df-147">속성</span><span class="sxs-lookup"><span data-stu-id="de6df-147">Property</span></span> | <span data-ttu-id="de6df-148">설명</span><span class="sxs-lookup"><span data-stu-id="de6df-148">Description</span></span> |
| ------ | ----------- |
| `All` | <span data-ttu-id="de6df-149">연결 된 모든 클라이언트에서 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-149">Calls a method on all connected clients</span></span> |
| `Caller` | <span data-ttu-id="de6df-150">허브 메서드를 호출한 클라이언트에서 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-150">Calls a method on the client that invoked the hub method</span></span> |
| `Others` | <span data-ttu-id="de6df-151">메서드를 호출한 클라이언트를 제외한 모든 연결 된 클라이언트에서 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-151">Calls a method on all connected clients except the client that invoked the method</span></span> |

<span data-ttu-id="de6df-152">또한 `Hub.Clients`에는 다음과 같은 메서드가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-152">`Hub.Clients` also contains the following methods:</span></span>

| <span data-ttu-id="de6df-153">메서드</span><span class="sxs-lookup"><span data-stu-id="de6df-153">Method</span></span> | <span data-ttu-id="de6df-154">설명</span><span class="sxs-lookup"><span data-stu-id="de6df-154">Description</span></span> |
| ------ | ----------- |
| `AllExcept` | <span data-ttu-id="de6df-155">지정 된 연결을 제외한 모든 연결 된 클라이언트에서 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-155">Calls a method on all connected clients except for the specified connections</span></span> |
| `Client` | <span data-ttu-id="de6df-156">연결 된 특정 클라이언트에서 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-156">Calls a method on a specific connected client</span></span> |
| `Clients` | <span data-ttu-id="de6df-157">연결 된 특정 클라이언트에서 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-157">Calls a method on specific connected clients</span></span> |
| `Group` | <span data-ttu-id="de6df-158">지정 된 그룹의 모든 연결에 대해 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-158">Calls a method on all connections in the specified group</span></span>  |
| `GroupExcept` | <span data-ttu-id="de6df-159">지정 된 연결을 제외 하 고 지정 된 그룹의 모든 연결에 대해 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-159">Calls a method on all connections in the specified group, except the specified connections</span></span> |
| `Groups` | <span data-ttu-id="de6df-160">여러 연결 그룹에 대해 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-160">Calls a method on multiple groups of connections</span></span>  |
| `OthersInGroup` | <span data-ttu-id="de6df-161">허브 메서드를 호출한 클라이언트를 제외 하 고 연결 그룹에서 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-161">Calls a method on a group of connections, excluding the client that invoked the hub method</span></span>  |
| `User` | <span data-ttu-id="de6df-162">특정 사용자와 연결 된 모든 연결에 대해 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-162">Calls a method on all connections associated with a specific user</span></span> |
| `Users` | <span data-ttu-id="de6df-163">지정 된 사용자와 연결 된 모든 연결에 대해 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-163">Calls a method on all connections associated with the specified users</span></span> |

<span data-ttu-id="de6df-164">위의 표에 있는 각 속성 또는 메서드는 `SendAsync` 메서드를 사용 하 여 개체를 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-164">Each property or method in the preceding tables returns an object with a `SendAsync` method.</span></span> <span data-ttu-id="de6df-165">`SendAsync` 메서드를 사용 하 여 호출할 클라이언트 메서드의 이름 및 매개 변수를 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-165">The `SendAsync` method allows you to supply the name and parameters of the client method to call.</span></span>

## <a name="send-messages-to-clients"></a><span data-ttu-id="de6df-166">클라이언트에 메시지 보내기</span><span class="sxs-lookup"><span data-stu-id="de6df-166">Send messages to clients</span></span>

<span data-ttu-id="de6df-167">특정 클라이언트에 대 한 호출을 수행 하려면 `Clients` 개체의 속성을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-167">To make calls to specific clients, use the properties of the `Clients` object.</span></span> <span data-ttu-id="de6df-168">다음 예제에는 세 가지 허브 메서드가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-168">In the following example, there are three Hub methods:</span></span>

* <span data-ttu-id="de6df-169">`SendMessage` `Clients.All`를 사용 하 여 연결 된 모든 클라이언트에 메시지를 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-169">`SendMessage` sends a message to all connected clients, using `Clients.All`.</span></span>
* <span data-ttu-id="de6df-170">`SendMessageToCaller` `Clients.Caller`를 사용 하 여 호출자에 게 메시지를 다시 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-170">`SendMessageToCaller` sends a message back to the caller, using `Clients.Caller`.</span></span>
* <span data-ttu-id="de6df-171">`SendMessageToGroups`는 `SignalR Users` 그룹의 모든 클라이언트에 메시지를 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-171">`SendMessageToGroups` sends a message to all clients in the `SignalR Users` group.</span></span>

[!code-csharp[Send messages](hubs/sample/hubs/chathub.cs?name=HubMethods)]

## <a name="strongly-typed-hubs"></a><span data-ttu-id="de6df-172">강력한 형식의 허브</span><span class="sxs-lookup"><span data-stu-id="de6df-172">Strongly typed hubs</span></span>

<span data-ttu-id="de6df-173">`SendAsync` 사용의 단점은 호출 될 클라이언트 메서드를 지정 하기 위해 매직 문자열을 사용 한다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-173">A drawback of using `SendAsync` is that it relies on a magic string to specify the client method to be called.</span></span> <span data-ttu-id="de6df-174">그러면 메서드 이름의 철자가 틀렸거나 클라이언트에서 누락 된 경우 코드를 런타임 오류에 열어 둡니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-174">This leaves code open to runtime errors if the method name is misspelled or missing from the client.</span></span>

<span data-ttu-id="de6df-175">`SendAsync`를 사용 하는 대신 <xref:Microsoft.AspNetCore.SignalR.Hub%601>를 사용 하 여 `Hub`를 강력 하 게 입력할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-175">An alternative to using `SendAsync` is to strongly type the `Hub` with <xref:Microsoft.AspNetCore.SignalR.Hub%601>.</span></span> <span data-ttu-id="de6df-176">다음 예제에서는 `ChatHub` 클라이언트 메서드가 `IChatClient`라는 인터페이스로 추출 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-176">In the following example, the `ChatHub` client methods have been extracted out into an interface called `IChatClient`.</span></span>

[!code-csharp[Interface for IChatClient](hubs/sample/hubs/ichatclient.cs?name=snippet_IChatClient)]

<span data-ttu-id="de6df-177">이 인터페이스를 사용 하 여 이전 `ChatHub` 예제를 리팩터링할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-177">This interface can be used to refactor the preceding `ChatHub` example.</span></span>

[!code-csharp[Strongly typed ChatHub](hubs/sample/hubs/StronglyTypedChatHub.cs?range=8-18,36)]

<span data-ttu-id="de6df-178">`Hub<IChatClient>`를 사용 하면 클라이언트 메서드를 컴파일할 때 검사할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-178">Using `Hub<IChatClient>` enables compile-time checking of the client methods.</span></span> <span data-ttu-id="de6df-179">이는 `Hub<T>` 인터페이스에 정의 된 메서드에만 액세스할 수 있으므로 매직 문자열을 사용 하 여 발생 하는 문제를 방지 합니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-179">This prevents issues caused by using magic strings, since `Hub<T>` can only provide access to the methods defined in the interface.</span></span>

<span data-ttu-id="de6df-180">강력한 형식의 `Hub<T>`를 사용 하면 `SendAsync`를 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-180">Using a strongly typed `Hub<T>` disables the ability to use `SendAsync`.</span></span> <span data-ttu-id="de6df-181">인터페이스에 정의 된 메서드는 여전히 비동기로 정의 될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-181">Any methods defined on the interface can still be defined as asynchronous.</span></span> <span data-ttu-id="de6df-182">실제로 이러한 각 메서드는 `Task`을 반환 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-182">In fact, each of these methods should return a `Task`.</span></span> <span data-ttu-id="de6df-183">인터페이스 이므로 `async` 키워드를 사용 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="de6df-183">Since it's an interface, don't use the `async` keyword.</span></span> <span data-ttu-id="de6df-184">예를 들어 다음과 같은 가치를 제공해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-184">For example:</span></span>

```csharp
public interface IClient
{
    Task ClientMethod();
}
```

> [!NOTE]
> <span data-ttu-id="de6df-185">`Async` 접미사는 메서드 이름에서 제거 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-185">The `Async` suffix isn't stripped from the method name.</span></span> <span data-ttu-id="de6df-186">클라이언트 메서드가 `.on('MyMethodAsync')`를 사용 하 여 정의 되지 않은 경우에는 `MyMethodAsync`를 이름으로 사용 하지 않아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-186">Unless your client method is defined with `.on('MyMethodAsync')`, you shouldn't use `MyMethodAsync` as a name.</span></span>

## <a name="change-the-name-of-a-hub-method"></a><span data-ttu-id="de6df-187">허브 메서드 이름 변경</span><span class="sxs-lookup"><span data-stu-id="de6df-187">Change the name of a hub method</span></span>

<span data-ttu-id="de6df-188">기본적으로 서버 허브 메서드 이름은 .NET 메서드의 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-188">By default, a server hub method name is the name of the .NET method.</span></span> <span data-ttu-id="de6df-189">그러나 [HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) 특성을 사용 하 여이 기본값을 변경 하 고 메서드의 이름을 수동으로 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-189">However, you can use the [HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) attribute to change this default and manually specify a name for the method.</span></span> <span data-ttu-id="de6df-190">클라이언트는 메서드를 호출할 때 .NET 메서드 이름 대신이 이름을 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-190">The client should use this name, instead of the .NET method name, when invoking the method.</span></span>

[!code-csharp[HubMethodName attribute](hubs/sample/hubs/chathub.cs?name=HubMethodName&highlight=1)]

## <a name="handle-events-for-a-connection"></a><span data-ttu-id="de6df-191">연결에 대 한 이벤트 처리</span><span class="sxs-lookup"><span data-stu-id="de6df-191">Handle events for a connection</span></span>

<span data-ttu-id="de6df-192">SignalR Hubs API는 `OnConnectedAsync` 및 `OnDisconnectedAsync` 가상 메서드를 제공 하 여 연결을 관리 하 고 추적 합니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-192">The SignalR Hubs API provides the `OnConnectedAsync` and `OnDisconnectedAsync` virtual methods to manage and track connections.</span></span> <span data-ttu-id="de6df-193">클라이언트가 허브에 연결 하는 경우 (예: 그룹에 추가 하는 경우) 작업을 수행 하도록 `OnConnectedAsync` 가상 메서드를 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-193">Override the `OnConnectedAsync` virtual method to perform actions when a client connects to the Hub, such as adding it to a group.</span></span>

[!code-csharp[Handle connection](hubs/sample/hubs/chathub.cs?name=OnConnectedAsync)]

<span data-ttu-id="de6df-194">클라이언트의 연결이 끊어질 때 작업을 수행 하도록 `OnDisconnectedAsync` 가상 메서드를 재정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-194">Override the `OnDisconnectedAsync` virtual method to perform actions when a client disconnects.</span></span> <span data-ttu-id="de6df-195">예를 들어 `connection.stop()`를 호출 하 여 클라이언트가 의도적으로 연결을 끊으면 `exception` 매개 변수가 `null`됩니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-195">If the client disconnects intentionally (by calling `connection.stop()`, for example), the `exception` parameter will be `null`.</span></span> <span data-ttu-id="de6df-196">그러나 오류 (예: 네트워크 오류)로 인해 클라이언트의 연결이 끊어지면 `exception` 매개 변수는 오류를 설명 하는 예외를 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-196">However, if the client is disconnected due to an error (such as a network failure), the `exception` parameter will contain an exception describing the failure.</span></span>

[!code-csharp[Handle disconnection](hubs/sample/hubs/chathub.cs?name=OnDisconnectedAsync)]

## <a name="handle-errors"></a><span data-ttu-id="de6df-197">오류 처리</span><span class="sxs-lookup"><span data-stu-id="de6df-197">Handle errors</span></span>

<span data-ttu-id="de6df-198">허브 메서드에서 throw 된 예외는 메서드를 호출한 클라이언트로 전송 됩니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-198">Exceptions thrown in your hub methods are sent to the client that invoked the method.</span></span> <span data-ttu-id="de6df-199">JavaScript 클라이언트에서 `invoke` 메서드는 [Javascript 약속](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises)을 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-199">On the JavaScript client, the `invoke` method returns a [JavaScript Promise](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises).</span></span> <span data-ttu-id="de6df-200">클라이언트는 `catch`을 사용 하 여 약속에 연결 된 처리기와 함께 오류를 수신 하면이를 호출 하 고 JavaScript `Error` 개체로 전달 합니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-200">When the client receives an error with a handler attached to the promise using `catch`, it's invoked and passed as a JavaScript `Error` object.</span></span>

[!code-javascript[Error](hubs/sample/wwwroot/js/chat.js?range=23)]

<span data-ttu-id="de6df-201">허브가 예외를 throw 하는 경우 연결이 닫혀 있지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-201">If your Hub throws an exception, connections aren't closed.</span></span> <span data-ttu-id="de6df-202">기본적으로 SignalR는 일반 오류 메시지를 클라이언트에 반환 합니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-202">By default, SignalR returns a generic error message to the client.</span></span> <span data-ttu-id="de6df-203">예를 들어 다음과 같은 가치를 제공해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-203">For example:</span></span>

```
Microsoft.AspNetCore.SignalR.HubException: An unexpected error occurred invoking 'MethodName' on the server.
```

<span data-ttu-id="de6df-204">예기치 않은 예외에는 데이터베이스 연결에 실패 한 경우 트리거된 예외의 데이터베이스 서버 이름과 같은 중요 한 정보가 포함 되어 있는 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-204">Unexpected exceptions often contain sensitive information, such as the name of a database server in an exception triggered when the database connection fails.</span></span> SignalR<span data-ttu-id="de6df-205">는 기본적으로 이러한 자세한 오류 메시지를 보안 조치로 노출 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-205"> doesn't expose these detailed error messages by default as a security measure.</span></span> <span data-ttu-id="de6df-206">예외 정보를 표시 하지 않는 이유에 대 한 자세한 내용은 [보안 고려 사항 문서](xref:signalr/security#exceptions) 를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="de6df-206">See the [Security considerations article](xref:signalr/security#exceptions) for more information on why exception details are suppressed.</span></span>

<span data-ttu-id="de6df-207">클라이언트에 *전파 하려는 예외* 조건이 있는 경우 `HubException` 클래스를 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-207">If you have an exceptional condition you *do* want to propagate to the client, you can use the `HubException` class.</span></span> <span data-ttu-id="de6df-208">허브 메서드에서 `HubException`를 throw 하는 경우 SignalR는 수정 **되지** 않은 상태로 전체 메시지를 클라이언트에 전송 합니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-208">If you throw a `HubException` from your hub method, SignalR **will** send the entire message to the client, unmodified.</span></span>

[!code-csharp[ThrowHubException](hubs/sample/hubs/chathub.cs?name=ThrowHubException&highlight=3)]

> [!NOTE]
> SignalR<span data-ttu-id="de6df-209">는 예외의 `Message` 속성만 클라이언트에 게 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-209"> only sends the `Message` property of the exception to the client.</span></span> <span data-ttu-id="de6df-210">예외에 대 한 스택 추적 및 기타 속성은 클라이언트에서 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="de6df-210">The stack trace and other properties on the exception aren't available to the client.</span></span>

## <a name="related-resources"></a><span data-ttu-id="de6df-211">관련 참고 자료</span><span class="sxs-lookup"><span data-stu-id="de6df-211">Related resources</span></span>

* <span data-ttu-id="de6df-212">[ASP.NET Core SignalR 소개](xref:signalr/introduction)</span><span class="sxs-lookup"><span data-stu-id="de6df-212">[Intro to ASP.NET Core SignalR](xref:signalr/introduction)</span></span>
* [<span data-ttu-id="de6df-213">JavaScript 클라이언트</span><span class="sxs-lookup"><span data-stu-id="de6df-213">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="de6df-214">Azure에 게시</span><span class="sxs-lookup"><span data-stu-id="de6df-214">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
