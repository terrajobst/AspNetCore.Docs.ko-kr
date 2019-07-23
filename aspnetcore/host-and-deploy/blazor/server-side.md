---
title: ASP.NET Core Blazor 서버 쪽 호스트 및 배포
author: guardrex
description: ASP.NET Core를 사용하여 Blazor 서버 쪽 앱을 호스팅하고 배포하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 06/11/2019
uid: host-and-deploy/blazor/server-side
ms.openlocfilehash: 56a03ff583bf85497e2b3bacc70123845a046e3d
ms.sourcegitcommit: 040aedca220ed24ee1726e6886daf6906f95a028
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67892691"
---
# <a name="host-and-deploy-blazor-server-side"></a><span data-ttu-id="8168c-103">Blazor 서버 쪽 호스트 및 배포</span><span class="sxs-lookup"><span data-stu-id="8168c-103">Host and deploy Blazor server-side</span></span>

<span data-ttu-id="8168c-104">작성자: [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) 및 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="8168c-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="8168c-105">호스트 구성 값</span><span class="sxs-lookup"><span data-stu-id="8168c-105">Host configuration values</span></span>

<span data-ttu-id="8168c-106">[서버 쪽 호스팅 모델](xref:blazor/hosting-models#server-side)을 사용하는 서버 쪽 앱은 [제네릭 호스트 구성 값](xref:fundamentals/host/generic-host#host-configuration)을 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8168c-106">Server-side apps that use the [server-side hosting model](xref:blazor/hosting-models#server-side) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="8168c-107">배포</span><span class="sxs-lookup"><span data-stu-id="8168c-107">Deployment</span></span>

<span data-ttu-id="8168c-108">[서버 쪽 호스팅 모델](xref:blazor/hosting-models#server-side)을 사용하면 Blazor가 서버의 ASP.NET Core 앱 내에서 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="8168c-108">With the [server-side hosting model](xref:blazor/hosting-models#server-side), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="8168c-109">UI 업데이트, 이벤트 처리 및 JavaScript 호출은 [SignalR](xref:signalr/introduction) 연결을 통해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="8168c-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="8168c-110">ASP.NET Core 앱을 호스팅할 수 있는 웹 서버가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="8168c-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="8168c-111">Visual Studio에는 **Blazor 서버 앱** 프로젝트 템플릿([dotnet new](/dotnet/core/tools/dotnet-new) 명령을 사용하는 경우 `blazorserverside` 템플릿)이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="8168c-111">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="connection-scale-out"></a><span data-ttu-id="8168c-112">연결 확장</span><span class="sxs-lookup"><span data-stu-id="8168c-112">Connection scale out</span></span>

<span data-ttu-id="8168c-113">Blazor 서버 쪽 앱에는 각 사용자에 대한 하나의 활성 SignalR 연결이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="8168c-113">Blazor server-side apps require one active SignalR connection for each user.</span></span> <span data-ttu-id="8168c-114">프로덕션 Blazor 서버 쪽 배포에는 앱에 필요한 만큼의 동시 연결을 지원하는 솔루션이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="8168c-114">A production Blazor server-side deployment requires a solution for supporting as many concurrent connections as required by the app.</span></span> <span data-ttu-id="8168c-115">[Azure SignalR Service](/azure/azure-signalr/)는 연결 확장을 처리하며, Blazor 서버 쪽 앱의 확장 솔루션으로 권장됩니다.</span><span class="sxs-lookup"><span data-stu-id="8168c-115">The [Azure SignalR Service](/azure/azure-signalr/) handles the scaling of connections and is recommended as a scaling solution for Blazor server-side apps.</span></span> <span data-ttu-id="8168c-116">자세한 내용은 <xref:signalr/publish-to-azure-web-app>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8168c-116">For more information, see <xref:signalr/publish-to-azure-web-app>.</span></span>

## <a name="signalr-configuration"></a><span data-ttu-id="8168c-117">SignalR 구성</span><span class="sxs-lookup"><span data-stu-id="8168c-117">SignalR configuration</span></span>

<span data-ttu-id="8168c-118">SignalR은 ASP.NET Core에 의해 가장 일반적인 Blazor 서버 쪽 시나리오용으로 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="8168c-118">SignalR is configured by ASP.NET Core for the most common Blazor server-side scenarios.</span></span> <span data-ttu-id="8168c-119">사용자 지정 시나리오 및 고급 시나리오의 경우에는 [추가 리소스](#additional-resources) 섹션의 SignalR 문서를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8168c-119">For custom and advanced scenarios, consult the SignalR articles in the [Additional resources](#additional-resources) section.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8168c-120">추가 자료</span><span class="sxs-lookup"><span data-stu-id="8168c-120">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="8168c-121">Azure SignalR Service 설명서</span><span class="sxs-lookup"><span data-stu-id="8168c-121">Azure SignalR Service Documentation</span></span>](/azure/azure-signalr/)
* [<span data-ttu-id="8168c-122">빠른 시작: SignalR Service를 사용하여 대화방 만들기</span><span class="sxs-lookup"><span data-stu-id="8168c-122">Quickstart: Create a chat room by using SignalR Service</span></span>](/azure/azure-signalr/signalr-quickstart-dotnet-core)
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="8168c-123">Azure App Service에 ASP.NET Core 미리 보기 릴리스 배포</span><span class="sxs-lookup"><span data-stu-id="8168c-123">Deploy ASP.NET Core preview release to Azure App Service</span></span>](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
