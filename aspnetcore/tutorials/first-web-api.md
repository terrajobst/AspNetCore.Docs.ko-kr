---
title: '자습서: ASP.NET Core를 사용하여 웹 API 만들기'
author: rick-anderson
description: ASP.NET Core를 사용하여 웹 API를 빌드하는 방법을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 06/23/2019
uid: tutorials/first-web-api
ms.openlocfilehash: a53f7019c1079296f073e743ddbf9d90fc5abad3
ms.sourcegitcommit: d6e51c60439f03a8992bda70cc982ddb15d3f100
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555871"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="94a66-103">자습서: ASP.NET Core를 사용하여 웹 API 만들기</span><span class="sxs-lookup"><span data-stu-id="94a66-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="94a66-104">작성자: [Rick Anderson](https://twitter.com/RickAndMSFT) 및 [Mike Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="94a66-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="94a66-105">이 자습서에서는 ASP.NET Core를 사용하여 웹 API를 빌드하는 작업의 기본 사항을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

<span data-ttu-id="94a66-106">이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="94a66-107">웹 API 프로젝트를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-107">Create a web API project.</span></span>
> * <span data-ttu-id="94a66-108">모델 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="94a66-108">Add a model class.</span></span>
> * <span data-ttu-id="94a66-109">데이터베이스 컨텍스트 만들기</span><span class="sxs-lookup"><span data-stu-id="94a66-109">Create the database context.</span></span>
> * <span data-ttu-id="94a66-110">데이터베이스 컨텍스트 등록</span><span class="sxs-lookup"><span data-stu-id="94a66-110">Register the database context.</span></span>
> * <span data-ttu-id="94a66-111">컨트롤러 추가</span><span class="sxs-lookup"><span data-stu-id="94a66-111">Add a controller.</span></span>
> * <span data-ttu-id="94a66-112">CRUD 메서드 추가</span><span class="sxs-lookup"><span data-stu-id="94a66-112">Add CRUD methods.</span></span>
> * <span data-ttu-id="94a66-113">라우팅 및 URL 경로 구성</span><span class="sxs-lookup"><span data-stu-id="94a66-113">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="94a66-114">반환 값 지정</span><span class="sxs-lookup"><span data-stu-id="94a66-114">Specify return values.</span></span>
> * <span data-ttu-id="94a66-115">Postman을 사용하여 웹 API 호출</span><span class="sxs-lookup"><span data-stu-id="94a66-115">Call the web API with Postman.</span></span>
> * <span data-ttu-id="94a66-116">jQuery를 사용하여 웹 API를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-116">Call the web API with jQuery.</span></span>

<span data-ttu-id="94a66-117">작업을 완료하면 웹 API는 관계형 데이터베이스에 저장된 "할 일" 항목을 관리할 수 있게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-117">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview"></a><span data-ttu-id="94a66-118">개요</span><span class="sxs-lookup"><span data-stu-id="94a66-118">Overview</span></span>

<span data-ttu-id="94a66-119">이 자습서에서는 다음 API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-119">This tutorial creates the following API:</span></span>

|<span data-ttu-id="94a66-120">API</span><span class="sxs-lookup"><span data-stu-id="94a66-120">API</span></span> | <span data-ttu-id="94a66-121">설명</span><span class="sxs-lookup"><span data-stu-id="94a66-121">Description</span></span> | <span data-ttu-id="94a66-122">요청 본문</span><span class="sxs-lookup"><span data-stu-id="94a66-122">Request body</span></span> | <span data-ttu-id="94a66-123">응답 본문</span><span class="sxs-lookup"><span data-stu-id="94a66-123">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="94a66-124">GET /api/todo</span><span class="sxs-lookup"><span data-stu-id="94a66-124">GET /api/todo</span></span> | <span data-ttu-id="94a66-125">할 일 항목 모두 가져오기</span><span class="sxs-lookup"><span data-stu-id="94a66-125">Get all to-do items</span></span> | <span data-ttu-id="94a66-126">없음</span><span class="sxs-lookup"><span data-stu-id="94a66-126">None</span></span> | <span data-ttu-id="94a66-127">할 일 항목의 배열</span><span class="sxs-lookup"><span data-stu-id="94a66-127">Array of to-do items</span></span>|
|<span data-ttu-id="94a66-128">GET /api/todo/{id}</span><span class="sxs-lookup"><span data-stu-id="94a66-128">GET /api/todo/{id}</span></span> | <span data-ttu-id="94a66-129">ID로 항목 가져오기</span><span class="sxs-lookup"><span data-stu-id="94a66-129">Get an item by ID</span></span> | <span data-ttu-id="94a66-130">없음</span><span class="sxs-lookup"><span data-stu-id="94a66-130">None</span></span> | <span data-ttu-id="94a66-131">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="94a66-131">To-do item</span></span>|
|<span data-ttu-id="94a66-132">POST /api/todo</span><span class="sxs-lookup"><span data-stu-id="94a66-132">POST /api/todo</span></span> | <span data-ttu-id="94a66-133">새 항목 추가</span><span class="sxs-lookup"><span data-stu-id="94a66-133">Add a new item</span></span> | <span data-ttu-id="94a66-134">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="94a66-134">To-do item</span></span> | <span data-ttu-id="94a66-135">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="94a66-135">To-do item</span></span> |
|<span data-ttu-id="94a66-136">PUT /api/todo/{id}</span><span class="sxs-lookup"><span data-stu-id="94a66-136">PUT /api/todo/{id}</span></span> | <span data-ttu-id="94a66-137">기존 항목 업데이트 &nbsp;</span><span class="sxs-lookup"><span data-stu-id="94a66-137">Update an existing item &nbsp;</span></span> | <span data-ttu-id="94a66-138">할 일 항목</span><span class="sxs-lookup"><span data-stu-id="94a66-138">To-do item</span></span> | <span data-ttu-id="94a66-139">없음</span><span class="sxs-lookup"><span data-stu-id="94a66-139">None</span></span> |
|<span data-ttu-id="94a66-140">DELETE /api/todo/{id} &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="94a66-140">DELETE /api/todo/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="94a66-141">항목 삭제 &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="94a66-141">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="94a66-142">없음</span><span class="sxs-lookup"><span data-stu-id="94a66-142">None</span></span> | <span data-ttu-id="94a66-143">없음</span><span class="sxs-lookup"><span data-stu-id="94a66-143">None</span></span>|

<span data-ttu-id="94a66-144">다음 다이어그램에서는 앱의 디자인을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-144">The following diagram shows the design of the app.</span></span>

![클라이언트는 왼쪽에 상자로 표시됩니다.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="94a66-150">전제 조건</span><span class="sxs-lookup"><span data-stu-id="94a66-150">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="94a66-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94a66-151">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="94a66-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94a66-152">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="94a66-153">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="94a66-153">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="94a66-154">웹 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="94a66-154">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="94a66-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94a66-155">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="94a66-156">**파일** 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-156">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="94a66-157">**ASP.NET Core 웹 애플리케이션** 템플릿을 선택하고 **다음**을 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-157">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="94a66-158">프로젝트 이름을 *TodoApi*로 지정하고 **만들기**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-158">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="94a66-159">**새 ASP.NET Core 웹 애플리케이션 만들기** 대화 상자에서 **.NET Core** 및 **ASP.NET Core 2.2**가 선택되었는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-159">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="94a66-160">**API** 템플릿을 선택하고 **만들기**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-160">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="94a66-161">**Docker 지원 사용**을 선택하지 **마세요**.</span><span class="sxs-lookup"><span data-stu-id="94a66-161">**Don't** select **Enable Docker Support**.</span></span>

![VS 새 프로젝트 대화 상자](first-web-api/_static/vs.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="94a66-163">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94a66-163">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="94a66-164">[통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-164">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="94a66-165">디렉터리(`cd`)를 프로젝트 폴더를 포함하는 폴더로 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-165">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="94a66-166">다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-166">Run the following commands:</span></span>

   ```console
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="94a66-167">이러한 명령은 새 웹 API 프로젝트를 만들고 새 프로젝트 폴더에서 Visual Studio Code의 새 인스턴스를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-167">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="94a66-168">프로젝트에 필수 자산을 추가하려는지 묻는 대화 상자가 나타나면 **예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-168">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="94a66-169">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="94a66-169">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="94a66-170">**파일** > **새 솔루션**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-170">Select **File** > **New Solution**.</span></span>

  ![macOS 새 솔루션](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="94a66-172">**.NET Core App** > **ASP.NET Core Web API** > **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-172">Select **.NET Core App** > **ASP.NET Core Web API** > **Next**.</span></span>

  ![macOS 새 프로젝트 대화 상자](first-web-api-mac/_static/1.png)
  
* <span data-ttu-id="94a66-174">**새 ASP.NET Core Web API 구성** 대화 상자에서 \* *.NET Core 2.2*라는 기본 **대상 프레임워크**를 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-174">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of \**.NET Core 2.2*.</span></span>

* <span data-ttu-id="94a66-175">**프로젝트 이름**으로 *TodoApi*를 입력한 다음, **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-175">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![구성 대화 상자](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api"></a><span data-ttu-id="94a66-177">API 테스트</span><span class="sxs-lookup"><span data-stu-id="94a66-177">Test the API</span></span>

<span data-ttu-id="94a66-178">프로젝트 템플릿은 `values` API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-178">The project template creates a `values` API.</span></span> <span data-ttu-id="94a66-179">브라우저에서 `Get` 메서드를 호출하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-179">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="94a66-180">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94a66-180">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="94a66-181">Ctrl+F5 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-181">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="94a66-182">Visual Studio가 브라우저를 시작하고 `https://localhost:<port>/api/values`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-182">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="94a66-183">IIS Express 인증서를 신뢰해야 하는지 묻는 대화 상자가 표시되면 **예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-183">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="94a66-184">다음으로, **보안 경고** 대화 상자가 나타나면 **예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-184">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="94a66-185">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94a66-185">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="94a66-186">Ctrl+F5 키를 눌러 앱을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-186">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="94a66-187">브라우저에서 다음 URL [https://localhost:5001/api/values](https://localhost:5001/api/values)로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-187">In a browser, go to following URL: [https://localhost:5001/api/values](https://localhost:5001/api/values).</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="94a66-188">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="94a66-188">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="94a66-189">**실행** > **디버깅 시작**을 선택하여 앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-189">Select **Run** > **Start With Debugging** to launch the app.</span></span> <span data-ttu-id="94a66-190">Mac용 Visual Studio가 브라우저를 시작하고 `https://localhost:<port>`로 이동합니다. 여기서 `<port>`는 임의로 선택된 포트 번호입니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-190">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="94a66-191">HTTP 404(찾을 수 없음) 오류가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-191">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="94a66-192">`/api/values`를 URL에 추가합니다(URL을 `https://localhost:<port>/api/values`로 변경).</span><span class="sxs-lookup"><span data-stu-id="94a66-192">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="94a66-193">다음 JSON이 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-193">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class"></a><span data-ttu-id="94a66-194">모델 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="94a66-194">Add a model class</span></span>

<span data-ttu-id="94a66-195">*모델*은 앱에서 관리하는 데이터를 나타내는 일련의 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-195">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="94a66-196">이 앱에 대한 모델은 단일 `TodoItem` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-196">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="94a66-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94a66-197">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="94a66-198">**솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-198">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="94a66-199">**추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-199">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="94a66-200">폴더 이름을 *Models*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-200">Name the folder *Models*.</span></span>

* <span data-ttu-id="94a66-201">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-201">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="94a66-202">클래스 이름을 *TodoItem*으로 지정하고 **추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-202">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="94a66-203">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-203">Replace the template code with the following code:</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="94a66-204">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94a66-204">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="94a66-205">*Models* 폴더를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-205">Add a folder named *Models*.</span></span>

* <span data-ttu-id="94a66-206">다음 코드를 사용하여 *Models* 폴더에 `TodoItem` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-206">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="94a66-207">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="94a66-207">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="94a66-208">프로젝트를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-208">Right-click the project.</span></span> <span data-ttu-id="94a66-209">**추가** > **새 폴더**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-209">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="94a66-210">폴더 이름을 *Models*로 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-210">Name the folder *Models*.</span></span>

  ![새 폴더](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="94a66-212">*모델* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 파일** > **일반** > **빈 클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-212">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="94a66-213">클래스 이름을 *TodoItem*으로 지정한 다음, **새로 만들기**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-213">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="94a66-214">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-214">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="94a66-215">`Id` 속성은 관계형 데이터베이스에서 고유 키로 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-215">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="94a66-216">모델 클래스는 프로젝트의 어디로든 이동할 수 있지만 규칙에 따라 *Models* 폴더를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-216">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="94a66-217">데이터베이스 컨텍스트 추가</span><span class="sxs-lookup"><span data-stu-id="94a66-217">Add a database context</span></span>

<span data-ttu-id="94a66-218">*데이터베이스 컨텍스트*는 데이터 모델에 맞게 Entity Framework 기능을 조정하는 주 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-218">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="94a66-219">`Microsoft.EntityFrameworkCore.DbContext` 클래스에서 파생시키는 방식으로 이 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-219">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="94a66-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94a66-220">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="94a66-221">*Models* 폴더를 마우스 오른쪽 단추로 클릭하고 **추가** > **클래스**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-221">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="94a66-222">클래스 이름을 *TodoContext*로 지정하고 **추가**를 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-222">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="94a66-223">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="94a66-223">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="94a66-224">`TodoContext` 클래스를 *Models* 폴더에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-224">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="94a66-225">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-225">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="94a66-226">데이터베이스 컨텍스트 등록</span><span class="sxs-lookup"><span data-stu-id="94a66-226">Register the database context</span></span>

<span data-ttu-id="94a66-227">ASP.NET Core에서는 DB 컨텍스트와 같은 서비스를 [DI(종속성 주입)](xref:fundamentals/dependency-injection) 컨테이너에 등록해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-227">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="94a66-228">컨테이너는 컨트롤러에 서비스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-228">The container provides the service to controllers.</span></span>

<span data-ttu-id="94a66-229">*Startup.cs*를 다음 강조 표시된 코드로 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-229">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="94a66-230">위의 코드는:</span><span class="sxs-lookup"><span data-stu-id="94a66-230">The preceding code:</span></span>

* <span data-ttu-id="94a66-231">사용되지 않는 `using` 선언을 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-231">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="94a66-232">DI 컨테이너에 데이터베이스 컨텍스트를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-232">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="94a66-233">데이터베이스 컨텍스트가 메모리 내 데이터베이스를 사용하도록 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-233">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="94a66-234">컨트롤러 추가</span><span class="sxs-lookup"><span data-stu-id="94a66-234">Add a controller</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="94a66-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94a66-235">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="94a66-236">*Controllers* 폴더를 마우스 오른쪽 단추로 클릭합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-236">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="94a66-237">**추가** > **새 항목**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-237">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="94a66-238">**새 항목 추가** 대화 상자에서 **API 컨트롤러 클래스** 템플릿을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-238">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="94a66-239">클래스 이름을 *TodoController*로 지정하고 **추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-239">Name the class *TodoController*, and select **Add**.</span></span>

  ![검색 상자의 컨트롤러 및 웹 API 컨트롤러가 선택된 새 항목 추가 대화 상자](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="94a66-241">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="94a66-241">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="94a66-242">*Controllers* 폴더에 `TodoController`라는 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-242">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="94a66-243">템플릿 코드를 다음 코드로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-243">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="94a66-244">위의 코드는:</span><span class="sxs-lookup"><span data-stu-id="94a66-244">The preceding code:</span></span>

* <span data-ttu-id="94a66-245">메서드 없이 API 컨트롤러 클래스를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-245">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="94a66-246">[[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) 특성을 사용하여 클래스를 데코레이팅합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-246">Decorates the class with the [[ApiController]](/dotnet/api/microsoft.aspnetcore.mvc.apicontrollerattribute) attribute.</span></span> <span data-ttu-id="94a66-247">이 특성은 컨트롤러가 웹 API 요청에 응답함을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-247">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="94a66-248">특성을 사용하도록 설정하는 특정 동작에 대한 정보는 <xref:web-api/index>를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="94a66-248">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="94a66-249">DI를 사용하여 데이터베이스 컨텍스트(`TodoContext`)를 컨트롤러에 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-249">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="94a66-250">컨트롤러의 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 메서드 각각에서 데이터베이스 컨텍스트를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-250">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="94a66-251">데이터베이스가 비어 있는 경우 데이터베이스에 `Item1`이라는 항목을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-251">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="94a66-252">이 코드는 생성자에 위치하므로 새 HTTP 요청이 발생할 때마다 실행됩니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-252">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="94a66-253">모든 항목을 삭제하면 생성자는 다음에 API가 호출될 경우 `Item1`을 다시 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-253">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="94a66-254">따라서 실제로 작동되는 경우 삭제가 작동하지 않는 것처럼 보일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-254">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods"></a><span data-ttu-id="94a66-255">GET 메서드 추가</span><span class="sxs-lookup"><span data-stu-id="94a66-255">Add Get methods</span></span>

<span data-ttu-id="94a66-256">할 일 항목을 가져오는 API를 제공하려면 다음 메서드를 `TodoController` 클래스에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-256">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="94a66-257">다음과 같은 메서드는 두 개의 GET 엔드포인트를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-257">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="94a66-258">브라우저에서 두 개의 엔드포인트를 호출하여 앱을 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-258">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="94a66-259">예:</span><span class="sxs-lookup"><span data-stu-id="94a66-259">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="94a66-260">`GetTodoItems`를 호출하여 다음 HTTP 응답이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-260">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths"></a><span data-ttu-id="94a66-261">라우팅 및 URL 경로</span><span class="sxs-lookup"><span data-stu-id="94a66-261">Routing and URL paths</span></span>

<span data-ttu-id="94a66-262">[`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) 특성은 HTTP GET 요청에 응답하는 메서드를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-262">The [`[HttpGet]`](/dotnet/api/microsoft.aspnetcore.mvc.httpgetattribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="94a66-263">각 방법에 대한 URL 경로는 다음과 같이 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-263">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="94a66-264">컨트롤러의 `Route` 특성에서 템플릿 문자열로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-264">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="94a66-265">`[controller]`를 컨트롤러의 이름으로 바꿉니다. 일반적으로 컨트롤러 클래스 이름에서 "Controller" 접미사를 뺀 이름입니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-265">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="94a66-266">이 샘플의 경우 컨트롤러 클래스 이름은 **Todo**Controller이므로 컨트롤러 이름은 "todo"입니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-266">For this sample, the controller class name is **Todo**Controller, so the controller name is "todo".</span></span> <span data-ttu-id="94a66-267">ASP.NET Core [라우팅](xref:mvc/controllers/routing)은 대/소문자를 구분하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-267">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="94a66-268">`[HttpGet]` 특성에 경로 템플릿(예: `[HttpGet("products")]`)이 있는 경우 경로에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-268">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="94a66-269">이 샘플은 템플릿을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-269">This sample doesn't use a template.</span></span> <span data-ttu-id="94a66-270">자세한 내용은 [Http[동사] 특성을 사용한 특성 라우팅](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="94a66-270">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="94a66-271">다음 `GetTodoItem` 메서드에서 `"{id}"`는 할 일 항목의 고유 식별자에 대한 자리 표시자 변수입니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-271">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="94a66-272">`GetTodoItem`가 호출되면 URL의 `"{id}"` 값을 `id` 매개 변수의 메서드에 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-272">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="94a66-273">반환 값</span><span class="sxs-lookup"><span data-stu-id="94a66-273">Return values</span></span>

<span data-ttu-id="94a66-274">`GetTodoItems` 및 `GetTodoItem` 메서드의 반환 형식은 [ActionResult\<T> 형식](xref:web-api/action-return-types#actionresultt-type)입니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-274">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="94a66-275">ASP.NET Core는 자동으로 [JSON](https://www.json.org/)에 개체를 직렬화하고 JSON을 응답 메시지의 본문에 기록합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-275">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="94a66-276">이 반환 형식의 응답 코드는 200이며 처리되지 않은 예외가 없다고 가정합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-276">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="94a66-277">처리되지 않은 예외는 5xx 오류로 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-277">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="94a66-278">`ActionResult` 반환 형식은 다양한 HTTP 상태 코드를 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-278">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="94a66-279">예를 들어 `GetTodoItem`은 두 가지 상태 값을 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-279">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="94a66-280">요청된 ID와 일치하는 항목이 없는 경우 메서드가 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) 오류 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-280">If no item matches the requested ID, the method returns a 404 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) error code.</span></span>
* <span data-ttu-id="94a66-281">그렇지 않으면 메서드가 JSON 응답 본문에서 200을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-281">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="94a66-282">`item`을 반환하면 HTTP 200 응답이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-282">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method"></a><span data-ttu-id="94a66-283">GetTodoItems 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="94a66-283">Test the GetTodoItems method</span></span>

<span data-ttu-id="94a66-284">이 자습서에서는 Postman을 사용하여 웹 API를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-284">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="94a66-285">[Postman](https://www.getpostman.com/downloads/) 설치</span><span class="sxs-lookup"><span data-stu-id="94a66-285">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="94a66-286">웹앱을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-286">Start the web app.</span></span>
* <span data-ttu-id="94a66-287">Postman을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-287">Start Postman.</span></span>
* <span data-ttu-id="94a66-288">**SSL 인증서 확인** 사용 안 함</span><span class="sxs-lookup"><span data-stu-id="94a66-288">Disable **SSL certificate verification**</span></span>
  
  * <span data-ttu-id="94a66-289">**파일 > 설정**(\**일반* 탭)에서 **SSL 인증서 확인**을 사용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-289">From  **File > Settings** (\**General* tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="94a66-290">컨트롤러를 테스트한 후에 SSL 인증서 확인을 다시 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-290">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="94a66-291">새 요청을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-291">Create a new request.</span></span>
  * <span data-ttu-id="94a66-292">HTTP 메서드를 **GET**으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-292">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="94a66-293">요청 URL을 `https://localhost:<port>/api/todo`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-293">Set the request URL to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="94a66-294">예를 들어, `https://localhost:5001/api/todo`을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-294">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="94a66-295">Postman에서 **두 개의 창 보기**를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-295">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="94a66-296">**보내기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-296">Select **Send**.</span></span>

![Get 요청이 있는 Postman](first-web-api/_static/2pv.png)

## <a name="add-a-create-method"></a><span data-ttu-id="94a66-298">메서드 만들기 추가</span><span class="sxs-lookup"><span data-stu-id="94a66-298">Add a Create method</span></span>

<span data-ttu-id="94a66-299">다음 `PostTodoItem` 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-299">Add the following `PostTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="94a66-300">이전 코드는 [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) 특성으로 표시되는 HTTP POST 메서드입니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-300">The preceding code is an HTTP POST method, as indicated by the [[HttpPost]](/dotnet/api/microsoft.aspnetcore.mvc.httppostattribute) attribute.</span></span> <span data-ttu-id="94a66-301">이 메서드는 HTTP 요청 본문에서 할 일 항목 값을 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-301">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="94a66-302">`CreatedAtAction` 메서드는 다음 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-302">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="94a66-303">성공 시 HTTP 201 상태 코드를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-303">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="94a66-304">HTTP 201은 서버에서 새 리소스를 만드는 HTTP POST 메서드의 표준 응답입니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-304">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="94a66-305">`Location` 헤더를 응답에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-305">Adds a `Location` header to the response.</span></span> <span data-ttu-id="94a66-306">`Location` 헤더는 새로 만들어진 할 일 항목의 URI를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-306">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="94a66-307">자세한 내용은 [10.2.2 201 생성됨](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="94a66-307">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="94a66-308">`Location` 헤더의 URI를 만들려면 `GetTodoItem` 작업을 참조합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-308">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="94a66-309">C# `nameof` 키워드는 `CreatedAtAction` 호출에서 작업 이름의 하드 코딩을 방지하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-309">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method"></a><span data-ttu-id="94a66-310">PostTodoItem 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="94a66-310">Test the PostTodoItem method</span></span>

* <span data-ttu-id="94a66-311">프로젝트를 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-311">Build the project.</span></span>
* <span data-ttu-id="94a66-312">Postman에서 HTTP 메서드를 `POST`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-312">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="94a66-313">**본문** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-313">Select the **Body** tab.</span></span>
* <span data-ttu-id="94a66-314">**원시** 라디오 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-314">Select the **raw** radio button.</span></span>
* <span data-ttu-id="94a66-315">유형을 **JSON(application/json)** 으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-315">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="94a66-316">요청 본문에서 할 일 항목에 대한 JSON을 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-316">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="94a66-317">**보내기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-317">Select **Send**.</span></span>

  ![생성 요청이 있는 Postman](first-web-api/_static/create.png)

  <span data-ttu-id="94a66-319">405 메서드가 허용되지 않음 오류가 발생할 경우 `PostTodoItem` 메서드를 추가한 후에 프로젝트를 컴파일하지 않아서 발생한 결과일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-319">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri"></a><span data-ttu-id="94a66-320">위치 헤더 URI 테스트</span><span class="sxs-lookup"><span data-stu-id="94a66-320">Test the location header URI</span></span>

* <span data-ttu-id="94a66-321">**응답** 창에서 **헤더** 탭을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-321">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="94a66-322">**위치** 헤더 값을 복사합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-322">Copy the **Location** header value:</span></span>

  ![Postman 콘솔의 헤더 탭](first-web-api/_static/pmc2.png)

* <span data-ttu-id="94a66-324">메서드를 GET으로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-324">Set the method to GET.</span></span>
* <span data-ttu-id="94a66-325">URI(예: `https://localhost:5001/api/Todo/2`) 붙여넣기</span><span class="sxs-lookup"><span data-stu-id="94a66-325">Paste the URI (for example, `https://localhost:5001/api/Todo/2`)</span></span>
* <span data-ttu-id="94a66-326">**보내기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-326">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method"></a><span data-ttu-id="94a66-327">PutTodoItem 메서드 추가</span><span class="sxs-lookup"><span data-stu-id="94a66-327">Add a PutTodoItem method</span></span>

<span data-ttu-id="94a66-328">다음 `PutTodoItem` 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-328">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="94a66-329">HTTP PUT을 사용하는 것을 제외하고 `PutTodoItem`는 `PostTodoItem`와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-329">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="94a66-330">응답은 [204(콘텐츠 없음)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-330">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="94a66-331">HTTP 사양에 따라 PUT 요청의 경우 클라이언트는 변경 내용만이 아니라 전체 업데이트된 엔터티를 보내야 합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-331">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="94a66-332">부분 업데이트를 지원하려면 [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-332">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="94a66-333">`PutTodoItem`을 호출하는 중 오류가 발생하면 `GET`을 호출하여 데이터베이스에 항목이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-333">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="94a66-334">PutTodoItem 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="94a66-334">Test the PutTodoItem method</span></span>

<span data-ttu-id="94a66-335">이 샘플은 앱이 시작될 때마다 초기화되어야 하는 메모리 내 데이터베이스를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-335">This sample uses an in-memory database that must be initialed each time the app is started.</span></span> <span data-ttu-id="94a66-336">PUT 호출을 실행하기 전에 데이터베이스에 항목이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-336">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="94a66-337">GET을 호출하여 PUT 호출을 실행하기 전에 데이터베이스에 항목이 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-337">Call GET to insure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="94a66-338">ID = 1인 할 일 항목을 업데이트하고 해당 이름을 "feed fish"로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-338">Update the to-do item that has id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "ID":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="94a66-339">다음 이미지는 Postman 업데이트를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-339">The following image shows the Postman update:</span></span>

![204(콘텐츠 없음) 응답을 보여주는 Postman 콘솔](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method"></a><span data-ttu-id="94a66-341">DeleteTodoItem 메서드 추가</span><span class="sxs-lookup"><span data-stu-id="94a66-341">Add a DeleteTodoItem method</span></span>

<span data-ttu-id="94a66-342">다음 `DeleteTodoItem` 메서드를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-342">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="94a66-343">`DeleteTodoItem` 응답은 [204(콘텐츠 없음)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)입니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-343">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="94a66-344">DeleteTodoItem 메서드 테스트</span><span class="sxs-lookup"><span data-stu-id="94a66-344">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="94a66-345">Postman을 사용하여 할 일 항목을 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-345">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="94a66-346">메서드를 `DELETE`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-346">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="94a66-347">예를 들어 삭제할 개체의 URI를 `https://localhost:5001/api/todo/1`로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-347">Set the URI of the object to delete, for example `https://localhost:5001/api/todo/1`</span></span>
* <span data-ttu-id="94a66-348">**보내기** 선택</span><span class="sxs-lookup"><span data-stu-id="94a66-348">Select **Send**</span></span>

<span data-ttu-id="94a66-349">샘플 앱을 사용하면 모든 항목을 삭제할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-349">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="94a66-350">하지만 마지막 항목이 삭제되면 다음에 API를 호출하는 경우 모델 클래스 생성자에서 새로운 항목이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-350">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-api-with-jquery"></a><span data-ttu-id="94a66-351">jQuery를 사용하여 API 호출</span><span class="sxs-lookup"><span data-stu-id="94a66-351">Call the API with jQuery</span></span>

<span data-ttu-id="94a66-352">이 섹션에서는 jQuery를 사용하여 웹 API를 호출하는 HTML 페이지가 추가되었습니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-352">In this section, an HTML page is added that uses jQuery to call the web api.</span></span> <span data-ttu-id="94a66-353">jQuery는 요청을 시작하고 API 응답의 세부 정보로 페이지를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-353">jQuery initiates the request and updates the page with the details from the API's response.</span></span>

<span data-ttu-id="94a66-354">다음 강조 표시된 코드로 *Startup.cs*를 업데이트하여 앱이 [정적 파일을 제공](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)하고 [기본 파일 매핑을 사용](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_)하도록 구성합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-354">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

::: moniker range=">= aspnetcore-2.2"
<span data-ttu-id="94a66-355">프로젝트 디렉터리에서 *wwwroot* 폴더를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-355">Create a *wwwroot* folder in the project directory.</span></span>
::: moniker-end

<span data-ttu-id="94a66-356">*index.html*이라는 HTML 파일을 *wwwroot* 디렉터리에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-356">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="94a66-357">다음 표시로 콘텐츠를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-357">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="94a66-358">*site.js*라는 JavaScript 파일을 *wwwroot* 디렉터리에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-358">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="94a66-359">다음 코드로 콘텐츠를 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-359">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="94a66-360">HTML 페이지를 로컬에서 테스트하려면 ASP.NET Core 프로젝트의 시작 설정을 변경해야 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-360">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="94a66-361">*Properties\launchSettings.json*을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-361">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="94a66-362">`launchUrl` 속성을 제거하여 앱이 *index.html*&mdash; 프로젝트의 기본 파일에서 열리도록 합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-362">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="94a66-363">여러 가지 방법으로 jQuery를 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-363">There are several ways to get jQuery.</span></span> <span data-ttu-id="94a66-364">위의 코드 조각에서 라이브러리는 CDN에서 로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-364">In the preceding snippet, the library is loaded from a CDN.</span></span>

<span data-ttu-id="94a66-365">이 샘플은 API의 모든 CRUD 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-365">This sample calls all of the CRUD methods of the API.</span></span> <span data-ttu-id="94a66-366">API 호출에 대한 설명은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-366">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="94a66-367">할 일 항목의 목록 가져오기</span><span class="sxs-lookup"><span data-stu-id="94a66-367">Get a list of to-do items</span></span>

<span data-ttu-id="94a66-368">jQuery [ajax](https://api.jquery.com/jquery.ajax/) 함수는 할 일 항목의 배열을 나타내는 JSON을 반환하는 API에 `GET` 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-368">The jQuery [ajax](https://api.jquery.com/jquery.ajax/) function sends a `GET` request to the API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="94a66-369">요청이 성공하면 `success` 콜백 함수가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-369">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="94a66-370">콜백에서 DOM은 할 일 정보로 업데이트됩니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-370">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item"></a><span data-ttu-id="94a66-371">할 일 항목 추가</span><span class="sxs-lookup"><span data-stu-id="94a66-371">Add a to-do item</span></span>

<span data-ttu-id="94a66-372">[ajax](https://api.jquery.com/jquery.ajax/) 함수는 `POST` 요청 본문에서 할 일 항목을 사용하여 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-372">The [ajax](https://api.jquery.com/jquery.ajax/) function sends a `POST` request with the to-do item in the request body.</span></span> <span data-ttu-id="94a66-373">`accepts` 및 `contentType` 옵션은 수신 및 전송되는 미디어 형식을 지정하기 위해 `application/json`으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-373">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="94a66-374">[JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)를 사용하여 할 일 항목을 JSON으로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-374">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="94a66-375">API가 성공적인 상태 코드를 반환하면 `getData` 함수가 호출되어 HTML 테이블을 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-375">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item"></a><span data-ttu-id="94a66-376">할 일 항목 업데이트</span><span class="sxs-lookup"><span data-stu-id="94a66-376">Update a to-do item</span></span>

<span data-ttu-id="94a66-377">할 일 항목을 업데이트하는 작업은 추가하는 작업과 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-377">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="94a66-378">`url`은 항목의 고유 식별자를 추가하도록 변경되고 `type`은 `PUT`입니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-378">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="94a66-379">할 일 항목 삭제</span><span class="sxs-lookup"><span data-stu-id="94a66-379">Delete a to-do item</span></span>

<span data-ttu-id="94a66-380">할 일 항목을 삭제하려면 AJAX 호출에서 `type`을 `DELETE`로 설정하고 URL에서 항목의 고유 식별자를 지정하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-380">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

## <a name="additional-resources"></a><span data-ttu-id="94a66-381">추가 자료</span><span class="sxs-lookup"><span data-stu-id="94a66-381">Additional resources</span></span>

<span data-ttu-id="94a66-382">[이 자습서에서 샘플 코드 보기 또는 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples)</span><span class="sxs-lookup"><span data-stu-id="94a66-382">[View or download sample code for this tutorial](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="94a66-383">[다운로드하는 방법](xref:index#how-to-download-a-sample)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="94a66-383">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="94a66-384">자세한 내용은 다음 리소스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="94a66-384">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/index>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="94a66-385">이 자습서의 YouTube 버전</span><span class="sxs-lookup"><span data-stu-id="94a66-385">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)

## <a name="next-steps"></a><span data-ttu-id="94a66-386">다음 단계</span><span class="sxs-lookup"><span data-stu-id="94a66-386">Next steps</span></span>

<span data-ttu-id="94a66-387">본 자습서에서는 다음 작업에 관한 방법을 학습했습니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-387">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="94a66-388">웹 API 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="94a66-388">Create a web api project.</span></span>
> * <span data-ttu-id="94a66-389">모델 클래스 추가</span><span class="sxs-lookup"><span data-stu-id="94a66-389">Add a model class.</span></span>
> * <span data-ttu-id="94a66-390">데이터베이스 컨텍스트 만들기</span><span class="sxs-lookup"><span data-stu-id="94a66-390">Create the database context.</span></span>
> * <span data-ttu-id="94a66-391">데이터베이스 컨텍스트 등록</span><span class="sxs-lookup"><span data-stu-id="94a66-391">Register the database context.</span></span>
> * <span data-ttu-id="94a66-392">컨트롤러 추가</span><span class="sxs-lookup"><span data-stu-id="94a66-392">Add a controller.</span></span>
> * <span data-ttu-id="94a66-393">CRUD 메서드 추가</span><span class="sxs-lookup"><span data-stu-id="94a66-393">Add CRUD methods.</span></span>
> * <span data-ttu-id="94a66-394">라우팅 및 URL 경로 구성</span><span class="sxs-lookup"><span data-stu-id="94a66-394">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="94a66-395">반환 값 지정</span><span class="sxs-lookup"><span data-stu-id="94a66-395">Specify return values.</span></span>
> * <span data-ttu-id="94a66-396">Postman을 사용하여 웹 API 호출</span><span class="sxs-lookup"><span data-stu-id="94a66-396">Call the web API with Postman.</span></span>
> * <span data-ttu-id="94a66-397">jQuery를 사용하여 웹 API 호출</span><span class="sxs-lookup"><span data-stu-id="94a66-397">Call the web api with jQuery.</span></span>

<span data-ttu-id="94a66-398">API 도움말 페이지를 생성하는 방법을 알아보려면 다음 자습서로 계속 진행합니다.</span><span class="sxs-lookup"><span data-stu-id="94a66-398">Advance to the next tutorial to learn how to generate API help pages:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>
