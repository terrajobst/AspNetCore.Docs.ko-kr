---
title: ASP.NET Core 및 MongoDB를 사용하여 웹 API 만들기
author: prkhandelwal
description: 이 자습서에서는 MongoDB NoSQL 데이터베이스를 사용하여 ASP.NET Core 웹 API를 만드는 방법을 보여 줍니다.
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 06/04/2019
uid: tutorials/first-mongo-app
ms.openlocfilehash: 6a8c5d75f562b38015101e039a2f5d96a5491595
ms.sourcegitcommit: 5dd2ce9709c9e41142771e652d1a4bd0b5248cec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2019
ms.locfileid: "66692556"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="9e270-103">ASP.NET Core 및 MongoDB를 사용하여 웹 API 만들기</span><span class="sxs-lookup"><span data-stu-id="9e270-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="9e270-104">작성자: [Pratik Khandelwal](https://twitter.com/K2Prk) 및 [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="9e270-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="9e270-105">이 자습서에서는 [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL 데이터베이스에 대해 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업을 수행하는 웹 API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="9e270-106">이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9e270-107">MongoDB 구성</span><span class="sxs-lookup"><span data-stu-id="9e270-107">Configure MongoDB</span></span>
> * <span data-ttu-id="9e270-108">MongoDB 데이터베이스 만들기</span><span class="sxs-lookup"><span data-stu-id="9e270-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="9e270-109">MongoDB 컬렉션 및 스키마 정의</span><span class="sxs-lookup"><span data-stu-id="9e270-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="9e270-110">웹 API에서 MongoDB CRUD 작업 수행</span><span class="sxs-lookup"><span data-stu-id="9e270-110">Perform MongoDB CRUD operations from a web API</span></span>

<span data-ttu-id="9e270-111">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9e270-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9e270-112">전제 조건</span><span class="sxs-lookup"><span data-stu-id="9e270-112">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9e270-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9e270-113">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="9e270-114">.NET Core SDK 2.2 이상</span><span class="sxs-lookup"><span data-stu-id="9e270-114">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="9e270-115">**ASP.NET 및 웹 개발** 워크로드가 있는 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="9e270-115">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="9e270-116">MongoDB</span><span class="sxs-lookup"><span data-stu-id="9e270-116">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9e270-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9e270-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="9e270-118">.NET Core SDK 2.2 이상</span><span class="sxs-lookup"><span data-stu-id="9e270-118">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="9e270-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9e270-119">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="9e270-120">Visual Studio Code용 C#</span><span class="sxs-lookup"><span data-stu-id="9e270-120">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [<span data-ttu-id="9e270-121">MongoDB</span><span class="sxs-lookup"><span data-stu-id="9e270-121">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="9e270-122">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9e270-122">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="9e270-123">.NET Core SDK 2.2 이상</span><span class="sxs-lookup"><span data-stu-id="9e270-123">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="9e270-124">Mac용 Visual Studio 버전 7.7 이상</span><span class="sxs-lookup"><span data-stu-id="9e270-124">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="9e270-125">MongoDB</span><span class="sxs-lookup"><span data-stu-id="9e270-125">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="9e270-126">MongoDB 구성</span><span class="sxs-lookup"><span data-stu-id="9e270-126">Configure MongoDB</span></span>

<span data-ttu-id="9e270-127">Windows를 사용하는 경우 MongoDB는 기본적으로*C:\\Program Files\\MongoDB*에 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-127">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="9e270-128">*C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin*을 `Path` 환경 변수에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-128">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="9e270-129">이렇게 변경하면 개발 머신의 어디에서나 MongoDB에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-129">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="9e270-130">다음 단계에서 mongo 셸을 사용하여 데이터베이스 및 컬렉션을 만들고 문서를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-130">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="9e270-131">mongo 셸 명령에 대한 자세한 내용은 [mongo 셸 작업](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9e270-131">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="9e270-132">개발 머신에서 데이터를 저장할 디렉터리를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-132">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="9e270-133">예를 들어 Windows의 경우 *C:\\BooksData*를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-133">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="9e270-134">디렉터리가 없을 경우 새로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-134">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="9e270-135">mongo 셸은 새 디렉터리를 만들지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-135">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="9e270-136">명령 셸을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-136">Open a command shell.</span></span> <span data-ttu-id="9e270-137">다음 명령을 실행하여 기본 포트 27017에서 MongoDB에 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-137">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="9e270-138">`<data_directory_path>`를 이전 단계에서 선택한 디렉터리로 바꿔야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-138">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

    ```console
    mongod --dbpath <data_directory_path>
    ```

1. <span data-ttu-id="9e270-139">다른 명령 셸 인스턴스를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-139">Open another command shell instance.</span></span> <span data-ttu-id="9e270-140">다음 명령을 실행하여 기본 테스트 데이터베이스에 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-140">Connect to the default test database by running the following command:</span></span>

    ```console
    mongo
    ```

1. <span data-ttu-id="9e270-141">명령 셸에서 다음을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-141">Run the following in a command shell:</span></span>

    ```console
    use BookstoreDb
    ```

    <span data-ttu-id="9e270-142">아직 존재하지 않는 경우 *BookstoreDb*라는 데이터베이스가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-142">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="9e270-143">데이터베이스가 있는 경우 트랜잭션을 위해 해당 연결이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-143">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="9e270-144">다음 명령을 사용하여 `Books` 컬렉션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-144">Create a `Books` collection using following command:</span></span>

    ```console
    db.createCollection('Books')
    ```

    <span data-ttu-id="9e270-145">다음 결과가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-145">The following result is displayed:</span></span>

    ```console
    { "ok" : 1 }
    ```

1. <span data-ttu-id="9e270-146">다음 명령을 사용하여 `Books` 컬렉션에 대한 스키마를 정의하고 두 개의 문서를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-146">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

    ```console
    db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
    ```

    <span data-ttu-id="9e270-147">다음 결과가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-147">The following result is displayed:</span></span>

    ```console
    {
      "acknowledged" : true,
      "insertedIds" : [
        ObjectId("5bfd996f7b8e48dc15ff215d"),
        ObjectId("5bfd996f7b8e48dc15ff215e")
      ]
    }
    ```

1. <span data-ttu-id="9e270-148">다음 명령을 사용하여 데이터베이스의 문서를 봅니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-148">View the documents in the database using the following command:</span></span>

    ```console
    db.Books.find({}).pretty()
    ```

    <span data-ttu-id="9e270-149">다음 결과가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-149">The following result is displayed:</span></span>

    ```console
    {
      "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
      "Name" : "Design Patterns",
      "Price" : 54.93,
      "Category" : "Computers",
      "Author" : "Ralph Johnson"
    }
    {
      "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
      "Name" : "Clean Code",
      "Price" : 43.15,
      "Category" : "Computers",
      "Author" : "Robert C. Martin"
    }
    ```

    <span data-ttu-id="9e270-150">스키마가 각 문서에 대해 자동 생성된 `ObjectId` 형식의 `_id` 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-150">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="9e270-151">데이터베이스가 준비되었습니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-151">The database is ready.</span></span> <span data-ttu-id="9e270-152">이제 ASP.NET Core 웹 API를 만들기 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-152">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="9e270-153">ASP.NET Core 웹 API 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="9e270-153">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9e270-154">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9e270-154">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="9e270-155">**파일** > **새로 만들기** > **프로젝트**로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-155">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="9e270-156">**ASP.NET Core 웹 애플리케이션** 프로젝트 유형을 선택하고 **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-156">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="9e270-157">프로젝트 이름을 *BooksApi*로 지정하고 **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-157">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="9e270-158">**.NET Core** 대상 프레임워크 및 **ASP.NET Core 2.2**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-158">Select the **.NET Core** target framework and **ASP.NET Core 2.2**.</span></span> <span data-ttu-id="9e270-159">**API** 프로젝트 템플릿을 선택하고 **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-159">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="9e270-160">[NuGet 갤러리: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/)를 방문하여 MongoDB용 .NET 드라이버의 안정적인 최신 버전을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-160">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="9e270-161">**패키지 관리자 콘솔** 창에서 프로젝트 루트로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-161">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="9e270-162">다음 명령을 실행하여 MongoDB용 .NET 드라이버를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-162">Run the following command to install the .NET driver for MongoDB:</span></span>

    ```powershell
    Install-Package MongoDB.Driver -Version {VERSION}
    ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9e270-163">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9e270-163">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="9e270-164">명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-164">Run the following commands in a command shell:</span></span>

    ```console
    dotnet new webapi -o BooksApi
    code BooksApi
    ```

    <span data-ttu-id="9e270-165">.NET Core를 대상으로 하는 새로운 ASP.NET Core 웹 API 프로젝트가 Visual Studio Code에서 생성되어 열립니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-165">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="9e270-166">상태 표시줄의 OmniSharp 불꽃 아이콘이 녹색으로 바뀐 후 대화 상자에 **빌드 및 디버그에 필요한 자산이 'BooksApi'에서 누락되었습니다.라는 메시지가 표시됩니다. 추가할까요?** .</span><span class="sxs-lookup"><span data-stu-id="9e270-166">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="9e270-167">**예**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-167">Select **Yes**.</span></span>
1. <span data-ttu-id="9e270-168">[NuGet 갤러리: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/)를 방문하여 MongoDB용 .NET 드라이버의 안정적인 최신 버전을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-168">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="9e270-169">**통합 터미널**을 열고 프로젝트 루트로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-169">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="9e270-170">다음 명령을 실행하여 MongoDB용 .NET 드라이버를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-170">Run the following command to install the .NET driver for MongoDB:</span></span>

    ```console
    dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
    ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="9e270-171">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="9e270-171">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="9e270-172">**파일** > **새 솔루션** >  **.NET Core** > **앱**으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-172">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="9e270-173">**ASP.NET Core Web API** C# 프로젝트 템플릿을 선택하고 **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-173">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="9e270-174">**대상 프레임워크** 드롭다운 목록에서 **.NET Core 2.2**를 선택하고 **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-174">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="9e270-175">**프로젝트 이름**으로 *BooksApi*를 입력하고 **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-175">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="9e270-176">**솔루션** 패드에서 프로젝트의 **종속성** 노드를 마우스 오른쪽 단추로 클릭하고 **패키지 추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-176">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="9e270-177">검색 상자에 *MongoDB.Driver*를 입력하여 *MongoDB.Driver* 패키지를 선택하고 **패키지 추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-177">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="9e270-178">**라이선스 승인** 대화 상자에서 **동의** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-178">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="9e270-179">엔터티 모델 추가</span><span class="sxs-lookup"><span data-stu-id="9e270-179">Add an entity model</span></span>

1. <span data-ttu-id="9e270-180">프로젝트 루트에 *Models* 디렉터리를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-180">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="9e270-181">다음 코드를 사용하여 *Models* 디렉터리에 `Book` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-181">Add a `Book` class to the *Models* directory with the following code:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Models/Book.cs)]

    <span data-ttu-id="9e270-182">앞의 클래스에서 `Id` 속성은</span><span class="sxs-lookup"><span data-stu-id="9e270-182">In the preceding class, the `Id` property:</span></span>
    
    * <span data-ttu-id="9e270-183">CLR(공용 언어 런타임) 개체를 MongoDB 컬렉션에 매핑하는 데 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-183">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
    * <span data-ttu-id="9e270-184">이 속성을 문서의 기본 키로 지정하려면 [[BsonId]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm)에 주석을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-184">Is annotated with [[BsonId]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
    * <span data-ttu-id="9e270-185">[ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) 구조 대신 `string` 유형으로 매개 변수를 전달하려면 [[BsonRepresentation(BsonType.ObjectId)]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm)에 주석을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-185">Is annotated with [[BsonRepresentation(BsonType.ObjectId)]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="9e270-186">Mongo는 `string`에서 `ObjectId`로 변환을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-186">Mongo handles the conversion from `string` to `ObjectId`.</span></span>
    
    <span data-ttu-id="9e270-187">클래스의 기타 속성은 [[BsonElement]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) 특성으로 주석이 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-187">Other properties in the class are annotated with the [[BsonElement]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="9e270-188">특성 값은 MongoDB 컬렉션의 속성 이름을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-188">The attribute's value represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="9e270-189">구성 모델 추가</span><span class="sxs-lookup"><span data-stu-id="9e270-189">Add a configuration model</span></span>

