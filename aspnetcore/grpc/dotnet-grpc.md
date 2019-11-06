---
title: dotnet-grpc를 사용하여 Protobuf 참조를 관리하세요.
author: juntaoluo
description: Dotnet grpc global 도구를 사용 하 여 Protobuf 참조를 추가, 업데이트, 제거 및 나열 하는 방법에 대해 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
uid: grpc/dotnet-grpc
ms.openlocfilehash: 994597c854a95bb33de1686ab025cb3744cf6845
ms.sourcegitcommit: e71b6a85b0e94a600af607107e298f932924c849
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72519042"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a><span data-ttu-id="cdd87-103">dotnet-grpc를 사용하여 Protobuf 참조를 관리하세요.</span><span class="sxs-lookup"><span data-stu-id="cdd87-103">Manage Protobuf references with dotnet-grpc</span></span>

<span data-ttu-id="cdd87-104">작성자: [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="cdd87-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="cdd87-105">`dotnet-grpc`는 .NET gRPC 프로젝트 내에서 [Protobuf ( *.proto*)](xref:grpc/basics#proto-file) 참조를 관리하기 위한 .NET Core Global Tool입니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-105">`dotnet-grpc` is a .NET Core Global Tool for managing [Protobuf (*.proto*)](xref:grpc/basics#proto-file) references within a .NET gRPC project.</span></span> <span data-ttu-id="cdd87-106">도구를 사용 하 여 Protobuf 참조를 추가, 새로 고침, 제거 및 나열할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-106">The tool can be used to add, refresh, remove, and list Protobuf references.</span></span>

## <a name="installation"></a><span data-ttu-id="cdd87-107">설치</span><span class="sxs-lookup"><span data-stu-id="cdd87-107">Installation</span></span>

<span data-ttu-id="cdd87-108">@No__t_0 [.Net Core 글로벌 도구](/dotnet/core/tools/global-tools)를 설치 하려면 다음 명령을 실행 합니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-108">To install the `dotnet-grpc` [.NET Core Global Tool](/dotnet/core/tools/global-tools), run the following command:</span></span>

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a><span data-ttu-id="cdd87-109">참조 추가</span><span class="sxs-lookup"><span data-stu-id="cdd87-109">Add references</span></span>

<span data-ttu-id="cdd87-110">`dotnet-grpc`은 *.csproj* 파일에 `<Protobuf />` 항목으로 Protobuf references를 추가 하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-110">`dotnet-grpc` can be used to add Protobuf references as `<Protobuf />` items to the *.csproj* file:</span></span>

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

<span data-ttu-id="cdd87-111">Protobuf 참조는 C# 클라이언트 및/또는 서버 자산을 생성 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-111">The Protobuf references are used to generate the C# client and/or server assets.</span></span> <span data-ttu-id="cdd87-112">@No__t_0 도구는 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-112">The `dotnet-grpc` tool can:</span></span>

* <span data-ttu-id="cdd87-113">디스크의 로컬 파일에서 Protobuf 참조를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-113">Create a Protobuf reference from local files on disk.</span></span>
* <span data-ttu-id="cdd87-114">URL로 지정 된 원격 파일에서 Protobuf 참조를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-114">Create a Protobuf reference from a remote file specified by a URL.</span></span>
* <span data-ttu-id="cdd87-115">올바른 gRPC 패키지 종속성이 프로젝트에 추가 되었는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-115">Ensure the correct gRPC package dependencies are added to the project.</span></span>

<span data-ttu-id="cdd87-116">예를 들어 `Grpc.AspNetCore` 패키지는 웹 앱에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-116">For example, the `Grpc.AspNetCore` package is added to a web app.</span></span> <span data-ttu-id="cdd87-117">`Grpc.AspNetCore`에는 gRPC 서버 및 클라이언트 라이브러리와 도구 지원이 포함 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-117">`Grpc.AspNetCore` contains gRPC server and client libraries and tooling support.</span></span> <span data-ttu-id="cdd87-118">또는 gRPC 클라이언트 라이브러리와 도구 지원만 포함 하는 `Grpc.Net.Client`, `Grpc.Tools` 및 `Google.Protobuf` 패키지가 콘솔 앱에 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-118">Alternatively, the `Grpc.Net.Client`, `Grpc.Tools` and `Google.Protobuf` packages, which contain only the gRPC client libraries and tooling support, are added to a Console app.</span></span>

### <a name="add-file"></a><span data-ttu-id="cdd87-119">파일 추가</span><span class="sxs-lookup"><span data-stu-id="cdd87-119">Add file</span></span>

<span data-ttu-id="cdd87-120">@No__t_0 명령은 디스크의 로컬 파일을 Protobuf references로 추가 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-120">The `add-file` command is used to add local files on disk as Protobuf references.</span></span> <span data-ttu-id="cdd87-121">제공 된 파일 경로:</span><span class="sxs-lookup"><span data-stu-id="cdd87-121">The file paths provided:</span></span>

* <span data-ttu-id="cdd87-122">현재 디렉터리나 절대 경로에 대 한 상대 경로일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-122">Can be relative to the current directory or absolute paths.</span></span>
* <span data-ttu-id="cdd87-123">패턴 기반 파일 [와일드 카드 사용](https://wikipedia.org/wiki/Glob_(programming))와일드 카드를 포함할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-123">May contain wild cards for pattern-based file [globbing](https://wikipedia.org/wiki/Glob_(programming)).</span></span>

<span data-ttu-id="cdd87-124">파일이 프로젝트 디렉터리 외부에 있는 경우 Visual Studio에서 `Protos` 폴더 아래에 파일을 표시 하는 `Link` 요소가 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-124">If any files are outside the project directory, a `Link` element is added to display the file under the folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="cdd87-125">사용법</span><span class="sxs-lookup"><span data-stu-id="cdd87-125">Usage</span></span>

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a><span data-ttu-id="cdd87-126">인수</span><span class="sxs-lookup"><span data-stu-id="cdd87-126">Arguments</span></span>

| <span data-ttu-id="cdd87-127">인수</span><span class="sxs-lookup"><span data-stu-id="cdd87-127">Argument</span></span> | <span data-ttu-id="cdd87-128">설명</span><span class="sxs-lookup"><span data-stu-id="cdd87-128">Description</span></span> |
|-|-|
| <span data-ttu-id="cdd87-129">files</span><span class="sxs-lookup"><span data-stu-id="cdd87-129">files</span></span> | <span data-ttu-id="cdd87-130">Protobuf 파일 참조입니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-130">The protobuf file references.</span></span> <span data-ttu-id="cdd87-131">로컬 protobuf 파일의 glob에 대 한 경로일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-131">These can be a path to glob for local protobuf files.</span></span> |

#### <a name="options"></a><span data-ttu-id="cdd87-132">옵션</span><span class="sxs-lookup"><span data-stu-id="cdd87-132">Options</span></span>

| <span data-ttu-id="cdd87-133">짧음 옵션</span><span class="sxs-lookup"><span data-stu-id="cdd87-133">Short option</span></span> | <span data-ttu-id="cdd87-134">긴 옵션</span><span class="sxs-lookup"><span data-stu-id="cdd87-134">Long option</span></span> | <span data-ttu-id="cdd87-135">설명</span><span class="sxs-lookup"><span data-stu-id="cdd87-135">Description</span></span> |
|-|-|-|
| <span data-ttu-id="cdd87-136">-p</span><span class="sxs-lookup"><span data-stu-id="cdd87-136">-p</span></span> | <span data-ttu-id="cdd87-137">--project</span><span class="sxs-lookup"><span data-stu-id="cdd87-137">--project</span></span> | <span data-ttu-id="cdd87-138">작업할 프로젝트 파일의 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-138">The path to the project file to operate on.</span></span> <span data-ttu-id="cdd87-139">파일이 지정 되지 않은 경우이 명령은 현재 디렉터리를 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-139">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="cdd87-140">-s</span><span class="sxs-lookup"><span data-stu-id="cdd87-140">-s</span></span> | <span data-ttu-id="cdd87-141">--서비스</span><span class="sxs-lookup"><span data-stu-id="cdd87-141">--services</span></span> | <span data-ttu-id="cdd87-142">생성 해야 하는 gRPC 서비스의 유형입니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-142">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="cdd87-143">@No__t_0를 지정 하는 경우 `Both` 웹 프로젝트에 사용 되 고 `Client` 웹 프로젝트가 아닌 프로젝트에 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-143">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="cdd87-144">허용 되는 값은 `Both`, `Client`, `Default`, `None`, `Server`입니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-144">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="cdd87-145">-i</span><span class="sxs-lookup"><span data-stu-id="cdd87-145">-i</span></span> | <span data-ttu-id="cdd87-146">--추가 가져오기-디렉터리</span><span class="sxs-lookup"><span data-stu-id="cdd87-146">--additional-import-dirs</span></span> | <span data-ttu-id="cdd87-147">Protobuf 파일에 대 한 가져오기를 확인할 때 사용할 추가 디렉터리입니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-147">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="cdd87-148">세미콜론으로 구분 된 경로 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-148">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="cdd87-149">--액세스</span><span class="sxs-lookup"><span data-stu-id="cdd87-149">--access</span></span> | <span data-ttu-id="cdd87-150">생성 C# 된 클래스에 사용할 액세스 한정자입니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-150">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="cdd87-151">기본값은 `Public`입니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-151">The default value is `Public`.</span></span> <span data-ttu-id="cdd87-152">허용 되는 값은 `Internal` 및 `Public`입니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-152">Accepted values are `Internal` and `Public`.</span></span>

### <a name="add-url"></a><span data-ttu-id="cdd87-153">URL 추가</span><span class="sxs-lookup"><span data-stu-id="cdd87-153">Add URL</span></span>

<span data-ttu-id="cdd87-154">@No__t_0 명령은 원본 URL에서 지정 된 원격 파일을 Protobuf reference로 추가 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-154">The `add-url` command is used to add a remote file specified by an source URL as Protobuf reference.</span></span> <span data-ttu-id="cdd87-155">원격 파일을 다운로드할 위치를 지정 하려면 파일 경로를 제공 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-155">A file path must be provided to specify where to download the remote file.</span></span> <span data-ttu-id="cdd87-156">파일 경로는 현재 디렉터리 또는 절대 경로에 대 한 상대 경로일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-156">The file path can be relative to the current directory or an absolute path.</span></span> <span data-ttu-id="cdd87-157">파일 경로가 프로젝트 디렉터리 외부에 있는 경우 Visual Studio의 `Protos` 가상 폴더 아래에 파일을 표시 하는 `Link` 요소가 추가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-157">If the file path is outside the project directory, a `Link` element is added to display the file under the virtual folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="cdd87-158">사용법</span><span class="sxs-lookup"><span data-stu-id="cdd87-158">Usage</span></span>

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a><span data-ttu-id="cdd87-159">인수</span><span class="sxs-lookup"><span data-stu-id="cdd87-159">Arguments</span></span>

| <span data-ttu-id="cdd87-160">인수</span><span class="sxs-lookup"><span data-stu-id="cdd87-160">Argument</span></span> | <span data-ttu-id="cdd87-161">설명</span><span class="sxs-lookup"><span data-stu-id="cdd87-161">Description</span></span> |
|-|-|
| <span data-ttu-id="cdd87-162">url</span><span class="sxs-lookup"><span data-stu-id="cdd87-162">url</span></span> | <span data-ttu-id="cdd87-163">원격 protobuf 파일에 대 한 URL입니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-163">The URL to a remote protobuf file.</span></span> |

#### <a name="options"></a><span data-ttu-id="cdd87-164">옵션</span><span class="sxs-lookup"><span data-stu-id="cdd87-164">Options</span></span>

| <span data-ttu-id="cdd87-165">짧음 옵션</span><span class="sxs-lookup"><span data-stu-id="cdd87-165">Short option</span></span> | <span data-ttu-id="cdd87-166">긴 옵션</span><span class="sxs-lookup"><span data-stu-id="cdd87-166">Long option</span></span> | <span data-ttu-id="cdd87-167">설명</span><span class="sxs-lookup"><span data-stu-id="cdd87-167">Description</span></span> |
|-|-|-|
| <span data-ttu-id="cdd87-168">-o</span><span class="sxs-lookup"><span data-stu-id="cdd87-168">-o</span></span> | <span data-ttu-id="cdd87-169">--출력</span><span class="sxs-lookup"><span data-stu-id="cdd87-169">--output</span></span> | <span data-ttu-id="cdd87-170">원격 protobuf 파일의 다운로드 경로를 지정 합니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-170">Specifies the download path for the remote protobuf file.</span></span> <span data-ttu-id="cdd87-171">필수 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-171">This is a required option.</span></span>
| <span data-ttu-id="cdd87-172">-p</span><span class="sxs-lookup"><span data-stu-id="cdd87-172">-p</span></span> | <span data-ttu-id="cdd87-173">--project</span><span class="sxs-lookup"><span data-stu-id="cdd87-173">--project</span></span> | <span data-ttu-id="cdd87-174">작업할 프로젝트 파일의 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-174">The path to the project file to operate on.</span></span> <span data-ttu-id="cdd87-175">파일이 지정 되지 않은 경우이 명령은 현재 디렉터리를 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-175">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="cdd87-176">-s</span><span class="sxs-lookup"><span data-stu-id="cdd87-176">-s</span></span> | <span data-ttu-id="cdd87-177">--서비스</span><span class="sxs-lookup"><span data-stu-id="cdd87-177">--services</span></span> | <span data-ttu-id="cdd87-178">생성 해야 하는 gRPC 서비스의 유형입니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-178">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="cdd87-179">@No__t_0를 지정 하는 경우 `Both` 웹 프로젝트에 사용 되 고 `Client` 웹 프로젝트가 아닌 프로젝트에 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-179">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="cdd87-180">허용 되는 값은 `Both`, `Client`, `Default`, `None`, `Server`입니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-180">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="cdd87-181">-i</span><span class="sxs-lookup"><span data-stu-id="cdd87-181">-i</span></span> | <span data-ttu-id="cdd87-182">--추가 가져오기-디렉터리</span><span class="sxs-lookup"><span data-stu-id="cdd87-182">--additional-import-dirs</span></span> | <span data-ttu-id="cdd87-183">Protobuf 파일에 대 한 가져오기를 확인할 때 사용할 추가 디렉터리입니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-183">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="cdd87-184">세미콜론으로 구분 된 경로 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-184">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="cdd87-185">--액세스</span><span class="sxs-lookup"><span data-stu-id="cdd87-185">--access</span></span> | <span data-ttu-id="cdd87-186">생성 C# 된 클래스에 사용할 액세스 한정자입니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-186">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="cdd87-187">기본값은 `Public`여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-187">Default value is `Public`.</span></span> <span data-ttu-id="cdd87-188">허용 되는 값은 `Internal` 및 `Public`입니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-188">Accepted values are `Internal` and `Public`.</span></span>

## <a name="remove"></a><span data-ttu-id="cdd87-189">제거</span><span class="sxs-lookup"><span data-stu-id="cdd87-189">Remove</span></span>

<span data-ttu-id="cdd87-190">@No__t_0 명령은 *.csproj* 파일에서 Protobuf 참조를 제거 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-190">The `remove` command is used to remove Protobuf references from the *.csproj* file.</span></span> <span data-ttu-id="cdd87-191">이 명령은 경로 인수와 소스 Url을 인수로 받아들입니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-191">The command accepts path arguments and source URLs as arguments.</span></span> <span data-ttu-id="cdd87-192">도구:</span><span class="sxs-lookup"><span data-stu-id="cdd87-192">The tool:</span></span>

* <span data-ttu-id="cdd87-193">Protobuf 참조만 제거 합니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-193">Only removes the Protobuf reference.</span></span>
* <span data-ttu-id="cdd87-194">는 원래 원격 URL에서 다운로드 된 경우에도 해당 파일을 삭제 하지 않습니다 *.*</span><span class="sxs-lookup"><span data-stu-id="cdd87-194">Does not delete the *.proto* file, even if it was originally downloaded from a remote URL.</span></span>

### <a name="usage"></a><span data-ttu-id="cdd87-195">사용법</span><span class="sxs-lookup"><span data-stu-id="cdd87-195">Usage</span></span>

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a><span data-ttu-id="cdd87-196">인수</span><span class="sxs-lookup"><span data-stu-id="cdd87-196">Arguments</span></span>

| <span data-ttu-id="cdd87-197">인수</span><span class="sxs-lookup"><span data-stu-id="cdd87-197">Argument</span></span> | <span data-ttu-id="cdd87-198">설명</span><span class="sxs-lookup"><span data-stu-id="cdd87-198">Description</span></span> |
|-|-|
| <span data-ttu-id="cdd87-199">참조</span><span class="sxs-lookup"><span data-stu-id="cdd87-199">references</span></span> | <span data-ttu-id="cdd87-200">제거할 protobuf 참조의 Url 또는 파일 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-200">The URLs or file paths of the protobuf references to remove.</span></span> |

### <a name="options"></a><span data-ttu-id="cdd87-201">옵션</span><span class="sxs-lookup"><span data-stu-id="cdd87-201">Options</span></span>

| <span data-ttu-id="cdd87-202">짧음 옵션</span><span class="sxs-lookup"><span data-stu-id="cdd87-202">Short option</span></span> | <span data-ttu-id="cdd87-203">긴 옵션</span><span class="sxs-lookup"><span data-stu-id="cdd87-203">Long option</span></span> | <span data-ttu-id="cdd87-204">설명</span><span class="sxs-lookup"><span data-stu-id="cdd87-204">Description</span></span> |
|-|-|-|
| <span data-ttu-id="cdd87-205">-p</span><span class="sxs-lookup"><span data-stu-id="cdd87-205">-p</span></span> | <span data-ttu-id="cdd87-206">--project</span><span class="sxs-lookup"><span data-stu-id="cdd87-206">--project</span></span> | <span data-ttu-id="cdd87-207">작업할 프로젝트 파일의 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-207">The path to the project file to operate on.</span></span> <span data-ttu-id="cdd87-208">파일이 지정 되지 않은 경우이 명령은 현재 디렉터리를 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-208">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="refresh"></a><span data-ttu-id="cdd87-209">새로 고침</span><span class="sxs-lookup"><span data-stu-id="cdd87-209">Refresh</span></span>

<span data-ttu-id="cdd87-210">@No__t_0 명령은 원본 URL에서 최신 콘텐츠로 원격 참조를 업데이트 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-210">The `refresh` command is used to update a remote reference with the latest content from the source URL.</span></span> <span data-ttu-id="cdd87-211">다운로드 파일 경로와 원본 URL을 모두 사용 하 여 업데이트할 참조를 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-211">Both the download file path and the source URL can be used to specify the reference to be updated.</span></span> <span data-ttu-id="cdd87-212">참고:</span><span class="sxs-lookup"><span data-stu-id="cdd87-212">Note:</span></span>

* <span data-ttu-id="cdd87-213">파일 내용의 해시를 비교 하 여 로컬 파일을 업데이트 해야 하는지 여부를 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-213">The hashes of the file contents are compared to determine whether the local file should be updated.</span></span>
* <span data-ttu-id="cdd87-214">타임 스탬프 정보는 비교 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-214">No timestamp information is compared.</span></span>

<span data-ttu-id="cdd87-215">업데이트를 필요로 하는 경우 도구는 항상 로컬 파일을 원격 파일로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-215">The tool always replaces the local file with the remote file if an update is needed.</span></span>

### <a name="usage"></a><span data-ttu-id="cdd87-216">사용법</span><span class="sxs-lookup"><span data-stu-id="cdd87-216">Usage</span></span>

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a><span data-ttu-id="cdd87-217">인수</span><span class="sxs-lookup"><span data-stu-id="cdd87-217">Arguments</span></span>

| <span data-ttu-id="cdd87-218">인수</span><span class="sxs-lookup"><span data-stu-id="cdd87-218">Argument</span></span> | <span data-ttu-id="cdd87-219">설명</span><span class="sxs-lookup"><span data-stu-id="cdd87-219">Description</span></span> |
|-|-|
| <span data-ttu-id="cdd87-220">참조</span><span class="sxs-lookup"><span data-stu-id="cdd87-220">references</span></span> | <span data-ttu-id="cdd87-221">업데이트 해야 하는 원격 protobuf 참조에 대 한 Url 또는 파일 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-221">The URLs or file paths to remote protobuf references that should be updated.</span></span> <span data-ttu-id="cdd87-222">모든 원격 참조를 새로 고치려면이 인수를 비워 둡니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-222">Leave this argument empty to refresh all remote references.</span></span> |

### <a name="options"></a><span data-ttu-id="cdd87-223">옵션</span><span class="sxs-lookup"><span data-stu-id="cdd87-223">Options</span></span>

| <span data-ttu-id="cdd87-224">짧음 옵션</span><span class="sxs-lookup"><span data-stu-id="cdd87-224">Short option</span></span> | <span data-ttu-id="cdd87-225">긴 옵션</span><span class="sxs-lookup"><span data-stu-id="cdd87-225">Long option</span></span> | <span data-ttu-id="cdd87-226">설명</span><span class="sxs-lookup"><span data-stu-id="cdd87-226">Description</span></span> |
|-|-|-|
| <span data-ttu-id="cdd87-227">-p</span><span class="sxs-lookup"><span data-stu-id="cdd87-227">-p</span></span> | <span data-ttu-id="cdd87-228">--project</span><span class="sxs-lookup"><span data-stu-id="cdd87-228">--project</span></span> | <span data-ttu-id="cdd87-229">작업할 프로젝트 파일의 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-229">The path to the project file to operate on.</span></span> <span data-ttu-id="cdd87-230">파일이 지정 되지 않은 경우이 명령은 현재 디렉터리를 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-230">If a file is not specified, the command searches the current directory for one.</span></span>
| | <span data-ttu-id="cdd87-231">--예행 실행</span><span class="sxs-lookup"><span data-stu-id="cdd87-231">--dry-run</span></span> | <span data-ttu-id="cdd87-232">새 콘텐츠를 다운로드 하지 않고 업데이트 되는 파일의 목록을 출력 합니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-232">Outputs a list of files that would be updated without downloading any new content.</span></span>

## <a name="list"></a><span data-ttu-id="cdd87-233">목록</span><span class="sxs-lookup"><span data-stu-id="cdd87-233">List</span></span>

<span data-ttu-id="cdd87-234">@No__t_0 명령은 프로젝트 파일에 모든 Protobuf 참조를 표시 하는 데 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-234">The `list` command is used to display all the Protobuf references in the project file.</span></span> <span data-ttu-id="cdd87-235">열의 모든 값이 기본값이 면 열을 생략할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-235">If all values of a column are default values, the column may be omitted.</span></span>

### <a name="usage"></a><span data-ttu-id="cdd87-236">사용법</span><span class="sxs-lookup"><span data-stu-id="cdd87-236">Usage</span></span>

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a><span data-ttu-id="cdd87-237">옵션</span><span class="sxs-lookup"><span data-stu-id="cdd87-237">Options</span></span>

| <span data-ttu-id="cdd87-238">짧음 옵션</span><span class="sxs-lookup"><span data-stu-id="cdd87-238">Short option</span></span> | <span data-ttu-id="cdd87-239">긴 옵션</span><span class="sxs-lookup"><span data-stu-id="cdd87-239">Long option</span></span> | <span data-ttu-id="cdd87-240">설명</span><span class="sxs-lookup"><span data-stu-id="cdd87-240">Description</span></span> |
|-|-|-|
| <span data-ttu-id="cdd87-241">-p</span><span class="sxs-lookup"><span data-stu-id="cdd87-241">-p</span></span> | <span data-ttu-id="cdd87-242">--project</span><span class="sxs-lookup"><span data-stu-id="cdd87-242">--project</span></span> | <span data-ttu-id="cdd87-243">작업할 프로젝트 파일의 경로입니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-243">The path to the project file to operate on.</span></span> <span data-ttu-id="cdd87-244">파일이 지정 되지 않은 경우이 명령은 현재 디렉터리를 검색 합니다.</span><span class="sxs-lookup"><span data-stu-id="cdd87-244">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cdd87-245">추가 자료</span><span class="sxs-lookup"><span data-stu-id="cdd87-245">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
