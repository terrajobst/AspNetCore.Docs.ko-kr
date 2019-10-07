---
title: OpenAPI를 사용한 ASP.NET Core 앱 개발
author: ryanbrandenburg
description: "'Microsoft dotnet-openapi' 도구를 사용하여 OpenAPI 파일에 참조를 추가하는 방법을 보여줍니다."
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: f5eae9e871bc8efc30d500769adb845ff244a90c
ms.sourcegitcommit: e644258c95dd50a82284f107b9bf3becbc43b2b2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71317769"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a><span data-ttu-id="98148-103">OpenAPI 도구를 사용한 ASP.NET Core 앱 개발</span><span class="sxs-lookup"><span data-stu-id="98148-103">Develop ASP.NET Core apps using OpenAPI tools</span></span>

<span data-ttu-id="98148-104">Ryan Brandenburg가 개발</span><span class="sxs-lookup"><span data-stu-id="98148-104">By Ryan Brandenburg</span></span>

<span data-ttu-id="98148-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi)은 [OpenAPI](https://github.com/OAI/OpenAPI-Specification) 참조를 관리하기 위한 [.NET Core 전역 도구](/dotnet/core/tools/global-tools)입니다.</span><span class="sxs-lookup"><span data-stu-id="98148-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) is a [.NET Core Global Tool](/dotnet/core/tools/global-tools) for managing [OpenAPI](https://github.com/OAI/OpenAPI-Specification) references within a project.</span></span>

## <a name="installation"></a><span data-ttu-id="98148-106">설치</span><span class="sxs-lookup"><span data-stu-id="98148-106">Installation</span></span>

<span data-ttu-id="98148-107">`Microsoft.dotnet-openapi`를 설치하려면 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="98148-107">To install `Microsoft.dotnet-openapi`, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a><span data-ttu-id="98148-108">추가</span><span class="sxs-lookup"><span data-stu-id="98148-108">Add</span></span>

<span data-ttu-id="98148-109">이 페이지의 명령 중 하나를 사용하여 OpenAPI 참조를 추가하면 다음과 유사한 `<OpenApiReference />` 요소가 *.csproj* 파일에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="98148-109">Adding an OpenAPI reference using any of the commands on this page adds an `<OpenApiReference />` element similar to the following to the *.csproj* file:</span></span>

```xml
<OpenApiReference Include="openapi.json" />
```

<span data-ttu-id="98148-110">생성된 클라이언트 코드를 앱에서 호출하려면 이전 참조가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="98148-110">The preceding reference is required for the app to call the generated client code.</span></span>

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

### <a name="add-file"></a><span data-ttu-id="98148-111">파일 추가</span><span class="sxs-lookup"><span data-stu-id="98148-111">Add File</span></span>

#### <a name="options"></a><span data-ttu-id="98148-112">옵션</span><span class="sxs-lookup"><span data-stu-id="98148-112">Options</span></span>

| <span data-ttu-id="98148-113">짧음 옵션</span><span class="sxs-lookup"><span data-stu-id="98148-113">Short option</span></span>| <span data-ttu-id="98148-114">긴 옵션</span><span class="sxs-lookup"><span data-stu-id="98148-114">Long option</span></span>| <span data-ttu-id="98148-115">설명</span><span class="sxs-lookup"><span data-stu-id="98148-115">Description</span></span> | <span data-ttu-id="98148-116">예</span><span class="sxs-lookup"><span data-stu-id="98148-116">Example</span></span> |
|-------|------|-------|---------|
| <span data-ttu-id="98148-117">-v</span><span class="sxs-lookup"><span data-stu-id="98148-117">-v</span></span>|<span data-ttu-id="98148-118">--verbose</span><span class="sxs-lookup"><span data-stu-id="98148-118">--verbose</span></span> | <span data-ttu-id="98148-119">자세한 정보 출력을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="98148-119">Show verbose output.</span></span> |<span data-ttu-id="98148-120">dotnet openapi add file *-v* .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="98148-120">dotnet openapi add file *-v* .\OpenAPI.json</span></span> |
| <span data-ttu-id="98148-121">-p</span><span class="sxs-lookup"><span data-stu-id="98148-121">-p</span></span>|<span data-ttu-id="98148-122">--updateProject</span><span class="sxs-lookup"><span data-stu-id="98148-122">--updateProject</span></span> | <span data-ttu-id="98148-123">작동할 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="98148-123">The project to operate on.</span></span> |<span data-ttu-id="98148-124">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="98148-124">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="98148-125">-c</span><span class="sxs-lookup"><span data-stu-id="98148-125">-c</span></span>|<span data-ttu-id="98148-126">--code-generator</span><span class="sxs-lookup"><span data-stu-id="98148-126">--code-generator</span></span>| <span data-ttu-id="98148-127">참조에 적용할 코드 생성기입니다.</span><span class="sxs-lookup"><span data-stu-id="98148-127">The code generator to apply to the reference.</span></span> <span data-ttu-id="98148-128">옵션은 `NSwagCSharp` 및 `NSwagTypeScript`입니다.</span><span class="sxs-lookup"><span data-stu-id="98148-128">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> <span data-ttu-id="98148-129">`--code-generator`가 지정되지 않으면 도구는 기본적으로 `NSwagCSharp`가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="98148-129">If `--code-generator` is not specified the tooling defaults to `NSwagCSharp`.</span></span>|<span data-ttu-id="98148-130">dotnet openapi add file .\OpenApi.json --code-generator</span><span class="sxs-lookup"><span data-stu-id="98148-130">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="98148-131">-h</span><span class="sxs-lookup"><span data-stu-id="98148-131">-h</span></span>|<span data-ttu-id="98148-132">--help</span><span class="sxs-lookup"><span data-stu-id="98148-132">--help</span></span>|<span data-ttu-id="98148-133">도움말 정보를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="98148-133">Show help information</span></span>|<span data-ttu-id="98148-134">dotnet openapi add file --help</span><span class="sxs-lookup"><span data-stu-id="98148-134">dotnet openapi add file --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="98148-135">인수</span><span class="sxs-lookup"><span data-stu-id="98148-135">Arguments</span></span>

|  <span data-ttu-id="98148-136">인수</span><span class="sxs-lookup"><span data-stu-id="98148-136">Argument</span></span>  | <span data-ttu-id="98148-137">설명</span><span class="sxs-lookup"><span data-stu-id="98148-137">Description</span></span> | <span data-ttu-id="98148-138">예</span><span class="sxs-lookup"><span data-stu-id="98148-138">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="98148-139">source-file</span><span class="sxs-lookup"><span data-stu-id="98148-139">source-file</span></span> | <span data-ttu-id="98148-140">참조를 만들 원본입니다.</span><span class="sxs-lookup"><span data-stu-id="98148-140">The source to create a reference from.</span></span> <span data-ttu-id="98148-141">OpenAPI 파일이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="98148-141">Must be an OpenAPI file.</span></span> |<span data-ttu-id="98148-142">dotnet openapi add file *.\OpenAPI.json*</span><span class="sxs-lookup"><span data-stu-id="98148-142">dotnet openapi add file *.\OpenAPI.json*</span></span> |

### <a name="add-url"></a><span data-ttu-id="98148-143">URL 추가</span><span class="sxs-lookup"><span data-stu-id="98148-143">Add URL</span></span>

#### <a name="options"></a><span data-ttu-id="98148-144">옵션</span><span class="sxs-lookup"><span data-stu-id="98148-144">Options</span></span>

| <span data-ttu-id="98148-145">짧음 옵션</span><span class="sxs-lookup"><span data-stu-id="98148-145">Short option</span></span>| <span data-ttu-id="98148-146">긴 옵션</span><span class="sxs-lookup"><span data-stu-id="98148-146">Long option</span></span>| <span data-ttu-id="98148-147">설명</span><span class="sxs-lookup"><span data-stu-id="98148-147">Description</span></span> | <span data-ttu-id="98148-148">예</span><span class="sxs-lookup"><span data-stu-id="98148-148">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="98148-149">-v</span><span class="sxs-lookup"><span data-stu-id="98148-149">-v</span></span>|<span data-ttu-id="98148-150">--verbose</span><span class="sxs-lookup"><span data-stu-id="98148-150">--verbose</span></span> | <span data-ttu-id="98148-151">자세한 정보 출력을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="98148-151">Show verbose output.</span></span> |<span data-ttu-id="98148-152">dotnet openapi add url *-v* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="98148-152">dotnet openapi add url *-v* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="98148-153">-p</span><span class="sxs-lookup"><span data-stu-id="98148-153">-p</span></span>|<span data-ttu-id="98148-154">--updateProject</span><span class="sxs-lookup"><span data-stu-id="98148-154">--updateProject</span></span> | <span data-ttu-id="98148-155">작동할 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="98148-155">The project to operate on.</span></span> |<span data-ttu-id="98148-156">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="98148-156">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="98148-157">-o</span><span class="sxs-lookup"><span data-stu-id="98148-157">-o</span></span>|<span data-ttu-id="98148-158">--output-file</span><span class="sxs-lookup"><span data-stu-id="98148-158">--output-file</span></span> | <span data-ttu-id="98148-159">OpenAPI 파일의 로컬 복사본을 저장할 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="98148-159">Where to place the local copy of the OpenAPI file.</span></span> |<span data-ttu-id="98148-160">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span><span class="sxs-lookup"><span data-stu-id="98148-160">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span></span> |
| <span data-ttu-id="98148-161">-c</span><span class="sxs-lookup"><span data-stu-id="98148-161">-c</span></span>|<span data-ttu-id="98148-162">--code-generator</span><span class="sxs-lookup"><span data-stu-id="98148-162">--code-generator</span></span>| <span data-ttu-id="98148-163">참조에 적용할 코드 생성기입니다.</span><span class="sxs-lookup"><span data-stu-id="98148-163">The code generator to apply to the reference.</span></span> <span data-ttu-id="98148-164">옵션은 `NSwagCSharp` 및 `NSwagTypeScript`입니다.</span><span class="sxs-lookup"><span data-stu-id="98148-164">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> |<span data-ttu-id="98148-165">dotnet openapi add file .\OpenApi.json --code-generator</span><span class="sxs-lookup"><span data-stu-id="98148-165">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="98148-166">-h</span><span class="sxs-lookup"><span data-stu-id="98148-166">-h</span></span>|<span data-ttu-id="98148-167">--help</span><span class="sxs-lookup"><span data-stu-id="98148-167">--help</span></span>|<span data-ttu-id="98148-168">도움말 정보를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="98148-168">Show help information</span></span>|<span data-ttu-id="98148-169">dotnet openapi add url --help</span><span class="sxs-lookup"><span data-stu-id="98148-169">dotnet openapi add url --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="98148-170">인수</span><span class="sxs-lookup"><span data-stu-id="98148-170">Arguments</span></span>

|  <span data-ttu-id="98148-171">인수</span><span class="sxs-lookup"><span data-stu-id="98148-171">Argument</span></span>  | <span data-ttu-id="98148-172">설명</span><span class="sxs-lookup"><span data-stu-id="98148-172">Description</span></span> | <span data-ttu-id="98148-173">예</span><span class="sxs-lookup"><span data-stu-id="98148-173">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="98148-174">원본-URL</span><span class="sxs-lookup"><span data-stu-id="98148-174">source-URL</span></span> | <span data-ttu-id="98148-175">참조를 만들 원본입니다.</span><span class="sxs-lookup"><span data-stu-id="98148-175">The source to create a reference from.</span></span> <span data-ttu-id="98148-176">URL이어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="98148-176">Must be a URL.</span></span> |<span data-ttu-id="98148-177">dotnet openapi add url `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="98148-177">dotnet openapi add url `https://contoso.com/openapi.json`</span></span> |

## <a name="remove"></a><span data-ttu-id="98148-178">제거</span><span class="sxs-lookup"><span data-stu-id="98148-178">Remove</span></span>

<span data-ttu-id="98148-179">*.csproj* 파일에서 지정된 파일 이름과 일치하는 OpenAPI 참조를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="98148-179">Removes the OpenAPI reference matching the given filename from the *.csproj* file.</span></span> <span data-ttu-id="98148-180">OpenAPI 참조가 제거되면 클라이언트가 생성되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="98148-180">When the OpenAPI reference is removed, clients won't be generated.</span></span> <span data-ttu-id="98148-181">로컬 *.json* and *.yaml* 파일이 삭제됩니다.</span><span class="sxs-lookup"><span data-stu-id="98148-181">Local *.json* and *.yaml* files are deleted.</span></span>

### <a name="options"></a><span data-ttu-id="98148-182">옵션</span><span class="sxs-lookup"><span data-stu-id="98148-182">Options</span></span>

| <span data-ttu-id="98148-183">짧음 옵션</span><span class="sxs-lookup"><span data-stu-id="98148-183">Short option</span></span>| <span data-ttu-id="98148-184">긴 옵션</span><span class="sxs-lookup"><span data-stu-id="98148-184">Long option</span></span>| <span data-ttu-id="98148-185">설명</span><span class="sxs-lookup"><span data-stu-id="98148-185">Description</span></span>| <span data-ttu-id="98148-186">예</span><span class="sxs-lookup"><span data-stu-id="98148-186">Example</span></span> |
|-------|------|------------|---------|
| <span data-ttu-id="98148-187">-v</span><span class="sxs-lookup"><span data-stu-id="98148-187">-v</span></span>|<span data-ttu-id="98148-188">--verbose</span><span class="sxs-lookup"><span data-stu-id="98148-188">--verbose</span></span> | <span data-ttu-id="98148-189">자세한 정보 출력을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="98148-189">Show verbose output.</span></span> |<span data-ttu-id="98148-190">dotnet openapi remove *-v*</span><span class="sxs-lookup"><span data-stu-id="98148-190">dotnet openapi remove *-v*</span></span>|
| <span data-ttu-id="98148-191">-p</span><span class="sxs-lookup"><span data-stu-id="98148-191">-p</span></span>|<span data-ttu-id="98148-192">--updateProject</span><span class="sxs-lookup"><span data-stu-id="98148-192">--updateProject</span></span> | <span data-ttu-id="98148-193">작동할 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="98148-193">The project to operate on.</span></span> |<span data-ttu-id="98148-194">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="98148-194">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="98148-195">-h</span><span class="sxs-lookup"><span data-stu-id="98148-195">-h</span></span>|<span data-ttu-id="98148-196">--help</span><span class="sxs-lookup"><span data-stu-id="98148-196">--help</span></span>|<span data-ttu-id="98148-197">도움말 정보를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="98148-197">Show help information</span></span>|<span data-ttu-id="98148-198">dotnet openapi remove --help</span><span class="sxs-lookup"><span data-stu-id="98148-198">dotnet openapi remove --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="98148-199">인수</span><span class="sxs-lookup"><span data-stu-id="98148-199">Arguments</span></span>

|  <span data-ttu-id="98148-200">인수</span><span class="sxs-lookup"><span data-stu-id="98148-200">Argument</span></span>  | <span data-ttu-id="98148-201">설명</span><span class="sxs-lookup"><span data-stu-id="98148-201">Description</span></span>| <span data-ttu-id="98148-202">예</span><span class="sxs-lookup"><span data-stu-id="98148-202">Example</span></span> |
| ------------|------------|---------|
| <span data-ttu-id="98148-203">source-file</span><span class="sxs-lookup"><span data-stu-id="98148-203">source-file</span></span> | <span data-ttu-id="98148-204">참조를 제거할 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="98148-204">The source to remove the reference to.</span></span> |<span data-ttu-id="98148-205">dotnet openapi remove *.\OpenAPI.json*</span><span class="sxs-lookup"><span data-stu-id="98148-205">dotnet openapi remove *.\OpenAPI.json*</span></span> |

## <a name="refresh"></a><span data-ttu-id="98148-206">새로 고침</span><span class="sxs-lookup"><span data-stu-id="98148-206">Refresh</span></span>

<span data-ttu-id="98148-207">다운로드 URL의 최신 콘텐츠를 사용하여 다운로드한 파일의 로컬 버전을 새로 고칩니다.</span><span class="sxs-lookup"><span data-stu-id="98148-207">Refreshes the local version of a file that was downloaded using the latest content from the download URL.</span></span>

### <a name="options"></a><span data-ttu-id="98148-208">옵션</span><span class="sxs-lookup"><span data-stu-id="98148-208">Options</span></span>

| <span data-ttu-id="98148-209">짧음 옵션</span><span class="sxs-lookup"><span data-stu-id="98148-209">Short option</span></span>| <span data-ttu-id="98148-210">긴 옵션</span><span class="sxs-lookup"><span data-stu-id="98148-210">Long option</span></span>| <span data-ttu-id="98148-211">설명</span><span class="sxs-lookup"><span data-stu-id="98148-211">Description</span></span> | <span data-ttu-id="98148-212">예</span><span class="sxs-lookup"><span data-stu-id="98148-212">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="98148-213">-v</span><span class="sxs-lookup"><span data-stu-id="98148-213">-v</span></span>|<span data-ttu-id="98148-214">--verbose</span><span class="sxs-lookup"><span data-stu-id="98148-214">--verbose</span></span> | <span data-ttu-id="98148-215">자세한 정보 출력을 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="98148-215">Show verbose output.</span></span> | <span data-ttu-id="98148-216">dotnet openapi refresh *-v* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="98148-216">dotnet openapi refresh *-v* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="98148-217">-p</span><span class="sxs-lookup"><span data-stu-id="98148-217">-p</span></span>|<span data-ttu-id="98148-218">--updateProject</span><span class="sxs-lookup"><span data-stu-id="98148-218">--updateProject</span></span> | <span data-ttu-id="98148-219">작동할 프로젝트입니다.</span><span class="sxs-lookup"><span data-stu-id="98148-219">The project to operate on.</span></span> | <span data-ttu-id="98148-220">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="98148-220">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="98148-221">-h</span><span class="sxs-lookup"><span data-stu-id="98148-221">-h</span></span>|<span data-ttu-id="98148-222">--help</span><span class="sxs-lookup"><span data-stu-id="98148-222">--help</span></span>|<span data-ttu-id="98148-223">도움말 정보를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="98148-223">Show help information</span></span>|<span data-ttu-id="98148-224">dotnet openapi refresh --help</span><span class="sxs-lookup"><span data-stu-id="98148-224">dotnet openapi refresh --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="98148-225">인수</span><span class="sxs-lookup"><span data-stu-id="98148-225">Arguments</span></span>

|  <span data-ttu-id="98148-226">인수</span><span class="sxs-lookup"><span data-stu-id="98148-226">Argument</span></span>  | <span data-ttu-id="98148-227">설명</span><span class="sxs-lookup"><span data-stu-id="98148-227">Description</span></span> | <span data-ttu-id="98148-228">예</span><span class="sxs-lookup"><span data-stu-id="98148-228">Example</span></span> |
| ------------|-------------|---------|
| <span data-ttu-id="98148-229">원본-URL</span><span class="sxs-lookup"><span data-stu-id="98148-229">source-URL</span></span> | <span data-ttu-id="98148-230">참조를 새로 고칠 URL입니다.</span><span class="sxs-lookup"><span data-stu-id="98148-230">The URL to refresh the reference from.</span></span> | <span data-ttu-id="98148-231">dotnet openapi refresh `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="98148-231">dotnet openapi refresh `https://contoso.com/openapi.json`</span></span> |
