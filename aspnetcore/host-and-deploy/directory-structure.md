---
title: ASP.NET Core 디렉터리 구조
author: guardrex
description: 게시된 ASP.NET Core 앱의 디렉터리 구조에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 06/17/2019
uid: host-and-deploy/directory-structure
ms.openlocfilehash: f1df047decc7a0a6b7dcee57a690c55eea428b05
ms.sourcegitcommit: 28a2874765cefe9eaa068dceb989a978ba2096aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67166973"
---
# <a name="aspnet-core-directory-structure"></a><span data-ttu-id="d8bb5-103">ASP.NET Core 디렉터리 구조</span><span class="sxs-lookup"><span data-stu-id="d8bb5-103">ASP.NET Core directory structure</span></span>

<span data-ttu-id="d8bb5-104">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="d8bb5-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d8bb5-105">*게시* 디렉터리에는 [dotnet 게시](/dotnet/core/tools/dotnet-publish) 명령에 의해 생성된 앱의 배포 가능한 자산이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8bb5-105">The *publish* directory contains the app's deployable assets produced by the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="d8bb5-106">디렉터리에는 다음이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8bb5-106">The directory contains:</span></span>

* <span data-ttu-id="d8bb5-107">애플리케이션 파일</span><span class="sxs-lookup"><span data-stu-id="d8bb5-107">Application files</span></span>
* <span data-ttu-id="d8bb5-108">구성 파일</span><span class="sxs-lookup"><span data-stu-id="d8bb5-108">Configuration files</span></span>
* <span data-ttu-id="d8bb5-109">정적 자산</span><span class="sxs-lookup"><span data-stu-id="d8bb5-109">Static assets</span></span>
* <span data-ttu-id="d8bb5-110">패키지</span><span class="sxs-lookup"><span data-stu-id="d8bb5-110">Packages</span></span>
* <span data-ttu-id="d8bb5-111">런타임([자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)만 해당)</span><span class="sxs-lookup"><span data-stu-id="d8bb5-111">A runtime ([self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) only)</span></span>

