---
title: ASP.NET Core SignalR Java 클라이언트
author: mikaelm12
description: ASP.NET Core SignalR Java 클라이언트를 사용 하는 방법에 알아봅니다.
monikerRange: '>= aspnetcore-2.2'
ms.author: mimengis
ms.custom: mvc
ms.date: 06/27/2019
uid: signalr/java-client
ms.openlocfilehash: eea1dfb7d8afcd34c0dacd8315ad196d7235c9f7
ms.sourcegitcommit: 6d9cf728465cdb0de1037633a8b7df9a8989cccb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67463265"
---
# <a name="aspnet-core-signalr-java-client"></a><span data-ttu-id="6a01a-103">ASP.NET Core SignalR Java 클라이언트</span><span class="sxs-lookup"><span data-stu-id="6a01a-103">ASP.NET Core SignalR Java client</span></span>

<span data-ttu-id="6a01a-104">작성자: [Mikael Mengistu](https://twitter.com/MikaelM_12)</span><span class="sxs-lookup"><span data-stu-id="6a01a-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="6a01a-105">Java 클라이언트 Android 앱을 포함 하 여 Java 코드에서 ASP.NET Core SignalR 서버에 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6a01a-105">The Java client enables connecting to an ASP.NET Core SignalR server from Java code, including Android apps.</span></span> <span data-ttu-id="6a01a-106">같은 합니다 [JavaScript 클라이언트](xref:signalr/javascript-client) 하며 [.NET 클라이언트](xref:signalr/dotnet-client), Java 클라이언트를 사용 하면 실시간으로 허브에 메시지를 보내고 받을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6a01a-106">Like the [JavaScript client](xref:signalr/javascript-client) and the [.NET client](xref:signalr/dotnet-client), the Java client enables you to receive and send messages to a hub in real time.</span></span> <span data-ttu-id="6a01a-107">Java 클라이언트는 ASP.NET Core 2.2에서 사용할 수 있는 이상.</span><span class="sxs-lookup"><span data-stu-id="6a01a-107">The Java client is available in ASP.NET Core 2.2 and later.</span></span>

<span data-ttu-id="6a01a-108">이 문서에에서 나와 있는 샘플 Java 콘솔 앱 SignalR Java 클라이언트를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="6a01a-108">The sample Java console app referenced in this article uses the SignalR Java client.</span></span>

<span data-ttu-id="6a01a-109">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6a01a-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-signalr-java-client-package"></a><span data-ttu-id="6a01a-110">SignalR Java 클라이언트 패키지를 설치 합니다.</span><span class="sxs-lookup"><span data-stu-id="6a01a-110">Install the SignalR Java client package</span></span>

<span data-ttu-id="6a01a-111">합니다 *signalr 1.0.0* JAR 파일에는 클라이언트가 SignalR 허브에 연결할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6a01a-111">The *signalr-1.0.0* JAR file allows clients to connect to SignalR hubs.</span></span> <span data-ttu-id="6a01a-112">최신 JAR 파일 버전 번호를 찾으려면 다음을 참조 합니다 [Maven 검색 결과](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr)합니다.</span><span class="sxs-lookup"><span data-stu-id="6a01a-112">To find the latest JAR file version number, see the [Maven search results](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).</span></span>

<span data-ttu-id="6a01a-113">Gradle을 사용 하는 경우에 다음 줄을 추가 합니다 `dependencies` 의 섹션에 *build.gradle* 파일:</span><span class="sxs-lookup"><span data-stu-id="6a01a-113">If using Gradle, add the following line to the `dependencies` section of your *build.gradle* file:</span></span>

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

<span data-ttu-id="6a01a-114">Maven을 사용 하 여 내에 다음 줄을 추가 합니다 `<dependencies>` 의 요소에 *pom.xml* 파일:</span><span class="sxs-lookup"><span data-stu-id="6a01a-114">If using Maven, add the following lines inside the `<dependencies>` element of your *pom.xml* file:</span></span>

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a><span data-ttu-id="6a01a-115">허브에 연결하기</span><span class="sxs-lookup"><span data-stu-id="6a01a-115">Connect to a hub</span></span>

<span data-ttu-id="6a01a-116">설정 하는 `HubConnection`, `HubConnectionBuilder` 사용 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6a01a-116">To establish a `HubConnection`, the `HubConnectionBuilder` should be used.</span></span> <span data-ttu-id="6a01a-117">허브 URL 및 로그 수준에 대 한 연결을 구축 하는 동안 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6a01a-117">The hub URL and log level can be configured while building a connection.</span></span> <span data-ttu-id="6a01a-118">중 하나를 호출 하 여 필요한 모든 옵션을 구성 합니다 `HubConnectionBuilder` 하기 전에 메서드 `build`합니다.</span><span class="sxs-lookup"><span data-stu-id="6a01a-118">Configure any required options by calling any of the `HubConnectionBuilder` methods before `build`.</span></span> <span data-ttu-id="6a01a-119">`start`를 사용하여 연결을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="6a01a-119">Start the connection with `start`.</span></span>

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="6a01a-120">클라이언트에서 허브 메서드 호출하기</span><span class="sxs-lookup"><span data-stu-id="6a01a-120">Call hub methods from client</span></span>

<span data-ttu-id="6a01a-121">에 대 한 호출 `send` 허브 메서드를 호출 합니다.</span><span class="sxs-lookup"><span data-stu-id="6a01a-121">A call to `send` invokes a hub method.</span></span> <span data-ttu-id="6a01a-122">허브 메서드의 이름과 허브 메서드에 정의된 모든 인수를 `send`에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="6a01a-122">Pass the hub method name and any arguments defined in the hub method to `send`.</span></span>

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

> [!NOTE]
> <span data-ttu-id="6a01a-123">Azure SignalR Service를 사용 하는 경우 *서버 리스 모드*, 클라이언트에서 허브 메서드를 호출할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="6a01a-123">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="6a01a-124">자세한 내용은 참조는 [SignalR Service 설명서](/azure/azure-signalr/signalr-concept-serverless-development-config)합니다.</span><span class="sxs-lookup"><span data-stu-id="6a01a-124">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="6a01a-125">허브에서 클라이언트 메서드 호출하기</span><span class="sxs-lookup"><span data-stu-id="6a01a-125">Call client methods from hub</span></span>

<span data-ttu-id="6a01a-126">사용 하 여 `hubConnection.on` 허브를 호출할 수 있는 클라이언트에서 메서드를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="6a01a-126">Use `hubConnection.on` to define methods on the client that the hub can call.</span></span> <span data-ttu-id="6a01a-127">빌드 후 있지만 연결을 시작 하기 전에 메서드를 정의 합니다.</span><span class="sxs-lookup"><span data-stu-id="6a01a-127">Define the methods after building but before starting the connection.</span></span>

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a><span data-ttu-id="6a01a-128">로깅 추가</span><span class="sxs-lookup"><span data-stu-id="6a01a-128">Add logging</span></span>

<span data-ttu-id="6a01a-129">SignalR Java 클라이언트를 사용 하 여 [SLF4J](https://www.slf4j.org/) 로깅에 대 한 라이브러리입니다.</span><span class="sxs-lookup"><span data-stu-id="6a01a-129">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="6a01a-130">라이브러리의 사용자가 자신의 특정 로깅 구현을 특정 로깅 종속성에서 전환 하 여 선택한 수 있는 높은 수준의 로깅 API입니다.</span><span class="sxs-lookup"><span data-stu-id="6a01a-130">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="6a01a-131">다음 코드 조각을 사용 하는 방법을 보여 줍니다 `java.util.logging` SignalR Java 클라이언트를 사용 하 여 합니다.</span><span class="sxs-lookup"><span data-stu-id="6a01a-131">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="6a01a-132">종속성에 대 한 로깅을 구성 하지 않으면, SLF4J 다음 경고 메시지를 사용 하 여 기본 작업 없음로 거를 로드 합니다.</span><span class="sxs-lookup"><span data-stu-id="6a01a-132">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="6a01a-133">이 안전 하 게 무시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6a01a-133">This can safely be ignored.</span></span>

## <a name="android-development-notes"></a><span data-ttu-id="6a01a-134">Android 개발 정보</span><span class="sxs-lookup"><span data-stu-id="6a01a-134">Android development notes</span></span>

<span data-ttu-id="6a01a-135">SignalR 클라이언트 기능에 대 한 호환성 Android SDK와 관련 하 여 대상 Android SDK 버전을 지정 하는 경우 다음 사항을 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="6a01a-135">With regards to Android SDK compatibility for the SignalR client features, consider the following items when specifying your target Android SDK version:</span></span>

* <span data-ttu-id="6a01a-136">SignalR Java 클라이언트에는 Android API 레벨 16 이상 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6a01a-136">The SignalR Java Client will run on Android API Level 16 and later.</span></span>
* <span data-ttu-id="6a01a-137">Android API 레벨 20 이상 Azure SignalR Service를 통해 연결 해야 하기 때문에 합니다 [Azure SignalR Service](/azure/azure-signalr/signalr-overview) TLS 1.2를 차지 하며 s h A-1부터 시작 하는 암호 그룹을 지원 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6a01a-137">Connecting through the Azure SignalR Service will require Android API Level 20 and later because the [Azure SignalR Service](/azure/azure-signalr/signalr-overview) requires TLS 1.2 and doesn't support SHA-1-based cipher suites.</span></span> <span data-ttu-id="6a01a-138">Android [지원에 대 한 SHA-256 이상 나열한 추가](https://developer.android.com/reference/javax/net/ssl/SSLSocket) API 레벨 20에서.</span><span class="sxs-lookup"><span data-stu-id="6a01a-138">Android [added support for SHA-256 (and above) cipher suites](https://developer.android.com/reference/javax/net/ssl/SSLSocket) in API Level 20.</span></span>

## <a name="configure-bearer-token-authentication"></a><span data-ttu-id="6a01a-139">전달자 토큰 인증을 구성</span><span class="sxs-lookup"><span data-stu-id="6a01a-139">Configure bearer token authentication</span></span>

<span data-ttu-id="6a01a-140">SignalR Java 클라이언트에서 전달자 토큰을 "액세스 토큰 팩터리가"를 제공 하 여 인증에 사용 하도록를 구성할 수 있습니다 합니다 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)합니다.</span><span class="sxs-lookup"><span data-stu-id="6a01a-140">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an "access token factory" to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="6a01a-141">사용 하 여 [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) 제공 하는 [RxJava](https://github.com/ReactiveX/RxJava) [Single\<문자열 >](http://reactivex.io/documentation/single.html)합니다.</span><span class="sxs-lookup"><span data-stu-id="6a01a-141">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](http://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="6a01a-142">호출 하 여 [Single.defer](http://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)를 클라이언트에 대 한 액세스 토큰을 생성 하는 논리를 작성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6a01a-142">With a call to [Single.defer](http://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

## <a name="known-limitations"></a><span data-ttu-id="6a01a-143">알려진 제한 사항</span><span class="sxs-lookup"><span data-stu-id="6a01a-143">Known limitations</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="6a01a-144">JSON 프로토콜에만 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6a01a-144">Only the JSON protocol is supported.</span></span>
* <span data-ttu-id="6a01a-145">대체 (fallback) 전송 및 서버 전송 이벤트 전송을 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6a01a-145">Transport fallback and the Server Sent Events transport aren't supported.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="6a01a-146">JSON 프로토콜에만 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6a01a-146">Only the JSON protocol is supported.</span></span>
* <span data-ttu-id="6a01a-147">Websocket 전송만 지원 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6a01a-147">Only the WebSockets transport is supported.</span></span>
* <span data-ttu-id="6a01a-148">스트리밍 아직 지원 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6a01a-148">Streaming isn't supported yet.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="6a01a-149">추가 자료</span><span class="sxs-lookup"><span data-stu-id="6a01a-149">Additional resources</span></span>

* [<span data-ttu-id="6a01a-150">Java API 참조</span><span class="sxs-lookup"><span data-stu-id="6a01a-150">Java API reference</span></span>](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>
* [<span data-ttu-id="6a01a-151">Azure SignalR Service 서버 리스 설명서</span><span class="sxs-lookup"><span data-stu-id="6a01a-151">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
