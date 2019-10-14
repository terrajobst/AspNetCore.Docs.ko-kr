---
title: ASP.NET Core의 정적 파일
author: rick-anderson
description: ASP.NET Core 웹앱에서 정적 파일을 제공 및 보호하고 정적 파일 호스팅 미들웨어 동작을 구성하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
uid: fundamentals/static-files
ms.openlocfilehash: 2f153551a86860616469200862723528e4a8cc1c
ms.sourcegitcommit: 3d082bd46e9e00a3297ea0314582b1ed2abfa830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2019
ms.locfileid: "72007321"
---
# <a name="static-files-in-aspnet-core"></a><span data-ttu-id="80cab-103">ASP.NET Core의 정적 파일</span><span class="sxs-lookup"><span data-stu-id="80cab-103">Static files in ASP.NET Core</span></span>

<span data-ttu-id="80cab-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="80cab-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="80cab-105">HTML, CSS, 이미지 및 JavaScript와 같은 정적 파일은 ASP.NET Core 앱이 클라이언트에 직접 제공하는 자산입니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-105">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients.</span></span> <span data-ttu-id="80cab-106">일부 구성은 이러한 파일을 제공하는 데 필수적입니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-106">Some configuration is required to enable serving of these files.</span></span>

<span data-ttu-id="80cab-107">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="80cab-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="80cab-108">정적 파일 제공</span><span class="sxs-lookup"><span data-stu-id="80cab-108">Serve static files</span></span>

