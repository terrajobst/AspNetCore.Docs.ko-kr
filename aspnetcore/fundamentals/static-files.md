---
title: ASP.NET Core의 고정 파일
author: rick-anderson
description: ASP.NET Core 웹앱에서 정적 파일을 제공 및 보호하고 정적 파일 호스팅 미들웨어 동작을 구성하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2019
uid: fundamentals/static-files
ms.openlocfilehash: 12c7b39bee462ff83188a5a0f10b133ca273863b
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59425064"
---
# <a name="static-files-in-aspnet-core"></a><span data-ttu-id="e4265-103">ASP.NET Core의 고정 파일</span><span class="sxs-lookup"><span data-stu-id="e4265-103">Static files in ASP.NET Core</span></span>

<span data-ttu-id="e4265-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="e4265-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="e4265-105">HTML, CSS, 이미지 및 JavaScript와 같은 정적 파일은 ASP.NET Core 앱이 클라이언트에 직접 제공하는 자산입니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-105">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients.</span></span> <span data-ttu-id="e4265-106">일부 구성은 이러한 파일을 제공하는 데 필수적입니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-106">Some configuration is required to enable serving of these files.</span></span>

<span data-ttu-id="e4265-107">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e4265-107">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="e4265-108">정적 파일 제공</span><span class="sxs-lookup"><span data-stu-id="e4265-108">Serve static files</span></span>