| <span data-ttu-id="d8bb5-112">앱 형식</span><span class="sxs-lookup"><span data-stu-id="d8bb5-112">App Type</span></span> | <span data-ttu-id="d8bb5-113">디렉터리 구조</span><span class="sxs-lookup"><span data-stu-id="d8bb5-113">Directory Structure</span></span> |
| -------- | ------------------- |
| [<span data-ttu-id="d8bb5-114">프레임워크 종속 배포</span><span class="sxs-lookup"><span data-stu-id="d8bb5-114">Framework-dependent Deployment</span></span>](/dotnet/core/deploying/#framework-dependent-deployments-fdd) | <ul><li><span data-ttu-id="d8bb5-115">publish&dagger;</span><span class="sxs-lookup"><span data-stu-id="d8bb5-115">publish&dagger;</span></span><ul><li><span data-ttu-id="d8bb5-116">Views&dagger;(MVC 앱, 뷰가 미리 컴파일되지 않은 경우)</span><span class="sxs-lookup"><span data-stu-id="d8bb5-116">Views&dagger; (MVC apps; if views aren't precompiled)</span></span></li><li><span data-ttu-id="d8bb5-117">Pages&dagger;(MVC 또는 Razor 페이지 앱, 페이지가 미리 컴파일되지 않은 경우)</span><span class="sxs-lookup"><span data-stu-id="d8bb5-117">Pages&dagger; (MVC or Razor Pages apps; if pages aren't precompiled)</span></span></li><li><span data-ttu-id="d8bb5-118">wwwroot&dagger;</span><span class="sxs-lookup"><span data-stu-id="d8bb5-118">wwwroot&dagger;</span></span></li><li><span data-ttu-id="d8bb5-119">\*\.dll 파일</span><span class="sxs-lookup"><span data-stu-id="d8bb5-119">\*\.dll files</span></span></li><li><span data-ttu-id="d8bb5-120">{ASSEMBLY NAME}.deps.json</span><span class="sxs-lookup"><span data-stu-id="d8bb5-120">{ASSEMBLY NAME}.deps.json</span></span></li><li><span data-ttu-id="d8bb5-121">{ASSEMBLY NAME}.dll</span><span class="sxs-lookup"><span data-stu-id="d8bb5-121">{ASSEMBLY NAME}.dll</span></span></li><li><span data-ttu-id="d8bb5-122">{ASSEMBLY NAME}.pdb</span><span class="sxs-lookup"><span data-stu-id="d8bb5-122">{ASSEMBLY NAME}.pdb</span></span></li><li><span data-ttu-id="d8bb5-123">{ASSEMBLY NAME}.Views.dll</span><span class="sxs-lookup"><span data-stu-id="d8bb5-123">{ASSEMBLY NAME}.Views.dll</span></span></li><li><span data-ttu-id="d8bb5-124">{ASSEMBLY NAME}.Views.pdb</span><span class="sxs-lookup"><span data-stu-id="d8bb5-124">{ASSEMBLY NAME}.Views.pdb</span></span></li><li><span data-ttu-id="d8bb5-125">{ASSEMBLY NAME}.runtimeconfig.json</span><span class="sxs-lookup"><span data-stu-id="d8bb5-125">{ASSEMBLY NAME}.runtimeconfig.json</span></span></li><li><span data-ttu-id="d8bb5-126">web.config(IIS 배포)</span><span class="sxs-lookup"><span data-stu-id="d8bb5-126">web.config (IIS deployments)</span></span></li></ul></li></ul> |
| [<span data-ttu-id="d8bb5-127">자체 포함 배포</span><span class="sxs-lookup"><span data-stu-id="d8bb5-127">Self-contained Deployment</span></span>](/dotnet/core/deploying/#self-contained-deployments-scd) | <ul><li><span data-ttu-id="d8bb5-128">publish&dagger;</span><span class="sxs-lookup"><span data-stu-id="d8bb5-128">publish&dagger;</span></span><ul><li><span data-ttu-id="d8bb5-129">Views&dagger;(MVC 앱, 뷰가 미리 컴파일되지 않은 경우)</span><span class="sxs-lookup"><span data-stu-id="d8bb5-129">Views&dagger; (MVC apps; if views aren't precompiled)</span></span></li><li><span data-ttu-id="d8bb5-130">Pages&dagger;(MVC 또는 Razor 페이지 앱, 페이지가 미리 컴파일되지 않은 경우)</span><span class="sxs-lookup"><span data-stu-id="d8bb5-130">Pages&dagger; (MVC or Razor Pages apps; if pages aren't precompiled)</span></span></li><li><span data-ttu-id="d8bb5-131">wwwroot&dagger;</span><span class="sxs-lookup"><span data-stu-id="d8bb5-131">wwwroot&dagger;</span></span></li><li><span data-ttu-id="d8bb5-132">\*.dll 파일</span><span class="sxs-lookup"><span data-stu-id="d8bb5-132">\*.dll files</span></span></li><li><span data-ttu-id="d8bb5-133">{ASSEMBLY NAME}.deps.json</span><span class="sxs-lookup"><span data-stu-id="d8bb5-133">{ASSEMBLY NAME}.deps.json</span></span></li><li><span data-ttu-id="d8bb5-134">{ASSEMBLY NAME}.dll</span><span class="sxs-lookup"><span data-stu-id="d8bb5-134">{ASSEMBLY NAME}.dll</span></span></li><li><span data-ttu-id="d8bb5-135">{ASSEMBLY NAME}.exe</span><span class="sxs-lookup"><span data-stu-id="d8bb5-135">{ASSEMBLY NAME}.exe</span></span></li><li><span data-ttu-id="d8bb5-136">{ASSEMBLY NAME}.pdb</span><span class="sxs-lookup"><span data-stu-id="d8bb5-136">{ASSEMBLY NAME}.pdb</span></span></li><li><span data-ttu-id="d8bb5-137">{ASSEMBLY NAME}.Views.dll</span><span class="sxs-lookup"><span data-stu-id="d8bb5-137">{ASSEMBLY NAME}.Views.dll</span></span></li><li><span data-ttu-id="d8bb5-138">{ASSEMBLY NAME}.Views.pdb</span><span class="sxs-lookup"><span data-stu-id="d8bb5-138">{ASSEMBLY NAME}.Views.pdb</span></span></li><li><span data-ttu-id="d8bb5-139">{ASSEMBLY NAME}.runtimeconfig.json</span><span class="sxs-lookup"><span data-stu-id="d8bb5-139">{ASSEMBLY NAME}.runtimeconfig.json</span></span></li><li><span data-ttu-id="d8bb5-140">web.config(IIS 배포)</span><span class="sxs-lookup"><span data-stu-id="d8bb5-140">web.config (IIS deployments)</span></span></li></ul></li></ul> |

<span data-ttu-id="d8bb5-141">&dagger;디렉터리를 나타냄</span><span class="sxs-lookup"><span data-stu-id="d8bb5-141">&dagger;Indicates a directory</span></span>

<span data-ttu-id="d8bb5-142">*publish* 디렉터리는 배포의 *애플리케이션 기본 경로*라고도 하는 *콘텐츠 루트 경로*를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="d8bb5-142">The *publish* directory represents the *content root path*, also called the *application base path*, of the deployment.</span></span> <span data-ttu-id="d8bb5-143">서버에 배포된 앱의 *publish* 디렉터리에 어떤 이름을 지정하더라도 해당 위치는 호스트된 앱에 대한 서버의 실제 경로로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8bb5-143">Whatever name is given to the *publish* directory of the deployed app on the server, its location serves as the server's physical path to the hosted app.</span></span>

<span data-ttu-id="d8bb5-144">*wwwroot* 디렉터리(있는 경우)에는 정적 자산만 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="d8bb5-144">The *wwwroot* directory, if present, only contains static assets.</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d8bb5-145">*로그* 폴더를 만드는 것은 [ASP.NET Core 모듈 향상된 디버그 로깅](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="d8bb5-145">Creating a *Logs* folder is useful for [ASP.NET Core Module enhanced debug logging](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="d8bb5-146">`<handlerSetting>` 값에 제공된 경로에 있는 폴더는 모듈에서 자동으로 생성되지 않으며 모듈이 디버그 로그를 작성할 수 있도록 배포에 미리 존재해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d8bb5-146">Folders in the path provided to the `<handlerSetting>` value aren't created by the module automatically and should pre-exist in the deployment to allow the module to write the debug log.</span></span>

<span data-ttu-id="d8bb5-147">다음 두 가지 방법 중 하나를 사용하여 *Logs* 디렉터리를 배포용으로 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d8bb5-147">A *Logs* directory can be created for the deployment using one of the following two approaches:</span></span>

* <span data-ttu-id="d8bb5-148">다음 `<Target>` 요소를 프로젝트 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d8bb5-148">Add the following `<Target>` element to the project file:</span></span>

   ```xml
   <Target Name="CreateLogsFolder" AfterTargets="Publish">
     <MakeDir Directories="$(PublishDir)Logs" 
              Condition="!Exists('$(PublishDir)Logs')" />
     <WriteLinesToFile File="$(PublishDir)Logs\.log" 
                       Lines="Generated file" 
                       Overwrite="True" 
                       Condition="!Exists('$(PublishDir)Logs\.log')" />
   </Target>
   ```

   <span data-ttu-id="d8bb5-149">`<MakeDir>` 요소는 게시된 출력에 빈 *Logs* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d8bb5-149">The `<MakeDir>` element creates an empty *Logs* folder in the published output.</span></span> <span data-ttu-id="d8bb5-150">이 요소는 `PublishDir` 속성을 사용하여 폴더를 만들 대상 위치를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d8bb5-150">The element uses the `PublishDir` property to determine the target location for creating the folder.</span></span> <span data-ttu-id="d8bb5-151">웹 배포와 같은 여러 배포 방법에서는 배포 중에 빈 폴더를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="d8bb5-151">Several deployment methods, such as Web Deploy, skip empty folders during deployment.</span></span> <span data-ttu-id="d8bb5-152">`<WriteLinesToFile>` 요소는 파일이 서버에 배포되도록 *Logs* 폴더에 파일을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="d8bb5-152">The `<WriteLinesToFile>` element generates a file in the *Logs* folder, which guarantees deployment of the folder to the server.</span></span> <span data-ttu-id="d8bb5-153">대상 폴더에 대한 쓰기 권한이 작업자 프로세스에 없는 경우 이 방법을 사용한 폴더 만들기가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="d8bb5-153">Folder creation using this approach fails if the worker process doesn't have write access to the target folder.</span></span>

* <span data-ttu-id="d8bb5-154">배포 시 서버에서 *Logs* 디렉터리를 실제로 만드세요.</span><span class="sxs-lookup"><span data-stu-id="d8bb5-154">Physically create the *Logs* directory on the server in the deployment.</span></span>

<span data-ttu-id="d8bb5-155">배포 디렉터리에는 읽기/실행 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="d8bb5-155">The deployment directory requires Read/Execute permissions.</span></span> <span data-ttu-id="d8bb5-156">*Logs* 디렉터리에는 읽기/쓰기 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="d8bb5-156">The *Logs* directory requires Read/Write permissions.</span></span> <span data-ttu-id="d8bb5-157">파일이 작성되는 추가 디렉터리에는 읽기/쓰기 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="d8bb5-157">Additional directories where files are written require Read/Write permissions.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="d8bb5-158">추가 자료</span><span class="sxs-lookup"><span data-stu-id="d8bb5-158">Additional resources</span></span>

* [<span data-ttu-id="d8bb5-159">dotnet publish</span><span class="sxs-lookup"><span data-stu-id="d8bb5-159">dotnet publish</span></span>](/dotnet/core/tools/dotnet-publish)
* [<span data-ttu-id="d8bb5-160">.NET Core 애플리케이션 배포</span><span class="sxs-lookup"><span data-stu-id="d8bb5-160">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* [<span data-ttu-id="d8bb5-161">대상 프레임워크</span><span class="sxs-lookup"><span data-stu-id="d8bb5-161">Target frameworks</span></span>](/dotnet/standard/frameworks)
* [<span data-ttu-id="d8bb5-162">.NET Core RID 카탈로그</span><span class="sxs-lookup"><span data-stu-id="d8bb5-162">.NET Core RID Catalog</span></span>](/dotnet/core/rid-catalog)
