---
title: ASP.NET Core의 파일 공급자
author: guardrex
description: ASP.NET Core에서 파일 공급자를 사용하여 파일 시스템 액세스를 추상화하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
uid: fundamentals/file-providers
ms.openlocfilehash: a454ca394546184968222ca2ca44d7159b19a12a
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74944310"
---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="cb07b-103">ASP.NET Core의 파일 공급자</span><span class="sxs-lookup"><span data-stu-id="cb07b-103">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="cb07b-104">작성자: [Steve Smith](https://ardalis.com/) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="cb07b-104">By [Steve Smith](https://ardalis.com/) and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cb07b-105">ASP.NET Core에서 파일 공급자를 사용하여 파일 시스템 액세스를 추상화합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-105">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="cb07b-106">파일 공급자는 ASP.NET Core 프레임워크 전체에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-106">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="cb07b-107">`IWebHostEnvironment`는 앱의 [콘텐츠 루트](xref:fundamentals/index#content-root)와 [웹 루트](xref:fundamentals/index#web-root)를 `IFileProvider` 형식으로 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-107">`IWebHostEnvironment` exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="cb07b-108">[정적 파일 미들웨어](xref:fundamentals/static-files)는 파일 공급자를 사용해서 정적 파일을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-108">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="cb07b-109">[Razor](xref:mvc/views/razor)는 파일 공급자를 사용하여 페이지 및 뷰를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-109">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="cb07b-110">.NET Core 도구는 파일 공급자와 GLOB 패턴을 사용해서 게시해야 할 파일을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-110">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="cb07b-111">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cb07b-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="cb07b-112">파일 공급자 인터페이스</span><span class="sxs-lookup"><span data-stu-id="cb07b-112">File Provider interfaces</span></span>

<span data-ttu-id="cb07b-113">기본 인터페이스는 <xref:Microsoft.Extensions.FileProviders.IFileProvider>입니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-113">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="cb07b-114">`IFileProvider`는 다음을 수행하는 메서드를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-114">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="cb07b-115">파일 정보를 가져옵니다(<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span><span class="sxs-lookup"><span data-stu-id="cb07b-115">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="cb07b-116">디렉터리 정보를 가져옵니다(<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span><span class="sxs-lookup"><span data-stu-id="cb07b-116">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="cb07b-117">변경 알림을 설정합니다(<xref:Microsoft.Extensions.Primitives.IChangeToken> 사용).</span><span class="sxs-lookup"><span data-stu-id="cb07b-117">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="cb07b-118">`IFileInfo`는 파일 작업에 대한 메서드 및 속성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-118">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="cb07b-119"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length>(바이트)</span><span class="sxs-lookup"><span data-stu-id="cb07b-119"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="cb07b-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> 날짜</span><span class="sxs-lookup"><span data-stu-id="cb07b-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="cb07b-121">[IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) 메서드를 사용하여 파일에서 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-121">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="cb07b-122">샘플 앱은 [종속성 주입](xref:fundamentals/dependency-injection)을 통해 앱 전체에서 사용하기 위해 `Startup.ConfigureServices`에 파일 공급자를 구성하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-122">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="cb07b-123">파일 공급자 구현</span><span class="sxs-lookup"><span data-stu-id="cb07b-123">File Provider implementations</span></span>

<span data-ttu-id="cb07b-124">`IFileProvider`의 세 가지 구현을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-124">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="cb07b-125">구현</span><span class="sxs-lookup"><span data-stu-id="cb07b-125">Implementation</span></span> | <span data-ttu-id="cb07b-126">설명</span><span class="sxs-lookup"><span data-stu-id="cb07b-126">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="cb07b-127">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="cb07b-127">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="cb07b-128">물리적 공급자는 시스템의 물리적 파일에 액세스하기 위해서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-128">The physical provider is used to access the system's physical files.</span></span> |
| [<span data-ttu-id="cb07b-129">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="cb07b-129">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="cb07b-130">매니페스트 임베디드 공급자는 어셈블리에 포함된 파일에 액세스하기 위해서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-130">The manifest embedded provider is used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="cb07b-131">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="cb07b-131">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="cb07b-132">마지막으로 복합 공급자는 하나 이상의 개별 공급자로부터 얻어진 파일 및 디렉터리에 대한 복합적인 접근을 지원하기 위해서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-132">The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="cb07b-133">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="cb07b-133">PhysicalFileProvider</span></span>

<span data-ttu-id="cb07b-134"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> 는 실제 파일 시스템에 대한 접근을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-134">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="cb07b-135">`PhysicalFileProvider`는 <xref:System.IO.File?displayProperty=fullName> 형식(물리적 공급자에 대해)을 사용하고 디렉터리와 그 하위 자식에 대한 모든 경로의 범위를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-135">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="cb07b-136">이 범위 지정은 지정된 디렉터리와 그 하위 자식 이외의 파일 시스템에 대한 액세스를 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-136">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="cb07b-137">가장 공통적인 `PhysicalFileProvider` 생성 및 사용 시나리오는 [종속성 주입](xref:fundamentals/dependency-injection)을 통해 생성자 내에 `IFileProvider`를 요청하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-137">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="cb07b-138">이 공급자를 직접 인스턴스화하는 경우 디렉터리 경로가 필요하며, 공급자를 사용하여 수행한 모든 요청에 대한 기본 경로로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-138">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="cb07b-139">다음 코드는 `PhysicalFileProvider`를 생성하고 이를 사용하여 디렉터리 콘텐츠 및 파일 정보를 가져오는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-139">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="cb07b-140">이전 예제의 형식:</span><span class="sxs-lookup"><span data-stu-id="cb07b-140">Types in the preceding example:</span></span>

* <span data-ttu-id="cb07b-141">`provider`는 `IFileProvider`입니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-141">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="cb07b-142">`contents`는 `IDirectoryContents`입니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-142">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="cb07b-143">`fileInfo`는 `IFileInfo`입니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-143">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="cb07b-144">파일 공급자는 `applicationRoot`로 지정된 디렉터리를 반복하거나 `GetFileInfo`를 호출하여 파일 정보를 가져오는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-144">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="cb07b-145">파일 공급자는 `applicationRoot` 디렉터리 외에는 액세스 권한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-145">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="cb07b-146">샘플 앱은 [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider)를 사용하여 앱의 `Startup.ConfigureServices` 클래스에 공급자를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-146">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="cb07b-147">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="cb07b-147">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="cb07b-148"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider>는 어셈블리에 포함된 파일에 접근하기 위한 용도로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-148">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="cb07b-149">`ManifestEmbeddedFileProvider`는 어셈블리로 컴파일된 매니페스트를 사용하여 포함된 파일의 원래 경로를 재구성합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-149">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="cb07b-150">[Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) 패키지의 프로젝트에 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-150">Add a package reference to the project for the [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) package.</span></span>

<span data-ttu-id="cb07b-151">포함된 파일의 매니페스트를 생성하려면 `<GenerateEmbeddedFilesManifest>` 속성을 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-151">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="cb07b-152">[\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects)를 사용하여 포함할 파일을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-152">Specify the files to embed with [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

<span data-ttu-id="cb07b-153">[GLOB 패턴](#glob-patterns)을 사용하여 어셈블리에 포함할 파일을 하나 이상 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-153">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="cb07b-154">샘플 앱은 `ManifestEmbeddedFileProvider`를 생성하고 현재 실행 중인 어셈블리를 생성자에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-154">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="cb07b-155">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="cb07b-155">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="cb07b-156">추가 오버로드를 사용하면 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-156">Additional overloads allow you to:</span></span>

* <span data-ttu-id="cb07b-157">상대 파일 경로 지정</span><span class="sxs-lookup"><span data-stu-id="cb07b-157">Specify a relative file path.</span></span>
* <span data-ttu-id="cb07b-158">마지막으로 수정한 날짜로 파일의 범위 지정</span><span class="sxs-lookup"><span data-stu-id="cb07b-158">Scope files to a last modified date.</span></span>
* <span data-ttu-id="cb07b-159">포함된 파일 매니페스트를 포함하는 포함 리소스의 이름 지정</span><span class="sxs-lookup"><span data-stu-id="cb07b-159">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="cb07b-160">오버로드</span><span class="sxs-lookup"><span data-stu-id="cb07b-160">Overload</span></span> | <span data-ttu-id="cb07b-161">설명</span><span class="sxs-lookup"><span data-stu-id="cb07b-161">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="cb07b-162">선택적인 `root` 상대 경로 매개 변수를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-162">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="cb07b-163">`root`를 지정하여 <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*>에 대한 호출의 범위를 제공된 경로의 해당 리소스로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-163">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="cb07b-164">선택적인 `root` 상대 경로 매개 변수 및 `lastModified` 날짜(<xref:System.DateTimeOffset>) 매개 변수를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-164">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="cb07b-165">`lastModified` 날짜는 <xref:Microsoft.Extensions.FileProviders.IFileProvider>가 반환한 <xref:Microsoft.Extensions.FileProviders.IFileInfo> 인스턴스에 대한 마지막 수정 날짜의 범위를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-165">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="cb07b-166">선택적인 `root` 상대 경로, `lastModified` 날짜 및 `manifestName` 매개 변수를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-166">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="cb07b-167">`manifestName`은 매니페스트가 포함된 포함 리소스의 이름을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-167">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="cb07b-168">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="cb07b-168">CompositeFileProvider</span></span>

<span data-ttu-id="cb07b-169"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> 는 `IFileProvider` 의 인스턴스들을 결합해서 다수의 공급자를 이용한 파일 작업을 처리할 수 있는 단일 인터페이스를 제공합니다. </span><span class="sxs-lookup"><span data-stu-id="cb07b-169">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="cb07b-170">`CompositeFileProvider`를 생성할 때는 생성자에 하나 이상의 `IFileProvider` 인스턴스를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-170">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="cb07b-171">샘플 앱에서 `PhysicalFileProvider` 및 `ManifestEmbeddedFileProvider`는 앱의 서비스 컨테이너에 등록된 `CompositeFileProvider`에 파일을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-171">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="cb07b-172">변경 내용 관찰</span><span class="sxs-lookup"><span data-stu-id="cb07b-172">Watch for changes</span></span>

<span data-ttu-id="cb07b-173">[IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) 메서드는 하나 이상의 파일 또는 디렉터리의 변경 내용을 관찰하는 시나리오를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-173">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="cb07b-174">`Watch`는 [GLOB 패턴](#glob-patterns)을 사용하여 여러 파일을 지정할 수 있는 경로 문자열을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-174">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="cb07b-175">`Watch`는 <xref:Microsoft.Extensions.Primitives.IChangeToken>을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-175">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="cb07b-176">변경 토큰은 다음을 공개합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-176">The change token exposes:</span></span>

* <span data-ttu-id="cb07b-177"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; 변경이 발생했는지 확인하기 위해 검사할 수 있는 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-177"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="cb07b-178"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; 지정한 경로 문자열에 대한 변경 내용이 검색되면 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-178"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="cb07b-179">각 변경 토큰은 단일 변경에 대한 응답으로 자신과 연결된 콜백만 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-179">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="cb07b-180">모니터링을 지속적으로 수행하기 위해서는 다음 예처럼 <xref:System.Threading.Tasks.TaskCompletionSource`1>를 사용하거나 변경 사항에 대한 응답에서 `IChangeToken` 인스턴스를 다시 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-180">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="cb07b-181">샘플 앱에서 *WatchConsole* 콘솔 앱은 텍스트 파일이 수정될 때마다 메시지를 표시하도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-181">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="cb07b-182">Docker 컨테이너나 네트워크 공유 같은 일부 파일 시스템은 변경 알림을 안정적으로 전송할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-182">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="cb07b-183">`DOTNET_USE_POLLING_FILE_WATCHER` 환경 변수를 `1` 또는 `true`로 설정하여 변경에 대해 파일 시스템을 4초마다 폴링합니다(구성할 수 없음).</span><span class="sxs-lookup"><span data-stu-id="cb07b-183">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="cb07b-184">GLOB 패턴</span><span class="sxs-lookup"><span data-stu-id="cb07b-184">Glob patterns</span></span>

<span data-ttu-id="cb07b-185">파일 시스템 경로는 *GLOB(또는 와일드카드 사용) 패턴*이라고 부르는 와일드카드 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-185">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="cb07b-186">이러한 패턴을 사용하여 파일 그룹을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-186">Specify groups of files with these patterns.</span></span> <span data-ttu-id="cb07b-187">두 개의 와일드카드 문자는 `*`와 `**`입니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-187">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="cb07b-188">현재 폴더 수준의 모든 항목, 모든 파일명 또는 모든 파일 확장자를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-188">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="cb07b-189">파일 경로의 `/` 및 `.` 문자에 의해서 일치가 중단됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-189">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="cb07b-190">여러 디렉터리 수준에서 모든 것을 일치시킵니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-190">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="cb07b-191">디렉터리 계층 구조 내의 여러 파일과 일치시키는 데 재귀적으로 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-191">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="cb07b-192">**GLOB 패턴 예제**</span><span class="sxs-lookup"><span data-stu-id="cb07b-192">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="cb07b-193">특정 디렉터리에 있는 특정 파일을 일치시킵니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-193">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="cb07b-194">특정 디렉터리에서 확장명이 *.txt*인 파일을 모두 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-194">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="cb07b-195">*directory* 폴더보다 정확히 한 수준 아래의 디렉터리에서 모든 `appsettings.json` 파일을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-195">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="cb07b-196">*directory* 폴더 아래의 모든 곳에서 찾은 확장명이 *.txt*인 파일을 모두 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-196">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cb07b-197">ASP.NET Core에서 파일 공급자를 사용하여 파일 시스템 액세스를 추상화합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-197">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="cb07b-198">파일 공급자는 ASP.NET Core 프레임워크 전체에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-198">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="cb07b-199"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment>는 앱의 [콘텐츠 루트](xref:fundamentals/index#content-root)와 [웹 루트](xref:fundamentals/index#web-root)를 `IFileProvider` 형식으로 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-199"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="cb07b-200">[정적 파일 미들웨어](xref:fundamentals/static-files)는 파일 공급자를 사용해서 정적 파일을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-200">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="cb07b-201">[Razor](xref:mvc/views/razor)는 파일 공급자를 사용하여 페이지 및 뷰를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-201">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="cb07b-202">.NET Core 도구는 파일 공급자와 GLOB 패턴을 사용해서 게시해야 할 파일을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-202">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="cb07b-203">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cb07b-203">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="cb07b-204">파일 공급자 인터페이스</span><span class="sxs-lookup"><span data-stu-id="cb07b-204">File Provider interfaces</span></span>

<span data-ttu-id="cb07b-205">기본 인터페이스는 <xref:Microsoft.Extensions.FileProviders.IFileProvider>입니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-205">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="cb07b-206">`IFileProvider`는 다음을 수행하는 메서드를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-206">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="cb07b-207">파일 정보를 가져옵니다(<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span><span class="sxs-lookup"><span data-stu-id="cb07b-207">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="cb07b-208">디렉터리 정보를 가져옵니다(<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span><span class="sxs-lookup"><span data-stu-id="cb07b-208">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="cb07b-209">변경 알림을 설정합니다(<xref:Microsoft.Extensions.Primitives.IChangeToken> 사용).</span><span class="sxs-lookup"><span data-stu-id="cb07b-209">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="cb07b-210">`IFileInfo`는 파일 작업에 대한 메서드 및 속성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-210">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="cb07b-211"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length>(바이트)</span><span class="sxs-lookup"><span data-stu-id="cb07b-211"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="cb07b-212"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> 날짜</span><span class="sxs-lookup"><span data-stu-id="cb07b-212"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="cb07b-213">[IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) 메서드를 사용하여 파일에서 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-213">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="cb07b-214">샘플 앱은 [종속성 주입](xref:fundamentals/dependency-injection)을 통해 앱 전체에서 사용하기 위해 `Startup.ConfigureServices`에 파일 공급자를 구성하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-214">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="cb07b-215">파일 공급자 구현</span><span class="sxs-lookup"><span data-stu-id="cb07b-215">File Provider implementations</span></span>

<span data-ttu-id="cb07b-216">`IFileProvider`의 세 가지 구현을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-216">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="cb07b-217">구현</span><span class="sxs-lookup"><span data-stu-id="cb07b-217">Implementation</span></span> | <span data-ttu-id="cb07b-218">설명</span><span class="sxs-lookup"><span data-stu-id="cb07b-218">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="cb07b-219">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="cb07b-219">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="cb07b-220">물리적 공급자는 시스템의 물리적 파일에 액세스하기 위해서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-220">The physical provider is used to access the system's physical files.</span></span> |
| [<span data-ttu-id="cb07b-221">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="cb07b-221">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="cb07b-222">매니페스트 임베디드 공급자는 어셈블리에 포함된 파일에 액세스하기 위해서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-222">The manifest embedded provider is used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="cb07b-223">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="cb07b-223">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="cb07b-224">마지막으로 복합 공급자는 하나 이상의 개별 공급자로부터 얻어진 파일 및 디렉터리에 대한 복합적인 접근을 지원하기 위해서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-224">The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="cb07b-225">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="cb07b-225">PhysicalFileProvider</span></span>

<span data-ttu-id="cb07b-226"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> 는 실제 파일 시스템에 대한 접근을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-226">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="cb07b-227">`PhysicalFileProvider`는 <xref:System.IO.File?displayProperty=fullName> 형식(물리적 공급자에 대해)을 사용하고 디렉터리와 그 하위 자식에 대한 모든 경로의 범위를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-227">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="cb07b-228">이 범위 지정은 지정된 디렉터리와 그 하위 자식 이외의 파일 시스템에 대한 액세스를 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-228">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="cb07b-229">가장 공통적인 `PhysicalFileProvider` 생성 및 사용 시나리오는 [종속성 주입](xref:fundamentals/dependency-injection)을 통해 생성자 내에 `IFileProvider`를 요청하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-229">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="cb07b-230">이 공급자를 직접 인스턴스화하는 경우 디렉터리 경로가 필요하며, 공급자를 사용하여 수행한 모든 요청에 대한 기본 경로로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-230">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="cb07b-231">다음 코드는 `PhysicalFileProvider`를 생성하고 이를 사용하여 디렉터리 콘텐츠 및 파일 정보를 가져오는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-231">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="cb07b-232">이전 예제의 형식:</span><span class="sxs-lookup"><span data-stu-id="cb07b-232">Types in the preceding example:</span></span>

* <span data-ttu-id="cb07b-233">`provider`는 `IFileProvider`입니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-233">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="cb07b-234">`contents`는 `IDirectoryContents`입니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-234">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="cb07b-235">`fileInfo`는 `IFileInfo`입니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-235">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="cb07b-236">파일 공급자는 `applicationRoot`로 지정된 디렉터리를 반복하거나 `GetFileInfo`를 호출하여 파일 정보를 가져오는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-236">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="cb07b-237">파일 공급자는 `applicationRoot` 디렉터리 외에는 액세스 권한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-237">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="cb07b-238">샘플 앱은 [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider)를 사용하여 앱의 `Startup.ConfigureServices` 클래스에 공급자를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-238">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="cb07b-239">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="cb07b-239">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="cb07b-240"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider>는 어셈블리에 포함된 파일에 접근하기 위한 용도로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-240">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="cb07b-241">`ManifestEmbeddedFileProvider`는 어셈블리로 컴파일된 매니페스트를 사용하여 포함된 파일의 원래 경로를 재구성합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-241">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="cb07b-242">포함된 파일의 매니페스트를 생성하려면 `<GenerateEmbeddedFilesManifest>` 속성을 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-242">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="cb07b-243">[&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects)를 사용하여 포함할 파일을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-243">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="cb07b-244">[GLOB 패턴](#glob-patterns)을 사용하여 어셈블리에 포함할 파일을 하나 이상 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-244">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="cb07b-245">샘플 앱은 `ManifestEmbeddedFileProvider`를 생성하고 현재 실행 중인 어셈블리를 생성자에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-245">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="cb07b-246">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="cb07b-246">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="cb07b-247">추가 오버로드를 사용하면 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-247">Additional overloads allow you to:</span></span>

* <span data-ttu-id="cb07b-248">상대 파일 경로 지정</span><span class="sxs-lookup"><span data-stu-id="cb07b-248">Specify a relative file path.</span></span>
* <span data-ttu-id="cb07b-249">마지막으로 수정한 날짜로 파일의 범위 지정</span><span class="sxs-lookup"><span data-stu-id="cb07b-249">Scope files to a last modified date.</span></span>
* <span data-ttu-id="cb07b-250">포함된 파일 매니페스트를 포함하는 포함 리소스의 이름 지정</span><span class="sxs-lookup"><span data-stu-id="cb07b-250">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="cb07b-251">오버로드</span><span class="sxs-lookup"><span data-stu-id="cb07b-251">Overload</span></span> | <span data-ttu-id="cb07b-252">설명</span><span class="sxs-lookup"><span data-stu-id="cb07b-252">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="cb07b-253">선택적인 `root` 상대 경로 매개 변수를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-253">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="cb07b-254">`root`를 지정하여 <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*>에 대한 호출의 범위를 제공된 경로의 해당 리소스로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-254">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="cb07b-255">선택적인 `root` 상대 경로 매개 변수 및 `lastModified` 날짜(<xref:System.DateTimeOffset>) 매개 변수를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-255">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="cb07b-256">`lastModified` 날짜는 <xref:Microsoft.Extensions.FileProviders.IFileProvider>가 반환한 <xref:Microsoft.Extensions.FileProviders.IFileInfo> 인스턴스에 대한 마지막 수정 날짜의 범위를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-256">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="cb07b-257">선택적인 `root` 상대 경로, `lastModified` 날짜 및 `manifestName` 매개 변수를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-257">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="cb07b-258">`manifestName`은 매니페스트가 포함된 포함 리소스의 이름을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-258">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="cb07b-259">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="cb07b-259">CompositeFileProvider</span></span>

<span data-ttu-id="cb07b-260"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> 는 `IFileProvider` 의 인스턴스들을 결합해서 다수의 공급자를 이용한 파일 작업을 처리할 수 있는 단일 인터페이스를 제공합니다. </span><span class="sxs-lookup"><span data-stu-id="cb07b-260">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="cb07b-261">`CompositeFileProvider`를 생성할 때는 생성자에 하나 이상의 `IFileProvider` 인스턴스를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-261">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="cb07b-262">샘플 앱에서 `PhysicalFileProvider` 및 `ManifestEmbeddedFileProvider`는 앱의 서비스 컨테이너에 등록된 `CompositeFileProvider`에 파일을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-262">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="cb07b-263">변경 내용 관찰</span><span class="sxs-lookup"><span data-stu-id="cb07b-263">Watch for changes</span></span>

<span data-ttu-id="cb07b-264">[IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) 메서드는 하나 이상의 파일 또는 디렉터리의 변경 내용을 관찰하는 시나리오를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-264">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="cb07b-265">`Watch`는 [GLOB 패턴](#glob-patterns)을 사용하여 여러 파일을 지정할 수 있는 경로 문자열을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-265">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="cb07b-266">`Watch`는 <xref:Microsoft.Extensions.Primitives.IChangeToken>을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-266">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="cb07b-267">변경 토큰은 다음을 공개합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-267">The change token exposes:</span></span>

* <span data-ttu-id="cb07b-268"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; 변경이 발생했는지 확인하기 위해 검사할 수 있는 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-268"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="cb07b-269"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; 지정한 경로 문자열에 대한 변경 내용이 검색되면 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-269"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="cb07b-270">각 변경 토큰은 단일 변경에 대한 응답으로 자신과 연결된 콜백만 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-270">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="cb07b-271">모니터링을 지속적으로 수행하기 위해서는 다음 예처럼 <xref:System.Threading.Tasks.TaskCompletionSource`1>를 사용하거나 변경 사항에 대한 응답에서 `IChangeToken` 인스턴스를 다시 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-271">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="cb07b-272">샘플 앱에서 *WatchConsole* 콘솔 앱은 텍스트 파일이 수정될 때마다 메시지를 표시하도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-272">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="cb07b-273">Docker 컨테이너나 네트워크 공유 같은 일부 파일 시스템은 변경 알림을 안정적으로 전송할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-273">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="cb07b-274">`DOTNET_USE_POLLING_FILE_WATCHER` 환경 변수를 `1` 또는 `true`로 설정하여 변경에 대해 파일 시스템을 4초마다 폴링합니다(구성할 수 없음).</span><span class="sxs-lookup"><span data-stu-id="cb07b-274">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="cb07b-275">GLOB 패턴</span><span class="sxs-lookup"><span data-stu-id="cb07b-275">Glob patterns</span></span>

<span data-ttu-id="cb07b-276">파일 시스템 경로는 *GLOB(또는 와일드카드 사용) 패턴*이라고 부르는 와일드카드 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-276">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="cb07b-277">이러한 패턴을 사용하여 파일 그룹을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-277">Specify groups of files with these patterns.</span></span> <span data-ttu-id="cb07b-278">두 개의 와일드카드 문자는 `*`와 `**`입니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-278">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="cb07b-279">현재 폴더 수준의 모든 항목, 모든 파일명 또는 모든 파일 확장자를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-279">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="cb07b-280">파일 경로의 `/` 및 `.` 문자에 의해서 일치가 중단됩니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-280">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="cb07b-281">여러 디렉터리 수준에서 모든 것을 일치시킵니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-281">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="cb07b-282">디렉터리 계층 구조 내의 여러 파일과 일치시키는 데 재귀적으로 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-282">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="cb07b-283">**GLOB 패턴 예제**</span><span class="sxs-lookup"><span data-stu-id="cb07b-283">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="cb07b-284">특정 디렉터리에 있는 특정 파일을 일치시킵니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-284">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="cb07b-285">특정 디렉터리에서 확장명이 *.txt*인 파일을 모두 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-285">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="cb07b-286">*directory* 폴더보다 정확히 한 수준 아래의 디렉터리에서 모든 `appsettings.json` 파일을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-286">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="cb07b-287">*directory* 폴더 아래의 모든 곳에서 찾은 확장명이 *.txt*인 파일을 모두 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="cb07b-287">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end
