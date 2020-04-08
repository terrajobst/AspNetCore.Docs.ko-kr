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
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="9e513-103">ASP.NET Core 부하/스트레스 테스트</span><span class="sxs-lookup"><span data-stu-id="9e513-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="9e513-104">부하 테스트와 스트레스 테스트는 웹앱의 성능과 확장성을 보장하는 데 중요합니다.</span><span class="sxs-lookup"><span data-stu-id="9e513-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="9e513-105">유사한 테스트를 공유하는 경우가 많지만, 두 테스트의 목표는 서로 다릅니다.</span><span class="sxs-lookup"><span data-stu-id="9e513-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="9e513-106">**부하 테스트** - 앱에서 응답 목표를 충족하면서 특정 시나리오에서 지정된 사용자 부하를 처리할 수 있는지를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="9e513-106">**Load tests** &ndash; Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="9e513-107">정상 조건으로 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="9e513-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="9e513-108">**스트레스 테스트** - 극단적인 조건에서 앱을 실행할 때의 앱 안정성을 테스트하며, 오랫동안 실행하는 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="9e513-108">**Stress tests** &ndash; Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="9e513-109">이 테스트는 앱에서 부하를 급하게 또는 점진적으로 늘려 높은 사용자 부하를 적용하거나, 앱의 컴퓨팅 리소스를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="9e513-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="9e513-110">스트레스 테스트는 스트레스 상태의 앱이 오류를 복구하고 정상적으로 예상 동작으로 돌아올 수 있는지를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="9e513-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="9e513-111">스트레스 상태에서는 정상 조건으로 앱을 실행하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9e513-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="9e513-112">Visual Studio 2019는 부하 테스트 기능이 있는 마지막 Visual Studio 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="9e513-112">Visual Studio 2019 is the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="9e513-113">이후에 부하 테스트 도구가 필요한 고객은 Apache JMeter, Akamai CloudTest, BlazeMeter와 같은 대체 도구를 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="9e513-113">For customers requiring load testing tools in the future, we recommend alternate tools, such as Apache JMeter, Akamai CloudTest, and BlazeMeter.</span></span> <span data-ttu-id="9e513-114">자세한 내용은 [Visual Studio 2019 릴리스 정보](/visualstudio/releases/2019/release-notes-v16.0#test-tools)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9e513-114">For more information, see the [Visual Studio 2019 Release Notes](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="9e513-115">Visual Studio Tools</span><span class="sxs-lookup"><span data-stu-id="9e513-115">Visual Studio tools</span></span>

<span data-ttu-id="9e513-116">Visual Studio에서는 사용자가 웹 성능 및 부하 테스트를 생성, 개발, 디버그할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9e513-116">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="9e513-117">웹 브라우저에서 작업을 기록하여 테스트를 만드는 옵션이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e513-117">An option is available to create tests by recording actions in a web browser.</span></span>

<span data-ttu-id="9e513-118">Visual Studio 2017을 사용하여 부하 테스트 프로젝트를 생성, 구성, 실행하는 방법에 대한 자세한 내용은 [빠른 시작: 부하 테스트 프로젝트 만들기](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9e513-118">For information on how to create, configure, and run a load test projects using Visual Studio 2017, see [Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span></span>

<span data-ttu-id="9e513-119">온-프레미스로 실행되거나 Azure DevOps를 사용하여 클라우드에서 실행되도록 부하 테스트를 구성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9e513-119">Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="9e513-120">타사 도구</span><span class="sxs-lookup"><span data-stu-id="9e513-120">Third-party tools</span></span>

<span data-ttu-id="9e513-121">다음 목록에는 다양한 기능 집합을 포함하는 타사 웹 성능 도구가 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9e513-121">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="9e513-122">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="9e513-122">Apache JMeter</span></span>](https://jmeter.apache.org/)
* <span data-ttu-id="9e513-123">[ApacheBench](https://httpd.apache.org/docs/2.4/programs/ab.html)(ab)</span><span class="sxs-lookup"><span data-stu-id="9e513-123">[ApacheBench (ab)](https://httpd.apache.org/docs/2.4/programs/ab.html)</span></span>
* [<span data-ttu-id="9e513-124">Gatling</span><span class="sxs-lookup"><span data-stu-id="9e513-124">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="9e513-125">k6</span><span class="sxs-lookup"><span data-stu-id="9e513-125">k6</span></span>](https://k6.io)
* [<span data-ttu-id="9e513-126">Locust</span><span class="sxs-lookup"><span data-stu-id="9e513-126">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="9e513-127">West Wind WebSurge</span><span class="sxs-lookup"><span data-stu-id="9e513-127">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="9e513-128">Netling</span><span class="sxs-lookup"><span data-stu-id="9e513-128">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="9e513-129">Vegeta</span><span class="sxs-lookup"><span data-stu-id="9e513-129">Vegeta</span></span>](https://github.com/tsenart/vegeta)

