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
# <a name="host-and-deploy-blazor-server-side"></a><span data-ttu-id="cf0ff-103">Blazor 서버 쪽 호스트 및 배포</span><span class="sxs-lookup"><span data-stu-id="cf0ff-103">Host and deploy Blazor server-side</span></span>

<span data-ttu-id="cf0ff-104">작성자: [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) 및 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="cf0ff-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="cf0ff-105">호스트 구성 값</span><span class="sxs-lookup"><span data-stu-id="cf0ff-105">Host configuration values</span></span>

<span data-ttu-id="cf0ff-106">[서버 쪽 호스팅 모델](xref:blazor/hosting-models#server-side-hosting-model)을 사용하는 서버 쪽 앱은 [제네릭 호스트 구성 값](xref:fundamentals/host/generic-host#host-configuration)을 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cf0ff-106">Server-side apps that use the [server-side hosting model](xref:blazor/hosting-models#server-side-hosting-model) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="cf0ff-107">배포</span><span class="sxs-lookup"><span data-stu-id="cf0ff-107">Deployment</span></span>

<span data-ttu-id="cf0ff-108">[서버 쪽 호스팅 모델](xref:blazor/hosting-models#server-side-hosting-model)을 사용하면 Blazor가 서버의 ASP.NET Core 앱 내에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="cf0ff-108">With the [server-side hosting model](xref:blazor/hosting-models#server-side-hosting-model), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="cf0ff-109">UI 업데이트, 이벤트 처리 및 JavaScript 호출은 [SignalR](xref:signalr/introduction) 연결을 통해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="cf0ff-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="cf0ff-110">이 앱은 게시된 출력의 ASP.NET Core 앱과 함께 포함되고, 두 앱이 함께 배포됩니다.</span><span class="sxs-lookup"><span data-stu-id="cf0ff-110">The app is included with the ASP.NET Core app in the published output, and the two apps are deployed together.</span></span> <span data-ttu-id="cf0ff-111">ASP.NET Core 앱을 호스트할 수 있는 웹 서버가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="cf0ff-111">A web server that's capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="cf0ff-112">서버 쪽 배포의 경우 Visual Studio에는 **Razor 구성 요소** 프로젝트 템플릿([dotnet new](/dotnet/core/tools/dotnet-new) 명령을 사용하는 경우 `razorcomponents` 템플릿)이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="cf0ff-112">For a server-side deployment, Visual Studio includes the **Razor Components** project template (`razorcomponents` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

<!--

**INSERT: Concerns are the same as publishing an ASP.NET Core SignalR app**

**INSERT: Content on the Azure SignalR Service**

**INSERT: Manually turn on WebSockets support**

-->

<span data-ttu-id="cf0ff-113">ASP.NET Core 앱 호스팅 및 배포에 대한 자세한 내용은 <xref:host-and-deploy/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cf0ff-113">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="cf0ff-114">Azure App Service 배포에 대한 자세한 내용은 <xref:tutorials/publish-to-azure-webapp-using-vs>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="cf0ff-114">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>
