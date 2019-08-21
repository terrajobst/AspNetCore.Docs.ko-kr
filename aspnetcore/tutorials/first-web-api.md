---
title: '자습서: ASP.NET Core를 사용하여 웹 API 만들기'
author: rick-anderson
description: ASP.NET Core를 사용하여 웹 API를 빌드하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 08/14/2019
uid: tutorials/first-web-api
ms.openlocfilehash: 99985e9fb1134c2ba808434f8d24c4a768773268
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022601"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="d1101-103">자습서: ASP.NET Core를 사용하여 웹 API 만들기</span><span class="sxs-lookup"><span data-stu-id="d1101-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="d1101-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="d1101-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="d1101-105">이 자습서에서는 ASP.NET Core를 사용하여 웹 API를 빌드하는 작업의 기본 사항을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d1101-106">이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d1101-107">웹 API 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-107">Create a web API project.</span></span>
> * <span data-ttu-id="d1101-108">모델 클래스와 데이터베이스 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="d1101-109">CRUD 메서드로 컨트롤러를 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="d1101-110">라우팅, URL 경로 및 반환 값을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="d1101-111">Postman을 사용하여 웹 API 호출</span><span class="sxs-lookup"><span data-stu-id="d1101-111">Call the web API with Postman.</span></span>

<span data-ttu-id="d1101-112">작업을 완료하면 웹 API는 데이터베이스에 저장된 “할 일” 항목을 관리할 수 있게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="d1101-113">개요</span><span class="sxs-lookup"><span data-stu-id="d1101-113">Overview</span></span>

<span data-ttu-id="d1101-114">이 자습서에서는 다음 API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="d1101-115">API</span><span class="sxs-lookup"><span data-stu-id="d1101-115">API</span></span> | <span data-ttu-id="d1101-116">설명</span><span class="sxs-lookup"><span data-stu-id="d1101-116">Description</span></span> | <span data-ttu-id="d1101-117">요청 본문</span><span class="sxs-lookup"><span data-stu-id="d1101-117">Request body</span></span> | <span data-ttu-id="d1101-118">응답 본문</span><span class="sxs-lookup"><span data-stu-id="d1101-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="d1101-119">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="d1101-119">GET /api/TodoItems</span></span> | <span data-ttu-id="d1101-120">할 일 항목 모두 가져오기</span><span class="sxs-lookup"><span data-stu-id="d1101-120">Get all to-do items</span></span> | <span data-ttu-id="d1101-121">없음</span><span class="sxs-lookup"><span data-stu-id="d1101-121">None</span></span> | <span data-ttu-id="d1101-122">할 일 항목의 배열</span><span class="sxs-lookup"><span data-stu-id="d1101-122">Array of to-do items</span></span>|
|<span data-ttu-id="d1101-123">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="d1101-123">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="d1101-124">ID로 항목 가져오기</span><span class="sxs-lookup"><span data-stu-id="d1101-124">Get an item by ID</span></span> | <span data-ttu-id="d1101-125">없음</span><span class="sxs-lookup"><span data-stu-id="d1101-125">None</span></span> | <span data-ttu-id="d1101-126">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="d1101-126">To-do item</span></span>|
|<span data-ttu-id="d1101-127">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="d1101-127">POST /api/TodoItems</span></span> | <span data-ttu-id="d1101-128">새 항목 추가</span><span class="sxs-lookup"><span data-stu-id="d1101-128">Add a new item</span></span> | <span data-ttu-id="d1101-129">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="d1101-129">To-do item</span></span> | <span data-ttu-id="d1101-130">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="d1101-130">To-do item</span></span> |
|<span data-ttu-id="d1101-131">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="d1101-131">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="d1101-132">기존 항목 업데이트 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="d1101-132">Update an existing item &nbsp;</span></span> | <span data-ttu-id="d1101-133">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="d1101-133">To-do item</span></span> | <span data-ttu-id="d1101-134">없음</span><span class="sxs-lookup"><span data-stu-id="d1101-134">None</span></span> |
|<span data-ttu-id="d1101-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="d1101-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="d1101-136">항목 삭제 &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="d1101-136">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="d1101-137">없음</span><span class="sxs-lookup"><span data-stu-id="d1101-137">None</span></span> | <span data-ttu-id="d1101-138">없음</span><span class="sxs-lookup"><span data-stu-id="d1101-138">None</span></span>|

<span data-ttu-id="d1101-139">다음 다이어그램에서는 앱의 디자인을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-139">The following diagram shows the design of the app.</span></span>

