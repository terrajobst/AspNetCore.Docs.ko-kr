---
title: Swashbuckle 및 ASP.NET Core 시작
author: zuckerthoben
description: ASP.NET Core Web API 프로젝트에 Swashbuckle을 추가하여 Swagger UI를 통합하는 방법을 알아봅니다.
ms.author: scaddie
ms.custom: mvc
ms.date: 04/04/2019
uid: tutorials/get-started-with-swashbuckle
ms.openlocfilehash: ebbdfa4cfeb3013cd961167439dd261f2e058a4b
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087609"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a><span data-ttu-id="e7368-103">Swashbuckle 및 ASP.NET Core 시작</span><span class="sxs-lookup"><span data-stu-id="e7368-103">Get started with Swashbuckle and ASP.NET Core</span></span>

<span data-ttu-id="e7368-104">작성자: [Shayne Boyer](https://twitter.com/spboyer) 및 [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="e7368-104">By [Shayne Boyer](https://twitter.com/spboyer) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="e7368-105">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e7368-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e7368-106">Swashbuckle에 대한 세 가지 주 구성 요소는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-106">There are three main components to Swashbuckle:</span></span>

* <span data-ttu-id="e7368-107">[Swashbuckle.AspNetCore.Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): `SwaggerDocument` 개체를 JSON 엔드포인트로 노출하기 위한 Swagger 개체 모델 및 미들웨어.</span><span class="sxs-lookup"><span data-stu-id="e7368-107">[Swashbuckle.AspNetCore.Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): a Swagger object model and middleware to expose `SwaggerDocument` objects as JSON endpoints.</span></span>

* <span data-ttu-id="e7368-108">[Swashbuckle.AspNetCore.SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): 경로, 컨트롤러 및 모델에서 직접 `SwaggerDocument` 개체를 빌드하는 Swagger 생성기.</span><span class="sxs-lookup"><span data-stu-id="e7368-108">[Swashbuckle.AspNetCore.SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): a Swagger generator that builds `SwaggerDocument` objects directly from your routes, controllers, and models.</span></span> <span data-ttu-id="e7368-109">일반적으로 Swagger 엔드포인트 미들웨어와 결합되어 자동으로 Swagger JSON을 노출합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-109">It's typically combined with the Swagger endpoint middleware to automatically expose Swagger JSON.</span></span>

* <span data-ttu-id="e7368-110">[Swashbuckle.AspNetCore.SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): Swagger UI 도구의 포함된 버전.</span><span class="sxs-lookup"><span data-stu-id="e7368-110">[Swashbuckle.AspNetCore.SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): an embedded version of the Swagger UI tool.</span></span> <span data-ttu-id="e7368-111">Swagger JSON을 해석하여 웹 API 기능을 설명하기 위한 다양한 사용자 지정 가능한 환경을 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-111">It interprets Swagger JSON to build a rich, customizable experience for describing the web API functionality.</span></span> <span data-ttu-id="e7368-112">여기에는 공용 메서드에 대한 기본 제공 테스트 도구가 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-112">It includes built-in test harnesses for the public methods.</span></span>

## <a name="package-installation"></a><span data-ttu-id="e7368-113">패키지 설치</span><span class="sxs-lookup"><span data-stu-id="e7368-113">Package installation</span></span>

<span data-ttu-id="e7368-114">다음 방법으로 Swashbuckle을 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-114">Swashbuckle can be added with the following approaches:</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e7368-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e7368-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e7368-116">**패키지 관리자 콘솔** 창에서:</span><span class="sxs-lookup"><span data-stu-id="e7368-116">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="e7368-117">**보기** > **다른 창** > **패키지 관리자 콘솔**로 이동</span><span class="sxs-lookup"><span data-stu-id="e7368-117">Go to **View** > **Other Windows** > **Package Manager Console**</span></span>
  * <span data-ttu-id="e7368-118">*TodoApi.csproj* 파일이 위치한 디렉터리로 이동</span><span class="sxs-lookup"><span data-stu-id="e7368-118">Navigate to the directory in which the *TodoApi.csproj* file exists</span></span>
  * <span data-ttu-id="e7368-119">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-119">Execute the following command:</span></span>

    ```powershell
    Install-Package Swashbuckle.AspNetCore
    ```

* <span data-ttu-id="e7368-120">**NuGet 패키지 관리** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="e7368-120">From the **Manage NuGet Packages** dialog:</span></span>
  * <span data-ttu-id="e7368-121">**솔루션 탐색기** > **NuGet 패키지 관리**에서 프로젝트를 마우스 오른쪽 단추로 클릭</span><span class="sxs-lookup"><span data-stu-id="e7368-121">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
  * <span data-ttu-id="e7368-122">**패키지 소스**를 “nuget.org”로 설정</span><span class="sxs-lookup"><span data-stu-id="e7368-122">Set the **Package source** to "nuget.org"</span></span>
  * <span data-ttu-id="e7368-123">검색 상자에 “Swashbuckle.AspNetCore” 입력</span><span class="sxs-lookup"><span data-stu-id="e7368-123">Enter "Swashbuckle.AspNetCore" in the search box</span></span>
  * <span data-ttu-id="e7368-124">**찾아보기** 탭에서 “Swashbuckle.AspNetCore” 패키지를 선택하고 **설치** 클릭</span><span class="sxs-lookup"><span data-stu-id="e7368-124">Select the "Swashbuckle.AspNetCore" package from the **Browse** tab and click **Install**</span></span>

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e7368-125">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="e7368-125">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e7368-126">**Solution Pad**에서 *Packages* 폴더를 마우스 오른쪽 단추로 클릭 > **패키지 추가...** 선택</span><span class="sxs-lookup"><span data-stu-id="e7368-126">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="e7368-127">**패키지 추가** 창의 **소스** 드롭다운을 “nuget.org”로 설정</span><span class="sxs-lookup"><span data-stu-id="e7368-127">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="e7368-128">검색 상자에 “Swashbuckle.AspNetCore” 입력</span><span class="sxs-lookup"><span data-stu-id="e7368-128">Enter "Swashbuckle.AspNetCore" in the search box</span></span>
* <span data-ttu-id="e7368-129">결과 창에서 "Swashbuckle.AspNetCore" 패키지를 선택하고 **패키지 추가** 클릭</span><span class="sxs-lookup"><span data-stu-id="e7368-129">Select the "Swashbuckle.AspNetCore" package from the results pane and click **Add Package**</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e7368-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e7368-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e7368-131">**통합 터미널**에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-131">Run the following command from the **Integrated Terminal**:</span></span>

```console
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore
```

### <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="e7368-132">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="e7368-132">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="e7368-133">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-133">Run the following command:</span></span>

```console
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore
```

---

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="e7368-134">Swagger 미들웨어 추가 및 구성</span><span class="sxs-lookup"><span data-stu-id="e7368-134">Add and configure Swagger middleware</span></span>

<span data-ttu-id="e7368-135">`Info` 클래스를 사용하기 위해 다음 네임스페이스를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-135">Import the following namespace to use the `Info` class:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_InfoClassNamespace)]

<span data-ttu-id="e7368-136">`Startup.ConfigureServices` 메서드의 서비스 컬렉션에 Swagger 생성기를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-136">Add the Swagger generator to the services collection in the `Startup.ConfigureServices` method:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=9-12)]

::: moniker-end

<span data-ttu-id="e7368-137">`Startup.Configure` 메서드에서 생성된 JSON 문서 및 Swagger UI를 지원하기 위해 미들웨어를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-137">In the `Startup.Configure` method, enable the middleware for serving the generated JSON document and the Swagger UI:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

<span data-ttu-id="e7368-138">위의 `UseSwaggerUI` 메서드 호출은 [정적 파일 미들웨어](xref:fundamentals/static-files)를 활성화합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-138">The preceding `UseSwaggerUI` method call enables the [Static File Middleware](xref:fundamentals/static-files).</span></span> <span data-ttu-id="e7368-139">.NET Framework 또는 NET Core 1.x를 대상으로 하는 경우 [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet 패키지를 프로젝트에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-139">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet package to the project.</span></span>

<span data-ttu-id="e7368-140">앱을 시작하고 `http://localhost:<port>/swagger/v1/swagger.json`으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-140">Launch the app, and navigate to `http://localhost:<port>/swagger/v1/swagger.json`.</span></span> <span data-ttu-id="e7368-141">엔드포인트를 설명하는 생성된 문서는 [Swagger 사양(swagger.json)](xref:tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson)에 표시된 것처럼 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-141">The generated document describing the endpoints appears as shown in [Swagger specification (swagger.json)](xref:tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).</span></span>

<span data-ttu-id="e7368-142">Swagger UI는 `http://localhost:<port>/swagger`에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-142">The Swagger UI can be found at `http://localhost:<port>/swagger`.</span></span> <span data-ttu-id="e7368-143">Swagger UI를 통해 API를 탐색하고 다른 프로그램에 통합합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-143">Explore the API via Swagger UI and incorporate it in other programs.</span></span>

> [!TIP]
> <span data-ttu-id="e7368-144">앱의 루트(`http://localhost:<port>/`)에서 Swagger UI를 제공하려면 `RoutePrefix` 속성을 빈 문자열로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-144">To serve the Swagger UI at the app's root (`http://localhost:<port>/`), set the `RoutePrefix` property to an empty string:</span></span>
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_UseSwaggerUI&highlight=4)]

