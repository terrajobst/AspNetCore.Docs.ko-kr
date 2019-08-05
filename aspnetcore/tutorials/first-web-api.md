---
title: '자습서: ASP.NET Core를 사용하여 웹 API 만들기'
author: rick-anderson
description: ASP.NET Core를 사용하여 웹 API를 빌드하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
uid: tutorials/first-web-api
ms.openlocfilehash: 60235af56077127093ac1d77338bc228a6edf073
ms.sourcegitcommit: 0efb9e219fef481dee35f7b763165e488aa6cf9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602491"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="7f94a-103">자습서: ASP.NET Core를 사용하여 웹 API 만들기</span><span class="sxs-lookup"><span data-stu-id="7f94a-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="7f94a-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="7f94a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="7f94a-105">이 자습서에서는 ASP.NET Core를 사용하여 웹 API를 빌드하는 작업의 기본 사항을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7f94a-106">이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="7f94a-107">웹 API 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-107">Create a web API project.</span></span>
> * <span data-ttu-id="7f94a-108">모델 클래스와 데이터베이스 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="7f94a-109">CRUD 메서드로 컨트롤러를 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="7f94a-110">라우팅, URL 경로 및 반환 값을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="7f94a-111">Postman을 사용하여 웹 API 호출</span><span class="sxs-lookup"><span data-stu-id="7f94a-111">Call the web API with Postman.</span></span>

<span data-ttu-id="7f94a-112">작업을 완료하면 웹 API는 데이터베이스에 저장된 “할 일” 항목을 관리할 수 있게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="7f94a-113">개요</span><span class="sxs-lookup"><span data-stu-id="7f94a-113">Overview</span></span>

<span data-ttu-id="7f94a-114">이 자습서에서는 다음 API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="7f94a-115">API</span><span class="sxs-lookup"><span data-stu-id="7f94a-115">API</span></span> | <span data-ttu-id="7f94a-116">설명</span><span class="sxs-lookup"><span data-stu-id="7f94a-116">Description</span></span> | <span data-ttu-id="7f94a-117">요청 본문</span><span class="sxs-lookup"><span data-stu-id="7f94a-117">Request body</span></span> | <span data-ttu-id="7f94a-118">응답 본문</span><span class="sxs-lookup"><span data-stu-id="7f94a-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="7f94a-119">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="7f94a-119">GET /api/TodoItems</span></span> | <span data-ttu-id="7f94a-120">할 일 항목 모두 가져오기</span><span class="sxs-lookup"><span data-stu-id="7f94a-120">Get all to-do items</span></span> | <span data-ttu-id="7f94a-121">없음</span><span class="sxs-lookup"><span data-stu-id="7f94a-121">None</span></span> | <span data-ttu-id="7f94a-122">할 일 항목의 배열</span><span class="sxs-lookup"><span data-stu-id="7f94a-122">Array of to-do items</span></span>|
|<span data-ttu-id="7f94a-123">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="7f94a-123">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="7f94a-124">ID로 항목 가져오기</span><span class="sxs-lookup"><span data-stu-id="7f94a-124">Get an item by ID</span></span> | <span data-ttu-id="7f94a-125">없음</span><span class="sxs-lookup"><span data-stu-id="7f94a-125">None</span></span> | <span data-ttu-id="7f94a-126">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="7f94a-126">To-do item</span></span>|
|<span data-ttu-id="7f94a-127">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="7f94a-127">POST /api/TodoItems</span></span> | <span data-ttu-id="7f94a-128">새 항목 추가</span><span class="sxs-lookup"><span data-stu-id="7f94a-128">Add a new item</span></span> | <span data-ttu-id="7f94a-129">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="7f94a-129">To-do item</span></span> | <span data-ttu-id="7f94a-130">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="7f94a-130">To-do item</span></span> |
|<span data-ttu-id="7f94a-131">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="7f94a-131">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="7f94a-132">기존 항목 업데이트 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="7f94a-132">Update an existing item &nbsp;</span></span> | <span data-ttu-id="7f94a-133">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="7f94a-133">To-do item</span></span> | <span data-ttu-id="7f94a-134">없음</span><span class="sxs-lookup"><span data-stu-id="7f94a-134">None</span></span> |
|<span data-ttu-id="7f94a-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="7f94a-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="7f94a-136">항목 삭제 &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="7f94a-136">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="7f94a-137">없음</span><span class="sxs-lookup"><span data-stu-id="7f94a-137">None</span></span> | <span data-ttu-id="7f94a-138">없음</span><span class="sxs-lookup"><span data-stu-id="7f94a-138">None</span></span>|

<span data-ttu-id="7f94a-139">다음 다이어그램에서는 앱의 디자인을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-139">The following diagram shows the design of the app.</span></span>

