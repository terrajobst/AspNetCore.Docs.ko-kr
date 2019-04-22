---
title: ASP.NET Core 부하/스트레스 테스트
author: Jeremy-Meng
description: 몇 가지 주요 도구 및 부하 테스트 및 스트레스 테스트 ASP.NET Core 앱에 대 한 접근 방법에 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 04/05/2019
uid: test/loadtests
ms.openlocfilehash: 0a8449ea2c9df0f2ac93058f03af0a1a2aa66508
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59068185"
---
# <a name="aspnet-core-loadstress-testing"></a>ASP.NET Core 부하/스트레스 테스트

부하 테스트 및 스트레스 테스트 성능이 뛰어난 웹 앱을 확인 해야 하 고 확장 가능한 됩니다. 목표는 종종 비슷한 테스트를 공유 하는 경우에 다릅니다.

**부하 테스트** &ndash; 앱 응답 목표를 만족 하면서 특정 시나리오에 대 한 사용자 지정된 부하를 처리할 수 있는지 여부를 테스트 합니다. 앱이 정상 조건에서 실행 됩니다.

**스트레스 테스트** &ndash; 극단적인 경우 오랜 기간에 대 한 자주 실행 하는 경우 앱 안정성을 테스트 합니다. 앱을 배치할 높은 사용자 부하 스파이크 또는 점진적으로 증가 한 부하를 테스트 하거나 앱의 컴퓨팅 리소스를 제한 합니다.

스트레스 테스트 스트레스 앱 오류에서 복구를 정상적으로 예상 되는 동작을 반환 하는 경우를 결정 합니다. 스트레스 상태에서 앱이 정상 조건 실행 되지 않습니다.

Visual Studio 2019에는 부하 테스트 기능을 사용 하 여 Visual Studio의 마지막 버전입니다. 부하 테스트는 나중에 도구를 필요로 하는 고객에 대 한 Apache JMeter, Akamai CloudTest BlazeMeter 같은 대체 도구를 것이 좋습니다. 자세한 내용은 참조는 [Visual Studio 2019 릴리스](/visualstudio/releases/2019/release-notes#test-tools)합니다.

부하 테스트 서비스의 Azure DevOps 2020에 종료 될 예정입니다. 자세한 내용은 [클라우드 기반 부하 테스트 서비스 수명 끝](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/)합니다.

## <a name="visual-studio-tools"></a>Visual Studio Tools

Visual Studio를 사용 하면 만들기, 개발 및 웹 성능 및 부하 테스트를 디버그할 수 있습니다. 옵션은 웹 브라우저에서 작업을 기록 하 여 테스트를 만들 수 있습니다.

만들기, 구성 및 부하 테스트를 Visual Studio 2017을 사용 하 여 프로젝트를 실행 하는 방법에 대 한 정보를 참조 하세요. [빠른 시작: 부하 테스트 프로젝트 만들기](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017)를 참조하세요. 자세한 내용은 참조는 [추가 리소스](#additional-resources) 섹션입니다.

Azure DevOps를 사용 하 여 클라우드에서 온-프레미스 나 실행을 실행 하려면 부하 테스트를 구성할 수 있습니다.

## <a name="azure-devops"></a>Azure DevOps

사용 하 여 부하 테스트 실행을 시작할 수 있습니다 합니다 [Azure DevOps 테스트 계획](/azure/devops/test/load-test/index?view=vsts) 서비스입니다.

![Azure DevOps 테스트 방문 페이지를 로드합니다.](./load-tests/_static/azure-devops-load-test.png)

서비스에는 다음 테스트 형식을 지원합니다.

* Visual Studio &ndash; Visual Studio에서 만든 웹 테스트 합니다.
* HTTP 보관 &ndash; 보관 파일 내의 캡처된 HTTP 트래픽을 테스트 중에 재생 됩니다.
* [URL 기반](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) &ndash; 테스트, 요청 형식, 헤더 및 쿼리 문자열을 로드 하는 Url을 지정할 수 있습니다. 실행 지속 시간 같은 매개 변수를 설정, 부하 패턴 및 사용자 수가 구성할 수 있습니다.
* [Apache JMeter](https://jmeter.apache.org/).

## <a name="azure-portal"></a>Azure 포털

[Azure 포털을 통해 설정 하 고 웹 앱의 부하 테스트 실행](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) 에서 직접 합니다 **성능** Azure portal에서 App Service의 탭 합니다.

![Azure portal에서 azure App Service](./load-tests/_static/azure-appservice-perf-test.png)

테스트는 지정 된 URL 또는 Url을 여러 개를 테스트할 수 있는 Visual Studio 웹 테스트 파일을 사용 하 여 수동 테스트를 수 있습니다.

![Azure portal에서 새 성능 테스트 페이지](./load-tests/_static/azure-appservice-perf-test-config.png)

테스트의 끝에 생성 된 보고서는 앱의 성능 특성을 보여 줍니다. 예제에서는 통계 포함 됩니다.

* 평균 응답 시간
* 최대 처리량: 초당 요청 수
* 실패 백분율

## <a name="third-party-tools"></a>타사 도구

다음 목록은 다양 한 기능 집합을 사용 하 여 타사 웹 성능 도구를 보여 줍니다.

* [Apache JMeter](https://jmeter.apache.org/)
* [ApacheBench (ab)](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [Gatling](https://gatling.io/)
* [메뚜기](https://locust.io/)
* [West Wind WebSurge](http://websurge.west-wind.com/)
* [Netling](https://github.com/hallatore/Netling)

## <a name="additional-resources"></a>추가 자료

* [부하 테스트 블로그 시리즈](https://blogs.msdn.microsoft.com/charles_sterling/2015/06/01/load-test-series-part-i-creating-web-performance-tests-for-a-load-test/)