<span data-ttu-id="e7368-145">IIS 또는 역방향 프록시에서 디렉터리를 사용하는 경우 `./` 접두사를 사용하여 Swagger 엔드포인트를 상대 경로로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-145">If using directories with IIS or a reverse proxy, set the Swagger endpoint to a relative path using the `./` prefix.</span></span> <span data-ttu-id="e7368-146">예를 들어, `./swagger/v1/swagger.json`을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-146">For example, `./swagger/v1/swagger.json`.</span></span> <span data-ttu-id="e7368-147">`/swagger/v1/swagger.json`을 사용하면 앱이 URL의 실제 루트(사용되는 경우 경로 접두사)에서 JSON 파일을 찾도록 지시합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-147">Using `/swagger/v1/swagger.json` instructs the app to look for the JSON file at the true root of the URL (plus the route prefix, if used).</span></span> <span data-ttu-id="e7368-148">예를 들어 `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json` 대신 `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json`을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-148">For example, use `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` instead of `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span></span>

## <a name="customize-and-extend"></a><span data-ttu-id="e7368-149">사용자 지정 및 확장</span><span class="sxs-lookup"><span data-stu-id="e7368-149">Customize and extend</span></span>

<span data-ttu-id="e7368-150">Swagger는 개체 모델을 설명하고 테마와 일치하도록 UI를 사용자 지정하는 옵션을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-150">Swagger provides options for documenting the object model and customizing the UI to match your theme.</span></span>

### <a name="api-info-and-description"></a><span data-ttu-id="e7368-151">API 정보 및 설명</span><span class="sxs-lookup"><span data-stu-id="e7368-151">API info and description</span></span>

<span data-ttu-id="e7368-152">`AddSwaggerGen` 메서드에 전달되는 구성 작업은 작성자, 라이선스 및 설명과 같은 정보를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-152">The configuration action passed to the `AddSwaggerGen` method adds information such as the author, license, and description:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup4.cs?name=snippet_AddSwaggerGen)]

<span data-ttu-id="e7368-153">Swagger UI는 버전의 정보를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-153">The Swagger UI displays the version's information:</span></span>

![버전 정보가 포함된 Swagger UI: 설명, 작성자 및 추가 링크 확인](web-api-help-pages-using-swagger/_static/custom-info.png)

### <a name="xml-comments"></a><span data-ttu-id="e7368-155">XML 주석</span><span class="sxs-lookup"><span data-stu-id="e7368-155">XML comments</span></span>

<span data-ttu-id="e7368-156">XML 주석은 다음 방법으로 사용하도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-156">XML comments can be enabled with the following approaches:</span></span>

#### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e7368-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e7368-157">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="e7368-158">**솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **<project_name>.csproj 편집**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-158">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="e7368-159">강조 표시된 줄을 *.csproj* 파일에 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-159">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="e7368-160">**솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-160">Right-click the project in **Solution Explorer** and select **Properties**.</span></span>
* <span data-ttu-id="e7368-161">**빌드** 탭의 **출력** 섹션에서 **XML 설명서 파일** 상자를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-161">Check the **XML documentation file** box under the **Output** section of the **Build** tab.</span></span>

::: moniker-end

#### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e7368-162">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="e7368-162">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="e7368-163">*Solution Pad*에서 **control** 키를 누르고 프로젝트 이름을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-163">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="e7368-164">**도구** > **파일 편집**으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-164">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="e7368-165">강조 표시된 줄을 *.csproj* 파일에 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-165">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="e7368-166">**프로젝트 옵션** 대화 상자 > **빌드** > **컴파일러**를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-166">Open the **Project Options** dialog > **Build** > **Compiler**</span></span>
* <span data-ttu-id="e7368-167">**일반 옵션** 섹션에서 **XML 문서 생성** 상자 선택</span><span class="sxs-lookup"><span data-stu-id="e7368-167">Check the **Generate xml documentation** box under the **General Options** section</span></span>

::: moniker-end

#### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e7368-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e7368-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e7368-169">강조 표시된 줄을 *.csproj* 파일에 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-169">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

#### <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="e7368-170">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="e7368-170">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="e7368-171">강조 표시된 줄을 *.csproj* 파일에 수동으로 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-171">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

---

<span data-ttu-id="e7368-172">XML 주석을 사용하면 문서화되지 않은 public 형식과 멤버에 대한 디버그 정보를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-172">Enabling XML comments provides debug information for undocumented public types and members.</span></span> <span data-ttu-id="e7368-173">문서화되지 않은 형식 및 멤버는 경고 메시지로 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-173">Undocumented types and members are indicated by the warning message.</span></span> <span data-ttu-id="e7368-174">예를 들어 다음 메시지는 경고 코드 1591 위반을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-174">For example, the following message indicates a violation of warning code 1591:</span></span>

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

<span data-ttu-id="e7368-175">프로젝트 전반에서 경고를 표시하지 않으려면 프로젝트 파일에서 무시할 경고 코드의 세미콜론으로 구분된 목록을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-175">To suppress warnings project-wide, define a semicolon-delimited list of warning codes to ignore in the project file.</span></span> <span data-ttu-id="e7368-176">`$(NoWarn);`에 경고 코드를 추가하면 [C# 기본값](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16)도 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-176">Appending the warning codes to `$(NoWarn);` applies the [C# default values](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) too.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

<span data-ttu-id="e7368-177">특정 멤버에 대해서만 경고를 표시하지 않으려면 [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) 전처리기 지시문에 코드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-177">To suppress warnings only for specific members, enclose the code in [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) preprocessor directives.</span></span> <span data-ttu-id="e7368-178">이 방법은 API 문서를 통해 노출하지 않아야 하는 코드에 유용합니다. 다음 예제에서는 전체 `Program` 클래스에 대해 경고 코드 CS1591이 무시됩니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-178">This approach is useful for code that shouldn't be exposed via the API docs. In the following example, warning code CS1591 is ignored for the entire `Program` class.</span></span> <span data-ttu-id="e7368-179">클래스 정의를 닫으면 경고 코드 적용이 복원됩니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-179">Enforcement of the warning code is restored at the close of the class definition.</span></span> <span data-ttu-id="e7368-180">여러 경고 코드는 쉼표로 구분된 목록으로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-180">Specify multiple warning codes with a comma-delimited list.</span></span>

```csharp
namespace TodoApi
{
#pragma warning disable CS1591
    public class Program
    {
        public static void Main(string[] args) =>
            BuildWebHost(args).Run();

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
#pragma warning restore CS1591
}
```

<span data-ttu-id="e7368-181">이전 지침에 따라 생성된 XML 파일을 사용하도록 Swagger를 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-181">Configure Swagger to use the XML file that's generated with the preceding instructions.</span></span> <span data-ttu-id="e7368-182">Linux 또는 Windows가 아닌 운영 체제의 경우 파일 이름 및 경로는 대/소문자를 구분할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-182">For Linux or non-Windows operating systems, file names and paths can be case-sensitive.</span></span> <span data-ttu-id="e7368-183">예를 들어 *TodoApi.XML* 파일은 Windows에는 유효하지만 CentOS에는 그렇지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-183">For example, a *TodoApi.XML* file is valid on Windows but not CentOS.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=31-33)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup1x.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

<span data-ttu-id="e7368-184">이전 코드에서 [리플렉션](/dotnet/csharp/programming-guide/concepts/reflection)은 웹 API 프로젝트의 이름과 일치하는 XML 파일 이름을 빌드하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-184">In the preceding code, [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) is used to build an XML file name matching that of the web API project.</span></span> <span data-ttu-id="e7368-185">[AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory*) 속성은 XML 파일에 대한 경로를 생성하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-185">The [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory*) property is used to construct a path to the XML file.</span></span> <span data-ttu-id="e7368-186">일부 Swagger 기능(예: 각 특성에서 입력 매개 변수 또는 HTTP 메서드와 응답 코드의 schemata)은 XML 문서 파일을 사용하지 않고 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-186">Some Swagger features (for example, schemata of input parameters or HTTP methods and response codes from the respective attributes) work without the use of an XML documentation file.</span></span> <span data-ttu-id="e7368-187">대부분 기능, 메서드 요약 및 매개 변수/응답 코드 설명의 경우 XML 파일을 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-187">For most features, namely method summaries and the descriptions of parameters and response codes, the use of an XML file is mandatory.</span></span>

<span data-ttu-id="e7368-188">작업에 3중 슬래시 주석을 추가하면 섹션 헤더에 설명이 추가되어 Swagger UI가 향상됩니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-188">Adding triple-slash comments to an action enhances the Swagger UI by adding the description to the section header.</span></span> <span data-ttu-id="e7368-189">`Delete` 작업 위에 [\<요약>](/dotnet/csharp/programming-guide/xmldoc/summary) 요소를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-189">Add a [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) element above the `Delete` action:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Delete&highlight=1-3)]

<span data-ttu-id="e7368-190">Swagger UI는 이전 코드 `<summary>` 요소의 내부 텍스트를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-190">The Swagger UI displays the inner text of the preceding code's `<summary>` element:</span></span>

![DELETE 메서드에 대한 XML 주석 ‘특정 TodoItem을 삭제합니다.’를 보여 주는](web-api-help-pages-using-swagger/_static/triple-slash-comments.png)

<span data-ttu-id="e7368-193">UI는 생성된 JSON 스키마에 의해 구동됩니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-193">The UI is driven by the generated JSON schema:</span></span>

```json
"delete": {
    "tags": [
        "Todo"
    ],
    "summary": "Deletes a specific TodoItem.",
    "operationId": "ApiTodoByIdDelete",
    "consumes": [],
    "produces": [],
    "parameters": [
        {
            "name": "id",
            "in": "path",
            "description": "",
            "required": true,
            "type": "integer",
            "format": "int64"
        }
    ],
    "responses": {
        "200": {
            "description": "Success"
        }
    }
}
```

<span data-ttu-id="e7368-194">[\<설명>](/dotnet/csharp/programming-guide/xmldoc/remarks) 요소를 `Create` 작업 메서드 문서에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-194">Add a [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) element to the `Create` action method documentation.</span></span> <span data-ttu-id="e7368-195">이는 `<summary>` 요소에 지정된 정보를 보충하고 더 강력한 Swagger UI를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-195">It supplements information specified in the `<summary>` element and provides a more robust Swagger UI.</span></span> <span data-ttu-id="e7368-196">`<remarks>` 요소 콘텐츠는 텍스트, JSON 또는 XML로 구성될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-196">The `<remarks>` element content can consist of text, JSON, or XML.</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

<span data-ttu-id="e7368-197">이러한 추가 주석이 포함된 향상된 UI 기능을 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-197">Notice the UI enhancements with these additional comments:</span></span>

![추가 주석이 표시된 Swagger UI](web-api-help-pages-using-swagger/_static/xml-comments-extended.png)

### <a name="data-annotations"></a><span data-ttu-id="e7368-199">데이터 주석</span><span class="sxs-lookup"><span data-stu-id="e7368-199">Data annotations</span></span>

<span data-ttu-id="e7368-200">[System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) 네임스페이스에 있는 특성으로 모델을 데코레이트하여 Swagger UI 구성 요소를 구동합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-200">Decorate the model with attributes, found in the [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) namespace, to help drive the Swagger UI components.</span></span>

<span data-ttu-id="e7368-201">`[Required]` 특성을 `TodoItem` 클래스의 `Name` 속성에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-201">Add the `[Required]` attribute to the `Name` property of the `TodoItem` class:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Models/TodoItem.cs?highlight=10)]

<span data-ttu-id="e7368-202">이 특성이 있으면 UI 동작이 변경되고 기본 JSON 스키마가 변경됩니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-202">The presence of this attribute changes the UI behavior and alters the underlying JSON schema:</span></span>

```json
"definitions": {
    "TodoItem": {
        "required": [
            "name"
        ],
        "type": "object",
        "properties": {
            "id": {
                "format": "int64",
                "type": "integer"
            },
            "name": {
                "type": "string"
            },
            "isComplete": {
                "default": false,
                "type": "boolean"
            }
        }
    }
},
```

<span data-ttu-id="e7368-203">API 컨트롤러에 `[Produces("application/json")]` 특성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-203">Add the `[Produces("application/json")]` attribute to the API controller.</span></span> <span data-ttu-id="e7368-204">이 특성은 컨트롤러 동작이 *application/json*의 응답 콘텐츠 형식을 지원함을 선언하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-204">Its purpose is to declare that the controller's actions support a response content type of *application/json*:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

<span data-ttu-id="e7368-205">컨트롤러 GET 작업의 경우 **응답 콘텐츠 형식** 드롭다운에서 이 콘텐츠 형식이 기본값으로 선택됩니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-205">The **Response Content Type** drop-down selects this content type as the default for the controller's GET actions:</span></span>

![기본 응답 콘텐츠 형식이 포함된 Swagger UI](web-api-help-pages-using-swagger/_static/json-response-content-type.png)

<span data-ttu-id="e7368-207">웹 API에서 데이터 주석 사용이 증가하면 UI 및 API 도움말 페이지에는 더 자세한 설명과 유용한 정보가 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-207">As the usage of data annotations in the web API increases, the UI and API help pages become more descriptive and useful.</span></span>

### <a name="describe-response-types"></a><span data-ttu-id="e7368-208">응답 형식 설명</span><span class="sxs-lookup"><span data-stu-id="e7368-208">Describe response types</span></span>

<span data-ttu-id="e7368-209">웹 API를 사용하는 개발자는 반환된 내용&mdash; 중에서도 특히 응답 형식 및 오류 코드(표준이 아닌 경우)를 가장 중요하게 생각합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-209">Developers consuming a web API are most concerned with what's returned&mdash;specifically response types and error codes (if not standard).</span></span> <span data-ttu-id="e7368-210">응답 형식 및 오류 코드는 XML 주석 및 데이터 주석에 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-210">The response types and error codes are denoted in the XML comments and data annotations.</span></span>

<span data-ttu-id="e7368-211">`Create` 작업은 성공 시 HTTP 201 상태 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-211">The `Create` action returns an HTTP 201 status code on success.</span></span> <span data-ttu-id="e7368-212">게시된 요청 본문이 null일 경우 HTTP 400 상태 코드가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-212">An HTTP 400 status code is returned when the posted request body is null.</span></span> <span data-ttu-id="e7368-213">Swagger UI에 적절한 문서가 없으면 소비자는 이러한 예상 결과에 대한 정보를 알 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-213">Without proper documentation in the Swagger UI, the consumer lacks knowledge of these expected outcomes.</span></span> <span data-ttu-id="e7368-214">다음 예제에서 강조 표시된 줄을 추가하여 해당 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-214">Fix that problem by adding the highlighted lines in the following example:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

<span data-ttu-id="e7368-215">이제 Swagger UI에서는 예상 HTTP 응답 코드를 분명히 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-215">The Swagger UI now clearly documents the expected HTTP response codes:</span></span>

![POST 응답 클래스 설명 ‘새로 만들어진 Todo 항목 반환’ 및 ‘400 - 응답 메시지에서 항목의 상태 코드 및 이유가 null인 경우’를 보여 주는 Swagger UI](web-api-help-pages-using-swagger/_static/data-annotations-response-types.png)

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="e7368-217">ASP.NET Core 2.2 이상에서는 `[ProducesResponseType]`을 사용하여 명시적으로 개별 작업을 데코레이팅하는 대신 규칙을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-217">In ASP.NET Core 2.2 or later, conventions can be used as an alternative to explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="e7368-218">자세한 내용은 <xref:web-api/advanced/conventions>을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e7368-218">For more information, see <xref:web-api/advanced/conventions>.</span></span>

::: moniker-end

### <a name="customize-the-ui"></a><span data-ttu-id="e7368-219">UI 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="e7368-219">Customize the UI</span></span>

<span data-ttu-id="e7368-220">재고 UI는 기능적이며 표시 가능합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-220">The stock UI is both functional and presentable.</span></span> <span data-ttu-id="e7368-221">그러나 API 설명서 페이지는 브랜드 또는 테마를 나타내야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-221">However, API documentation pages should represent your brand or theme.</span></span> <span data-ttu-id="e7368-222">Swashbuckle 구성 요소를 브랜딩하려면 리소스를 추가하여 정적 파일을 지원하고 폴더 구조를 빌드하여 해당 파일을 호스트해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-222">Branding the Swashbuckle components requires adding the resources to serve static files and building the folder structure to host those files.</span></span>

<span data-ttu-id="e7368-223">.NET Framework 또는 NET Core 1.x를 대상으로 하는 경우 [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) NuGet 패키지를 프로젝트에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-223">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) NuGet package to the project:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="2.0.0" />
```

<span data-ttu-id="e7368-224">이전 NuGet 패키지는 .NET Core 2.x를 대상으로 하고 [metapackage](xref:fundamentals/metapackage)를 사용하는 경우 이미 설치되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-224">The preceding NuGet package is already installed if targeting .NET Core 2.x and using the [metapackage](xref:fundamentals/metapackage).</span></span>

<span data-ttu-id="e7368-225">정적 파일 미들웨어 사용:</span><span class="sxs-lookup"><span data-stu-id="e7368-225">Enable Static File Middleware:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

<span data-ttu-id="e7368-226">[Swagger UI GitHub 리포지토리](https://github.com/swagger-api/swagger-ui/tree/master/dist)에서 *dist* 폴더의 콘텐츠를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-226">Acquire the contents of the *dist* folder from the [Swagger UI GitHub repository](https://github.com/swagger-api/swagger-ui/tree/master/dist).</span></span> <span data-ttu-id="e7368-227">이 폴더에는 Swagger UI 페이지에 필요한 자산이 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-227">This folder contains the necessary assets for the Swagger UI page.</span></span>

<span data-ttu-id="e7368-228">*wwwroot/swagger/ui* 폴더를 만들고 *dist* 폴더의 콘텐츠를 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-228">Create a *wwwroot/swagger/ui* folder, and copy into it the contents of the *dist* folder.</span></span>

<span data-ttu-id="e7368-229">다음 CSS를 사용하여 *wwwroot/swagger/ui*에 *custom.css* 파일을 만들어 페이지 헤더를 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-229">Create a *custom.css* file, in *wwwroot/swagger/ui*, with the following CSS to customize the page header:</span></span>

[!code-css[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/wwwroot/swagger/ui/custom.css)]

<span data-ttu-id="e7368-230">다른 CSS 파일 다음에 *index.html* 파일의 *custom.css*를 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-230">Reference *custom.css* in the *index.html* file, after any other CSS files:</span></span>

[!code-html[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/wwwroot/swagger/ui/index.html?name=snippet_SwaggerUiCss&highlight=3)]

<span data-ttu-id="e7368-231">`http://localhost:<port>/swagger/ui/index.html`의 *index.html* 페이지로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-231">Browse to the *index.html* page at `http://localhost:<port>/swagger/ui/index.html`.</span></span> <span data-ttu-id="e7368-232">헤더의 텍스트 상자에 `http://localhost:<port>/swagger/v1/swagger.json`을 입력하고 **탐색** 단추를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-232">Enter `http://localhost:<port>/swagger/v1/swagger.json` in the header's textbox, and click the **Explore** button.</span></span> <span data-ttu-id="e7368-233">결과 페이지가 다음과 같이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-233">The resulting page looks as follows:</span></span>

![사용자 지정 헤더 제목이 포함된 Swagger UI](web-api-help-pages-using-swagger/_static/custom-header.png)

<span data-ttu-id="e7368-235">페이지를 사용하여 훨씬 더 많은 작업을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e7368-235">There's much more you can do with the page.</span></span> <span data-ttu-id="e7368-236">[Swagger UI GitHub 리포지토리](https://github.com/swagger-api/swagger-ui)에서 UI 리소스에 대한 전체 기능을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e7368-236">See the full capabilities for the UI resources at the [Swagger UI GitHub repository](https://github.com/swagger-api/swagger-ui).</span></span>
