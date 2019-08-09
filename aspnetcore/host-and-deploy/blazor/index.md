---
title: ASP.NET Core Blazor 호스트 및 배포
author: guardrex
description: Blazor 앱을 호스트하고 배포하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 06/14/2019
uid: host-and-deploy/blazor/index
ms.openlocfilehash: d18abbf33c71dca5130bfc6b503b46c1d5bce537
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68913932"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a><span data-ttu-id="14df6-103">ASP.NET Core Blazor 호스트 및 배포</span><span class="sxs-lookup"><span data-stu-id="14df6-103">Host and deploy ASP.NET Core Blazor</span></span>

<span data-ttu-id="14df6-104">작성자: [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) 및 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="14df6-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="14df6-105">앱 게시</span><span class="sxs-lookup"><span data-stu-id="14df6-105">Publish the app</span></span>

<span data-ttu-id="14df6-106">앱은 릴리스 구성으로 배포하기 위해 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="14df6-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="14df6-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="14df6-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="14df6-108">탐색 모음에서 **빌드** >  **{APPLICATION} 게시**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="14df6-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="14df6-109">*publish target*을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="14df6-109">Select the *publish target*.</span></span> <span data-ttu-id="14df6-110">로컬로 게시하려면 **폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="14df6-110">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="14df6-111">**폴더 선택** 필드에서 기본 위치를 그대로 사용하거나 다른 위치를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="14df6-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="14df6-112">**게시** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="14df6-112">Select the **Publish** button.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="14df6-113">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="14df6-113">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="14df6-114">[dotnet publish](/dotnet/core/tools/dotnet-publish) 명령을 사용하여 릴리스 구성으로 앱을 게시합니다.</span><span class="sxs-lookup"><span data-stu-id="14df6-114">Use the [dotnet publish](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```console
dotnet publish -c Release
```

---

<span data-ttu-id="14df6-115">앱을 게시하면 배포할 자산을 만들기 전에 프로젝트 종속성의 [복원](/dotnet/core/tools/dotnet-restore)과 프로젝트의 [빌드](/dotnet/core/tools/dotnet-build)가 트리거됩니다.</span><span class="sxs-lookup"><span data-stu-id="14df6-115">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="14df6-116">빌드 프로세스의 일부로 앱 다운로드 크기와 로드 시간을 줄이기 위해 사용하지 않는 메서드와 어셈블리를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="14df6-116">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="14df6-117">Blazor 클라이언트 쪽 앱은 */bin/Release/{TARGET FRAMEWORK}/publish/{ASSEMBLY NAME}/dist* 폴더에 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="14df6-117">A Blazor client-side app is published to the */bin/Release/{TARGET FRAMEWORK}/publish/{ASSEMBLY NAME}/dist* folder.</span></span> <span data-ttu-id="14df6-118">Blazor 서버 쪽 앱은 */bin/Release/{TARGET FRAMEWORK}/publish* 폴더에 게시됩니다.</span><span class="sxs-lookup"><span data-stu-id="14df6-118">A Blazor server-side app is published to the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span>

<span data-ttu-id="14df6-119">이 폴더의 자산은 웹 서버에 배포됩니다.</span><span class="sxs-lookup"><span data-stu-id="14df6-119">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="14df6-120">배포는 사용 중인 개발 도구에 따라 수동 프로세스일 수도 있고 자동 프로세스일 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="14df6-120">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="deployment"></a><span data-ttu-id="14df6-121">배포</span><span class="sxs-lookup"><span data-stu-id="14df6-121">Deployment</span></span>

<span data-ttu-id="14df6-122">배포 지침은 다음 항목을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="14df6-122">For deployment guidance, see the following topics:</span></span>

* <xref:host-and-deploy/blazor/client-side>
* <xref:host-and-deploy/blazor/server-side>

## <a name="blazor-serverless-hosting-with-azure-storage"></a><span data-ttu-id="14df6-123">Azure Storage를 사용하여 Blazor 서버리스 호스팅</span><span class="sxs-lookup"><span data-stu-id="14df6-123">Blazor serverless hosting with Azure Storage</span></span>

<span data-ttu-id="14df6-124">[Azure Storage](https://azure.microsoft.com/services/storage/)의 Blazor 클라이언트 쪽 앱은 스토리지 컨테이너에서 직접 정적 콘텐츠로 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="14df6-124">Blazor client-side apps can be served from [Azure Storage](https://azure.microsoft.com/services/storage/) as static content directly from a storage container.</span></span>

<span data-ttu-id="14df6-125">자세한 내용은 [ASP.NET Core Blazor 클라이언트 쪽 호스트 및 배포(독립 실행형 배포): Azure Storage](xref:host-and-deploy/blazor/client-side#azure-storage)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="14df6-125">For more information, see [Host and deploy ASP.NET Core Blazor client-side (Standalone deployment): Azure Storage](xref:host-and-deploy/blazor/client-side#azure-storage).</span></span>
