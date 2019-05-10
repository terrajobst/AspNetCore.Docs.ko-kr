---
title: ASP.NET Core의 파일 공급자
author: guardrex
description: ASP.NET Core에서 파일 공급자를 사용하여 파일 시스템 액세스를 추상화하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2019
uid: fundamentals/file-providers
ms.openlocfilehash: 93eb48d81a853061a874641e84b4875849690a93
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64886618"
---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="d4b40-103">ASP.NET Core의 파일 공급자</span><span class="sxs-lookup"><span data-stu-id="d4b40-103">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="d4b40-104">작성자: [Steve Smith](https://ardalis.com/) 및 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d4b40-104">By [Steve Smith](https://ardalis.com/) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d4b40-105">ASP.NET Core에서 파일 공급자를 사용하여 파일 시스템 액세스를 추상화합니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-105">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="d4b40-106">파일 공급자는 ASP.NET Core 프레임워크 전체에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-106">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="d4b40-107">[IHostingEnvironment](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment)는 앱의 콘텐츠 루트와 웹 루트를 `IFileProvider` 형식으로 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-107">[IHostingEnvironment](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment) exposes the app's content root and web root as `IFileProvider` types.</span></span>
* <span data-ttu-id="d4b40-108">[정적 파일 미들웨어](xref:fundamentals/static-files)는 파일 공급자를 사용해서 정적 파일을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-108">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="d4b40-109">[Razor](xref:mvc/views/razor)는 파일 공급자를 사용하여 페이지 및 뷰를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-109">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="d4b40-110">.NET Core 도구는 파일 공급자와 GLOB 패턴을 사용해서 게시해야 할 파일을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-110">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="d4b40-111">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d4b40-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="d4b40-112">파일 공급자 인터페이스</span><span class="sxs-lookup"><span data-stu-id="d4b40-112">File Provider interfaces</span></span>

<span data-ttu-id="d4b40-113">기본 인터페이스는 [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider)입니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-113">The primary interface is [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider).</span></span> <span data-ttu-id="d4b40-114">`IFileProvider`는 다음을 수행하는 메서드를 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-114">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="d4b40-115">파일 정보([IFileInfo](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo))를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-115">Obtain file information ([IFileInfo](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo)).</span></span>
* <span data-ttu-id="d4b40-116">디렉터리 정보([IDirectoryContents](/dotnet/api/microsoft.extensions.fileproviders.idirectorycontents))를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-116">Obtain directory information ([IDirectoryContents](/dotnet/api/microsoft.extensions.fileproviders.idirectorycontents)).</span></span>
* <span data-ttu-id="d4b40-117">변경 알림([IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken) 사용)을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-117">Set up change notifications (using an [IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken)).</span></span>

<span data-ttu-id="d4b40-118">`IFileInfo`는 파일 작업에 대한 메서드 및 속성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-118">`IFileInfo` provides methods and properties for working with files:</span></span>

* [<span data-ttu-id="d4b40-119">Exists</span><span class="sxs-lookup"><span data-stu-id="d4b40-119">Exists</span></span>](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.exists)
* [<span data-ttu-id="d4b40-120">IsDirectory</span><span class="sxs-lookup"><span data-stu-id="d4b40-120">IsDirectory</span></span>](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.isdirectory)
* [<span data-ttu-id="d4b40-121">이름</span><span class="sxs-lookup"><span data-stu-id="d4b40-121">Name</span></span>](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.name)
* <span data-ttu-id="d4b40-122">[Length](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.length)(바이트)</span><span class="sxs-lookup"><span data-stu-id="d4b40-122">[Length](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.length) (in bytes)</span></span>
* <span data-ttu-id="d4b40-123">[LastModified](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.lastmodified) 날짜</span><span class="sxs-lookup"><span data-stu-id="d4b40-123">[LastModified](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.lastmodified) date</span></span>

<span data-ttu-id="d4b40-124">[IFileInfo.CreateReadStream](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.createreadstream) 메서드를 사용하여 파일에서 읽을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-124">You can read from the file using the [IFileInfo.CreateReadStream](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.createreadstream) method.</span></span>

<span data-ttu-id="d4b40-125">샘플 앱은 [종속성 주입](xref:fundamentals/dependency-injection)을 통해 앱 전체에서 사용하기 위해 `Startup.ConfigureServices`에 파일 공급자를 구성하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-125">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="d4b40-126">파일 공급자 구현</span><span class="sxs-lookup"><span data-stu-id="d4b40-126">File Provider implementations</span></span>

<span data-ttu-id="d4b40-127">`IFileProvider`의 세 가지 구현을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-127">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="d4b40-128">구현</span><span class="sxs-lookup"><span data-stu-id="d4b40-128">Implementation</span></span> | <span data-ttu-id="d4b40-129">설명</span><span class="sxs-lookup"><span data-stu-id="d4b40-129">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="d4b40-130">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="d4b40-130">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="d4b40-131">물리적 공급자는 시스템의 물리적 파일에 액세스하기 위해서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-131">The physical provider is used to access the system's physical files.</span></span> |
| [<span data-ttu-id="d4b40-132">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="d4b40-132">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="d4b40-133">매니페스트 임베디드 공급자는 어셈블리에 포함된 파일에 액세스하기 위해서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-133">The manifest embedded provider is used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="d4b40-134">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="d4b40-134">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="d4b40-135">마지막으로 복합 공급자는 하나 이상의 개별 공급자로부터 얻어진 파일 및 디렉터리에 대한 복합적인 접근을 지원하기 위해서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-135">The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="d4b40-136">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="d4b40-136">PhysicalFileProvider</span></span>

<span data-ttu-id="d4b40-137">[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider)는 물리적 파일 시스템에 대한 액세스 권한을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-137">The [PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider) provides access to the physical file system.</span></span> <span data-ttu-id="d4b40-138">`PhysicalFileProvider`는 [System.IO.File](/dotnet/api/system.io.file) 형식(물리적 공급자에 대해)을 사용하고 디렉터리와 그 하위 자식에 대한 모든 경로의 범위를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-138">`PhysicalFileProvider` uses the [System.IO.File](/dotnet/api/system.io.file) type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="d4b40-139">이 범위 지정은 지정된 디렉터리와 그 하위 자식 이외의 파일 시스템에 대한 액세스를 방지합니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-139">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="d4b40-140">이 공급자를 인스턴스화하는 경우 디렉터리 경로가 필요하며, 공급자를 사용하여 수행한 모든 요청에 대한 기본 경로로 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-140">When instantiating this provider, a directory path is required and serves as the base path for all requests made using the provider.</span></span> <span data-ttu-id="d4b40-141">`PhysicalFileProvider` 공급자를 직접 인스턴스화하거나 생성자에서 [종속성 주입](xref:fundamentals/dependency-injection)을 통해 `IFileProvider`를 요청할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-141">You can instantiate a `PhysicalFileProvider` provider directly, or you can request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="d4b40-142">**정적 형식**</span><span class="sxs-lookup"><span data-stu-id="d4b40-142">**Static types**</span></span>

<span data-ttu-id="d4b40-143">다음 코드는 `PhysicalFileProvider`를 생성하고 이를 사용하여 디렉터리 콘텐츠 및 파일 정보를 가져오는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-143">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="d4b40-144">이전 예제의 형식:</span><span class="sxs-lookup"><span data-stu-id="d4b40-144">Types in the preceding example:</span></span>

* <span data-ttu-id="d4b40-145">`provider`는 `IFileProvider`입니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-145">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="d4b40-146">`contents`는 `IDirectoryContents`입니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-146">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="d4b40-147">`fileInfo`는 `IFileInfo`입니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-147">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="d4b40-148">파일 공급자는 `applicationRoot`로 지정된 디렉터리를 반복하거나 `GetFileInfo`를 호출하여 파일 정보를 가져오는 데 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-148">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="d4b40-149">파일 공급자는 `applicationRoot` 디렉터리 외에는 액세스 권한이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-149">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="d4b40-150">샘플 앱은 [IHostingEnvironment.ContentRootFileProvider](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.contentrootfileprovider)를 사용하여 앱의 `Startup.ConfigureServices` 클래스에 공급자를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-150">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.contentrootfileprovider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

<span data-ttu-id="d4b40-151">**종속성 주입을 사용하여 파일 공급자 형식 가져오기**</span><span class="sxs-lookup"><span data-stu-id="d4b40-151">**Obtain File Provider types with dependency injection**</span></span>

<span data-ttu-id="d4b40-152">클래스 생성자에 공급자를 주입하고 이를 로컬 필드에 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-152">Inject the provider into any class constructor and assign it to a local field.</span></span> <span data-ttu-id="d4b40-153">클래스의 메서드 전체에 있는 필드를 사용하여 파일에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-153">Use the field throughout the class's methods to access files.</span></span>

<span data-ttu-id="d4b40-154">샘플 앱에서 `IndexModel` 클래스는 `IFileProvider` 인스턴스를 받아 앱의 기본 경로에 대한 디렉터리 콘텐츠를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-154">In the sample app, the `IndexModel` class receives an `IFileProvider` instance to obtain directory contents for the app's base path.</span></span>

<span data-ttu-id="d4b40-155">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="d4b40-155">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="d4b40-156">`IDirectoryContents`는 페이지에서 반복됩니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-156">The `IDirectoryContents` are iterated in the page.</span></span>

<span data-ttu-id="d4b40-157">*Pages/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="d4b40-157">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](file-providers/samples/2.x/FileProviderSample/Pages/Index.cshtml?name=snippet1)]

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="d4b40-158">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="d4b40-158">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="d4b40-159">[ManifestEmbeddedFileProvider](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider)는 어셈블리에 포함된 파일에 액세스하기 위해서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-159">The [ManifestEmbeddedFileProvider](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider) is used to access files embedded within assemblies.</span></span> <span data-ttu-id="d4b40-160">`ManifestEmbeddedFileProvider`는 어셈블리로 컴파일된 매니페스트를 사용하여 포함된 파일의 원래 경로를 재구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-160">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="d4b40-161">포함된 파일의 매니페스트를 생성하려면 `<GenerateEmbeddedFilesManifest>` 속성을 `true`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-161">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="d4b40-162">[&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects)를 사용하여 포함할 파일을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-162">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

