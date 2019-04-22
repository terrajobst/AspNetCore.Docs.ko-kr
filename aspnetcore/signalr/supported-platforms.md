---
title: ASP.NET Core SignalR 지원 플랫폼
author: bradygaster
description: ASP.NET Core SignalR에 대 한 지원 되는 플랫폼에 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/06/2019
uid: signalr/supported-platforms
ms.openlocfilehash: fefaaf97de3f1fabf8f3154bf5b4ccb37195ccff
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59068224"
---
# <a name="aspnet-core-signalr-supported-platforms"></a>ASP.NET Core SignalR 지원 플랫폼

## <a name="server-system-requirements"></a>서버 시스템 요구 사항

ASP.NET core SignalR은 ASP.NET Core에서 지 원하는 서버 플랫폼을 지원 합니다.

## <a name="javascript-client"></a>JavaScript 클라이언트

[JavaScript 클라이언트](https://www.npmjs.com/package/@aspnet/signalr)는 NodeJS 8 이상의 버전과 다음 브라우저에서 실행됩니다.

| 브라우저                         | 버전 |
| ------------------------------- | ------- |
| Microsoft Edge                  | 현재 |
| Mozilla Firefox                 | 현재 |
| Google Chrome(Android 포함) | 현재 |
| Safari(iOS 포함)            | 현재 |
| Microsoft Internet Explorer     | 11      |
 
## <a name="net-client"></a>.NET 클라이언트

합니다 [.NET 클라이언트](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) ASP.NET Core에서 지 원하는 모든 플랫폼에서 실행 됩니다. 예를 들어 [Xamarin 개발자 SignalR을 사용 하 여](https://github.com/aspnet/Announcements/issues/305) 8.4.0.1 Xamarin.Android를 사용 하 여 Android 앱을 빌드하기 위한 나중 및 11.14.0.4 Xamarin.iOS를 사용 하 여 iOS 앱 이상.

서버에서 IIS를 실행 하는 경우 Websocket 전송이 IIS 8.0 또는 Windows Server 2012 이상 이상이 필요 합니다. 다른 전송 방식들은 모든 플랫폼에서 지원됩니다.

## <a name="java-client"></a>Java 클라이언트

[Java 클라이언트](https://search.maven.org/artifact/com.microsoft.aspnet/signalr)는 Java 8 이상의 버전을 지원합니다.

## <a name="unsupported-clients"></a>지원되지 않는 클라이언트

다음 클라이언트는 사용할 수는 있지만 실험적이거나 비공식적입니다. 현재 지원 되지 않으며 되지 않을 수 있습니다.

* [C++ 클라이언트](https://github.com/aspnet/SignalR/tree/master/clients/cpp)

* [Swift 클라이언트](https://github.com/moozzyk/SignalR-Client-Swift)
