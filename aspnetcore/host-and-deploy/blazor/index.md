---
title: Blazor 호스트 및 배포
author: guardrex
description: Blazor 앱을 호스트하고 배포하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/18/2019
uid: host-and-deploy/blazor/index
ms.openlocfilehash: 774fbb6fdaab14a015db4fb39de2e1ea73a1837b
ms.sourcegitcommit: eb784a68219b4829d8e50c8a334c38d4b94e0cfa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59982642"
---
# <a name="host-and-deploy-blazor"></a>Blazor 호스트 및 배포

작성자: [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) 및 [Daniel Roth](https://github.com/danroth27)

## <a name="publish-the-app"></a>앱 게시

앱은 [dotnet publish](/dotnet/core/tools/dotnet-publish) 명령을 사용하여 릴리스 구성으로 배포하기 위해 게시됩니다. IDE(통합 개발 환경)는 기본 제공 게시 기능을 사용하여 자동으로 `dotnet publish` 명령의 실행을 처리할 수 있으므로, 사용 중인 개발 도구에 따라 명령 프롬프트에서 수동으로 명령을 실행할 필요가 없을 수도 있습니다.

```console
dotnet publish -c Release
```

`dotnet publish`는 배포할 자산을 만들기 전에 프로젝트의 종속성을 [복원](/dotnet/core/tools/dotnet-restore)하고 프로젝트를 [빌드](/dotnet/core/tools/dotnet-build)합니다. 빌드 프로세스의 일부로 앱 다운로드 크기와 로드 시간을 줄이기 위해 사용하지 않는 메서드와 어셈블리를 제거합니다. 배포는 */bin/Release/{대상 프레임워크}/publish* 폴더에 생성됩니다.

*publish* 폴더의 자산은 웹 서버에 배포됩니다. 배포는 사용 중인 개발 도구에 따라 수동 프로세스일 수도 있고 자동 프로세스일 수도 있습니다.

## <a name="deployment"></a>배포

배포 지침은 다음 항목을 참조하세요.

* <xref:host-and-deploy/blazor/client-side>
* <xref:host-and-deploy/blazor/server-side>
