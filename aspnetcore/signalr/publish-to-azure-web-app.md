---
title: Azure 웹앱에 ASP.NET Core SignalR 앱 게시
author: bradygaster
description: Azure 웹앱에 ASP.NET Core SignalR 앱 게시
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/20/2018
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: 1c472711a86edae8dc6e207734aa54e48c02d47d
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087697"
---
# <a name="publish-an-aspnet-core-signalr-app-to-an-azure-web-app"></a><span data-ttu-id="30c30-103">Azure 웹앱에 ASP.NET Core SignalR 앱 게시</span><span class="sxs-lookup"><span data-stu-id="30c30-103">Publish an ASP.NET Core SignalR app to an Azure Web App</span></span>

<span data-ttu-id="30c30-104">[Azure 웹앱](/azure/app-service/app-service-web-overview)은 ASP.NET Core를 비롯한 웹앱을 호스팅하기 위한 [Microsoft 클라우드 컴퓨팅](https://azure.microsoft.com/) 플랫폼 서비스입니다.</span><span class="sxs-lookup"><span data-stu-id="30c30-104">[Azure Web App](/azure/app-service/app-service-web-overview) is a [Microsoft cloud computing](https://azure.microsoft.com/) platform service for hosting web apps, including ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="30c30-105">본문에서는 Visual Studio에서 ASP.NET Core SignalR 앱을 게시하는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="30c30-105">This article refers to publishing an ASP.NET Core SignalR app from Visual Studio.</span></span> <span data-ttu-id="30c30-106">Azure에서 SignalR을 사용하는 방법에 대한 자세한 내용은 [Azure SignalR 서비스](https://azure.microsoft.com/services/signalr-service)를 방문해보시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="30c30-106">Visit [SignalR service for Azure](https://azure.microsoft.com/services/signalr-service) for more information about using SignalR on Azure.</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="30c30-107">앱 게시</span><span class="sxs-lookup"><span data-stu-id="30c30-107">Publish the app</span></span>

<span data-ttu-id="30c30-108">Visual Studio는 Azure 웹앱에 게시하기 위한 기본 제공 도구를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="30c30-108">Visual Studio provides built-in tools for publishing to an Azure Web App.</span></span> <span data-ttu-id="30c30-109">Visual Studio Code 사용자는 [Azure CLI](/cli/azure) 명령을 사용해서 Azure에 앱을 게시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="30c30-109">Visual Studio Code user can use [Azure CLI](/cli/azure) commands to publish apps to Azure.</span></span> <span data-ttu-id="30c30-110">본문에서는 Visual Studio의 도구를 이용해서 게시하는 방법을 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="30c30-110">This article covers publishing using the tools in Visual Studio.</span></span> <span data-ttu-id="30c30-111">Azure CLI를 사용해서 앱을 게시하려면 [명령줄 도구를 사용하여 Azure에 ASP.NET Core 앱 게시](/azure/app-service/app-service-web-get-started-dotnet)를 참고하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="30c30-111">To publish an app using Azure CLI, see [Publish an ASP.NET Core app to Azure with command line tools](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

<span data-ttu-id="30c30-112">**솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 버튼으로 클릭하고 **게시**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="30c30-112">Right-click on the project in **Solution Explorer** and select **Publish**.</span></span> <span data-ttu-id="30c30-113">**게시 대상 선택** 대화 상자에서 **새로 만들기**가 선택되어 있는지 확인하고 **게시**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="30c30-113">Confirm that **Create new** is checked in the **Pick a publish target** dialog, and select **Publish**.</span></span>

![게시 대상 선택](publish-to-azure-web-app/_static/pick-publish-target-dialog.png)

<span data-ttu-id="30c30-115">**App Service 만들기** 대화 상자에서 다음 정보를 입력하고 **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="30c30-115">Enter the following information in the **Create App Service** dialog and select **Create**.</span></span>

| <span data-ttu-id="30c30-116">항목</span><span class="sxs-lookup"><span data-stu-id="30c30-116">Item</span></span> | <span data-ttu-id="30c30-117">설명</span><span class="sxs-lookup"><span data-stu-id="30c30-117">Description</span></span> |
| ---- | ----------- |
| <span data-ttu-id="30c30-118">**앱 이름**</span><span class="sxs-lookup"><span data-stu-id="30c30-118">**App name**</span></span> | <span data-ttu-id="30c30-119">앱의 고유 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="30c30-119">A unique name of the app.</span></span> |
| <span data-ttu-id="30c30-120">**구독**</span><span class="sxs-lookup"><span data-stu-id="30c30-120">**Subscription**</span></span> | <span data-ttu-id="30c30-121">앱이 사용하는 Azure 구독입니다.</span><span class="sxs-lookup"><span data-stu-id="30c30-121">The Azure subscription that the app uses.</span></span> |
| <span data-ttu-id="30c30-122">**리소스 그룹**</span><span class="sxs-lookup"><span data-stu-id="30c30-122">**Resource Group**</span></span> | <span data-ttu-id="30c30-123">앱이 속해 있는 관련 리소스들의 그룹입니다.</span><span class="sxs-lookup"><span data-stu-id="30c30-123">The group of related resources to which the app belongs.</span></span>  |
| <span data-ttu-id="30c30-124">**호스팅 계획**</span><span class="sxs-lookup"><span data-stu-id="30c30-124">**Hosting Plan**</span></span> | <span data-ttu-id="30c30-125">웹앱에 대한 가격 책정 계획입니다.</span><span class="sxs-lookup"><span data-stu-id="30c30-125">The pricing plan for the web app.</span></span> |

![앱 서비스 만들기](publish-to-azure-web-app/_static/create-app-service-dialog.png)

<span data-ttu-id="30c30-127">그러면 Visual Studio가 다음 작업을 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="30c30-127">Visual Studio completes the following tasks:</span></span>

* <span data-ttu-id="30c30-128">게시 설정이 담긴 게시 프로필을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="30c30-128">Creates a Publish Profile containing publish settings.</span></span>
* <span data-ttu-id="30c30-129">지정한 세부 정보로 *Azure 웹앱*을 생성하거나 기존 *Azure 웹앱*을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="30c30-129">Creates or uses an existing *Azure Web App* with the provided details.</span></span>
* <span data-ttu-id="30c30-130">앱을 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="30c30-130">Publishes the app.</span></span>
* <span data-ttu-id="30c30-131">게시된 웹앱이 로드된 브라우저를 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="30c30-131">Launches a browser, with the published web app loaded.</span></span>

<span data-ttu-id="30c30-132">앱의 URL 형식이 *{앱 이름}.azurewebsites.net*이라는 점에 주의하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="30c30-132">Notice the format of the URL for the app is *{app name}.azurewebsites.net*.</span></span> <span data-ttu-id="30c30-133">예를 들어 이름이 `SignalRChattR`인 앱의 URL은 `https://signalrchattr.azurewebsites.net`입니다.</span><span class="sxs-lookup"><span data-stu-id="30c30-133">For example, an app named `SignalRChattR` has a URL that looks like `https://signalrchattr.azurewebsites.net`.</span></span>

<span data-ttu-id="30c30-134">HTTP 502.2 오류가 발생할 경우 [Azure App Service에 ASP.NET Core 미리 보기 릴리스 배포](xref:host-and-deploy/azure-apps/index)를 참고하여 해결하시기 바랍니다.</span><span class="sxs-lookup"><span data-stu-id="30c30-134">If an HTTP 502.2 error occurs, see [Deploy ASP.NET Core preview release to Azure App Service](xref:host-and-deploy/azure-apps/index) to resolve it.</span></span>

## <a name="configure-signalr-web-app"></a><span data-ttu-id="30c30-135">SignalR 웹 앱 구성</span><span class="sxs-lookup"><span data-stu-id="30c30-135">Configure SignalR web app</span></span>

<span data-ttu-id="30c30-136">Azure 웹앱에 게시되는 ASP.NET Core SignalR 앱은 [ARR 선호도](https://en.wikipedia.org/wiki/Application_Request_Routing)를 활성화시켜야 합니다.</span><span class="sxs-lookup"><span data-stu-id="30c30-136">ASP.NET Core SignalR apps that are published as an Azure Web App must have [ARR Affinity](https://en.wikipedia.org/wiki/Application_Request_Routing) enabled.</span></span> <span data-ttu-id="30c30-137">WebSockets 전송 기능을 사용하려면 [Websocket](xref:fundamentals/websockets)을 활성화시켜야 합니다.</span><span class="sxs-lookup"><span data-stu-id="30c30-137">[WebSockets](xref:fundamentals/websockets) should be enabled, to allow the WebSockets transport to function.</span></span>

<span data-ttu-id="30c30-138">Azure 포털에서 웹앱의 **응용 프로그램 설정**으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="30c30-138">In the Azure portal, navigate to **App Settings** for your web app.</span></span> <span data-ttu-id="30c30-139">**웹 소켓**을 **설정**으로 설정하고 **ARR 선호도**가 **설정**인지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="30c30-139">Set **WebSockets** to **On**, and verify **ARR Affinity** is **On**.</span></span>

![Azure 포털의 Azure 웹앱 설정](publish-to-azure-web-app/_static/azure-web-app-settings.png)

 <span data-ttu-id="30c30-141">Websocket 및 다른 전송은 [App Service 계획에 따라 제한](/azure/azure-subscription-service-limits#app-service-limits)됩니다.</span><span class="sxs-lookup"><span data-stu-id="30c30-141">WebSockets and other transports [are limited based on the App Service Plan](/azure/azure-subscription-service-limits#app-service-limits).</span></span>

## <a name="related-resources"></a><span data-ttu-id="30c30-142">관련 자료</span><span class="sxs-lookup"><span data-stu-id="30c30-142">Related resources</span></span>

* [<span data-ttu-id="30c30-143">명령줄 도구를 사용하여 Azure에 ASP.NET Core 앱 게시</span><span class="sxs-lookup"><span data-stu-id="30c30-143">Publish an ASP.NET Core app to Azure with command line tools</span></span>](/azure/app-service/app-service-web-get-started-dotnet)
* [<span data-ttu-id="30c30-144">Visual Studio 사용하여 Azure에 ASP.NET Core 앱 게시</span><span class="sxs-lookup"><span data-stu-id="30c30-144">Publish an ASP.NET Core app to Azure with Visual Studio</span></span>](xref:tutorials/publish-to-azure-webapp-using-vs)
* [<span data-ttu-id="30c30-145">Azure에서 ASP.NET Core 미리 보기 앱 호스트 및 배포</span><span class="sxs-lookup"><span data-stu-id="30c30-145">Host and deploy ASP.NET Core Preview apps on Azure</span></span>](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
