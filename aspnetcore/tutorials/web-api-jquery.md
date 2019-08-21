---
title: '자습서: ASP.NET Core를 사용하여, jQuery로 웹 API 호출하기'
author: rick-anderson
description: jQuery를 사용하여 ASP.NET Core 웹 API를 호출하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 07/20/2019
uid: tutorials/web-api-jquery
ms.openlocfilehash: a319e4b4ce09e9b09afeaff065d5740276deb115
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022553"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-jquery"></a><span data-ttu-id="e4df4-103">자습서: jQuery로 ASP.NET Core 웹 API 호출하기</span><span class="sxs-lookup"><span data-stu-id="e4df4-103">Tutorial: Call an ASP.NET Core web API with jQuery</span></span>

<span data-ttu-id="e4df4-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e4df4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e4df4-105">이 자습서에서는 jQuery를 사용하여 ASP.NET Core 웹 API를 호출하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="e4df4-105">This tutorial shows how to call an ASP.NET Core web API with jQuery</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e4df4-106">ASP.NET Core 2.2의 경우에는 [jQuery로 웹 API 호출하기](xref:tutorials/first-web-api#call-the-api-with-jquery) 버전 2.2를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="e4df4-106">For ASP.NET Core 2.2, see the 2.2 version of [Call the Web API with jQuery](xref:tutorials/first-web-api#call-the-api-with-jquery).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="e4df4-107">전제 조건</span><span class="sxs-lookup"><span data-stu-id="e4df4-107">Prerequisites</span></span>

* <span data-ttu-id="e4df4-108">[자습서: 웹 API 만들기](xref:tutorials/first-web-api) 완료</span><span class="sxs-lookup"><span data-stu-id="e4df4-108">Complete [Tutorial: Create a web API](xref:tutorials/first-web-api)</span></span>
* <span data-ttu-id="e4df4-109">CSS, HTML, JavaScript 및 jQuery에 대한 지식</span><span class="sxs-lookup"><span data-stu-id="e4df4-109">Familiarity with CSS, HTML, JavaScript, and jQuery</span></span>

## <a name="call-the-api-with-jquery"></a><span data-ttu-id="e4df4-110">jQuery를 사용하여 API 호출</span><span class="sxs-lookup"><span data-stu-id="e4df4-110">Call the API with jQuery</span></span>

<span data-ttu-id="e4df4-111">이 섹션에서는 jQuery를 사용하여 웹 API를 호출하는 HTML 페이지가 추가되었습니다.</span><span class="sxs-lookup"><span data-stu-id="e4df4-111">In this section, an HTML page is added that uses jQuery to call the web api.</span></span> <span data-ttu-id="e4df4-112">jQuery는 요청을 시작하고 API 응답의 세부 정보로 페이지를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="e4df4-112">jQuery initiates the request and updates the page with the details from the API's response.</span></span>

<span data-ttu-id="e4df4-113">다음 강조 표시된 코드로 *Startup.cs*를 업데이트하여 앱이 [정적 파일을 제공](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)하고 [기본 파일 매핑을 사용](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)하도록 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="e4df4-113">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJquery.cs?highlight=8-9&name=snippet_configure)]

<span data-ttu-id="e4df4-114">프로젝트 디렉터리에서 *wwwroot* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="e4df4-114">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="e4df4-115">*index.html*이라는 HTML 파일을 *wwwroot* 디렉터리에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e4df4-115">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="e4df4-116">다음 표시로 콘텐츠를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="e4df4-116">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

<span data-ttu-id="e4df4-117">*site.js*라는 JavaScript 파일을 *wwwroot* 디렉터리에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="e4df4-117">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="e4df4-118">다음 코드로 콘텐츠를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="e4df4-118">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="e4df4-119">HTML 페이지를 로컬에서 테스트하려면 ASP.NET Core 프로젝트의 시작 설정을 변경해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e4df4-119">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="e4df4-120">*Properties\launchSettings.json*을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="e4df4-120">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="e4df4-121">`launchUrl` 속성을 제거하여 앱이 *index.html*&mdash; 프로젝트의 기본 파일에서 열리도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="e4df4-121">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="e4df4-122">여러 가지 방법으로 jQuery를 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="e4df4-122">There are several ways to get jQuery.</span></span> <span data-ttu-id="e4df4-123">위의 코드 조각에서 라이브러리는 CDN에서 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="e4df4-123">In the preceding snippet, the library is loaded from a CDN.</span></span>

<span data-ttu-id="e4df4-124">이 샘플은 API의 모든 CRUD 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="e4df4-124">This sample calls all of the CRUD methods of the API.</span></span> <span data-ttu-id="e4df4-125">API 호출에 대한 설명은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="e4df4-125">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="e4df4-126">할 일 항목의 목록 가져오기</span><span class="sxs-lookup"><span data-stu-id="e4df4-126">Get a list of to-do items</span></span>

<span data-ttu-id="e4df4-127">jQuery [ajax](https://api.jquery.com/jquery.ajax/) 함수는 할 일 항목의 배열을 나타내는 JSON을 반환하는 API에 `GET` 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="e4df4-127">The jQuery [ajax](https://api.jquery.com/jquery.ajax/) function sends a `GET` request to the API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="e4df4-128">요청이 성공하면 `success` 콜백 함수가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="e4df4-128">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="e4df4-129">콜백에서 DOM은 할 일 정보로 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="e4df4-129">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="e4df4-130">할 일 항목 추가</span><span class="sxs-lookup"><span data-stu-id="e4df4-130">Add a to-do item</span></span>

<span data-ttu-id="e4df4-131">[ajax](https://api.jquery.com/jquery.ajax/) 함수는 `POST` 요청 본문에서 할 일 항목을 사용하여 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="e4df4-131">The [ajax](https://api.jquery.com/jquery.ajax/) function sends a `POST` request with the to-do item in the request body.</span></span> <span data-ttu-id="e4df4-132">`accepts` 및 `contentType` 옵션은 수신 및 전송되는 미디어 형식을 지정하기 위해 `application/json`으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="e4df4-132">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="e4df4-133">[JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)를 사용하여 할 일 항목을 JSON으로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="e4df4-133">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="e4df4-134">API가 성공적인 상태 코드를 반환하면 `getData` 함수가 호출되어 HTML 테이블을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="e4df4-134">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="e4df4-135">할 일 항목 업데이트</span><span class="sxs-lookup"><span data-stu-id="e4df4-135">Update a to-do item</span></span>

<span data-ttu-id="e4df4-136">할 일 항목을 업데이트하는 작업은 추가하는 작업과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="e4df4-136">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="e4df4-137">`url`은 항목의 고유 식별자를 추가하도록 변경되고 `type`은 `PUT`입니다.</span><span class="sxs-lookup"><span data-stu-id="e4df4-137">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="e4df4-138">할 일 항목 삭제</span><span class="sxs-lookup"><span data-stu-id="e4df4-138">Delete a to-do item</span></span>

<span data-ttu-id="e4df4-139">할 일 항목을 삭제하려면 AJAX 호출에서 `type`을 `DELETE`로 설정하고 URL에서 항목의 고유 식별자를 지정하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="e4df4-139">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

<span data-ttu-id="e4df4-140">API 도움말 페이지를 생성하는 방법을 알아보려면 다음 자습서로 계속 진행합니다.</span><span class="sxs-lookup"><span data-stu-id="e4df4-140">Advance to the next tutorial to learn how to generate API help pages:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>
::: moniker-end