![클라이언트는 왼쪽에 상자로 표시됩니다.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="d1101-145">전제 조건</span><span class="sxs-lookup"><span data-stu-id="d1101-145">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d1101-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d1101-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d1101-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d1101-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d1101-148">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d1101-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="d1101-149">웹 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="d1101-149">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d1101-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d1101-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d1101-151">**파일** 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="d1101-152">**ASP.NET Core 웹 애플리케이션** 템플릿을 선택하고 **다음**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="d1101-153">프로젝트 이름을 *TodoApi*로 지정하고 **만들기**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-153">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="d1101-154">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **.NET Core** 및 **ASP.NET Core 3.0**이 선택되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span> <span data-ttu-id="d1101-155">**API** 템플릿을 선택하고 **만들기**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-155">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="d1101-156">**Docker 지원 사용**을 선택하지 **마세요**.</span><span class="sxs-lookup"><span data-stu-id="d1101-156">**Don't** select **Enable Docker Support**.</span></span>

![VS 새 프로젝트 대화 상자](first-web-api/_static/vs3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d1101-158">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d1101-158">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d1101-159">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-159">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="d1101-160">디렉터리(`cd`)를 프로젝트 폴더를 포함하는 폴더로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-160">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="d1101-161">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-161">Run the following commands:</span></span>

   ```console
   dotnet new webapi -o TodoApi
   cd TodoAPI
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
   dotnet add package Microsoft.EntityFrameworkCore.InMemory --version 3.0.0-*
   code -r ../TodoApi
   ```

* <span data-ttu-id="d1101-162">프로젝트에 필수 자산을 추가하려는지 묻는 대화 상자가 나타나면 **예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-162">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="d1101-163">이전 명령은:</span><span class="sxs-lookup"><span data-stu-id="d1101-163">The preceding commands:</span></span>

  * <span data-ttu-id="d1101-164">새 웹 API 프로젝트를 만들고 Visual Studio Code에서 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-164">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="d1101-165">다음 섹션에서 필요한 NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-165">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d1101-166">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d1101-166">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d1101-167">**파일** > **새 솔루션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-167">Select **File** > **New Solution**.</span></span>

  ![macOS 새 솔루션](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="d1101-169">**.NET Core** > **앱** > **API** > **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-169">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS 새 프로젝트 대화 상자](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="d1101-171">**새 ASP.NET Core Web API 구성** 대화 상자에서 \* *.NET Core 3.0*의 **대상 프레임워크**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-171">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.0*.</span></span>

* <span data-ttu-id="d1101-172">**프로젝트 이름**으로 *TodoApi*를 입력한 다음, **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-172">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![구성 대화 상자](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="d1101-174">프로젝트 폴더에서 명령 터미널을 열고 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-174">Open a command terminal in the project folder and run the following commands:</span></span>

   ```console
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.0.0-*
   dotnet add package Microsoft.EntityFrameworkCore.InMemory --version 3.0.0-*
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="d1101-175">API 테스트</span><span class="sxs-lookup"><span data-stu-id="d1101-175">Test the API</span></span>

<span data-ttu-id="d1101-176">프로젝트 템플릿은 `WeatherForecast` API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-176">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="d1101-177">브라우저에서 `Get` 메서드를 호출하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-177">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d1101-178">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d1101-178">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d1101-179">Ctrl+F5 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-179">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="d1101-180">Visual Studio가 브라우저를 시작하고 `https://localhost:<port>/WeatherForecast`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-180">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="d1101-181">IIS Express 인증서를 신뢰해야 하는지 묻는 대화 상자가 표시되면 **예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-181">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="d1101-182">다음으로, **보안 경고** 대화 상자가 나타나면 **예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-182">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d1101-183">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d1101-183">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d1101-184">Ctrl+F5 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-184">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="d1101-185">브라우저에서 다음 URL [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast)로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-185">In a browser, go to following URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d1101-186">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d1101-186">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d1101-187">**실행** > **디버깅 시작**을 선택하여 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-187">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="d1101-188">Mac용 Visual Studio가 브라우저를 시작하고 `https://localhost:<port>`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-188">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="d1101-189">HTTP 404(찾을 수 없음) 오류가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-189">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="d1101-190">`/WeatherForecast`를 URL에 추가합니다(URL을 `https://localhost:<port>/WeatherForecast`로 변경).</span><span class="sxs-lookup"><span data-stu-id="d1101-190">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="d1101-191">다음과 비슷한 JSON이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-191">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="d1101-192">모델 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="d1101-192">Add a model class</span></span>

<span data-ttu-id="d1101-193">*모델*은 앱에서 관리하는 데이터를 나타내는 일련의 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-193">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="d1101-194">이 앱에 대한 모델은 단일 `TodoItem` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-194">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d1101-195">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d1101-195">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d1101-196">**솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-196">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="d1101-197">**추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-197">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="d1101-198">폴더 이름을 *Models*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-198">Name the folder *Models*.</span></span>

* <span data-ttu-id="d1101-199">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-199">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="d1101-200">클래스 이름을 *TodoItem*으로 지정하고 **추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-200">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="d1101-201">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-201">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d1101-202">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d1101-202">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d1101-203">*Models* 폴더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-203">Add a folder named *Models*.</span></span>

* <span data-ttu-id="d1101-204">다음 코드를 사용하여 *Models* 폴더에 `TodoItem` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-204">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d1101-205">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d1101-205">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d1101-206">프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-206">Right-click the project.</span></span> <span data-ttu-id="d1101-207">**추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-207">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="d1101-208">폴더 이름을 *Models*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-208">Name the folder *Models*.</span></span>

  ![새 폴더](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="d1101-210">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 파일** > **일반** > **빈 클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-210">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="d1101-211">클래스 이름을 *TodoItem*으로 지정한 다음, **새로 만들기**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-211">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="d1101-212">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-212">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="d1101-213">`Id` 속성은 관계형 데이터베이스에서 고유 키로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-213">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="d1101-214">모델 클래스는 프로젝트의 어디로든 이동할 수 있지만 규칙에 따라 *Models* 폴더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-214">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="d1101-215">데이터베이스 컨텍스트 추가</span><span class="sxs-lookup"><span data-stu-id="d1101-215">Add a database context</span></span>

<span data-ttu-id="d1101-216">*데이터베이스 컨텍스트*는 데이터 모델에 맞게 Entity Framework 기능을 조정하는 주 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-216">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="d1101-217">`Microsoft.EntityFrameworkCore.DbContext` 클래스에서 파생시키는 방식으로 이 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-217">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d1101-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d1101-218">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="d1101-219">Microsoft.EntityFrameworkCore.SqlServer 추가</span><span class="sxs-lookup"><span data-stu-id="d1101-219">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="d1101-220">**도구** 메뉴에서 **NuGet 패키지 관리자 > 솔루션용 NuGet 패키지 관리**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-220">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="d1101-221">**시험판 포함** 확인란을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-221">Select the **Include prerelease** checkbox.</span></span>
* <span data-ttu-id="d1101-222">**찾아보기** 탭을 선택한 다음 검색 상자에 **Microsoft.EntityFrameworkCore.SqlServer**를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-222">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="d1101-223">왼쪽 창에서 **Microsoft.EntityFrameworkCore.SqlServer V3.0.0-preview**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-223">Select  **Microsoft.EntityFrameworkCore.SqlServer V3.0.0-preview** in the left pane.</span></span>
* <span data-ttu-id="d1101-224">오른쪽 창에서 **프로젝트** 확인란을 선택하고 **설치**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-224">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="d1101-225">위 지침에 따라 `Microsoft.EntityFrameworkCore.InMemory` NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-225">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![NuGet 패키지 관리자](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="d1101-227">TodoContext 데이터베이스 컨텍스트 추가</span><span class="sxs-lookup"><span data-stu-id="d1101-227">Add the TodoContext database context</span></span>

* <span data-ttu-id="d1101-228">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-228">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="d1101-229">클래스 이름을 *TodoContext*로 지정하고 **추가**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-229">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="d1101-230">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d1101-230">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="d1101-231">`TodoContext` 클래스를 *Models* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-231">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="d1101-232">다음 코드를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-232">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="d1101-233">데이터베이스 컨텍스트 등록</span><span class="sxs-lookup"><span data-stu-id="d1101-233">Register the database context</span></span>

<span data-ttu-id="d1101-234">ASP.NET Core에서는 DB 컨텍스트와 같은 서비스를 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 컨테이너에 등록해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-234">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="d1101-235">컨테이너는 컨트롤러에 서비스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-235">The container provides the service to controllers.</span></span>

<span data-ttu-id="d1101-236">*Startup.cs*를 다음 강조 표시된 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-236">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="d1101-237">위의 코드는:</span><span class="sxs-lookup"><span data-stu-id="d1101-237">The preceding code:</span></span>

* <span data-ttu-id="d1101-238">사용되지 않는 `using` 선언을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-238">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="d1101-239">DI 컨테이너에 데이터베이스 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-239">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="d1101-240">데이터베이스 컨텍스트가 메모리 내 데이터베이스를 사용하도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-240">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="d1101-241">컨트롤러 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="d1101-241">Scaffold a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d1101-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d1101-242">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d1101-243">*Controllers* 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-243">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="d1101-244">**추가** > **스캐폴드 항목 새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-244">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="d1101-245">**Entity Framework를 사용하며 동작이 포함된 API 컨트롤러**를 선택하고 **추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-245">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="d1101-246">**Entity Framework를 사용하며 동작이 포함된 API 컨트롤러 추가** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="d1101-246">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="d1101-247">**모델 클래스**에서 **TodoItem (TodoAPI.Models)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-247">Select **TodoItem (TodoAPI.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="d1101-248">**데이터 컨텍스트 클래스**에서 **TodoContext (TodoAPI.Models)** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-248">Select **TodoContext (TodoAPI.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="d1101-249">**추가** 선택</span><span class="sxs-lookup"><span data-stu-id="d1101-249">Select **Add**</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="d1101-250">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d1101-250">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="d1101-251">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-251">Run the following commands:</span></span>

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.0.0-*
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext  -outDir Controllers
```

<span data-ttu-id="d1101-252">이전 명령은:</span><span class="sxs-lookup"><span data-stu-id="d1101-252">The preceding commands:</span></span>

* <span data-ttu-id="d1101-253">스캐폴딩에 필요한 NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-253">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="d1101-254">스캐폴딩 엔진(`dotnet-aspnet-codegenerator`)을 설치합니다</span><span class="sxs-lookup"><span data-stu-id="d1101-254">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="d1101-255">`TodoItemsController`를 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-255">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="d1101-256">생성된 코드는:</span><span class="sxs-lookup"><span data-stu-id="d1101-256">The generated code:</span></span>

* <span data-ttu-id="d1101-257">메서드 없이 API 컨트롤러 클래스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-257">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="d1101-258">[[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 특성을 사용하여 클래스를 데코레이팅합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-258">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="d1101-259">이 특성은 컨트롤러가 웹 API 요청에 응답함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-259">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="d1101-260">특성을 사용하도록 설정하는 특정 동작에 대한 정보는 <xref:web-api/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d1101-260">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="d1101-261">DI를 사용하여 데이터베이스 컨텍스트(`TodoContext`)를 컨트롤러에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-261">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="d1101-262">컨트롤러의 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드 각각에서 데이터베이스 컨텍스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-262">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="d1101-263">PostTodoItem 만들기 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-263">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="d1101-264">`PostTodoItem`의 return 문이 [nameof](/dotnet/csharp/language-reference/operators/nameof) 연산자를 사용하도록 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-264">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="d1101-265">이전 코드는 [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 특성으로 표시되는 HTTP POST 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-265">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="d1101-266">이 메서드는 HTTP 요청 본문에서 할 일 항목 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-266">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="d1101-267"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-267">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="d1101-268">성공 시 HTTP 201 상태 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-268">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="d1101-269">HTTP 201은 서버에서 새 리소스를 만드는 HTTP POST 메서드의 표준 응답입니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-269">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="d1101-270">응답에 대한 [위치](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-270">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="d1101-271">`Location` 헤더는 새로 만들어진 할 일 항목의 [URI](https://developer.mozilla.org/docs/Glossary/URI)를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-271">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="d1101-272">자세한 내용은 [10.2.2 201 생성됨](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d1101-272">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="d1101-273">`Location` 헤더의 URI를 만들려면 `GetTodoItem` 작업을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-273">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="d1101-274">C# `nameof` 키워드는 `CreatedAtAction` 호출에서 작업 이름의 하드 코딩을 방지하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-274">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="d1101-275">Postman 설치</span><span class="sxs-lookup"><span data-stu-id="d1101-275">Install Postman</span></span>

<span data-ttu-id="d1101-276">이 자습서에서는 Postman을 사용하여 웹 API를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-276">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="d1101-277">[Postman](https://www.getpostman.com/downloads/) 설치</span><span class="sxs-lookup"><span data-stu-id="d1101-277">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="d1101-278">웹앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-278">Start the web app.</span></span>
* <span data-ttu-id="d1101-279">Postman을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-279">Start Postman.</span></span>
* <span data-ttu-id="d1101-280">**SSL 인증서 확인** 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="d1101-280">Disable **SSL certificate verification**</span></span>
* <span data-ttu-id="d1101-281">**파일 > 설정**(\**일반* 탭)에서 **SSL 인증서 확인**을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-281">From  **File > Settings** (\**General* tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="d1101-282">컨트롤러를 테스트한 후에 SSL 인증서 확인을 다시 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-282">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="d1101-283">Postman을 사용하여 PostTodoItem 테스트</span><span class="sxs-lookup"><span data-stu-id="d1101-283">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="d1101-284">새 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-284">Create a new request.</span></span>
* <span data-ttu-id="d1101-285">HTTP 메서드를 `POST`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-285">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="d1101-286">**본문** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-286">Select the **Body** tab.</span></span>
* <span data-ttu-id="d1101-287">**원시** 라디오 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-287">Select the **raw** radio button.</span></span>
* <span data-ttu-id="d1101-288">유형을 **JSON(application/json)** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-288">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="d1101-289">요청 본문에서 할 일 항목에 대한 JSON을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-289">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="d1101-290">**보내기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-290">Select **Send**.</span></span>

  ![생성 요청이 있는 Postman](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="d1101-292">위치 헤더 URI 테스트</span><span class="sxs-lookup"><span data-stu-id="d1101-292">Test the location header URI</span></span>

* <span data-ttu-id="d1101-293">**응답** 창에서 **헤더** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-293">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="d1101-294">**위치** 헤더 값을 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-294">Copy the **Location** header value:</span></span>

  ![Postman 콘솔의 헤더 탭](first-web-api/_static/3/create.png)

* <span data-ttu-id="d1101-296">메서드를 GET으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-296">Set the method to GET.</span></span>
* <span data-ttu-id="d1101-297">URI(예: `https://localhost:5001/api/TodoItems/1`) 붙여넣기</span><span class="sxs-lookup"><span data-stu-id="d1101-297">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`)</span></span>
* <span data-ttu-id="d1101-298">**보내기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-298">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="d1101-299">GET 메서드 검사</span><span class="sxs-lookup"><span data-stu-id="d1101-299">Examine the GET methods</span></span>

<span data-ttu-id="d1101-300">다음과 같은 메서드는 두 개의 GET 엔드포인트를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-300">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="d1101-301">브라우저 또는 Postman에서 두 개의 엔드포인트를 호출하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-301">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="d1101-302">예:</span><span class="sxs-lookup"><span data-stu-id="d1101-302">For example:</span></span>

* [https://localhost:5001/api/TodoItems](https://localhost:5001/api/TodoItems)
* [https://localhost:5001/api/TodoItems/1](https://localhost:5001/api/TodoItems/1)

<span data-ttu-id="d1101-303">`GetTodoItems`를 호출하면 다음과 비슷한 응답이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-303">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="d1101-304">Postman을 사용하여 Get 테스트</span><span class="sxs-lookup"><span data-stu-id="d1101-304">Test Get with Postman</span></span>

* <span data-ttu-id="d1101-305">새 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-305">Create a new request.</span></span>
* <span data-ttu-id="d1101-306">HTTP 메서드를 **GET**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-306">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="d1101-307">요청 URL을 `https://localhost:<port>/api/TodoItems`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-307">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="d1101-308">예를 들어, `https://localhost:5001/api/TodoItems`을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-308">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="d1101-309">Postman에서 **두 개의 창 보기**를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-309">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="d1101-310">**보내기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-310">Select **Send**.</span></span>

<span data-ttu-id="d1101-311">이 앱은 메모리 내 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-311">This app uses an in-memory database.</span></span> <span data-ttu-id="d1101-312">앱이 중지된 후 시작되면 이전 GET 요청이 데이터를 반환하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-312">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="d1101-313">데이터가 반환되지 않으면 앱에 데이터를 [POST](#post)합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-313">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="d1101-314">라우팅 및 URL 경로</span><span class="sxs-lookup"><span data-stu-id="d1101-314">Routing and URL paths</span></span>

<span data-ttu-id="d1101-315">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 특성은 HTTP GET 요청에 응답하는 메서드를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-315">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="d1101-316">각 방법에 대한 URL 경로는 다음과 같이 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-316">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="d1101-317">컨트롤러의 `Route` 특성에서 템플릿 문자열로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-317">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="d1101-318">`[controller]`를 컨트롤러의 이름으로 바꿉니다. 일반적으로 컨트롤러 클래스 이름에서 "Controller" 접미사를 뺀 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-318">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="d1101-319">이 샘플의 경우 컨트롤러 클래스 이름은 **TodoItems**Controller이므로 컨트롤러 이름은 “TodoItems”입니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-319">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="d1101-320">ASP.NET Core [라우팅](xref:mvc/controllers/routing)은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-320">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="d1101-321">`[HttpGet]` 특성에 경로 템플릿(예: `[HttpGet("products")]`)이 있는 경우 경로에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-321">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="d1101-322">이 샘플은 템플릿을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-322">This sample doesn't use a template.</span></span> <span data-ttu-id="d1101-323">자세한 내용은 [Http[동사] 특성을 사용한 특성 라우팅](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d1101-323">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="d1101-324">다음 `GetTodoItem` 메서드에서 `"{id}"`는 할 일 항목의 고유 식별자에 대한 자리 표시자 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-324">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="d1101-325">`GetTodoItem`가 호출되면 URL의 `"{id}"` 값을 `id` 매개 변수의 메서드에 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-325">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="d1101-326">반환 값</span><span class="sxs-lookup"><span data-stu-id="d1101-326">Return values</span></span>

<span data-ttu-id="d1101-327">`GetTodoItems` 및 `GetTodoItem` 메서드의 반환 형식은 [ActionResult\<T> 형식](xref:web-api/action-return-types#actionresultt-type)입니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-327">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="d1101-328">ASP.NET Core는 자동으로 [JSON](https://www.json.org/)에 개체를 직렬화하고 JSON을 응답 메시지의 본문에 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-328">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="d1101-329">이 반환 형식의 응답 코드는 200이며 처리되지 않은 예외가 없다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-329">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="d1101-330">처리되지 않은 예외는 5xx 오류로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-330">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="d1101-331">`ActionResult` 반환 형식은 다양한 HTTP 상태 코드를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-331">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="d1101-332">예를 들어 `GetTodoItem`은 두 가지 상태 값을 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-332">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="d1101-333">요청된 ID와 일치하는 항목이 없는 경우 메서드가 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) 오류 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-333">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="d1101-334">그렇지 않으면 메서드가 JSON 응답 본문에서 200을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-334">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="d1101-335">`item`을 반환하면 HTTP 200 응답이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-335">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="d1101-336">PutTodoItem 메서드</span><span class="sxs-lookup"><span data-stu-id="d1101-336">The PutTodoItem method</span></span>

<span data-ttu-id="d1101-337">다음과 같이 `PutTodoItem` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-337">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="d1101-338">HTTP PUT을 사용하는 것을 제외하고 `PutTodoItem`는 `PostTodoItem`와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-338">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="d1101-339">응답은 [204(콘텐츠 없음)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-339">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="d1101-340">HTTP 사양에 따라 PUT 요청의 경우 클라이언트는 변경 내용만이 아니라 전체 업데이트된 엔터티를 보내야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-340">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="d1101-341">부분 업데이트를 지원하려면 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-341">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="d1101-342">`PutTodoItem`을 호출하는 중 오류가 발생하면 `GET`을 호출하여 데이터베이스에 항목이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-342">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="d1101-343">PutTodoItem 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="d1101-343">Test the PutTodoItem method</span></span>

<span data-ttu-id="d1101-344">이 샘플은 앱이 시작될 때마다 초기화되어야 하는 메모리 내 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-344">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="d1101-345">PUT 호출을 실행하기 전에 데이터베이스에 항목이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-345">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="d1101-346">GET을 호출하여 PUT 호출을 실행하기 전에 데이터베이스에 항목이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-346">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="d1101-347">ID = 1인 할 일 항목을 업데이트하고 해당 이름을 “feed fish”로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-347">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="d1101-348">다음 이미지는 Postman 업데이트를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-348">The following image shows the Postman update:</span></span>

![204(콘텐츠 없음) 응답을 보여주는 Postman 콘솔](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="d1101-350">DeleteTodoItem 메서드</span><span class="sxs-lookup"><span data-stu-id="d1101-350">The DeleteTodoItem method</span></span>

<span data-ttu-id="d1101-351">다음과 같이 `DeleteTodoItem` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-351">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

<span data-ttu-id="d1101-352">`DeleteTodoItem` 응답은 [204(콘텐츠 없음)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-352">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="d1101-353">DeleteTodoItem 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="d1101-353">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="d1101-354">Postman을 사용하여 할 일 항목을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-354">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="d1101-355">메서드를 `DELETE`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-355">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="d1101-356">예를 들어 삭제할 개체의 URI를 `https://localhost:5001/api/TodoItems/1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-356">Set the URI of the object to delete, for example `https://localhost:5001/api/TodoItems/1`</span></span>
* <span data-ttu-id="d1101-357">**보내기** 선택</span><span class="sxs-lookup"><span data-stu-id="d1101-357">Select **Send**</span></span>

## <a name="call-the-api-from-jquery"></a><span data-ttu-id="d1101-358">jQuery에서 API 호출</span><span class="sxs-lookup"><span data-stu-id="d1101-358">Call the API from jQuery</span></span>

<span data-ttu-id="d1101-359">단계별 지침은 [자습서: jQuery로 ASP.NET Core 웹 API 호출하기](xref:tutorials/web-api-jquery)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d1101-359">See [Tutorial: Call an ASP.NET Core web API with jQuery](xref:tutorials/web-api-jquery).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d1101-360">이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-360">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d1101-361">웹 API 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-361">Create a web API project.</span></span>
> * <span data-ttu-id="d1101-362">모델 클래스와 데이터베이스 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-362">Add a model class and a database context.</span></span>
> * <span data-ttu-id="d1101-363">컨트롤러 추가</span><span class="sxs-lookup"><span data-stu-id="d1101-363">Add a controller.</span></span>
> * <span data-ttu-id="d1101-364">CRUD 메서드 추가</span><span class="sxs-lookup"><span data-stu-id="d1101-364">Add CRUD methods.</span></span>
> * <span data-ttu-id="d1101-365">라우팅 및 URL 경로 구성</span><span class="sxs-lookup"><span data-stu-id="d1101-365">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="d1101-366">반환 값 지정</span><span class="sxs-lookup"><span data-stu-id="d1101-366">Specify return values.</span></span>
> * <span data-ttu-id="d1101-367">Postman을 사용하여 웹 API 호출</span><span class="sxs-lookup"><span data-stu-id="d1101-367">Call the web API with Postman.</span></span>
> * <span data-ttu-id="d1101-368">jQuery를 사용하여 웹 API를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-368">Call the web API with jQuery.</span></span>

<span data-ttu-id="d1101-369">작업을 완료하면 웹 API는 관계형 데이터베이스에 저장된 "할 일" 항목을 관리할 수 있게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-369">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>
## <a name="overview"></a><span data-ttu-id="d1101-370">개요</span><span class="sxs-lookup"><span data-stu-id="d1101-370">Overview</span></span>

<span data-ttu-id="d1101-371">이 자습서에서는 다음 API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-371">This tutorial creates the following API:</span></span>

|<span data-ttu-id="d1101-372">API</span><span class="sxs-lookup"><span data-stu-id="d1101-372">API</span></span> | <span data-ttu-id="d1101-373">설명</span><span class="sxs-lookup"><span data-stu-id="d1101-373">Description</span></span> | <span data-ttu-id="d1101-374">요청 본문</span><span class="sxs-lookup"><span data-stu-id="d1101-374">Request body</span></span> | <span data-ttu-id="d1101-375">응답 본문</span><span class="sxs-lookup"><span data-stu-id="d1101-375">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="d1101-376">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="d1101-376">GET /api/TodoItems</span></span> | <span data-ttu-id="d1101-377">할 일 항목 모두 가져오기</span><span class="sxs-lookup"><span data-stu-id="d1101-377">Get all to-do items</span></span> | <span data-ttu-id="d1101-378">없음</span><span class="sxs-lookup"><span data-stu-id="d1101-378">None</span></span> | <span data-ttu-id="d1101-379">할 일 항목의 배열</span><span class="sxs-lookup"><span data-stu-id="d1101-379">Array of to-do items</span></span>|
|<span data-ttu-id="d1101-380">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="d1101-380">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="d1101-381">ID로 항목 가져오기</span><span class="sxs-lookup"><span data-stu-id="d1101-381">Get an item by ID</span></span> | <span data-ttu-id="d1101-382">없음</span><span class="sxs-lookup"><span data-stu-id="d1101-382">None</span></span> | <span data-ttu-id="d1101-383">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="d1101-383">To-do item</span></span>|
|<span data-ttu-id="d1101-384">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="d1101-384">POST /api/TodoItems</span></span> | <span data-ttu-id="d1101-385">새 항목 추가</span><span class="sxs-lookup"><span data-stu-id="d1101-385">Add a new item</span></span> | <span data-ttu-id="d1101-386">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="d1101-386">To-do item</span></span> | <span data-ttu-id="d1101-387">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="d1101-387">To-do item</span></span> |
|<span data-ttu-id="d1101-388">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="d1101-388">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="d1101-389">기존 항목 업데이트 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="d1101-389">Update an existing item &nbsp;</span></span> | <span data-ttu-id="d1101-390">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="d1101-390">To-do item</span></span> | <span data-ttu-id="d1101-391">없음</span><span class="sxs-lookup"><span data-stu-id="d1101-391">None</span></span> |
|<span data-ttu-id="d1101-392">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="d1101-392">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="d1101-393">항목 삭제 &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="d1101-393">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="d1101-394">없음</span><span class="sxs-lookup"><span data-stu-id="d1101-394">None</span></span> | <span data-ttu-id="d1101-395">없음</span><span class="sxs-lookup"><span data-stu-id="d1101-395">None</span></span>|

<span data-ttu-id="d1101-396">다음 다이어그램에서는 앱의 디자인을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-396">The following diagram shows the design of the app.</span></span>

![클라이언트는 왼쪽에 상자로 표시됩니다.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="d1101-402">전제 조건</span><span class="sxs-lookup"><span data-stu-id="d1101-402">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d1101-403">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d1101-403">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d1101-404">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d1101-404">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d1101-405">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d1101-405">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="d1101-406">웹 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="d1101-406">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d1101-407">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d1101-407">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d1101-408">**파일** 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-408">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="d1101-409">**ASP.NET Core 웹 애플리케이션** 템플릿을 선택하고 **다음**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-409">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="d1101-410">프로젝트 이름을 *TodoApi*로 지정하고 **만들기**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-410">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="d1101-411">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **.NET Core** 및 **ASP.NET Core 2.2**가 선택되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-411">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="d1101-412">**API** 템플릿을 선택하고 **만들기**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-412">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="d1101-413">**Docker 지원 사용**을 선택하지 **마세요**.</span><span class="sxs-lookup"><span data-stu-id="d1101-413">**Don't** select **Enable Docker Support**.</span></span>

![VS 새 프로젝트 대화 상자](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d1101-415">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d1101-415">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d1101-416">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-416">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="d1101-417">디렉터리(`cd`)를 프로젝트 폴더를 포함하는 폴더로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-417">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="d1101-418">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-418">Run the following commands:</span></span>

   ```console
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="d1101-419">이러한 명령은 새 웹 API 프로젝트를 만들고 새 프로젝트 폴더에서 Visual Studio Code의 새 인스턴스를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-419">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="d1101-420">프로젝트에 필수 자산을 추가하려는지 묻는 대화 상자가 나타나면 **예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-420">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d1101-421">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d1101-421">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d1101-422">**파일** > **새 솔루션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-422">Select **File** > **New Solution**.</span></span>

  ![macOS 새 솔루션](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="d1101-424">**.NET Core** > **앱** > **API** > **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-424">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS 새 프로젝트 대화 상자](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="d1101-426">**새 ASP.NET Core Web API 구성** 대화 상자에서 \* *.NET Core 2.2*라는 기본 **대상 프레임워크**를 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-426">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="d1101-427">**프로젝트 이름**으로 *TodoApi*를 입력한 다음, **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-427">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![구성 대화 상자](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="d1101-429">API 테스트</span><span class="sxs-lookup"><span data-stu-id="d1101-429">Test the API</span></span>

<span data-ttu-id="d1101-430">프로젝트 템플릿은 `values` API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-430">The project template creates a `values` API.</span></span> <span data-ttu-id="d1101-431">브라우저에서 `Get` 메서드를 호출하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-431">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d1101-432">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d1101-432">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d1101-433">Ctrl+F5 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-433">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="d1101-434">Visual Studio가 브라우저를 시작하고 `https://localhost:<port>/api/values`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-434">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="d1101-435">IIS Express 인증서를 신뢰해야 하는지 묻는 대화 상자가 표시되면 **예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-435">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="d1101-436">다음으로, **보안 경고** 대화 상자가 나타나면 **예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-436">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d1101-437">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d1101-437">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d1101-438">Ctrl+F5 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-438">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="d1101-439">브라우저에서 다음 URL [https://localhost:5001/api/values](https://localhost:5001/api/values)로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-439">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d1101-440">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d1101-440">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d1101-441">**실행** > **디버깅 시작**을 선택하여 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-441">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="d1101-442">Mac용 Visual Studio가 브라우저를 시작하고 `https://localhost:<port>`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-442">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="d1101-443">HTTP 404(찾을 수 없음) 오류가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-443">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="d1101-444">`/api/values`를 URL에 추가합니다(URL을 `https://localhost:<port>/api/values`로 변경).</span><span class="sxs-lookup"><span data-stu-id="d1101-444">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="d1101-445">다음 JSON이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-445">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="d1101-446">모델 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="d1101-446">Add a model class</span></span>

<span data-ttu-id="d1101-447">*모델*은 앱에서 관리하는 데이터를 나타내는 일련의 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-447">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="d1101-448">이 앱에 대한 모델은 단일 `TodoItem` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-448">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d1101-449">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d1101-449">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d1101-450">**솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-450">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="d1101-451">**추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-451">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="d1101-452">폴더 이름을 *Models*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-452">Name the folder *Models*.</span></span>

* <span data-ttu-id="d1101-453">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-453">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="d1101-454">클래스 이름을 *TodoItem*으로 지정하고 **추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-454">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="d1101-455">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-455">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d1101-456">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d1101-456">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="d1101-457">*Models* 폴더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-457">Add a folder named *Models*.</span></span>

* <span data-ttu-id="d1101-458">다음 코드를 사용하여 *Models* 폴더에 `TodoItem` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-458">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="d1101-459">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d1101-459">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d1101-460">프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-460">Right-click the project.</span></span> <span data-ttu-id="d1101-461">**추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-461">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="d1101-462">폴더 이름을 *Models*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-462">Name the folder *Models*.</span></span>

  ![새 폴더](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="d1101-464">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 파일** > **일반** > **빈 클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-464">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="d1101-465">클래스 이름을 *TodoItem*으로 지정한 다음, **새로 만들기**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-465">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="d1101-466">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-466">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="d1101-467">`Id` 속성은 관계형 데이터베이스에서 고유 키로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-467">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="d1101-468">모델 클래스는 프로젝트의 어디로든 이동할 수 있지만 규칙에 따라 *Models* 폴더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-468">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="d1101-469">데이터베이스 컨텍스트 추가</span><span class="sxs-lookup"><span data-stu-id="d1101-469">Add a database context</span></span>

<span data-ttu-id="d1101-470">*데이터베이스 컨텍스트*는 데이터 모델에 맞게 Entity Framework 기능을 조정하는 주 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-470">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="d1101-471">`Microsoft.EntityFrameworkCore.DbContext` 클래스에서 파생시키는 방식으로 이 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-471">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d1101-472">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d1101-472">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d1101-473">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-473">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="d1101-474">클래스 이름을 *TodoContext*로 지정하고 **추가**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-474">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="d1101-475">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d1101-475">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="d1101-476">`TodoContext` 클래스를 *Models* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-476">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="d1101-477">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-477">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="d1101-478">데이터베이스 컨텍스트 등록</span><span class="sxs-lookup"><span data-stu-id="d1101-478">Register the database context</span></span>

<span data-ttu-id="d1101-479">ASP.NET Core에서는 DB 컨텍스트와 같은 서비스를 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 컨테이너에 등록해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-479">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="d1101-480">컨테이너는 컨트롤러에 서비스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-480">The container provides the service to controllers.</span></span>

<span data-ttu-id="d1101-481">*Startup.cs*를 다음 강조 표시된 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-481">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="d1101-482">위의 코드는:</span><span class="sxs-lookup"><span data-stu-id="d1101-482">The preceding code:</span></span>

* <span data-ttu-id="d1101-483">사용되지 않는 `using` 선언을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-483">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="d1101-484">DI 컨테이너에 데이터베이스 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-484">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="d1101-485">데이터베이스 컨텍스트가 메모리 내 데이터베이스를 사용하도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-485">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="d1101-486">컨트롤러 추가</span><span class="sxs-lookup"><span data-stu-id="d1101-486">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d1101-487">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d1101-487">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d1101-488">*Controllers* 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-488">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="d1101-489">**추가** > **새 항목**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-489">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="d1101-490">**새 항목 추가** 대화 상자에서 **API 컨트롤러 클래스** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-490">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="d1101-491">클래스 이름을 *TodoController*로 지정하고 **추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-491">Name the class *TodoController*, and select **Add**.</span></span>

  ![검색 상자의 컨트롤러 및 웹 API 컨트롤러가 선택된 새 항목 추가 대화 상자](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="d1101-493">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d1101-493">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="d1101-494">*Controllers* 폴더에 `TodoController`라는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-494">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="d1101-495">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-495">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="d1101-496">위의 코드는:</span><span class="sxs-lookup"><span data-stu-id="d1101-496">The preceding code:</span></span>

* <span data-ttu-id="d1101-497">메서드 없이 API 컨트롤러 클래스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-497">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="d1101-498">[[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 특성을 사용하여 클래스를 데코레이팅합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-498">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="d1101-499">이 특성은 컨트롤러가 웹 API 요청에 응답함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-499">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="d1101-500">특성을 사용하도록 설정하는 특정 동작에 대한 정보는 <xref:web-api/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d1101-500">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="d1101-501">DI를 사용하여 데이터베이스 컨텍스트(`TodoContext`)를 컨트롤러에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-501">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="d1101-502">컨트롤러의 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드 각각에서 데이터베이스 컨텍스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-502">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="d1101-503">데이터베이스가 비어 있는 경우 데이터베이스에 `Item1`이라는 항목을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-503">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="d1101-504">이 코드는 생성자에 위치하므로 새 HTTP 요청이 발생할 때마다 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-504">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="d1101-505">모든 항목을 삭제하면 생성자는 다음에 API가 호출될 경우 `Item1`을 다시 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-505">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="d1101-506">따라서 실제로 작동되는 경우 삭제가 작동하지 않는 것처럼 보일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-506">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="d1101-507">GET 메서드 추가</span><span class="sxs-lookup"><span data-stu-id="d1101-507">Add Get methods</span></span>

<span data-ttu-id="d1101-508">할 일 항목을 가져오는 API를 제공하려면 다음 메서드를 `TodoController` 클래스에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-508">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="d1101-509">다음과 같은 메서드는 두 개의 GET 엔드포인트를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-509">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="d1101-510">앱이 계속 실행되고 있으면 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-510">Stop the app if it's still running.</span></span> <span data-ttu-id="d1101-511">그런 다음, 다시 실행하여 최신 변경 내용을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-511">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="d1101-512">브라우저에서 두 개의 엔드포인트를 호출하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-512">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="d1101-513">예:</span><span class="sxs-lookup"><span data-stu-id="d1101-513">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="d1101-514">`GetTodoItems`를 호출하여 다음 HTTP 응답이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-514">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="d1101-515">라우팅 및 URL 경로</span><span class="sxs-lookup"><span data-stu-id="d1101-515">Routing and URL paths</span></span>

<span data-ttu-id="d1101-516">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 특성은 HTTP GET 요청에 응답하는 메서드를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-516">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="d1101-517">각 방법에 대한 URL 경로는 다음과 같이 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-517">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="d1101-518">컨트롤러의 `Route` 특성에서 템플릿 문자열로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-518">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="d1101-519">`[controller]`를 컨트롤러의 이름으로 바꿉니다. 일반적으로 컨트롤러 클래스 이름에서 "Controller" 접미사를 뺀 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-519">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="d1101-520">이 샘플의 경우 컨트롤러 클래스 이름은 **Todo**Controller이므로 컨트롤러 이름은 "todo"입니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-520">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="d1101-521">ASP.NET Core [라우팅](xref:mvc/controllers/routing)은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-521">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="d1101-522">`[HttpGet]` 특성에 경로 템플릿(예: `[HttpGet("products")]`)이 있는 경우 경로에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-522">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="d1101-523">이 샘플은 템플릿을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-523">This sample doesn't use a template.</span></span> <span data-ttu-id="d1101-524">자세한 내용은 [Http[동사] 특성을 사용한 특성 라우팅](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d1101-524">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="d1101-525">다음 `GetTodoItem` 메서드에서 `"{id}"`는 할 일 항목의 고유 식별자에 대한 자리 표시자 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-525">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="d1101-526">`GetTodoItem`가 호출되면 URL의 `"{id}"` 값을 `id` 매개 변수의 메서드에 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-526">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="d1101-527">반환 값</span><span class="sxs-lookup"><span data-stu-id="d1101-527">Return values</span></span>

<span data-ttu-id="d1101-528">`GetTodoItems` 및 `GetTodoItem` 메서드의 반환 형식은 [ActionResult\<T> 형식](xref:web-api/action-return-types#actionresultt-type)입니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-528">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="d1101-529">ASP.NET Core는 자동으로 [JSON](https://www.json.org/)에 개체를 직렬화하고 JSON을 응답 메시지의 본문에 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-529">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="d1101-530">이 반환 형식의 응답 코드는 200이며 처리되지 않은 예외가 없다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-530">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="d1101-531">처리되지 않은 예외는 5xx 오류로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-531">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="d1101-532">`ActionResult` 반환 형식은 다양한 HTTP 상태 코드를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-532">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="d1101-533">예를 들어 `GetTodoItem`은 두 가지 상태 값을 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-533">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="d1101-534">요청된 ID와 일치하는 항목이 없는 경우 메서드가 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) 오류 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-534">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="d1101-535">그렇지 않으면 메서드가 JSON 응답 본문에서 200을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-535">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="d1101-536">`item`을 반환하면 HTTP 200 응답이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-536">Returning `item` results in an HTTP 200 response.</span></span>


## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="d1101-537">GetTodoItems 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="d1101-537">Test the GetTodoItems method</span></span>

<span data-ttu-id="d1101-538">이 자습서에서는 Postman을 사용하여 웹 API를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-538">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="d1101-539">[Postman](https://www.getpostman.com/downloads/) 설치</span><span class="sxs-lookup"><span data-stu-id="d1101-539">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="d1101-540">웹앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-540">Start the web app.</span></span>
* <span data-ttu-id="d1101-541">Postman을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-541">Start Postman.</span></span>
* <span data-ttu-id="d1101-542">**SSL 인증서 확인** 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="d1101-542">Disable **SSL certificate verification**</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d1101-543">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d1101-543">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d1101-544">**파일** > **설정**(**일반** 탭)에서 **SSL 인증서 확인**을 사용하지 않게 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-544">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="d1101-545">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d1101-545">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="d1101-546">**Postman** > **기본 설정**(**일반** 탭)에서 **SSL 인증서 확인**을 사용하지 않게 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-546">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="d1101-547">또는 렌치를 선택하고 **설정**을 선택한 다음 SSL 인증서 확인을 사용하지 않게 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-547">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="d1101-548">컨트롤러를 테스트한 후에 SSL 인증서 확인을 다시 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-548">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="d1101-549">새 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-549">Create a new request.</span></span>
  * <span data-ttu-id="d1101-550">HTTP 메서드를 **GET**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-550">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="d1101-551">요청 URL을 `https://localhost:<port>/api/todo`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-551">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="d1101-552">예를 들어, `https://localhost:5001/api/todo`을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-552">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="d1101-553">Postman에서 **두 개의 창 보기**를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-553">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="d1101-554">**보내기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-554">Select **Send**.</span></span>

![Get 요청이 있는 Postman](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="d1101-556">메서드 만들기 추가</span><span class="sxs-lookup"><span data-stu-id="d1101-556">Add a Create method</span></span>

<span data-ttu-id="d1101-557">*Controllers/TodoController.cs* 내부에 다음 `PostTodoItem` 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-557">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="d1101-558">이전 코드는 [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 특성으로 표시되는 HTTP POST 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-558">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="d1101-559">이 메서드는 HTTP 요청 본문에서 할 일 항목 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-559">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="d1101-560">`CreatedAtAction` 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-560">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="d1101-561">성공 시 HTTP 201 상태 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-561">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="d1101-562">HTTP 201은 서버에서 새 리소스를 만드는 HTTP POST 메서드의 표준 응답입니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-562">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="d1101-563">`Location` 헤더를 응답에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-563">Adds a `Location` header to the response.</span></span> <span data-ttu-id="d1101-564">`Location` 헤더는 새로 만들어진 할 일 항목의 URI를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-564">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="d1101-565">자세한 내용은 [10.2.2 201 생성됨](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d1101-565">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="d1101-566">`Location` 헤더의 URI를 만들려면 `GetTodoItem` 작업을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-566">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="d1101-567">C# `nameof` 키워드는 `CreatedAtAction` 호출에서 작업 이름의 하드 코딩을 방지하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-567">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="d1101-568">PostTodoItem 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="d1101-568">Test the PostTodoItem method</span></span>

* <span data-ttu-id="d1101-569">프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-569">Build the project.</span></span>
* <span data-ttu-id="d1101-570">Postman에서 HTTP 메서드를 `POST`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-570">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="d1101-571">**본문** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-571">Select the **Body** tab.</span></span>
* <span data-ttu-id="d1101-572">**원시** 라디오 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-572">Select the **raw** radio button.</span></span>
* <span data-ttu-id="d1101-573">유형을 **JSON(application/json)** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-573">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="d1101-574">요청 본문에서 할 일 항목에 대한 JSON을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-574">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="d1101-575">**보내기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-575">Select **Send**.</span></span>

  ![생성 요청이 있는 Postman](first-web-api/_static/create.png)

  <span data-ttu-id="d1101-577">405 메서드가 허용되지 않음 오류가 발생할 경우 `PostTodoItem` 메서드를 추가한 후에 프로젝트를 컴파일하지 않아서 발생한 결과일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-577">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="d1101-578">위치 헤더 URI 테스트</span><span class="sxs-lookup"><span data-stu-id="d1101-578">Test the location header URI</span></span>

* <span data-ttu-id="d1101-579">**응답** 창에서 **헤더** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-579">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="d1101-580">**위치** 헤더 값을 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-580">Copy the **Location** header value:</span></span>

  ![Postman 콘솔의 헤더 탭](first-web-api/_static/pmc2.png)

* <span data-ttu-id="d1101-582">메서드를 GET으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-582">Set the method to GET.</span></span>
* <span data-ttu-id="d1101-583">URI(예: `https://localhost:5001/api/Todo/2`) 붙여넣기</span><span class="sxs-lookup"><span data-stu-id="d1101-583">Paste the URI (for example, `https://localhost:5001/api/Todo/2`)</span></span>
* <span data-ttu-id="d1101-584">**보내기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-584">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="d1101-585">PutTodoItem 메서드 추가</span><span class="sxs-lookup"><span data-stu-id="d1101-585">Add a PutTodoItem method</span></span>

<span data-ttu-id="d1101-586">다음 `PutTodoItem` 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-586">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="d1101-587">HTTP PUT을 사용하는 것을 제외하고 `PutTodoItem`는 `PostTodoItem`와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-587">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="d1101-588">응답은 [204(콘텐츠 없음)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-588">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="d1101-589">HTTP 사양에 따라 PUT 요청의 경우 클라이언트는 변경 내용만이 아니라 전체 업데이트된 엔터티를 보내야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-589">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="d1101-590">부분 업데이트를 지원하려면 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-590">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="d1101-591">`PutTodoItem`을 호출하는 중 오류가 발생하면 `GET`을 호출하여 데이터베이스에 항목이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-591">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="d1101-592">PutTodoItem 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="d1101-592">Test the PutTodoItem method</span></span>

<span data-ttu-id="d1101-593">이 샘플은 앱이 시작될 때마다 초기화되어야 하는 메모리 내 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-593">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="d1101-594">PUT 호출을 실행하기 전에 데이터베이스에 항목이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-594">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="d1101-595">GET을 호출하여 PUT 호출을 실행하기 전에 데이터베이스에 항목이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-595">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="d1101-596">ID = 1인 할 일 항목을 업데이트하고 해당 이름을 "feed fish"로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-596">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="d1101-597">다음 이미지는 Postman 업데이트를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-597">The following image shows the Postman update:</span></span>

![204(콘텐츠 없음) 응답을 보여주는 Postman 콘솔](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="d1101-599">DeleteTodoItem 메서드 추가</span><span class="sxs-lookup"><span data-stu-id="d1101-599">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="d1101-600">다음 `DeleteTodoItem` 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-600">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="d1101-601">`DeleteTodoItem` 응답은 [204(콘텐츠 없음)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-601">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="d1101-602">DeleteTodoItem 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="d1101-602">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="d1101-603">Postman을 사용하여 할 일 항목을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-603">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="d1101-604">메서드를 `DELETE`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-604">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="d1101-605">예를 들어 삭제할 개체의 URI를 `https://localhost:5001/api/todo/1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-605">Set the URI of the object to delete, for example `https://localhost:5001/api/todo/1`</span></span>
* <span data-ttu-id="d1101-606">**보내기** 선택</span><span class="sxs-lookup"><span data-stu-id="d1101-606">Select **Send**</span></span>

<span data-ttu-id="d1101-607">샘플 앱을 사용하면 모든 항목을 삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-607">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="d1101-608">하지만 마지막 항목이 삭제되면 다음에 API를 호출하는 경우 모델 클래스 생성자에서 새로운 항목이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-608">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-api-with-jquery"></a><span data-ttu-id="d1101-609">jQuery를 사용하여 API 호출</span><span class="sxs-lookup"><span data-stu-id="d1101-609">Call the API with jQuery</span></span>

<span data-ttu-id="d1101-610">이 섹션에서는 jQuery를 사용하여 웹 API를 호출하는 HTML 페이지가 추가되었습니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-610">In this section, an HTML page is added that uses jQuery to call the web api.</span></span> <span data-ttu-id="d1101-611">jQuery는 요청을 시작하고 API 응답의 세부 정보로 페이지를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-611">jQuery initiates the request and updates the page with the details from the API's response.</span></span>

<span data-ttu-id="d1101-612">다음 강조 표시된 코드로 *Startup.cs*를 업데이트하여 앱이 [정적 파일을 제공](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)하고 [기본 파일 매핑을 사용](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)하도록 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-612">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="d1101-613">프로젝트 디렉터리에서 *wwwroot* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-613">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="d1101-614">*index.html*이라는 HTML 파일을 *wwwroot* 디렉터리에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-614">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="d1101-615">다음 표시로 콘텐츠를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-615">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="d1101-616">*site.js*라는 JavaScript 파일을 *wwwroot* 디렉터리에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-616">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="d1101-617">다음 코드로 콘텐츠를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-617">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="d1101-618">HTML 페이지를 로컬에서 테스트하려면 ASP.NET Core 프로젝트의 시작 설정을 변경해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-618">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="d1101-619">*Properties\launchSettings.json*을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-619">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="d1101-620">`launchUrl` 속성을 제거하여 앱이 *index.html*&mdash; 프로젝트의 기본 파일에서 열리도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-620">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="d1101-621">여러 가지 방법으로 jQuery를 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-621">There are several ways to get jQuery.</span></span> <span data-ttu-id="d1101-622">위의 코드 조각에서 라이브러리는 CDN에서 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-622">In the preceding snippet, the library is loaded from a CDN.</span></span>

<span data-ttu-id="d1101-623">이 샘플은 API의 모든 CRUD 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-623">This sample calls all of the CRUD methods of the API.</span></span> <span data-ttu-id="d1101-624">API 호출에 대한 설명은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-624">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="d1101-625">할 일 항목의 목록 가져오기</span><span class="sxs-lookup"><span data-stu-id="d1101-625">Get a list of to-do items</span></span>

<span data-ttu-id="d1101-626">jQuery [ajax](https://api.jquery.com/jquery.ajax/) 함수는 할 일 항목의 배열을 나타내는 JSON을 반환하는 API에 `GET` 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-626">The jQuery [ajax](https://api.jquery.com/jquery.ajax/) function sends a `GET` request to the API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="d1101-627">요청이 성공하면 `success` 콜백 함수가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-627">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="d1101-628">콜백에서 DOM은 할 일 정보로 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-628">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="d1101-629">할 일 항목 추가</span><span class="sxs-lookup"><span data-stu-id="d1101-629">Add a to-do item</span></span>

<span data-ttu-id="d1101-630">[ajax](https://api.jquery.com/jquery.ajax/) 함수는 `POST` 요청 본문에서 할 일 항목을 사용하여 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-630">The [ajax](https://api.jquery.com/jquery.ajax/) function sends a `POST` request with the to-do item in the request body.</span></span> <span data-ttu-id="d1101-631">`accepts` 및 `contentType` 옵션은 수신 및 전송되는 미디어 형식을 지정하기 위해 `application/json`으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-631">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="d1101-632">[JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)를 사용하여 할 일 항목을 JSON으로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-632">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="d1101-633">API가 성공적인 상태 코드를 반환하면 `getData` 함수가 호출되어 HTML 테이블을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-633">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="d1101-634">할 일 항목 업데이트</span><span class="sxs-lookup"><span data-stu-id="d1101-634">Update a to-do item</span></span>

<span data-ttu-id="d1101-635">할 일 항목을 업데이트하는 작업은 추가하는 작업과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-635">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="d1101-636">`url`은 항목의 고유 식별자를 추가하도록 변경되고 `type`은 `PUT`입니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-636">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="d1101-637">할 일 항목 삭제</span><span class="sxs-lookup"><span data-stu-id="d1101-637">Delete a to-do item</span></span>

<span data-ttu-id="d1101-638">할 일 항목을 삭제하려면 AJAX 호출에서 `type`을 `DELETE`로 설정하고 URL에서 항목의 고유 식별자를 지정하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="d1101-638">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="d1101-639">추가 자료</span><span class="sxs-lookup"><span data-stu-id="d1101-639">Additional resources</span></span>

<span data-ttu-id="d1101-640">[이 자습서에서 샘플 코드 보기 또는 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)</span><span class="sxs-lookup"><span data-stu-id="d1101-640">[View or download sample code for this tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="d1101-641">[다운로드하는 방법](xref:index#how-to-download-a-sample)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d1101-641">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="d1101-642">자세한 내용은 다음 리소스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d1101-642">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="d1101-643">이 자습서의 YouTube 버전</span><span class="sxs-lookup"><span data-stu-id="d1101-643">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
