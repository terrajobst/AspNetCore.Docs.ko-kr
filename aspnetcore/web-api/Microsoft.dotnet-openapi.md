---
title: OpenAPI를 사용한 ASP.NET Core 앱 개발
author: ryanbrandenburg
description: "'Microsoft dotnet-openapi' 도구를 사용하여 OpenAPI 파일에 참조를 추가하는 방법을 보여줍니다."
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: 4be2846f0348788102672978a0487e646da434a0
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75354748"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a><span data-ttu-id="963f7-103">OpenAPI 도구를 사용한 ASP.NET Core 앱 개발</span><span class="sxs-lookup"><span data-stu-id="963f7-103">Develop ASP.NET Core apps using OpenAPI tools</span></span>

<span data-ttu-id="963f7-104">Ryan Brandenburg가 개발</span><span class="sxs-lookup"><span data-stu-id="963f7-104">By Ryan Brandenburg</span></span>

<span data-ttu-id="963f7-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi)은 [OpenAPI](https://github.com/OAI/OpenAPI-Specification) 참조를 관리하기 위한 [.NET Core 전역 도구](/dotnet/core/tools/global-tools)입니다.</span><span class="sxs-lookup"><span data-stu-id="963f7-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) is a [.NET Core Global Tool](/dotnet/core/tools/global-tools) for managing [OpenAPI](https://github.com/OAI/OpenAPI-Specification) references within a project.</span></span>

## <a name="installation"></a><span data-ttu-id="963f7-106">설치</span><span class="sxs-lookup"><span data-stu-id="963f7-106">Installation</span></span>

<span data-ttu-id="963f7-107">`Microsoft.dotnet-openapi`를 설치하려면 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="963f7-107">To install `Microsoft.dotnet-openapi`, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a><span data-ttu-id="963f7-108">추가</span><span class="sxs-lookup"><span data-stu-id="963f7-108">Add</span></span>

<span data-ttu-id="963f7-109">이 페이지의 명령 중 하나를 사용하여 OpenAPI 참조를 추가하면 다음과 유사한 `<OpenApiReference />` 요소가 *.csproj* 파일에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="963f7-109">Adding an OpenAPI reference using any of the commands on this page adds an `<OpenApiReference />` element similar to the following to the *.csproj* file:</span></span>

```xml
<OpenApiReference Include="openapi.json" />
```

<span data-ttu-id="963f7-110">생성된 클라이언트 코드를 앱에서 호출하려면 이전 참조가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="963f7-110">The preceding reference is required for the app to call the generated client code.</span></span>

<!-- TODO: Restore after https://github.com/aspnet/AspNetCore/issues/12738
### Add Project

#### Options

| Short option | Long option | Description | Example |
|-------|------|-------|---------|
| -p|--project | The project to operate on. |dotnet openapi add project *--project .\Ref.csproj* ../Ref/ProjRef.csproj |

#### Arguments

|  Argument  | Description | Example |
|-------------|-------------|---------|
| source-file | The source to create a reference from. Must be a project file. |dotnet openapi add project *../Ref/ProjRef.csproj* | -->

### <a name="add-file"></a><span data-ttu-id="963f7-111">파일 추가</span><span class="sxs-lookup"><span data-stu-id="963f7-111">Add File</span></span>

#### <a name="options"></a><span data-ttu-id="963f7-112">옵션</span><span class="sxs-lookup"><span data-stu-id="963f7-112">Options</span></span>

| <span data-ttu-id="963f7-113">짧음 옵션</span><span class="sxs-lookup"><span data-stu-id="963f7-113">Short option</span></span>| <span data-ttu-id="963f7-114">긴 옵션</span><span class="sxs-lookup"><span data-stu-id="963f7-114">Long option</span></span>| <span data-ttu-id="963f7-115">설명</span><span class="sxs-lookup"><span data-stu-id="963f7-115">Description</span></span> | <span data-ttu-id="963f7-116">예제</span><span class="sxs-lookup"><span data-stu-id="963f7-116">Example</span></span> |
|-------|------|-------|---------|
| <span data-ttu-id="963f7-117">-p</span><span class="sxs-lookup"><span data-stu-id="963f7-117">-p</span></span>|<span data-ttu-id="963f7-118">--updateProject</span><span class="sxs-lookup"><span data-stu-id="963f7-118">--updateProject</span></span> | <span data-ttu-id="963f7-119">작동할 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="963f7-119">The project to operate on.</span></span> |<span data-ttu-id="963f7-120">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="963f7-120">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="963f7-121">-c</span><span class="sxs-lookup"><span data-stu-id="963f7-121">-c</span></span>|<span data-ttu-id="963f7-122">--code-generator</span><span class="sxs-lookup"><span data-stu-id="963f7-122">--code-generator</span></span>| <span data-ttu-id="963f7-123">참조에 적용할 코드 생성기입니다.</span><span class="sxs-lookup"><span data-stu-id="963f7-123">The code generator to apply to the reference.</span></span> <span data-ttu-id="963f7-124">옵션은 `NSwagCSharp` 및 `NSwagTypeScript`입니다.</span><span class="sxs-lookup"><span data-stu-id="963f7-124">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> <span data-ttu-id="963f7-125">`--code-generator`가 지정되지 않으면 도구는 기본적으로 `NSwagCSharp`가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="963f7-125">If `--code-generator` is not specified the tooling defaults to `NSwagCSharp`.</span></span>|<span data-ttu-id="963f7-126">dotnet openapi add file .\OpenApi.json --code-generator</span><span class="sxs-lookup"><span data-stu-id="963f7-126">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="963f7-127">-h</span><span class="sxs-lookup"><span data-stu-id="963f7-127">-h</span></span>|<span data-ttu-id="963f7-128">--help</span><span class="sxs-lookup"><span data-stu-id="963f7-128">--help</span></span>|<span data-ttu-id="963f7-129">도움말 정보를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="963f7-129">Show help information</span></span>|<span data-ttu-id="963f7-130">dotnet openapi add file --help</span><span class="sxs-lookup"><span data-stu-id="963f7-130">dotnet openapi add file --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="963f7-131">인수</span><span class="sxs-lookup"><span data-stu-id="963f7-131">Arguments</span></span>

|  <span data-ttu-id="963f7-132">인수</span><span class="sxs-lookup"><span data-stu-id="963f7-132">Argument</span></span>  | <span data-ttu-id="963f7-133">설명</span><span class="sxs-lookup"><span data-stu-id="963f7-133">Description</span></span> | <span data-ttu-id="963f7-134">예제</span><span class="sxs-lookup"><span data-stu-id="963f7-134">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="963f7-135">source-file</span><span class="sxs-lookup"><span data-stu-id="963f7-135">source-file</span></span> | <span data-ttu-id="963f7-136">참조를 만들 원본입니다.</span><span class="sxs-lookup"><span data-stu-id="963f7-136">The source to create a reference from.</span></span> <span data-ttu-id="963f7-137">OpenAPI 파일이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="963f7-137">Must be an OpenAPI file.</span></span> |<span data-ttu-id="963f7-138">dotnet openapi add file *.\OpenAPI.json*</span><span class="sxs-lookup"><span data-stu-id="963f7-138">dotnet openapi add file *.\OpenAPI.json*</span></span> |

### <a name="add-url"></a><span data-ttu-id="963f7-139">URL 추가</span><span class="sxs-lookup"><span data-stu-id="963f7-139">Add URL</span></span>

#### <a name="options"></a><span data-ttu-id="963f7-140">옵션</span><span class="sxs-lookup"><span data-stu-id="963f7-140">Options</span></span>

| <span data-ttu-id="963f7-141">짧음 옵션</span><span class="sxs-lookup"><span data-stu-id="963f7-141">Short option</span></span>| <span data-ttu-id="963f7-142">긴 옵션</span><span class="sxs-lookup"><span data-stu-id="963f7-142">Long option</span></span>| <span data-ttu-id="963f7-143">설명</span><span class="sxs-lookup"><span data-stu-id="963f7-143">Description</span></span> | <span data-ttu-id="963f7-144">예제</span><span class="sxs-lookup"><span data-stu-id="963f7-144">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="963f7-145">-p</span><span class="sxs-lookup"><span data-stu-id="963f7-145">-p</span></span>|<span data-ttu-id="963f7-146">--updateProject</span><span class="sxs-lookup"><span data-stu-id="963f7-146">--updateProject</span></span> | <span data-ttu-id="963f7-147">작동할 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="963f7-147">The project to operate on.</span></span> |<span data-ttu-id="963f7-148">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="963f7-148">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="963f7-149">-o</span><span class="sxs-lookup"><span data-stu-id="963f7-149">-o</span></span>|<span data-ttu-id="963f7-150">--output-file</span><span class="sxs-lookup"><span data-stu-id="963f7-150">--output-file</span></span> | <span data-ttu-id="963f7-151">OpenAPI 파일의 로컬 복사본을 저장할 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="963f7-151">Where to place the local copy of the OpenAPI file.</span></span> |<span data-ttu-id="963f7-152">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span><span class="sxs-lookup"><span data-stu-id="963f7-152">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span></span> |
| <span data-ttu-id="963f7-153">-c</span><span class="sxs-lookup"><span data-stu-id="963f7-153">-c</span></span>|<span data-ttu-id="963f7-154">--code-generator</span><span class="sxs-lookup"><span data-stu-id="963f7-154">--code-generator</span></span>| <span data-ttu-id="963f7-155">참조에 적용할 코드 생성기입니다.</span><span class="sxs-lookup"><span data-stu-id="963f7-155">The code generator to apply to the reference.</span></span> <span data-ttu-id="963f7-156">옵션은 `NSwagCSharp` 및 `NSwagTypeScript`입니다.</span><span class="sxs-lookup"><span data-stu-id="963f7-156">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> |<span data-ttu-id="963f7-157">dotnet openapi add file .\OpenApi.json --code-generator</span><span class="sxs-lookup"><span data-stu-id="963f7-157">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="963f7-158">-h</span><span class="sxs-lookup"><span data-stu-id="963f7-158">-h</span></span>|<span data-ttu-id="963f7-159">--help</span><span class="sxs-lookup"><span data-stu-id="963f7-159">--help</span></span>|<span data-ttu-id="963f7-160">도움말 정보를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="963f7-160">Show help information</span></span>|<span data-ttu-id="963f7-161">dotnet openapi add url --help</span><span class="sxs-lookup"><span data-stu-id="963f7-161">dotnet openapi add url --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="963f7-162">인수</span><span class="sxs-lookup"><span data-stu-id="963f7-162">Arguments</span></span>

|  <span data-ttu-id="963f7-163">인수</span><span class="sxs-lookup"><span data-stu-id="963f7-163">Argument</span></span>  | <span data-ttu-id="963f7-164">설명</span><span class="sxs-lookup"><span data-stu-id="963f7-164">Description</span></span> | <span data-ttu-id="963f7-165">예제</span><span class="sxs-lookup"><span data-stu-id="963f7-165">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="963f7-166">원본-URL</span><span class="sxs-lookup"><span data-stu-id="963f7-166">source-URL</span></span> | <span data-ttu-id="963f7-167">참조를 만들 원본입니다.</span><span class="sxs-lookup"><span data-stu-id="963f7-167">The source to create a reference from.</span></span> <span data-ttu-id="963f7-168">URL이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="963f7-168">Must be a URL.</span></span> |<span data-ttu-id="963f7-169">dotnet openapi add url `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="963f7-169">dotnet openapi add url `https://contoso.com/openapi.json`</span></span> |

## <a name="remove"></a><span data-ttu-id="963f7-170">제거</span><span class="sxs-lookup"><span data-stu-id="963f7-170">Remove</span></span>

<span data-ttu-id="963f7-171">*.csproj* 파일에서 지정된 파일 이름과 일치하는 OpenAPI 참조를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="963f7-171">Removes the OpenAPI reference matching the given filename from the *.csproj* file.</span></span> <span data-ttu-id="963f7-172">OpenAPI 참조가 제거되면 클라이언트가 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="963f7-172">When the OpenAPI reference is removed, clients won't be generated.</span></span> <span data-ttu-id="963f7-173">로컬 *.json* and *.yaml* 파일이 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="963f7-173">Local *.json* and *.yaml* files are deleted.</span></span>

### <a name="options"></a><span data-ttu-id="963f7-174">옵션</span><span class="sxs-lookup"><span data-stu-id="963f7-174">Options</span></span>

| <span data-ttu-id="963f7-175">짧음 옵션</span><span class="sxs-lookup"><span data-stu-id="963f7-175">Short option</span></span>| <span data-ttu-id="963f7-176">긴 옵션</span><span class="sxs-lookup"><span data-stu-id="963f7-176">Long option</span></span>| <span data-ttu-id="963f7-177">설명</span><span class="sxs-lookup"><span data-stu-id="963f7-177">Description</span></span>| <span data-ttu-id="963f7-178">예제</span><span class="sxs-lookup"><span data-stu-id="963f7-178">Example</span></span> |
|-------|------|------------|---------|
| <span data-ttu-id="963f7-179">-p</span><span class="sxs-lookup"><span data-stu-id="963f7-179">-p</span></span>|<span data-ttu-id="963f7-180">--updateProject</span><span class="sxs-lookup"><span data-stu-id="963f7-180">--updateProject</span></span> | <span data-ttu-id="963f7-181">작동할 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="963f7-181">The project to operate on.</span></span> |<span data-ttu-id="963f7-182">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="963f7-182">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="963f7-183">-h</span><span class="sxs-lookup"><span data-stu-id="963f7-183">-h</span></span>|<span data-ttu-id="963f7-184">--help</span><span class="sxs-lookup"><span data-stu-id="963f7-184">--help</span></span>|<span data-ttu-id="963f7-185">도움말 정보를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="963f7-185">Show help information</span></span>|<span data-ttu-id="963f7-186">dotnet openapi remove --help</span><span class="sxs-lookup"><span data-stu-id="963f7-186">dotnet openapi remove --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="963f7-187">인수</span><span class="sxs-lookup"><span data-stu-id="963f7-187">Arguments</span></span>

|  <span data-ttu-id="963f7-188">인수</span><span class="sxs-lookup"><span data-stu-id="963f7-188">Argument</span></span>  | <span data-ttu-id="963f7-189">설명</span><span class="sxs-lookup"><span data-stu-id="963f7-189">Description</span></span>| <span data-ttu-id="963f7-190">예제</span><span class="sxs-lookup"><span data-stu-id="963f7-190">Example</span></span> |
| ------------|------------|---------|
| <span data-ttu-id="963f7-191">source-file</span><span class="sxs-lookup"><span data-stu-id="963f7-191">source-file</span></span> | <span data-ttu-id="963f7-192">참조를 제거할 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="963f7-192">The source to remove the reference to.</span></span> |<span data-ttu-id="963f7-193">dotnet openapi remove *.\OpenAPI.json*</span><span class="sxs-lookup"><span data-stu-id="963f7-193">dotnet openapi remove *.\OpenAPI.json*</span></span> |

## <a name="refresh"></a><span data-ttu-id="963f7-194">새로 고침</span><span class="sxs-lookup"><span data-stu-id="963f7-194">Refresh</span></span>

<span data-ttu-id="963f7-195">다운로드 URL의 최신 콘텐츠를 사용하여 다운로드한 파일의 로컬 버전을 새로 고칩니다.</span><span class="sxs-lookup"><span data-stu-id="963f7-195">Refreshes the local version of a file that was downloaded using the latest content from the download URL.</span></span>

### <a name="options"></a><span data-ttu-id="963f7-196">옵션</span><span class="sxs-lookup"><span data-stu-id="963f7-196">Options</span></span>

| <span data-ttu-id="963f7-197">짧음 옵션</span><span class="sxs-lookup"><span data-stu-id="963f7-197">Short option</span></span>| <span data-ttu-id="963f7-198">긴 옵션</span><span class="sxs-lookup"><span data-stu-id="963f7-198">Long option</span></span>| <span data-ttu-id="963f7-199">설명</span><span class="sxs-lookup"><span data-stu-id="963f7-199">Description</span></span> | <span data-ttu-id="963f7-200">예제</span><span class="sxs-lookup"><span data-stu-id="963f7-200">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="963f7-201">-p</span><span class="sxs-lookup"><span data-stu-id="963f7-201">-p</span></span>|<span data-ttu-id="963f7-202">--updateProject</span><span class="sxs-lookup"><span data-stu-id="963f7-202">--updateProject</span></span> | <span data-ttu-id="963f7-203">작동할 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="963f7-203">The project to operate on.</span></span> | <span data-ttu-id="963f7-204">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="963f7-204">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="963f7-205">-h</span><span class="sxs-lookup"><span data-stu-id="963f7-205">-h</span></span>|<span data-ttu-id="963f7-206">--help</span><span class="sxs-lookup"><span data-stu-id="963f7-206">--help</span></span>|<span data-ttu-id="963f7-207">도움말 정보를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="963f7-207">Show help information</span></span>|<span data-ttu-id="963f7-208">dotnet openapi refresh --help</span><span class="sxs-lookup"><span data-stu-id="963f7-208">dotnet openapi refresh --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="963f7-209">인수</span><span class="sxs-lookup"><span data-stu-id="963f7-209">Arguments</span></span>

|  <span data-ttu-id="963f7-210">인수</span><span class="sxs-lookup"><span data-stu-id="963f7-210">Argument</span></span>  | <span data-ttu-id="963f7-211">설명</span><span class="sxs-lookup"><span data-stu-id="963f7-211">Description</span></span> | <span data-ttu-id="963f7-212">예제</span><span class="sxs-lookup"><span data-stu-id="963f7-212">Example</span></span> |
| ------------|-------------|---------|
| <span data-ttu-id="963f7-213">원본-URL</span><span class="sxs-lookup"><span data-stu-id="963f7-213">source-URL</span></span> | <span data-ttu-id="963f7-214">참조를 새로 고칠 URL입니다.</span><span class="sxs-lookup"><span data-stu-id="963f7-214">The URL to refresh the reference from.</span></span> | <span data-ttu-id="963f7-215">dotnet openapi refresh `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="963f7-215">dotnet openapi refresh `https://contoso.com/openapi.json`</span></span> |
