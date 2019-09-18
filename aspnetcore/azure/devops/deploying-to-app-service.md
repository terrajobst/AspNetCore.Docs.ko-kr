---
title: App Service-ASP.NET Core 및 Azure를 사용 하 여 DevOps에 앱 배포
author: CamSoper
description: Azure App service에 ASP.NET Core 및 Azure를 사용 하 여 DevOps에 대 한 첫 번째 단계는 ASP.NET Core 앱을 배포 합니다.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/deploy-to-app-service
ms.openlocfilehash: df41f296e9c4e1eff6e31d45b29ec30ee1e20cf4
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080444"
---
# <a name="deploy-an-app-to-app-service"></a><span data-ttu-id="6806c-103">App Service에 앱 배포</span><span class="sxs-lookup"><span data-stu-id="6806c-103">Deploy an app to App Service</span></span>

<span data-ttu-id="6806c-104">[Azure App Service](/azure/app-service/) 는 Azure의 웹 호스팅 플랫폼입니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-104">[Azure App Service](/azure/app-service/) is Azure's web hosting platform.</span></span> <span data-ttu-id="6806c-105">수동으로 또는 자동화 된 프로세스를 통해 Azure App Service에 웹 앱 배포를 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-105">Deploying a web app to Azure App Service can be done manually or by an automated process.</span></span> <span data-ttu-id="6806c-106">이 가이드의이 섹션에서는 명령줄을 사용 하 여 스크립트 하거나 수동으로 트리거할 수 있습니다 또는 Visual Studio를 사용 하 여 수동으로 트리거되는 배포 방법을 설명 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-106">This section of the guide discusses deployment methods that can be triggered manually or by script using the command line, or triggered manually using Visual Studio.</span></span>

<span data-ttu-id="6806c-107">이 섹션에서는 다음 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-107">In this section, you'll accomplish the following tasks:</span></span>

* <span data-ttu-id="6806c-108">다운로드 하 고 샘플 앱을 빌드하십시오.</span><span class="sxs-lookup"><span data-stu-id="6806c-108">Download and build the sample app.</span></span>
* <span data-ttu-id="6806c-109">Azure Cloud Shell을 사용 하 여 Azure App Service 웹 앱을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-109">Create an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="6806c-110">Git를 사용 하 여 Azure에 샘플 앱을 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-110">Deploy the sample app to Azure using Git.</span></span>
* <span data-ttu-id="6806c-111">Visual Studio를 사용 하 여 앱에는 변경 내용을 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-111">Deploy a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="6806c-112">웹 앱에 스테이징 슬롯을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-112">Add a staging slot to the web app.</span></span>
* <span data-ttu-id="6806c-113">스테이징 슬롯에 업데이트를 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-113">Deploy an update to the staging slot.</span></span>
* <span data-ttu-id="6806c-114">스테이징 및 프로덕션 슬롯을 교환 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-114">Swap the staging and production slots.</span></span>

## <a name="download-and-test-the-app"></a><span data-ttu-id="6806c-115">다운로드 하 여 앱 테스트</span><span class="sxs-lookup"><span data-stu-id="6806c-115">Download and test the app</span></span>

