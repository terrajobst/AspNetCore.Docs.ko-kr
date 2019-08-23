---
title: ASP.NET Core 로드/스트레스 테스트
author: Jeremy-Meng
description: 앱 ASP.NET Core 부하 테스트 및 스트레스 테스트에 대 한 몇 가지 주요 도구 및 접근 방식을 알아보세요.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
uid: test/loadtests
ms.openlocfilehash: 7a9dfc1fedf747ab26daa573b61ed01c31709058
ms.sourcegitcommit: 8835b6777682da6fb3becf9f9121c03f89dc7614
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69975242"
---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="1c6be-103">ASP.NET Core 로드/스트레스 테스트</span><span class="sxs-lookup"><span data-stu-id="1c6be-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="1c6be-104">부하 테스트 및 스트레스 테스트는 웹 앱의 성능과 확장성을 보장 하는 데 중요 합니다.</span><span class="sxs-lookup"><span data-stu-id="1c6be-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="1c6be-105">이러한 목표는 유사한 테스트를 자주 공유 하는 경우에도 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="1c6be-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="1c6be-106">**부하 테스트** &ndash; 응용 프로그램에서 특정 시나리오에 대해 지정 된 사용자 부하를 처리할 수 있는지 여부를 테스트 하 고 응답 목표를 만족 시킵니다.</span><span class="sxs-lookup"><span data-stu-id="1c6be-106">**Load tests** &ndash; Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="1c6be-107">앱이 정상 상태에서 실행 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1c6be-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="1c6be-108">**스트레스 테스트** &ndash; 극단적인 조건으로 실행 될 때 앱 안정성을 테스트 합니다 (종종 오랜 시간 동안).</span><span class="sxs-lookup"><span data-stu-id="1c6be-108">**Stress tests** &ndash; Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="1c6be-109">이 테스트는 앱에서 부하가 급증 하거나 점진적으로 증가 하거나 앱의 컴퓨팅 리소스를 제한 하는 높은 사용자 부하를 발생 시킵니다.</span><span class="sxs-lookup"><span data-stu-id="1c6be-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="1c6be-110">스트레스 테스트는 스트레스 상태의 앱이 오류를 복구 하 고 정상적인 동작으로 정상적으로 돌아올 수 있는지를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="1c6be-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="1c6be-111">스트레스 상태에서는 앱이 정상 상태에서 실행 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="1c6be-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="1c6be-112">Visual Studio 2019는 부하 테스트 기능을 사용 하는 마지막 버전의 Visual Studio입니다.</span><span class="sxs-lookup"><span data-stu-id="1c6be-112">Visual Studio 2019 is the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="1c6be-113">향후 부하 테스트 도구를 필요로 하는 고객의 경우 Apache JMeter, Akamai CloudTest 및 BlazeMeter와 같은 대체 도구를 권장 합니다.</span><span class="sxs-lookup"><span data-stu-id="1c6be-113">For customers requiring load testing tools in the future, we recommend alternate tools, such as Apache JMeter, Akamai CloudTest, and BlazeMeter.</span></span> <span data-ttu-id="1c6be-114">자세한 내용은 [Visual Studio 2019 릴리스 정보](/visualstudio/releases/2019/release-notes-v16.0#test-tools)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="1c6be-114">For more information, see the [Visual Studio 2019 Release Notes](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span></span>

<span data-ttu-id="1c6be-115">Azure DevOps의 부하 테스트 서비스는 2020로 종료 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1c6be-115">The load testing service in Azure DevOps is ending in 2020.</span></span> <span data-ttu-id="1c6be-116">자세한 내용은 [클라우드 기반 부하 테스트 서비스 수명 종료](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/)를 참조 하세요.</span><span class="sxs-lookup"><span data-stu-id="1c6be-116">For more information, see [Cloud-based load testing service end of life](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="1c6be-117">Visual Studio Tools</span><span class="sxs-lookup"><span data-stu-id="1c6be-117">Visual Studio tools</span></span>

<span data-ttu-id="1c6be-118">Visual Studio를 사용 하면 사용자가 웹 성능 및 부하 테스트를 만들고, 개발 하 고, 디버그할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1c6be-118">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="1c6be-119">웹 브라우저에서 작업을 기록 하 여 테스트를 만드는 데 사용할 수 있는 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="1c6be-119">An option is available to create tests by recording actions in a web browser.</span></span>

<span data-ttu-id="1c6be-120">Visual Studio 2017을 [사용 하 여 부하 테스트 프로젝트를 만들고, 구성 하 고, 실행 하는 방법에 대 한 자세한 내용은 빠른 시작: 부하 테스트 프로젝트 만들기](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="1c6be-120">For information on how to create, configure, and run a load test projects using Visual Studio 2017, see [Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span></span>

<span data-ttu-id="1c6be-121">부하 테스트는 온-프레미스로 실행 되거나 Azure DevOps를 사용 하 여 클라우드에서 실행 되도록 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1c6be-121">Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="azure-devops"></a><span data-ttu-id="1c6be-122">Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="1c6be-122">Azure DevOps</span></span>

<span data-ttu-id="1c6be-123">[Azure DevOps Test Plans](/azure/devops/test/load-test/index?view=vsts) 서비스를 사용 하 여 부하 테스트 실행을 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1c6be-123">Load test runs can be started using the [Azure DevOps Test Plans](/azure/devops/test/load-test/index?view=vsts) service.</span></span>

![Azure DevOps 부하 테스트 방문 페이지](./load-tests/_static/azure-devops-load-test.png)

<span data-ttu-id="1c6be-125">서비스는 다음과 같은 테스트 형식을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="1c6be-125">The service supports the following test formats:</span></span>

* <span data-ttu-id="1c6be-126">Visual studio &ndash; 에서 만든 visual studio 웹 테스트입니다.</span><span class="sxs-lookup"><span data-stu-id="1c6be-126">Visual Studio &ndash; Web test created in Visual Studio.</span></span>
* <span data-ttu-id="1c6be-127">보관 내의 &ndash; http 보관 http 트래픽은 테스트 하는 동안 재생 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1c6be-127">HTTP Archive &ndash; Captured HTTP traffic inside archive is replayed during testing.</span></span>
* <span data-ttu-id="1c6be-128">[URL 기반](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) &ndash; 테스트, 요청 형식, 헤더 및 쿼리 문자열을 로드 하는 url을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1c6be-128">[URL-based](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) &ndash; Allows specifying URLs to load test, request types, headers, and query strings.</span></span> <span data-ttu-id="1c6be-129">기간, 부하 패턴 및 사용자 수와 같은 실행 설정 매개 변수를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1c6be-129">Run setting parameters such as duration, load pattern, and number of users can be configured.</span></span>
* <span data-ttu-id="1c6be-130">[Apache JMeter](https://jmeter.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="1c6be-130">[Apache JMeter](https://jmeter.apache.org/).</span></span>

## <a name="azure-portal"></a><span data-ttu-id="1c6be-131">Azure Portal</span><span class="sxs-lookup"><span data-stu-id="1c6be-131">Azure portal</span></span>

<span data-ttu-id="1c6be-132">Azure Portal를 사용 하면 Azure Portal App Service의 **성능** 탭에서 직접 [웹 앱의 부하 테스트를 설정 하 고 실행할 수 있습니다](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) .</span><span class="sxs-lookup"><span data-stu-id="1c6be-132">[Azure portal allows setting up and running load testing of web apps](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) directly from the **Performance** tab of the App Service in Azure portal.</span></span>

![Azure Portal Azure App Service](./load-tests/_static/azure-appservice-perf-test.png)

<span data-ttu-id="1c6be-134">테스트는 지정 된 URL 또는 Visual Studio 웹 테스트 파일을 사용 하는 수동 테스트 일 수 있습니다 .이를 통해 여러 Url을 테스트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1c6be-134">The test can be a manual test with a specified URL or a Visual Studio Web Test file, which can test multiple URLs.</span></span>

![Azure Portal의 새 성능 테스트 페이지](./load-tests/_static/azure-appservice-perf-test-config.png)

<span data-ttu-id="1c6be-136">테스트 끝에 생성 된 보고서에는 앱의 성능 특성이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="1c6be-136">At end of the test, generated reports show the performance characteristics of the app.</span></span> <span data-ttu-id="1c6be-137">예제 통계는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="1c6be-137">Example statistics include:</span></span>

* <span data-ttu-id="1c6be-138">평균 응답 시간</span><span class="sxs-lookup"><span data-stu-id="1c6be-138">Average response time</span></span>
* <span data-ttu-id="1c6be-139">최대 처리량: 초당 요청 수</span><span class="sxs-lookup"><span data-stu-id="1c6be-139">Max throughput: requests per second</span></span>
* <span data-ttu-id="1c6be-140">실패 비율</span><span class="sxs-lookup"><span data-stu-id="1c6be-140">Failure percentage</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="1c6be-141">타사 도구</span><span class="sxs-lookup"><span data-stu-id="1c6be-141">Third-party tools</span></span>

<span data-ttu-id="1c6be-142">다음 목록에는 다양 한 기능 집합을 포함 하는 타사 웹 성능 도구가 포함 되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="1c6be-142">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="1c6be-143">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="1c6be-143">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="1c6be-144">ApacheBench (ab)</span><span class="sxs-lookup"><span data-stu-id="1c6be-144">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="1c6be-145">Gatling</span><span class="sxs-lookup"><span data-stu-id="1c6be-145">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="1c6be-146">Locust</span><span class="sxs-lookup"><span data-stu-id="1c6be-146">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="1c6be-147">서 부 윈드 WebSurge</span><span class="sxs-lookup"><span data-stu-id="1c6be-147">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="1c6be-148">Netling</span><span class="sxs-lookup"><span data-stu-id="1c6be-148">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="1c6be-149">Vegeta</span><span class="sxs-lookup"><span data-stu-id="1c6be-149">Vegeta</span></span>](https://github.com/tsenart/vegeta)
