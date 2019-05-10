---
title: 모니터링 및 디버그-ASP.NET Core 및 Azure를 사용 하 여 DevOps
author: CamSoper
description: 모니터링 및 ASP.NET Core 및 Azure를 사용 하 여 DevOps 솔루션의 일부로 코드 디버깅
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/monitor
ms.openlocfilehash: 00489bd92dfff8fd80bd24c2e60193d32031d7c4
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64893070"
---
# <a name="monitor-and-debug"></a>모니터링 및 디버그

앱을 배포할 것 이므로 DevOps 파이프라인을 작성, 모니터링 하 고 앱의 문제를 해결 하는 방법을 이해 해야 합니다.

이 섹션에서는 다음 작업을 완료 합니다.

* 기본 모니터링 및 문제 해결 Azure portal에서 데이터 찾기
* Azure Monitor 메트릭을 면밀히 검토 모든 Azure 서비스에서 제공 하는 방법에 대해 알아봅니다.
* 응용 프로그램 프로 파일링에 대 한 Application Insights를 사용 하 여 웹 앱 연결
* 로깅을 설정 하 고 로그를 다운로드할 수 있는 위치에 알아봅니다.
* Stream 로그를 실시간으로
* 경고를 설정 하는 위치에 알아봅니다.
* 원격 디버깅 Azure App Service web apps에 알아봅니다.

## <a name="basic-monitoring-and-troubleshooting"></a>기본 모니터링 및 문제 해결

App Service 웹 앱은 쉽게 실시간으로 모니터링 됩니다. Azure portal 메트릭 이해 하기 쉬운 차트 및 그래프를 렌더링합니다.

