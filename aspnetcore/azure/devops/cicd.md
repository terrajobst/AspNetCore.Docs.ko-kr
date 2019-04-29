---
title: 지속적인 통합 및 배포-ASP.NET Core 및 Azure를 사용 하 여 DevOps
author: CamSoper
description: 지속적인 통합 및 ASP.NET Core 및 Azure를 사용 하 여 DevOps에 배포
ms.author: scaddie
ms.date: 10/24/2018
ms.custom: mvc, seodec18
uid: azure/devops/cicd
ms.openlocfilehash: 5fdf52235b49119503885f92c370dc588e809ffe
ms.sourcegitcommit: 8a84ce880b4c40d6694ba6423038f18fc2eb5746
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60165279"
---
# <a name="continuous-integration-and-deployment"></a><span data-ttu-id="6bcbc-103">지속적인 통합 및 배포</span><span class="sxs-lookup"><span data-stu-id="6bcbc-103">Continuous integration and deployment</span></span>

<span data-ttu-id="6bcbc-104">이전 장에서 간단한 피드 판독기 앱에 대 한 로컬 Git 리포지토리를 만들었습니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-104">In the previous chapter, you created a local Git repository for the Simple Feed Reader app.</span></span> <span data-ttu-id="6bcbc-105">이 챕터에서는 해당 코드는 GitHub 리포지토리를 게시 하 고 Azure 파이프라인을 사용 하 여 Azure DevOps 서비스 파이프라인을 생성 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-105">In this chapter, you'll publish that code to a GitHub repository and construct an Azure DevOps Services pipeline using Azure Pipelines.</span></span> <span data-ttu-id="6bcbc-106">파이프라인에는 연속 빌드 및 앱의 배포 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-106">The pipeline enables continuous builds and deployments of the app.</span></span> <span data-ttu-id="6bcbc-107">GitHub 리포지토리에 커밋 빌드 및 Azure 웹 앱의 스테이징 슬롯에 배포를 트리거합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-107">Any commit to the GitHub repository triggers a build and a deployment to the Azure Web App's staging slot.</span></span>

<span data-ttu-id="6bcbc-108">이 섹션에서는 다음 작업을 완료 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-108">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="6bcbc-109">앱의 코드를 GitHub에 게시</span><span class="sxs-lookup"><span data-stu-id="6bcbc-109">Publish the app's code to GitHub</span></span>
* <span data-ttu-id="6bcbc-110">로컬 Git 배포를 연결 끊기</span><span class="sxs-lookup"><span data-stu-id="6bcbc-110">Disconnect local Git deployment</span></span>
* <span data-ttu-id="6bcbc-111">Azure DevOps 조직 만들기</span><span class="sxs-lookup"><span data-stu-id="6bcbc-111">Create an Azure DevOps organization</span></span>
* <span data-ttu-id="6bcbc-112">Azure DevOps 서비스에서 팀 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="6bcbc-112">Create a team project in Azure DevOps Services</span></span>
* <span data-ttu-id="6bcbc-113">빌드 정의 만들기</span><span class="sxs-lookup"><span data-stu-id="6bcbc-113">Create a build definition</span></span>
* <span data-ttu-id="6bcbc-114">릴리스 파이프라인을 만들려면</span><span class="sxs-lookup"><span data-stu-id="6bcbc-114">Create a release pipeline</span></span>
* <span data-ttu-id="6bcbc-115">GitHub에 변경 내용 커밋 및 자동으로 Azure에 배포</span><span class="sxs-lookup"><span data-stu-id="6bcbc-115">Commit changes to GitHub and automatically deploy to Azure</span></span>
* <span data-ttu-id="6bcbc-116">Azure 파이프라인 파이프라인 검토</span><span class="sxs-lookup"><span data-stu-id="6bcbc-116">Examine the Azure Pipelines pipeline</span></span>

## <a name="publish-the-apps-code-to-github"></a><span data-ttu-id="6bcbc-117">앱의 코드를 GitHub에 게시</span><span class="sxs-lookup"><span data-stu-id="6bcbc-117">Publish the app's code to GitHub</span></span>

1. <span data-ttu-id="6bcbc-118">브라우저 창을 열고 이동할 `https://github.com`합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-118">Open a browser window, and navigate to `https://github.com`.</span></span>
1. <span data-ttu-id="6bcbc-119">클릭 합니다 **+** 드롭 다운 헤더에서 선택한 **새 리포지토리**:</span><span class="sxs-lookup"><span data-stu-id="6bcbc-119">Click the **+** drop-down in the header, and select **New repository**:</span></span>

    ![새 GitHub 리포지토리 옵션](media/cicd/github-new-repo.png)

1. <span data-ttu-id="6bcbc-121">계정을 선택 합니다 **소유자** 드롭다운 목록에서 enter *단순 피드 판독기* 에서 **리포지토리 이름** 텍스트 상자에 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-121">Select your account in the **Owner** drop-down, and enter *simple-feed-reader* in the **Repository name** textbox.</span></span>
1. <span data-ttu-id="6bcbc-122">클릭 합니다 **리포지토리 만들기** 단추입니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-122">Click the **Create repository** button.</span></span>
1. <span data-ttu-id="6bcbc-123">로컬 컴퓨터의 명령 셸을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-123">Open your local machine's command shell.</span></span> <span data-ttu-id="6bcbc-124">디렉터리를 이동 합니다 *단순 피드 판독기* Git 리포지토리에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-124">Navigate to the directory in which the *simple-feed-reader* Git repository is stored.</span></span>
1. <span data-ttu-id="6bcbc-125">기존 이름 바꾸기 *원점* 원격 *업스트림*합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-125">Rename the existing *origin* remote to *upstream*.</span></span> <span data-ttu-id="6bcbc-126">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-126">Execute the following command:</span></span>

    ```console
    git remote rename origin upstream
    ```

