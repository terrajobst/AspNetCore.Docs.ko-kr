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
# <a name="publish-an-aspnet-core-signalr-app-to-an-azure-web-app"></a>Azure 웹앱에 ASP.NET Core SignalR 앱 게시

[Azure 웹앱](/azure/app-service/app-service-web-overview)은 ASP.NET Core를 비롯한 웹앱을 호스팅하기 위한 [Microsoft 클라우드 컴퓨팅](https://azure.microsoft.com/) 플랫폼 서비스입니다.

> [!NOTE]
> 본문에서는 Visual Studio에서 ASP.NET Core SignalR 앱을 게시하는 방법을 설명합니다. Azure에서 SignalR을 사용하는 방법에 대한 자세한 내용은 [Azure SignalR 서비스](https://azure.microsoft.com/services/signalr-service)를 방문해보시기 바랍니다.

## <a name="publish-the-app"></a>앱 게시

Visual Studio는 Azure 웹앱에 게시하기 위한 기본 제공 도구를 제공합니다. Visual Studio Code 사용자는 [Azure CLI](/cli/azure) 명령을 사용해서 Azure에 앱을 게시할 수 있습니다. 본문에서는 Visual Studio의 도구를 이용해서 게시하는 방법을 알아봅니다. Azure CLI를 사용해서 앱을 게시하려면 [명령줄 도구를 사용하여 Azure에 ASP.NET Core 앱 게시](/azure/app-service/app-service-web-get-started-dotnet)를 참고하시기 바랍니다.

**솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 버튼으로 클릭하고 **게시**를 선택합니다. **게시 대상 선택** 대화 상자에서 **새로 만들기**가 선택되어 있는지 확인하고 **게시**를 선택합니다.

![게시 대상 선택](publish-to-azure-web-app/_static/pick-publish-target-dialog.png)

**App Service 만들기** 대화 상자에서 다음 정보를 입력하고 **만들기**를 선택합니다.

| 항목 | 설명 |
| ---- | ----------- |
| **앱 이름** | 앱의 고유 이름입니다. |
| **구독** | 앱이 사용하는 Azure 구독입니다. |
| **리소스 그룹** | 앱이 속해 있는 관련 리소스들의 그룹입니다.  |
| **호스팅 계획** | 웹앱에 대한 가격 책정 계획입니다. |

![앱 서비스 만들기](publish-to-azure-web-app/_static/create-app-service-dialog.png)

그러면 Visual Studio가 다음 작업을 완료합니다.

* 게시 설정이 담긴 게시 프로필을 만듭니다.
* 지정한 세부 정보로 *Azure 웹앱*을 생성하거나 기존 *Azure 웹앱*을 사용합니다.
* 앱을 게시합니다.
* 게시된 웹앱이 로드된 브라우저를 실행합니다.

앱의 URL 형식이 *{앱 이름}.azurewebsites.net*이라는 점에 주의하시기 바랍니다. 예를 들어 이름이 `SignalRChattR`인 앱의 URL은 `https://signalrchattr.azurewebsites.net`입니다.

HTTP 502.2 오류가 발생할 경우 [Azure App Service에 ASP.NET Core 미리 보기 릴리스 배포](xref:host-and-deploy/azure-apps/index)를 참고하여 해결하시기 바랍니다.

## <a name="configure-signalr-web-app"></a>SignalR 웹 앱 구성

Azure 웹앱에 게시되는 ASP.NET Core SignalR 앱은 [ARR 선호도](https://en.wikipedia.org/wiki/Application_Request_Routing)를 활성화시켜야 합니다. WebSockets 전송 기능을 사용하려면 [Websocket](xref:fundamentals/websockets)을 활성화시켜야 합니다.

Azure 포털에서 웹앱의 **응용 프로그램 설정**으로 이동합니다. **웹 소켓**을 **설정**으로 설정하고 **ARR 선호도**가 **설정**인지 확인합니다.

![Azure 포털의 Azure 웹앱 설정](publish-to-azure-web-app/_static/azure-web-app-settings.png)

 Websocket 및 다른 전송은 [App Service 계획에 따라 제한](/azure/azure-subscription-service-limits#app-service-limits)됩니다.

## <a name="related-resources"></a>관련 자료

* [명령줄 도구를 사용하여 Azure에 ASP.NET Core 앱 게시](/azure/app-service/app-service-web-get-started-dotnet)
* [Visual Studio 사용하여 Azure에 ASP.NET Core 앱 게시](xref:tutorials/publish-to-azure-webapp-using-vs)
* [Azure에서 ASP.NET Core 미리 보기 앱 호스트 및 배포](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
