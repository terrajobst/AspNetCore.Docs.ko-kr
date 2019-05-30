---
title: Blazor 호스트 및 배포
author: guardrex
description: Blazor 앱을 호스트하고 배포하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/23/2019
uid: host-and-deploy/blazor/index
ms.openlocfilehash: 0fc7643c65b93a63d7a594d35e4013eab76e9db8
ms.sourcegitcommit: 4d05e30567279072f1b070618afe58ae1bcefd5a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2019
ms.locfileid: "66376379"
---
# <a name="host-and-deploy-blazor"></a>Blazor 호스트 및 배포

작성자: [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) 및 [Daniel Roth](https://github.com/danroth27)

## <a name="publish-the-app"></a>앱 게시

앱은 릴리스 구성으로 배포하기 위해 게시됩니다.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 탐색 모음에서 **빌드** >  **{APPLICATION} 게시**를 선택합니다.
1. *publish target*을 선택합니다. 로컬로 게시하려면 **폴더**를 선택합니다.
1. **폴더 선택** 필드에서 기본 위치를 그대로 사용하거나 다른 위치를 지정합니다. **게시** 단추를 선택합니다.

# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[Visual Studio Code/.NET Core CLI](#tab/visual-studio-code+netcore-cli)

[dotnet publish](/dotnet/core/tools/dotnet-publish) 명령을 사용하여 릴리스 구성으로 앱을 게시합니다.

```console
dotnet publish -c Release
```

---

앱을 게시하면 배포할 자산을 만들기 전에 프로젝트 종속성의 [복원](/dotnet/core/tools/dotnet-restore)과 프로젝트의 [빌드](/dotnet/core/tools/dotnet-build)가 트리거됩니다. 빌드 프로세스의 일부로 앱 다운로드 크기와 로드 시간을 줄이기 위해 사용하지 않는 메서드와 어셈블리를 제거합니다.

Blazor 클라이언트 쪽 앱은 */bin/Release/{TARGET FRAMEWORK}/publish/{ASSEMBLY NAME}/dist* 폴더에 게시됩니다. Blazor 서버 쪽 앱은 */bin/Release/{TARGET FRAMEWORK}/publish* 폴더에 게시됩니다.

이 폴더의 자산은 웹 서버에 배포됩니다. 배포는 사용 중인 개발 도구에 따라 수동 프로세스일 수도 있고 자동 프로세스일 수도 있습니다.

## <a name="deployment"></a>배포

배포 지침은 다음 항목을 참조하세요.

* <xref:host-and-deploy/blazor/client-side>
* <xref:host-and-deploy/blazor/server-side>

## <a name="blazor-serverless-hosting-with-azure-storage"></a>Azure Storage를 사용하여 Blazor 서버리스 호스팅

[Azure Storage](https://azure.microsoft.com/services/storage/)의 Blazor 클라이언트 쪽 앱은 스토리지 컨테이너에서 직접 정적 콘텐츠로 제공됩니다.

자세한 내용은 [Blazor 클라이언트 쪽 호스트 및 배포(독립 실행형 배포): Azure Storage](xref:host-and-deploy/blazor/client-side#azure-storage)를 참조하세요.