1. <span data-ttu-id="6bcbc-127">새 *원본* 원격 github 리포지토리의 사본을 가리키는 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-127">Add a new *origin* remote pointing to your copy of the repository on GitHub.</span></span> <span data-ttu-id="6bcbc-128">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-128">Execute the following command:</span></span>

    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```

1. <span data-ttu-id="6bcbc-129">새로 만든 GitHub 리포지토리에 로컬 Git 리포지토리를 게시 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-129">Publish your local Git repository to the newly created GitHub repository.</span></span> <span data-ttu-id="6bcbc-130">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-130">Execute the following command:</span></span>

    ```console
    git push -u origin master
    ```

1. <span data-ttu-id="6bcbc-131">브라우저 창을 열고 이동할 `https://github.com/<GitHub_username>/simple-feed-reader/`합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-131">Open a browser window, and navigate to `https://github.com/<GitHub_username>/simple-feed-reader/`.</span></span> <span data-ttu-id="6bcbc-132">GitHub 리포지토리에서 코드 표시 되는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-132">Validate that your code appears in the GitHub repository.</span></span>

## <a name="disconnect-local-git-deployment"></a><span data-ttu-id="6bcbc-133">로컬 Git 배포를 연결 끊기</span><span class="sxs-lookup"><span data-stu-id="6bcbc-133">Disconnect local Git deployment</span></span>

<span data-ttu-id="6bcbc-134">다음 단계를 사용 하 여 로컬 Git 배포를 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-134">Remove the local Git deployment with the following steps.</span></span> <span data-ttu-id="6bcbc-135">Azure 파이프라인 (Azure DevOps 서비스) 모두 대체 하 고 해당 기능을 보완 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-135">Azure Pipelines (an Azure DevOps service) both replaces and augments that functionality.</span></span>