![클라이언트는 왼쪽에 상자로 표시됩니다.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="7f94a-145">전제 조건</span><span class="sxs-lookup"><span data-stu-id="7f94a-145">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7f94a-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f94a-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7f94a-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7f94a-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7f94a-148">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7f94a-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="7f94a-149">웹 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="7f94a-149">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7f94a-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f94a-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7f94a-151">**파일** 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="7f94a-152">**ASP.NET Core 웹 애플리케이션** 템플릿을 선택하고 **다음**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="7f94a-153">프로젝트 이름을 *TodoApi*로 지정하고 **만들기**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-153">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="7f94a-154">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **.NET Core** 및 **ASP.NET Core 3.0**이 선택되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span> <span data-ttu-id="7f94a-155">**API** 템플릿을 선택하고 **만들기**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-155">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="7f94a-156">**Docker 지원 사용**을 선택하지 **마세요**.</span><span class="sxs-lookup"><span data-stu-id="7f94a-156">**Don't** select **Enable Docker Support**.</span></span>

![VS 새 프로젝트 대화 상자](first-web-api/_static/vs3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7f94a-158">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7f94a-158">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7f94a-159">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-159">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="7f94a-160">디렉터리(`cd`)를 프로젝트 폴더를 포함하는 폴더로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-160">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="7f94a-161">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-161">Run the following commands:</span></span>

   ```console
   dotnet new webapi -o TodoApi
   cd TodoAPI
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
   dotnet add package Microsoft.EntityFrameworkCore.InMemory --version 3.0.0-*
   code -r ../TodoApi
   ```

* <span data-ttu-id="7f94a-162">프로젝트에 필수 자산을 추가하려는지 묻는 대화 상자가 나타나면 **예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-162">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="7f94a-163">이전 명령은:</span><span class="sxs-lookup"><span data-stu-id="7f94a-163">The preceding commands:</span></span>

  * <span data-ttu-id="7f94a-164">새 웹 API 프로젝트를 만들고 Visual Studio Code에서 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-164">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="7f94a-165">다음 섹션에서 필요한 NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-165">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7f94a-166">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7f94a-166">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7f94a-167">**파일** > **새 솔루션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-167">Select **File** > **New Solution**.</span></span>

  ![macOS 새 솔루션](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="7f94a-169">**.NET Core** > **앱** > **API** > **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-169">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS 새 프로젝트 대화 상자](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="7f94a-171">**새 ASP.NET Core Web API 구성** 대화 상자에서 \* *.NET Core 3.0*의 **대상 프레임워크**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-171">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.0*.</span></span>

* <span data-ttu-id="7f94a-172">**프로젝트 이름**으로 *TodoApi*를 입력한 다음, **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-172">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![구성 대화 상자](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="7f94a-174">API 테스트</span><span class="sxs-lookup"><span data-stu-id="7f94a-174">Test the API</span></span>

<span data-ttu-id="7f94a-175">프로젝트 템플릿은 `WeatherForecast` API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-175">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="7f94a-176">브라우저에서 `Get` 메서드를 호출하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-176">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7f94a-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f94a-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7f94a-178">Ctrl+F5 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-178">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="7f94a-179">Visual Studio가 브라우저를 시작하고 `https://localhost:<port>/WeatherForecast`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-179">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="7f94a-180">IIS Express 인증서를 신뢰해야 하는지 묻는 대화 상자가 표시되면 **예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-180">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="7f94a-181">다음으로, **보안 경고** 대화 상자가 나타나면 **예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-181">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7f94a-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7f94a-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7f94a-183">Ctrl+F5 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-183">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="7f94a-184">브라우저에서 다음 URL [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast)로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-184">In a browser, go to following URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7f94a-185">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7f94a-185">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7f94a-186">**실행** > **디버깅 시작**을 선택하여 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-186">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="7f94a-187">Mac용 Visual Studio가 브라우저를 시작하고 `https://localhost:<port>`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-187">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="7f94a-188">HTTP 404(찾을 수 없음) 오류가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-188">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="7f94a-189">`/WeatherForecast`를 URL에 추가합니다(URL을 `https://localhost:<port>/WeatherForecast`로 변경).</span><span class="sxs-lookup"><span data-stu-id="7f94a-189">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="7f94a-190">다음과 비슷한 JSON이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-190">JSON similar to the following is returned:</span></span>

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

## <a name="add-a-model-class"></a><span data-ttu-id="7f94a-191">모델 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="7f94a-191">Add a model class</span></span>

<span data-ttu-id="7f94a-192">*모델*은 앱에서 관리하는 데이터를 나타내는 일련의 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-192">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="7f94a-193">이 앱에 대한 모델은 단일 `TodoItem` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-193">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7f94a-194">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f94a-194">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7f94a-195">**솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-195">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="7f94a-196">**추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-196">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="7f94a-197">폴더 이름을 *Models*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-197">Name the folder *Models*.</span></span>

* <span data-ttu-id="7f94a-198">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-198">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="7f94a-199">클래스 이름을 *TodoItem*으로 지정하고 **추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-199">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="7f94a-200">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-200">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7f94a-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7f94a-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7f94a-202">*Models* 폴더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-202">Add a folder named *Models*.</span></span>

* <span data-ttu-id="7f94a-203">다음 코드를 사용하여 *Models* 폴더에 `TodoItem` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-203">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7f94a-204">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7f94a-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7f94a-205">프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-205">Right-click the project.</span></span> <span data-ttu-id="7f94a-206">**추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-206">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="7f94a-207">폴더 이름을 *Models*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-207">Name the folder *Models*.</span></span>

  ![새 폴더](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="7f94a-209">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 파일** > **일반** > **빈 클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-209">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="7f94a-210">클래스 이름을 *TodoItem*으로 지정한 다음, **새로 만들기**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-210">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="7f94a-211">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-211">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="7f94a-212">`Id` 속성은 관계형 데이터베이스에서 고유 키로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-212">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="7f94a-213">모델 클래스는 프로젝트의 어디로든 이동할 수 있지만 규칙에 따라 *Models* 폴더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-213">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="7f94a-214">데이터베이스 컨텍스트 추가</span><span class="sxs-lookup"><span data-stu-id="7f94a-214">Add a database context</span></span>

<span data-ttu-id="7f94a-215">*데이터베이스 컨텍스트*는 데이터 모델에 맞게 Entity Framework 기능을 조정하는 주 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-215">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="7f94a-216">`Microsoft.EntityFrameworkCore.DbContext` 클래스에서 파생시키는 방식으로 이 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-216">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7f94a-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f94a-217">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="7f94a-218">Microsoft.EntityFrameworkCore.SqlServer 추가</span><span class="sxs-lookup"><span data-stu-id="7f94a-218">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="7f94a-219">**도구** 메뉴에서 **NuGet 패키지 관리자 > 솔루션용 NuGet 패키지 관리**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-219">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="7f94a-220">**시험판 포함** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-220">Select the **Include prerelease** checkbox.</span></span>
* <span data-ttu-id="7f94a-221">**찾아보기** 탭을 선택한 다음 검색 상자에 **Microsoft.EntityFrameworkCore.SqlServer**를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-221">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="7f94a-222">왼쪽 창에서 **Microsoft.EntityFrameworkCore.SqlServer V3.0.0-preview**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-222">Select  **Microsoft.EntityFrameworkCore.SqlServer V3.0.0-preview** in the left pane.</span></span>
* <span data-ttu-id="7f94a-223">오른쪽 창에서 **프로젝트** 확인란을 선택하고 **설치**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-223">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="7f94a-224">위 지침에 따라 `Microsoft.EntityFrameworkCore.InMemory` NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-224">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![NuGet 패키지 관리자](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="7f94a-226">TodoContext 데이터베이스 컨텍스트 추가</span><span class="sxs-lookup"><span data-stu-id="7f94a-226">Add the TodoContext database context</span></span>

* <span data-ttu-id="7f94a-227">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-227">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="7f94a-228">클래스 이름을 *TodoContext*로 지정하고 **추가**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-228">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="7f94a-229">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7f94a-229">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="7f94a-230">`TodoContext` 클래스를 *Models* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-230">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="7f94a-231">다음 코드를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-231">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="7f94a-232">데이터베이스 컨텍스트 등록</span><span class="sxs-lookup"><span data-stu-id="7f94a-232">Register the database context</span></span>

<span data-ttu-id="7f94a-233">ASP.NET Core에서는 DB 컨텍스트와 같은 서비스를 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 컨테이너에 등록해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-233">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="7f94a-234">컨테이너는 컨트롤러에 서비스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-234">The container provides the service to controllers.</span></span>

<span data-ttu-id="7f94a-235">*Startup.cs*를 다음 강조 표시된 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-235">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="7f94a-236">위의 코드는:</span><span class="sxs-lookup"><span data-stu-id="7f94a-236">The preceding code:</span></span>

* <span data-ttu-id="7f94a-237">사용되지 않는 `using` 선언을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-237">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="7f94a-238">DI 컨테이너에 데이터베이스 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-238">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="7f94a-239">데이터베이스 컨텍스트가 메모리 내 데이터베이스를 사용하도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-239">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="7f94a-240">컨트롤러 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="7f94a-240">Scaffold a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7f94a-241">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f94a-241">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7f94a-242">*Controllers* 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-242">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="7f94a-243">**추가** > **스캐폴드 항목 새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-243">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="7f94a-244">**Entity Framework를 사용하며 동작이 포함된 API 컨트롤러**를 선택하고 **추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-244">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="7f94a-245">**Entity Framework를 사용하며 동작이 포함된 API 컨트롤러 추가** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="7f94a-245">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="7f94a-246">**모델 클래스**에서 **TodoItem (TodoAPI.Models)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-246">Select **TodoItem (TodoAPI.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="7f94a-247">**데이터 컨텍스트 클래스**에서 **TodoContext (TodoAPI.Models)** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-247">Select **TodoContext (TodoAPI.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="7f94a-248">**추가** 선택</span><span class="sxs-lookup"><span data-stu-id="7f94a-248">Select **Add**</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="7f94a-249">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7f94a-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="7f94a-250">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-250">Run the following commands:</span></span>

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.0.0-*
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext  -outDir Controllers
```

<span data-ttu-id="7f94a-251">이전 명령은:</span><span class="sxs-lookup"><span data-stu-id="7f94a-251">The preceding commands:</span></span>

* <span data-ttu-id="7f94a-252">스캐폴딩에 필요한 NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-252">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="7f94a-253">스캐폴딩 엔진(`dotnet-aspnet-codegenerator`)을 설치합니다</span><span class="sxs-lookup"><span data-stu-id="7f94a-253">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="7f94a-254">`TodoItemsController`를 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-254">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="7f94a-255">생성된 코드는:</span><span class="sxs-lookup"><span data-stu-id="7f94a-255">The generated code:</span></span>

* <span data-ttu-id="7f94a-256">메서드 없이 API 컨트롤러 클래스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-256">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="7f94a-257">[[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 특성을 사용하여 클래스를 데코레이팅합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-257">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="7f94a-258">이 특성은 컨트롤러가 웹 API 요청에 응답함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-258">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="7f94a-259">특성을 사용하도록 설정하는 특정 동작에 대한 정보는 <xref:web-api/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7f94a-259">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="7f94a-260">DI를 사용하여 데이터베이스 컨텍스트(`TodoContext`)를 컨트롤러에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-260">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="7f94a-261">컨트롤러의 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드 각각에서 데이터베이스 컨텍스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-261">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="7f94a-262">PostTodoItem 만들기 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-262">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="7f94a-263">`PostTodoItem`의 return 문이 [nameof](/dotnet/csharp/language-reference/operators/nameof) 연산자를 사용하도록 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-263">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="7f94a-264">이전 코드는 [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 특성으로 표시되는 HTTP POST 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-264">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="7f94a-265">이 메서드는 HTTP 요청 본문에서 할 일 항목 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-265">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="7f94a-266"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-266">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="7f94a-267">성공 시 HTTP 201 상태 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-267">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="7f94a-268">HTTP 201은 서버에서 새 리소스를 만드는 HTTP POST 메서드의 표준 응답입니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-268">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="7f94a-269">응답에 대한 [위치](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-269">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="7f94a-270">`Location` 헤더는 새로 만들어진 할 일 항목의 [URI](https://developer.mozilla.org/docs/Glossary/URI)를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-270">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="7f94a-271">자세한 내용은 [10.2.2 201 생성됨](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7f94a-271">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="7f94a-272">`Location` 헤더의 URI를 만들려면 `GetTodoItem` 작업을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-272">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="7f94a-273">C# `nameof` 키워드는 `CreatedAtAction` 호출에서 작업 이름의 하드 코딩을 방지하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-273">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="7f94a-274">Postman 설치</span><span class="sxs-lookup"><span data-stu-id="7f94a-274">Install Postman</span></span>

<span data-ttu-id="7f94a-275">이 자습서에서는 Postman을 사용하여 웹 API를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-275">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="7f94a-276">[Postman](https://www.getpostman.com/downloads/) 설치</span><span class="sxs-lookup"><span data-stu-id="7f94a-276">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="7f94a-277">웹앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-277">Start the web app.</span></span>
* <span data-ttu-id="7f94a-278">Postman을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-278">Start Postman.</span></span>
* <span data-ttu-id="7f94a-279">**SSL 인증서 확인** 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="7f94a-279">Disable **SSL certificate verification**</span></span>
* <span data-ttu-id="7f94a-280">**파일 > 설정**(\**일반* 탭)에서 **SSL 인증서 확인**을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-280">From  **File > Settings** (\**General* tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="7f94a-281">컨트롤러를 테스트한 후에 SSL 인증서 확인을 다시 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-281">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="7f94a-282">Postman을 사용하여 PostTodoItem 테스트</span><span class="sxs-lookup"><span data-stu-id="7f94a-282">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="7f94a-283">새 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-283">Create a new request.</span></span>
* <span data-ttu-id="7f94a-284">HTTP 메서드를 `POST`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-284">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="7f94a-285">**본문** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-285">Select the **Body** tab.</span></span>
* <span data-ttu-id="7f94a-286">**원시** 라디오 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-286">Select the **raw** radio button.</span></span>
* <span data-ttu-id="7f94a-287">유형을 **JSON(application/json)** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-287">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="7f94a-288">요청 본문에서 할 일 항목에 대한 JSON을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-288">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="7f94a-289">**보내기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-289">Select **Send**.</span></span>

  ![생성 요청이 있는 Postman](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="7f94a-291">위치 헤더 URI 테스트</span><span class="sxs-lookup"><span data-stu-id="7f94a-291">Test the location header URI</span></span>

* <span data-ttu-id="7f94a-292">**응답** 창에서 **헤더** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-292">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="7f94a-293">**위치** 헤더 값을 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-293">Copy the **Location** header value:</span></span>

  ![Postman 콘솔의 헤더 탭](first-web-api/_static/3/create.png)

* <span data-ttu-id="7f94a-295">메서드를 GET으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-295">Set the method to GET.</span></span>
* <span data-ttu-id="7f94a-296">URI(예: `https://localhost:5001/api/TodoItems/1`) 붙여넣기</span><span class="sxs-lookup"><span data-stu-id="7f94a-296">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`)</span></span>
* <span data-ttu-id="7f94a-297">**보내기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-297">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="7f94a-298">GET 메서드 검사</span><span class="sxs-lookup"><span data-stu-id="7f94a-298">Examine the GET methods</span></span>

<span data-ttu-id="7f94a-299">다음과 같은 메서드는 두 개의 GET 엔드포인트를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-299">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="7f94a-300">브라우저 또는 Postman에서 두 개의 엔드포인트를 호출하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-300">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="7f94a-301">예:</span><span class="sxs-lookup"><span data-stu-id="7f94a-301">For example:</span></span>

* [https://localhost:5001/api/TodoItems](https://localhost:5001/api/TodoItems)
* [https://localhost:5001/api/TodoItems/1](https://localhost:5001/api/TodoItems/1)

<span data-ttu-id="7f94a-302">`GetTodoItems`를 호출하면 다음과 비슷한 응답이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-302">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="7f94a-303">Postman을 사용하여 Get 테스트</span><span class="sxs-lookup"><span data-stu-id="7f94a-303">Test Get with Postman</span></span>

* <span data-ttu-id="7f94a-304">새 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-304">Create a new request.</span></span>
* <span data-ttu-id="7f94a-305">HTTP 메서드를 **GET**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-305">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="7f94a-306">요청 URL을 `https://localhost:<port>/api/TodoItems`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-306">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="7f94a-307">예를 들어, `https://localhost:5001/api/TodoItems`을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-307">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="7f94a-308">Postman에서 **두 개의 창 보기**를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-308">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="7f94a-309">**보내기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-309">Select **Send**.</span></span>

<span data-ttu-id="7f94a-310">이 앱은 메모리 내 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-310">This app uses an in-memory database.</span></span> <span data-ttu-id="7f94a-311">앱이 중지된 후 시작되면 이전 GET 요청이 데이터를 반환하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-311">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="7f94a-312">데이터가 반환되지 않으면 앱에 데이터를 [POST](#post)합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-312">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="7f94a-313">라우팅 및 URL 경로</span><span class="sxs-lookup"><span data-stu-id="7f94a-313">Routing and URL paths</span></span>

<span data-ttu-id="7f94a-314">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 특성은 HTTP GET 요청에 응답하는 메서드를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-314">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="7f94a-315">각 방법에 대한 URL 경로는 다음과 같이 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-315">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="7f94a-316">컨트롤러의 `Route` 특성에서 템플릿 문자열로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-316">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="7f94a-317">`[controller]`를 컨트롤러의 이름으로 바꿉니다. 일반적으로 컨트롤러 클래스 이름에서 "Controller" 접미사를 뺀 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-317">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="7f94a-318">이 샘플의 경우 컨트롤러 클래스 이름은 **TodoItems**Controller이므로 컨트롤러 이름은 “TodoItems”입니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-318">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="7f94a-319">ASP.NET Core [라우팅](xref:mvc/controllers/routing)은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-319">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="7f94a-320">`[HttpGet]` 특성에 경로 템플릿(예: `[HttpGet("products")]`)이 있는 경우 경로에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-320">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="7f94a-321">이 샘플은 템플릿을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-321">This sample doesn't use a template.</span></span> <span data-ttu-id="7f94a-322">자세한 내용은 [Http[동사] 특성을 사용한 특성 라우팅](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7f94a-322">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="7f94a-323">다음 `GetTodoItem` 메서드에서 `"{id}"`는 할 일 항목의 고유 식별자에 대한 자리 표시자 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-323">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="7f94a-324">`GetTodoItem`가 호출되면 URL의 `"{id}"` 값을 `id` 매개 변수의 메서드에 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-324">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="7f94a-325">반환 값</span><span class="sxs-lookup"><span data-stu-id="7f94a-325">Return values</span></span>

<span data-ttu-id="7f94a-326">`GetTodoItems` 및 `GetTodoItem` 메서드의 반환 형식은 [ActionResult\<T> 형식](xref:web-api/action-return-types#actionresultt-type)입니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-326">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="7f94a-327">ASP.NET Core는 자동으로 [JSON](https://www.json.org/)에 개체를 직렬화하고 JSON을 응답 메시지의 본문에 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-327">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="7f94a-328">이 반환 형식의 응답 코드는 200이며 처리되지 않은 예외가 없다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-328">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="7f94a-329">처리되지 않은 예외는 5xx 오류로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-329">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="7f94a-330">`ActionResult` 반환 형식은 다양한 HTTP 상태 코드를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-330">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="7f94a-331">예를 들어 `GetTodoItem`은 두 가지 상태 값을 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-331">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="7f94a-332">요청된 ID와 일치하는 항목이 없는 경우 메서드가 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) 오류 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-332">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="7f94a-333">그렇지 않으면 메서드가 JSON 응답 본문에서 200을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-333">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="7f94a-334">`item`을 반환하면 HTTP 200 응답이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-334">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="7f94a-335">PutTodoItem 메서드</span><span class="sxs-lookup"><span data-stu-id="7f94a-335">The PutTodoItem method</span></span>

<span data-ttu-id="7f94a-336">다음과 같이 `PutTodoItem` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-336">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="7f94a-337">HTTP PUT을 사용하는 것을 제외하고 `PutTodoItem`는 `PostTodoItem`와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-337">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="7f94a-338">응답은 [204(콘텐츠 없음)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-338">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="7f94a-339">HTTP 사양에 따라 PUT 요청의 경우 클라이언트는 변경 내용만이 아니라 전체 업데이트된 엔터티를 보내야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-339">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="7f94a-340">부분 업데이트를 지원하려면 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-340">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="7f94a-341">`PutTodoItem`을 호출하는 중 오류가 발생하면 `GET`을 호출하여 데이터베이스에 항목이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-341">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="7f94a-342">PutTodoItem 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="7f94a-342">Test the PutTodoItem method</span></span>

<span data-ttu-id="7f94a-343">이 샘플은 앱이 시작될 때마다 초기화되어야 하는 메모리 내 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-343">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="7f94a-344">PUT 호출을 실행하기 전에 데이터베이스에 항목이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-344">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="7f94a-345">GET을 호출하여 PUT 호출을 실행하기 전에 데이터베이스에 항목이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-345">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="7f94a-346">ID = 1인 할 일 항목을 업데이트하고 해당 이름을 “feed fish”로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-346">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="7f94a-347">다음 이미지는 Postman 업데이트를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-347">The following image shows the Postman update:</span></span>

![204(콘텐츠 없음) 응답을 보여주는 Postman 콘솔](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="7f94a-349">DeleteTodoItem 메서드</span><span class="sxs-lookup"><span data-stu-id="7f94a-349">The DeleteTodoItem method</span></span>

<span data-ttu-id="7f94a-350">다음과 같이 `DeleteTodoItem` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-350">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

<span data-ttu-id="7f94a-351">`DeleteTodoItem` 응답은 [204(콘텐츠 없음)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-351">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="7f94a-352">DeleteTodoItem 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="7f94a-352">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="7f94a-353">Postman을 사용하여 할 일 항목을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-353">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="7f94a-354">메서드를 `DELETE`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-354">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="7f94a-355">예를 들어 삭제할 개체의 URI를 `https://localhost:5001/api/TodoItems/1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-355">Set the URI of the object to delete, for example `https://localhost:5001/api/TodoItems/1`</span></span>
* <span data-ttu-id="7f94a-356">**보내기** 선택</span><span class="sxs-lookup"><span data-stu-id="7f94a-356">Select **Send**</span></span>

## <a name="call-the-api-from-jquery"></a><span data-ttu-id="7f94a-357">jQuery에서 API 호출</span><span class="sxs-lookup"><span data-stu-id="7f94a-357">Call the API from jQuery</span></span>

<span data-ttu-id="7f94a-358">단계별 지침은 [자습서: jQuery로 ASP.NET Core 웹 API 호출하기](xref:tutorials/web-api-jquery)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7f94a-358">See [Tutorial: Call an ASP.NET Core web API with jQuery](xref:tutorials/web-api-jquery).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7f94a-359">이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-359">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="7f94a-360">웹 API 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-360">Create a web API project.</span></span>
> * <span data-ttu-id="7f94a-361">모델 클래스와 데이터베이스 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-361">Add a model class and a database context.</span></span>
> * <span data-ttu-id="7f94a-362">컨트롤러 추가</span><span class="sxs-lookup"><span data-stu-id="7f94a-362">Add a controller.</span></span>
> * <span data-ttu-id="7f94a-363">CRUD 메서드 추가</span><span class="sxs-lookup"><span data-stu-id="7f94a-363">Add CRUD methods.</span></span>
> * <span data-ttu-id="7f94a-364">라우팅 및 URL 경로 구성</span><span class="sxs-lookup"><span data-stu-id="7f94a-364">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="7f94a-365">반환 값 지정</span><span class="sxs-lookup"><span data-stu-id="7f94a-365">Specify return values.</span></span>
> * <span data-ttu-id="7f94a-366">Postman을 사용하여 웹 API 호출</span><span class="sxs-lookup"><span data-stu-id="7f94a-366">Call the web API with Postman.</span></span>
> * <span data-ttu-id="7f94a-367">jQuery를 사용하여 웹 API를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-367">Call the web API with jQuery.</span></span>

<span data-ttu-id="7f94a-368">작업을 완료하면 웹 API는 관계형 데이터베이스에 저장된 "할 일" 항목을 관리할 수 있게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-368">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>
## <a name="overview"></a><span data-ttu-id="7f94a-369">개요</span><span class="sxs-lookup"><span data-stu-id="7f94a-369">Overview</span></span>

<span data-ttu-id="7f94a-370">이 자습서에서는 다음 API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-370">This tutorial creates the following API:</span></span>

|<span data-ttu-id="7f94a-371">API</span><span class="sxs-lookup"><span data-stu-id="7f94a-371">API</span></span> | <span data-ttu-id="7f94a-372">설명</span><span class="sxs-lookup"><span data-stu-id="7f94a-372">Description</span></span> | <span data-ttu-id="7f94a-373">요청 본문</span><span class="sxs-lookup"><span data-stu-id="7f94a-373">Request body</span></span> | <span data-ttu-id="7f94a-374">응답 본문</span><span class="sxs-lookup"><span data-stu-id="7f94a-374">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="7f94a-375">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="7f94a-375">GET /api/TodoItems</span></span> | <span data-ttu-id="7f94a-376">할 일 항목 모두 가져오기</span><span class="sxs-lookup"><span data-stu-id="7f94a-376">Get all to-do items</span></span> | <span data-ttu-id="7f94a-377">없음</span><span class="sxs-lookup"><span data-stu-id="7f94a-377">None</span></span> | <span data-ttu-id="7f94a-378">할 일 항목의 배열</span><span class="sxs-lookup"><span data-stu-id="7f94a-378">Array of to-do items</span></span>|
|<span data-ttu-id="7f94a-379">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="7f94a-379">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="7f94a-380">ID로 항목 가져오기</span><span class="sxs-lookup"><span data-stu-id="7f94a-380">Get an item by ID</span></span> | <span data-ttu-id="7f94a-381">없음</span><span class="sxs-lookup"><span data-stu-id="7f94a-381">None</span></span> | <span data-ttu-id="7f94a-382">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="7f94a-382">To-do item</span></span>|
|<span data-ttu-id="7f94a-383">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="7f94a-383">POST /api/TodoItems</span></span> | <span data-ttu-id="7f94a-384">새 항목 추가</span><span class="sxs-lookup"><span data-stu-id="7f94a-384">Add a new item</span></span> | <span data-ttu-id="7f94a-385">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="7f94a-385">To-do item</span></span> | <span data-ttu-id="7f94a-386">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="7f94a-386">To-do item</span></span> |
|<span data-ttu-id="7f94a-387">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="7f94a-387">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="7f94a-388">기존 항목 업데이트 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="7f94a-388">Update an existing item &nbsp;</span></span> | <span data-ttu-id="7f94a-389">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="7f94a-389">To-do item</span></span> | <span data-ttu-id="7f94a-390">없음</span><span class="sxs-lookup"><span data-stu-id="7f94a-390">None</span></span> |
|<span data-ttu-id="7f94a-391">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="7f94a-391">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="7f94a-392">항목 삭제 &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="7f94a-392">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="7f94a-393">없음</span><span class="sxs-lookup"><span data-stu-id="7f94a-393">None</span></span> | <span data-ttu-id="7f94a-394">없음</span><span class="sxs-lookup"><span data-stu-id="7f94a-394">None</span></span>|

<span data-ttu-id="7f94a-395">다음 다이어그램에서는 앱의 디자인을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-395">The following diagram shows the design of the app.</span></span>

![클라이언트는 왼쪽에 상자로 표시됩니다.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="7f94a-401">전제 조건</span><span class="sxs-lookup"><span data-stu-id="7f94a-401">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7f94a-402">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f94a-402">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7f94a-403">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7f94a-403">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7f94a-404">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7f94a-404">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="7f94a-405">웹 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="7f94a-405">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7f94a-406">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f94a-406">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7f94a-407">**파일** 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-407">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="7f94a-408">**ASP.NET Core 웹 애플리케이션** 템플릿을 선택하고 **다음**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-408">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="7f94a-409">프로젝트 이름을 *TodoApi*로 지정하고 **만들기**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-409">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="7f94a-410">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **.NET Core** 및 **ASP.NET Core 2.2**가 선택되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-410">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="7f94a-411">**API** 템플릿을 선택하고 **만들기**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-411">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="7f94a-412">**Docker 지원 사용**을 선택하지 **마세요**.</span><span class="sxs-lookup"><span data-stu-id="7f94a-412">**Don't** select **Enable Docker Support**.</span></span>

![VS 새 프로젝트 대화 상자](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7f94a-414">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7f94a-414">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7f94a-415">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-415">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="7f94a-416">디렉터리(`cd`)를 프로젝트 폴더를 포함하는 폴더로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-416">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="7f94a-417">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-417">Run the following commands:</span></span>

   ```console
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="7f94a-418">이러한 명령은 새 웹 API 프로젝트를 만들고 새 프로젝트 폴더에서 Visual Studio Code의 새 인스턴스를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-418">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="7f94a-419">프로젝트에 필수 자산을 추가하려는지 묻는 대화 상자가 나타나면 **예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-419">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7f94a-420">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7f94a-420">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7f94a-421">**파일** > **새 솔루션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-421">Select **File** > **New Solution**.</span></span>

  ![macOS 새 솔루션](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="7f94a-423">**.NET Core** > **앱** > **API** > **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-423">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS 새 프로젝트 대화 상자](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="7f94a-425">**새 ASP.NET Core Web API 구성** 대화 상자에서 \* *.NET Core 2.2*라는 기본 **대상 프레임워크**를 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-425">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="7f94a-426">**프로젝트 이름**으로 *TodoApi*를 입력한 다음, **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-426">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![구성 대화 상자](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="7f94a-428">API 테스트</span><span class="sxs-lookup"><span data-stu-id="7f94a-428">Test the API</span></span>

<span data-ttu-id="7f94a-429">프로젝트 템플릿은 `values` API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-429">The project template creates a `values` API.</span></span> <span data-ttu-id="7f94a-430">브라우저에서 `Get` 메서드를 호출하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-430">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7f94a-431">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f94a-431">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7f94a-432">Ctrl+F5 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-432">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="7f94a-433">Visual Studio가 브라우저를 시작하고 `https://localhost:<port>/api/values`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-433">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="7f94a-434">IIS Express 인증서를 신뢰해야 하는지 묻는 대화 상자가 표시되면 **예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-434">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="7f94a-435">다음으로, **보안 경고** 대화 상자가 나타나면 **예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-435">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7f94a-436">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7f94a-436">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7f94a-437">Ctrl+F5 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-437">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="7f94a-438">브라우저에서 다음 URL [https://localhost:5001/api/values](https://localhost:5001/api/values)로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-438">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7f94a-439">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7f94a-439">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7f94a-440">**실행** > **디버깅 시작**을 선택하여 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-440">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="7f94a-441">Mac용 Visual Studio가 브라우저를 시작하고 `https://localhost:<port>`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-441">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="7f94a-442">HTTP 404(찾을 수 없음) 오류가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-442">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="7f94a-443">`/api/values`를 URL에 추가합니다(URL을 `https://localhost:<port>/api/values`로 변경).</span><span class="sxs-lookup"><span data-stu-id="7f94a-443">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="7f94a-444">다음 JSON이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-444">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="7f94a-445">모델 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="7f94a-445">Add a model class</span></span>

<span data-ttu-id="7f94a-446">*모델*은 앱에서 관리하는 데이터를 나타내는 일련의 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-446">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="7f94a-447">이 앱에 대한 모델은 단일 `TodoItem` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-447">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7f94a-448">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f94a-448">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7f94a-449">**솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-449">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="7f94a-450">**추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-450">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="7f94a-451">폴더 이름을 *Models*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-451">Name the folder *Models*.</span></span>

* <span data-ttu-id="7f94a-452">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-452">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="7f94a-453">클래스 이름을 *TodoItem*으로 지정하고 **추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-453">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="7f94a-454">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-454">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7f94a-455">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7f94a-455">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7f94a-456">*Models* 폴더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-456">Add a folder named *Models*.</span></span>

* <span data-ttu-id="7f94a-457">다음 코드를 사용하여 *Models* 폴더에 `TodoItem` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-457">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7f94a-458">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7f94a-458">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7f94a-459">프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-459">Right-click the project.</span></span> <span data-ttu-id="7f94a-460">**추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-460">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="7f94a-461">폴더 이름을 *Models*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-461">Name the folder *Models*.</span></span>

  ![새 폴더](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="7f94a-463">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 파일** > **일반** > **빈 클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-463">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="7f94a-464">클래스 이름을 *TodoItem*으로 지정한 다음, **새로 만들기**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-464">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="7f94a-465">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-465">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="7f94a-466">`Id` 속성은 관계형 데이터베이스에서 고유 키로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-466">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="7f94a-467">모델 클래스는 프로젝트의 어디로든 이동할 수 있지만 규칙에 따라 *Models* 폴더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-467">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="7f94a-468">데이터베이스 컨텍스트 추가</span><span class="sxs-lookup"><span data-stu-id="7f94a-468">Add a database context</span></span>

<span data-ttu-id="7f94a-469">*데이터베이스 컨텍스트*는 데이터 모델에 맞게 Entity Framework 기능을 조정하는 주 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-469">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="7f94a-470">`Microsoft.EntityFrameworkCore.DbContext` 클래스에서 파생시키는 방식으로 이 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-470">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7f94a-471">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f94a-471">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7f94a-472">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-472">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="7f94a-473">클래스 이름을 *TodoContext*로 지정하고 **추가**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-473">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="7f94a-474">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7f94a-474">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="7f94a-475">`TodoContext` 클래스를 *Models* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-475">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="7f94a-476">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-476">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="7f94a-477">데이터베이스 컨텍스트 등록</span><span class="sxs-lookup"><span data-stu-id="7f94a-477">Register the database context</span></span>

<span data-ttu-id="7f94a-478">ASP.NET Core에서는 DB 컨텍스트와 같은 서비스를 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 컨테이너에 등록해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-478">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="7f94a-479">컨테이너는 컨트롤러에 서비스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-479">The container provides the service to controllers.</span></span>

<span data-ttu-id="7f94a-480">*Startup.cs*를 다음 강조 표시된 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-480">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="7f94a-481">위의 코드는:</span><span class="sxs-lookup"><span data-stu-id="7f94a-481">The preceding code:</span></span>

* <span data-ttu-id="7f94a-482">사용되지 않는 `using` 선언을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-482">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="7f94a-483">DI 컨테이너에 데이터베이스 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-483">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="7f94a-484">데이터베이스 컨텍스트가 메모리 내 데이터베이스를 사용하도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-484">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="7f94a-485">컨트롤러 추가</span><span class="sxs-lookup"><span data-stu-id="7f94a-485">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7f94a-486">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7f94a-486">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7f94a-487">*Controllers* 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-487">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="7f94a-488">**추가** > **새 항목**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-488">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="7f94a-489">**새 항목 추가** 대화 상자에서 **API 컨트롤러 클래스** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-489">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="7f94a-490">클래스 이름을 *TodoController*로 지정하고 **추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-490">Name the class *TodoController*, and select **Add**.</span></span>

  ![검색 상자의 컨트롤러 및 웹 API 컨트롤러가 선택된 새 항목 추가 대화 상자](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="7f94a-492">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7f94a-492">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="7f94a-493">*Controllers* 폴더에 `TodoController`라는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-493">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="7f94a-494">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-494">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="7f94a-495">위의 코드는:</span><span class="sxs-lookup"><span data-stu-id="7f94a-495">The preceding code:</span></span>

* <span data-ttu-id="7f94a-496">메서드 없이 API 컨트롤러 클래스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-496">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="7f94a-497">[[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 특성을 사용하여 클래스를 데코레이팅합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-497">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="7f94a-498">이 특성은 컨트롤러가 웹 API 요청에 응답함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-498">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="7f94a-499">특성을 사용하도록 설정하는 특정 동작에 대한 정보는 <xref:web-api/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7f94a-499">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="7f94a-500">DI를 사용하여 데이터베이스 컨텍스트(`TodoContext`)를 컨트롤러에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-500">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="7f94a-501">컨트롤러의 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드 각각에서 데이터베이스 컨텍스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-501">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="7f94a-502">데이터베이스가 비어 있는 경우 데이터베이스에 `Item1`이라는 항목을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-502">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="7f94a-503">이 코드는 생성자에 위치하므로 새 HTTP 요청이 발생할 때마다 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-503">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="7f94a-504">모든 항목을 삭제하면 생성자는 다음에 API가 호출될 경우 `Item1`을 다시 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-504">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="7f94a-505">따라서 실제로 작동되는 경우 삭제가 작동하지 않는 것처럼 보일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-505">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="7f94a-506">GET 메서드 추가</span><span class="sxs-lookup"><span data-stu-id="7f94a-506">Add Get methods</span></span>

<span data-ttu-id="7f94a-507">할 일 항목을 가져오는 API를 제공하려면 다음 메서드를 `TodoController` 클래스에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-507">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="7f94a-508">다음과 같은 메서드는 두 개의 GET 엔드포인트를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-508">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="7f94a-509">앱이 계속 실행되고 있으면 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-509">Stop the app if it's still running.</span></span> <span data-ttu-id="7f94a-510">그런 다음, 다시 실행하여 최신 변경 내용을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-510">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="7f94a-511">브라우저에서 두 개의 엔드포인트를 호출하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-511">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="7f94a-512">예:</span><span class="sxs-lookup"><span data-stu-id="7f94a-512">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="7f94a-513">`GetTodoItems`를 호출하여 다음 HTTP 응답이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-513">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="7f94a-514">라우팅 및 URL 경로</span><span class="sxs-lookup"><span data-stu-id="7f94a-514">Routing and URL paths</span></span>

<span data-ttu-id="7f94a-515">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 특성은 HTTP GET 요청에 응답하는 메서드를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-515">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="7f94a-516">각 방법에 대한 URL 경로는 다음과 같이 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-516">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="7f94a-517">컨트롤러의 `Route` 특성에서 템플릿 문자열로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-517">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="7f94a-518">`[controller]`를 컨트롤러의 이름으로 바꿉니다. 일반적으로 컨트롤러 클래스 이름에서 "Controller" 접미사를 뺀 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-518">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="7f94a-519">이 샘플의 경우 컨트롤러 클래스 이름은 **Todo**Controller이므로 컨트롤러 이름은 "todo"입니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-519">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="7f94a-520">ASP.NET Core [라우팅](xref:mvc/controllers/routing)은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-520">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="7f94a-521">`[HttpGet]` 특성에 경로 템플릿(예: `[HttpGet("products")]`)이 있는 경우 경로에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-521">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="7f94a-522">이 샘플은 템플릿을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-522">This sample doesn't use a template.</span></span> <span data-ttu-id="7f94a-523">자세한 내용은 [Http[동사] 특성을 사용한 특성 라우팅](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7f94a-523">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="7f94a-524">다음 `GetTodoItem` 메서드에서 `"{id}"`는 할 일 항목의 고유 식별자에 대한 자리 표시자 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-524">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="7f94a-525">`GetTodoItem`가 호출되면 URL의 `"{id}"` 값을 `id` 매개 변수의 메서드에 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-525">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="7f94a-526">반환 값</span><span class="sxs-lookup"><span data-stu-id="7f94a-526">Return values</span></span>

<span data-ttu-id="7f94a-527">`GetTodoItems` 및 `GetTodoItem` 메서드의 반환 형식은 [ActionResult\<T> 형식](xref:web-api/action-return-types#actionresultt-type)입니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-527">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="7f94a-528">ASP.NET Core는 자동으로 [JSON](https://www.json.org/)에 개체를 직렬화하고 JSON을 응답 메시지의 본문에 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-528">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="7f94a-529">이 반환 형식의 응답 코드는 200이며 처리되지 않은 예외가 없다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-529">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="7f94a-530">처리되지 않은 예외는 5xx 오류로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-530">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="7f94a-531">`ActionResult` 반환 형식은 다양한 HTTP 상태 코드를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-531">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="7f94a-532">예를 들어 `GetTodoItem`은 두 가지 상태 값을 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-532">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="7f94a-533">요청된 ID와 일치하는 항목이 없는 경우 메서드가 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) 오류 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-533">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="7f94a-534">그렇지 않으면 메서드가 JSON 응답 본문에서 200을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-534">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="7f94a-535">`item`을 반환하면 HTTP 200 응답이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-535">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="7f94a-536">GetTodoItems 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="7f94a-536">Test the GetTodoItems method</span></span>

<span data-ttu-id="7f94a-537">이 자습서에서는 Postman을 사용하여 웹 API를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-537">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="7f94a-538">[Postman](https://www.getpostman.com/downloads/) 설치</span><span class="sxs-lookup"><span data-stu-id="7f94a-538">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="7f94a-539">웹앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-539">Start the web app.</span></span>
* <span data-ttu-id="7f94a-540">Postman을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-540">Start Postman.</span></span>
* <span data-ttu-id="7f94a-541">**SSL 인증서 확인** 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="7f94a-541">Disable **SSL certificate verification**</span></span>
  
  * <span data-ttu-id="7f94a-542">**파일 > 설정**(\**일반* 탭)에서 **SSL 인증서 확인**을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-542">From  **File > Settings** (\**General* tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="7f94a-543">컨트롤러를 테스트한 후에 SSL 인증서 확인을 다시 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-543">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="7f94a-544">새 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-544">Create a new request.</span></span>
  * <span data-ttu-id="7f94a-545">HTTP 메서드를 **GET**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-545">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="7f94a-546">요청 URL을 `https://localhost:<port>/api/todo`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-546">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="7f94a-547">예를 들어, `https://localhost:5001/api/todo`을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-547">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="7f94a-548">Postman에서 **두 개의 창 보기**를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-548">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="7f94a-549">**보내기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-549">Select **Send**.</span></span>

![Get 요청이 있는 Postman](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="7f94a-551">메서드 만들기 추가</span><span class="sxs-lookup"><span data-stu-id="7f94a-551">Add a Create method</span></span>

<span data-ttu-id="7f94a-552">다음 `PostTodoItem` 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-552">Add the following `PostTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="7f94a-553">이전 코드는 [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 특성으로 표시되는 HTTP POST 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-553">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="7f94a-554">이 메서드는 HTTP 요청 본문에서 할 일 항목 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-554">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="7f94a-555">`CreatedAtAction` 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-555">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="7f94a-556">성공 시 HTTP 201 상태 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-556">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="7f94a-557">HTTP 201은 서버에서 새 리소스를 만드는 HTTP POST 메서드의 표준 응답입니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-557">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="7f94a-558">`Location` 헤더를 응답에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-558">Adds a `Location` header to the response.</span></span> <span data-ttu-id="7f94a-559">`Location` 헤더는 새로 만들어진 할 일 항목의 URI를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-559">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="7f94a-560">자세한 내용은 [10.2.2 201 생성됨](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7f94a-560">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="7f94a-561">`Location` 헤더의 URI를 만들려면 `GetTodoItem` 작업을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-561">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="7f94a-562">C# `nameof` 키워드는 `CreatedAtAction` 호출에서 작업 이름의 하드 코딩을 방지하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-562">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="7f94a-563">PostTodoItem 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="7f94a-563">Test the PostTodoItem method</span></span>

* <span data-ttu-id="7f94a-564">프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-564">Build the project.</span></span>
* <span data-ttu-id="7f94a-565">Postman에서 HTTP 메서드를 `POST`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-565">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="7f94a-566">**본문** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-566">Select the **Body** tab.</span></span>
* <span data-ttu-id="7f94a-567">**원시** 라디오 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-567">Select the **raw** radio button.</span></span>
* <span data-ttu-id="7f94a-568">유형을 **JSON(application/json)** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-568">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="7f94a-569">요청 본문에서 할 일 항목에 대한 JSON을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-569">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="7f94a-570">**보내기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-570">Select **Send**.</span></span>

  ![생성 요청이 있는 Postman](first-web-api/_static/create.png)

  <span data-ttu-id="7f94a-572">405 메서드가 허용되지 않음 오류가 발생할 경우 `PostTodoItem` 메서드를 추가한 후에 프로젝트를 컴파일하지 않아서 발생한 결과일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-572">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="7f94a-573">위치 헤더 URI 테스트</span><span class="sxs-lookup"><span data-stu-id="7f94a-573">Test the location header URI</span></span>

* <span data-ttu-id="7f94a-574">**응답** 창에서 **헤더** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-574">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="7f94a-575">**위치** 헤더 값을 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-575">Copy the **Location** header value:</span></span>

  ![Postman 콘솔의 헤더 탭](first-web-api/_static/pmc2.png)

* <span data-ttu-id="7f94a-577">메서드를 GET으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-577">Set the method to GET.</span></span>
* <span data-ttu-id="7f94a-578">URI(예: `https://localhost:5001/api/Todo/2`) 붙여넣기</span><span class="sxs-lookup"><span data-stu-id="7f94a-578">Paste the URI (for example, `https://localhost:5001/api/Todo/2`)</span></span>
* <span data-ttu-id="7f94a-579">**보내기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-579">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="7f94a-580">PutTodoItem 메서드 추가</span><span class="sxs-lookup"><span data-stu-id="7f94a-580">Add a PutTodoItem method</span></span>

<span data-ttu-id="7f94a-581">다음 `PutTodoItem` 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-581">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="7f94a-582">HTTP PUT을 사용하는 것을 제외하고 `PutTodoItem`는 `PostTodoItem`와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-582">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="7f94a-583">응답은 [204(콘텐츠 없음)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-583">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="7f94a-584">HTTP 사양에 따라 PUT 요청의 경우 클라이언트는 변경 내용만이 아니라 전체 업데이트된 엔터티를 보내야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-584">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="7f94a-585">부분 업데이트를 지원하려면 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-585">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="7f94a-586">`PutTodoItem`을 호출하는 중 오류가 발생하면 `GET`을 호출하여 데이터베이스에 항목이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-586">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="7f94a-587">PutTodoItem 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="7f94a-587">Test the PutTodoItem method</span></span>

<span data-ttu-id="7f94a-588">이 샘플은 앱이 시작될 때마다 초기화되어야 하는 메모리 내 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-588">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="7f94a-589">PUT 호출을 실행하기 전에 데이터베이스에 항목이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-589">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="7f94a-590">GET을 호출하여 PUT 호출을 실행하기 전에 데이터베이스에 항목이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-590">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="7f94a-591">ID = 1인 할 일 항목을 업데이트하고 해당 이름을 "feed fish"로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-591">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="7f94a-592">다음 이미지는 Postman 업데이트를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-592">The following image shows the Postman update:</span></span>

![204(콘텐츠 없음) 응답을 보여주는 Postman 콘솔](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="7f94a-594">DeleteTodoItem 메서드 추가</span><span class="sxs-lookup"><span data-stu-id="7f94a-594">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="7f94a-595">다음 `DeleteTodoItem` 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-595">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="7f94a-596">`DeleteTodoItem` 응답은 [204(콘텐츠 없음)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-596">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="7f94a-597">DeleteTodoItem 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="7f94a-597">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="7f94a-598">Postman을 사용하여 할 일 항목을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-598">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="7f94a-599">메서드를 `DELETE`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-599">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="7f94a-600">예를 들어 삭제할 개체의 URI를 `https://localhost:5001/api/todo/1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-600">Set the URI of the object to delete, for example `https://localhost:5001/api/todo/1`</span></span>
* <span data-ttu-id="7f94a-601">**보내기** 선택</span><span class="sxs-lookup"><span data-stu-id="7f94a-601">Select **Send**</span></span>

<span data-ttu-id="7f94a-602">샘플 앱을 사용하면 모든 항목을 삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-602">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="7f94a-603">하지만 마지막 항목이 삭제되면 다음에 API를 호출하는 경우 모델 클래스 생성자에서 새로운 항목이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-603">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-api-with-jquery"></a><span data-ttu-id="7f94a-604">jQuery를 사용하여 API 호출</span><span class="sxs-lookup"><span data-stu-id="7f94a-604">Call the API with jQuery</span></span>

<span data-ttu-id="7f94a-605">이 섹션에서는 jQuery를 사용하여 웹 API를 호출하는 HTML 페이지가 추가되었습니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-605">In this section, an HTML page is added that uses jQuery to call the web api.</span></span> <span data-ttu-id="7f94a-606">jQuery는 요청을 시작하고 API 응답의 세부 정보로 페이지를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-606">jQuery initiates the request and updates the page with the details from the API's response.</span></span>

<span data-ttu-id="7f94a-607">다음 강조 표시된 코드로 *Startup.cs*를 업데이트하여 앱이 [정적 파일을 제공](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)하고 [기본 파일 매핑을 사용](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)하도록 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-607">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="7f94a-608">프로젝트 디렉터리에서 *wwwroot* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-608">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="7f94a-609">*index.html*이라는 HTML 파일을 *wwwroot* 디렉터리에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-609">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="7f94a-610">다음 표시로 콘텐츠를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-610">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="7f94a-611">*site.js*라는 JavaScript 파일을 *wwwroot* 디렉터리에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-611">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="7f94a-612">다음 코드로 콘텐츠를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-612">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="7f94a-613">HTML 페이지를 로컬에서 테스트하려면 ASP.NET Core 프로젝트의 시작 설정을 변경해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-613">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="7f94a-614">*Properties\launchSettings.json*을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-614">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="7f94a-615">`launchUrl` 속성을 제거하여 앱이 *index.html*&mdash; 프로젝트의 기본 파일에서 열리도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-615">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="7f94a-616">여러 가지 방법으로 jQuery를 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-616">There are several ways to get jQuery.</span></span> <span data-ttu-id="7f94a-617">위의 코드 조각에서 라이브러리는 CDN에서 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-617">In the preceding snippet, the library is loaded from a CDN.</span></span>

<span data-ttu-id="7f94a-618">이 샘플은 API의 모든 CRUD 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-618">This sample calls all of the CRUD methods of the API.</span></span> <span data-ttu-id="7f94a-619">API 호출에 대한 설명은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-619">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="7f94a-620">할 일 항목의 목록 가져오기</span><span class="sxs-lookup"><span data-stu-id="7f94a-620">Get a list of to-do items</span></span>

<span data-ttu-id="7f94a-621">jQuery [ajax](https://api.jquery.com/jquery.ajax/) 함수는 할 일 항목의 배열을 나타내는 JSON을 반환하는 API에 `GET` 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-621">The jQuery [ajax](https://api.jquery.com/jquery.ajax/) function sends a `GET` request to the API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="7f94a-622">요청이 성공하면 `success` 콜백 함수가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-622">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="7f94a-623">콜백에서 DOM은 할 일 정보로 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-623">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="7f94a-624">할 일 항목 추가</span><span class="sxs-lookup"><span data-stu-id="7f94a-624">Add a to-do item</span></span>

<span data-ttu-id="7f94a-625">[ajax](https://api.jquery.com/jquery.ajax/) 함수는 `POST` 요청 본문에서 할 일 항목을 사용하여 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-625">The [ajax](https://api.jquery.com/jquery.ajax/) function sends a `POST` request with the to-do item in the request body.</span></span> <span data-ttu-id="7f94a-626">`accepts` 및 `contentType` 옵션은 수신 및 전송되는 미디어 형식을 지정하기 위해 `application/json`으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-626">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="7f94a-627">[JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)를 사용하여 할 일 항목을 JSON으로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-627">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="7f94a-628">API가 성공적인 상태 코드를 반환하면 `getData` 함수가 호출되어 HTML 테이블을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-628">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="7f94a-629">할 일 항목 업데이트</span><span class="sxs-lookup"><span data-stu-id="7f94a-629">Update a to-do item</span></span>

<span data-ttu-id="7f94a-630">할 일 항목을 업데이트하는 작업은 추가하는 작업과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-630">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="7f94a-631">`url`은 항목의 고유 식별자를 추가하도록 변경되고 `type`은 `PUT`입니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-631">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="7f94a-632">할 일 항목 삭제</span><span class="sxs-lookup"><span data-stu-id="7f94a-632">Delete a to-do item</span></span>

<span data-ttu-id="7f94a-633">할 일 항목을 삭제하려면 AJAX 호출에서 `type`을 `DELETE`로 설정하고 URL에서 항목의 고유 식별자를 지정하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7f94a-633">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="7f94a-634">추가 자료</span><span class="sxs-lookup"><span data-stu-id="7f94a-634">Additional resources</span></span>

<span data-ttu-id="7f94a-635">[이 자습서에서 샘플 코드 보기 또는 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)</span><span class="sxs-lookup"><span data-stu-id="7f94a-635">[View or download sample code for this tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="7f94a-636">[다운로드하는 방법](xref:index#how-to-download-a-sample)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7f94a-636">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="7f94a-637">자세한 내용은 다음 리소스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7f94a-637">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/index>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="7f94a-638">이 자습서의 YouTube 버전</span><span class="sxs-lookup"><span data-stu-id="7f94a-638">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