<span data-ttu-id="80cab-109">정적 파일은 프로젝트의 [웹 루트](xref:fundamentals/index#web-root) 디렉터리 내에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-109">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="80cab-110">기본 디렉터리는 *{content root}/wwwroot*이지만, [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) 메서드를 통해 변경할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-110">The default directory is *{content root}/wwwroot*, but it can be changed via the [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) method.</span></span> <span data-ttu-id="80cab-111">자세한 정보는 [콘텐츠 루트](xref:fundamentals/index#content-root) 및 [웹 루트](xref:fundamentals/index#web-root)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="80cab-111">See [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root) for more information.</span></span>

<span data-ttu-id="80cab-112">앱의 웹 호스트에서 콘텐츠 루트 디렉터리를 인식하도록 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-112">The app's web host must be made aware of the content root directory.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="80cab-113">`WebHost.CreateDefaultBuilder` 메서드는 콘텐츠 루트를 현재 디렉터리로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-113">The `WebHost.CreateDefaultBuilder` method sets the content root to the current directory:</span></span>

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="80cab-114">`Program.Main` 내부에서 [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseContentRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_)를 호출하여 콘텐츠 루트를 현재 디렉터리로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-114">Set the content root to the current directory by invoking [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseContentRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) inside of `Program.Main`:</span></span>

[!code-csharp[](static-files/samples/1x/Program.cs?name=snippet_ProgramClass&highlight=7)]

::: moniker-end

<span data-ttu-id="80cab-115">정적 파일은 [웹 루트](xref:fundamentals/index#web-root)에 상대적인 경로를 통해 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-115">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="80cab-116">예를 들어 **웹 응용 프로그램** 프로젝트 템플릿에는 *wwwroot* 폴더 내에 여러 폴더가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-116">For example, the **Web Application** project template contains several folders within the *wwwroot* folder:</span></span>

* <span data-ttu-id="80cab-117">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="80cab-117">**wwwroot**</span></span>
  * <span data-ttu-id="80cab-118">**css**</span><span class="sxs-lookup"><span data-stu-id="80cab-118">**css**</span></span>
  * <span data-ttu-id="80cab-119">**images**</span><span class="sxs-lookup"><span data-stu-id="80cab-119">**images**</span></span>
  * <span data-ttu-id="80cab-120">**js**</span><span class="sxs-lookup"><span data-stu-id="80cab-120">**js**</span></span>

<span data-ttu-id="80cab-121">*images* 하위 폴더에 있는 파일에 액세스하기 위한 URI 형식은 *http://\<server_address>/images/\<image_file_name>* 입니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-121">The URI format to access a file in the *images* subfolder is *http://\<server_address>/images/\<image_file_name>*.</span></span> <span data-ttu-id="80cab-122">예를 들어 *http://localhost:9189/images/banner3.svg* 와 같습니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-122">For example, *http://localhost:9189/images/banner3.svg*.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="80cab-123">.NET Framework를 대상으로 하는 경우 [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) 패키지를 프로젝트에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-123">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="80cab-124">.NET Core를 대상으로 하는 경우 [Microsoft.AspNetCore.App 메타패키지](xref:fundamentals/metapackage-app)에 이 패키지가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-124">If targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) includes this package.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="80cab-125">.NET Framework를 대상으로 하는 경우 [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) 패키지를 프로젝트에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-125">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="80cab-126">.NET Core를 대상으로 하는 경우 [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage)가 이 패키지를 포함하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-126">If targeting .NET Core, the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) includes this package.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="80cab-127">[Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) 패키지를 프로젝트에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-127">Add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span>

::: moniker-end

<span data-ttu-id="80cab-128">정적 파일을 제공할 수 있도록 [미들웨어](xref:fundamentals/middleware/index)를 구성하세요.</span><span class="sxs-lookup"><span data-stu-id="80cab-128">Configure the [middleware](xref:fundamentals/middleware/index) which enables the serving of static files.</span></span>

### <a name="serve-files-inside-of-web-root"></a><span data-ttu-id="80cab-129">웹 루트 내부의 파일 제공</span><span class="sxs-lookup"><span data-stu-id="80cab-129">Serve files inside of web root</span></span>

<span data-ttu-id="80cab-130">`Startup.Configure` 내부에서 [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-130">Invoke the [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) method within `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

<span data-ttu-id="80cab-131">매개 변수가 없는 `UseStaticFiles` 메서드 오버로드는 [웹 루트](xref:fundamentals/index#web-root)에 있는 파일을 제공 가능으로 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-131">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="80cab-132">다음 표시는 *wwwroot/images/banner1.svg*를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-132">The following markup references *wwwroot/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

<span data-ttu-id="80cab-133">위의 코드에서 물결표 문자 `~/`는 [웹 루트](xref:fundamentals/index#web-root)를 가리킵니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-133">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="80cab-134">웹 루트 외부의 파일 제공</span><span class="sxs-lookup"><span data-stu-id="80cab-134">Serve files outside of web root</span></span>

<span data-ttu-id="80cab-135">제공할 정적 파일이 [웹 루트](xref:fundamentals/index#web-root) 외부에 있는 디렉터리 계층 구조를 고려해보세요.</span><span class="sxs-lookup"><span data-stu-id="80cab-135">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* <span data-ttu-id="80cab-136">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="80cab-136">**wwwroot**</span></span>
  * <span data-ttu-id="80cab-137">**css**</span><span class="sxs-lookup"><span data-stu-id="80cab-137">**css**</span></span>
  * <span data-ttu-id="80cab-138">**images**</span><span class="sxs-lookup"><span data-stu-id="80cab-138">**images**</span></span>
  * <span data-ttu-id="80cab-139">**js**</span><span class="sxs-lookup"><span data-stu-id="80cab-139">**js**</span></span>
* <span data-ttu-id="80cab-140">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="80cab-140">**MyStaticFiles**</span></span>
  * <span data-ttu-id="80cab-141">**images**</span><span class="sxs-lookup"><span data-stu-id="80cab-141">**images**</span></span>
    * <span data-ttu-id="80cab-142">*banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="80cab-142">*banner1.svg*</span></span>

<span data-ttu-id="80cab-143">요청은 정적 파일 미들웨어를 다음과 같이 구성하여 *banner1.svg* 파일에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-143">A request can access the *banner1.svg* file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](static-files/samples/1x/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

<span data-ttu-id="80cab-144">위의 코드에서 *MyStaticFiles* 디렉터리 계층 구조는 *StaticFiles* URI 세그먼트를 통해 공개적으로 노출됩니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-144">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="80cab-145">*http://\<server_address>/StaticFiles/images/banner1.svg*에 대한 요청은 *banner1.svg* 파일을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-145">A request to *http://\<server_address>/StaticFiles/images/banner1.svg* serves the *banner1.svg* file.</span></span>

<span data-ttu-id="80cab-146">다음 태그는 *MyStaticFiles/images/banner1.svg*를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-146">The following markup references *MyStaticFiles/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a><span data-ttu-id="80cab-147">HTTP 응답 헤더 설정</span><span class="sxs-lookup"><span data-stu-id="80cab-147">Set HTTP response headers</span></span>

<span data-ttu-id="80cab-148">[StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions) 개체를 사용하여 HTTP 응답 헤더를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-148">A [StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions) object can be used to set HTTP response headers.</span></span> <span data-ttu-id="80cab-149">다음 코드는 [웹 루트](xref:fundamentals/index#web-root)에서 제공되는 정적 파일을 구성하는 것 외에도 `Cache-Control` 헤더를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-149">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](static-files/samples/1x/StartupAddHeader.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="80cab-150">[HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) 메서드는 [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) 패키지에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-150">The [HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) method exists in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

<span data-ttu-id="80cab-151">개발 환경에서 파일은 10분(600초) 동안 공개적으로 캐시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-151">The files have been made publicly cacheable for 10 minutes (600 seconds) in the Development environment:</span></span>

![추가된 캐시 제어 헤더를 보여 주는 응답 헤더](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="80cab-153">정적 파일 권한 부여</span><span class="sxs-lookup"><span data-stu-id="80cab-153">Static file authorization</span></span>

<span data-ttu-id="80cab-154">정적 파일 미들웨어는 권한 부여 검사를 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-154">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="80cab-155">*wwwroot* 아래의 항목을 비롯한 제공되는 모든 파일은 공개적으로 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-155">Any files served by it, including those under *wwwroot*, are publicly accessible.</span></span> <span data-ttu-id="80cab-156">권한 부여를 기반으로 파일을 제공하려면 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-156">To serve files based on authorization:</span></span>

* <span data-ttu-id="80cab-157">파일을 *wwwroot* 외부의 정적 파일 미들웨어에 액세스할 수 있는 임의의 디렉터리에 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-157">Store them outside of *wwwroot* and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="80cab-158">권한 부여가 적용되는 작업 메서드를 통해서 파일을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-158">Serve them via an action method to which authorization is applied.</span></span> <span data-ttu-id="80cab-159">[FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult) 개체를 반환하세요.</span><span class="sxs-lookup"><span data-stu-id="80cab-159">Return a [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult) object:</span></span>

  [!code-csharp[](static-files/samples/1x/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a><span data-ttu-id="80cab-160">디렉터리 검색 사용</span><span class="sxs-lookup"><span data-stu-id="80cab-160">Enable directory browsing</span></span>

<span data-ttu-id="80cab-161">디렉터리 검색을 사용하면 웹앱 사용자가 지정된 디렉터리 내의 디렉터리 목록 및 파일을 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-161">Directory browsing allows users of your web app to see a directory listing and files within a specified directory.</span></span> <span data-ttu-id="80cab-162">기본적으로 디렉터리 검색은 보안상의 이유로 비활성화되어 있습니다([고려할 사항](#considerations) 참조).</span><span class="sxs-lookup"><span data-stu-id="80cab-162">Directory browsing is disabled by default for security reasons (see [Considerations](#considerations)).</span></span> <span data-ttu-id="80cab-163">`Startup.Configure`에서 [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) 메서드를 호출하여 디렉터리 검색을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-163">Enable directory browsing by invoking the [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) method in `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

<span data-ttu-id="80cab-164">`Startup.ConfigureServices`에서 [AddDirectoryBrowser](/dotnet/api/microsoft.extensions.dependencyinjection.directorybrowserserviceextensions.adddirectorybrowser#Microsoft_Extensions_DependencyInjection_DirectoryBrowserServiceExtensions_AddDirectoryBrowser_Microsoft_Extensions_DependencyInjection_IServiceCollection_) 메서드를 호출하여 필요한 서비스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-164">Add required services by invoking the [AddDirectoryBrowser](/dotnet/api/microsoft.extensions.dependencyinjection.directorybrowserserviceextensions.adddirectorybrowser#Microsoft_Extensions_DependencyInjection_DirectoryBrowserServiceExtensions_AddDirectoryBrowser_Microsoft_Extensions_DependencyInjection_IServiceCollection_) method from `Startup.ConfigureServices`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

<span data-ttu-id="80cab-165">위의 코드를 통해 URL *http://\<server_address>/MyImages*를 사용하여 각 파일 및 폴더에 대한 링크가 제공되는 *wwwroot/images* 폴더의 디렉터리 검색을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-165">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*, with links to each file and folder:</span></span>

![디렉터리 검색](static-files/_static/dir-browse.png)

<span data-ttu-id="80cab-167">검색을 활성화하는 경우의 보안 위험에 대한 [고려할 사항](#considerations)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="80cab-167">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span>

<span data-ttu-id="80cab-168">다음 예제에서 두 `UseStaticFiles` 호출을 참고하세요.</span><span class="sxs-lookup"><span data-stu-id="80cab-168">Note the two `UseStaticFiles` calls in the following example.</span></span> <span data-ttu-id="80cab-169">첫 번째 호출은 *wwwroot* 폴더에서 정적 파일 제공을 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-169">The first call enables the serving of static files in the *wwwroot* folder.</span></span> <span data-ttu-id="80cab-170">두 번째 호출은 URL *http://\<server_address>/MyImages*를 사용하여 *wwwroot/images* 폴더의 디렉터리 검색을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-170">The second call enables directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a><span data-ttu-id="80cab-171">기본 문서 제공</span><span class="sxs-lookup"><span data-stu-id="80cab-171">Serve a default document</span></span>

<span data-ttu-id="80cab-172">기본 홈페이지를 설정하면 방문자가 사이트를 방문할 때 논리적 시작점을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-172">Setting a default home page provides visitors a logical starting point when visiting your site.</span></span> <span data-ttu-id="80cab-173">사용자가 URI를 정규화하지 않더라도 기본 페이지를 제공하려면 `Startup.Configure`에서 [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-173">To serve a default page without the user fully qualifying the URI, call the [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) method from `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> <span data-ttu-id="80cab-174">기본 파일을 제공하려면 `UseStaticFiles` 전에 `UseDefaultFiles`를 먼저 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-174">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="80cab-175">`UseDefaultFiles`는 실제로 파일을 제공하지 않는 URL 재작성기입니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-175">`UseDefaultFiles` is a URL rewriter that doesn't actually serve the file.</span></span> <span data-ttu-id="80cab-176">파일을 제공하도록 `UseStaticFiles`를 통해 정적 파일 미들웨어를 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-176">Enable Static File Middleware via `UseStaticFiles` to serve the file.</span></span>

<span data-ttu-id="80cab-177">`UseDefaultFiles`를 사용하면 폴더에 대한 요청이 다음 파일들을 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-177">With `UseDefaultFiles`, requests to a folder search for:</span></span>

* <span data-ttu-id="80cab-178">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="80cab-178">*default.htm*</span></span>
* <span data-ttu-id="80cab-179">*default.html*</span><span class="sxs-lookup"><span data-stu-id="80cab-179">*default.html*</span></span>
* <span data-ttu-id="80cab-180">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="80cab-180">*index.htm*</span></span>
* <span data-ttu-id="80cab-181">*index.html*</span><span class="sxs-lookup"><span data-stu-id="80cab-181">*index.html*</span></span>

<span data-ttu-id="80cab-182">목록에서 찾은 첫 번째 파일이 마치 요청이 정규화된 URI인 것처럼 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-182">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="80cab-183">브라우저 URL은 요청된 URI를 계속 반영합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-183">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="80cab-184">다음 코드는 기본 파일 이름을 *mydefault.html*로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-184">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](static-files/samples/1x/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a><span data-ttu-id="80cab-185">UseFileServer</span><span class="sxs-lookup"><span data-stu-id="80cab-185">UseFileServer</span></span>

<span data-ttu-id="80cab-186">[UseFileServer](/dotnet/api/microsoft.aspnetcore.builder.fileserverextensions.usefileserver#Microsoft_AspNetCore_Builder_FileServerExtensions_UseFileServer_Microsoft_AspNetCore_Builder_IApplicationBuilder_)는 `UseStaticFiles`, `UseDefaultFiles` 및 `UseDirectoryBrowser`의 기능을 병행합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-186">[UseFileServer](/dotnet/api/microsoft.aspnetcore.builder.fileserverextensions.usefileserver#Microsoft_AspNetCore_Builder_FileServerExtensions_UseFileServer_Microsoft_AspNetCore_Builder_IApplicationBuilder_) combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="80cab-187">다음 코드는 정적 파일 및 기본 파일 제공을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-187">The following code enables the serving of static files and the default file.</span></span> <span data-ttu-id="80cab-188">디렉터리 검색은 활성화되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-188">Directory browsing isn't enabled.</span></span>

```csharp
app.UseFileServer();
```

<span data-ttu-id="80cab-189">다음 코드는 매개 변수가 없는 오버로드에 추가적으로 디렉터리 검색을 활성화하여 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-189">The following code builds upon the parameterless overload by enabling directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="80cab-190">다음 디렉터리 계층 구조를 고려해보세요.</span><span class="sxs-lookup"><span data-stu-id="80cab-190">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="80cab-191">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="80cab-191">**wwwroot**</span></span>
  * <span data-ttu-id="80cab-192">**css**</span><span class="sxs-lookup"><span data-stu-id="80cab-192">**css**</span></span>
  * <span data-ttu-id="80cab-193">**images**</span><span class="sxs-lookup"><span data-stu-id="80cab-193">**images**</span></span>
  * <span data-ttu-id="80cab-194">**js**</span><span class="sxs-lookup"><span data-stu-id="80cab-194">**js**</span></span>
* <span data-ttu-id="80cab-195">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="80cab-195">**MyStaticFiles**</span></span>
  * <span data-ttu-id="80cab-196">**images**</span><span class="sxs-lookup"><span data-stu-id="80cab-196">**images**</span></span>
    * <span data-ttu-id="80cab-197">*banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="80cab-197">*banner1.svg*</span></span>
  * <span data-ttu-id="80cab-198">*default.html*</span><span class="sxs-lookup"><span data-stu-id="80cab-198">*default.html*</span></span>

<span data-ttu-id="80cab-199">다음 코드는 `MyStaticFiles`의 정적 파일, 기본 파일 및 디렉터리 검색을 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-199">The following code enables static files, default files, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

<span data-ttu-id="80cab-200">`EnableDirectoryBrowsing` 속성 값이 `true`인 경우 `AddDirectoryBrowser`를 호출해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-200">`AddDirectoryBrowser` must be called when the `EnableDirectoryBrowsing` property value is `true`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

<span data-ttu-id="80cab-201">URL은 파일 계층 구조 및 이전 코드를 사용하여 다음과 같이 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-201">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="80cab-202">URI</span><span class="sxs-lookup"><span data-stu-id="80cab-202">URI</span></span>            |                             <span data-ttu-id="80cab-203">응답</span><span class="sxs-lookup"><span data-stu-id="80cab-203">Response</span></span>  |
| ------- | ------|
| <span data-ttu-id="80cab-204">*http://\<server_address>/StaticFiles/images/banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="80cab-204">*http://\<server_address>/StaticFiles/images/banner1.svg*</span></span>    |      <span data-ttu-id="80cab-205">MyStaticFiles/images/banner1.svg</span><span class="sxs-lookup"><span data-stu-id="80cab-205">MyStaticFiles/images/banner1.svg</span></span> |
| <span data-ttu-id="80cab-206">*http://\<server_address>/StaticFiles*</span><span class="sxs-lookup"><span data-stu-id="80cab-206">*http://\<server_address>/StaticFiles*</span></span>             |     <span data-ttu-id="80cab-207">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="80cab-207">MyStaticFiles/default.html</span></span> |

<span data-ttu-id="80cab-208">*MyStaticFiles* 디렉터리에 기본 이름이 지정된 파일이 없는 경우 *http://\<server_address>/StaticFiles*는 클릭 가능한 링크와 함께 디렉터리 목록을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-208">If no default-named file exists in the *MyStaticFiles* directory, *http://\<server_address>/StaticFiles* returns the directory listing with clickable links:</span></span>

![정적 파일 목록](static-files/_static/db2.png)

> [!NOTE]
> <span data-ttu-id="80cab-210"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> 및 <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*>는 `http://{SERVER ADDRESS}/StaticFiles`(후행 슬래시 없음)에서 `http://{SERVER ADDRESS}/StaticFiles/`(후행 슬래시 있음)로 클라이언트 쪽 리디렉션을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-210"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from `http://{SERVER ADDRESS}/StaticFiles` (without a trailing slash) to `http://{SERVER ADDRESS}/StaticFiles/` (with a trailing slash).</span></span> <span data-ttu-id="80cab-211">*StaticFiles* 디렉터리 내의 상대 URL은 후행 슬래시가 있어야 유효합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-211">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash.</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="80cab-212">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="80cab-212">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="80cab-213">[FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) 클래스는 MIME 콘텐츠 형식에 대한 파일 확장명 매핑 역할을 수행하는 `Mappings` 속성을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-213">The [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) class contains a `Mappings` property serving as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="80cab-214">다음 샘플에서는 여러 파일 확장명이 알려진 MIME 형식에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-214">In the following sample, several file extensions are registered to known MIME types.</span></span> <span data-ttu-id="80cab-215">*.rtf* 확장은 대체되며 *.mp4*는 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-215">The *.rtf* extension is replaced, and *.mp4* is removed.</span></span>

[!code-csharp[](static-files/samples/1x/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

<span data-ttu-id="80cab-216">[MIME 콘텐츠 형식](https://www.iana.org/assignments/media-types/media-types.xhtml)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="80cab-216">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="80cab-217">비표준 콘텐츠 형식</span><span class="sxs-lookup"><span data-stu-id="80cab-217">Non-standard content types</span></span>

<span data-ttu-id="80cab-218">정적 파일 미들웨어는 거의 400가지의 알려진 파일 콘텐츠 형식을 이해합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-218">Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="80cab-219">사용자가 알 수 없는 파일 형식의 파일을 요청하는 경우 정적 파일 미들웨어가 해당 요청을 파이프라인의 다음 미들웨어로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-219">If the user requests a file with an unknown file type, Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="80cab-220">요청을 처리한 미들웨어가 없으면 ‘404 찾을 수 없음’ 응답이 반환됩니다. </span><span class="sxs-lookup"><span data-stu-id="80cab-220">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="80cab-221">디렉터리 검색이 사용 가능한 경우 파일에 대한 링크가 디렉터리 목록에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-221">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="80cab-222">다음 코드는 알 수 없는 형식 제공을 사용하도록 설정하고 알 수 없는 파일을 이미지로 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-222">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](static-files/samples/1x/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="80cab-223">위의 코드를 사용하면 알 수 없는 콘텐츠 형식의 파일에 대한 요청은 이미지로 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-223">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="80cab-224">[ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes)를 사용하도록 설정하면 보안 위험이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-224">Enabling [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) is a security risk.</span></span> <span data-ttu-id="80cab-225">기본적으로 비활성화되어 있으며 사용은 권장되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-225">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="80cab-226">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider)는 비표준 확장명을 가진 파일을 제공하는 것보다 안전한 대체 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-226">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

### <a name="considerations"></a><span data-ttu-id="80cab-227">고려 사항</span><span class="sxs-lookup"><span data-stu-id="80cab-227">Considerations</span></span>

> [!WARNING]
> <span data-ttu-id="80cab-228">`UseDirectoryBrowser` 및 `UseStaticFiles`는 비밀 정보를 누출할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-228">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="80cab-229">프로덕션 환경에서는 디렉터리 검색을 비활성화하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-229">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="80cab-230">`UseStaticFiles` 또는 `UseDirectoryBrowser`를 통해 어떤 디렉터리가 활성화되었는지 주의 깊게 검토하세요.</span><span class="sxs-lookup"><span data-stu-id="80cab-230">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="80cab-231">전체 디렉터리와 해당 하위 디렉터리는 공개적으로 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-231">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="80cab-232">*\<content_root>/wwwroot*와 같은 전용 디렉터리에 공개적으로 제공하는 데 적합한 파일을 저장하세요.</span><span class="sxs-lookup"><span data-stu-id="80cab-232">Store files suitable for serving to the public in a dedicated directory, such as *\<content_root>/wwwroot*.</span></span> <span data-ttu-id="80cab-233">MVC 보기, Razor 페이지(2.x에만 해당), 구성 파일 등과 이러한 파일을 분리하세요.</span><span class="sxs-lookup"><span data-stu-id="80cab-233">Separate these files from MVC views, Razor Pages (2.x only), configuration files, etc.</span></span>

* <span data-ttu-id="80cab-234">`UseDirectoryBrowser` 및 `UseStaticFiles`로 노출된 콘텐츠에 대한 URL은 기본 파일 시스템의 대/소문자 구분 및 문자 제한이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-234">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="80cab-235">예를 들어 Windows는 대/소문자를 구분하지 않는 반면 macOS 및 Linux는 그렇지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-235">For example, Windows is case insensitive&mdash;macOS and Linux aren't.</span></span>

* <span data-ttu-id="80cab-236">IIS에서 호스팅되는 ASP.NET Core 앱은 [ASP.NET Core 모듈](xref:host-and-deploy/aspnet-core-module)을 사용하여 정적 파일 요청을 비롯한 모든 요청을 앱에 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-236">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="80cab-237">IIS 정적 파일 처리기는 사용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-237">The IIS static file handler isn't used.</span></span> <span data-ttu-id="80cab-238">모듈에서 처리하기 전에는 요청을 처리할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-238">It has no chance to handle requests before they're handled by the module.</span></span>

* <span data-ttu-id="80cab-239">서버 또는 웹 사이트 수준에서 IIS 정적 파일 처리기를 제거하려면 IIS 관리자에서 다음 단계를 완료합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-239">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="80cab-240">**모듈** 기능으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-240">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="80cab-241">목록에서 **StaticFileModule**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-241">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="80cab-242">**동작** 사이드바에서 **제거**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-242">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="80cab-243">IIS 정적 파일 처리기를 사용하도록 설정되었으며 **그리고** ASP.NET Core 모듈이 올바르게 구성되지 않은 경우, 정적 파일이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-243">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="80cab-244">예를 들어 *web.config* 파일이 배포되지 않았을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-244">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="80cab-245">코드 파일( *.cs* 및 *.cshtml* 포함)을 앱 프로젝트의 [웹 루트](xref:fundamentals/index#web-root) 외부에 배치합니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-245">Place code files (including *.cs* and *.cshtml*) outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="80cab-246">따라서 논리적 분리가 앱의 클라이언트 쪽 콘텐츠 및 서버 기반 코드 사이에 만들어집니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-246">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="80cab-247">그러면 서버 쪽 코드가 유출되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="80cab-247">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="80cab-248">추가 자료</span><span class="sxs-lookup"><span data-stu-id="80cab-248">Additional resources</span></span>

* [<span data-ttu-id="80cab-249">미들웨어</span><span class="sxs-lookup"><span data-stu-id="80cab-249">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="80cab-250">ASP.NET Core 소개</span><span class="sxs-lookup"><span data-stu-id="80cab-250">Introduction to ASP.NET Core</span></span>](xref:index)
