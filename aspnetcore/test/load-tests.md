---
title: ASP.NET Core 부하/스트레스 테스트
author: Jeremy-Meng
description: ASP.NET Core 앱의 부하 테스트 및 스트레스 테스트를 위한 몇 가지 주요 도구와 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
uid: test/loadtests
ms.openlocfilehash: 1fd77a767fb53b9276081dd712e13108094a0382
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78649641"
---
# <a name="aspnet-core-loadstress-testing"></a>ASP.NET Core 부하/스트레스 테스트

부하 테스트와 스트레스 테스트는 웹앱의 성능과 확장성을 보장하는 데 중요합니다. 유사한 테스트를 공유하는 경우가 많지만, 두 테스트의 목표는 서로 다릅니다.

**부하 테스트** - 앱에서 응답 목표를 충족하면서 특정 시나리오에서 지정된 사용자 부하를 처리할 수 있는지를 테스트합니다. 정상 조건으로 앱을 실행합니다.

**스트레스 테스트** - 극단적인 조건에서 앱을 실행할 때의 앱 안정성을 테스트하며, 오랫동안 실행하는 경우가 많습니다. 이 테스트는 앱에서 부하를 급하게 또는 점진적으로 늘려 높은 사용자 부하를 적용하거나, 앱의 컴퓨팅 리소스를 제한합니다.

스트레스 테스트는 스트레스 상태의 앱이 오류를 복구하고 정상적으로 예상 동작으로 돌아올 수 있는지를 확인합니다. 스트레스 상태에서는 정상 조건으로 앱을 실행하지 않습니다.

Visual Studio 2019는 부하 테스트 기능이 있는 마지막 Visual Studio 버전입니다. 이후에 부하 테스트 도구가 필요한 고객은 Apache JMeter, Akamai CloudTest, BlazeMeter와 같은 대체 도구를 사용하는 것이 좋습니다. 자세한 내용은 [Visual Studio 2019 릴리스 정보](/visualstudio/releases/2019/release-notes-v16.0#test-tools)를 참조하세요.

## <a name="visual-studio-tools"></a>Visual Studio Tools

Visual Studio에서는 사용자가 웹 성능 및 부하 테스트를 생성, 개발, 디버그할 수 있습니다. 웹 브라우저에서 작업을 기록하여 테스트를 만드는 옵션이 제공됩니다.

Visual Studio 2017을 사용하여 부하 테스트 프로젝트를 생성, 구성, 실행하는 방법에 대한 자세한 내용은 [빠른 시작: 부하 테스트 프로젝트 만들기](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017)를 참조하세요.

온-프레미스로 실행되거나 Azure DevOps를 사용하여 클라우드에서 실행되도록 부하 테스트를 구성할 수 있습니다.

## <a name="third-party-tools"></a>타사 도구

다음 목록에는 다양한 기능 집합을 포함하는 타사 웹 성능 도구가 나와 있습니다.

* [Apache JMeter](https://jmeter.apache.org/)
* [ApacheBench](https://httpd.apache.org/docs/2.4/programs/ab.html)(ab)
* [Gatling](https://gatling.io/)
* [k6](https://k6.io)
* [Locust](https://locust.io/)
* [West Wind WebSurge](https://websurge.west-wind.com/)
* [Netling](https://github.com/hallatore/Netling)
* [Vegeta](https://github.com/tsenart/vegeta)

