---
title: '자습서: ASP.NET Core를 사용하여 웹 API 만들기'
author: rick-anderson
description: ASP.NET Core를 사용하여 웹 API를 빌드하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: tutorials/first-web-api
ms.openlocfilehash: 73e547b014d78dcbcbf1c887ebec16e0743d10b9
ms.sourcegitcommit: f259889044d1fc0f0c7e3882df0008157ced4915
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294745"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="07d37-103">자습서: ASP.NET Core를 사용하여 웹 API 만들기</span><span class="sxs-lookup"><span data-stu-id="07d37-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="07d37-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="07d37-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="07d37-105">이 자습서에서는 ASP.NET Core를 사용하여 웹 API를 빌드하는 작업의 기본 사항을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="07d37-106">이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="07d37-107">웹 API 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-107">Create a web API project.</span></span>
> * <span data-ttu-id="07d37-108">모델 클래스와 데이터베이스 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="07d37-109">CRUD 메서드로 컨트롤러를 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="07d37-110">라우팅, URL 경로 및 반환 값을 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="07d37-111">Postman을 사용하여 웹 API를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-111">Call the web API with Postman.</span></span>

<span data-ttu-id="07d37-112">과정을 마치면 웹 API를 통해 데이터베이스에 저장된 “할 일” 항목을 관리할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="07d37-113">개요</span><span class="sxs-lookup"><span data-stu-id="07d37-113">Overview</span></span>

<span data-ttu-id="07d37-114">이 자습서에서는 다음 API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="07d37-115">API</span><span class="sxs-lookup"><span data-stu-id="07d37-115">API</span></span> | <span data-ttu-id="07d37-116">설명</span><span class="sxs-lookup"><span data-stu-id="07d37-116">Description</span></span> | <span data-ttu-id="07d37-117">요청 본문</span><span class="sxs-lookup"><span data-stu-id="07d37-117">Request body</span></span> | <span data-ttu-id="07d37-118">응답 본문</span><span class="sxs-lookup"><span data-stu-id="07d37-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="07d37-119">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="07d37-119">GET /api/TodoItems</span></span> | <span data-ttu-id="07d37-120">할 일 항목 모두 가져오기</span><span class="sxs-lookup"><span data-stu-id="07d37-120">Get all to-do items</span></span> | <span data-ttu-id="07d37-121">없음</span><span class="sxs-lookup"><span data-stu-id="07d37-121">None</span></span> | <span data-ttu-id="07d37-122">할 일 항목의 배열</span><span class="sxs-lookup"><span data-stu-id="07d37-122">Array of to-do items</span></span>|
|<span data-ttu-id="07d37-123">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="07d37-123">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="07d37-124">ID로 항목 가져오기</span><span class="sxs-lookup"><span data-stu-id="07d37-124">Get an item by ID</span></span> | <span data-ttu-id="07d37-125">없음</span><span class="sxs-lookup"><span data-stu-id="07d37-125">None</span></span> | <span data-ttu-id="07d37-126">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="07d37-126">To-do item</span></span>|
|<span data-ttu-id="07d37-127">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="07d37-127">POST /api/TodoItems</span></span> | <span data-ttu-id="07d37-128">새 항목 추가</span><span class="sxs-lookup"><span data-stu-id="07d37-128">Add a new item</span></span> | <span data-ttu-id="07d37-129">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="07d37-129">To-do item</span></span> | <span data-ttu-id="07d37-130">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="07d37-130">To-do item</span></span> |
|<span data-ttu-id="07d37-131">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="07d37-131">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="07d37-132">기존 항목 업데이트 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="07d37-132">Update an existing item &nbsp;</span></span> | <span data-ttu-id="07d37-133">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="07d37-133">To-do item</span></span> | <span data-ttu-id="07d37-134">없음</span><span class="sxs-lookup"><span data-stu-id="07d37-134">None</span></span> |
|<span data-ttu-id="07d37-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="07d37-135">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="07d37-136">항목 삭제 &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="07d37-136">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="07d37-137">없음</span><span class="sxs-lookup"><span data-stu-id="07d37-137">None</span></span> | <span data-ttu-id="07d37-138">없음</span><span class="sxs-lookup"><span data-stu-id="07d37-138">None</span></span>|

<span data-ttu-id="07d37-139">다음 다이어그램에서는 앱의 디자인을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-139">The following diagram shows the design of the app.</span></span>

