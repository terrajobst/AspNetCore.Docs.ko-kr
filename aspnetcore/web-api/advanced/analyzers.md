---
title: 웹 API 분석기 사용
author: pranavkm
description: Microsoft.AspNetCore.Mvc.Api.Analyzers의 웹 API 분석기에 대해 알아봅니다.
monikerRange: '>= aspnetcore-2.2'
ms.author: pranavkm
ms.custom: mvc
ms.date: 12/14/2018
uid: web-api/advanced/analyzers
ms.openlocfilehash: bcc89f856e0aeef80c46a44f76f86b4c09ac6746
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64890828"
---
# <a name="use-web-api-analyzers"></a><span data-ttu-id="e325d-103">웹 API 분석기 사용</span><span class="sxs-lookup"><span data-stu-id="e325d-103">Use web API analyzers</span></span>

<span data-ttu-id="e325d-104">ASP.NET Core 2.2 이상에는 웹 API용 분석기가 포함된 [Microsoft.AspNetCore.Mvc.Api.Analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) NuGet 패키지가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e325d-104">ASP.NET Core 2.2 and later includes the [Microsoft.AspNetCore.Mvc.Api.Analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) NuGet package containing analyzers for web APIs.</span></span> <span data-ttu-id="e325d-105">분석기는 <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>로 주석을 단 컨트롤러와 함께 작동하지만 [API 규칙](xref:web-api/advanced/conventions)으로 빌드됩니다.</span><span class="sxs-lookup"><span data-stu-id="e325d-105">The analyzers work with controllers annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>, while building on [API conventions](xref:web-api/advanced/conventions).</span></span>

## <a name="package-installation"></a><span data-ttu-id="e325d-106">패키지 설치</span><span class="sxs-lookup"><span data-stu-id="e325d-106">Package installation</span></span>

<span data-ttu-id="e325d-107">`Microsoft.AspNetCore.Mvc.Api.Analyzers`는 다음 방법 중 하나를 사용하여 추가할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e325d-107">`Microsoft.AspNetCore.Mvc.Api.Analyzers` can be added with one of the following approaches:</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e325d-108">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e325d-108">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e325d-109">**패키지 관리자 콘솔** 창에서:</span><span class="sxs-lookup"><span data-stu-id="e325d-109">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="e325d-110">**보기** > **다른 창** > **패키지 관리자 콘솔**로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="e325d-110">Go to **View** > **Other Windows** > **Package Manager Console**.</span></span>
  * <span data-ttu-id="e325d-111">*ApiConventions.csproj* 파일이 위치한 디렉터리로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="e325d-111">Navigate to the directory in which the *ApiConventions.csproj* file exists.</span></span>
  * <span data-ttu-id="e325d-112">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e325d-112">Execute the following command:</span></span>

    ```powershell
    Install-Package Microsoft.AspNetCore.Mvc.Api.Analyzers
    ```

* <span data-ttu-id="e325d-113">**NuGet 패키지 관리** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="e325d-113">From the **Manage NuGet Packages** dialog:</span></span>
  * <span data-ttu-id="e325d-114">**솔루션 탐색기** > **NuGet 패키지 관리**에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="e325d-114">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**.</span></span>
  * <span data-ttu-id="e325d-115">**패키지 소스**를 “nuget.org”로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e325d-115">Set the **Package source** to "nuget.org".</span></span>
  * <span data-ttu-id="e325d-116">검색 상자에 "Microsoft.AspNetCore.Mvc.Api.Analyzers"를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="e325d-116">Enter "Microsoft.AspNetCore.Mvc.Api.Analyzers" in the search box.</span></span>
  * <span data-ttu-id="e325d-117">**찾아보기** 탭에서 "Microsoft.AspNetCore.Mvc.Api.Analyzers" 패키지를 선택하고 **설치**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="e325d-117">Select the "Microsoft.AspNetCore.Mvc.Api.Analyzers" package from the **Browse** tab and click **Install**.</span></span>

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="e325d-118">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="e325d-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="e325d-119">**Solution Pad** > **패키지 추가...** 에서 *패키지* 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="e325d-119">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**.</span></span>
* <span data-ttu-id="e325d-120">**패키지 추가** 창의 **소스** 드롭다운을 “nuget.org”로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="e325d-120">Set the **Add Packages** window's **Source** drop-down to "nuget.org".</span></span>
* <span data-ttu-id="e325d-121">검색 상자에 "Microsoft.AspNetCore.Mvc.Api.Analyzers"를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="e325d-121">Enter "Microsoft.AspNetCore.Mvc.Api.Analyzers" in the search box.</span></span>
* <span data-ttu-id="e325d-122">결과 창에서 "Microsoft.AspNetCore.Mvc.Api.Analyzers" 패키지를 선택하고 **패키지 추가**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="e325d-122">Select the "Microsoft.AspNetCore.Mvc.Api.Analyzers" package from the results pane and click **Add Package**.</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="e325d-123">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e325d-123">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e325d-124">**통합 터미널**에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e325d-124">Run the following command from the **Integrated Terminal**:</span></span>

