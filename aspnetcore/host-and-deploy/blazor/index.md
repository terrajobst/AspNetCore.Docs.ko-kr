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
# <a name="host-and-deploy-blazor"></a><span data-ttu-id="b0dc6-103">Blazor 호스트 및 배포</span><span class="sxs-lookup"><span data-stu-id="b0dc6-103">Host and deploy Blazor</span></span>

<span data-ttu-id="b0dc6-104">작성자: [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) 및 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="b0dc6-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="b0dc6-105">앱 게시</span><span class="sxs-lookup"><span data-stu-id="b0dc6-105">Publish the app</span></span>

<span data-ttu-id="b0dc6-106">앱은 [dotnet publish](/dotnet/core/tools/dotnet-publish) 명령을 사용하여 릴리스 구성으로 배포하기 위해 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc6-106">Apps are published for deployment in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="b0dc6-107">IDE(통합 개발 환경)는 기본 제공 게시 기능을 사용하여 자동으로 `dotnet publish` 명령의 실행을 처리할 수 있으므로, 사용 중인 개발 도구에 따라 명령 프롬프트에서 수동으로 명령을 실행할 필요가 없을 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc6-107">An integrated development environment (IDE) may handle executing the `dotnet publish` command automatically using its built-in publishing features, so it might not be necessary to manually execute the command from a command prompt depending on the development tools in use.</span></span>

```console
dotnet publish -c Release
```

<span data-ttu-id="b0dc6-108">`dotnet publish`는 배포할 자산을 만들기 전에 프로젝트의 종속성을 [복원](/dotnet/core/tools/dotnet-restore)하고 프로젝트를 [빌드](/dotnet/core/tools/dotnet-build)합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc6-108">`dotnet publish` triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="b0dc6-109">빌드 프로세스의 일부로 앱 다운로드 크기와 로드 시간을 줄이기 위해 사용하지 않는 메서드와 어셈블리를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc6-109">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span> <span data-ttu-id="b0dc6-110">배포는 */bin/Release/{대상 프레임워크}/publish* 폴더에 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc6-110">The deployment is created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span>

<span data-ttu-id="b0dc6-111">*publish* 폴더의 자산은 웹 서버에 배포됩니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc6-111">The assets in the *publish* folder are deployed to the web server.</span></span> <span data-ttu-id="b0dc6-112">배포는 사용 중인 개발 도구에 따라 수동 프로세스일 수도 있고 자동 프로세스일 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b0dc6-112">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="deployment"></a><span data-ttu-id="b0dc6-113">배포</span><span class="sxs-lookup"><span data-stu-id="b0dc6-113">Deployment</span></span>

<span data-ttu-id="b0dc6-114">배포 지침은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="b0dc6-114">For deployment guidance, see the following topics:</span></span>

* <xref:host-and-deploy/blazor/client-side>
* <xref:host-and-deploy/blazor/server-side>
