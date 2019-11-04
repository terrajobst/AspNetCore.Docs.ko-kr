---
title: ASP.NET Core SignalR 지원 플랫폼
author: bradygaster
description: ASP.NET Core SignalR에 대해 지원 되는 플랫폼에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/01/2019
uid: signalr/supported-platforms
ms.openlocfilehash: 1be7a307710e6e522c0088fd1ca01da11a13eda1
ms.sourcegitcommit: 77c8be22d5e88dd710f42c739748869f198865dd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/01/2019
ms.locfileid: "73426970"
---
# <a name="aspnet-core-signalr-supported-platforms"></a>ASP.NET Core SignalR 지원 플랫폼

## <a name="server-system-requirements"></a>서버 시스템 요구 사항

ASP.NET Core에 대 한 SignalR는 ASP.NET Core에서 지원 되는 모든 서버 플랫폼을 지원 합니다.

## <a name="javascript-client"></a>JavaScript 클라이언트

[JavaScript 클라이언트](https://www.npmjs.com/package/@aspnet/signalr) 는 nodejs 8 이상 버전과 다음 브라우저에서 실행 됩니다.

| 브라우저                         | Version         |
| ------------------------------- | --------------- |
| Microsoft Edge                  | 현재&dagger; |
| Mozilla Firefox                 | 현재&dagger; |
| Google Chrome; Android 포함 | 현재&dagger; |
| Safari iOS 포함            | 현재&dagger; |
| Microsoft Internet Explorer     | 11              |

*현재* &dagger;는 브라우저의 최신 버전을 참조 합니다.

## <a name="net-client"></a>.NET 클라이언트

[.Net 클라이언트](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) 는 ASP.NET Core에서 지 원하는 모든 플랫폼에서 실행 됩니다. 예를 들어 xamarin 개발자는 xamarin.ios 11.14.0.4 이상을 사용 하 여 Xamarin.ios 8.4.0.1 이상 및 iOS 앱을 사용 하 여 Android 앱을 빌드하는 데 [SignalR를 사용할 수 있습니다](https://github.com/aspnet/Announcements/issues/305) .

서버에서 IIS를 실행 하는 경우 Websocket 전송에는 Windows Server 2012 이상에서 IIS 8.0 이상이 필요 합니다. 다른 전송은 모든 플랫폼에서 지원 됩니다.

## <a name="java-client"></a>Java 클라이언트

[Java 클라이언트](https://search.maven.org/artifact/com.microsoft.aspnet/signalr) 는 java 8 이상 버전을 지원 합니다.

## <a name="unsupported-clients"></a>지원 되지 않는 클라이언트

다음 클라이언트는 사용할 수 있지만 실험적 이거나 비공식적입니다. 현재 지원 되지 않으며 그렇지 않을 수도 있습니다.

* [C++클라이언트로](https://github.com/aspnet/SignalR/tree/master/clients/cpp)

* [Swift 클라이언트](https://github.com/moozzyk/SignalR-Client-Swift)