```console
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

### <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="e325d-125">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="e325d-125">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="e325d-126">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="e325d-126">Run the following command:</span></span>

```console
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

---

## <a name="analyzers-for-api-conventions"></a><span data-ttu-id="e325d-127">API 규칙용 분석기</span><span class="sxs-lookup"><span data-stu-id="e325d-127">Analyzers for API conventions</span></span>

<span data-ttu-id="e325d-128">OpenAPI 문서에는 작업이 반환할 수 있는 상태 코드 및 응답 형식이 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e325d-128">OpenAPI documents contain status codes and response types that an action may return.</span></span> <span data-ttu-id="e325d-129">ASP.NET Core MVC에서는 <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> 및 <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> 와 같은 특성을 사용하여 작업을 문서화합니다.</span><span class="sxs-lookup"><span data-stu-id="e325d-129">In ASP.NET Core MVC, attributes such as <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> and <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> are used to document an action.</span></span> <span data-ttu-id="e325d-130"><xref:tutorials/web-api-help-pages-using-swagger>는 API 문서화에 대해 자세히 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="e325d-130"><xref:tutorials/web-api-help-pages-using-swagger> goes into further detail on documenting your API.</span></span>

<span data-ttu-id="e325d-131">패키지의 분석기 중 하나는 <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>로 주석을 단 컨트롤러를 검사하고 응답을 완전히 문서화하지 않은 작업을 식별합니다.</span><span class="sxs-lookup"><span data-stu-id="e325d-131">One of the analyzers in the package inspects controllers annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> and identifies actions that don't entirely document their responses.</span></span> <span data-ttu-id="e325d-132">다음 예제를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e325d-132">Consider the following example:</span></span>

[!code-csharp[](conventions/sample/Controllers/ContactsController.cs?name=missing404docs&highlight=9)]

<span data-ttu-id="e325d-133">앞의 작업은 HTTP 200 성공 반환 유형을 문서화하지만 HTTP 404 실패 상태 코드는 문서화하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="e325d-133">The preceding action documents the HTTP 200 success return type but doesn't document the HTTP 404 failure status code.</span></span> <span data-ttu-id="e325d-134">분석기는 HTTP 404 상태 코드에 대한 누락된 설명서를 경고로 보고합니다.</span><span class="sxs-lookup"><span data-stu-id="e325d-134">The analyzer reports the missing documentation for the HTTP 404 status code as a warning.</span></span> <span data-ttu-id="e325d-135">문제를 해결하기 위한 옵션이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="e325d-135">An option to fix the problem is provided.</span></span>

![경고를 보고하는 분석기](conventions/_static/Analyzer.gif)

## <a name="additional-resources"></a><span data-ttu-id="e325d-137">추가 자료</span><span class="sxs-lookup"><span data-stu-id="e325d-137">Additional resources</span></span>

* <xref:web-api/advanced/conventions>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:web-api/index>
