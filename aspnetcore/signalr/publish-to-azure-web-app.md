---
title: 게시는 ASP.NET Core SignalR 앱을 Azure App Service
author: bradygaster
description: Azure App Service에 ASP.NET Core SignalR 앱을 게시 하는 방법에 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/26/2019
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: 87a9c93add373b24e3c473912cdbfcc00bbebf7e
ms.sourcegitcommit: 9bb29f9ba6f0645ee8b9cabda07e3a5aa52cd659
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/26/2019
ms.locfileid: "67406108"
---
# <a name="publish-an-aspnet-core-signalr-app-to-azure-app-service"></a>게시는 ASP.NET Core SignalR 앱을 Azure App Service

[Brady Gaster](https://twitter.com/bradygaster)

[Azure App Service](/azure/app-service/app-service-web-overview) 되는 [Microsoft 클라우드 컴퓨팅](https://azure.microsoft.com/) ASP.NET Core를 포함 하 여 웹 앱을 호스트 하기 위한 플랫폼 서비스입니다.

> [!NOTE]
> 본문에서는 Visual Studio에서 ASP.NET Core SignalR 앱을 게시하는 방법을 설명합니다. 자세한 내용은 [azure SignalR service](https://azure.microsoft.com/services/signalr-service)합니다.

## <a name="publish-the-app"></a>앱 게시

본문에서는 Visual Studio의 도구를 이용해서 게시하는 방법을 알아봅니다. Visual Studio Code를 사용할 수 있습니다 [Azure CLI](/cli/azure) Azure에 앱을 게시 하는 명령입니다. 자세한 내용은 [명령줄 도구를 사용 하 여 Azure에 ASP.NET Core 앱을 게시](/azure/app-service/app-service-web-get-started-dotnet)합니다.

1. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 버튼으로 클릭하고 **게시**를 선택합니다.

1. 있는지 확인 **App Service** 하 고 **새로 만들기** 에서 선택한 합니다 **게시 대상 선택** 대화 합니다.

1. 선택 **프로필 만들기** 에서 합니다 **게시** 드롭 다운 단추입니다.

   다음 표에 설명 된 정보를 입력 합니다 **App Service 만들기** 대화 상자와 선택 **만들기**합니다.

   | 항목               | 설명 |
   | ------------------ | ----------- |
   | **이름**           | 앱의 고유한 이름입니다. |
   | **구독**   | 앱을 사용 하는 azure 구독입니다. |
   | **리소스 그룹** | 앱이 속해 있는 관련된 리소스의 그룹입니다. |
   | **호스팅 계획**   | 웹 앱에 대 한 가격 책정 계획입니다. |

1. 선택 된 **Azure SignalR Service** 에 **종속성** > **추가** 드롭 다운 목록:

   ![추가 드롭다운 목록에는 다양 한 Azure SignalR Service를 보여 주는 종속성 영역](publish-to-azure-web-app/_static/signalr-service-dependency.png)

1. 에 **Azure SignalR Service** 대화 상자에서 **새 Azure SignalR Service 인스턴스를 만들고**합니다.

1. 제공 된 **이름**, **리소스 그룹**, 및 **위치**합니다. 반환 합니다 **Azure SignalR Service** 대화 상자와 선택 **추가**합니다.

그러면 Visual Studio가 다음 작업을 완료합니다.

* 게시 설정이 담긴 게시 프로필을 만듭니다.
* 만듭니다는 *Azure Web App* 제공된 된 정보를 사용 하 여 합니다.
* 앱을 게시합니다.
* 웹 앱을 로드 하는 브라우저를 시작 합니다.

앱의 URL의 형식은 `{APP SERVICE NAME}.azurewebsites.net`합니다. 예를 들어, 명명 된 앱 `SignalRChatApp` 에 URL의 `https://signalrchatapp.azurewebsites.net`합니다.

경우 HTTP *502.2-잘못 된 게이트웨이* 미리 보기 릴리스에서.NET Core를 대상으로 하는 앱을 배포할 때 오류가 발생을 참조 하십시오 [Azure App Service에 ASP.NET Core 배포 미리 보기 릴리스](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) 해결 합니다.

## <a name="configure-the-app-in-azure-app-service"></a>Azure App Service에서 앱 구성

> [!NOTE]
> *이 섹션에서는 Azure SignalR Service를 사용 하지 앱에만 적용 됩니다.*
>
> 앱이 Azure SignalR Service를 사용 하는 경우 App Service 응용 프로그램 요청 라우팅 (ARR) 선호도 및이 섹션에서 설명 하는 웹 소켓의 구성이 필요 하지 않습니다. 클라이언트 앱에 직접 Azure SignalR Service에 해당 웹 소켓을 연결합니다.

Azure SignalR Service 없이 호스트 되는 앱에 대해 사용 하도록 설정 합니다.

* [ARR 선호도](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html) 동일한 App Service 인스턴스를 다시 사용자 로부터 요청을 라우팅하도록 합니다. 기본 설정은 **에서**합니다.
* [웹 소켓](xref:fundamentals/websockets) 함수 하는 웹 소켓 전송 수 있도록 합니다. 기본 설정은 **해제**합니다.

1. Azure portal에서 웹 앱으로 이동 **App Services**합니다.
1. 오픈 **Configuration** > **일반 설정**합니다.
1. 설정할 **웹 소켓** 하 **에서**합니다.
1. 확인 **ARR 선호도** 로 설정 된 **에서**합니다.

## <a name="app-service-plan-limits"></a>App Service 계획 제한

웹 소켓 및 다른 전송에 따라 제한 됩니다 App Service 계획을 선택 합니다. 자세한 내용은 참조 하세요. 합니다 *Azure Cloud Services 제한* 및 *App Service 제한* 섹션의 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](/azure/azure-subscription-service-limits#app-service-limits) 문서입니다.

## <a name="additional-resources"></a>추가 자료

* [Azure SignalR Service 란?](/azure/azure-signalr/signalr-overview)
* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [명령줄 도구를 사용하여 Azure에 ASP.NET Core 앱 게시](/azure/app-service/app-service-web-get-started-dotnet)
* [Azure에서 ASP.NET Core 미리 보기 앱 호스트 및 배포](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