1. 엽니다는 [Azure portal](https://portal.azure.com)로 이동한 다음 합니다 *mywebapp\<unique_number\>*  App Service.

1. 합니다 **개요** 탭 최근 메트릭을 표시 하는 그래프를 포함 하 여 "a 일목요연" 유용한 정보를 표시 합니다.

    ![스크린 샷 보여 주는 요약 패널](./media/monitoring/overview.png)

    * **Http 5xx**: ASP.NET Core 코드의 예외 일반적으로 서버 쪽 오류의 수입니다.
    * **데이터에서**: 웹 앱으로 들어오는 데이터 수신 합니다.
    * **데이터 출력**: 클라이언트에 웹 앱에서 데이터 송신 합니다.
    * **요청**: HTTP 요청 수입니다.
    * **평균 응답 시간**: HTTP 요청에 응답 하도록 웹 앱에 대 한 평균 시간입니다.

    여러 셀프 서비스 도구 문제 해결 및 최적화를 위한이 페이지에도 있습니다.

    ![스크린 샷 보여 주는 셀프 서비스 도구](./media/monitoring/wizards.png)

    * **문제 진단 및 해결** 셀프 서비스 문제 해결사에 전달 됩니다.
    * **Application Insights** 성능과 앱 동작을 프로 파일링 되며이 섹션의 뒷부분에서 설명 됩니다.
    * **App Service Advisor** 앱 환경을 튜닝 지침을 제공 합니다.

## <a name="advanced-monitoring"></a>고급 모니터링

[Azure Monitor](/azure/monitoring-and-diagnostics/) 모든 메트릭을 모니터링 하 고 Azure 서비스에서 경고 설정에 대 한 중앙 집중식된 서비스입니다. Azure Monitor에서 관리자 수 세부적으로 성과 추적 하며 추세를 파악 합니다. 각 Azure 서비스는 자체 [메트릭 집합이](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) Azure Monitor로 합니다.

## <a name="profile-with-application-insights"></a>Application Insights 사용 하 여 프로필

[Application Insights](/azure/application-insights/app-insights-overview) 웹 앱 및 사용자가 사용 하는 방법의 안정성 및 성능 분석에 대 한 Azure 서비스입니다. Application Insights의 데이터를 훨씬 광범위 하 고 Azure Monitor의 보다 수준이 깊은 합니다. 개발자와 관리자가 앱을 개선 하는 것에 대 한 키 정보를 사용 하 여 데이터를 제공할 수 있습니다. Application Insights는 코드 변경 없이 Azure App Service 리소스에 추가할 수 있습니다.

1. 엽니다는 [Azure portal](https://portal.azure.com)로 이동한 다음 합니다 *mywebapp\<unique_number\>*  App Service.
1. **개요** 탭을 클릭 합니다 **Application Insights** 바둑판식으로 배열 합니다.

    ![Application Insights 타일](./media/monitoring/app-insights.png)

1. 선택 된 **새 리소스 만들기** 라디오 단추입니다. 기본 리소스 이름을 사용 하 고 Application Insights 리소스에 대 한 위치를 선택 합니다. 위치는 웹 앱에 맞게 필요 하지 않습니다.

    ![Application Insights 설정](./media/monitoring/new-app-insights.png)

1. 에 대 한 **런타임/프레임 워크**를 선택 **ASP.NET Core**합니다. 기본 설정을 적용 합니다.
1. **확인**을 선택합니다. 확인 메시지가 표시 되 면 선택 **계속**합니다.
1. 리소스를 만든 후에 Application Insights 페이지로 직접 이동 하려면 Application Insights 리소스의 이름을 클릭 합니다.

    ![새 Application Insights 리소스는 준비](./media/monitoring/new-app-insights-done.png)

앱을 사용 하면 데이터를 누적 합니다. 선택 **새로 고침** 새 데이터를 사용 하 여 블레이드를 다시 로드 합니다.

![Application Insights 개요 탭](./media/monitoring/app-insights-overview.png)

Application Insights 추가 구성 없이 유용한 서버 쪽 정보를 제공 합니다. Application Insights에서 가장 많은 가치를 얻을 [Application Insights SDK를 사용 하 여 앱을 계측](/azure/application-insights/app-insights-asp-net-core)합니다. 제대로 구성 되었으면 서비스 웹 서버 및 클라이언트 쪽 성능을 포함 하 여 브라우저에서 종단 간 모니터링을 제공 합니다. 자세한 내용은 참조는 [Application Insights 설명서](/azure/application-insights/app-insights-overview)합니다.

## <a name="logging"></a>로깅

웹 서버 및 응용 프로그램 로그는 Azure App Service에서 기본적으로 비활성화 됩니다. 다음 단계를 사용 하 여 로그를 사용 하도록 설정 합니다.

1. 열기는 [Azure portal](https://portal.azure.com)로 이동 합니다 *mywebapp\<unique_number\>*  App Service.
1. 왼쪽 메뉴에서 아래로 스크롤하여 합니다 **모니터링** 섹션입니다. 선택 **진단 로그**합니다.

    ![진단 로그 링크](./media/monitoring/logging.png)

1. 켜기 **응용 프로그램 로깅 (파일 시스템)** 합니다. 메시지가 표시 되 면 웹 앱에 로그인 하는 앱을 사용 하도록 설정 하려면 확장을 설치 하는 상자를 클릭 합니다.
1. 설정할 **웹 서버 로깅을** 하 **파일 시스템**입니다.
1. 입력 된 **보존 기간** 일에서입니다. 예를 들어 30입니다.
1. **저장**을 클릭합니다.

ASP.NET Core 및 웹 서버 (App Service) 로그는 웹 앱에 대 한 생성 됩니다. 표시 되는 FTP/FTPS 정보를 사용 하 여 서 다운로드할 수 있습니다. 암호는이 가이드의 앞부분에서 만든 배포 자격 증명으로 동일 합니다. 로그 수 [PowerShell 또는 Azure CLI를 사용 하 여 로컬 컴퓨터에 직접 스트리밍할](/azure/app-service/web-sites-enable-diagnostic-log#download)합니다. 로그 수도 있습니다 [Application Insights에서 볼](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights)합니다.

## <a name="log-streaming"></a>로그 스트리밍

앱 및 웹 서버 로그는 포털을 통해 실시간으로에서 스트리밍할 수 있습니다.

1. 열기는 [Azure portal](https://portal.azure.com)로 이동 합니다 *mywebapp\<unique_number\>*  App Service.
1. 왼쪽 메뉴에서 아래로 스크롤하여 합니다 **모니터링** 선택한 섹션 **로그 스트림**합니다.

    ![링크가 표시 된 스크린샷 로그 스트림](./media/monitoring/log-stream.png)

로그 수도 있습니다 [Azure CLI 또는 Azure PowerShell을 통해 스트리밍할](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs)Cloud Shell을 통해 등입니다.

## <a name="alerts"></a>,

Azure Monitor 해줍니다 [실시간으로 경고](/azure/monitoring-and-diagnostics/insights-alerts-portal) 메트릭, 관리 이벤트 및 기타 조건을 기준으로 합니다.

> *참고: 현재 웹 앱 메트릭 경고는 경고 (클래식) 서비스에서 사용할 수만 합니다.*

합니다 [경고 (클래식) 서비스](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) 또는 Azure Monitor에서 찾을 수 있습니다 합니다 **모니터링** App Service 설정의 섹션입니다.

![경고 (클래식) 링크](./media/monitoring/alerts.png)

## <a name="live-debugging"></a>라이브 디버깅

Azure App Service 일 수 있습니다 [Visual Studio를 사용 하 여 원격으로 디버그할](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) 경우 로그 충분 한 정보를 제공 하지 않습니다. 그러나 원격 디버깅 디버그 기호를 사용 하 여 컴파일해야 하는 앱이 필요 합니다. 디버깅 하지 않아야 할 프로덕션 환경에서 제외 하 고 최후의 수단으로.

## <a name="conclusion"></a>결론

이 섹션에서는 다음 태스크를 완료 합니다.

* 기본 모니터링 및 문제 해결 Azure portal에서 데이터 찾기
* Azure Monitor 메트릭을 면밀히 검토 모든 Azure 서비스에서 제공 하는 방법에 대해 알아봅니다.
* 응용 프로그램 프로 파일링에 대 한 Application Insights를 사용 하 여 웹 앱 연결
* 로깅을 설정 하 고 로그를 다운로드할 수 있는 위치에 알아봅니다.
* Stream 로그를 실시간으로
* 경고를 설정 하는 위치에 알아봅니다.
* 원격 디버깅 Azure App Service web apps에 알아봅니다.

## <a name="additional-reading"></a>추가 참조 항목

* <xref:host-and-deploy/azure-apps/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [Application Insights를 사용 하 여 Azure 웹 앱 성능 모니터링](/azure/application-insights/app-insights-azure-web-apps)
* [Azure App Service에서 웹앱에 대한 진단 로깅 사용](/azure/app-service/web-sites-enable-diagnostic-log)
* [Visual Studio를 사용하여 Azure App Service의 웹앱 문제 해결](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Azure 서비스에 대 한 Azure Monitor에서 클래식 메트릭 경고를 만들려면 Azure portal](/azure/monitoring-and-diagnostics/insights-alerts-portal)
