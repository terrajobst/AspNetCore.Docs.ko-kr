---
title: ASP.NET Core Blazor 서버 쪽 호스트 및 배포
author: guardrex
description: ASP.NET Core를 사용하여 Blazor 서버 쪽 앱을 호스팅하고 배포하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 06/11/2019
uid: host-and-deploy/blazor/server-side
ms.openlocfilehash: 8b332c2fb439e9832d604ed26f972b266eed2507
ms.sourcegitcommit: 9bb29f9ba6f0645ee8b9cabda07e3a5aa52cd659
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/26/2019
ms.locfileid: "67406116"
---
# <a name="host-and-deploy-blazor-server-side"></a>Blazor 서버 쪽 호스트 및 배포

작성자: [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) 및 [Daniel Roth](https://github.com/danroth27)

## <a name="host-configuration-values"></a>호스트 구성 값

[서버 쪽 호스팅 모델](xref:blazor/hosting-models#server-side)을 사용하는 서버 쪽 앱은 [제네릭 호스트 구성 값](xref:fundamentals/host/generic-host#host-configuration)을 허용할 수 있습니다.

## <a name="deployment"></a>배포

[서버 쪽 호스팅 모델](xref:blazor/hosting-models#server-side)을 사용하면 Blazor가 서버의 ASP.NET Core 앱 내에서 실행됩니다. UI 업데이트, 이벤트 처리 및 JavaScript 호출은 [SignalR](xref:signalr/introduction) 연결을 통해 처리됩니다.

ASP.NET Core 앱을 호스팅할 수 있는 웹 서버가 필요합니다. Visual Studio에는 **Blazor(서버 쪽)** 프로젝트 템플릿([dotnet new](/dotnet/core/tools/dotnet-new) 명령을 사용하는 경우 `blazorserverside` 템플릿)이 포함됩니다.

## <a name="connection-scale-out"></a>연결 확장

Blazor 서버 쪽 앱에는 각 사용자에 대한 하나의 활성 SignalR 연결이 필요합니다. 프로덕션 Blazor 서버 쪽 배포에는 앱에 필요한 만큼의 동시 연결을 지원하는 솔루션이 필요합니다. [Azure SignalR Service](/azure/azure-signalr/)는 연결 확장을 처리하며, Blazor 서버 쪽 앱의 확장 솔루션으로 권장됩니다. 자세한 내용은 <xref:signalr/publish-to-azure-web-app>을 참조하세요.

## <a name="signalr-configuration"></a>SignalR 구성

SignalR은 ASP.NET Core에 의해 가장 일반적인 Blazor 서버 쪽 시나리오용으로 구성됩니다. 사용자 지정 시나리오 및 고급 시나리오의 경우에는 [추가 리소스](#additional-resources) 섹션의 SignalR 문서를 참조하세요.

## <a name="additional-resources"></a>추가 자료

* <xref:signalr/introduction>
* [Azure SignalR Service 설명서](/azure/azure-signalr/)
* [빠른 시작: SignalR Service를 사용하여 대화방 만들기](/azure/azure-signalr/signalr-quickstart-dotnet-core)
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [Azure App Service에 ASP.NET Core 미리 보기 릴리스 배포](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
