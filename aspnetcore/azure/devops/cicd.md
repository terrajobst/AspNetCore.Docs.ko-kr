---
title: 지속적인 통합 및 배포-ASP.NET Core 및 Azure를 사용 하 여 DevOps
author: CamSoper
description: 지속적인 통합 및 ASP.NET Core 및 Azure를 사용 하 여 DevOps에 배포
ms.author: scaddie
ms.date: 10/24/2018
ms.custom: mvc, seodec18
uid: azure/devops/cicd
ms.openlocfilehash: 5fdf52235b49119503885f92c370dc588e809ffe
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64894000"
---
# <a name="continuous-integration-and-deployment"></a>지속적인 통합 및 배포

이전 장에서 간단한 피드 판독기 앱에 대 한 로컬 Git 리포지토리를 만들었습니다. 이 챕터에서는 해당 코드는 GitHub 리포지토리를 게시 하 고 Azure 파이프라인을 사용 하 여 Azure DevOps 서비스 파이프라인을 생성 합니다. 파이프라인에는 연속 빌드 및 앱의 배포 가능합니다. GitHub 리포지토리에 커밋 빌드 및 Azure 웹 앱의 스테이징 슬롯에 배포를 트리거합니다.

이 섹션에서는 다음 작업을 완료 합니다.

* 앱의 코드를 GitHub에 게시
* 로컬 Git 배포를 연결 끊기
* Azure DevOps 조직 만들기
* Azure DevOps 서비스에서 팀 프로젝트 만들기
* 빌드 정의 만들기
* 릴리스 파이프라인을 만들려면
* GitHub에 변경 내용 커밋 및 자동으로 Azure에 배포
* Azure 파이프라인 파이프라인 검토

## <a name="publish-the-apps-code-to-github"></a>앱의 코드를 GitHub에 게시

1. 브라우저 창을 열고 이동할 `https://github.com`합니다.
1. 클릭 합니다 **+** 드롭 다운 헤더에서 선택한 **새 리포지토리**:

    ![새 GitHub 리포지토리 옵션](media/cicd/github-new-repo.png)

1. 계정을 선택 합니다 **소유자** 드롭다운 목록에서 enter *단순 피드 판독기* 에서 **리포지토리 이름** 텍스트 상자에 붙여넣습니다.
1. 클릭 합니다 **리포지토리 만들기** 단추입니다.
1. 로컬 컴퓨터의 명령 셸을 엽니다. 디렉터리를 이동 합니다 *단순 피드 판독기* Git 리포지토리에 저장 됩니다.
1. 기존 이름 바꾸기 *원점* 원격 *업스트림*합니다. 다음 명령을 실행합니다.

    ```console
    git remote rename origin upstream
    ```

1. 새 *원본* 원격 github 리포지토리의 사본을 가리키는 합니다. 다음 명령을 실행합니다.

    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```

1. 새로 만든 GitHub 리포지토리에 로컬 Git 리포지토리를 게시 합니다. 다음 명령을 실행합니다.

    ```console
    git push -u origin master
    ```

1. 브라우저 창을 열고 이동할 `https://github.com/<GitHub_username>/simple-feed-reader/`합니다. GitHub 리포지토리에서 코드 표시 되는지 확인 합니다.

## <a name="disconnect-local-git-deployment"></a>로컬 Git 배포를 연결 끊기

다음 단계를 사용 하 여 로컬 Git 배포를 제거 합니다. Azure 파이프라인 (Azure DevOps 서비스) 모두 대체 하 고 해당 기능을 보완 합니다.

