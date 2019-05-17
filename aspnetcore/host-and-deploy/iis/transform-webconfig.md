---
title: web.config 변환
author: guardrex
description: ASP.NET Core 앱을 게시할 때 web.config 파일을 변환하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2019
uid: host-and-deploy/iis/transform-webconfig
ms.openlocfilehash: bd8cf7d8515e874eefd2c326727f56d0a4b502a7
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087074"
---
# <a name="transform-webconfig"></a><span data-ttu-id="938c0-103">web.config 변환</span><span class="sxs-lookup"><span data-stu-id="938c0-103">Transform web.config</span></span>

<span data-ttu-id="938c0-104">작성자: [Vijay Ramakrishnan](https://github.com/vijayrkn) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="938c0-104">By [Vijay Ramakrishnan](https://github.com/vijayrkn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="938c0-105">다음을 기반으로 앱을 게시할 때 *web.config* 파일의 변환을 자동으로 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="938c0-105">Transformations to the *web.config* file can be applied automatically when an app is published based on:</span></span>

* [<span data-ttu-id="938c0-106">빌드 구성</span><span class="sxs-lookup"><span data-stu-id="938c0-106">Build configuration</span></span>](#build-configuration)
* [<span data-ttu-id="938c0-107">Profile</span><span class="sxs-lookup"><span data-stu-id="938c0-107">Profile</span></span>](#profile)
* [<span data-ttu-id="938c0-108">환경</span><span class="sxs-lookup"><span data-stu-id="938c0-108">Environment</span></span>](#environment)
* [<span data-ttu-id="938c0-109">사용자 지정</span><span class="sxs-lookup"><span data-stu-id="938c0-109">Custom</span></span>](#custom)

<span data-ttu-id="938c0-110">이 변환은 다음 *web.config* 생성 시나리오 중 하나에 대해 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="938c0-110">These transformations occur for either of the following *web.config* generation scenarios:</span></span>

* <span data-ttu-id="938c0-111">`Microsoft.NET.Sdk.Web` SDK에서 자동으로 생성되는 경우</span><span class="sxs-lookup"><span data-stu-id="938c0-111">Generated automatically by the `Microsoft.NET.Sdk.Web` SDK.</span></span>
* <span data-ttu-id="938c0-112">앱의 콘텐츠 루트에서 개발자가 제공하는 경우</span><span class="sxs-lookup"><span data-stu-id="938c0-112">Provided by the developer in the content root of the app.</span></span>

## <a name="build-configuration"></a><span data-ttu-id="938c0-113">빌드 구성</span><span class="sxs-lookup"><span data-stu-id="938c0-113">Build configuration</span></span>

<span data-ttu-id="938c0-114">빌드 구성 변환이 먼저 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="938c0-114">Build configuration transforms are run first.</span></span>

<span data-ttu-id="938c0-115">*web.config* 변환이 필요한 각 [빌드 구성(Debug|Release)](/dotnet/core/tools/dotnet-publish#options)에 사용할 *web.{CONFIGURATION}.config* 파일을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="938c0-115">Include a *web.{CONFIGURATION}.config* file for each [build configuration (Debug|Release)](/dotnet/core/tools/dotnet-publish#options) requiring a *web.config* transformation.</span></span>

<span data-ttu-id="938c0-116">다음 예제에서는 구성별 환경 변수가 *web.Release.config*에서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="938c0-116">In the following example, a configuration-specific environment variable is set in *web.Release.config*:</span></span>

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Configuration_Specific" 
                               value="Configuration_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="938c0-117">구성이 *Release*로 설정되면 변환이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="938c0-117">The transform is applied when the configuration is set to *Release*:</span></span>

```console
dotnet publish --configuration Release
```

<span data-ttu-id="938c0-118">구성의 MSBuild 속성은 `$(Configuration)`입니다.</span><span class="sxs-lookup"><span data-stu-id="938c0-118">The MSBuild property for the configuration is `$(Configuration)`.</span></span>

## <a name="profile"></a><span data-ttu-id="938c0-119">프로필</span><span class="sxs-lookup"><span data-stu-id="938c0-119">Profile</span></span>

<span data-ttu-id="938c0-120">프로필 변환은 [빌드 구성](#build-configuration) 변환 후에 두 번째로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="938c0-120">Profile transformations are run second, after [Build configuration](#build-configuration) transforms.</span></span>

<span data-ttu-id="938c0-121">*web.config* 변환이 필요한 각 프로필 구성에 사용할 *web.{PROFILE}.config* 파일을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="938c0-121">Include a *web.{PROFILE}.config* file for each profile configuration requiring a *web.config* transformation.</span></span>

<span data-ttu-id="938c0-122">다음 예제에서는 프로필별 환경 변수가 폴더 게시 프로필에 대한 *web.FolderProfile.config*에서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="938c0-122">In the following example, a profile-specific environment variable is set in *web.FolderProfile.config* for a folder publish profile:</span></span>

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Profile_Specific" 
                               value="Profile_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="938c0-123">프로필이 *FolderProfile*인 경우 변환이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="938c0-123">The transform is applied when the profile is *FolderProfile*:</span></span>

```console
dotnet publish --configuration Release /p:PublishProfile=FolderProfile
```

<span data-ttu-id="938c0-124">프로필 이름의 MSBuild 속성은 `$(PublishProfile)`입니다.</span><span class="sxs-lookup"><span data-stu-id="938c0-124">The MSBuild property for the profile name is `$(PublishProfile)`.</span></span>

<span data-ttu-id="938c0-125">프로필이 전달되지 않으면 기본 프로필 이름은 **FileSystem**이고, 파일이 앱의 콘텐츠 루트에 있으면 *web.FileSystem.config*가 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="938c0-125">If no profile is passed, the default profile name is **FileSystem** and *web.FileSystem.config* is applied if the file is present in the app's content root.</span></span>

## <a name="environment"></a><span data-ttu-id="938c0-126">환경</span><span class="sxs-lookup"><span data-stu-id="938c0-126">Environment</span></span>

<span data-ttu-id="938c0-127">환경 변환은 [빌드 구성](#build-configuration) 및 [프로필](#profile) 변환 후에 세 번째로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="938c0-127">Environment transformations are run third, after [Build configuration](#build-configuration) and [Profile](#profile) transforms.</span></span>

<span data-ttu-id="938c0-128">*web.config* 변환이 필요한 각 [환경](xref:fundamentals/environments)에 사용할 *web.{ENVIRONMENT}.config* 파일을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="938c0-128">Include a *web.{ENVIRONMENT}.config* file for each [environment](xref:fundamentals/environments) requiring a *web.config* transformation.</span></span>

<span data-ttu-id="938c0-129">다음 예제에서는 환경별 환경 변수가 프로덕션 환경에 대한 *web.Production.config*에서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="938c0-129">In the following example, a environment-specific environment variable is set in *web.Production.config* for the Production environment:</span></span>

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Environment_Specific" 
                               value="Environment_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="938c0-130">환경이 *Production*인 경우 변환이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="938c0-130">The transform is applied when the environment is *Production*:</span></span>

```console
dotnet publish --configuration Release /p:EnvironmentName=Production
```

<span data-ttu-id="938c0-131">환경의 MSBuild 속성은 `$(EnvironmentName)`입니다.</span><span class="sxs-lookup"><span data-stu-id="938c0-131">The MSBuild property for the environment is `$(EnvironmentName)`.</span></span>

<span data-ttu-id="938c0-132">Visual Studio에서 게시하고 게시 프로필을 사용하는 경우 <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="938c0-132">When publishing from Visual Studio and using a publish profile, see <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment>.</span></span>

<span data-ttu-id="938c0-133">환경 이름이 지정되면 `ASPNETCORE_ENVIRONMENT` 환경 변수가 *web.config* 파일에 자동으로 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="938c0-133">The `ASPNETCORE_ENVIRONMENT` environment variable is automatically added to the *web.config* file when the environment name is specified.</span></span>

## <a name="custom"></a><span data-ttu-id="938c0-134">사용자 지정</span><span class="sxs-lookup"><span data-stu-id="938c0-134">Custom</span></span>

<span data-ttu-id="938c0-135">사용자 지정 변환은 [빌드 구성](#build-configuration), [프로필](#profile) 및 [환경](#environment) 변환 후에 마지막으로 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="938c0-135">Custom transformations are run last, after [Build configuration](#build-configuration), [Profile](#profile), and [Environment](#environment) transforms.</span></span>

<span data-ttu-id="938c0-136">*web.config* 변환이 필요한 각 사용자 지정 구성에 사용할 *{CUSTOM_NAME}.transform* 파일을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="938c0-136">Include a *{CUSTOM_NAME}.transform* file for each custom configuration requiring a *web.config* transformation.</span></span>

<span data-ttu-id="938c0-137">다음 예제에서는 사용자 지정 변환 환경 변수가 *custom.transform*에서 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="938c0-137">In the following example, a custom transform environment variable is set in *custom.transform*:</span></span>

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Custom_Specific" 
                               value="Custom_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="938c0-138">`CustomTransformFileName` 속성이 [dotnet publish](/dotnet/core/tools/dotnet-publish) 명령에 전달되면 변환이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="938c0-138">The transform is applied when the `CustomTransformFileName` property is passed to the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

```console
dotnet publish --configuration Release /p:CustomTransformFileName=custom.transform
```

<span data-ttu-id="938c0-139">프로필 이름의 MSBuild 속성은 `$(CustomTransformFileName)`입니다.</span><span class="sxs-lookup"><span data-stu-id="938c0-139">The MSBuild property for the profile name is `$(CustomTransformFileName)`.</span></span>

## <a name="prevent-webconfig-transformation"></a><span data-ttu-id="938c0-140">web.config 변환 방지</span><span class="sxs-lookup"><span data-stu-id="938c0-140">Prevent web.config transformation</span></span>

<span data-ttu-id="938c0-141">*web.config* 파일의 변환을 방지하려면 MSBuild 속성 `$(IsWebConfigTransformDisabled)`를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="938c0-141">To prevent transformations of the *web.config* file, set the MSBuild property `$(IsWebConfigTransformDisabled)`:</span></span>

```console
dotnet publish /p:IsWebConfigTransformDisabled=true
```

## <a name="additional-resources"></a><span data-ttu-id="938c0-142">추가 자료</span><span class="sxs-lookup"><span data-stu-id="938c0-142">Additional resources</span></span>

* [<span data-ttu-id="938c0-143">웹 애플리케이션 프로젝트 배포를 위한 Web.config 변환 구문</span><span class="sxs-lookup"><span data-stu-id="938c0-143">Web.config Transformation Syntax for Web Application Project Deployment</span></span>](http://go.microsoft.com/fwlink/?LinkId=301874)
* <span data-ttu-id="938c0-144">[Visual Studio를 사용하여 웹 프로젝트 배포를 위한 Web.config 변환 구문](https://docs.microsoft.com/previous-versions/aspnet/dd465326(v=vs.110))</span><span class="sxs-lookup"><span data-stu-id="938c0-144">[Web.config Transformation Syntax for Web Project Deployment Using Visual Studio](https://docs.microsoft.com/previous-versions/aspnet/dd465326(v=vs.110))</span></span>