![클라이언트는 왼쪽에 상자로 표시됩니다.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="07d37-145">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="07d37-145">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="07d37-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d37-146">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="07d37-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07d37-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="07d37-148">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d37-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="07d37-149">웹 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="07d37-149">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="07d37-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d37-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="07d37-151">**파일 메뉴**에서 **새로 만들기** > **프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-151">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="07d37-152">**ASP.NET Core 웹 애플리케이션** 템플릿을 선택하고 **다음**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-152">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="07d37-153">프로젝트 이름을 *TodoApi*로 지정하고 **만들기**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-153">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="07d37-154">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **.NET Core** 및 **ASP.NET Core 3.1**이 선택되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-154">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="07d37-155">**API** 템플릿을 선택하고 **만들기**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-155">Select the **API** template and click **Create**.</span></span>

![VS 새 프로젝트 대화 상자](first-web-api/_static/vs3.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="07d37-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07d37-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="07d37-158">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-158">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="07d37-159">디렉터리(`cd`)를 프로젝트 폴더를 포함하는 폴더로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-159">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="07d37-160">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-160">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="07d37-161">프로젝트에 필수 자산을 추가하려는지 묻는 대화 상자가 나타나면 **예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-161">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="07d37-162">이전 명령은</span><span class="sxs-lookup"><span data-stu-id="07d37-162">The preceding commands:</span></span>

  * <span data-ttu-id="07d37-163">새 웹 API 프로젝트를 만들고 Visual Studio Code에서 엽니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-163">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="07d37-164">다음 섹션에서 필요한 NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-164">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="07d37-165">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d37-165">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="07d37-166">**파일** > **새 솔루션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-166">Select **File** > **New Solution**.</span></span>

  ![macOS 새 솔루션](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="07d37-168">**.NET Core** > **앱** > **API** > **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-168">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS 새 프로젝트 대화 상자](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="07d37-170">**새 ASP.NET Core Web API 구성** 대화 상자에서 \* *.NET Core 3.1*의 **대상 프레임워크**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-170">In the **Configure your new ASP.NET Core Web API** dialog, select **Target Framework** of \**.NET Core 3.1*.</span></span>

* <span data-ttu-id="07d37-171">**프로젝트 이름**으로 *TodoApi*를 입력한 다음, **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-171">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![구성 대화 상자](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="07d37-173">프로젝트 폴더에서 명령 터미널을 열고 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-173">Open a command terminal in the project folder and run the following commands:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.SqlServer
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="07d37-174">API 테스트</span><span class="sxs-lookup"><span data-stu-id="07d37-174">Test the API</span></span>

<span data-ttu-id="07d37-175">프로젝트 템플릿은 `WeatherForecast` API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-175">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="07d37-176">브라우저에서 `Get` 메서드를 호출하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-176">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="07d37-177">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d37-177">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="07d37-178">Ctrl+F5 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-178">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="07d37-179">Visual Studio가 브라우저를 시작하고 `https://localhost:<port>/WeatherForecast`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-179">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="07d37-180">IIS Express 인증서를 신뢰해야 하는지 묻는 대화 상자가 표시되면 **예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-180">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="07d37-181">다음으로, **보안 경고** 대화 상자가 나타나면 **예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-181">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="07d37-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07d37-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="07d37-183">Ctrl+F5 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-183">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="07d37-184">브라우저에서 다음 URL [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast)로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-184">In a browser, go to following URL: [https://localhost:5001/WeatherForecast](https://localhost:5001/WeatherForecast).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="07d37-185">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d37-185">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="07d37-186">**실행** > **디버깅 시작**을 선택하여 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-186">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="07d37-187">Mac용 Visual Studio가 브라우저를 시작하고 `https://localhost:<port>`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-187">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="07d37-188">HTTP 404(찾을 수 없음) 오류가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-188">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="07d37-189">`/WeatherForecast`를 URL에 추가합니다(URL을 `https://localhost:<port>/WeatherForecast`로 변경).</span><span class="sxs-lookup"><span data-stu-id="07d37-189">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="07d37-190">다음과 비슷한 JSON이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-190">JSON similar to the following is returned:</span></span>

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

## <a name="add-a-model-class"></a><span data-ttu-id="07d37-191">모델 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="07d37-191">Add a model class</span></span>

<span data-ttu-id="07d37-192">*모델*은 앱에서 관리하는 데이터를 나타내는 일련의 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-192">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="07d37-193">이 앱에 대한 모델은 단일 `TodoItem` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-193">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="07d37-194">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d37-194">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="07d37-195">**솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-195">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="07d37-196">**추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-196">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="07d37-197">폴더 이름을 *Models*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-197">Name the folder *Models*.</span></span>

* <span data-ttu-id="07d37-198">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-198">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="07d37-199">클래스 이름을 *TodoItem*으로 지정하고 **추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-199">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="07d37-200">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-200">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="07d37-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07d37-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="07d37-202">*Models* 폴더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-202">Add a folder named *Models*.</span></span>

* <span data-ttu-id="07d37-203">다음 코드를 사용하여 *Models* 폴더에 `TodoItem` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-203">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="07d37-204">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d37-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="07d37-205">프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-205">Right-click the project.</span></span> <span data-ttu-id="07d37-206">**추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-206">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="07d37-207">폴더 이름을 *Models*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-207">Name the folder *Models*.</span></span>

  ![새 폴더](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="07d37-209">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 파일** > **일반** > **빈 클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-209">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="07d37-210">클래스 이름을 *TodoItem*으로 지정한 다음, **새로 만들기**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-210">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="07d37-211">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-211">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="07d37-212">`Id` 속성은 관계형 데이터베이스에서 고유 키로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-212">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="07d37-213">모델 클래스는 프로젝트의 어디로든 이동할 수 있지만 규칙에 따라 *Models* 폴더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-213">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="07d37-214">데이터베이스 컨텍스트 추가</span><span class="sxs-lookup"><span data-stu-id="07d37-214">Add a database context</span></span>

<span data-ttu-id="07d37-215">*데이터베이스 컨텍스트*는 데이터 모델에 맞게 Entity Framework 기능을 조정하는 주 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-215">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="07d37-216">`Microsoft.EntityFrameworkCore.DbContext` 클래스에서 파생시키는 방식으로 이 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-216">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="07d37-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d37-217">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-microsoftentityframeworkcoresqlserver"></a><span data-ttu-id="07d37-218">Microsoft.EntityFrameworkCore.SqlServer 추가</span><span class="sxs-lookup"><span data-stu-id="07d37-218">Add Microsoft.EntityFrameworkCore.SqlServer</span></span>

* <span data-ttu-id="07d37-219">**도구** 메뉴에서 **NuGet 패키지 관리자 > 솔루션용 NuGet 패키지 관리**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-219">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="07d37-220">**찾아보기** 탭을 선택한 다음 검색 상자에 **Microsoft.EntityFrameworkCore.SqlServer**를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-220">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.SqlServer** in the search box.</span></span>
* <span data-ttu-id="07d37-221">왼쪽 창에서 **Microsoft.EntityFrameworkCore.SqlServer**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-221">Select **Microsoft.EntityFrameworkCore.SqlServer** in the left pane.</span></span>
* <span data-ttu-id="07d37-222">오른쪽 창에서 **프로젝트** 확인란을 선택하고 **설치**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-222">Select the **Project** check box in the right pane and then select **Install**.</span></span>
* <span data-ttu-id="07d37-223">위 지침에 따라 `Microsoft.EntityFrameworkCore.InMemory` NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-223">Use the preceding instructions to add the `Microsoft.EntityFrameworkCore.InMemory` NuGet package.</span></span>

![NuGet 패키지 관리자](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="07d37-225">TodoContext 데이터베이스 컨텍스트 추가</span><span class="sxs-lookup"><span data-stu-id="07d37-225">Add the TodoContext database context</span></span>

* <span data-ttu-id="07d37-226">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-226">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="07d37-227">클래스 이름을 *TodoContext*로 지정하고 **추가**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-227">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="07d37-228">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d37-228">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="07d37-229">`TodoContext` 클래스를 *Models* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-229">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="07d37-230">다음 코드를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-230">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="07d37-231">데이터베이스 컨텍스트 등록</span><span class="sxs-lookup"><span data-stu-id="07d37-231">Register the database context</span></span>

<span data-ttu-id="07d37-232">ASP.NET Core에서는 DB 컨텍스트와 같은 서비스를 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 컨테이너에 등록해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-232">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="07d37-233">컨테이너는 컨트롤러에 서비스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-233">The container provides the service to controllers.</span></span>

<span data-ttu-id="07d37-234">*Startup.cs*를 다음 강조 표시된 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-234">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="07d37-235">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="07d37-235">The preceding code:</span></span>

* <span data-ttu-id="07d37-236">사용되지 않는 `using` 선언을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-236">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="07d37-237">DI 컨테이너에 데이터베이스 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-237">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="07d37-238">데이터베이스 컨텍스트가 메모리 내 데이터베이스를 사용하도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-238">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="07d37-239">컨트롤러 스캐폴드</span><span class="sxs-lookup"><span data-stu-id="07d37-239">Scaffold a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="07d37-240">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d37-240">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="07d37-241">*Controllers* 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-241">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="07d37-242">**추가** > **스캐폴드 항목 새로 만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-242">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="07d37-243">**Entity Framework를 사용하며 동작이 포함된 API 컨트롤러**를 선택하고 **추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-243">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="07d37-244">**Entity Framework를 사용하며 동작이 포함된 API 컨트롤러 추가** 대화 상자에서:</span><span class="sxs-lookup"><span data-stu-id="07d37-244">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="07d37-245">**모델 클래스**에서 **TodoItem (TodoApi.Models)** 을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-245">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="07d37-246">**데이터 컨텍스트 클래스**에서 **TodoContext (TodoApi.Models)** 를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-246">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="07d37-247">**추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-247">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="07d37-248">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d37-248">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="07d37-249">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-249">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="07d37-250">이전 명령은</span><span class="sxs-lookup"><span data-stu-id="07d37-250">The preceding commands:</span></span>

* <span data-ttu-id="07d37-251">스캐폴딩에 필요한 NuGet 패키지를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-251">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="07d37-252">스캐폴딩 엔진(`dotnet-aspnet-codegenerator`)을 설치합니다</span><span class="sxs-lookup"><span data-stu-id="07d37-252">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="07d37-253">`TodoItemsController`를 스캐폴드합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-253">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="07d37-254">생성된 코드는:</span><span class="sxs-lookup"><span data-stu-id="07d37-254">The generated code:</span></span>

* <span data-ttu-id="07d37-255">[`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 특성으로 클래스를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-255">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="07d37-256">이 특성은 컨트롤러가 웹 API 요청에 응답함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-256">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="07d37-257">특성을 사용하도록 설정하는 특정 동작에 대한 정보는 <xref:web-api/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="07d37-257">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="07d37-258">DI를 사용하여 데이터베이스 컨텍스트(`TodoContext`)를 컨트롤러에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-258">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="07d37-259">컨트롤러의 각 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드에서 해당 데이터베이스 컨텍스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-259">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="07d37-260">PostTodoItem 만들기 메서드 검사</span><span class="sxs-lookup"><span data-stu-id="07d37-260">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="07d37-261">`PostTodoItem`의 return 문이 [nameof](/dotnet/csharp/language-reference/operators/nameof) 연산자를 사용하도록 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-261">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="07d37-262">위의 코드는 [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 특성으로 표시되는 HTTP POST 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-262">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="07d37-263">이 메서드는 HTTP 요청 본문에서 할 일 항목 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-263">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="07d37-264"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-264">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="07d37-265">성공 시 HTTP 201 상태 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-265">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="07d37-266">HTTP 201은 서버에서 새 리소스를 만드는 HTTP POST 메서드의 표준 응답입니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-266">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="07d37-267">응답에 대한 [위치](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) 헤더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-267">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="07d37-268">`Location` 헤더는 새로 만들어진 할 일 항목의 [URI](https://developer.mozilla.org/docs/Glossary/URI)를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-268">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="07d37-269">자세한 내용은 [10.2.2 201 생성됨](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="07d37-269">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="07d37-270">`Location` 헤더의 URI를 만들려면 `GetTodoItem` 작업을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-270">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="07d37-271">C# `nameof` 키워드는 `CreatedAtAction` 호출에서 작업 이름의 하드 코딩을 방지하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-271">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="07d37-272">Postman을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-272">Install Postman</span></span>

<span data-ttu-id="07d37-273">이 자습서에서는 Postman을 사용하여 웹 API를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-273">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="07d37-274">[Postman](https://www.getpostman.com/downloads/)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-274">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="07d37-275">웹앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-275">Start the web app.</span></span>
* <span data-ttu-id="07d37-276">Postman을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-276">Start Postman.</span></span>
* <span data-ttu-id="07d37-277">**SSL 인증서 확인**을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-277">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="07d37-278">**파일** > **설정**(**일반** 탭)에서 **SSL 인증서 확인**을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-278">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="07d37-279">컨트롤러를 테스트한 후에 SSL 인증서 확인을 다시 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-279">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="07d37-280">Postman을 사용하여 PostTodoItem 테스트</span><span class="sxs-lookup"><span data-stu-id="07d37-280">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="07d37-281">새 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-281">Create a new request.</span></span>
* <span data-ttu-id="07d37-282">HTTP 메서드를 `POST`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-282">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="07d37-283">**본문** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-283">Select the **Body** tab.</span></span>
* <span data-ttu-id="07d37-284">**원시** 라디오 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-284">Select the **raw** radio button.</span></span>
* <span data-ttu-id="07d37-285">유형을 **JSON(application/json)** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-285">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="07d37-286">요청 본문에서 할 일 항목에 대한 JSON을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-286">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="07d37-287">**보내기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-287">Select **Send**.</span></span>

  ![생성 요청이 있는 Postman](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="07d37-289">위치 헤더 URI 테스트</span><span class="sxs-lookup"><span data-stu-id="07d37-289">Test the location header URI</span></span>

* <span data-ttu-id="07d37-290">**응답** 창에서 **헤더** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-290">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="07d37-291">**위치** 헤더 값을 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-291">Copy the **Location** header value:</span></span>

  ![Postman 콘솔의 헤더 탭](first-web-api/_static/3/create.png)

* <span data-ttu-id="07d37-293">메서드를 GET으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-293">Set the method to GET.</span></span>
* <span data-ttu-id="07d37-294">URI(예: `https://localhost:5001/api/TodoItems/1`)를 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-294">Paste the URI (for example, `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="07d37-295">**보내기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-295">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="07d37-296">GET 메서드 검사</span><span class="sxs-lookup"><span data-stu-id="07d37-296">Examine the GET methods</span></span>

<span data-ttu-id="07d37-297">다음과 같은 메서드는 두 개의 GET 엔드포인트를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-297">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="07d37-298">브라우저 또는 Postman에서 두 개의 엔드포인트를 호출하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-298">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="07d37-299">예:</span><span class="sxs-lookup"><span data-stu-id="07d37-299">For example:</span></span>

* [https://localhost:5001/api/TodoItems](https://localhost:5001/api/TodoItems)
* [https://localhost:5001/api/TodoItems/1](https://localhost:5001/api/TodoItems/1)

<span data-ttu-id="07d37-300">`GetTodoItems`를 호출하면 다음과 비슷한 응답이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-300">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="07d37-301">Postman을 사용하여 Get 테스트</span><span class="sxs-lookup"><span data-stu-id="07d37-301">Test Get with Postman</span></span>

* <span data-ttu-id="07d37-302">새 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-302">Create a new request.</span></span>
* <span data-ttu-id="07d37-303">HTTP 메서드를 **GET**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-303">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="07d37-304">요청 URL을 `https://localhost:<port>/api/TodoItems`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-304">Set the request URL to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="07d37-305">예: `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="07d37-305">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="07d37-306">Postman에서 **두 개의 창 보기**를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-306">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="07d37-307">**보내기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-307">Select **Send**.</span></span>

<span data-ttu-id="07d37-308">이 앱은 메모리 내 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-308">This app uses an in-memory database.</span></span> <span data-ttu-id="07d37-309">앱이 중지된 후 시작되면 이전 GET 요청이 데이터를 반환하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-309">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="07d37-310">데이터가 반환되지 않으면 앱에 데이터를 [POST](#post)합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-310">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="07d37-311">라우팅 및 URL 경로</span><span class="sxs-lookup"><span data-stu-id="07d37-311">Routing and URL paths</span></span>

<span data-ttu-id="07d37-312">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 특성은 HTTP GET 요청에 응답하는 메서드를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-312">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="07d37-313">각 방법에 대한 URL 경로는 다음과 같이 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-313">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="07d37-314">컨트롤러의 `Route` 특성에서 템플릿 문자열로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-314">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="07d37-315">`[controller]`를 컨트롤러의 이름으로 바꿉니다. 일반적으로 컨트롤러 클래스 이름에서 "Controller" 접미사를 뺀 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-315">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="07d37-316">이 샘플의 경우 컨트롤러 클래스 이름은 **TodoItems**Controller이므로 컨트롤러 이름은 “TodoItems”입니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-316">For this sample, the controller class name is **TodoItems**Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="07d37-317">ASP.NET Core [라우팅](xref:mvc/controllers/routing)은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-317">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="07d37-318">`[HttpGet]` 특성에 경로 템플릿(예: `[HttpGet("products")]`)이 있는 경우 경로에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-318">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="07d37-319">이 샘플은 템플릿을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-319">This sample doesn't use a template.</span></span> <span data-ttu-id="07d37-320">자세한 내용은 [Http[동사] 특성을 사용한 특성 라우팅](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="07d37-320">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="07d37-321">다음 `GetTodoItem` 메서드에서 `"{id}"`는 할 일 항목의 고유 식별자에 대한 자리 표시자 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-321">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="07d37-322">`GetTodoItem`이 호출되면 URL의 `"{id}"` 값을 `id` 매개 변수의 메서드에 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-322">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="07d37-323">반환 값</span><span class="sxs-lookup"><span data-stu-id="07d37-323">Return values</span></span>

<span data-ttu-id="07d37-324">`GetTodoItems` 및 `GetTodoItem` 메서드의 반환 형식은 [ActionResult\<T> 형식](xref:web-api/action-return-types#actionresultt-type)입니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-324">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="07d37-325">ASP.NET Core는 자동으로 [JSON](https://www.json.org/)에 개체를 직렬화하고 JSON을 응답 메시지의 본문에 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-325">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="07d37-326">이 반환 형식의 응답 코드는 200이며 처리되지 않은 예외가 없다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-326">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="07d37-327">처리되지 않은 예외는 5xx 오류로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-327">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="07d37-328">`ActionResult` 반환 형식은 다양한 HTTP 상태 코드를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-328">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="07d37-329">예를 들어 `GetTodoItem`은 두 가지 상태 값을 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-329">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="07d37-330">요청된 ID와 일치하는 항목이 없는 경우 메서드가 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) 오류 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-330">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="07d37-331">그렇지 않으면 메서드가 JSON 응답 본문에서 200을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-331">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="07d37-332">`item`을 반환하면 HTTP 200 응답이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-332">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="07d37-333">PutTodoItem 메서드</span><span class="sxs-lookup"><span data-stu-id="07d37-333">The PutTodoItem method</span></span>

<span data-ttu-id="07d37-334">다음과 같이 `PutTodoItem` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-334">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="07d37-335">HTTP PUT을 사용하는 것을 제외하고 `PutTodoItem`는 `PostTodoItem`와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-335">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="07d37-336">응답은 [204(콘텐츠 없음)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-336">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="07d37-337">HTTP 사양에 따라 PUT 요청의 경우 클라이언트는 변경 내용만이 아니라 전체 업데이트된 엔터티를 보내야 합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-337">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="07d37-338">부분 업데이트를 지원하려면 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-338">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="07d37-339">`PutTodoItem`을 호출하는 중 오류가 발생하면 `GET`을 호출하여 데이터베이스에 항목이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-339">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="07d37-340">PutTodoItem 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="07d37-340">Test the PutTodoItem method</span></span>

<span data-ttu-id="07d37-341">이 샘플은 앱이 시작될 때마다 초기화되어야 하는 메모리 내 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-341">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="07d37-342">PUT 호출을 실행하기 전에 데이터베이스에 항목이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-342">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="07d37-343">GET을 호출하여 PUT 호출을 실행하기 전에 데이터베이스에 항목이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-343">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="07d37-344">ID = 1인 할 일 항목을 업데이트하고 해당 이름을 “feed fish”로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-344">Update the to-do item that has ID = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="07d37-345">다음 이미지는 Postman 업데이트를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-345">The following image shows the Postman update:</span></span>

![204(콘텐츠 없음) 응답을 보여주는 Postman 콘솔](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="07d37-347">DeleteTodoItem 메서드</span><span class="sxs-lookup"><span data-stu-id="07d37-347">The DeleteTodoItem method</span></span>

<span data-ttu-id="07d37-348">다음과 같이 `DeleteTodoItem` 메서드를 검사합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-348">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="07d37-349">DeleteTodoItem 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="07d37-349">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="07d37-350">Postman을 사용하여 할 일 항목을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-350">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="07d37-351">메서드를 `DELETE`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-351">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="07d37-352">예를 들어 삭제할 개체의 URI를 `https://localhost:5001/api/TodoItems/1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-352">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="07d37-353">**보내기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-353">Select **Send**.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="07d37-354">JavaScript를 사용하여 웹 API 호출</span><span class="sxs-lookup"><span data-stu-id="07d37-354">Call the web API with JavaScript</span></span>

<span data-ttu-id="07d37-355">[자습서: JavaScript로 ASP.NET Core 웹 API 호출하기](xref:tutorials/web-api-javascript)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="07d37-355">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="07d37-356">이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-356">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="07d37-357">웹 API 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-357">Create a web API project.</span></span>
> * <span data-ttu-id="07d37-358">모델 클래스와 데이터베이스 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-358">Add a model class and a database context.</span></span>
> * <span data-ttu-id="07d37-359">컨트롤러 추가</span><span class="sxs-lookup"><span data-stu-id="07d37-359">Add a controller.</span></span>
> * <span data-ttu-id="07d37-360">CRUD 메서드 추가</span><span class="sxs-lookup"><span data-stu-id="07d37-360">Add CRUD methods.</span></span>
> * <span data-ttu-id="07d37-361">라우팅 및 URL 경로 구성</span><span class="sxs-lookup"><span data-stu-id="07d37-361">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="07d37-362">반환 값 지정</span><span class="sxs-lookup"><span data-stu-id="07d37-362">Specify return values.</span></span>
> * <span data-ttu-id="07d37-363">Postman을 사용하여 웹 API를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-363">Call the web API with Postman.</span></span>
> * <span data-ttu-id="07d37-364">JavaScript를 사용하여 웹 API를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-364">Call the web API with JavaScript.</span></span>

<span data-ttu-id="07d37-365">작업을 완료하면 웹 API는 관계형 데이터베이스에 저장된 "할 일" 항목을 관리할 수 있게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-365">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="07d37-366">개요</span><span class="sxs-lookup"><span data-stu-id="07d37-366">Overview</span></span>

<span data-ttu-id="07d37-367">이 자습서에서는 다음 API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-367">This tutorial creates the following API:</span></span>

|<span data-ttu-id="07d37-368">API</span><span class="sxs-lookup"><span data-stu-id="07d37-368">API</span></span> | <span data-ttu-id="07d37-369">설명</span><span class="sxs-lookup"><span data-stu-id="07d37-369">Description</span></span> | <span data-ttu-id="07d37-370">요청 본문</span><span class="sxs-lookup"><span data-stu-id="07d37-370">Request body</span></span> | <span data-ttu-id="07d37-371">응답 본문</span><span class="sxs-lookup"><span data-stu-id="07d37-371">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="07d37-372">GET /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="07d37-372">GET /api/TodoItems</span></span> | <span data-ttu-id="07d37-373">할 일 항목 모두 가져오기</span><span class="sxs-lookup"><span data-stu-id="07d37-373">Get all to-do items</span></span> | <span data-ttu-id="07d37-374">없음</span><span class="sxs-lookup"><span data-stu-id="07d37-374">None</span></span> | <span data-ttu-id="07d37-375">할 일 항목의 배열</span><span class="sxs-lookup"><span data-stu-id="07d37-375">Array of to-do items</span></span>|
|<span data-ttu-id="07d37-376">GET /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="07d37-376">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="07d37-377">ID로 항목 가져오기</span><span class="sxs-lookup"><span data-stu-id="07d37-377">Get an item by ID</span></span> | <span data-ttu-id="07d37-378">없음</span><span class="sxs-lookup"><span data-stu-id="07d37-378">None</span></span> | <span data-ttu-id="07d37-379">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="07d37-379">To-do item</span></span>|
|<span data-ttu-id="07d37-380">POST /api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="07d37-380">POST /api/TodoItems</span></span> | <span data-ttu-id="07d37-381">새 항목 추가</span><span class="sxs-lookup"><span data-stu-id="07d37-381">Add a new item</span></span> | <span data-ttu-id="07d37-382">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="07d37-382">To-do item</span></span> | <span data-ttu-id="07d37-383">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="07d37-383">To-do item</span></span> |
|<span data-ttu-id="07d37-384">PUT /api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="07d37-384">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="07d37-385">기존 항목 업데이트 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="07d37-385">Update an existing item &nbsp;</span></span> | <span data-ttu-id="07d37-386">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="07d37-386">To-do item</span></span> | <span data-ttu-id="07d37-387">없음</span><span class="sxs-lookup"><span data-stu-id="07d37-387">None</span></span> |
|<span data-ttu-id="07d37-388">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="07d37-388">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="07d37-389">항목 삭제 &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="07d37-389">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="07d37-390">없음</span><span class="sxs-lookup"><span data-stu-id="07d37-390">None</span></span> | <span data-ttu-id="07d37-391">없음</span><span class="sxs-lookup"><span data-stu-id="07d37-391">None</span></span>|

<span data-ttu-id="07d37-392">다음 다이어그램에서는 앱의 디자인을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-392">The following diagram shows the design of the app.</span></span>

![클라이언트는 왼쪽에 상자로 표시됩니다.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="07d37-398">사전 요구 사항</span><span class="sxs-lookup"><span data-stu-id="07d37-398">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="07d37-399">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d37-399">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="07d37-400">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07d37-400">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="07d37-401">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d37-401">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="07d37-402">웹 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="07d37-402">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="07d37-403">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d37-403">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="07d37-404">**파일 메뉴**에서 **새로 만들기** > **프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-404">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="07d37-405">**ASP.NET Core 웹 애플리케이션** 템플릿을 선택하고 **다음**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-405">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="07d37-406">프로젝트 이름을 *TodoApi*로 지정하고 **만들기**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-406">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="07d37-407">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **.NET Core** 및 **ASP.NET Core 2.2**가 선택되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-407">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="07d37-408">**API** 템플릿을 선택하고 **만들기**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-408">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="07d37-409">**Docker 지원 사용**을 선택하지 **마세요**.</span><span class="sxs-lookup"><span data-stu-id="07d37-409">**Don't** select **Enable Docker Support**.</span></span>

![VS 새 프로젝트 대화 상자](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="07d37-411">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07d37-411">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="07d37-412">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-412">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="07d37-413">디렉터리(`cd`)를 프로젝트 폴더를 포함하는 폴더로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-413">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="07d37-414">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-414">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="07d37-415">이러한 명령은 새 웹 API 프로젝트를 만들고 새 프로젝트 폴더에서 Visual Studio Code의 새 인스턴스를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-415">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="07d37-416">프로젝트에 필수 자산을 추가하려는지 묻는 대화 상자가 나타나면 **예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-416">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="07d37-417">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d37-417">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="07d37-418">**파일** > **새 솔루션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-418">Select **File** > **New Solution**.</span></span>

  ![macOS 새 솔루션](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="07d37-420">**.NET Core** > **앱** > **API** > **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-420">Select **.NET Core** > **App** > **API** > **Next**.</span></span>

  ![macOS 새 프로젝트 대화 상자](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="07d37-422">**새 ASP.NET Core Web API 구성** 대화 상자에서 \* *.NET Core 2.2*라는 기본 **대상 프레임워크**를 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-422">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="07d37-423">**프로젝트 이름**으로 *TodoApi*를 입력한 다음, **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-423">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![구성 대화 상자](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="07d37-425">API 테스트</span><span class="sxs-lookup"><span data-stu-id="07d37-425">Test the API</span></span>

<span data-ttu-id="07d37-426">프로젝트 템플릿은 `values` API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-426">The project template creates a `values` API.</span></span> <span data-ttu-id="07d37-427">브라우저에서 `Get` 메서드를 호출하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-427">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="07d37-428">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d37-428">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="07d37-429">Ctrl+F5 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-429">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="07d37-430">Visual Studio가 브라우저를 시작하고 `https://localhost:<port>/api/values`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-430">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="07d37-431">IIS Express 인증서를 신뢰해야 하는지 묻는 대화 상자가 표시되면 **예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-431">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="07d37-432">다음으로, **보안 경고** 대화 상자가 나타나면 **예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-432">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="07d37-433">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07d37-433">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="07d37-434">Ctrl+F5 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-434">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="07d37-435">브라우저에서 다음 URL [https://localhost:5001/api/values](https://localhost:5001/api/values)로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-435">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="07d37-436">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d37-436">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="07d37-437">**실행** > **디버깅 시작**을 선택하여 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-437">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="07d37-438">Mac용 Visual Studio가 브라우저를 시작하고 `https://localhost:<port>`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-438">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="07d37-439">HTTP 404(찾을 수 없음) 오류가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-439">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="07d37-440">`/api/values`를 URL에 추가합니다(URL을 `https://localhost:<port>/api/values`로 변경).</span><span class="sxs-lookup"><span data-stu-id="07d37-440">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="07d37-441">다음 JSON이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-441">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="07d37-442">모델 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="07d37-442">Add a model class</span></span>

<span data-ttu-id="07d37-443">*모델*은 앱에서 관리하는 데이터를 나타내는 일련의 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-443">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="07d37-444">이 앱에 대한 모델은 단일 `TodoItem` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-444">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="07d37-445">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d37-445">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="07d37-446">**솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-446">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="07d37-447">**추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-447">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="07d37-448">폴더 이름을 *Models*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-448">Name the folder *Models*.</span></span>

* <span data-ttu-id="07d37-449">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-449">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="07d37-450">클래스 이름을 *TodoItem*으로 지정하고 **추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-450">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="07d37-451">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-451">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="07d37-452">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="07d37-452">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="07d37-453">*Models* 폴더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-453">Add a folder named *Models*.</span></span>

* <span data-ttu-id="07d37-454">다음 코드를 사용하여 *Models* 폴더에 `TodoItem` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-454">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="07d37-455">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d37-455">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="07d37-456">프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-456">Right-click the project.</span></span> <span data-ttu-id="07d37-457">**추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-457">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="07d37-458">폴더 이름을 *Models*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-458">Name the folder *Models*.</span></span>

  ![새 폴더](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="07d37-460">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 파일** > **일반** > **빈 클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-460">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="07d37-461">클래스 이름을 *TodoItem*으로 지정한 다음, **새로 만들기**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-461">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="07d37-462">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-462">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="07d37-463">`Id` 속성은 관계형 데이터베이스에서 고유 키로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-463">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="07d37-464">모델 클래스는 프로젝트의 어디로든 이동할 수 있지만 규칙에 따라 *Models* 폴더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-464">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="07d37-465">데이터베이스 컨텍스트 추가</span><span class="sxs-lookup"><span data-stu-id="07d37-465">Add a database context</span></span>

<span data-ttu-id="07d37-466">*데이터베이스 컨텍스트*는 데이터 모델에 맞게 Entity Framework 기능을 조정하는 주 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-466">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="07d37-467">`Microsoft.EntityFrameworkCore.DbContext` 클래스에서 파생시키는 방식으로 이 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-467">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="07d37-468">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d37-468">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="07d37-469">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-469">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="07d37-470">클래스 이름을 *TodoContext*로 지정하고 **추가**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-470">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="07d37-471">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d37-471">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="07d37-472">`TodoContext` 클래스를 *Models* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-472">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="07d37-473">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-473">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="07d37-474">데이터베이스 컨텍스트 등록</span><span class="sxs-lookup"><span data-stu-id="07d37-474">Register the database context</span></span>

<span data-ttu-id="07d37-475">ASP.NET Core에서는 DB 컨텍스트와 같은 서비스를 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 컨테이너에 등록해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-475">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="07d37-476">컨테이너는 컨트롤러에 서비스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-476">The container provides the service to controllers.</span></span>

<span data-ttu-id="07d37-477">*Startup.cs*를 다음 강조 표시된 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-477">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="07d37-478">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="07d37-478">The preceding code:</span></span>

* <span data-ttu-id="07d37-479">사용되지 않는 `using` 선언을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-479">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="07d37-480">DI 컨테이너에 데이터베이스 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-480">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="07d37-481">데이터베이스 컨텍스트가 메모리 내 데이터베이스를 사용하도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-481">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="07d37-482">컨트롤러 추가</span><span class="sxs-lookup"><span data-stu-id="07d37-482">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="07d37-483">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d37-483">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="07d37-484">*Controllers* 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-484">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="07d37-485">**추가** > **새 항목**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-485">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="07d37-486">**새 항목 추가** 대화 상자에서 **API 컨트롤러 클래스** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-486">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="07d37-487">클래스 이름을 *TodoController*로 지정하고 **추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-487">Name the class *TodoController*, and select **Add**.</span></span>

  ![검색 상자의 컨트롤러 및 웹 API 컨트롤러가 선택된 새 항목 추가 대화 상자](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="07d37-489">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d37-489">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="07d37-490">*Controllers* 폴더에 `TodoController`라는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-490">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="07d37-491">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-491">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="07d37-492">위의 코드는</span><span class="sxs-lookup"><span data-stu-id="07d37-492">The preceding code:</span></span>

* <span data-ttu-id="07d37-493">메서드 없이 API 컨트롤러 클래스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-493">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="07d37-494">[`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 특성으로 클래스를 표시합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-494">Marks the class with the [`[ApiController]`](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="07d37-495">이 특성은 컨트롤러가 웹 API 요청에 응답함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-495">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="07d37-496">특성을 사용하도록 설정하는 특정 동작에 대한 정보는 <xref:web-api/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="07d37-496">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="07d37-497">DI를 사용하여 데이터베이스 컨텍스트(`TodoContext`)를 컨트롤러에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-497">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="07d37-498">컨트롤러의 각 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드에서 해당 데이터베이스 컨텍스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-498">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="07d37-499">데이터베이스가 비어 있는 경우 데이터베이스에 `Item1`이라는 항목을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-499">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="07d37-500">이 코드는 생성자에 위치하므로 새 HTTP 요청이 발생할 때마다 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-500">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="07d37-501">모든 항목을 삭제하면 생성자는 다음에 API가 호출될 경우 `Item1`을 다시 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-501">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="07d37-502">따라서 실제로 작동되는 경우 삭제가 작동하지 않는 것처럼 보일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-502">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="07d37-503">GET 메서드 추가</span><span class="sxs-lookup"><span data-stu-id="07d37-503">Add Get methods</span></span>

<span data-ttu-id="07d37-504">할 일 항목을 가져오는 API를 제공하려면 다음 메서드를 `TodoController` 클래스에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-504">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="07d37-505">다음과 같은 메서드는 두 개의 GET 엔드포인트를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-505">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="07d37-506">앱이 계속 실행되고 있으면 중지합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-506">Stop the app if it's still running.</span></span> <span data-ttu-id="07d37-507">그런 다음, 다시 실행하여 최신 변경 내용을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-507">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="07d37-508">브라우저에서 두 개의 엔드포인트를 호출하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-508">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="07d37-509">예:</span><span class="sxs-lookup"><span data-stu-id="07d37-509">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="07d37-510">`GetTodoItems`를 호출하여 다음 HTTP 응답이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-510">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="07d37-511">라우팅 및 URL 경로</span><span class="sxs-lookup"><span data-stu-id="07d37-511">Routing and URL paths</span></span>

<span data-ttu-id="07d37-512">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 특성은 HTTP GET 요청에 응답하는 메서드를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-512">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="07d37-513">각 방법에 대한 URL 경로는 다음과 같이 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-513">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="07d37-514">컨트롤러의 `Route` 특성에서 템플릿 문자열로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-514">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="07d37-515">`[controller]`를 컨트롤러의 이름으로 바꿉니다. 일반적으로 컨트롤러 클래스 이름에서 "Controller" 접미사를 뺀 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-515">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="07d37-516">이 샘플의 경우 컨트롤러 클래스 이름은 **Todo**Controller이므로 컨트롤러 이름은 "todo"입니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-516">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="07d37-517">ASP.NET Core [라우팅](xref:mvc/controllers/routing)은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-517">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="07d37-518">`[HttpGet]` 특성에 경로 템플릿(예: `[HttpGet("products")]`)이 있는 경우 경로에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-518">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="07d37-519">이 샘플은 템플릿을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-519">This sample doesn't use a template.</span></span> <span data-ttu-id="07d37-520">자세한 내용은 [Http[동사] 특성을 사용한 특성 라우팅](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="07d37-520">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="07d37-521">다음 `GetTodoItem` 메서드에서 `"{id}"`는 할 일 항목의 고유 식별자에 대한 자리 표시자 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-521">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="07d37-522">`GetTodoItem`가 호출되면 URL의 `"{id}"` 값을 `id` 매개 변수의 메서드에 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-522">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="07d37-523">반환 값</span><span class="sxs-lookup"><span data-stu-id="07d37-523">Return values</span></span>

<span data-ttu-id="07d37-524">`GetTodoItems` 및 `GetTodoItem` 메서드의 반환 형식은 [ActionResult\<T> 형식](xref:web-api/action-return-types#actionresultt-type)입니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-524">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="07d37-525">ASP.NET Core는 자동으로 [JSON](https://www.json.org/)에 개체를 직렬화하고 JSON을 응답 메시지의 본문에 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-525">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="07d37-526">이 반환 형식의 응답 코드는 200이며 처리되지 않은 예외가 없다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-526">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="07d37-527">처리되지 않은 예외는 5xx 오류로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-527">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="07d37-528">`ActionResult` 반환 형식은 다양한 HTTP 상태 코드를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-528">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="07d37-529">예를 들어 `GetTodoItem`은 두 가지 상태 값을 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-529">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="07d37-530">요청된 ID와 일치하는 항목이 없는 경우 메서드가 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) 오류 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-530">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="07d37-531">그렇지 않으면 메서드가 JSON 응답 본문에서 200을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-531">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="07d37-532">`item`을 반환하면 HTTP 200 응답이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-532">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="07d37-533">GetTodoItems 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="07d37-533">Test the GetTodoItems method</span></span>

<span data-ttu-id="07d37-534">이 자습서에서는 Postman을 사용하여 웹 API를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-534">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="07d37-535">[Postman](https://www.getpostman.com/downloads/)을 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-535">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="07d37-536">웹앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-536">Start the web app.</span></span>
* <span data-ttu-id="07d37-537">Postman을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-537">Start Postman.</span></span>
* <span data-ttu-id="07d37-538">**SSL 인증서 확인**을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-538">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="07d37-539">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d37-539">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="07d37-540">**파일** > **설정**(**일반** 탭)에서 **SSL 인증서 확인**을 사용하지 않도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-540">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="07d37-541">Visual Studio Code / Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="07d37-541">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="07d37-542">**Postman** > **기본 설정**(**일반** 탭)에서 **SSL 인증서 확인**을 사용하지 않게 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-542">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="07d37-543">또는 렌치를 선택하고 **설정**을 선택한 다음 SSL 인증서 확인을 사용하지 않게 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-543">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="07d37-544">컨트롤러를 테스트한 후에 SSL 인증서 확인을 다시 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-544">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="07d37-545">새 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-545">Create a new request.</span></span>
  * <span data-ttu-id="07d37-546">HTTP 메서드를 **GET**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-546">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="07d37-547">요청 URL을 `https://localhost:<port>/api/todo`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-547">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="07d37-548">예: `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="07d37-548">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="07d37-549">Postman에서 **두 개의 창 보기**를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-549">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="07d37-550">**보내기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-550">Select **Send**.</span></span>

![Get 요청이 있는 Postman](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="07d37-552">메서드 만들기 추가</span><span class="sxs-lookup"><span data-stu-id="07d37-552">Add a Create method</span></span>

<span data-ttu-id="07d37-553">*Controllers/TodoController.cs* 내부에 다음 `PostTodoItem` 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-553">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="07d37-554">위의 코드는 [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 특성으로 표시되는 HTTP POST 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-554">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="07d37-555">이 메서드는 HTTP 요청 본문에서 할 일 항목 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-555">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="07d37-556">`CreatedAtAction` 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-556">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="07d37-557">성공 시 HTTP 201 상태 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-557">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="07d37-558">HTTP 201은 서버에서 새 리소스를 만드는 HTTP POST 메서드의 표준 응답입니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-558">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="07d37-559">`Location` 헤더를 응답에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-559">Adds a `Location` header to the response.</span></span> <span data-ttu-id="07d37-560">`Location` 헤더는 새로 만들어진 할 일 항목의 URI를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-560">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="07d37-561">자세한 내용은 [10.2.2 201 생성됨](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="07d37-561">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="07d37-562">`Location` 헤더의 URI를 만들려면 `GetTodoItem` 작업을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-562">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="07d37-563">C# `nameof` 키워드는 `CreatedAtAction` 호출에서 작업 이름의 하드 코딩을 방지하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-563">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="07d37-564">PostTodoItem 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="07d37-564">Test the PostTodoItem method</span></span>

* <span data-ttu-id="07d37-565">프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-565">Build the project.</span></span>
* <span data-ttu-id="07d37-566">Postman에서 HTTP 메서드를 `POST`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-566">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="07d37-567">**본문** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-567">Select the **Body** tab.</span></span>
* <span data-ttu-id="07d37-568">**원시** 라디오 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-568">Select the **raw** radio button.</span></span>
* <span data-ttu-id="07d37-569">유형을 **JSON(application/json)** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-569">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="07d37-570">요청 본문에서 할 일 항목에 대한 JSON을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-570">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="07d37-571">**보내기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-571">Select **Send**.</span></span>

  ![생성 요청이 있는 Postman](first-web-api/_static/create.png)

  <span data-ttu-id="07d37-573">405 메서드가 허용되지 않음 오류가 발생할 경우 `PostTodoItem` 메서드를 추가한 후에 프로젝트를 컴파일하지 않아서 발생한 결과일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-573">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="07d37-574">위치 헤더 URI 테스트</span><span class="sxs-lookup"><span data-stu-id="07d37-574">Test the location header URI</span></span>

* <span data-ttu-id="07d37-575">**응답** 창에서 **헤더** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-575">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="07d37-576">**위치** 헤더 값을 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-576">Copy the **Location** header value:</span></span>

  ![Postman 콘솔의 헤더 탭](first-web-api/_static/pmc2.png)

* <span data-ttu-id="07d37-578">메서드를 GET으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-578">Set the method to GET.</span></span>
* <span data-ttu-id="07d37-579">URI(예: `https://localhost:5001/api/Todo/2`)를 붙여넣습니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-579">Paste the URI (for example, `https://localhost:5001/api/Todo/2`).</span></span>
* <span data-ttu-id="07d37-580">**보내기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-580">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="07d37-581">PutTodoItem 메서드 추가</span><span class="sxs-lookup"><span data-stu-id="07d37-581">Add a PutTodoItem method</span></span>

<span data-ttu-id="07d37-582">다음 `PutTodoItem` 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-582">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="07d37-583">HTTP PUT을 사용하는 것을 제외하고 `PutTodoItem`는 `PostTodoItem`와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-583">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="07d37-584">응답은 [204(콘텐츠 없음)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-584">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="07d37-585">HTTP 사양에 따라 PUT 요청의 경우 클라이언트는 변경 내용만이 아니라 전체 업데이트된 엔터티를 보내야 합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-585">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="07d37-586">부분 업데이트를 지원하려면 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-586">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="07d37-587">`PutTodoItem`을 호출하는 중 오류가 발생하면 `GET`을 호출하여 데이터베이스에 항목이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-587">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="07d37-588">PutTodoItem 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="07d37-588">Test the PutTodoItem method</span></span>

<span data-ttu-id="07d37-589">이 샘플은 앱이 시작될 때마다 초기화되어야 하는 메모리 내 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-589">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="07d37-590">PUT 호출을 실행하기 전에 데이터베이스에 항목이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-590">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="07d37-591">GET을 호출하여 PUT 호출을 실행하기 전에 데이터베이스에 항목이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-591">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="07d37-592">ID = 1인 할 일 항목을 업데이트하고 해당 이름을 "feed fish"로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-592">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="07d37-593">다음 이미지는 Postman 업데이트를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-593">The following image shows the Postman update:</span></span>

![204(콘텐츠 없음) 응답을 보여주는 Postman 콘솔](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="07d37-595">DeleteTodoItem 메서드 추가</span><span class="sxs-lookup"><span data-stu-id="07d37-595">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="07d37-596">다음 `DeleteTodoItem` 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-596">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="07d37-597">`DeleteTodoItem` 응답은 [204(콘텐츠 없음)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-597">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="07d37-598">DeleteTodoItem 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="07d37-598">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="07d37-599">Postman을 사용하여 할 일 항목을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-599">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="07d37-600">메서드를 `DELETE`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-600">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="07d37-601">예를 들어 삭제할 개체의 URI를 `https://localhost:5001/api/todo/1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-601">Set the URI of the object to delete (for example `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="07d37-602">**보내기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-602">Select **Send**.</span></span>

<span data-ttu-id="07d37-603">샘플 앱을 사용하면 모든 항목을 삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-603">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="07d37-604">하지만 마지막 항목이 삭제되면 다음에 API를 호출하는 경우 모델 클래스 생성자에서 새로운 항목이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-604">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="07d37-605">JavaScript를 사용하여 웹 API 호출</span><span class="sxs-lookup"><span data-stu-id="07d37-605">Call the web API with JavaScript</span></span>

<span data-ttu-id="07d37-606">이 섹션에는 JavaScript를 사용하여 웹 API를 호출하는 HTML 페이지가 추가되었습니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-606">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="07d37-607">jQuery가 요청을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-607">jQuery initiates the request.</span></span> <span data-ttu-id="07d37-608">JavaScript는 웹 API 응답의 세부 정보를 토대로 페이지를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-608">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="07d37-609">다음 강조 표시된 코드로 *Startup.cs*를 업데이트하여 앱이 [정적 파일을 제공](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)하고 [기본 파일 매핑을 사용](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)하도록 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-609">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="07d37-610">프로젝트 디렉터리에서 *wwwroot* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-610">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="07d37-611">*index.html*이라는 HTML 파일을 *wwwroot* 디렉터리에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-611">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="07d37-612">다음 표시로 콘텐츠를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-612">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="07d37-613">*site.js*라는 JavaScript 파일을 *wwwroot* 디렉터리에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-613">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="07d37-614">다음 코드로 콘텐츠를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-614">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="07d37-615">HTML 페이지를 로컬에서 테스트하려면 ASP.NET Core 프로젝트의 시작 설정을 변경해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-615">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="07d37-616">*Properties\launchSettings.json*을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-616">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="07d37-617">`launchUrl` 속성을 제거하여 앱이 *index.html*&mdash; 프로젝트의 기본 파일에서 열리도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-617">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="07d37-618">이 샘플은 웹 API의 CRUD 메서드를 모두 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-618">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="07d37-619">API 호출에 대한 설명은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-619">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="07d37-620">할 일 항목의 목록 가져오기</span><span class="sxs-lookup"><span data-stu-id="07d37-620">Get a list of to-do items</span></span>

<span data-ttu-id="07d37-621">jQuery는 할 일 항목의 배열을 나타내는 JSON을 반환하는 웹 API에 HTTP GET 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-621">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="07d37-622">요청이 성공하면 `success` 콜백 함수가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-622">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="07d37-623">콜백에서 DOM은 할 일 정보로 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-623">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="07d37-624">할 일 항목 추가</span><span class="sxs-lookup"><span data-stu-id="07d37-624">Add a to-do item</span></span>

<span data-ttu-id="07d37-625">jQuery는 요청 본문에 있는 할 일 항목을 사용하여 HTTP POST 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-625">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="07d37-626">`accepts` 및 `contentType` 옵션은 수신 및 전송되는 미디어 형식을 지정하기 위해 `application/json`으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-626">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="07d37-627">[JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)를 사용하여 할 일 항목을 JSON으로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-627">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="07d37-628">API가 성공적인 상태 코드를 반환하면 `getData` 함수가 호출되어 HTML 테이블을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-628">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="07d37-629">할 일 항목 업데이트</span><span class="sxs-lookup"><span data-stu-id="07d37-629">Update a to-do item</span></span>

<span data-ttu-id="07d37-630">할 일 항목을 업데이트하는 작업은 추가하는 작업과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-630">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="07d37-631">`url`은 항목의 고유 식별자를 추가하도록 변경되고 `type`은 `PUT`입니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-631">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="07d37-632">할 일 항목 삭제</span><span class="sxs-lookup"><span data-stu-id="07d37-632">Delete a to-do item</span></span>

<span data-ttu-id="07d37-633">할 일 항목을 삭제하려면 AJAX 호출에서 `type`을 `DELETE`로 설정하고 URL에서 항목의 고유 식별자를 지정하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="07d37-633">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="07d37-634">웹 API에 인증 지원 추가</span><span class="sxs-lookup"><span data-stu-id="07d37-634">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources"></a><span data-ttu-id="07d37-635">추가 자료</span><span class="sxs-lookup"><span data-stu-id="07d37-635">Additional resources</span></span>

<span data-ttu-id="07d37-636">[이 자습서에서 샘플 코드 보기 또는 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)</span><span class="sxs-lookup"><span data-stu-id="07d37-636">[View or download sample code for this tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="07d37-637">[다운로드하는 방법](xref:index#how-to-download-a-sample)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="07d37-637">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="07d37-638">자세한 내용은 다음 자료를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="07d37-638">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="07d37-639">이 자습서의 YouTube 버전</span><span class="sxs-lookup"><span data-stu-id="07d37-639">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