1. 열기는 [Azure portal](https://portal.azure.com/)로 이동 합니다 *준비 (mywebapp\<unique_number\>준비 /)* 웹 앱. 입력 하 여 웹 앱을 신속 하 게 찾을 수 있습니다 *준비* 포털의 검색 상자에서:

    ![스테이징 웹 앱 검색 용어](media/cicd/portal-search-box.png)

1. 클릭 **배포 센터**합니다. 새 패널이 표시 됩니다. 클릭 **연결 끊기** 이전 장에서 추가 된 로컬 Git 소스 제어 구성 제거 합니다. 클릭 하 여 제거 작업을 확인 합니다 **예** 단추입니다.
1. 로 이동 합니다 *mywebapp < unique_number >* App Service입니다. 참고로, 포털의 검색 상자 빨리 App Service를 찾는 데 사용할 수 있습니다.
1. 클릭 **배포 센터**합니다. 새 패널이 표시 됩니다. 클릭 **연결 끊기** 이전 장에서 추가 된 로컬 Git 소스 제어 구성 제거 합니다. 클릭 하 여 제거 작업을 확인 합니다 **예** 단추입니다.

## <a name="create-an-azure-devops-organization"></a>Azure DevOps 조직 만들기

1. 브라우저를 열고로 이동 합니다 [Azure DevOps 조직 만들기 페이지](https://go.microsoft.com/fwlink/?LinkId=307137)합니다.
1. 에 고유한 이름을 입력 합니다 **기억 하기 쉬운 이름을 선택** Azure DevOps 조직에 액세스 하기 위한 url을 텍스트 상자에 붙여넣습니다.
1. 선택 된 **Git** 라디오 단추, 코드는 GitHub 리포지토리에 호스트 되는 때문입니다.
1. **계속** 단추를 클릭합니다. 짧은 대기 시간, 계정 및 팀 프로젝트, 후 이름이 *MyFirstProject*에 만들어집니다.

    ![Azure DevOps 조직 만들기 페이지](media/cicd/vsts-account-creation.png)

1. Azure DevOps 조직 및 프로젝트에서 사용 하기 위해 준비 되었는지 나타내는 확인 전자 메일을 엽니다. 클릭 합니다 **프로젝트를 시작** 단추:

    ![시작 프로젝트 단추](media/cicd/vsts-start-project.png)

1. 브라우저를 엽니다  *\<account_name\>. visualstudio.com*합니다. 클릭 합니다 *MyFirstProject* 프로젝트의 DevOps 파이프라인을 구성 하려면 먼저 연결 합니다.

## <a name="configure-the-azure-pipelines-pipeline"></a>Azure 파이프라인 파이프라인 구성

완료 하려면 다음 세 가지 단계가 있습니다. Operational DevOps 파이프라인의 다음 세 가지 섹션에서는 결과의 단계를 완료 합니다.

### <a name="grant-azure-devops-access-to-the-github-repository"></a>GitHub 리포지토리에 대 한 Grant Azure DevOps 액세스

1. 확장 된 **또는 외부 리포지토리에서 코드 빌드** accordion 합니다. 클릭 합니다 **설치 빌드** 단추:

    ![빌드 단추를 설정 합니다.](media/cicd/vsts-setup-build.png)

1. 선택 된 **GitHub** 에서 옵션을 **원본 선택** 섹션:

    ![소스-GitHub를 선택 합니다.](media/cicd/vsts-select-source.png)

1. Azure DevOps GitHub 리포지토리에 액세스 하기 전에 권한 부여 필요 합니다. 입력 *< GitHub_username > GitHub 연결* 에 **연결 이름** 텍스트 상자에 붙여넣습니다. 예를 들어 다음과 같습니다.

    ![GitHub 연결 이름](media/cicd/vsts-repo-authz.png)

1. GitHub 계정에 다단계 인증을 사용 하는 경우 개인용 액세스 토큰은 필요 합니다. 이 경우 클릭 합니다 **GitHub 개인용 액세스 토큰을 사용 하 여 권한 부여** 링크 합니다. 참조 된 [공식 GitHub 개인용 액세스 토큰 만들기 지침](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) 도움말에 대 한 합니다. 만 *리포지토리* 사용 권한 범위는 필요 합니다. 그렇지 않은 경우 클릭 합니다 **OAuth를 사용 하 여 권한 부여** 단추입니다.
1. 메시지가 표시 되 면 GitHub 계정에 로그인 합니다. Azure DevOps 조직에 대 한 액세스 권한을 부여 하려면 권한 부여를 선택 합니다. 성공 하면 새 서비스 끝점 생성 됩니다.
1. 그런 다음 줄임표 단추를 클릭 합니다 **리포지토리** 단추입니다. 선택 된 *< GitHub_username > / 간단한 피드 판독기* 리포지토리 목록에서. 클릭 합니다 **선택** 단추입니다.
1. 선택 된 *마스터* 에서 분기를 **수동 및 예약 된 빌드의 기본 분기** 드롭 다운 합니다. **계속** 단추를 클릭합니다. 템플릿 선택 페이지가 표시 됩니다.

### <a name="create-the-build-definition"></a>빌드 정의 만들기

1. 템플릿 선택 페이지에서 입력 *ASP.NET Core* 검색 상자에서:

    ![ASP.NET Core 템플릿 페이지의 검색 기능](media/cicd/vsts-template-selection.png)

1. 템플릿 검색 결과가 표시 됩니다. 마우스로 합니다 **ASP.NET Core** 템플릿과 클릭 합니다 **적용** 단추입니다.
1. 합니다 **작업** 빌드 정의의 탭이 표시 됩니다. **트리거** 탭을 클릭합니다.
1. 확인 합니다 **연속 통합을 사용 하도록 설정** 상자입니다. 아래는 **분기 필터** 섹션에서 확인 합니다 **형식** 드롭다운 목록으로 설정 됩니다 *포함*합니다. 설정 된 **분기 사양** 드롭다운을 *마스터*합니다.

    ![연속 통합 설정 사용](media/cicd/vsts-enable-ci.png)

    이러한 설정으로 인해 변경 내용 푸시 될 때 트리거 빌드 합니다 *마스터* GitHub 리포지토리의 분기입니다. 연속 통합에서 테스트 되는 [GitHub에 변경 내용 커밋 및 자동으로 Azure에 배포](#commit-changes-to-github-and-automatically-deploy-to-azure) 섹션입니다.

1. 클릭 합니다 **저장 및 큐에 넣기** 단추를 선택한 합니다 **저장** 옵션:

    ![저장 단추](media/cicd/vsts-save-build.png)

1. 다음 모달 대화 상자가 나타납니다.

    ![저장 빌드 정의-모달 대화 상자](media/cicd/vsts-save-modal.png)

    기본 폴더를 사용 하 여 *\\* 를 클릭 합니다 **저장** 단추입니다.

### <a name="create-the-release-pipeline"></a>릴리스 파이프라인을 만듭니다

1. 클릭 합니다 **릴리스** 팀 프로젝트의 탭 합니다. 클릭 합니다 **새 파이프라인** 단추입니다.

    ![릴리스 탭-새 정의 단추](media/cicd/vsts-new-release-definition.png)

    템플릿 선택 창이 나타납니다.

1. 템플릿 선택 페이지에서 입력 *App Service* 검색 상자에서:

    ![릴리스 파이프라인 템플릿 검색 상자](media/cicd/vsts-release-template-search.png)

1. 템플릿 검색 결과가 표시 됩니다. 마우스로 **Azure App Service 배포 슬롯** 템플릿과 클릭 합니다 **적용** 단추입니다. 합니다 **파이프라인** 릴리스 파이프라인의 탭이 표시 됩니다.

    ![릴리스 파이프라인 파이프라인 탭](media/cicd/vsts-release-definition-pipeline.png)

1. 클릭 합니다 **추가** 단추를 **아티팩트** 상자입니다. 합니다 **아티팩트 추가** 패널이 나타납니다.

    ![릴리스 파이프라인-아티팩트 패널 추가](media/cicd/vsts-release-add-artifact.png)

1. 선택 합니다 **빌드** 에서 타일을 **원본 유형** 섹션입니다. 빌드 정의에 릴리스 파이프라인을 연결 하는이 형식을 허용 합니다.
1. 선택 *MyFirstProject* 에서 합니다 **프로젝트** 드롭 다운 합니다.
1. 빌드 정의 이름을 선택 *MyFirstProject ASP.NET Core-CI*에서 **원본 (빌드 정의)** 드롭 다운 합니다.
1. 선택 *최신* 에서 합니다 **기본 버전** 드롭 다운 합니다. 이 옵션에는 빌드 정의의 가장 최근 실행과에서 생성 된 아티팩트를 작성 합니다.
1. 에 있는 텍스트 대체를 **소스 별칭** textbox *삭제*.
1. **추가** 단추를 클릭합니다. 합니다 **아티팩트** 섹션 변경 내용을 표시 하도록 업데이트 합니다.
1. 연속 배포를 사용 하도록 설정 하려면 번개 모양 아이콘을 클릭 합니다.

    ![릴리스 파이프라인 아티팩트-번개 모양 아이콘](media/cicd/vsts-artifacts-lightning-bolt.png)

    이 옵션을 사용 하면 배포에는 새 빌드를 사용할 때마다 발생 합니다.
1. A **연속 배포 트리거** 패널 오른쪽에 나타납니다. 기능을 사용 하도록 설정/해제 단추를 클릭 합니다. 사용 하도록 설정 하려면 필요는 없습니다 합니다 **끌어오기 요청 트리거**합니다.
1. 클릭 합니다 **추가** 드롭 다운에서의 **빌드 분기 필터** 섹션입니다. 선택 된 **빌드 정의 기본 분기** 옵션입니다. 이 필터를 사용 하면 GitHub 리포지토리의에서 빌드에 대해서만 트리거하려면 릴리스 *마스터* 분기 합니다.
1. **저장** 단추를 클릭합니다. 클릭 합니다 **확인** 결과 단추 **저장** 모달 대화 상자.
1. 클릭 합니다 **환경 1** 상자입니다. **환경** 패널 오른쪽에 나타납니다. 변경 합니다 *환경 1* 에서 텍스트를 **환경 이름** 텍스트 상자 *프로덕션*.

   ![릴리스 파이프라인-환경 이름 입력란](media/cicd/vsts-environment-name-textbox.png)

1. 클릭 합니다 **1 단계, 2 개의 태스크가** 링크를 **프로덕션** 상자:

    ![릴리스 파이프라인-프로덕션 환경 link.png](media/cicd/vsts-production-link.png)

    합니다 **작업** 환경의 탭이 표시 됩니다.
1. 클릭 합니다 **슬롯으로 Azure App Service 배포** 작업 합니다. 오른쪽 패널에서 해당 설정이 표시 됩니다.
1. App Service와 연결 된 Azure 구독을 선택 합니다 **Azure 구독** 드롭 다운 합니다. 선택를 클릭 합니다 **권한 부여** 단추입니다.
1. 선택 *웹 앱* 에서 합니다 **앱 유형** 드롭 다운 합니다.
1. 선택 *mywebapp / < unique_number / >* 에서 합니다 **App service 이름** 드롭 다운 합니다.
1. 선택 *AzureTutorial* 에서 합니다 **리소스 그룹** 드롭 다운 합니다.
1. 선택 *스테이징* 에서 합니다 **슬롯** 드롭 다운 합니다.
1. **저장** 단추를 클릭합니다.
1. 기본 릴리스 파이프라인 이름을 마우스로 가리킵니다. 편집 하려면 연필 아이콘을 클릭 합니다. 사용 하 여 *MyFirstProject ASP.NET Core-CD* 이름으로 합니다.

    ![릴리스 파이프라인 이름](media/cicd/vsts-release-definition-name.png)

1. **저장** 단추를 클릭합니다.

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a>GitHub에 변경 내용 커밋 및 자동으로 Azure에 배포

1. 오픈 *SimpleFeedReader.sln* Visual Studio에서.
1. 솔루션 탐색기에서 엽니다 *Pages\Index.cshtml*합니다. 변경 `<h2>Simple Feed Reader - V3</h2>` 에 `<h2>Simple Feed Reader - V4</h2>`입니다.
1. 키를 눌러 **Ctrl**+**Shift**+**B** 앱을 빌드할 수 있습니다.
1. GitHub 리포지토리에 파일을 커밋하십시오. 사용 하 여는 **변경 내용을** Visual studio의 페이지 *팀 탐색기* 탭 또는 다음 실행 하 여 로컬 컴퓨터의 명령 셸을 사용 하 여:

    ```console
    git commit -a -m "upgraded to V4"
    ```

1. 변경 내용을 푸시 합니다 *마스터* 분기를 *원본* GitHub 리포지토리 원격:

    ```console
    git push origin master
    ```

    GitHub의 리포지토리에서 커밋을 나타납니다 *마스터* 분기:

    ![마스터 분기에서 GitHub 커밋](media/cicd/github-commit.png)

    연속 통합 빌드 정의에서 활성화 되어 있으므로 빌드는 트리거되면 **트리거** 탭:

    ![연속 통합을 사용 하도록 설정](media/cicd/enable-ci.png)

1. 이동할를 **대기** 탭의 **Azure 파이프라인** > **빌드** Azure DevOps 서비스에서 페이지. 분기 및 빌드를 트리거한 커밋 대기 중인된 빌드를 보여 줍니다.

    ![큐에 대기 중인된 빌드](media/cicd/build-queued.png)

1. 빌드에 성공 하면 Azure에 배포를 발생 합니다. 브라우저에서 앱으로 이동 합니다. 제목에 "V4" 텍스트가 표시 되는지 확인 합니다.

    ![업데이트 된 앱](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a>Azure 파이프라인 파이프라인 검토

### <a name="build-definition"></a>빌드 정의

빌드 정의 이름을 만들어졌으므로 *MyFirstProject ASP.NET Core-CI*합니다. 빌드가 완료 되 면 생성 된 *.zip* 게시할 자산을 포함 하 여 파일. 릴리스 파이프라인을 Azure로 해당 자산을 배포합니다.

빌드 정의 **작업** 탭 사용 중인 개별 단계를 나열 합니다. 다섯 가지 빌드 작업이 있습니다.

![빌드 정의 작업](media/cicd/build-definition-tasks.png)

1. **복원** &mdash; 실행을 `dotnet restore` 앱의 NuGet 패키지를 복원 하는 명령입니다. 기본 패키지를 사용 하는 피드는 nuget.org 합니다.
1. **빌드** &mdash; 실행을 `dotnet build --configuration release` 앱의 코드를 컴파일하는 명령입니다. 이 `--configuration` 옵션은 최적화 된 코드는 프로덕션 환경에 배포에 적합 한 버전을 만드는 데 사용 됩니다. 수정 합니다 *BuildConfiguration* 빌드 정의 변수 **변수** 디버그 구성이 필요한 예를 들어 하는 경우를 탭 합니다.
1. **테스트** &mdash; 실행을 `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` 앱의 단위 테스트를 실행할 명령입니다. 단위 테스트는 모든 C# 프로젝트와 일치 하는 내에서 실행 되는 `**/*Tests/*.csproj` glob 패턴입니다. 테스트 결과에 저장 됩니다는 *.trx* 로 지정 된 위치에서 파일을 `--results-directory` 옵션입니다. 모든 테스트에 실패 하는 경우 빌드가 실패 하 고 배포 되지 않습니다.

    > [!NOTE]
    > 단위 테스트 작업을 확인 하려면 수정할 *SimpleFeedReader.Tests\Services\NewsServiceTests.cs* 의도적으로 테스트 중 하나를 중단 합니다. 예를 들어 변경 `Assert.True(result.Count > 0);` 하 `Assert.False(result.Count > 0);` 에 `Returns_News_Stories_Given_Valid_Uri` 메서드. 커밋하고 GitHub에 변경 내용을 푸시하십시오. 빌드가 트리거되고 실패 합니다. 빌드 파이프라인 상태를 변경 **못했습니다**합니다. 변경, 커밋 및 푸시를 다시 되돌립니다. 빌드 성공합니다.

1. **게시** &mdash; 실행 합니다 `dotnet publish --configuration release --output <local_path_on_build_agent>` 명령을 생성 하는 *.zip* 배포할 아티팩트를 사용 하 여 파일. 합니다 `--output` 옵션의 게시 위치를 지정 합니다 *.zip* 파일입니다. 전달 하 여 위치 지정 되어 있는지를 [미리 정의 된 변수](/azure/devops/pipelines/build/variables) 라는 `$(build.artifactstagingdirectory)`합니다. 변수를 확장 하 여 로컬 경로 같은 *c:\agent\_work\1\a*, 빌드 에이전트에서.
1. **아티팩트 게시** &mdash; Publishes 합니다 *.zip* 에서 생성 된 파일을 **게시** 작업. 작업을 허용 합니다 *.zip* 파일 위치는 미리 정의 된 변수는 매개 변수로 `$(build.artifactstagingdirectory)`합니다. 합니다 *.zip* 파일이 라는 폴더로 게시 되 *drop*합니다.

빌드 정의 클릭 **요약** 정의 사용 하 여 빌드 기록을 보려면 링크:

![표시 된 빌드 정의 기록을 스크린 샷](media/cicd/build-definition-summary.png)

결과 페이지에서 고유한 빌드 번호에 해당 하는 링크를 클릭 합니다.

![스크린 샷 빌드 정의 요약 페이지를 보여 주는](media/cicd/build-definition-completed.png)

이 특정 빌드 요약이 표시 됩니다. 클릭 합니다 **아티팩트** 탭을 확인 합니다 *drop* 빌드에서 생성 된 폴더가 나열 됩니다:

![빌드 정의 아티팩트-저장 폴더를 보여 주는 스크린샷](media/cicd/build-definition-artifacts.png)

사용 된 **다운로드** 및 **탐색** 게시 된 아티팩트를 검사 하는 링크입니다.

### <a name="release-pipeline"></a>릴리스 파이프라인

릴리스 파이프라인 이름을 만들어졌으므로 *MyFirstProject ASP.NET Core-CD*:

![스크린 샷 보여 주는 릴리스 파이프라인 개요](media/cicd/release-definition-overview.png)

릴리스 파이프라인의 두 가지 주요 구성 요소를 **아티팩트** 하며 **환경**합니다. 상자를 **아티팩트** 섹션에는 다음 창이 표시 됩니다.

![표시 된 릴리스 파이프라인 아티팩트 스크린 샷](media/cicd/release-definition-artifacts.png)

합니다 **소스 (빌드 정의)** 값이 릴리스 파이프라인은 연결 되는 빌드 정의 나타냅니다. 합니다 *.zip* 빌드 정의의 실행을 성공적으로 생성 된 파일에 제공 됩니다 합니다 *프로덕션* Azure에 배포 하기 위한 환경입니다. 클릭 합니다 *1 단계, 2 개의 태스크가* 링크를 *프로덕션* 릴리스 파이프라인 작업을 확인 하려면 상자, 환경:

![릴리스 파이프라인 작업을 보여 주는 스크린샷](media/cicd/release-definition-tasks.png)

릴리스 파이프라인의 두 가지 작업으로 구성 됩니다. *Azure App Service 슬롯에 배포* 하 고 *Azure App Service-Slot Swap 관리*. 첫 번째 작업을 클릭 하면 다음 작업 구성을 표시 됩니다.

![스크린 샷 보여 주는 릴리스 파이프라인 배포 작업](media/cicd/release-definition-task1.png)

Azure 구독, 서비스 유형, 웹 앱 이름, 리소스 그룹 및 배포 슬롯은 배포 작업에 정의 됩니다. **패키지 또는 폴더가** 보유 하는 텍스트 상자를 *.zip* 파일 경로를 추출 하 고 배포를 *준비* 의 슬롯을 *mywebapp\<고유 수 (_n)\>*  웹 앱입니다.

슬롯 스왑 작업을 클릭 하면 다음 작업 구성을 표시 됩니다.

![스크린 샷 보여 주는 릴리스 파이프라인 슬롯 스왑 작업](media/cicd/release-definition-task2.png)

구독, 리소스 그룹, 서비스 유형, 웹 앱 이름 및 배포 슬롯 세부 정보 제공 됩니다. 합니다 **프로덕션과 교환** 확인란을 선택 합니다. 비트를 배포 하는 결과적으로 *준비* 슬롯은 프로덕션 환경으로 교체 합니다.

## <a name="additional-reading"></a>추가 참조 항목

* [Azure Pipelines를 사용하여 첫 번째 파이프라인 만들기](/azure/devops/pipelines/get-started-yaml)
* [빌드 및.NET Core 프로젝트](/azure/devops/pipelines/languages/dotnet-core)
* [Azure 파이프라인을 사용 하 여 웹 앱 배포](/azure/devops/pipelines/targets/webapp)
