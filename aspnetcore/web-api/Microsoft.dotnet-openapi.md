---
title: OpenAPI를 사용한 ASP.NET Core 앱 개발
author: ryanbrandenburg
description: "'Microsoft dotnet-openapi' 도구를 사용하여 OpenAPI 파일에 참조를 추가하는 방법을 보여줍니다."
ms.author: rybrande
ms.date: 08/26/2019
monikerRange: '>= aspnetcore-3.0'
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: a9b38bb7e69744d72867bf69cecf1fa92d7c15b3
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71187367"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a><span data-ttu-id="af5ce-103">OpenAPI 도구를 사용한 ASP.NET Core 앱 개발</span><span class="sxs-lookup"><span data-stu-id="af5ce-103">Develop ASP.NET Core apps using OpenAPI tools</span></span>

<span data-ttu-id="af5ce-104">Ryan Brandenburg가 개발</span><span class="sxs-lookup"><span data-stu-id="af5ce-104">By Ryan Brandenburg</span></span>

<span data-ttu-id="af5ce-105">`Microsoft.dotnet-openapi`는 프로젝트 내에서 [OpenAPI](https://github.com/OAI/OpenAPI-Specification) 참조를 관리하기 위한 .NET Core 전역 도구입니다.</span><span class="sxs-lookup"><span data-stu-id="af5ce-105">`Microsoft.dotnet-openapi` is a .NET Core global tool for managing [OpenAPI](https://github.com/OAI/OpenAPI-Specification) references within a project.</span></span>

## <a name="installation"></a><span data-ttu-id="af5ce-106">설치</span><span class="sxs-lookup"><span data-stu-id="af5ce-106">Installation</span></span>

<span data-ttu-id="af5ce-107">`Microsoft.dotnet-openapi`를 설치하려면 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="af5ce-107">To install `Microsoft.dotnet-openapi`, run the following command:</span></span>

```console
dotnet tool install -g Microsoft.dotnet-openapi
```

<span data-ttu-id="af5ce-108">`Microsoft.dotnet-openapi`는 [.NET Core 전역 도구](/dotnet/core/tools/global-tools)입니다.</span><span class="sxs-lookup"><span data-stu-id="af5ce-108">`Microsoft.dotnet-openapi` is a [.NET Core Global Tool](/dotnet/core/tools/global-tools).</span></span>

## <a name="add"></a><span data-ttu-id="af5ce-109">추가</span><span class="sxs-lookup"><span data-stu-id="af5ce-109">Add</span></span>

<span data-ttu-id="af5ce-110">이 페이지의 명령 중 하나를 사용하여 OpenAPI 참조를 추가하면 다음과 유사한 `<OpenApiReference />`가 *.csproj* 파일에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="af5ce-110">Adding an OpenAPI reference using any of the commands on this page adds an `<OpenApiReference />`  element similar to the following to the *.csproj* file:</span></span>

```xml
<OpenApiReference Include="openapi.json" />
```

<span data-ttu-id="af5ce-111">생성된 클라이언트 코드를 앱에서 호출하려면 이전 참조가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="af5ce-111">The preceding reference is required for the app to call the generated client code.</span></span>

<!-- TODO: Restore after https://github.com/aspnet/AspNetCore/issues/12738
### Add Project

#### Options

| Short option | Long option | Description | Example |
|-------|------|-------|---------|
| -v|--verbose | Show verbose output. |dotnet openapi add project *-v* ../Ref/ProjRef.csproj |
| -p|--project | The project to operate on. |dotnet openapi add project *--project .\Ref.csproj* ../Ref/ProjRef.csproj |

#### Arguments

|  Argument  | Description | Example |
|-------------|-------------|---------|
| source-file | The source to create a reference from. Must be a project file. |dotnet openapi add project *../Ref/ProjRef.csproj* | -->

### <a name="add-file"></a><span data-ttu-id="af5ce-112">파일 추가</span><span class="sxs-lookup"><span data-stu-id="af5ce-112">Add File</span></span>

#### <a name="options"></a><span data-ttu-id="af5ce-113">옵션</span><span class="sxs-lookup"><span data-stu-id="af5ce-113">Options</span></span>

| <span data-ttu-id="af5ce-114">짧음 옵션</span><span class="sxs-lookup"><span data-stu-id="af5ce-114">Short option</span></span>| <span data-ttu-id="af5ce-115">긴 옵션</span><span class="sxs-lookup"><span data-stu-id="af5ce-115">Long option</span></span>| <span data-ttu-id="af5ce-116">설명</span><span class="sxs-lookup"><span data-stu-id="af5ce-116">Description</span></span> | <span data-ttu-id="af5ce-117">예</span><span class="sxs-lookup"><span data-stu-id="af5ce-117">Example</span></span> |
|-------|------|-------|---------|
| <span data-ttu-id="af5ce-118">-v</span><span class="sxs-lookup"><span data-stu-id="af5ce-118">-v</span></span>|<span data-ttu-id="af5ce-119">--verbose</span><span class="sxs-lookup"><span data-stu-id="af5ce-119">--verbose</span></span> | <span data-ttu-id="af5ce-120">자세한 정보 출력을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="af5ce-120">Show verbose output.</span></span> |<span data-ttu-id="af5ce-121">dotnet openapi add file *-v* .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="af5ce-121">dotnet openapi add file *-v* .\OpenAPI.json</span></span> |
| <span data-ttu-id="af5ce-122">-p</span><span class="sxs-lookup"><span data-stu-id="af5ce-122">-p</span></span>|<span data-ttu-id="af5ce-123">--updateProject</span><span class="sxs-lookup"><span data-stu-id="af5ce-123">--updateProject</span></span> | <span data-ttu-id="af5ce-124">작동할 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="af5ce-124">The project to operate on.</span></span> |<span data-ttu-id="af5ce-125">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="af5ce-125">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="af5ce-126">-c</span><span class="sxs-lookup"><span data-stu-id="af5ce-126">-c</span></span>|<span data-ttu-id="af5ce-127">--code-generator</span><span class="sxs-lookup"><span data-stu-id="af5ce-127">--code-generator</span></span>| <span data-ttu-id="af5ce-128">참조에 적용할 코드 생성기입니다.</span><span class="sxs-lookup"><span data-stu-id="af5ce-128">The code generator to apply to the reference.</span></span> <span data-ttu-id="af5ce-129">옵션은 `NSwagCSharp` 및 `NSwagTypeScript`입니다.</span><span class="sxs-lookup"><span data-stu-id="af5ce-129">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> <span data-ttu-id="af5ce-130">`--code-generator`가 지정되지 않으면 도구는 기본적으로 `NSwagCSharp`가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="af5ce-130">If `--code-generator` is not specified the tooling defaults to `NSwagCSharp`.</span></span>|<span data-ttu-id="af5ce-131">dotnet openapi add file .\OpenApi.json --code-generator</span><span class="sxs-lookup"><span data-stu-id="af5ce-131">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="af5ce-132">-h</span><span class="sxs-lookup"><span data-stu-id="af5ce-132">-h</span></span>|<span data-ttu-id="af5ce-133">--help</span><span class="sxs-lookup"><span data-stu-id="af5ce-133">--help</span></span>|<span data-ttu-id="af5ce-134">도움말 정보를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="af5ce-134">Show help information</span></span>|<span data-ttu-id="af5ce-135">dotnet openapi add file --help</span><span class="sxs-lookup"><span data-stu-id="af5ce-135">dotnet openapi add file --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="af5ce-136">인수</span><span class="sxs-lookup"><span data-stu-id="af5ce-136">Arguments</span></span>

|  <span data-ttu-id="af5ce-137">인수</span><span class="sxs-lookup"><span data-stu-id="af5ce-137">Argument</span></span>  | <span data-ttu-id="af5ce-138">설명</span><span class="sxs-lookup"><span data-stu-id="af5ce-138">Description</span></span> | <span data-ttu-id="af5ce-139">예</span><span class="sxs-lookup"><span data-stu-id="af5ce-139">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="af5ce-140">source-file</span><span class="sxs-lookup"><span data-stu-id="af5ce-140">source-file</span></span> | <span data-ttu-id="af5ce-141">참조를 만들 원본입니다.</span><span class="sxs-lookup"><span data-stu-id="af5ce-141">The source to create a reference from.</span></span> <span data-ttu-id="af5ce-142">OpenAPI 파일이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="af5ce-142">Must be an OpenAPI file.</span></span> |<span data-ttu-id="af5ce-143">dotnet openapi add file *.\OpenAPI.json*</span><span class="sxs-lookup"><span data-stu-id="af5ce-143">dotnet openapi add file *.\OpenAPI.json*</span></span> |

### <a name="add-url"></a><span data-ttu-id="af5ce-144">URL 추가</span><span class="sxs-lookup"><span data-stu-id="af5ce-144">Add URL</span></span>

#### <a name="options"></a><span data-ttu-id="af5ce-145">옵션</span><span class="sxs-lookup"><span data-stu-id="af5ce-145">Options</span></span>

| <span data-ttu-id="af5ce-146">짧음 옵션</span><span class="sxs-lookup"><span data-stu-id="af5ce-146">Short option</span></span>| <span data-ttu-id="af5ce-147">긴 옵션</span><span class="sxs-lookup"><span data-stu-id="af5ce-147">Long option</span></span>| <span data-ttu-id="af5ce-148">설명</span><span class="sxs-lookup"><span data-stu-id="af5ce-148">Description</span></span> | <span data-ttu-id="af5ce-149">예</span><span class="sxs-lookup"><span data-stu-id="af5ce-149">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="af5ce-150">-v</span><span class="sxs-lookup"><span data-stu-id="af5ce-150">-v</span></span>|<span data-ttu-id="af5ce-151">--verbose</span><span class="sxs-lookup"><span data-stu-id="af5ce-151">--verbose</span></span> | <span data-ttu-id="af5ce-152">자세한 정보 출력을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="af5ce-152">Show verbose output.</span></span> |<span data-ttu-id="af5ce-153">dotnet openapi add url *-v* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="af5ce-153">dotnet openapi add url *-v* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="af5ce-154">-p</span><span class="sxs-lookup"><span data-stu-id="af5ce-154">-p</span></span>|<span data-ttu-id="af5ce-155">--updateProject</span><span class="sxs-lookup"><span data-stu-id="af5ce-155">--updateProject</span></span> | <span data-ttu-id="af5ce-156">작동할 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="af5ce-156">The project to operate on.</span></span> |<span data-ttu-id="af5ce-157">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="af5ce-157">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="af5ce-158">-o</span><span class="sxs-lookup"><span data-stu-id="af5ce-158">-o</span></span>|<span data-ttu-id="af5ce-159">--output-file</span><span class="sxs-lookup"><span data-stu-id="af5ce-159">--output-file</span></span> | <span data-ttu-id="af5ce-160">OpenAPI 파일의 로컬 복사본을 저장할 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="af5ce-160">Where to place the local copy of the OpenAPI file.</span></span> |<span data-ttu-id="af5ce-161">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span><span class="sxs-lookup"><span data-stu-id="af5ce-161">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span></span> |
| <span data-ttu-id="af5ce-162">-c</span><span class="sxs-lookup"><span data-stu-id="af5ce-162">-c</span></span>|<span data-ttu-id="af5ce-163">--code-generator</span><span class="sxs-lookup"><span data-stu-id="af5ce-163">--code-generator</span></span>| <span data-ttu-id="af5ce-164">참조에 적용할 코드 생성기입니다.</span><span class="sxs-lookup"><span data-stu-id="af5ce-164">The code generator to apply to the reference.</span></span> <span data-ttu-id="af5ce-165">옵션은 `NSwagCSharp` 및 `NSwagTypeScript`입니다.</span><span class="sxs-lookup"><span data-stu-id="af5ce-165">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> |<span data-ttu-id="af5ce-166">dotnet openapi add file .\OpenApi.json --code-generator</span><span class="sxs-lookup"><span data-stu-id="af5ce-166">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="af5ce-167">-h</span><span class="sxs-lookup"><span data-stu-id="af5ce-167">-h</span></span>|<span data-ttu-id="af5ce-168">--help</span><span class="sxs-lookup"><span data-stu-id="af5ce-168">--help</span></span>|<span data-ttu-id="af5ce-169">도움말 정보를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="af5ce-169">Show help information</span></span>|<span data-ttu-id="af5ce-170">dotnet openapi add url --help</span><span class="sxs-lookup"><span data-stu-id="af5ce-170">dotnet openapi add url --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="af5ce-171">인수</span><span class="sxs-lookup"><span data-stu-id="af5ce-171">Arguments</span></span>

|  <span data-ttu-id="af5ce-172">인수</span><span class="sxs-lookup"><span data-stu-id="af5ce-172">Argument</span></span>  | <span data-ttu-id="af5ce-173">설명</span><span class="sxs-lookup"><span data-stu-id="af5ce-173">Description</span></span> | <span data-ttu-id="af5ce-174">예</span><span class="sxs-lookup"><span data-stu-id="af5ce-174">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="af5ce-175">원본-URL</span><span class="sxs-lookup"><span data-stu-id="af5ce-175">source-URL</span></span> | <span data-ttu-id="af5ce-176">참조를 만들 원본입니다.</span><span class="sxs-lookup"><span data-stu-id="af5ce-176">The source to create a reference from.</span></span> <span data-ttu-id="af5ce-177">URL이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="af5ce-177">Must be a URL.</span></span> |<span data-ttu-id="af5ce-178">dotnet openapi add url `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="af5ce-178">dotnet openapi add url `https://contoso.com/openapi.json`</span></span> |

## <a name="remove"></a><span data-ttu-id="af5ce-179">제거</span><span class="sxs-lookup"><span data-stu-id="af5ce-179">Remove</span></span>

<span data-ttu-id="af5ce-180">*.csproj* 파일에서 지정된 파일 이름과 일치하는 OpenAPI 참조를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="af5ce-180">Removes the OpenAPI reference matching the given filename from the *.csproj* file.</span></span> <span data-ttu-id="af5ce-181">OpenAPI 참조가 제거되면 클라이언트가 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="af5ce-181">When the OpenAPI reference is removed, clients won't be generated.</span></span> <span data-ttu-id="af5ce-182">로컬 *.json* and *.yaml* 파일이 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="af5ce-182">Local *.json* and *.yaml* files are deleted.</span></span>

### <a name="options"></a><span data-ttu-id="af5ce-183">옵션</span><span class="sxs-lookup"><span data-stu-id="af5ce-183">Options</span></span>

| <span data-ttu-id="af5ce-184">짧음 옵션</span><span class="sxs-lookup"><span data-stu-id="af5ce-184">Short option</span></span>| <span data-ttu-id="af5ce-185">긴 옵션</span><span class="sxs-lookup"><span data-stu-id="af5ce-185">Long option</span></span>| <span data-ttu-id="af5ce-186">설명</span><span class="sxs-lookup"><span data-stu-id="af5ce-186">Description</span></span>| <span data-ttu-id="af5ce-187">예</span><span class="sxs-lookup"><span data-stu-id="af5ce-187">Example</span></span> |
|-------|------|------------|---------|
| <span data-ttu-id="af5ce-188">-v</span><span class="sxs-lookup"><span data-stu-id="af5ce-188">-v</span></span>|<span data-ttu-id="af5ce-189">--verbose</span><span class="sxs-lookup"><span data-stu-id="af5ce-189">--verbose</span></span> | <span data-ttu-id="af5ce-190">자세한 정보 출력을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="af5ce-190">Show verbose output.</span></span> |<span data-ttu-id="af5ce-191">dotnet openapi remove *-v*</span><span class="sxs-lookup"><span data-stu-id="af5ce-191">dotnet openapi remove *-v*</span></span>|
| <span data-ttu-id="af5ce-192">-p</span><span class="sxs-lookup"><span data-stu-id="af5ce-192">-p</span></span>|<span data-ttu-id="af5ce-193">--updateProject</span><span class="sxs-lookup"><span data-stu-id="af5ce-193">--updateProject</span></span> | <span data-ttu-id="af5ce-194">작동할 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="af5ce-194">The project to operate on.</span></span> |<span data-ttu-id="af5ce-195">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="af5ce-195">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="af5ce-196">-h</span><span class="sxs-lookup"><span data-stu-id="af5ce-196">-h</span></span>|<span data-ttu-id="af5ce-197">--help</span><span class="sxs-lookup"><span data-stu-id="af5ce-197">--help</span></span>|<span data-ttu-id="af5ce-198">도움말 정보를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="af5ce-198">Show help information</span></span>|<span data-ttu-id="af5ce-199">dotnet openapi remove --help</span><span class="sxs-lookup"><span data-stu-id="af5ce-199">dotnet openapi remove --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="af5ce-200">인수</span><span class="sxs-lookup"><span data-stu-id="af5ce-200">Arguments</span></span>

|  <span data-ttu-id="af5ce-201">인수</span><span class="sxs-lookup"><span data-stu-id="af5ce-201">Argument</span></span>  | <span data-ttu-id="af5ce-202">설명</span><span class="sxs-lookup"><span data-stu-id="af5ce-202">Description</span></span>| <span data-ttu-id="af5ce-203">예</span><span class="sxs-lookup"><span data-stu-id="af5ce-203">Example</span></span> |
| ------------|------------|---------|
| <span data-ttu-id="af5ce-204">source-file</span><span class="sxs-lookup"><span data-stu-id="af5ce-204">source-file</span></span> | <span data-ttu-id="af5ce-205">참조를 제거할 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="af5ce-205">The source to remove the reference to.</span></span> |<span data-ttu-id="af5ce-206">dotnet openapi remove *.\OpenAPI.json*</span><span class="sxs-lookup"><span data-stu-id="af5ce-206">dotnet openapi remove *.\OpenAPI.json*</span></span> |

## <a name="refresh"></a><span data-ttu-id="af5ce-207">새로 고침</span><span class="sxs-lookup"><span data-stu-id="af5ce-207">Refresh</span></span>

<span data-ttu-id="af5ce-208">다운로드 URL의 최신 콘텐츠를 사용하여 다운로드한 파일의 로컬 버전을 새로 고칩니다.</span><span class="sxs-lookup"><span data-stu-id="af5ce-208">Refreshes the local version of a file that was downloaded using the latest content from the download URL.</span></span>

### <a name="options"></a><span data-ttu-id="af5ce-209">옵션</span><span class="sxs-lookup"><span data-stu-id="af5ce-209">Options</span></span>

| <span data-ttu-id="af5ce-210">짧음 옵션</span><span class="sxs-lookup"><span data-stu-id="af5ce-210">Short option</span></span>| <span data-ttu-id="af5ce-211">긴 옵션</span><span class="sxs-lookup"><span data-stu-id="af5ce-211">Long option</span></span>| <span data-ttu-id="af5ce-212">설명</span><span class="sxs-lookup"><span data-stu-id="af5ce-212">Description</span></span> | <span data-ttu-id="af5ce-213">예</span><span class="sxs-lookup"><span data-stu-id="af5ce-213">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="af5ce-214">-v</span><span class="sxs-lookup"><span data-stu-id="af5ce-214">-v</span></span>|<span data-ttu-id="af5ce-215">--verbose</span><span class="sxs-lookup"><span data-stu-id="af5ce-215">--verbose</span></span> | <span data-ttu-id="af5ce-216">자세한 정보 출력을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="af5ce-216">Show verbose output.</span></span> | <span data-ttu-id="af5ce-217">dotnet openapi refresh *-v* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="af5ce-217">dotnet openapi refresh *-v* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="af5ce-218">-p</span><span class="sxs-lookup"><span data-stu-id="af5ce-218">-p</span></span>|<span data-ttu-id="af5ce-219">--updateProject</span><span class="sxs-lookup"><span data-stu-id="af5ce-219">--updateProject</span></span> | <span data-ttu-id="af5ce-220">작동할 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="af5ce-220">The project to operate on.</span></span> | <span data-ttu-id="af5ce-221">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="af5ce-221">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="af5ce-222">-h</span><span class="sxs-lookup"><span data-stu-id="af5ce-222">-h</span></span>|<span data-ttu-id="af5ce-223">--help</span><span class="sxs-lookup"><span data-stu-id="af5ce-223">--help</span></span>|<span data-ttu-id="af5ce-224">도움말 정보를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="af5ce-224">Show help information</span></span>|<span data-ttu-id="af5ce-225">dotnet openapi refresh --help</span><span class="sxs-lookup"><span data-stu-id="af5ce-225">dotnet openapi refresh --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="af5ce-226">인수</span><span class="sxs-lookup"><span data-stu-id="af5ce-226">Arguments</span></span>

|  <span data-ttu-id="af5ce-227">인수</span><span class="sxs-lookup"><span data-stu-id="af5ce-227">Argument</span></span>  | <span data-ttu-id="af5ce-228">설명</span><span class="sxs-lookup"><span data-stu-id="af5ce-228">Description</span></span> | <span data-ttu-id="af5ce-229">예</span><span class="sxs-lookup"><span data-stu-id="af5ce-229">Example</span></span> |
| ------------|-------------|---------|
| <span data-ttu-id="af5ce-230">원본-URL</span><span class="sxs-lookup"><span data-stu-id="af5ce-230">source-URL</span></span> | <span data-ttu-id="af5ce-231">참조를 새로 고칠 URL입니다.</span><span class="sxs-lookup"><span data-stu-id="af5ce-231">The URL to refresh the reference from.</span></span> | <span data-ttu-id="af5ce-232">dotnet openapi refresh `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="af5ce-232">dotnet openapi refresh `https://contoso.com/openapi.json`</span></span> |