<span data-ttu-id="6806c-116">이 가이드에 사용 되는 앱은 ASP.NET Core 앱을 미리 빌드된 [간단한 피드 판독기](https://github.com/Azure-Samples/simple-feed-reader/)합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-116">The app used in this guide is a pre-built ASP.NET Core app, [Simple Feed Reader](https://github.com/Azure-Samples/simple-feed-reader/).</span></span> <span data-ttu-id="6806c-117">Razor 페이지 앱을 사용 하는 것은 `Microsoft.SyndicationFeed.ReaderWriter` API RSS/Atom 피드를 검색 하 고 목록에서 뉴스 항목을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-117">It's a Razor Pages app that uses the `Microsoft.SyndicationFeed.ReaderWriter` API to retrieve an RSS/Atom feed and display the news items in a list.</span></span>

<span data-ttu-id="6806c-118">코드 검토를 자유롭게 이지만이 앱에 대 한 특별 임을 이해 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-118">Feel free to review the code, but it's important to understand that there's nothing special about this app.</span></span> <span data-ttu-id="6806c-119">설명을 위해 간단한 ASP.NET Core 앱만 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-119">It's just a simple ASP.NET Core app for illustrative purposes.</span></span>

<span data-ttu-id="6806c-120">명령 셸에서 코드를 다운로드할 프로젝트를 빌드하고 다음과 같이 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-120">From a command shell, download the code, build the project, and run it as follows.</span></span>

> <span data-ttu-id="6806c-121">*참고: Linux/macos 사용자는 경로를 적절 하 게 변경 해야 합니다. 예를 들어 백슬래시`/`() 대신`\`슬래시 ()를 사용 해야 합니다.*</span><span class="sxs-lookup"><span data-stu-id="6806c-121">*Note: Linux/macOS users should make appropriate changes for paths, e.g., using forward slash (`/`) rather than back slash (`\`).*</span></span>

1. <span data-ttu-id="6806c-122">로컬 컴퓨터의 폴더로 코드를 복제 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-122">Clone the code to a folder on your local machine.</span></span>

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. <span data-ttu-id="6806c-123">작업 폴더를 변경 합니다 *단순 피드 판독기* 만들어진 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-123">Change your working folder to the *simple-feed-reader* folder that was created.</span></span>

    ```console
    cd .\simple-feed-reader\SimpleFeedReader
    ```

3. <span data-ttu-id="6806c-124">패키지를 복원 하 고 솔루션을 빌드하십시오.</span><span class="sxs-lookup"><span data-stu-id="6806c-124">Restore the packages, and build the solution.</span></span>

    ```dotnetcli
    dotnet build
    ```

4. <span data-ttu-id="6806c-125">앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-125">Run the app.</span></span>

    ```dotnetcli
    dotnet run
    ```

    ![Dotnet run 명령에 성공](./media/deploying-to-app-service/dotnet-run.png)

5. <span data-ttu-id="6806c-127">브라우저를 열고 이동할 `http://localhost:5000`합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-127">Open a browser and navigate to `http://localhost:5000`.</span></span> <span data-ttu-id="6806c-128">앱 입력 하거나 배포 피드 URL을 붙여 넣을 수 있으며 뉴스 항목 목록을 봅니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-128">The app allows you to type or paste a syndication feed URL and view a list of news items.</span></span>

     ![RSS 피드의 내용을 표시 하는 앱](./media/deploying-to-app-service/app-in-browser.png)

6. <span data-ttu-id="6806c-130">앱이 올바르게 작동 만족 했으면, 눌러 종료할 **Ctrl**+**C** 명령 셸에서 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-130">Once you're satisfied the app is working correctly, shut it down by pressing **Ctrl**+**C** in the command shell.</span></span>

## <a name="create-the-azure-app-service-web-app"></a><span data-ttu-id="6806c-131">Azure App Service 웹 앱 만들기</span><span class="sxs-lookup"><span data-stu-id="6806c-131">Create the Azure App Service Web App</span></span>

<span data-ttu-id="6806c-132">앱을 배포 하려면 App Service 만들기 해야 [웹 앱](/azure/app-service/app-service-web-overview)합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-132">To deploy the app, you'll need to create an App Service [Web App](/azure/app-service/app-service-web-overview).</span></span> <span data-ttu-id="6806c-133">웹 앱을 만든 후 Git를 사용 하 여 로컬 컴퓨터에서를 배포할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-133">After creation of the Web App, you'll deploy to it from your local machine using Git.</span></span>

1. <span data-ttu-id="6806c-134">에 로그인 합니다 [Azure Cloud Shell](https://shell.azure.com/bash)합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-134">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash).</span></span> <span data-ttu-id="6806c-135">참고: 처음으로 로그인 하면 Cloud Shell 구성 파일에 대 한 저장소 계정을 만들라는 메시지가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-135">Note: When you sign in for the first time, Cloud Shell prompts to create a storage account for configuration files.</span></span> <span data-ttu-id="6806c-136">기본값을 사용 하거나 고유 이름을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-136">Accept the defaults or provide a unique name.</span></span>

2. <span data-ttu-id="6806c-137">다음 단계에서 Cloud Shell을 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-137">Use the Cloud Shell for the following steps.</span></span>

    <span data-ttu-id="6806c-138">a.</span><span class="sxs-lookup"><span data-stu-id="6806c-138">a.</span></span> <span data-ttu-id="6806c-139">웹 앱의 이름을 저장할 변수를 선언 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-139">Declare a variable to store your web app's name.</span></span> <span data-ttu-id="6806c-140">이름을 기본 URL에 사용할 고유 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-140">The name must be unique to be used in the default URL.</span></span> <span data-ttu-id="6806c-141">사용 하는 `$RANDOM` Bash 함수 이름을 생성 하는 고유성을 보장 및 형식으로 결과 `webappname99999`합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-141">Using the `$RANDOM` Bash function to construct the name guarantees uniqueness and results in the format `webappname99999`.</span></span>

    ```console
    webappname=mywebapp$RANDOM
    ```

    <span data-ttu-id="6806c-142">b.</span><span class="sxs-lookup"><span data-stu-id="6806c-142">b.</span></span> <span data-ttu-id="6806c-143">리소스 그룹을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-143">Create a resource group.</span></span> <span data-ttu-id="6806c-144">리소스 그룹을 그룹으로 관리 하는 Azure 리소스를 집계 하는 수단을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-144">Resource groups provide a means to aggregate Azure resources to be managed as a group.</span></span>

    ```azure-cli
    az group create --location centralus --name AzureTutorial
    ```

    <span data-ttu-id="6806c-145">합니다 `az` 명령을 호출 합니다 [Azure CLI](/cli/azure/)합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-145">The `az` command invokes the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="6806c-146">CLI를 로컬로 실행할 수 있지만 시간 및 구성 저장 사용 하 여 Cloud Shell에서.</span><span class="sxs-lookup"><span data-stu-id="6806c-146">The CLI can be run locally, but using it in the Cloud Shell saves time and configuration.</span></span>

    <span data-ttu-id="6806c-147">다.</span><span class="sxs-lookup"><span data-stu-id="6806c-147">c.</span></span> <span data-ttu-id="6806c-148">S1 계층에서 App Service 계획을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-148">Create an App Service plan in the S1 tier.</span></span> <span data-ttu-id="6806c-149">App Service 계획은 동일한 가격 책정 계층을 공유 하는 웹 앱의 그룹입니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-149">An App Service plan is a grouping of web apps that share the same pricing tier.</span></span> <span data-ttu-id="6806c-150">S1 계층을 무료로 사용할 수 있는 없지만 스테이징 슬롯 기능 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-150">The S1 tier isn't free, but it's required for the staging slots feature.</span></span>

    ```azure-cli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    <span data-ttu-id="6806c-151">d.</span><span class="sxs-lookup"><span data-stu-id="6806c-151">d.</span></span> <span data-ttu-id="6806c-152">App Service 계획을 사용 하 여 동일한 리소스 그룹에서 웹 앱 리소스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-152">Create the web app resource using the App Service plan in the same resource group.</span></span>

    ```azure-cli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    <span data-ttu-id="6806c-153">e.</span><span class="sxs-lookup"><span data-stu-id="6806c-153">e.</span></span> <span data-ttu-id="6806c-154">배포 자격 증명을 설정 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-154">Set the deployment credentials.</span></span> <span data-ttu-id="6806c-155">이러한 배포 자격 증명은 구독에서 모든 웹 앱에 적용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-155">These deployment credentials apply to all the web apps in your subscription.</span></span> <span data-ttu-id="6806c-156">사용자 이름에 특수 문자를 사용 하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="6806c-156">Don't use special characters in the user name.</span></span>

    ```azure-cli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    <span data-ttu-id="6806c-157">f.</span><span class="sxs-lookup"><span data-stu-id="6806c-157">f.</span></span> <span data-ttu-id="6806c-158">로컬 Git에서 표시 하는 배포를 허용 하도록 웹 앱을 구성 합니다 *Git 배포 URL*합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-158">Configure the web app to accept deployments from local Git and display the *Git deployment URL*.</span></span> <span data-ttu-id="6806c-159">**나중에 참조에 대 한 url이**합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-159">**Note this URL for reference later**.</span></span>

    ```azure-cli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    <span data-ttu-id="6806c-160">g.</span><span class="sxs-lookup"><span data-stu-id="6806c-160">g.</span></span> <span data-ttu-id="6806c-161">표시 된 *웹 앱 URL*합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-161">Display the *web app URL*.</span></span> <span data-ttu-id="6806c-162">빈 웹 앱을 보려면이 URL로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-162">Browse to this URL to see the blank web app.</span></span> <span data-ttu-id="6806c-163">**나중에 참조에 대 한 url이**합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-163">**Note this URL for reference later**.</span></span>

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. <span data-ttu-id="6806c-164">명령 셸에서 사용 하 여 로컬 컴퓨터에 웹 앱의 프로젝트 폴더를 이동 (예를 들어 `.\simple-feed-reader\SimpleFeedReader`).</span><span class="sxs-lookup"><span data-stu-id="6806c-164">Using a command shell on your local machine, navigate to the web app's project folder (for example, `.\simple-feed-reader\SimpleFeedReader`).</span></span> <span data-ttu-id="6806c-165">Git 배포 URL을 푸시 하도록 설정 하려면 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-165">Execute the following commands to set up Git to push to the deployment URL:</span></span>

    <span data-ttu-id="6806c-166">a.</span><span class="sxs-lookup"><span data-stu-id="6806c-166">a.</span></span> <span data-ttu-id="6806c-167">로컬 리포지토리에 원격 URL을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-167">Add the remote URL to the local repository.</span></span>

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    <span data-ttu-id="6806c-168">b.</span><span class="sxs-lookup"><span data-stu-id="6806c-168">b.</span></span> <span data-ttu-id="6806c-169">로컬 푸시 *마스터* 분기를 *azure prod* 원격의 *마스터* 분기 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-169">Push the local *master* branch to the *azure-prod* remote's *master* branch.</span></span>

    ```console
    git push azure-prod master
    ```

    <span data-ttu-id="6806c-170">이전에 만든 배포 자격 증명에 대 한 라는 메시지가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-170">You'll be prompted for the deployment credentials you created earlier.</span></span> <span data-ttu-id="6806c-171">명령 셸에서 출력을 관찰 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-171">Observe the output in the command shell.</span></span> <span data-ttu-id="6806c-172">Azure는 원격으로 ASP.NET Core 앱을 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-172">Azure builds the ASP.NET Core app remotely.</span></span>

4. <span data-ttu-id="6806c-173">브라우저에서로 이동 합니다 *웹 앱 URL* 앱이 빌드 및 배포를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-173">In a browser, navigate to the *Web app URL* and note the app has been built and deployed.</span></span> <span data-ttu-id="6806c-174">추가 변경 내용을 사용 하 여 로컬 Git 리포지토리에 커밋할 수 `git commit`입니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-174">Additional changes can be committed to the local Git repository with `git commit`.</span></span> <span data-ttu-id="6806c-175">앞으로 이러한 변경 내용을 Azure로 푸시할 `git push` 명령입니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-175">These changes are pushed to Azure with the preceding `git push` command.</span></span>

## <a name="deployment-with-visual-studio"></a><span data-ttu-id="6806c-176">Visual Studio 사용 하 여 배포</span><span class="sxs-lookup"><span data-stu-id="6806c-176">Deployment with Visual Studio</span></span>

> <span data-ttu-id="6806c-177">*참고: 이 섹션은 Windows에만 적용 됩니다. Linux 및 macOS 사용자는 2 단계 아래에 설명 된 변경 내용을 확인 해야 합니다. 파일을 저장 하 고 사용 하 여 로컬 리포지토리에 변경 내용을 커밋하기 `git commit`합니다. 마지막으로 사용 하 여 변경 내용을 푸시 `git push`첫 번째 섹션 에서처럼 합니다.*</span><span class="sxs-lookup"><span data-stu-id="6806c-177">*Note: This section applies to Windows only. Linux and macOS users should make the change described in step 2 below. Save the file, and commit the change to the local repository with `git commit`. Finally, push the change with `git push`, as in the first section.*</span></span>

<span data-ttu-id="6806c-178">앱 명령 셸에서 이미 배포 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-178">The app has already been deployed from the command shell.</span></span> <span data-ttu-id="6806c-179">앱에 업데이트를 배포 하려면 Visual Studio의 통합된 도구를 사용해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-179">Let's use Visual Studio's integrated tools to deploy an update to the app.</span></span> <span data-ttu-id="6806c-180">배후에서 Visual Studio 도구, 명령줄 같지만 Visual Studio의 친숙 한 UI 내에서 동일한 작업을 수행 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-180">Behind the scenes, Visual Studio accomplishes the same thing as the command line tooling, but within Visual Studio's familiar UI.</span></span>

1. <span data-ttu-id="6806c-181">오픈 *SimpleFeedReader.sln* Visual Studio에서.</span><span class="sxs-lookup"><span data-stu-id="6806c-181">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
2. <span data-ttu-id="6806c-182">솔루션 탐색기에서 엽니다 *Pages\Index.cshtml*합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-182">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="6806c-183">변경 `<h2>Simple Feed Reader</h2>` 에 `<h2>Simple Feed Reader - V2</h2>`입니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-183">Change `<h2>Simple Feed Reader</h2>` to `<h2>Simple Feed Reader - V2</h2>`.</span></span>
3. <span data-ttu-id="6806c-184">키를 눌러 **Ctrl**+**Shift**+**B** 앱을 빌드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-184">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
4. <span data-ttu-id="6806c-185">솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 클릭 **게시**합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-185">In Solution Explorer, right-click on the project and click **Publish**.</span></span>

    ![마우스 오른쪽 단추로 클릭, 게시를 보여 주는 스크린샷](./media/deploying-to-app-service/publish.png)
5. <span data-ttu-id="6806c-187">Visual Studio에서 새 App Service 리소스를 만들 수 있지만이 업데이트는 기존 배포를 통해 게시 될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-187">Visual Studio can create a new App Service resource, but this update will be published over the existing deployment.</span></span> <span data-ttu-id="6806c-188">에 **게시 대상 선택** 대화 상자에서 **App Service** 왼쪽에 있는 목록에서 선택한 후 **기존 선택**합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-188">In the **Pick a publish target** dialog, select **App Service** from the list on the left, and then select **Select Existing**.</span></span> <span data-ttu-id="6806c-189">**게시**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-189">Click **Publish**.</span></span>
6. <span data-ttu-id="6806c-190">에 **App Service** 대화 상자에서 Microsoft 또는 조직 계정을 Azure 구독을 만드는 데 오른쪽 위에 표시 되는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-190">In the **App Service** dialog, confirm that the Microsoft or Organizational account used to create your Azure subscription is displayed in the upper right.</span></span> <span data-ttu-id="6806c-191">없는 경우 드롭다운을 클릭 하 고 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-191">If it's not, click the drop-down and add it.</span></span>
7. <span data-ttu-id="6806c-192">확인 하는 올바른 Azure **구독** 을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-192">Confirm that the correct Azure **Subscription** is selected.</span></span> <span data-ttu-id="6806c-193">에 대 한 **뷰**를 선택 **리소스 그룹**합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-193">For **View**, select **Resource Group**.</span></span> <span data-ttu-id="6806c-194">확장 된 **AzureTutorial** 리소스 그룹 및 기존 웹 앱을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-194">Expand the **AzureTutorial** resource group and then select the existing web app.</span></span> <span data-ttu-id="6806c-195">**확인**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-195">Click **OK**.</span></span>

    ![App Service 게시 대화 상자 보여 주는 스크린샷](./media/deploying-to-app-service/publish-dialog.png)

<span data-ttu-id="6806c-197">Visual Studio 빌드하고 Azure에 앱을 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-197">Visual Studio builds and deploys the app to Azure.</span></span> <span data-ttu-id="6806c-198">웹 앱 URL로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-198">Browse to the web app URL.</span></span> <span data-ttu-id="6806c-199">확인 된 `<h2>` 요소를 수정 하는 라이브입니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-199">Validate that the `<h2>` element modification is live.</span></span>

![변경 된 제목이 있는 앱](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a><span data-ttu-id="6806c-201">배포 슬롯</span><span class="sxs-lookup"><span data-stu-id="6806c-201">Deployment slots</span></span>

<span data-ttu-id="6806c-202">배포 슬롯 프로덕션 환경에서 실행 되는 앱에 영향을 주지 않고 변경 내용 스테이징을 지원 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-202">Deployment slots support the staging of changes without impacting the app running in production.</span></span> <span data-ttu-id="6806c-203">준비 된 버전의 앱 품질 보증 팀에서 유효성이 검사 되 면 프로덕션 및 스테이징 슬롯을 교환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-203">Once the staged version of the app is validated by a quality assurance team, the production and staging slots can be swapped.</span></span> <span data-ttu-id="6806c-204">준비 단계에서 앱이 방식으로 프로덕션으로 승격 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-204">The app in staging is promoted to production in this manner.</span></span> <span data-ttu-id="6806c-205">스테이징 슬롯을 만들고, 일부 변경 내용을 배포 확인 후 프로덕션과 스테이징 슬롯을 교환 하는 다음 단계를 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-205">The following steps create a staging slot, deploy some changes to it, and swap the staging slot with production after verification.</span></span>

1. <span data-ttu-id="6806c-206">에 로그인 합니다 [Azure Cloud Shell](https://shell.azure.com/bash)아직 로그인 하지 않은 경우.</span><span class="sxs-lookup"><span data-stu-id="6806c-206">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash), if not already signed in.</span></span>
2. <span data-ttu-id="6806c-207">스테이징 슬롯을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-207">Create the staging slot.</span></span>

    <span data-ttu-id="6806c-208">a.</span><span class="sxs-lookup"><span data-stu-id="6806c-208">a.</span></span> <span data-ttu-id="6806c-209">이름의 배포 슬롯을 만듭니다 *준비*합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-209">Create a deployment slot with the name *staging*.</span></span>

    ```azure-cli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    <span data-ttu-id="6806c-210">b.</span><span class="sxs-lookup"><span data-stu-id="6806c-210">b.</span></span> <span data-ttu-id="6806c-211">로컬 Git 및 get에서 배포를 사용 하려면 스테이징 슬롯을 구성 합니다 **준비** 배포 URL입니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-211">Configure the staging slot to use deployment from local Git and get the **staging** deployment URL.</span></span> <span data-ttu-id="6806c-212">**나중에 참조에 대 한 url이**합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-212">**Note this URL for reference later**.</span></span>

    ```azure-cli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    <span data-ttu-id="6806c-213">다.</span><span class="sxs-lookup"><span data-stu-id="6806c-213">c.</span></span> <span data-ttu-id="6806c-214">스테이징 슬롯의 URL을 표시 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-214">Display the staging slot's URL.</span></span> <span data-ttu-id="6806c-215">빈 스테이징 슬롯을 확인 하려면 URL로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-215">Browse to the URL to see the empty staging slot.</span></span> <span data-ttu-id="6806c-216">**나중에 참조에 대 한 url이**합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-216">**Note this URL for reference later**.</span></span>

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. <span data-ttu-id="6806c-217">텍스트 편집기 또는 Visual Studio에서 수정할 *pages/Index.cshtml* 다시 되도록 합니다 `<h2>` 요소를 읽습니다 `<h2>Simple Feed Reader - V3</h2>` 파일을 저장 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-217">In a text editor or Visual Studio, modify *Pages/Index.cshtml* again so that the `<h2>` element reads `<h2>Simple Feed Reader - V3</h2>` and save the file.</span></span>

4. <span data-ttu-id="6806c-218">파일 중 하나를 사용 하 여 로컬 Git 리포지토리에 커밋 합니다 **변경 내용을** Visual studio의 페이지 *팀 탐색기* 탭을 입력 하 여 다음 셸을 사용 하 여 로컬 컴퓨터의 명령 또는:</span><span class="sxs-lookup"><span data-stu-id="6806c-218">Commit the file to the local Git repository, using either the **Changes** page in Visual Studio's *Team Explorer* tab, or by entering the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. <span data-ttu-id="6806c-219">로컬 컴퓨터의 명령 셸 사용, Git 원격으로 스테이징 배포 URL을 추가 하 고 커밋된 변경 내용을 푸시하십시오.</span><span class="sxs-lookup"><span data-stu-id="6806c-219">Using the local machine's command shell, add the staging deployment URL as a Git remote and push the committed changes:</span></span>

    <span data-ttu-id="6806c-220">a.</span><span class="sxs-lookup"><span data-stu-id="6806c-220">a.</span></span> <span data-ttu-id="6806c-221">로컬 Git 리포지토리를 스테이징에 대 한 원격 URL을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-221">Add the remote URL for staging to the local Git repository.</span></span>

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    <span data-ttu-id="6806c-222">b.</span><span class="sxs-lookup"><span data-stu-id="6806c-222">b.</span></span> <span data-ttu-id="6806c-223">로컬 푸시 *마스터* 분기를 *azure 스테이징* 원격의 *마스터* 분기 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-223">Push the local *master* branch to the *azure-staging* remote's *master* branch.</span></span>

    ```console
    git push azure-staging master
    ```

    <span data-ttu-id="6806c-224">Azure를 빌드하고 앱을 배포 하는 동안 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-224">Wait while Azure builds and deploys the app.</span></span>

6. <span data-ttu-id="6806c-225">V3 스테이징 슬롯에 배포한 있는지를 확인 하려면 두 개의 브라우저 창을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-225">To verify that V3 has been deployed to the staging slot, open two browser windows.</span></span> <span data-ttu-id="6806c-226">하나의 창에서 원본 웹 앱 URL로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-226">In one window, navigate to the original web app URL.</span></span> <span data-ttu-id="6806c-227">다른 창에서 스테이징 웹 앱 URL로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-227">In the other window, navigate to the staging web app URL.</span></span> <span data-ttu-id="6806c-228">프로덕션 URL은 앱의 V2에 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-228">The production URL serves V2 of the app.</span></span> <span data-ttu-id="6806c-229">스테이징 URL에는 앱의 V3 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-229">The staging URL serves V3 of the app.</span></span>

    ![브라우저 창을 비교 스크린 샷](./media/deploying-to-app-service/ready-to-swap.png)

7. <span data-ttu-id="6806c-231">Cloud Shell에서 확인/준비 접속 스테이징 슬롯을 프로덕션으로 교환 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-231">In the Cloud Shell, swap the verified/warmed-up staging slot into production.</span></span>

    ```azure-cli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. <span data-ttu-id="6806c-232">두 개의 브라우저 창을 새로 고쳐서 교환 발생 했음을 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-232">Verify that the swap occurred by refreshing the two browser windows.</span></span>

    ![교환 후 브라우저 창을 비교](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a><span data-ttu-id="6806c-234">요약</span><span class="sxs-lookup"><span data-stu-id="6806c-234">Summary</span></span>

<span data-ttu-id="6806c-235">이 섹션에서는 다음 작업을 완료 했습니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-235">In this section, the following tasks were completed:</span></span>

* <span data-ttu-id="6806c-236">다운로드 하 고 샘플 앱을 작성 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-236">Downloaded and built the sample app.</span></span>
* <span data-ttu-id="6806c-237">Azure Cloud Shell을 사용 하 여 Azure App Service 웹 앱을 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-237">Created an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="6806c-238">Git를 사용 하 여 Azure에 샘플 앱을 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-238">Deployed the sample app to Azure using Git.</span></span>
* <span data-ttu-id="6806c-239">Visual Studio를 사용 하 여 앱에 변경 내용을 배포 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-239">Deployed a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="6806c-240">웹 앱에 스테이징 슬롯을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-240">Added a staging slot to the web app.</span></span>
* <span data-ttu-id="6806c-241">스테이징 슬롯에 대 한 업데이트를 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-241">Deployed an update to the staging slot.</span></span>
* <span data-ttu-id="6806c-242">스테이징 및 프로덕션 슬롯을 교환 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-242">Swapped the staging and production slots.</span></span>

<span data-ttu-id="6806c-243">다음 섹션에서는 Azure 파이프라인을 사용 하 여 DevOps 파이프라인을 빌드하는 방법에 알아봅니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-243">In the next section, you'll learn how to build a DevOps pipeline with Azure Pipelines.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="6806c-244">추가 참조 항목</span><span class="sxs-lookup"><span data-stu-id="6806c-244">Additional reading</span></span>

* [<span data-ttu-id="6806c-245">Web Apps 개요</span><span class="sxs-lookup"><span data-stu-id="6806c-245">Web Apps overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="6806c-246">Azure App Service에서.NET Core 및 SQL Database 웹 앱 빌드</span><span class="sxs-lookup"><span data-stu-id="6806c-246">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [<span data-ttu-id="6806c-247">Azure App Service에 대 한 배포 자격 증명을 구성 합니다.</span><span class="sxs-lookup"><span data-stu-id="6806c-247">Configure deployment credentials for Azure App Service</span></span>](/azure/app-service/app-service-deployment-credentials)
* [<span data-ttu-id="6806c-248">Azure App Service에서 스테이징 환경 설정</span><span class="sxs-lookup"><span data-stu-id="6806c-248">Set up staging environments in Azure App Service</span></span>](/azure/app-service/web-sites-staged-publishing)