<span data-ttu-id="e4265-109">정적 파일은 프로젝트의 웹 루트 디렉터리 내에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-109">Static files are stored within your project's web root directory.</span></span> <span data-ttu-id="e4265-110">기본 디렉터리는 *\<content_root>/wwwroot*이지만, [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) 메서드를 통해 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-110">The default directory is *\<content_root>/wwwroot*, but it can be changed via the [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) method.</span></span> <span data-ttu-id="e4265-111">자세한 정보는 [콘텐츠 루트](xref:fundamentals/index#content-root) 및 [웹 루트](xref:fundamentals/index#web-root)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e4265-111">See [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root) for more information.</span></span>

<span data-ttu-id="e4265-112">앱의 웹 호스트에서 콘텐츠 루트 디렉터리를 인식하도록 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-112">The app's web host must be made aware of the content root directory.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="e4265-113">`WebHost.CreateDefaultBuilder` 메서드는 콘텐츠 루트를 현재 디렉터리로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-113">The `WebHost.CreateDefaultBuilder` method sets the content root to the current directory:</span></span>

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="e4265-114">`Program.Main` 내부에 있는 [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseContentRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_)를 호출하여 콘텐츠 루트를 현재 디렉터리로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-114">Set the content root to the current directory by invoking [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseContentRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) inside of `Program.Main`:</span></span>

[!code-csharp[](static-files/samples/1x/Program.cs?name=snippet_ProgramClass&highlight=7)]

::: moniker-end

<span data-ttu-id="e4265-115">정적 파일은 웹 루트를 기준으로 하는 경로를 통해 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-115">Static files are accessible via a path relative to the web root.</span></span> <span data-ttu-id="e4265-116">예를 들어 **웹 애플리케이션** 프로젝트 템플릿에는 *wwwroot* 폴더 내에 여러 폴더를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-116">For example, the **Web Application** project template contains several folders within the *wwwroot* folder:</span></span>

* <span data-ttu-id="e4265-117">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="e4265-117">**wwwroot**</span></span>
  * <span data-ttu-id="e4265-118">**css**</span><span class="sxs-lookup"><span data-stu-id="e4265-118">**css**</span></span>
  * <span data-ttu-id="e4265-119">**images**</span><span class="sxs-lookup"><span data-stu-id="e4265-119">**images**</span></span>
  * <span data-ttu-id="e4265-120">**js**</span><span class="sxs-lookup"><span data-stu-id="e4265-120">**js**</span></span>

<span data-ttu-id="e4265-121">*images* 하위 폴더에 있는 파일에 액세스하기 위한 URI 형식은 *http://\<server_address>/images/\<image_file_name>* 입니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-121">The URI format to access a file in the *images* subfolder is *http://\<server_address>/images/\<image_file_name>*.</span></span> <span data-ttu-id="e4265-122">예: *http://localhost:9189/images/banner3.svg*.</span><span class="sxs-lookup"><span data-stu-id="e4265-122">For example, *http://localhost:9189/images/banner3.svg*.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="e4265-123">.NET Framework를 대상으로 하는 경우 [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) 패키지를 프로젝트에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-123">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to your project.</span></span> <span data-ttu-id="e4265-124">.NET Core를 대상으로 하는 경우 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 이 패키지가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-124">If targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) includes this package.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="e4265-125">.NET Framework를 대상으로 하는 경우 [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) 패키지를 프로젝트에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-125">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to your project.</span></span> <span data-ttu-id="e4265-126">.NET Core를 대상으로 하는 경우는 [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage)가 이 패키지를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-126">If targeting .NET Core, the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) includes this package.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="e4265-127">[Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) 패키지를 프로젝트에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-127">Add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to your project.</span></span>

::: moniker-end

<span data-ttu-id="e4265-128">정적 파일을 제공할 수 있도록 [미들웨어](xref:fundamentals/middleware/index)를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-128">Configure the [middleware](xref:fundamentals/middleware/index) which enables the serving of static files.</span></span>

### <a name="serve-files-inside-of-web-root"></a><span data-ttu-id="e4265-129">웹 루트 내부에 있는 파일을 제공합니다</span><span class="sxs-lookup"><span data-stu-id="e4265-129">Serve files inside of web root</span></span>

<span data-ttu-id="e4265-130">`Startup.Configure` 내부에 [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-130">Invoke the [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) method within `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

<span data-ttu-id="e4265-131">매개 변수가 없는 `UseStaticFiles` 메서드 오버로드는 웹 루트에 있는 파일을 제공 가능으로 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-131">The parameterless `UseStaticFiles` method overload marks the files in web root as servable.</span></span> <span data-ttu-id="e4265-132">다음 표시는 *wwwroot/images/banner1.svg*를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-132">The following markup references *wwwroot/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

<span data-ttu-id="e4265-133">위의 코드에서 물결표 문자 `~/`는 webroot를 가리킵니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-133">In the preceding code, the tilde character `~/` points to webroot.</span></span> <span data-ttu-id="e4265-134">자세한 내용은 [웹 루트](xref:fundamentals/index#web-root)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e4265-134">For more information, see [Web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="e4265-135">웹 루트 외부에 있는 파일을 제공합니다</span><span class="sxs-lookup"><span data-stu-id="e4265-135">Serve files outside of web root</span></span>

<span data-ttu-id="e4265-136">제공할 정적 파일이 웹 루트 외부에 있는 디렉터리 계층 구조를 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-136">Consider a directory hierarchy in which the static files to be served reside outside of the web root:</span></span>

* <span data-ttu-id="e4265-137">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="e4265-137">**wwwroot**</span></span>
  * <span data-ttu-id="e4265-138">**css**</span><span class="sxs-lookup"><span data-stu-id="e4265-138">**css**</span></span>
  * <span data-ttu-id="e4265-139">**images**</span><span class="sxs-lookup"><span data-stu-id="e4265-139">**images**</span></span>
  * <span data-ttu-id="e4265-140">**js**</span><span class="sxs-lookup"><span data-stu-id="e4265-140">**js**</span></span>
* <span data-ttu-id="e4265-141">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="e4265-141">**MyStaticFiles**</span></span>
  * <span data-ttu-id="e4265-142">**images**</span><span class="sxs-lookup"><span data-stu-id="e4265-142">**images**</span></span>
    * <span data-ttu-id="e4265-143">*banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="e4265-143">*banner1.svg*</span></span>

<span data-ttu-id="e4265-144">요청은 정적 파일 미들웨어를 다음과 같이 구성하여 *banner1.svg* 파일에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-144">A request can access the *banner1.svg* file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](static-files/samples/1x/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

<span data-ttu-id="e4265-145">위의 코드에서 *MyStaticFiles* 디렉터리 계층 구조가 *StaticFiles* URI 세그먼트를 통해 공개적으로 노출됩니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-145">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="e4265-146">*http://\<server_address>/StaticFiles/images/banner1.svg*에 대한 요청은 *banner1.svg* 파일을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-146">A request to *http://\<server_address>/StaticFiles/images/banner1.svg* serves the *banner1.svg* file.</span></span>

<span data-ttu-id="e4265-147">다음 표시는 *MyStaticFiles/images/banner1.svg*를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-147">The following markup references *MyStaticFiles/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a><span data-ttu-id="e4265-148">HTTP 응답 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="e4265-148">Set HTTP response headers</span></span>

<span data-ttu-id="e4265-149">[StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions) 개체는 HTTP 응답 헤더를 설정하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-149">A [StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions) object can be used to set HTTP response headers.</span></span> <span data-ttu-id="e4265-150">웹 루트에서 제공되는 정적 파일을 구성하는 것 외에도 다음 코드는 `Cache-Control` 헤더를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-150">In addition to configuring static file serving from the web root, the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](static-files/samples/1x/StartupAddHeader.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="e4265-151">[HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) 메서드는 [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) 패키지에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-151">The [HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) method exists in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

<span data-ttu-id="e4265-152">개발 환경에서 파일은 10분(600초) 동안 공개적으로 캐시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-152">The files have been made publicly cacheable for 10 minutes (600 seconds) in the Development environment:</span></span>

![추가된 캐시 제어 헤더를 보여 주는 응답 헤더](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="e4265-154">정적 파일 권한 부여</span><span class="sxs-lookup"><span data-stu-id="e4265-154">Static file authorization</span></span>

<span data-ttu-id="e4265-155">정적 파일 미들웨어는 권한 부여 검사를 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-155">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="e4265-156">*wwwroot* 아래의 항목을 비롯한 제공되는 모든 파일은 공개적으로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-156">Any files served by it, including those under *wwwroot*, are publicly accessible.</span></span> <span data-ttu-id="e4265-157">권한 부여를 기준으로 파일을 제공하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-157">To serve files based on authorization:</span></span>

* <span data-ttu-id="e4265-158">*wwwroot* 외부의 항목 및 정적 파일 미들웨어에 액세스할 수 있는 모든 디렉터리를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-158">Store them outside of *wwwroot* and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="e4265-159">권한 부여가 적용되는 동작 메서드를 통해 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-159">Serve them via an action method to which authorization is applied.</span></span> <span data-ttu-id="e4265-160">[FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult) 개체를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-160">Return a [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult) object:</span></span>

  [!code-csharp[](static-files/samples/1x/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a><span data-ttu-id="e4265-161">디렉터리 검색 사용</span><span class="sxs-lookup"><span data-stu-id="e4265-161">Enable directory browsing</span></span>

<span data-ttu-id="e4265-162">디렉터리 검색을 사용하면 웹앱 사용자가 지정된 디렉터리 내 디렉터리 목록 및 파일을 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-162">Directory browsing allows users of your web app to see a directory listing and files within a specified directory.</span></span> <span data-ttu-id="e4265-163">기본적으로 디렉터리 검색은 보안상의 이유로 비활성화되어 있습니다([고려할 사항](#considerations) 참조).</span><span class="sxs-lookup"><span data-stu-id="e4265-163">Directory browsing is disabled by default for security reasons (see [Considerations](#considerations)).</span></span> <span data-ttu-id="e4265-164">`Startup.Configure`에서 [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) 메서드를 호출하여 디렉터리 검색을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-164">Enable directory browsing by invoking the [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) method in `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

<span data-ttu-id="e4265-165">`Startup.ConfigureServices`에서 [AddDirectoryBrowser](/dotnet/api/microsoft.extensions.dependencyinjection.directorybrowserserviceextensions.adddirectorybrowser#Microsoft_Extensions_DependencyInjection_DirectoryBrowserServiceExtensions_AddDirectoryBrowser_Microsoft_Extensions_DependencyInjection_IServiceCollection_) 메서드를 호출하여 필요한 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-165">Add required services by invoking the [AddDirectoryBrowser](/dotnet/api/microsoft.extensions.dependencyinjection.directorybrowserserviceextensions.adddirectorybrowser#Microsoft_Extensions_DependencyInjection_DirectoryBrowserServiceExtensions_AddDirectoryBrowser_Microsoft_Extensions_DependencyInjection_IServiceCollection_) method from `Startup.ConfigureServices`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

<span data-ttu-id="e4265-166">위의 코드를 통해 각 파일 및 폴더에 대한 링크가 있는 URL *http://\<server_address>/MyImages*를 사용하여 *wwwroot/images* 폴더의 디렉터리 검색을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-166">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*, with links to each file and folder:</span></span>

![디렉터리 검색](static-files/_static/dir-browse.png)

<span data-ttu-id="e4265-168">검색을 활성화하는 경우 보안 위험에 대한 [고려할 사항](#considerations)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e4265-168">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span>

<span data-ttu-id="e4265-169">다음 예제에서 두 `UseStaticFiles` 호출을 참고하세요.</span><span class="sxs-lookup"><span data-stu-id="e4265-169">Note the two `UseStaticFiles` calls in the following example.</span></span> <span data-ttu-id="e4265-170">첫 번째 호출은 *wwwroot* 폴더에서 정적 파일 제공을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-170">The first call enables the serving of static files in the *wwwroot* folder.</span></span> <span data-ttu-id="e4265-171">두 번째 호출은 URL *http://\<server_address>/MyImages*를 사용하여 *wwwroot/images* 폴더의 디렉터리 검색을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-171">The second call enables directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a><span data-ttu-id="e4265-172">기본 문서 제공</span><span class="sxs-lookup"><span data-stu-id="e4265-172">Serve a default document</span></span>

<span data-ttu-id="e4265-173">기본 홈페이지를 설정하면 방문자가 사이트를 방문할 때 논리적 시작점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-173">Setting a default home page provides visitors a logical starting point when visiting your site.</span></span> <span data-ttu-id="e4265-174">URI를 정규화하는 사용자 없이 기본 페이지를 제공하려면 `Startup.Configure`에서 [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-174">To serve a default page without the user fully qualifying the URI, call the [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) method from `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> <span data-ttu-id="e4265-175">`UseStaticFiles`가 기본 파일을 제공하기 전에 `UseDefaultFiles`를 먼저 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-175">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="e4265-176">`UseDefaultFiles`는 파일을 실제로 제공하지 않는 URL 재작성기입니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-176">`UseDefaultFiles` is a URL rewriter that doesn't actually serve the file.</span></span> <span data-ttu-id="e4265-177">파일을 제공하도록 `UseStaticFiles`를 통해 정적 파일 미들웨어를 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-177">Enable Static File Middleware via `UseStaticFiles` to serve the file.</span></span>

<span data-ttu-id="e4265-178">`UseDefaultFiles`를 사용하여 다음에 대한 폴더 검색을 요청합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-178">With `UseDefaultFiles`, requests to a folder search for:</span></span>

* <span data-ttu-id="e4265-179">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="e4265-179">*default.htm*</span></span>
* <span data-ttu-id="e4265-180">*default.html*</span><span class="sxs-lookup"><span data-stu-id="e4265-180">*default.html*</span></span>
* <span data-ttu-id="e4265-181">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="e4265-181">*index.htm*</span></span>
* <span data-ttu-id="e4265-182">*index.html*</span><span class="sxs-lookup"><span data-stu-id="e4265-182">*index.html*</span></span>

<span data-ttu-id="e4265-183">목록에서 첫 번째 파일이 마치 요청이 정규화된 URI인 것처럼 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-183">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="e4265-184">브라우저 URL이 요청된 URI를 계속 반영합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-184">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="e4265-185">다음 코드는 기본 파일 이름을 *mydefault.html*로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-185">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](static-files/samples/1x/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a><span data-ttu-id="e4265-186">UseFileServer</span><span class="sxs-lookup"><span data-stu-id="e4265-186">UseFileServer</span></span>

<span data-ttu-id="e4265-187">[UseFileServer](/dotnet/api/microsoft.aspnetcore.builder.fileserverextensions.usefileserver#Microsoft_AspNetCore_Builder_FileServerExtensions_UseFileServer_Microsoft_AspNetCore_Builder_IApplicationBuilder_)는 `UseStaticFiles`, `UseDefaultFiles` 및 `UseDirectoryBrowser`의 기능을 결합합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-187">[UseFileServer](/dotnet/api/microsoft.aspnetcore.builder.fileserverextensions.usefileserver#Microsoft_AspNetCore_Builder_FileServerExtensions_UseFileServer_Microsoft_AspNetCore_Builder_IApplicationBuilder_) combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="e4265-188">다음 코드는 정적 파일의 제공 및 기본 파일을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-188">The following code enables the serving of static files and the default file.</span></span> <span data-ttu-id="e4265-189">디렉터리 검색 기능이 활성화되지 않았습니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-189">Directory browsing isn't enabled.</span></span>

```csharp
app.UseFileServer();
```

<span data-ttu-id="e4265-190">다음 코드는 매개 변수가 없는 오버로드에서 디렉터리 검색을 활성화하여 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-190">The following code builds upon the parameterless overload by enabling directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="e4265-191">다음 디렉터리 계층 구조를 고려합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-191">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="e4265-192">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="e4265-192">**wwwroot**</span></span>
  * <span data-ttu-id="e4265-193">**css**</span><span class="sxs-lookup"><span data-stu-id="e4265-193">**css**</span></span>
  * <span data-ttu-id="e4265-194">**images**</span><span class="sxs-lookup"><span data-stu-id="e4265-194">**images**</span></span>
  * <span data-ttu-id="e4265-195">**js**</span><span class="sxs-lookup"><span data-stu-id="e4265-195">**js**</span></span>
* <span data-ttu-id="e4265-196">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="e4265-196">**MyStaticFiles**</span></span>
  * <span data-ttu-id="e4265-197">**images**</span><span class="sxs-lookup"><span data-stu-id="e4265-197">**images**</span></span>
    * <span data-ttu-id="e4265-198">*banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="e4265-198">*banner1.svg*</span></span>
  * <span data-ttu-id="e4265-199">*default.html*</span><span class="sxs-lookup"><span data-stu-id="e4265-199">*default.html*</span></span>

<span data-ttu-id="e4265-200">다음 코드는 정적 파일, 기본 파일 및 `MyStaticFiles`의 디렉터리 검색을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-200">The following code enables static files, default files, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

<span data-ttu-id="e4265-201">`EnableDirectoryBrowsing` 속성 값이 `true`인 경우 `AddDirectoryBrowser`를 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-201">`AddDirectoryBrowser` must be called when the `EnableDirectoryBrowsing` property value is `true`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

<span data-ttu-id="e4265-202">URL은 파일 계층 구조 및 이전 코드를 사용하여 다음과 같이 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-202">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="e4265-203">URI</span><span class="sxs-lookup"><span data-stu-id="e4265-203">URI</span></span>            |                             <span data-ttu-id="e4265-204">응답</span><span class="sxs-lookup"><span data-stu-id="e4265-204">Response</span></span>  |
| ------- | ------|
| <span data-ttu-id="e4265-205">*http://\<server_address>/StaticFiles/images/banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="e4265-205">*http://\<server_address>/StaticFiles/images/banner1.svg*</span></span>    |      <span data-ttu-id="e4265-206">MyStaticFiles/images/banner1.svg</span><span class="sxs-lookup"><span data-stu-id="e4265-206">MyStaticFiles/images/banner1.svg</span></span> |
| <span data-ttu-id="e4265-207">*http://\<server_address>/StaticFiles*</span><span class="sxs-lookup"><span data-stu-id="e4265-207">*http://\<server_address>/StaticFiles*</span></span>             |     <span data-ttu-id="e4265-208">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="e4265-208">MyStaticFiles/default.html</span></span> |

<span data-ttu-id="e4265-209">*MyStaticFiles* 디렉터리에 기본 이름이 지정된 파일이 없는 경우 *http://\<server_address>/StaticFiles*는 클릭 가능한 링크와 함께 디렉터리 목록을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-209">If no default-named file exists in the *MyStaticFiles* directory, *http://\<server_address>/StaticFiles* returns the directory listing with clickable links:</span></span>

![정적 파일 목록](static-files/_static/db2.png)

> [!NOTE]
> <span data-ttu-id="e4265-211"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> 및 <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*>는 `http://{SERVER ADDRESS}/StaticFiles`(후행 슬래시 없음)에서 `http://{SERVER ADDRESS}/StaticFiles/`(후행 슬래시 있음)로 클라이언트 쪽 리디렉션을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-211"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from `http://{SERVER ADDRESS}/StaticFiles` (without a trailing slash) to `http://{SERVER ADDRESS}/StaticFiles/` (with a trailing slash).</span></span> <span data-ttu-id="e4265-212">*StaticFiles* 디렉터리 내의 상대 URL은 후행 슬래시가 있어야 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-212">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash.</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="e4265-213">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="e4265-213">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="e4265-214">[FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) 클래스는 MIME 콘텐츠 형식에 대한 파일 확장명 매핑 역할을 수행하는 `Mappings` 속성을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-214">The [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) class contains a `Mappings` property serving as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="e4265-215">다음 샘플에서는 여러 파일 확장명이 알려진 MIME 형식에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-215">In the following sample, several file extensions are registered to known MIME types.</span></span> <span data-ttu-id="e4265-216">*.rtf* 확장은 대체되며 *.mp4*는 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-216">The *.rtf* extension is replaced, and *.mp4* is removed.</span></span>

[!code-csharp[](static-files/samples/1x/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

<span data-ttu-id="e4265-217">[MIME 콘텐츠 형식](http://www.iana.org/assignments/media-types/media-types.xhtml)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e4265-217">See [MIME content types](http://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="e4265-218">비표준 콘텐츠 형식</span><span class="sxs-lookup"><span data-stu-id="e4265-218">Non-standard content types</span></span>

<span data-ttu-id="e4265-219">정적 파일 미들웨어는 거의 400가지의 알려진 파일 콘텐츠 형식을 이해합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-219">Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="e4265-220">사용자가 알 수 없는 파일 형식의 파일을 요청하는 경우 정적 파일 미들웨어가 해당 요청을 파이프라인의 다음 미들웨어로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-220">If the user requests a file with an unknown file type, Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="e4265-221">요청을 처리한 미들웨어가 없으면 ‘404 찾을 수 없음’ 응답이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-221">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="e4265-222">디렉터리 찾아보기가 사용 가능한 경우 파일에 대한 링크가 디렉터리 목록에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-222">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="e4265-223">다음 코드는 알 수 없는 형식 제공을 사용하도록 설정하고 알 수 없는 파일을 이미지로 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-223">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](static-files/samples/1x/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="e4265-224">위의 코드를 사용하면 알 수 없는 콘텐츠 형식인 파일에 대한 요청은 이미지로 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-224">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="e4265-225">[ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes)를 사용하도록 설정하면 보안 위험이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-225">Enabling [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) is a security risk.</span></span> <span data-ttu-id="e4265-226">기본적으로 비활성화되어 있으며 사용은 권장되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-226">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="e4265-227">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider)는 비표준 확장명을 가진 파일을 제공하는 것보다 안전한 대체 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-227">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

### <a name="considerations"></a><span data-ttu-id="e4265-228">고려 사항</span><span class="sxs-lookup"><span data-stu-id="e4265-228">Considerations</span></span>

> [!WARNING]
> <span data-ttu-id="e4265-229">`UseDirectoryBrowser` 및 `UseStaticFiles`는 비밀 정보를 누출 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-229">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="e4265-230">프로덕션 환경에서 디렉터리 검색을 비활성화하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-230">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="e4265-231">`UseStaticFiles` 또는 `UseDirectoryBrowser`를 통해 어떤 디렉터리가 활성화되었는지 주의 깊게 검토합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-231">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="e4265-232">전체 디렉터리와 해당 하위 디렉터리는 공개적으로 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-232">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="e4265-233">*\<content_root>/wwwroot*와 같이 전용 디렉터리에 공개적으로 제공하는 데 적합한 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-233">Store files suitable for serving to the public in a dedicated directory, such as *\<content_root>/wwwroot*.</span></span> <span data-ttu-id="e4265-234">MVC 뷰, Razor 페이지(2.x에만 해당), 구성 파일 등으로 이러한 파일을 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-234">Separate these files from MVC views, Razor Pages (2.x only), configuration files, etc.</span></span>

* <span data-ttu-id="e4265-235">`UseDirectoryBrowser` 및 `UseStaticFiles`로 노출된 콘텐츠에 대한 URL은 대/소문자 구분 및 기본 파일 시스템의 문자 제한이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-235">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="e4265-236">예를 들어 Windows는 대/소문자를 구분하지 않는 반면 macOS 및 Linux는 그렇지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-236">For example, Windows is case insensitive&mdash;macOS and Linux aren't.</span></span>

* <span data-ttu-id="e4265-237">IIS에서 호스팅되는 ASP.NET Core 앱은 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 사용하여 정적 파일 요청을 비롯한 모든 요청을 앱에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-237">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="e4265-238">IIS 정적 파일 처리기는 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-238">The IIS static file handler isn't used.</span></span> <span data-ttu-id="e4265-239">모듈에서 처리하기 전에는 요청을 처리할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-239">It has no chance to handle requests before they're handled by the module.</span></span>

* <span data-ttu-id="e4265-240">서버 또는 웹 사이트 수준에서 IIS 정적 파일 처리기를 제거하려면 IIS 관리자에서 다음 단계를 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-240">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="e4265-241">**모듈** 기능으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-241">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="e4265-242">목록에서 **StaticFileModule**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-242">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="e4265-243">**동작** 사이드바에서 **제거**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-243">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="e4265-244">IIS 정적 파일 처리기가 사용되도록 설정된 경우 **및** ASP.NET Core 모듈이 올바르게 구성된 경우, 정적 파일이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-244">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="e4265-245">이는 예를 들어 *web.config* 파일이 배포되지 않는 경우에 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-245">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="e4265-246">코드 파일(*.cs* 및 *.cshtml* 포함)을 앱 프로젝트의 웹 루트 외부에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-246">Place code files (including *.cs* and *.cshtml*) outside of the app project's web root.</span></span> <span data-ttu-id="e4265-247">따라서 논리적 분리가 앱의 클라이언트 쪽 콘텐츠 및 서버 기반 코드 사이에 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-247">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="e4265-248">그러면 서버 쪽 코드가 유출되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e4265-248">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e4265-249">추가 자료</span><span class="sxs-lookup"><span data-stu-id="e4265-249">Additional resources</span></span>

* [<span data-ttu-id="e4265-250">미들웨어</span><span class="sxs-lookup"><span data-stu-id="e4265-250">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="e4265-251">ASP.NET Core 소개</span><span class="sxs-lookup"><span data-stu-id="e4265-251">Introduction to ASP.NET Core</span></span>](xref:index)
