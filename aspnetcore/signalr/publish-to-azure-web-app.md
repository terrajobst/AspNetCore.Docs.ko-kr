---
title: Azure App Service에 ASP.NET Core SignalR 앱 게시
author: bradygaster
description: Azure App Service에 ASP.NET Core SignalR 앱을 게시 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: d03a007ca883b3d0391b848e3e92c90469ee640a
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963926"
---
# <a name="publish-an-aspnet-core-opno-locsignalr-app-to-azure-app-service"></a>Azure App Service에 ASP.NET Core SignalR 앱 게시

[Brady Gaster](https://twitter.com/bradygaster)

[Azure App Service](/azure/app-service/app-service-web-overview) 은 ASP.NET Core를 포함 하 여 웹 앱을 호스팅하기 위한 [Microsoft 클라우드 컴퓨팅](https://azure.microsoft.com/) 플랫폼 서비스입니다.

> [!NOTE]
> 이 문서는 Visual Studio에서 ASP.NET Core SignalR 앱 게시를 나타냅니다. 자세한 내용은 [Azure에 대 한SignalR 서비스](https://azure.microsoft.com/services/signalr-service)를 참조 하세요.

## <a name="publish-the-app"></a>앱 게시

이 문서에서는 Visual Studio의 도구를 사용 하 여 게시 하는 방법을 설명 합니다. 사용자 Visual Studio Code [Azure CLI](/cli/azure) 명령을 사용 하 여 Azure에 앱을 게시할 수 있습니다. 자세한 내용은 [명령줄 도구를 사용 하 여 Azure에 ASP.NET Core 앱 게시](/azure/app-service/app-service-web-get-started-dotnet)를 참조 하세요.

1. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

1. **게시 대상 선택** 대화 상자에서 **App Service** 및 **새로 만들기** 가 선택 되어 있는지 확인 합니다.

1. **게시** 단추 드롭다운에서 **프로필 만들기** 를 선택 합니다.

   **App Service 만들기** 대화 상자에서 다음 표에 설명 된 정보를 입력 하 고 **만들기**를 선택 합니다.

   | 항목               | 설명 |
   | ------------------ | ----------- |
   | **이름**           | 앱의 고유한 이름입니다. |
   | **구독**   | 앱에서 사용 하는 Azure 구독입니다. |
   | **리소스 그룹** | 앱이 속한 관련 리소스 그룹입니다. |
   | **호스팅 계획**   | 웹 앱에 대 한 가격 책정 계획입니다. |

1. **종속성** > **추가** 드롭다운 목록에서 **Azure SignalR 서비스** 를 선택 합니다.

   ![Azure의 선택 항목을 표시 하는 종속성 영역 [! OP. NO-LOC (SignalR)] 서비스 추가 드롭다운 목록](publish-to-azure-web-app/_static/signalr-service-dependency.png)

1. **Azure SignalR 서비스** 대화 상자에서 **새 azure SignalR 서비스 인스턴스 만들기**를 선택 합니다.

1. **이름**, **리소스 그룹**및 **위치**를 제공 합니다. **Azure SignalR 서비스** 대화 상자로 돌아가서 **추가**를 선택 합니다.

Visual Studio에서는 다음 작업을 완료 합니다.

* 게시 설정을 포함 하는 게시 프로필을 만듭니다.
* 제공 된 세부 정보를 사용 하 여 *Azure 웹 앱* 을 만듭니다.
* 앱을 게시 합니다.
* 웹 앱을 로드 하는 브라우저를 시작 합니다.

앱 URL의 형식은 `{APP SERVICE NAME}.azurewebsites.net`입니다. 예를 들어 `SignalRChatApp` 이라는 앱에는 `https://signalrchatapp.azurewebsites.net`의 URL이 있습니다.

Preview .NET Core 릴리스를 대상으로 하는 앱을 배포할 때 HTTP *502.2-잘못 된 게이트웨이* 오류가 발생 하는 경우이를 해결 하려면 [Azure App Service에 ASP.NET Core preview 릴리스 배포](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) 를 참조 하세요.

## <a name="configure-the-app-in-azure-app-service"></a>Azure App Service에서 앱 구성

> [!NOTE]
> *이 섹션은 Azure SignalR 서비스를 사용 하지 않는 앱에만 적용 됩니다.*
>
> 앱이 Azure SignalR App Service 서비스를 사용 하는 경우이 섹션에서 설명 하는 ARR (응용 프로그램 요청 라우팅) 선호도 및 웹 소켓 구성이 필요 하지 않습니다. 클라이언트는 앱에 직접 연결 하는 것이 아니라 Azure SignalR 서비스에 웹 소켓을 연결 합니다.

Azure SignalR 서비스 없이 호스트 되는 앱의 경우 다음을 사용 하도록 설정 합니다.

* 사용자의 요청을 동일한 App Service 인스턴스로 라우팅하는 [ARR 선호도](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html) 입니다. 기본 설정은 **On**입니다.
* 웹 [소켓은](xref:fundamentals/websockets) 웹 소켓 전송이 작동할 수 있도록 합니다. 기본 설정은 **Off**입니다.

1. Azure Portal에서 **App Services**웹 앱으로 이동 합니다.
1. **구성** > **일반 설정**을 엽니다.
1. **웹 소켓** 을 **켜기**로 설정 합니다.
1. **ARR 선호도** 가 **On**으로 설정 되어 있는지 확인 합니다.

## <a name="app-service-plan-limits"></a>App Service 계획 제한

웹 소켓 및 기타 전송은 선택한 App Service 계획을 기준으로 제한 됩니다. 자세한 내용은 azure [구독 및 서비스 제한, 할당량 및 제약 조건](/azure/azure-subscription-service-limits#app-service-limits) 문서의 *azure Cloud Services 제한* 및 *App Service 제한* 섹션을 참조 하세요.

## <a name="additional-resources"></a>추가 자료

* [Azure SignalR Service 란?](/azure/azure-signalr/signalr-overview)
* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [명령줄 도구를 사용 하 여 Azure에 ASP.NET Core 앱 게시](/azure/app-service/app-service-web-get-started-dotnet)
* [Azure에서 ASP.NET Core Preview 앱 호스트 및 배포](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