1. <span data-ttu-id="6bcbc-136">열기는 [Azure portal](https://portal.azure.com/)로 이동 합니다 *준비 (mywebapp\<unique_number\>준비 /)* 웹 앱.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-136">Open the [Azure portal](https://portal.azure.com/), and navigate to the *staging (mywebapp\<unique_number\>/staging)* Web App.</span></span> <span data-ttu-id="6bcbc-137">입력 하 여 웹 앱을 신속 하 게 찾을 수 있습니다 *준비* 포털의 검색 상자에서:</span><span class="sxs-lookup"><span data-stu-id="6bcbc-137">The Web App can be quickly located by entering *staging* in the portal's search box:</span></span>

    ![스테이징 웹 앱 검색 용어](media/cicd/portal-search-box.png)

1. <span data-ttu-id="6bcbc-139">클릭 **배포 센터**합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-139">Click **Deployment Center**.</span></span> <span data-ttu-id="6bcbc-140">새 패널이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-140">A new panel appears.</span></span> <span data-ttu-id="6bcbc-141">클릭 **연결 끊기** 이전 장에서 추가 된 로컬 Git 소스 제어 구성 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-141">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="6bcbc-142">클릭 하 여 제거 작업을 확인 합니다 **예** 단추입니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-142">Confirm the removal operation by clicking the **Yes** button.</span></span>
1. <span data-ttu-id="6bcbc-143">로 이동 합니다 *mywebapp < unique_number >* App Service입니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-143">Navigate to the *mywebapp<unique_number>* App Service.</span></span> <span data-ttu-id="6bcbc-144">참고로, 포털의 검색 상자 빨리 App Service를 찾는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-144">As a reminder, the portal's search box can be used to quickly locate the App Service.</span></span>
1. <span data-ttu-id="6bcbc-145">클릭 **배포 센터**합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-145">Click **Deployment Center**.</span></span> <span data-ttu-id="6bcbc-146">새 패널이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-146">A new panel appears.</span></span> <span data-ttu-id="6bcbc-147">클릭 **연결 끊기** 이전 장에서 추가 된 로컬 Git 소스 제어 구성 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-147">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="6bcbc-148">클릭 하 여 제거 작업을 확인 합니다 **예** 단추입니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-148">Confirm the removal operation by clicking the **Yes** button.</span></span>

## <a name="create-an-azure-devops-organization"></a><span data-ttu-id="6bcbc-149">Azure DevOps 조직 만들기</span><span class="sxs-lookup"><span data-stu-id="6bcbc-149">Create an Azure DevOps organization</span></span>

1. <span data-ttu-id="6bcbc-150">브라우저를 열고로 이동 합니다 [Azure DevOps 조직 만들기 페이지](https://go.microsoft.com/fwlink/?LinkId=307137)합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-150">Open a browser, and navigate to the [Azure DevOps organization creation page](https://go.microsoft.com/fwlink/?LinkId=307137).</span></span>
1. <span data-ttu-id="6bcbc-151">에 고유한 이름을 입력 합니다 **기억 하기 쉬운 이름을 선택** Azure DevOps 조직에 액세스 하기 위한 url을 텍스트 상자에 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-151">Type a unique name into the **Pick a memorable name** textbox to form the URL for accessing your Azure DevOps organization.</span></span>
1. <span data-ttu-id="6bcbc-152">선택 된 **Git** 라디오 단추, 코드는 GitHub 리포지토리에 호스트 되는 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-152">Select the **Git** radio button, since the code is hosted in a GitHub repository.</span></span>
1. <span data-ttu-id="6bcbc-153">**계속** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-153">Click the **Continue** button.</span></span> <span data-ttu-id="6bcbc-154">짧은 대기 시간, 계정 및 팀 프로젝트, 후 이름이 *MyFirstProject*에 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-154">After a short wait, an account and a team project, named *MyFirstProject*, are created.</span></span>

    ![Azure DevOps 조직 만들기 페이지](media/cicd/vsts-account-creation.png)

1. <span data-ttu-id="6bcbc-156">Azure DevOps 조직 및 프로젝트에서 사용 하기 위해 준비 되었는지 나타내는 확인 전자 메일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-156">Open the confirmation email indicating that the Azure DevOps organization and project are ready for use.</span></span> <span data-ttu-id="6bcbc-157">클릭 합니다 **프로젝트를 시작** 단추:</span><span class="sxs-lookup"><span data-stu-id="6bcbc-157">Click the **Start your project** button:</span></span>

    ![시작 프로젝트 단추](media/cicd/vsts-start-project.png)

1. <span data-ttu-id="6bcbc-159">브라우저를 엽니다  *\<account_name\>. visualstudio.com*합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-159">A browser opens to *\<account_name\>.visualstudio.com*.</span></span> <span data-ttu-id="6bcbc-160">클릭 합니다 *MyFirstProject* 프로젝트의 DevOps 파이프라인을 구성 하려면 먼저 연결 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-160">Click the *MyFirstProject* link to begin configuring the project's DevOps pipeline.</span></span>

## <a name="configure-the-azure-pipelines-pipeline"></a><span data-ttu-id="6bcbc-161">Azure 파이프라인 파이프라인 구성</span><span class="sxs-lookup"><span data-stu-id="6bcbc-161">Configure the Azure Pipelines pipeline</span></span>

<span data-ttu-id="6bcbc-162">완료 하려면 다음 세 가지 단계가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-162">There are three distinct steps to complete.</span></span> <span data-ttu-id="6bcbc-163">Operational DevOps 파이프라인의 다음 세 가지 섹션에서는 결과의 단계를 완료 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-163">Completing the steps in the following three sections results in an operational DevOps pipeline.</span></span>

### <a name="grant-azure-devops-access-to-the-github-repository"></a><span data-ttu-id="6bcbc-164">GitHub 리포지토리에 대 한 Grant Azure DevOps 액세스</span><span class="sxs-lookup"><span data-stu-id="6bcbc-164">Grant Azure DevOps access to the GitHub repository</span></span>

1. <span data-ttu-id="6bcbc-165">확장 된 **또는 외부 리포지토리에서 코드 빌드** accordion 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-165">Expand the **or build code from an external repository** accordion.</span></span> <span data-ttu-id="6bcbc-166">클릭 합니다 **설치 빌드** 단추:</span><span class="sxs-lookup"><span data-stu-id="6bcbc-166">Click the **Setup Build** button:</span></span>

    ![빌드 단추를 설정 합니다.](media/cicd/vsts-setup-build.png)

1. <span data-ttu-id="6bcbc-168">선택 된 **GitHub** 에서 옵션을 **원본 선택** 섹션:</span><span class="sxs-lookup"><span data-stu-id="6bcbc-168">Select the **GitHub** option from the **Select a source** section:</span></span>

    ![소스-GitHub를 선택 합니다.](media/cicd/vsts-select-source.png)

1. <span data-ttu-id="6bcbc-170">Azure DevOps GitHub 리포지토리에 액세스 하기 전에 권한 부여 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-170">Authorization is required before Azure DevOps can access your GitHub repository.</span></span> <span data-ttu-id="6bcbc-171">입력 *< GitHub_username > GitHub 연결* 에 **연결 이름** 텍스트 상자에 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-171">Enter *<GitHub_username> GitHub connection* in the **Connection name** textbox.</span></span> <span data-ttu-id="6bcbc-172">예를 들어 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-172">For example:</span></span>

    ![GitHub 연결 이름](media/cicd/vsts-repo-authz.png)

1. <span data-ttu-id="6bcbc-174">GitHub 계정에 다단계 인증을 사용 하는 경우 개인용 액세스 토큰은 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-174">If two-factor authentication is enabled on your GitHub account, a personal access token is required.</span></span> <span data-ttu-id="6bcbc-175">이 경우 클릭 합니다 **GitHub 개인용 액세스 토큰을 사용 하 여 권한 부여** 링크 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-175">In that case, click the **Authorize with a GitHub personal access token** link.</span></span> <span data-ttu-id="6bcbc-176">참조 된 [공식 GitHub 개인용 액세스 토큰 만들기 지침](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) 도움말에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-176">See the [official GitHub personal access token creation instructions](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) for help.</span></span> <span data-ttu-id="6bcbc-177">만 *리포지토리* 사용 권한 범위는 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-177">Only the *repo* scope of permissions is needed.</span></span> <span data-ttu-id="6bcbc-178">그렇지 않은 경우 클릭 합니다 **OAuth를 사용 하 여 권한 부여** 단추입니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-178">Otherwise, click the **Authorize using OAuth** button.</span></span>
1. <span data-ttu-id="6bcbc-179">메시지가 표시 되 면 GitHub 계정에 로그인 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-179">When prompted, sign in to your GitHub account.</span></span> <span data-ttu-id="6bcbc-180">Azure DevOps 조직에 대 한 액세스 권한을 부여 하려면 권한 부여를 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-180">Then select Authorize to grant access to your Azure DevOps organization.</span></span> <span data-ttu-id="6bcbc-181">성공 하면 새 서비스 끝점 생성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-181">If successful, a new service endpoint is created.</span></span>
1. <span data-ttu-id="6bcbc-182">그런 다음 줄임표 단추를 클릭 합니다 **리포지토리** 단추입니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-182">Click the ellipsis button next to the **Repository** button.</span></span> <span data-ttu-id="6bcbc-183">선택 된 *< GitHub_username > / 간단한 피드 판독기* 리포지토리 목록에서.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-183">Select the *<GitHub_username>/simple-feed-reader* repository from the list.</span></span> <span data-ttu-id="6bcbc-184">클릭 합니다 **선택** 단추입니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-184">Click the **Select** button.</span></span>
1. <span data-ttu-id="6bcbc-185">선택 된 *마스터* 에서 분기를 **수동 및 예약 된 빌드의 기본 분기** 드롭 다운 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-185">Select the *master* branch from the **Default branch for manual and scheduled builds** drop-down.</span></span> <span data-ttu-id="6bcbc-186">**계속** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-186">Click the **Continue** button.</span></span> <span data-ttu-id="6bcbc-187">템플릿 선택 페이지가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-187">The template selection page appears.</span></span>

### <a name="create-the-build-definition"></a><span data-ttu-id="6bcbc-188">빌드 정의 만들기</span><span class="sxs-lookup"><span data-stu-id="6bcbc-188">Create the build definition</span></span>

1. <span data-ttu-id="6bcbc-189">템플릿 선택 페이지에서 입력 *ASP.NET Core* 검색 상자에서:</span><span class="sxs-lookup"><span data-stu-id="6bcbc-189">From the template selection page, enter *ASP.NET Core* in the search box:</span></span>

    ![ASP.NET Core 템플릿 페이지의 검색 기능](media/cicd/vsts-template-selection.png)

1. <span data-ttu-id="6bcbc-191">템플릿 검색 결과가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-191">The template search results appear.</span></span> <span data-ttu-id="6bcbc-192">마우스로 합니다 **ASP.NET Core** 템플릿과 클릭 합니다 **적용** 단추입니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-192">Hover over the **ASP.NET Core** template, and click the **Apply** button.</span></span>
1. <span data-ttu-id="6bcbc-193">합니다 **작업** 빌드 정의의 탭이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-193">The **Tasks** tab of the build definition appears.</span></span> <span data-ttu-id="6bcbc-194">**트리거** 탭을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-194">Click the **Triggers** tab.</span></span>
1. <span data-ttu-id="6bcbc-195">확인 합니다 **연속 통합을 사용 하도록 설정** 상자입니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-195">Check the **Enable continuous integration** box.</span></span> <span data-ttu-id="6bcbc-196">아래는 **분기 필터** 섹션에서 확인 합니다 **형식** 드롭다운 목록으로 설정 됩니다 *포함*합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-196">Under the **Branch filters** section, confirm that the **Type** drop-down is set to *Include*.</span></span> <span data-ttu-id="6bcbc-197">설정 된 **분기 사양** 드롭다운을 *마스터*합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-197">Set the **Branch specification** drop-down to *master*.</span></span>

    ![연속 통합 설정 사용](media/cicd/vsts-enable-ci.png)

    <span data-ttu-id="6bcbc-199">이러한 설정으로 인해 변경 내용 푸시 될 때 트리거 빌드 합니다 *마스터* GitHub 리포지토리의 분기입니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-199">These settings cause a build to trigger when any change is pushed to the *master* branch of the GitHub repository.</span></span> <span data-ttu-id="6bcbc-200">연속 통합에서 테스트 되는 [GitHub에 변경 내용 커밋 및 자동으로 Azure에 배포](#commit-changes-to-github-and-automatically-deploy-to-azure) 섹션입니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-200">Continuous integration is tested in the [Commit changes to GitHub and automatically deploy to Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) section.</span></span>

1. <span data-ttu-id="6bcbc-201">클릭 합니다 **저장 및 큐에 넣기** 단추를 선택한 합니다 **저장** 옵션:</span><span class="sxs-lookup"><span data-stu-id="6bcbc-201">Click the **Save & queue** button, and select the **Save** option:</span></span>

    ![저장 단추](media/cicd/vsts-save-build.png)

1. <span data-ttu-id="6bcbc-203">다음 모달 대화 상자가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-203">The following modal dialog appears:</span></span>

    ![저장 빌드 정의-모달 대화 상자](media/cicd/vsts-save-modal.png)

    <span data-ttu-id="6bcbc-205">기본 폴더를 사용 하 여 *\\* 를 클릭 합니다 **저장** 단추입니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-205">Use the default folder of *\\*, and click the **Save** button.</span></span>

### <a name="create-the-release-pipeline"></a><span data-ttu-id="6bcbc-206">릴리스 파이프라인을 만듭니다</span><span class="sxs-lookup"><span data-stu-id="6bcbc-206">Create the release pipeline</span></span>

1. <span data-ttu-id="6bcbc-207">클릭 합니다 **릴리스** 팀 프로젝트의 탭 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-207">Click the **Releases** tab of your team project.</span></span> <span data-ttu-id="6bcbc-208">클릭 합니다 **새 파이프라인** 단추입니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-208">Click the **New pipeline** button.</span></span>

    ![릴리스 탭-새 정의 단추](media/cicd/vsts-new-release-definition.png)

    <span data-ttu-id="6bcbc-210">템플릿 선택 창이 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-210">The template selection pane appears.</span></span>

1. <span data-ttu-id="6bcbc-211">템플릿 선택 페이지에서 입력 *App Service* 검색 상자에서:</span><span class="sxs-lookup"><span data-stu-id="6bcbc-211">From the template selection page, enter *App Service* in the search box:</span></span>

    ![릴리스 파이프라인 템플릿 검색 상자](media/cicd/vsts-release-template-search.png)

1. <span data-ttu-id="6bcbc-213">템플릿 검색 결과가 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-213">The template search results appear.</span></span> <span data-ttu-id="6bcbc-214">마우스로 **Azure App Service 배포 슬롯** 템플릿과 클릭 합니다 **적용** 단추입니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-214">Hover over the **Azure App Service Deployment with Slot** template, and click the **Apply** button.</span></span> <span data-ttu-id="6bcbc-215">합니다 **파이프라인** 릴리스 파이프라인의 탭이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-215">The **Pipeline** tab of the release pipeline appears.</span></span>

    ![릴리스 파이프라인 파이프라인 탭](media/cicd/vsts-release-definition-pipeline.png)

1. <span data-ttu-id="6bcbc-217">클릭 합니다 **추가** 단추를 **아티팩트** 상자입니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-217">Click the **Add** button in the **Artifacts** box.</span></span> <span data-ttu-id="6bcbc-218">합니다 **아티팩트 추가** 패널이 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-218">The **Add artifact** panel appears:</span></span>

    ![릴리스 파이프라인-아티팩트 패널 추가](media/cicd/vsts-release-add-artifact.png)

1. <span data-ttu-id="6bcbc-220">선택 합니다 **빌드** 에서 타일을 **원본 유형** 섹션입니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-220">Select the **Build** tile from the **Source type** section.</span></span> <span data-ttu-id="6bcbc-221">빌드 정의에 릴리스 파이프라인을 연결 하는이 형식을 허용 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-221">This type allows for the linking of the release pipeline to the build definition.</span></span>
1. <span data-ttu-id="6bcbc-222">선택 *MyFirstProject* 에서 합니다 **프로젝트** 드롭 다운 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-222">Select *MyFirstProject* from the **Project** drop-down.</span></span>
1. <span data-ttu-id="6bcbc-223">빌드 정의 이름을 선택 *MyFirstProject ASP.NET Core-CI*에서 **원본 (빌드 정의)** 드롭 다운 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-223">Select the build definition name, *MyFirstProject-ASP.NET Core-CI*, from the **Source (Build definition)** drop-down.</span></span>
1. <span data-ttu-id="6bcbc-224">선택 *최신* 에서 합니다 **기본 버전** 드롭 다운 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-224">Select *Latest* from the **Default version** drop-down.</span></span> <span data-ttu-id="6bcbc-225">이 옵션에는 빌드 정의의 가장 최근 실행과에서 생성 된 아티팩트를 작성 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-225">This option builds the artifacts produced by the latest run of the build definition.</span></span>
1. <span data-ttu-id="6bcbc-226">에 있는 텍스트 대체를 **소스 별칭** textbox *삭제*.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-226">Replace the text in the **Source alias** textbox with *Drop*.</span></span>
1. <span data-ttu-id="6bcbc-227">**추가** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-227">Click the **Add** button.</span></span> <span data-ttu-id="6bcbc-228">합니다 **아티팩트** 섹션 변경 내용을 표시 하도록 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-228">The **Artifacts** section updates to display the changes.</span></span>
1. <span data-ttu-id="6bcbc-229">연속 배포를 사용 하도록 설정 하려면 번개 모양 아이콘을 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-229">Click the lightning bolt icon to enable continuous deployments:</span></span>

    ![릴리스 파이프라인 아티팩트-번개 모양 아이콘](media/cicd/vsts-artifacts-lightning-bolt.png)

    <span data-ttu-id="6bcbc-231">이 옵션을 사용 하면 배포에는 새 빌드를 사용할 때마다 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-231">With this option enabled, a deployment occurs each time a new build is available.</span></span>
1. <span data-ttu-id="6bcbc-232">A **연속 배포 트리거** 패널 오른쪽에 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-232">A **Continuous deployment trigger** panel appears to the right.</span></span> <span data-ttu-id="6bcbc-233">기능을 사용 하도록 설정/해제 단추를 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-233">Click the toggle button to enable the feature.</span></span> <span data-ttu-id="6bcbc-234">사용 하도록 설정 하려면 필요는 없습니다 합니다 **끌어오기 요청 트리거**합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-234">It isn't necessary to enable the **Pull request trigger**.</span></span>
1. <span data-ttu-id="6bcbc-235">클릭 합니다 **추가** 드롭 다운에서의 **빌드 분기 필터** 섹션입니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-235">Click the **Add** drop-down in the **Build branch filters** section.</span></span> <span data-ttu-id="6bcbc-236">선택 된 **빌드 정의 기본 분기** 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-236">Choose the **Build Definition's default branch** option.</span></span> <span data-ttu-id="6bcbc-237">이 필터를 사용 하면 GitHub 리포지토리의에서 빌드에 대해서만 트리거하려면 릴리스 *마스터* 분기 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-237">This filter causes the release to trigger only for a build from the GitHub repository's *master* branch.</span></span>
1. <span data-ttu-id="6bcbc-238">**저장** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-238">Click the **Save** button.</span></span> <span data-ttu-id="6bcbc-239">클릭 합니다 **확인** 결과 단추 **저장** 모달 대화 상자.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-239">Click the **OK** button in the resulting **Save** modal dialog.</span></span>
1. <span data-ttu-id="6bcbc-240">클릭 합니다 **환경 1** 상자입니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-240">Click the **Environment 1** box.</span></span> <span data-ttu-id="6bcbc-241">**환경** 패널 오른쪽에 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-241">An **Environment** panel appears to the right.</span></span> <span data-ttu-id="6bcbc-242">변경 합니다 *환경 1* 에서 텍스트를 **환경 이름** 텍스트 상자 *프로덕션*.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-242">Change the *Environment 1* text in the **Environment name** textbox to *Production*.</span></span>

   ![릴리스 파이프라인-환경 이름 입력란](media/cicd/vsts-environment-name-textbox.png)

1. <span data-ttu-id="6bcbc-244">클릭 합니다 **1 단계, 2 개의 태스크가** 링크를 **프로덕션** 상자:</span><span class="sxs-lookup"><span data-stu-id="6bcbc-244">Click the **1 phase, 2 tasks** link in the **Production** box:</span></span>

    ![릴리스 파이프라인-프로덕션 환경 link.png](media/cicd/vsts-production-link.png)

    <span data-ttu-id="6bcbc-246">합니다 **작업** 환경의 탭이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-246">The **Tasks** tab of the environment appears.</span></span>
1. <span data-ttu-id="6bcbc-247">클릭 합니다 **슬롯으로 Azure App Service 배포** 작업 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-247">Click the **Deploy Azure App Service to Slot** task.</span></span> <span data-ttu-id="6bcbc-248">오른쪽 패널에서 해당 설정이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-248">Its settings appear in a panel to the right.</span></span>
1. <span data-ttu-id="6bcbc-249">App Service와 연결 된 Azure 구독을 선택 합니다 **Azure 구독** 드롭 다운 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-249">Select the Azure subscription associated with the App Service from the **Azure subscription** drop-down.</span></span> <span data-ttu-id="6bcbc-250">선택를 클릭 합니다 **권한 부여** 단추입니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-250">Once selected, click the **Authorize** button.</span></span>
1. <span data-ttu-id="6bcbc-251">선택 *웹 앱* 에서 합니다 **앱 유형** 드롭 다운 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-251">Select *Web App* from the **App type** drop-down.</span></span>
1. <span data-ttu-id="6bcbc-252">선택 *mywebapp / < unique_number / >* 에서 합니다 **App service 이름** 드롭 다운 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-252">Select *mywebapp/<unique_number/>* from the **App service name** drop-down.</span></span>
1. <span data-ttu-id="6bcbc-253">선택 *AzureTutorial* 에서 합니다 **리소스 그룹** 드롭 다운 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-253">Select *AzureTutorial* from the **Resource group** drop-down.</span></span>
1. <span data-ttu-id="6bcbc-254">선택 *스테이징* 에서 합니다 **슬롯** 드롭 다운 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-254">Select *staging* from the **Slot** drop-down.</span></span>
1. <span data-ttu-id="6bcbc-255">**저장** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-255">Click the **Save** button.</span></span>
1. <span data-ttu-id="6bcbc-256">기본 릴리스 파이프라인 이름을 마우스로 가리킵니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-256">Hover over the default release pipeline name.</span></span> <span data-ttu-id="6bcbc-257">편집 하려면 연필 아이콘을 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-257">Click the pencil icon to edit it.</span></span> <span data-ttu-id="6bcbc-258">사용 하 여 *MyFirstProject ASP.NET Core-CD* 이름으로 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-258">Use *MyFirstProject-ASP.NET Core-CD* as the name.</span></span>

    ![릴리스 파이프라인 이름](media/cicd/vsts-release-definition-name.png)

1. <span data-ttu-id="6bcbc-260">**저장** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-260">Click the **Save** button.</span></span>

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a><span data-ttu-id="6bcbc-261">GitHub에 변경 내용 커밋 및 자동으로 Azure에 배포</span><span class="sxs-lookup"><span data-stu-id="6bcbc-261">Commit changes to GitHub and automatically deploy to Azure</span></span>

1. <span data-ttu-id="6bcbc-262">오픈 *SimpleFeedReader.sln* Visual Studio에서.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-262">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
1. <span data-ttu-id="6bcbc-263">솔루션 탐색기에서 엽니다 *Pages\Index.cshtml*합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-263">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="6bcbc-264">변경 `<h2>Simple Feed Reader - V3</h2>` 에 `<h2>Simple Feed Reader - V4</h2>`입니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-264">Change `<h2>Simple Feed Reader - V3</h2>` to `<h2>Simple Feed Reader - V4</h2>`.</span></span>
1. <span data-ttu-id="6bcbc-265">키를 눌러 **Ctrl**+**Shift**+**B** 앱을 빌드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-265">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
1. <span data-ttu-id="6bcbc-266">GitHub 리포지토리에 파일을 커밋하십시오.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-266">Commit the file to the GitHub repository.</span></span> <span data-ttu-id="6bcbc-267">사용 하 여는 **변경 내용을** Visual studio의 페이지 *팀 탐색기* 탭 또는 다음 실행 하 여 로컬 컴퓨터의 명령 셸을 사용 하 여:</span><span class="sxs-lookup"><span data-stu-id="6bcbc-267">Use either the **Changes** page in Visual Studio's *Team Explorer* tab, or execute the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V4"
    ```

1. <span data-ttu-id="6bcbc-268">변경 내용을 푸시 합니다 *마스터* 분기를 *원본* GitHub 리포지토리 원격:</span><span class="sxs-lookup"><span data-stu-id="6bcbc-268">Push the change in the *master* branch to the *origin* remote of your GitHub repository:</span></span>

    ```console
    git push origin master
    ```

    <span data-ttu-id="6bcbc-269">GitHub의 리포지토리에서 커밋을 나타납니다 *마스터* 분기:</span><span class="sxs-lookup"><span data-stu-id="6bcbc-269">The commit appears in the GitHub repository's *master* branch:</span></span>

    ![마스터 분기에서 GitHub 커밋](media/cicd/github-commit.png)

    <span data-ttu-id="6bcbc-271">연속 통합 빌드 정의에서 활성화 되어 있으므로 빌드는 트리거되면 **트리거** 탭:</span><span class="sxs-lookup"><span data-stu-id="6bcbc-271">The build is triggered, since continuous integration is enabled in the build definition's **Triggers** tab:</span></span>

    ![연속 통합을 사용 하도록 설정](media/cicd/enable-ci.png)

1. <span data-ttu-id="6bcbc-273">이동할를 **대기** 탭의 **Azure 파이프라인** > **빌드** Azure DevOps 서비스에서 페이지.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-273">Navigate to the **Queued** tab of the **Azure Pipelines** > **Builds** page in Azure DevOps Services.</span></span> <span data-ttu-id="6bcbc-274">분기 및 빌드를 트리거한 커밋 대기 중인된 빌드를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-274">The queued build shows the branch and commit that triggered the build:</span></span>

    ![큐에 대기 중인된 빌드](media/cicd/build-queued.png)

1. <span data-ttu-id="6bcbc-276">빌드에 성공 하면 Azure에 배포를 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-276">Once the build succeeds, a deployment to Azure occurs.</span></span> <span data-ttu-id="6bcbc-277">브라우저에서 앱으로 이동 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-277">Navigate to the app in the browser.</span></span> <span data-ttu-id="6bcbc-278">제목에 "V4" 텍스트가 표시 되는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-278">Notice that the "V4" text appears in the heading:</span></span>

    ![업데이트 된 앱](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a><span data-ttu-id="6bcbc-280">Azure 파이프라인 파이프라인 검토</span><span class="sxs-lookup"><span data-stu-id="6bcbc-280">Examine the Azure Pipelines pipeline</span></span>

### <a name="build-definition"></a><span data-ttu-id="6bcbc-281">빌드 정의</span><span class="sxs-lookup"><span data-stu-id="6bcbc-281">Build definition</span></span>

<span data-ttu-id="6bcbc-282">빌드 정의 이름을 만들어졌으므로 *MyFirstProject ASP.NET Core-CI*합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-282">A build definition was created with the name *MyFirstProject-ASP.NET Core-CI*.</span></span> <span data-ttu-id="6bcbc-283">빌드가 완료 되 면 생성 된 *.zip* 게시할 자산을 포함 하 여 파일.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-283">Upon completion, the build produces a *.zip* file including the assets to be published.</span></span> <span data-ttu-id="6bcbc-284">릴리스 파이프라인을 Azure로 해당 자산을 배포합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-284">The release pipeline deploys those assets to Azure.</span></span>

<span data-ttu-id="6bcbc-285">빌드 정의 **작업** 탭 사용 중인 개별 단계를 나열 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-285">The build definition's **Tasks** tab lists the individual steps being used.</span></span> <span data-ttu-id="6bcbc-286">다섯 가지 빌드 작업이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-286">There are five build tasks.</span></span>

![빌드 정의 작업](media/cicd/build-definition-tasks.png)

1. <span data-ttu-id="6bcbc-288">**복원** &mdash; 실행을 `dotnet restore` 앱의 NuGet 패키지를 복원 하는 명령입니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-288">**Restore** &mdash; Executes the `dotnet restore` command to restore the app's NuGet packages.</span></span> <span data-ttu-id="6bcbc-289">기본 패키지를 사용 하는 피드는 nuget.org 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-289">The default package feed used is nuget.org.</span></span>
1. <span data-ttu-id="6bcbc-290">**빌드** &mdash; 실행을 `dotnet build --configuration release` 앱의 코드를 컴파일하는 명령입니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-290">**Build** &mdash; Executes the `dotnet build --configuration release` command to compile the app's code.</span></span> <span data-ttu-id="6bcbc-291">이 `--configuration` 옵션은 최적화 된 코드는 프로덕션 환경에 배포에 적합 한 버전을 만드는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-291">This `--configuration` option is used to produce an optimized version of the code, which is suitable for deployment to a production environment.</span></span> <span data-ttu-id="6bcbc-292">수정 합니다 *BuildConfiguration* 빌드 정의 변수 **변수** 디버그 구성이 필요한 예를 들어 하는 경우를 탭 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-292">Modify the *BuildConfiguration* variable on the build definition's **Variables** tab if, for example, a debug configuration is needed.</span></span>
1. <span data-ttu-id="6bcbc-293">**테스트** &mdash; 실행을 `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` 앱의 단위 테스트를 실행할 명령입니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-293">**Test** &mdash; Executes the `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` command to run the app's unit tests.</span></span> <span data-ttu-id="6bcbc-294">단위 테스트는 모든 C# 프로젝트와 일치 하는 내에서 실행 되는 `**/*Tests/*.csproj` glob 패턴입니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-294">Unit tests are executed within any C# project matching the `**/*Tests/*.csproj` glob pattern.</span></span> <span data-ttu-id="6bcbc-295">테스트 결과에 저장 됩니다는 *.trx* 로 지정 된 위치에서 파일을 `--results-directory` 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-295">Test results are saved in a *.trx* file at the location specified by the `--results-directory` option.</span></span> <span data-ttu-id="6bcbc-296">모든 테스트에 실패 하는 경우 빌드가 실패 하 고 배포 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-296">If any tests fail, the build fails and isn't deployed.</span></span>

    > [!NOTE]
    > <span data-ttu-id="6bcbc-297">단위 테스트 작업을 확인 하려면 수정할 *SimpleFeedReader.Tests\Services\NewsServiceTests.cs* 의도적으로 테스트 중 하나를 중단 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-297">To verify the unit tests work, modify *SimpleFeedReader.Tests\Services\NewsServiceTests.cs* to purposefully break one of the tests.</span></span> <span data-ttu-id="6bcbc-298">예를 들어 변경 `Assert.True(result.Count > 0);` 하 `Assert.False(result.Count > 0);` 에 `Returns_News_Stories_Given_Valid_Uri` 메서드.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-298">For example, change `Assert.True(result.Count > 0);` to `Assert.False(result.Count > 0);` in the `Returns_News_Stories_Given_Valid_Uri` method.</span></span> <span data-ttu-id="6bcbc-299">커밋하고 GitHub에 변경 내용을 푸시하십시오.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-299">Commit and push the change to GitHub.</span></span> <span data-ttu-id="6bcbc-300">빌드가 트리거되고 실패 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-300">The build is triggered and fails.</span></span> <span data-ttu-id="6bcbc-301">빌드 파이프라인 상태를 변경 **못했습니다**합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-301">The build pipeline status changes to **failed**.</span></span> <span data-ttu-id="6bcbc-302">변경, 커밋 및 푸시를 다시 되돌립니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-302">Revert the change, commit, and push again.</span></span> <span data-ttu-id="6bcbc-303">빌드 성공합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-303">The build succeeds.</span></span>

1. <span data-ttu-id="6bcbc-304">**게시** &mdash; 실행 합니다 `dotnet publish --configuration release --output <local_path_on_build_agent>` 명령을 생성 하는 *.zip* 배포할 아티팩트를 사용 하 여 파일.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-304">**Publish** &mdash; Executes the `dotnet publish --configuration release --output <local_path_on_build_agent>` command to produce a *.zip* file with the artifacts to be deployed.</span></span> <span data-ttu-id="6bcbc-305">합니다 `--output` 옵션의 게시 위치를 지정 합니다 *.zip* 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-305">The `--output` option specifies the publish location of the *.zip* file.</span></span> <span data-ttu-id="6bcbc-306">전달 하 여 위치 지정 되어 있는지를 [미리 정의 된 변수](/azure/devops/pipelines/build/variables) 라는 `$(build.artifactstagingdirectory)`합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-306">That location is specified by passing a [predefined variable](/azure/devops/pipelines/build/variables) named `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="6bcbc-307">변수를 확장 하 여 로컬 경로 같은 *c:\agent\_work\1\a*, 빌드 에이전트에서.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-307">That variable expands to a local path, such as *c:\agent\_work\1\a*, on the build agent.</span></span>
1. <span data-ttu-id="6bcbc-308">**아티팩트 게시** &mdash; Publishes 합니다 *.zip* 에서 생성 된 파일을 **게시** 작업.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-308">**Publish Artifact** &mdash; Publishes the *.zip* file produced by the **Publish** task.</span></span> <span data-ttu-id="6bcbc-309">작업을 허용 합니다 *.zip* 파일 위치는 미리 정의 된 변수는 매개 변수로 `$(build.artifactstagingdirectory)`합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-309">The task accepts the *.zip* file location as a parameter, which is the predefined variable `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="6bcbc-310">합니다 *.zip* 파일이 라는 폴더로 게시 되 *drop*합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-310">The *.zip* file is published as a folder named *drop*.</span></span>

<span data-ttu-id="6bcbc-311">빌드 정의 클릭 **요약** 정의 사용 하 여 빌드 기록을 보려면 링크:</span><span class="sxs-lookup"><span data-stu-id="6bcbc-311">Click the build definition's **Summary** link to view a history of builds with the definition:</span></span>

![표시 된 빌드 정의 기록을 스크린 샷](media/cicd/build-definition-summary.png)

<span data-ttu-id="6bcbc-313">결과 페이지에서 고유한 빌드 번호에 해당 하는 링크를 클릭 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-313">On the resulting page, click the link corresponding to the unique build number:</span></span>

![스크린 샷 빌드 정의 요약 페이지를 보여 주는](media/cicd/build-definition-completed.png)

<span data-ttu-id="6bcbc-315">이 특정 빌드 요약이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-315">A summary of this specific build is displayed.</span></span> <span data-ttu-id="6bcbc-316">클릭 합니다 **아티팩트** 탭을 확인 합니다 *drop* 빌드에서 생성 된 폴더가 나열 됩니다:</span><span class="sxs-lookup"><span data-stu-id="6bcbc-316">Click the **Artifacts** tab, and notice the *drop* folder produced by the build is listed:</span></span>

![빌드 정의 아티팩트-저장 폴더를 보여 주는 스크린샷](media/cicd/build-definition-artifacts.png)

<span data-ttu-id="6bcbc-318">사용 된 **다운로드** 및 **탐색** 게시 된 아티팩트를 검사 하는 링크입니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-318">Use the **Download** and **Explore** links to inspect the published artifacts.</span></span>

### <a name="release-pipeline"></a><span data-ttu-id="6bcbc-319">릴리스 파이프라인</span><span class="sxs-lookup"><span data-stu-id="6bcbc-319">Release pipeline</span></span>

<span data-ttu-id="6bcbc-320">릴리스 파이프라인 이름을 만들어졌으므로 *MyFirstProject ASP.NET Core-CD*:</span><span class="sxs-lookup"><span data-stu-id="6bcbc-320">A release pipeline was created with the name *MyFirstProject-ASP.NET Core-CD*:</span></span>

![스크린 샷 보여 주는 릴리스 파이프라인 개요](media/cicd/release-definition-overview.png)

<span data-ttu-id="6bcbc-322">릴리스 파이프라인의 두 가지 주요 구성 요소를 **아티팩트** 하며 **환경**합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-322">The two major components of the release pipeline are the **Artifacts** and the **Environments**.</span></span> <span data-ttu-id="6bcbc-323">상자를 **아티팩트** 섹션에는 다음 창이 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-323">Clicking the box in the **Artifacts** section reveals the following panel:</span></span>

![표시 된 릴리스 파이프라인 아티팩트 스크린 샷](media/cicd/release-definition-artifacts.png)

<span data-ttu-id="6bcbc-325">합니다 **소스 (빌드 정의)** 값이 릴리스 파이프라인은 연결 되는 빌드 정의 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-325">The **Source (Build definition)** value represents the build definition to which this release pipeline is linked.</span></span> <span data-ttu-id="6bcbc-326">합니다 *.zip* 빌드 정의의 실행을 성공적으로 생성 된 파일에 제공 됩니다 합니다 *프로덕션* Azure에 배포 하기 위한 환경입니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-326">The *.zip* file produced by a successful run of the build definition is provided to the *Production* environment for deployment to Azure.</span></span> <span data-ttu-id="6bcbc-327">클릭 합니다 *1 단계, 2 개의 태스크가* 링크를 *프로덕션* 릴리스 파이프라인 작업을 확인 하려면 상자, 환경:</span><span class="sxs-lookup"><span data-stu-id="6bcbc-327">Click the *1 phase, 2 tasks* link in the *Production* environment box to view the release pipeline tasks:</span></span>

![릴리스 파이프라인 작업을 보여 주는 스크린샷](media/cicd/release-definition-tasks.png)

<span data-ttu-id="6bcbc-329">릴리스 파이프라인의 두 가지 작업으로 구성 됩니다. *Azure App Service 슬롯에 배포* 하 고 *Azure App Service-Slot Swap 관리*.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-329">The release pipeline consists of two tasks: *Deploy Azure App Service to Slot* and *Manage Azure App Service - Slot Swap*.</span></span> <span data-ttu-id="6bcbc-330">첫 번째 작업을 클릭 하면 다음 작업 구성을 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-330">Clicking the first task reveals the following task configuration:</span></span>

![스크린 샷 보여 주는 릴리스 파이프라인 배포 작업](media/cicd/release-definition-task1.png)

<span data-ttu-id="6bcbc-332">Azure 구독, 서비스 유형, 웹 앱 이름, 리소스 그룹 및 배포 슬롯은 배포 작업에 정의 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-332">The Azure subscription, service type, web app name, resource group, and deployment slot are defined in the deployment task.</span></span> <span data-ttu-id="6bcbc-333">**패키지 또는 폴더가** 보유 하는 텍스트 상자를 *.zip* 파일 경로를 추출 하 고 배포를 *준비* 의 슬롯을 *mywebapp\<고유 수 (_n)\>*  웹 앱입니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-333">The **Package or folder** textbox holds the *.zip* file path to be extracted and deployed to the *staging* slot of the *mywebapp\<unique_number\>* web app.</span></span>

<span data-ttu-id="6bcbc-334">슬롯 스왑 작업을 클릭 하면 다음 작업 구성을 표시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-334">Clicking the slot swap task reveals the following task configuration:</span></span>

![스크린 샷 보여 주는 릴리스 파이프라인 슬롯 스왑 작업](media/cicd/release-definition-task2.png)

<span data-ttu-id="6bcbc-336">구독, 리소스 그룹, 서비스 유형, 웹 앱 이름 및 배포 슬롯 세부 정보 제공 됩니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-336">The subscription, resource group, service type, web app name, and deployment slot details are provided.</span></span> <span data-ttu-id="6bcbc-337">합니다 **프로덕션과 교환** 확인란을 선택 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-337">The **Swap with Production** check box is checked.</span></span> <span data-ttu-id="6bcbc-338">비트를 배포 하는 결과적으로 *준비* 슬롯은 프로덕션 환경으로 교체 합니다.</span><span class="sxs-lookup"><span data-stu-id="6bcbc-338">Consequently, the bits deployed to the *staging* slot are swapped into the production environment.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="6bcbc-339">추가 참조 항목</span><span class="sxs-lookup"><span data-stu-id="6bcbc-339">Additional reading</span></span>

* [<span data-ttu-id="6bcbc-340">Azure Pipelines를 사용하여 첫 번째 파이프라인 만들기</span><span class="sxs-lookup"><span data-stu-id="6bcbc-340">Create your first pipeline with Azure Pipelines</span></span>](/azure/devops/pipelines/get-started-yaml)
* [<span data-ttu-id="6bcbc-341">빌드 및.NET Core 프로젝트</span><span class="sxs-lookup"><span data-stu-id="6bcbc-341">Build and .NET Core project</span></span>](/azure/devops/pipelines/languages/dotnet-core)
* [<span data-ttu-id="6bcbc-342">Azure 파이프라인을 사용 하 여 웹 앱 배포</span><span class="sxs-lookup"><span data-stu-id="6bcbc-342">Deploy a web app with Azure Pipelines</span></span>](/azure/devops/pipelines/targets/webapp)
