---
title: ASP.NET Core SignalR Java 클라이언트
author: mikaelm12
description: ASP.NET Core SignalR Java 클라이언트를 사용 하는 방법에 알아봅니다.
monikerRange: '>= aspnetcore-2.2'
ms.author: mimengis
ms.custom: mvc
ms.date: 06/27/2019
uid: signalr/java-client
ms.openlocfilehash: ea0abbaee81222493ff02e1f3bba13ed1e494bf5
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67814977"
---
# <a name="aspnet-core-signalr-java-client"></a>ASP.NET Core SignalR Java 클라이언트

작성자: [Mikael Mengistu](https://twitter.com/MikaelM_12)

Java 클라이언트 Android 앱을 포함 하 여 Java 코드에서 ASP.NET Core SignalR 서버에 연결할 수 있습니다. 같은 합니다 [JavaScript 클라이언트](xref:signalr/javascript-client) 하며 [.NET 클라이언트](xref:signalr/dotnet-client), Java 클라이언트를 사용 하면 실시간으로 허브에 메시지를 보내고 받을 수 있습니다. Java 클라이언트는 ASP.NET Core 2.2에서 사용할 수 있는 이상.

이 문서에에서 나와 있는 샘플 Java 콘솔 앱 SignalR Java 클라이언트를 사용 합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/java-client/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="install-the-signalr-java-client-package"></a>SignalR Java 클라이언트 패키지를 설치 합니다.

합니다 *signalr 1.0.0* JAR 파일에는 클라이언트가 SignalR 허브에 연결할 수 있습니다. 최신 JAR 파일 버전 번호를 찾으려면 다음을 참조 합니다 [Maven 검색 결과](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr)합니다.

Gradle을 사용 하는 경우에 다음 줄을 추가 합니다 `dependencies` 의 섹션에 *build.gradle* 파일:

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

Maven을 사용 하 여 내에 다음 줄을 추가 합니다 `<dependencies>` 의 요소에 *pom.xml* 파일:

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a>허브에 연결하기

설정 하는 `HubConnection`, `HubConnectionBuilder` 사용 해야 합니다. 허브 URL 및 로그 수준에 대 한 연결을 구축 하는 동안 구성할 수 있습니다. 중 하나를 호출 하 여 필요한 모든 옵션을 구성 합니다 `HubConnectionBuilder` 하기 전에 메서드 `build`합니다. `start`를 사용하여 연결을 시작합니다.

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a>클라이언트에서 허브 메서드 호출하기

에 대 한 호출 `send` 허브 메서드를 호출 합니다. 허브 메서드의 이름과 허브 메서드에 정의된 모든 인수를 `send`에 전달합니다.

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

> [!NOTE]
> Azure SignalR Service를 사용 하는 경우 *서버 리스 모드*, 클라이언트에서 허브 메서드를 호출할 수 없습니다. 자세한 내용은 참조는 [SignalR Service 설명서](/azure/azure-signalr/signalr-concept-serverless-development-config)합니다.

## <a name="call-client-methods-from-hub"></a>허브에서 클라이언트 메서드 호출하기

사용 하 여 `hubConnection.on` 허브를 호출할 수 있는 클라이언트에서 메서드를 정의 합니다. 빌드 후 있지만 연결을 시작 하기 전에 메서드를 정의 합니다.

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a>로깅 추가

SignalR Java 클라이언트를 사용 하 여 [SLF4J](https://www.slf4j.org/) 로깅에 대 한 라이브러리입니다. 라이브러리의 사용자가 자신의 특정 로깅 구현을 특정 로깅 종속성에서 전환 하 여 선택한 수 있는 높은 수준의 로깅 API입니다. 다음 코드 조각을 사용 하는 방법을 보여 줍니다 `java.util.logging` SignalR Java 클라이언트를 사용 하 여 합니다.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

종속성에 대 한 로깅을 구성 하지 않으면, SLF4J 다음 경고 메시지를 사용 하 여 기본 작업 없음로 거를 로드 합니다.

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

이 안전 하 게 무시할 수 있습니다.

## <a name="android-development-notes"></a>Android 개발 정보

SignalR 클라이언트 기능에 대 한 호환성 Android SDK와 관련 하 여 대상 Android SDK 버전을 지정 하는 경우 다음 사항을 고려 합니다.

* SignalR Java 클라이언트에는 Android API 레벨 16 이상 실행 됩니다.
* Android API 레벨 20 이상 Azure SignalR Service를 통해 연결 해야 하기 때문에 합니다 [Azure SignalR Service](/azure/azure-signalr/signalr-overview) TLS 1.2를 차지 하며 s h A-1부터 시작 하는 암호 그룹을 지원 하지 않습니다. Android [지원에 대 한 SHA-256 이상 나열한 추가](https://developer.android.com/reference/javax/net/ssl/SSLSocket) API 레벨 20에서.

## <a name="configure-bearer-token-authentication"></a>전달자 토큰 인증을 구성

SignalR Java 클라이언트에서 전달자 토큰을 "액세스 토큰 팩터리가"를 제공 하 여 인증에 사용 하도록를 구성할 수 있습니다 합니다 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)합니다. 사용 하 여 [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) 제공 하는 [RxJava](https://github.com/ReactiveX/RxJava) [Single\<문자열 >](https://reactivex.io/documentation/single.html)합니다. 호출 하 여 [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)를 클라이언트에 대 한 액세스 토큰을 생성 하는 논리를 작성할 수 있습니다.

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

## <a name="known-limitations"></a>알려진 제한 사항

::: moniker range=">= aspnetcore-3.0"

* JSON 프로토콜에만 지원 됩니다.
* 대체 (fallback) 전송 및 서버 전송 이벤트 전송을 지원 되지 않습니다.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* JSON 프로토콜에만 지원 됩니다.
* Websocket 전송만 지원 됩니다.
* 스트리밍 아직 지원 되지 않습니다.

::: moniker-end

## <a name="additional-resources"></a>추가 자료

* [Java API 참조](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>
* [Azure SignalR Service 서버 리스 설명서](/azure/azure-signalr/signalr-concept-serverless-development-config)