1. <span data-ttu-id="9e270-190">다음 데이터베이스 구성 값을 *appsettings.json*에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-190">Add the following database configuration values to *appsettings.json*:</span></span>

    [!code-json[](first-mongo-app/sample/BooksApi/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="9e270-191">다음 코드를 사용하여 *BookstoreDatabaseSettings.cs* 파일을 *모델* 디렉터리에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-191">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Models/BookstoreDatabaseSettings.cs)]

    <span data-ttu-id="9e270-192">위의 `BookstoreDatabaseSettings` 클래스는 *appsettings.json* 파일의 `BookstoreDatabaseSettings` 속성 값을 저장하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-192">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="9e270-193">JSON 및 C# 속성 이름은 매핑 프로세스를 용이하게 하기 위해 동일한 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-193">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="9e270-194">`AddMvc`로 호출하기 전에 다음 코드를 `Startup.ConfigureServices`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-194">Add the following code to `Startup.ConfigureServices`, before the call to `AddMvc`:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Startup.cs?name=snippet_ConfigureDatabaseSettings)]

    <span data-ttu-id="9e270-195">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="9e270-195">In the preceding code:</span></span>

    * <span data-ttu-id="9e270-196">*appsettings.json* 파일의 `BookstoreDatabaseSettings` 섹션이 바인딩되는 구성 인스턴스가 DI(종속성 주입) 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-196">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="9e270-197">예를 들어 `BookstoreDatabaseSettings` 개체의 `ConnectionString` 속성은 *appsettings.json*의 `BookstoreDatabaseSettings:ConnectionString` 속성으로 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-197">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
    * <span data-ttu-id="9e270-198">`IBookstoreDatabaseSettings` 인터페이스는 싱글톤 [서비스 수명](xref:fundamentals/dependency-injection#service-lifetimes)으로 DI에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-198">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="9e270-199">삽입하면 인터페이스 인스턴스가 `BookstoreDatabaseSettings` 개체로 확인됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-199">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="9e270-200">*Startup.cs*의 맨 위에 다음 코드를 추가하여 `BookstoreDatabaseSettings` 및 `IBookstoreDatabaseSettings` 참조를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-200">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="9e270-201">CRUD 작업 서비스 추가</span><span class="sxs-lookup"><span data-stu-id="9e270-201">Add a CRUD operations service</span></span>

1. <span data-ttu-id="9e270-202">프로젝트 루트에 *Services* 디렉터리를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-202">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="9e270-203">다음 코드를 사용하여 *Services* 디렉터리에 `BookService` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-203">Add a `BookService` class to the *Services* directory with the following code:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Services/BookService.cs?name=snippet_BookServiceClass)]

    <span data-ttu-id="9e270-204">위의 코드에서 생성자 주입을 통해 DI에서 `IBookstoreDatabaseSettings` 인스턴스가 검색됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-204">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="9e270-205">이 기술은 [구성 모델 추가](#add-a-configuration-model) 섹션에 추가된 *appsettings.json* 구성 값에 대한 액세스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-205">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="9e270-206">`Startup.ConfigureServices`에서 `BookService` 클래스를 DI로 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-206">In `Startup.ConfigureServices`, register the `BookService` class with DI:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Startup.cs?name=snippet_ConfigureServices&highlight=9)]

    <span data-ttu-id="9e270-207">위의 코드에서 `BookService` 클래스는 사용 클래스에서 생성자 주입을 지원하는 DI로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-207">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="9e270-208">`BookService`가 `MongoClient`에 직접 종속되기 때문에 싱글톤 서비스 수명이 가장 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-208">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="9e270-209">공식 [Mongo 클라이언트 재사용 지침](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)에 따라 `MongoClient`를 싱글톤 수명으로 DI에 등록해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-209">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="9e270-210">*Startup.cs*의 맨 위에 다음 코드를 추가하여 `BookService` 참조를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-210">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="9e270-211">`BookService` 클래스는 다음 `MongoDB.Driver` 멤버를 사용하여 데이터베이스에 대해 CRUD 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-211">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="9e270-212">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; 데이터베이스 작업을 수행하기 위한 서버 인스턴스를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-212">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="9e270-213">이 클래스의 생성자에 MongoDB 연결 문자열이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-213">The constructor of this class is provided the MongoDB connection string:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="9e270-214">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; 작업 수행을 위한 Mongo 데이터베이스를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-214">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="9e270-215">이 자습서에서는 인터페이스의 일반 [GetCollection<TDocument>(컬렉션)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) 메서드를 사용하여 특정 컬렉션의 데이터에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-215">This tutorial uses the generic [GetCollection<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="9e270-216">이 메서드를 호출한 후 컬렉션에 대해 CRUD 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-216">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="9e270-217">`GetCollection<TDocument>(collection)` 메서드 호출에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-217">In the `GetCollection<TDocument>(collection)` method call:</span></span>
  * <span data-ttu-id="9e270-218">`collection`은 컬렉션 이름을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-218">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="9e270-219">`TDocument`는 컬렉션에 저장된 CLR 개체 형식을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-219">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="9e270-220">`GetCollection<TDocument>(collection)`는 컬렉션을 나타내는 [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) 개체를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-220">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="9e270-221">이 자습서에서는 컬렉션에 대해 다음 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-221">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="9e270-222">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; 제공된 검색 조건과 일치하는 단일 문서를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-222">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="9e270-223">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; 제공된 검색 조건과 일치하는 컬렉션의 모든 문서를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-223">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="9e270-224">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; 제공된 개체를 컬렉션에 새 문서로 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-224">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="9e270-225">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; 제공된 검색 조건과 일치하는 단일 문서를 제공된 개체로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-225">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="9e270-226">컨트롤러 추가</span><span class="sxs-lookup"><span data-stu-id="9e270-226">Add a controller</span></span>

<span data-ttu-id="9e270-227">다음 코드를 사용하여 *Controllers* 디렉터리에 `BooksController` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-227">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/sample/BooksApi/Controllers/BooksController.cs)]

<span data-ttu-id="9e270-228">앞의 웹 API 컨트롤러는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-228">The preceding web API controller:</span></span>

* <span data-ttu-id="9e270-229">`BookService` 클래스를 사용하여 CRUD 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-229">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="9e270-230">GET, POST, PUT 및 DELETE HTTP 요청을 지원하는 작업 메서드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-230">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="9e270-231">`Create` 작업 메서드에서 <xref:System.Web.Http.ApiController.CreatedAtRoute*>를 호출하여 [HTTP 201](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) 응답을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-231">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="9e270-232">상태 코드 201은 서버에서 새 리소스를 만드는 HTTP POST 메서드의 표준 응답입니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-232">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="9e270-233">또한 `CreatedAtRoute`는 `Location` 헤더를 응답에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-233">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="9e270-234">`Location` 헤더는 새로 만든 책의 URI를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-234">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="9e270-235">웹 API 테스트</span><span class="sxs-lookup"><span data-stu-id="9e270-235">Test the web API</span></span>

1. <span data-ttu-id="9e270-236">앱을 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-236">Build and run the app.</span></span>

1. <span data-ttu-id="9e270-237">`http://localhost:<port>/api/books`로 이동하여 컨트롤러의 매개 변수가 없는 `Get` 작업 메서드를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-237">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="9e270-238">다음 JSON 응답이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-238">The following JSON response is displayed:</span></span>

    ```json
    [
      {
        "id":"5bfd996f7b8e48dc15ff215d",
        "bookName":"Design Patterns",
        "price":54.93,
        "category":"Computers",
        "author":"Ralph Johnson"
      },
      {
        "id":"5bfd996f7b8e48dc15ff215e",
        "bookName":"Clean Code",
        "price":43.15,
        "category":"Computers",
        "author":"Robert C. Martin"
      }
    ]
    ```

1. <span data-ttu-id="9e270-239">`http://localhost:<port>/api/books/5bfd996f7b8e48dc15ff215e`로 이동하여 컨트롤러의 오버로드된 `Get` 작업 메서드를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-239">Navigate to `http://localhost:<port>/api/books/5bfd996f7b8e48dc15ff215e` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="9e270-240">다음 JSON 응답이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="9e270-240">The following JSON response is displayed:</span></span>

    ```json
    {
      "id":"5bfd996f7b8e48dc15ff215e",
      "bookName":"Clean Code",
      "price":43.15,
      "category":"Computers",
      "author":"Robert C. Martin"
    }
    ```

## <a name="next-steps"></a><span data-ttu-id="9e270-241">다음 단계</span><span class="sxs-lookup"><span data-stu-id="9e270-241">Next steps</span></span>

<span data-ttu-id="9e270-242">ASP.NET Core 웹 API 빌드 방법에 대한 자세한 내용은 다음 리소스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="9e270-242">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="9e270-243">이 문서의 YouTube 버전</span><span class="sxs-lookup"><span data-stu-id="9e270-243">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