<span data-ttu-id="d4b40-163">[GLOB 패턴](#glob-patterns)을 사용하여 어셈블리에 포함할 파일을 하나 이상 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-163">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="d4b40-164">샘플 앱은 `ManifestEmbeddedFileProvider`를 생성하고 현재 실행 중인 어셈블리를 생성자에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-164">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="d4b40-165">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="d4b40-165">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(Assembly.GetEntryAssembly());
```

<span data-ttu-id="d4b40-166">추가 오버로드를 사용하면 다음을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-166">Additional overloads allow you to:</span></span>

* <span data-ttu-id="d4b40-167">상대 파일 경로 지정</span><span class="sxs-lookup"><span data-stu-id="d4b40-167">Specify a relative file path.</span></span>
* <span data-ttu-id="d4b40-168">마지막으로 수정한 날짜로 파일의 범위 지정</span><span class="sxs-lookup"><span data-stu-id="d4b40-168">Scope files to a last modified date.</span></span>
* <span data-ttu-id="d4b40-169">포함된 파일 매니페스트를 포함하는 포함 리소스의 이름 지정</span><span class="sxs-lookup"><span data-stu-id="d4b40-169">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="d4b40-170">오버로드</span><span class="sxs-lookup"><span data-stu-id="d4b40-170">Overload</span></span> | <span data-ttu-id="d4b40-171">설명</span><span class="sxs-lookup"><span data-stu-id="d4b40-171">Description</span></span> |
| -------- | ----------- |
| [<span data-ttu-id="d4b40-172">ManifestEmbeddedFileProvider(Assembly, String)</span><span class="sxs-lookup"><span data-stu-id="d4b40-172">ManifestEmbeddedFileProvider(Assembly, String)</span></span>](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider.-ctor#Microsoft_Extensions_FileProviders_ManifestEmbeddedFileProvider__ctor_System_Reflection_Assembly_System_String_) | <span data-ttu-id="d4b40-173">선택적인 `root` 상대 경로 매개 변수를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-173">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="d4b40-174">`root`를 지정하여 [GetDirectoryContents](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.getdirectorycontents)에 대한 호출의 범위를 제공된 경로 아래의 해당 리소스로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-174">Specify the `root` to scope calls to [GetDirectoryContents](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.getdirectorycontents) to those resources under the provided path.</span></span> |
| [<span data-ttu-id="d4b40-175">ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)</span><span class="sxs-lookup"><span data-stu-id="d4b40-175">ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)</span></span>](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider.-ctor#Microsoft_Extensions_FileProviders_ManifestEmbeddedFileProvider__ctor_System_Reflection_Assembly_System_String_System_DateTimeOffset_) | <span data-ttu-id="d4b40-176">선택적인 `root` 상대 경로 매개 변수 및 `lastModified` 날짜([DateTimeOffset](/dotnet/api/system.datetimeoffset)) 매개 변수를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-176">Accepts an optional `root` relative path parameter and a `lastModified` date ([DateTimeOffset](/dotnet/api/system.datetimeoffset)) parameter.</span></span> <span data-ttu-id="d4b40-177">`lastModified` 날짜는 [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider)로 반환된 [IFileInfo](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo) 인스턴스에 대한 마지막 수정 날짜의 범위를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-177">The `lastModified` date scopes the last modification date for the [IFileInfo](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo) instances returned by the [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider).</span></span> |
| [<span data-ttu-id="d4b40-178">ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)</span><span class="sxs-lookup"><span data-stu-id="d4b40-178">ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)</span></span>](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider.-ctor#Microsoft_Extensions_FileProviders_ManifestEmbeddedFileProvider__ctor_System_Reflection_Assembly_System_String_System_String_System_DateTimeOffset_) | <span data-ttu-id="d4b40-179">선택적인 `root` 상대 경로, `lastModified` 날짜 및 `manifestName` 매개 변수를 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-179">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="d4b40-180">`manifestName`은 매니페스트가 포함된 포함 리소스의 이름을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-180">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="d4b40-181">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="d4b40-181">CompositeFileProvider</span></span>

<span data-ttu-id="d4b40-182">[CompositeFileProvider](/dotnet/api/microsoft.extensions.fileproviders.compositefileprovider)는 `IFileProvider` 인스턴스를 결합해서 다수의 공급자를 이용한 파일 작업을 처리할 수 있는 단일 인터페이스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-182">The [CompositeFileProvider](/dotnet/api/microsoft.extensions.fileproviders.compositefileprovider) combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="d4b40-183">`CompositeFileProvider`를 생성할 때는 생성자에 하나 이상의 `IFileProvider` 인스턴스를 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-183">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="d4b40-184">샘플 앱에서 `PhysicalFileProvider` 및 `ManifestEmbeddedFileProvider`는 앱의 서비스 컨테이너에 등록된 `CompositeFileProvider`에 파일을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-184">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="d4b40-185">변경 내용 관찰</span><span class="sxs-lookup"><span data-stu-id="d4b40-185">Watch for changes</span></span>

<span data-ttu-id="d4b40-186">[IFileProvider.Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch) 메서드는 하나 이상의 파일 또는 디렉터리의 변경 내용을 관찰하는 시나리오를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-186">The [IFileProvider.Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="d4b40-187">`Watch`는 [GLOB 패턴](#glob-patterns)을 사용하여 여러 파일을 지정할 수 있는 경로 문자열을 허용합니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-187">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="d4b40-188">`Watch`는 [IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken)을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-188">`Watch` returns an [IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken).</span></span> <span data-ttu-id="d4b40-189">변경 토큰은 다음을 공개합니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-189">The change token exposes:</span></span>

* <span data-ttu-id="d4b40-190">[HasChanged](/dotnet/api/microsoft.extensions.primitives.ichangetoken.haschanged): 변경이 발생했는지 확인하기 위해 검사할 수 있는 속성입니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-190">[HasChanged](/dotnet/api/microsoft.extensions.primitives.ichangetoken.haschanged): A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="d4b40-191">[RegisterChangeCallback](/dotnet/api/microsoft.extensions.primitives.ichangetoken.registerchangecallback): 지정한 경로 문자열에 대한 변경 내용이 검색되면 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-191">[RegisterChangeCallback](/dotnet/api/microsoft.extensions.primitives.ichangetoken.registerchangecallback): Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="d4b40-192">각 변경 토큰은 단일 변경에 대한 응답으로 자신과 연결된 콜백만 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-192">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="d4b40-193">모니터링을 지속적으로 수행하기 위해서는 다음 예제처럼 [TaskCompletionSource](/dotnet/api/system.threading.tasks.taskcompletionsource-1)를 사용하거나 변경 사항에 대한 응답에서 `IChangeToken` 인스턴스를 다시 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-193">To enable constant monitoring, use a [TaskCompletionSource](/dotnet/api/system.threading.tasks.taskcompletionsource-1) (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="d4b40-194">샘플 앱에서 *WatchConsole* 콘솔 앱은 텍스트 파일이 수정될 때마다 메시지를 표시하도록 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-194">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="d4b40-195">Docker 컨테이너나 네트워크 공유 같은 일부 파일 시스템은 변경 알림을 안정적으로 전송할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-195">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="d4b40-196">`DOTNET_USE_POLLING_FILE_WATCHER` 환경 변수를 `1` 또는 `true`로 설정하여 변경에 대해 파일 시스템을 4초마다 폴링합니다(구성할 수 없음).</span><span class="sxs-lookup"><span data-stu-id="d4b40-196">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="d4b40-197">GLOB 패턴</span><span class="sxs-lookup"><span data-stu-id="d4b40-197">Glob patterns</span></span>

<span data-ttu-id="d4b40-198">파일 시스템 경로는 *GLOB(또는 와일드카드 사용) 패턴*이라고 부르는 와일드카드 패턴을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-198">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="d4b40-199">이러한 패턴을 사용하여 파일 그룹을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-199">Specify groups of files with these patterns.</span></span> <span data-ttu-id="d4b40-200">두 개의 와일드카드 문자는 `*`와 `**`입니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-200">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="d4b40-201">현재 폴더 수준의 모든 항목, 모든 파일명 또는 모든 파일 확장자를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-201">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="d4b40-202">파일 경로의 `/` 및 `.` 문자에 의해서 일치가 중단됩니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-202">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="d4b40-203">여러 디렉터리 수준에서 모든 것을 일치시킵니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-203">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="d4b40-204">디렉터리 계층 구조 내의 여러 파일과 일치시키는 데 재귀적으로 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-204">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="d4b40-205">**GLOB 패턴 예제**</span><span class="sxs-lookup"><span data-stu-id="d4b40-205">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="d4b40-206">특정 디렉터리에 있는 특정 파일을 일치시킵니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-206">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="d4b40-207">특정 디렉터리에서 확장명이 *.txt*인 파일을 모두 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-207">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="d4b40-208">*directory* 폴더보다 정확히 한 수준 아래의 디렉터리에서 모든 `appsettings.json` 파일을 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-208">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="d4b40-209">*directory* 폴더 아래의 모든 곳에서 찾은 확장명이 *.txt*인 파일을 모두 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="d4b40-209">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>
