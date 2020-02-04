---
title: ASP.NET Core 디렉터리 구조
author: guardrex
description: 게시된 ASP.NET Core 앱의 디렉터리 구조에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 01/28/2020
uid: host-and-deploy/directory-structure
ms.openlocfilehash: ba5cb96dfdcdca10034299e3bbe662ce056af791
ms.sourcegitcommit: fe41cff0b99f3920b727286944e5b652ca301640
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76870268"
---
# <a name="aspnet-core-directory-structure"></a><span data-ttu-id="ba911-103">ASP.NET Core 디렉터리 구조</span><span class="sxs-lookup"><span data-stu-id="ba911-103">ASP.NET Core directory structure</span></span>

<span data-ttu-id="ba911-104">[Luke Latham](https://github.com/guardrex)으로</span><span class="sxs-lookup"><span data-stu-id="ba911-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ba911-105">*게시* 디렉터리에는 [dotnet 게시](/dotnet/core/tools/dotnet-publish) 명령에 의해 생성된 앱의 배포 가능한 자산이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ba911-105">The *publish* directory contains the app's deployable assets produced by the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="ba911-106">디렉터리에는 다음이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="ba911-106">The directory contains:</span></span>

* <span data-ttu-id="ba911-107">애플리케이션 파일</span><span class="sxs-lookup"><span data-stu-id="ba911-107">Application files</span></span>
* <span data-ttu-id="ba911-108">구성 파일</span><span class="sxs-lookup"><span data-stu-id="ba911-108">Configuration files</span></span>
* <span data-ttu-id="ba911-109">정적 자산</span><span class="sxs-lookup"><span data-stu-id="ba911-109">Static assets</span></span>
* <span data-ttu-id="ba911-110">패키지</span><span class="sxs-lookup"><span data-stu-id="ba911-110">Packages</span></span>
* <span data-ttu-id="ba911-111">런타임([자체 포함 배포](/dotnet/core/deploying/#self-contained-deployments-scd)만 해당)</span><span class="sxs-lookup"><span data-stu-id="ba911-111">A runtime ([self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) only)</span></span>

| <span data-ttu-id="ba911-112">앱 형식</span><span class="sxs-lookup"><span data-stu-id="ba911-112">App Type</span></span> | <span data-ttu-id="ba911-113">디렉터리 구조</span><span class="sxs-lookup"><span data-stu-id="ba911-113">Directory Structure</span></span> |
| -------- | ------------------- |
| [<span data-ttu-id="ba911-114">프레임워크 종속 실행 파일</span><span class="sxs-lookup"><span data-stu-id="ba911-114">Framework-dependent Executable (FDE)</span></span>](/dotnet/core/deploying/#framework-dependent-executables-fde) | <ul><li><span data-ttu-id="ba911-115">publish&dagger;</span><span class="sxs-lookup"><span data-stu-id="ba911-115">publish&dagger;</span></span><ul><li><span data-ttu-id="ba911-116">Views&dagger; MVC 앱, 뷰가 미리 컴파일되지 않은 경우</span><span class="sxs-lookup"><span data-stu-id="ba911-116">Views&dagger; MVC apps; if views aren't precompiled</span></span></li><li><span data-ttu-id="ba911-117">Pages&dagger; MVC 또는 Razor 페이지 앱, 페이지가 미리 컴파일되지 않은 경우</span><span class="sxs-lookup"><span data-stu-id="ba911-117">Pages&dagger; MVC or Razor Pages apps, if pages aren't precompiled</span></span></li><li><span data-ttu-id="ba911-118">wwwroot&dagger;</span><span class="sxs-lookup"><span data-stu-id="ba911-118">wwwroot&dagger;</span></span></li><li><span data-ttu-id="ba911-119">Windows용 ‘.dll 파일</li><li>{ASSEMBLY NAME}.deps.json</li><li>{ASSEMBLY NAME}.dll</li><li>{ASSEMBLY NAME}{.EXTENSION} *.exe* 확장, macOS 또는 Linux용 확장 없음</li><li>{ASSEMBLY NAME}.pdb</li><li>{ASSEMBLY NAME}.Views.dll</li><li>{ASSEMBLY NAME}.Views.pdb</li><li>{ASSEMBLY NAME}.runtimeconfig.json</li><li>web.config(IIS 배포)</li><li>createdump([Linux createdump 유틸리티](https://github.com/dotnet/coreclr/blob/master/Documentation/botr/xplat-minidump-generation.md#configurationpolicy))</li><li>’.so(Linux 공유 개체 라이브러리) </span><span class="sxs-lookup"><span data-stu-id="ba911-119">*.dll files</li><li>{ASSEMBLY NAME}.deps.json</li><li>{ASSEMBLY NAME}.dll</li><li>{ASSEMBLY NAME}{.EXTENSION} *.exe* extension on Windows, no extension on macOS or Linux</li><li>{ASSEMBLY NAME}.pdb</li><li>{ASSEMBLY NAME}.Views.dll</li><li>{ASSEMBLY NAME}.Views.pdb</li><li>{ASSEMBLY NAME}.runtimeconfig.json</li><li>web.config (IIS deployments)</li><li>createdump ([Linux createdump utility](https://github.com/dotnet/coreclr/blob/master/Documentation/botr/xplat-minidump-generation.md#configurationpolicy))</li><li>*.so (Linux shared object library)</span></span></li><li><span data-ttu-id="ba911-120">‘.a(macOS 보관)</li><li>’.dylib(macOS 동적 라이브러리) </span><span class="sxs-lookup"><span data-stu-id="ba911-120">*.a (macOS archive)</li><li>*.dylib (macOS dynamic library)</span></span></li></ul></li></ul> |
| [<span data-ttu-id="ba911-121">SCD(자체 포함 배포)</span><span class="sxs-lookup"><span data-stu-id="ba911-121">Self-contained Deployment (SCD)</span></span>](/dotnet/core/deploying/#self-contained-deployments-scd) | <ul><li><span data-ttu-id="ba911-122">publish&dagger;</span><span class="sxs-lookup"><span data-stu-id="ba911-122">publish&dagger;</span></span><ul><li><span data-ttu-id="ba911-123">Views&dagger; MVC 앱, 뷰가 미리 컴파일되지 않은 경우</span><span class="sxs-lookup"><span data-stu-id="ba911-123">Views&dagger; MVC apps, if views aren't precompiled</span></span></li><li><span data-ttu-id="ba911-124">Pages&dagger; MVC 또는 Razor 페이지 앱, 페이지가 미리 컴파일되지 않은 경우</span><span class="sxs-lookup"><span data-stu-id="ba911-124">Pages&dagger; MVC or Razor Pages apps, if pages aren't precompiled</span></span></li><li><span data-ttu-id="ba911-125">wwwroot&dagger;</span><span class="sxs-lookup"><span data-stu-id="ba911-125">wwwroot&dagger;</span></span></li><li><span data-ttu-id="ba911-126">\*.dll 파일</span><span class="sxs-lookup"><span data-stu-id="ba911-126">\*.dll files</span></span></li><li><span data-ttu-id="ba911-127">{ASSEMBLY NAME}.deps.json</span><span class="sxs-lookup"><span data-stu-id="ba911-127">{ASSEMBLY NAME}.deps.json</span></span></li><li><span data-ttu-id="ba911-128">{ASSEMBLY NAME}.dll</span><span class="sxs-lookup"><span data-stu-id="ba911-128">{ASSEMBLY NAME}.dll</span></span></li><li><span data-ttu-id="ba911-129">{ASSEMBLY NAME}.exe</span><span class="sxs-lookup"><span data-stu-id="ba911-129">{ASSEMBLY NAME}.exe</span></span></li><li><span data-ttu-id="ba911-130">{ASSEMBLY NAME}.pdb</span><span class="sxs-lookup"><span data-stu-id="ba911-130">{ASSEMBLY NAME}.pdb</span></span></li><li><span data-ttu-id="ba911-131">{ASSEMBLY NAME}.Views.dll</span><span class="sxs-lookup"><span data-stu-id="ba911-131">{ASSEMBLY NAME}.Views.dll</span></span></li><li><span data-ttu-id="ba911-132">{ASSEMBLY NAME}.Views.pdb</span><span class="sxs-lookup"><span data-stu-id="ba911-132">{ASSEMBLY NAME}.Views.pdb</span></span></li><li><span data-ttu-id="ba911-133">{ASSEMBLY NAME}.runtimeconfig.json</span><span class="sxs-lookup"><span data-stu-id="ba911-133">{ASSEMBLY NAME}.runtimeconfig.json</span></span></li><li><span data-ttu-id="ba911-134">web.config(IIS 배포)</span><span class="sxs-lookup"><span data-stu-id="ba911-134">web.config (IIS deployments)</span></span></li></ul></li></ul> |

<span data-ttu-id="ba911-135">&dagger;디렉터리를 나타냄</span><span class="sxs-lookup"><span data-stu-id="ba911-135">&dagger;Indicates a directory</span></span>

<span data-ttu-id="ba911-136">*publish* 디렉터리는 배포의 *애플리케이션 기본 경로*라고도 하는 *콘텐츠 루트 경로*를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="ba911-136">The *publish* directory represents the *content root path*, also called the *application base path*, of the deployment.</span></span> <span data-ttu-id="ba911-137">서버에 배포된 앱의 *publish* 디렉터리에 어떤 이름을 지정하더라도 해당 위치는 호스트된 앱에 대한 서버의 실제 경로로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ba911-137">Whatever name is given to the *publish* directory of the deployed app on the server, its location serves as the server's physical path to the hosted app.</span></span>

<span data-ttu-id="ba911-138">*wwwroot* 디렉터리(있는 경우)에는 정적 자산만 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="ba911-138">The *wwwroot* directory, if present, only contains static assets.</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ba911-139">*로그* 폴더를 만드는 것은 [ASP.NET Core 모듈 향상된 디버그 로깅](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="ba911-139">Creating a *Logs* folder is useful for [ASP.NET Core Module enhanced debug logging](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs).</span></span> <span data-ttu-id="ba911-140">`<handlerSetting>` 값에 제공된 경로에 있는 폴더는 모듈에서 자동으로 생성되지 않으며 모듈이 디버그 로그를 작성할 수 있도록 배포에 미리 존재해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ba911-140">Folders in the path provided to the `<handlerSetting>` value aren't created by the module automatically and should pre-exist in the deployment to allow the module to write the debug log.</span></span>

<span data-ttu-id="ba911-141">다음 두 가지 방법 중 하나를 사용하여 *Logs* 디렉터리를 배포용으로 만들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ba911-141">A *Logs* directory can be created for the deployment using one of the following two approaches:</span></span>

* <span data-ttu-id="ba911-142">다음 `<Target>` 요소를 프로젝트 파일에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="ba911-142">Add the following `<Target>` element to the project file:</span></span>

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

   <span data-ttu-id="ba911-143">`<MakeDir>` 요소는 게시된 출력에 빈 *Logs* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="ba911-143">The `<MakeDir>` element creates an empty *Logs* folder in the published output.</span></span> <span data-ttu-id="ba911-144">이 요소는 `PublishDir` 속성을 사용하여 폴더를 만들 대상 위치를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ba911-144">The element uses the `PublishDir` property to determine the target location for creating the folder.</span></span> <span data-ttu-id="ba911-145">웹 배포와 같은 여러 배포 방법에서는 배포 중에 빈 폴더를 건너뜁니다.</span><span class="sxs-lookup"><span data-stu-id="ba911-145">Several deployment methods, such as Web Deploy, skip empty folders during deployment.</span></span> <span data-ttu-id="ba911-146">`<WriteLinesToFile>` 요소는 파일이 서버에 배포되도록 *Logs* 폴더에 파일을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="ba911-146">The `<WriteLinesToFile>` element generates a file in the *Logs* folder, which guarantees deployment of the folder to the server.</span></span> <span data-ttu-id="ba911-147">대상 폴더에 대한 쓰기 권한이 작업자 프로세스에 없는 경우 이 방법을 사용한 폴더 만들기가 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="ba911-147">Folder creation using this approach fails if the worker process doesn't have write access to the target folder.</span></span>

* <span data-ttu-id="ba911-148">배포 시 서버에서 *Logs* 디렉터리를 실제로 만드세요.</span><span class="sxs-lookup"><span data-stu-id="ba911-148">Physically create the *Logs* directory on the server in the deployment.</span></span>

<span data-ttu-id="ba911-149">배포 디렉터리에는 읽기/실행 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="ba911-149">The deployment directory requires Read/Execute permissions.</span></span> <span data-ttu-id="ba911-150">*Logs* 디렉터리에는 읽기/쓰기 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="ba911-150">The *Logs* directory requires Read/Write permissions.</span></span> <span data-ttu-id="ba911-151">파일이 작성되는 추가 디렉터리에는 읽기/쓰기 권한이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="ba911-151">Additional directories where files are written require Read/Write permissions.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="ba911-152">추가 자료</span><span class="sxs-lookup"><span data-stu-id="ba911-152">Additional resources</span></span>

* [<span data-ttu-id="ba911-153">dotnet publish</span><span class="sxs-lookup"><span data-stu-id="ba911-153">dotnet publish</span></span>](/dotnet/core/tools/dotnet-publish)
* [<span data-ttu-id="ba911-154">.NET Core 애플리케이션 배포</span><span class="sxs-lookup"><span data-stu-id="ba911-154">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* [<span data-ttu-id="ba911-155">대상 프레임워크</span><span class="sxs-lookup"><span data-stu-id="ba911-155">Target frameworks</span></span>](/dotnet/standard/frameworks)
* [<span data-ttu-id="ba911-156">.NET Core RID 카탈로그</span><span class="sxs-lookup"><span data-stu-id="ba911-156">.NET Core RID Catalog</span></span>](/dotnet/core/rid-catalog)
