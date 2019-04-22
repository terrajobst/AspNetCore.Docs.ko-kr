---
title: Blazor 서버 쪽 호스트 및 배포
author: guardrex
description: ASP.NET Core를 사용하여 Blazor 서버 쪽 앱을 호스팅하고 배포하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: host-and-deploy/blazor/server-side
ms.openlocfilehash: 940020ee44d72d50395aad64bc924413c1bbecfb
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614713"
---
# <a name="host-and-deploy-blazor-server-side"></a>Blazor 서버 쪽 호스트 및 배포

작성자: [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) 및 [Daniel Roth](https://github.com/danroth27)

## <a name="host-configuration-values"></a>호스트 구성 값

[서버 쪽 호스팅 모델](xref:blazor/hosting-models#server-side-hosting-model)을 사용하는 서버 쪽 앱은 [제네릭 호스트 구성 값](xref:fundamentals/host/generic-host#host-configuration)을 허용할 수 있습니다.

## <a name="deployment"></a>배포

[서버 쪽 호스팅 모델](xref:blazor/hosting-models#server-side-hosting-model)을 사용하면 Blazor가 서버의 ASP.NET Core 앱 내에서 실행됩니다. UI 업데이트, 이벤트 처리 및 JavaScript 호출은 [SignalR](xref:signalr/introduction) 연결을 통해 처리됩니다.

이 앱은 게시된 출력의 ASP.NET Core 앱과 함께 포함되고, 두 앱이 함께 배포됩니다. ASP.NET Core 앱을 호스트할 수 있는 웹 서버가 필요합니다. 서버 쪽 배포의 경우 Visual Studio에는 **Razor 구성 요소** 프로젝트 템플릿([dotnet new](/dotnet/core/tools/dotnet-new) 명령을 사용하는 경우 `razorcomponents` 템플릿)이 포함됩니다.

<!--

**INSERT: Concerns are the same as publishing an ASP.NET Core SignalR app**

**INSERT: Content on the Azure SignalR Service**

**INSERT: Manually turn on WebSockets support**

-->

ASP.NET Core 앱 호스팅 및 배포에 대한 자세한 내용은 <xref:host-and-deploy/index>를 참조하세요.

Azure App Service 배포에 대한 자세한 내용은 <xref:tutorials/publish-to-azure-webapp-using-vs>를 참조하세요.
