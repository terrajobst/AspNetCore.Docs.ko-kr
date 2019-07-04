---
title: Visual Studio를 사용하여 Azure에 ASP.NET Core 앱 게시
author: rick-anderson
description: Visual Studio를 사용하여 Azure App Service에 ASP.NET Core 앱을 게시하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2018
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: c0251032d83349d4d3925d1ec594c9230ed6ad2d
ms.sourcegitcommit: 6d9cf728465cdb0de1037633a8b7df9a8989cccb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67207650"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a>Visual Studio를 사용하여 Azure에 ASP.NET Core 앱 게시

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

macOS에서 작업하고 있는 경우 [Mac용 Visual Studio에서 Azure에 게시](https://blog.xamarin.com/publish-azure-visual-studio-mac/)를 참조하세요.

App Service 배포 문제를 해결하려면 <xref:host-and-deploy/azure-apps/troubleshoot>을 참조하세요.

## <a name="set-up"></a>설치

* 계정이 없는 경우 [체험 Azure 계정](https://azure.microsoft.com/free/dotnet/)을 엽니다. 

## <a name="create-a-web-app"></a>웹앱 만들기

Visual Studio 시작 페이지에서 **파일 > 새로 만들기 > 프로젝트...** 를 선택합니다.

![파일 메뉴](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

**새 프로젝트** 대화 상자를 완료합니다.

* 왼쪽 창에서 **.NET Core**를 선택합니다.
* 가운데 창에서 **ASP.NET Core 웹 애플리케이션**을 선택합니다.
* **확인**을 선택합니다.

![새 프로젝트 대화 상자](publish-to-azure-webapp-using-vs/_static/new_prj.png)

**새 ASP.NET Core 웹 애플리케이션** 대화 상자에서:

* **웹 애플리케이션**을 선택합니다.
* **인증 변경**을 선택합니다.

![새 프로젝트 대화 상자](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

**인증 변경** 대화 상자가 나타납니다. 

* **개별 사용자 계정**을 선택합니다.
* **확인**을 선택하여 **새 ASP.NET Core 웹 애플리케이션**으로 돌아간 다음 다시 **확인**을 선택합니다.

![새 ASP.NET Core 웹 인증 대화 상자](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

Visual Studio는 솔루션을 만듭니다.

## <a name="run-the-app"></a>앱 실행

* Ctrl+F5를 눌러 프로젝트를 실행합니다.
* **정보** 및 **연락처** 링크를 테스트합니다.

![localhost의 Microsoft Edge에서 열린 웹 애플리케이션](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a>사용자 등록

* **등록**을 선택하고 새 사용자를 등록합니다. 가상의 전자 메일 주소를 사용할 수 있습니다. 제출하면 페이지에 다음과 같은 오류가 표시됩니다.

    *"내부 서버 오류: 요청을 처리하는 동안 데이터베이스 작업이 실패했습니다. SQL 예외: 데이터베이스를 열 수 없습니다. 애플리케이션 DB 컨텍스트에 대한 기존 마이그레이션 적용으로 이 문제를 해결할 수 있습니다.”*
* **마이그레이션 적용**을 선택한 다음 페이지가 업데이트되면 페이지를 새로 고칩니다.

![내부 서버 오류: 요청을 처리하는 동안 데이터베이스 작업이 실패했습니다. SQL 예외: 데이터베이스를 열 수 없습니다. 애플리케이션 DB 컨텍스트에 대한 기존 마이그레이션 적용으로 이 문제를 해결할 수 있습니다.](publish-to-azure-webapp-using-vs/_static/mig.png)

앱은 새 사용자를 등록하는 데 사용한 전자 메일 및 **로그아웃** 링크를 표시합니다.

![Microsoft Edge에서 열린 웹 애플리케이션. 레지스터 링크는 텍스트 Hello email@domain.com으로 교체됩니다.](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a>Azure에 앱 배포

솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시...** 를 선택합니다.

![강조 표시된 게시 링크로 열린 바로 가기 메뉴](publish-to-azure-webapp-using-vs/_static/pub.png)

**게시** 대화 상자에서:

* **Microsoft Azure App Service**를 선택합니다.
* 기어 아이콘을 선택한 다음 **프로필 만들기**를 선택합니다.
* **프로필 만들기**를 선택합니다.

![게시 대화 상자](publish-to-azure-webapp-using-vs/_static/maas1.png)

### <a name="create-azure-resources"></a>Azure 리소스 만들기

**App Service 만들기** 대화 상자가 나타납니다.

* 구독을 입력합니다.
* **앱 이름**, **리소스 그룹** 및 **App Service 계획** 항목 필드가 채워집니다. 이러한 이름을 유지하거나 변경할 수 있습니다.

![App Service 대화 상자](publish-to-azure-webapp-using-vs/_static/newrg1.png)

* **서비스** 탭을 선택하여 새 데이터베이스를 만듭니다.

* 녹색 **+** 아이콘을 선택하여 새 SQL Database를 만듭니다.

![새 SQL Database](publish-to-azure-webapp-using-vs/_static/sql.png)

* **SQL Database 구성** 대화 상자에서 **새로 만들기...** 를 선택하여 새 데이터베이스 서버를 만듭니다.

![새 SQL Database 및 서버](publish-to-azure-webapp-using-vs/_static/conf.png)

**SQL Server 구성** 대화 상자가 나타납니다.

* 관리자 사용자 이름 및 암호를 입력한 다음 **확인**을 선택합니다. 기본 **서버 이름**을 유지할 수 있습니다. 

> [!NOTE]
> "관리자"는 관리자 사용자 이름으로 사용할 수 없습니다.

![SQL Server 구성 대화 상자](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

* **확인**을 선택합니다.

Visual Studio가 **App Service 만들기** 대화 상자로 돌아갑니다.

* **App Service 만들기** 대화 상자에서 **만들기**를 선택합니다.

![SQL Database 구성 대화 상자](publish-to-azure-webapp-using-vs/_static/conf_final.png)

Visual Studio는 Azure에서 웹앱 및 SQL Server를 만듭니다. 이 단계는 몇 분 정도 걸릴 수 있습니다. 만든 리소스에 대한 자세한 내용은 [추가 리소스](#additional-resources)를 참조하세요.

배포가 완료되면 **설정**을 선택합니다.

![SQL Server 구성 대화 상자](publish-to-azure-webapp-using-vs/_static/set.png)

**게시** 대화 상자의 **설정** 페이지에서:

* **데이터베이스**를 확장하고 **런타임 시 이 연결 문자열 사용**을 선택합니다.
* **Entity Framework 마이그레이션**을 확장하고 **게시에 이 마이그레이션 적용**을 선택합니다.

* **저장**을 선택합니다. Visual Studio가 **게시** 대화 상자로 돌아갑니다. 

![게시 대화 상자: 설정 패널](publish-to-azure-webapp-using-vs/_static/pubs.png)

**게시**를 클릭합니다. Visual Studio는 Azure에 앱을 게시합니다. 배포가 완료되면 앱이 브라우저에서 열립니다.

### <a name="test-your-app-in-azure"></a>Azure에서 앱 테스트

* **정보** 및 **연락처** 링크 테스트

* 새 사용자 등록

![Azure App Service의 Microsoft Edge에서 열린 웹 애플리케이션](publish-to-azure-webapp-using-vs/_static/register.png)

### <a name="update-the-app"></a>앱 업데이트

* *Pages/About.cshtml* Razor 페이지를 편집하고 내용을 변경합니다. 예를 들어 단락을 수정하여 “Hello ASP.NET Core!” 문구를 표시할 수 있습니다.

    [!code-html[About](publish-to-azure-webapp-using-vs/sample/about.cshtml?highlight=9&range=1-9)]

* 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시...** 를 선택합니다.

![강조 표시된 게시 링크로 열린 바로 가기 메뉴](publish-to-azure-webapp-using-vs/_static/pub.png)

* 앱이 게시된 후 변경 내용이 Azure에서 제공되는지 확인합니다.

![작업이 완료되었는지 확인합니다.](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a>정리

앱 테스트를 완료하면 [Azure Portal](https://portal.azure.com/)로 이동하고 앱을 삭제합니다.

* **리소스 그룹**을 선택한 다음 만든 리소스 그룹을 선택합니다.

![Azure Portal: 사이드바 메뉴에 있는 리소스 그룹](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* **리소스 그룹** 페이지에서 **삭제**를 선택합니다.

![Azure Portal: 리소스 그룹 페이지](publish-to-azure-webapp-using-vs/_static/rgd.png)

* 리소스 그룹의 이름을 입력하고 **삭제**를 선택합니다. 이 자습서에서 만든 앱과 다른 모든 리소스는 이제 Azure에서 삭제됩니다.

### <a name="next-steps"></a>다음 단계

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a>추가 자료

* Visual Studio Code는 [게시 프로필](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles)을 참조하세요.
* [Azure App Service](/azure/app-service/app-service-web-overview)
* [Azure 리소스 그룹](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [Azure SQL Database](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:host-and-deploy/azure-apps/troubleshoot>
