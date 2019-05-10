---
title: 도구 및 다운로드-ASP.NET Core 및 Azure를 사용 하 여 DevOps
author: CamSoper
description: ASP.NET Core 및 Azure를 사용 하 여 DevOps에 필요한 다운로드 및 도구입니다.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: ae4827f735274405021e5ee539d1029b7ddb9553
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64891990"
---
# <a name="tools-and-downloads"></a>도구 및 다운로드

Azure에 프로 비전 및와 같은 리소스를 관리 하기 위한 몇 가지 인터페이스를 [Azure portal](https://portal.azure.com)를 [Azure CLI](/cli/azure/)를 [Azure PowerShell](/powershell/azure/overview), [Azure 클라우드 셸](https://shell.azure.com/bash), 및 Visual Studio입니다. 이 가이드는 최소 방식을 사용 하 고는 데 필요한 단계를 줄이기 위해 가능 하면 Azure Cloud Shell를 사용 합니다. 그러나 일부에 대 한 Azure portal은 사용 해야 합니다.

## <a name="prerequisites"></a>전제 조건

다음 구독이 필요 합니다.

* Azure &mdash; 계정이 없다면 [무료 평가판 받기](https://azure.microsoft.com/free/)합니다.
* Azure DevOps 서비스 &mdash; Azure DevOps 구독 및 조직 4 장의 만들어집니다.
* GitHub &mdash; 계정이 없다면 [무료로 등록](https://github.com/join)합니다.

다음 도구가 필요 합니다.

* [Git](https://git-scm.com/downloads) &mdash; Git에 대 한 기본적인 이해는이 가이드에 권장 됩니다. 검토 합니다 [Git 설명서](https://git-scm.com/doc), 특히 [git 원격](https://git-scm.com/docs/git-remote) 하 고 [git 푸시](https://git-scm.com/docs/git-push)합니다.
* [.NET core SDK](https://www.microsoft.com/net/download/) &mdash; 2.1.300 버전을 빌드하고 샘플 앱을 실행 하려면 나중에 필요 합니다. Visual Studio가 설치 된 경우는 **.NET Core 플랫폼 간 개발** 워크 로드를.NET Core SDK가 이미 설치 되어 있습니다.

    .NET Core SDK 설치를 확인 합니다. 명령 셸을 열고 다음 명령을 실행 합니다.

    ```console
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a>권장된 도구 (Windows만 해당)

* [Visual Studio](https://visualstudio.microsoft.com)의 강력한 Azure 도구 GUI에 대 한 제공 대부분의 기능에이 가이드에서 설명 합니다. 무료 Visual Studio Community Edition을 비롯 한 모든 버전의 Visual Studio 작동 합니다. 자습서를 사용 하 여와 Visual Studio 없이 개발, 배포 및 DevOps를 보여 주기 위해 기록 됩니다.

  Visual Studio에는 다음이 있는지 확인 [워크 로드](/visualstudio/install/modify-visual-studio) 설치:

  * ASP.NET 및 웹 개발
  * Azure 개발
  * .NET Core 플랫폼 간 개발
