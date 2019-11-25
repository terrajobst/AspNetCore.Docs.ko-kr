---
title: ASP.NET Core 로드/스트레스 테스트
author: Jeremy-Meng
description: 앱 ASP.NET Core 부하 테스트 및 스트레스 테스트에 대 한 몇 가지 주요 도구 및 접근 방식을 알아보세요.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
uid: test/loadtests
ms.openlocfilehash: edaa9e873e8e489f0c560c1736f81358ca1720d0
ms.sourcegitcommit: f40c9311058c9b1add4ec043ddc5629384af6c56
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74289020"
---
# <a name="aspnet-core-loadstress-testing"></a>ASP.NET Core 로드/스트레스 테스트

부하 테스트 및 스트레스 테스트는 웹 앱의 성능과 확장성을 보장 하는 데 중요 합니다. 이러한 목표는 유사한 테스트를 자주 공유 하는 경우에도 다릅니다.

**부하 테스트** 는 응용 프로그램이 특정 시나리오에 대해 지정 된 부하를 처리할 수 있는지 여부를 테스트 하 고 응답 목표를 만족 하는 &ndash; 테스트 합니다. 앱이 정상 상태에서 실행 됩니다.

**스트레스 테스트** 는 극단적인 조건으로 실행 되는 경우 앱 안정성을 테스트 하는 데 오랜 시간 동안 자주 실행 &ndash; 합니다. 이 테스트는 앱에서 부하가 급증 하거나 점진적으로 증가 하거나 앱의 컴퓨팅 리소스를 제한 하는 높은 사용자 부하를 발생 시킵니다.

스트레스 테스트는 스트레스 상태의 앱이 오류를 복구 하 고 정상적인 동작으로 정상적으로 돌아올 수 있는지를 확인 합니다. 스트레스 상태에서는 앱이 정상 상태에서 실행 되지 않습니다.

Visual Studio 2019는 부하 테스트 기능을 사용 하는 마지막 버전의 Visual Studio입니다. 향후 부하 테스트 도구를 필요로 하는 고객의 경우 Apache JMeter, Akamai CloudTest 및 BlazeMeter와 같은 대체 도구를 권장 합니다. 자세한 내용은 [Visual Studio 2019 릴리스 정보](/visualstudio/releases/2019/release-notes-v16.0#test-tools)를 참조 하세요.

## <a name="visual-studio-tools"></a>Visual Studio Tools

Visual Studio를 사용 하면 사용자가 웹 성능 및 부하 테스트를 만들고, 개발 하 고, 디버그할 수 있습니다. 웹 브라우저에서 작업을 기록 하 여 테스트를 만드는 데 사용할 수 있는 옵션입니다.

Visual Studio 2017을 사용 하 여 부하 테스트 프로젝트를 만들고, 구성 하 고, 실행 하는 방법에 대 한 자세한 내용은 [빠른 시작: 부하 테스트 프로젝트 만들기](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017)를 참조 하세요.

부하 테스트는 온-프레미스로 실행 되거나 Azure DevOps를 사용 하 여 클라우드에서 실행 되도록 구성할 수 있습니다.

## <a name="third-party-tools"></a>타사 도구

다음 목록에는 다양 한 기능 집합을 포함 하는 타사 웹 성능 도구가 포함 되어 있습니다.

* [Apache JMeter](https://jmeter.apache.org/)
* [ApacheBench (ab)](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [Gatling](https://gatling.io/)
* [Locust](https://locust.io/)
* [서 부 윈드 WebSurge](https://websurge.west-wind.com/)
* [Netling](https://github.com/hallatore/Netling)
* [Vegeta](https://github.com/tsenart/vegeta)
