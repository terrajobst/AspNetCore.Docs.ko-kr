---
title: ASP.NET Core 및 MongoDB를 사용하여 웹 API 만들기
author: prkhandelwal
description: 이 자습서에서는 MongoDB NoSQL 데이터베이스를 사용하여 ASP.NET Core 웹 API를 만드는 방법을 보여 줍니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 08/17/2019
uid: tutorials/first-mongo-app
ms.openlocfilehash: 1425abbfc7bce6bdc445f4e41d9e004405c96e13
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880343"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="32d2d-103">ASP.NET Core 및 MongoDB를 사용하여 웹 API 만들기</span><span class="sxs-lookup"><span data-stu-id="32d2d-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="32d2d-104">작성자: [Pratik Khandelwal](https://twitter.com/K2Prk) 및 [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="32d2d-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="32d2d-105">이 자습서에서는 [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL 데이터베이스에 대해 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업을 수행하는 웹 API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="32d2d-106">이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="32d2d-107">MongoDB 구성</span><span class="sxs-lookup"><span data-stu-id="32d2d-107">Configure MongoDB</span></span>
> * <span data-ttu-id="32d2d-108">MongoDB 데이터베이스 만들기</span><span class="sxs-lookup"><span data-stu-id="32d2d-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="32d2d-109">MongoDB 컬렉션 및 스키마 정의</span><span class="sxs-lookup"><span data-stu-id="32d2d-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="32d2d-110">웹 API에서 MongoDB CRUD 작업 수행</span><span class="sxs-lookup"><span data-stu-id="32d2d-110">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="32d2d-111">JSON serialization 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="32d2d-111">Customize JSON serialization</span></span>

<span data-ttu-id="32d2d-112">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="32d2d-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="32d2d-113">전제 조건</span><span class="sxs-lookup"><span data-stu-id="32d2d-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="32d2d-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="32d2d-114">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="32d2d-115">.NET Core SDK 3.0 이상</span><span class="sxs-lookup"><span data-stu-id="32d2d-115">.NET Core SDK 3.0 or later</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="32d2d-116">**ASP.NET 및 웹 개발** 워크로드가 설치된 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="32d2d-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="32d2d-117">MongoDB</span><span class="sxs-lookup"><span data-stu-id="32d2d-117">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="32d2d-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="32d2d-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="32d2d-119">.NET Core SDK 3.0 이상</span><span class="sxs-lookup"><span data-stu-id="32d2d-119">.NET Core SDK 3.0 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="32d2d-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="32d2d-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="32d2d-121">Visual Studio Code용 C#</span><span class="sxs-lookup"><span data-stu-id="32d2d-121">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [<span data-ttu-id="32d2d-122">MongoDB</span><span class="sxs-lookup"><span data-stu-id="32d2d-122">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="32d2d-123">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="32d2d-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="32d2d-124">.NET Core SDK 3.0 이상</span><span class="sxs-lookup"><span data-stu-id="32d2d-124">.NET Core SDK 3.0 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="32d2d-125">Mac용 Visual Studio 버전 7.7 이상</span><span class="sxs-lookup"><span data-stu-id="32d2d-125">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="32d2d-126">MongoDB</span><span class="sxs-lookup"><span data-stu-id="32d2d-126">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="32d2d-127">MongoDB 구성</span><span class="sxs-lookup"><span data-stu-id="32d2d-127">Configure MongoDB</span></span>

<span data-ttu-id="32d2d-128">Windows를 사용하는 경우 MongoDB는 기본적으로*C:\\Program Files\\MongoDB*에 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-128">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="32d2d-129">*C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin*을 `Path` 환경 변수에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-129">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="32d2d-130">이렇게 변경하면 개발 머신의 어디에서나 MongoDB에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-130">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="32d2d-131">다음 단계에서 mongo 셸을 사용하여 데이터베이스 및 컬렉션을 만들고 문서를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-131">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="32d2d-132">mongo 셸 명령에 대한 자세한 내용은 [mongo 셸 작업](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="32d2d-132">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="32d2d-133">개발 머신에서 데이터를 저장할 디렉터리를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-133">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="32d2d-134">예를 들어 Windows의 경우 *C:\\BooksData*를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-134">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="32d2d-135">디렉터리가 없을 경우 새로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-135">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="32d2d-136">mongo 셸은 새 디렉터리를 만들지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-136">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="32d2d-137">명령 셸을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-137">Open a command shell.</span></span> <span data-ttu-id="32d2d-138">다음 명령을 실행하여 기본 포트 27017에서 MongoDB에 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-138">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="32d2d-139">`<data_directory_path>`를 이전 단계에서 선택한 디렉터리로 바꿔야 합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-139">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="32d2d-140">다른 명령 셸 인스턴스를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-140">Open another command shell instance.</span></span> <span data-ttu-id="32d2d-141">다음 명령을 실행하여 기본 테스트 데이터베이스에 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-141">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="32d2d-142">명령 셸에서 다음을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-142">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="32d2d-143">아직 존재하지 않는 경우 *BookstoreDb*라는 데이터베이스가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-143">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="32d2d-144">데이터베이스가 있는 경우 트랜잭션을 위해 해당 연결이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-144">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="32d2d-145">다음 명령을 사용하여 `Books` 컬렉션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-145">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="32d2d-146">다음 결과가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-146">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="32d2d-147">다음 명령을 사용하여 `Books` 컬렉션에 대한 스키마를 정의하고 두 개의 문서를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-147">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="32d2d-148">다음 결과가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-148">The following result is displayed:</span></span>

   ```console
   {
     "acknowledged" : true,
     "insertedIds" : [
       ObjectId("5bfd996f7b8e48dc15ff215d"),
       ObjectId("5bfd996f7b8e48dc15ff215e")
     ]
   }
   ```
  
   > [!NOTE]
   > <span data-ttu-id="32d2d-149">이 문서에 표시된 ID는 이 샘플을 실행할 때의 ID와 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-149">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="32d2d-150">다음 명령을 사용하여 데이터베이스의 문서를 봅니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-150">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="32d2d-151">다음 결과가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-151">The following result is displayed:</span></span>

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

   <span data-ttu-id="32d2d-152">스키마가 각 문서에 대해 자동 생성된 `ObjectId` 형식의 `_id` 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-152">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="32d2d-153">데이터베이스가 준비되었습니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-153">The database is ready.</span></span> <span data-ttu-id="32d2d-154">이제 ASP.NET Core 웹 API를 만들기 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-154">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="32d2d-155">ASP.NET Core 웹 API 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="32d2d-155">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="32d2d-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="32d2d-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="32d2d-157">**파일** > **새로 만들기** > **프로젝트**로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-157">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="32d2d-158">**ASP.NET Core 웹 애플리케이션** 프로젝트 유형을 선택하고 **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-158">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="32d2d-159">프로젝트 이름을 *BooksApi*로 지정하고 **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-159">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="32d2d-160">**.NET Core** 대상 프레임워크 및 **ASP.NET Core 3.0**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-160">Select the **.NET Core** target framework and **ASP.NET Core 3.0**.</span></span> <span data-ttu-id="32d2d-161">**API** 프로젝트 템플릿을 선택하고 **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-161">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="32d2d-162">[NuGet 갤러리: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/)를 방문하여 MongoDB용 .NET 드라이버의 안정적인 최신 버전을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-162">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="32d2d-163">**패키지 관리자 콘솔** 창에서 프로젝트 루트로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-163">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="32d2d-164">다음 명령을 실행하여 MongoDB용 .NET 드라이버를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-164">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="32d2d-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="32d2d-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="32d2d-166">명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-166">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="32d2d-167">.NET Core를 대상으로 하는 새로운 ASP.NET Core 웹 API 프로젝트가 Visual Studio Code에서 생성되어 열립니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-167">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="32d2d-168">상태 표시줄의 OmniSharp 불꽃 아이콘이 녹색으로 바뀐 후 대화 상자에 **빌드 및 디버그에 필요한 자산이 'BooksApi'에서 누락되었습니다.라는 메시지가 표시됩니다. 추가할까요?** .</span><span class="sxs-lookup"><span data-stu-id="32d2d-168">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="32d2d-169">**Yes**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-169">Select **Yes**.</span></span>
1. <span data-ttu-id="32d2d-170">[NuGet 갤러리: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/)를 방문하여 MongoDB용 .NET 드라이버의 안정적인 최신 버전을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-170">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="32d2d-171">**통합 터미널**을 열고 프로젝트 루트로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-171">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="32d2d-172">다음 명령을 실행하여 MongoDB용 .NET 드라이버를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-172">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="32d2d-173">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="32d2d-173">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="32d2d-174">**파일** > **새 솔루션** > **.NET Core** > **앱**으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-174">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="32d2d-175">**ASP.NET Core Web API** C# 프로젝트 템플릿을 선택하고 **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-175">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="32d2d-176">**대상 프레임워크** 드롭다운 목록에서 **.NET Core 3.0**을 선택하고 **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-176">Select **.NET Core 3.0** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="32d2d-177">**프로젝트 이름**으로 *BooksApi*를 입력하고 **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-177">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="32d2d-178">**솔루션** 패드에서 프로젝트의 **종속성** 노드를 마우스 오른쪽 단추로 클릭하고 **패키지 추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-178">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="32d2d-179">검색 상자에 *MongoDB.Driver*를 입력하여 *MongoDB.Driver* 패키지를 선택하고 **패키지 추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-179">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="32d2d-180">**라이선스 승인** 대화 상자에서 **동의** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-180">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="32d2d-181">엔터티 모델 추가</span><span class="sxs-lookup"><span data-stu-id="32d2d-181">Add an entity model</span></span>

1. <span data-ttu-id="32d2d-182">프로젝트 루트에 *Models* 디렉터리를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-182">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="32d2d-183">다음 코드를 사용하여 *Models* 디렉터리에 `Book` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-183">Add a `Book` class to the *Models* directory with the following code:</span></span>

   ```csharp
   using MongoDB.Bson;
   using MongoDB.Bson.Serialization.Attributes;

   namespace BooksApi.Models
   {
       public class Book
       {
           [BsonId]
           [BsonRepresentation(BsonType.ObjectId)]
           public string Id { get; set; }

           [BsonElement("Name")]
           public string BookName { get; set; }

           public decimal Price { get; set; }

           public string Category { get; set; }

           public string Author { get; set; }
       }
   }
   ```

   <span data-ttu-id="32d2d-184">앞의 클래스에서 `Id` 속성은</span><span class="sxs-lookup"><span data-stu-id="32d2d-184">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="32d2d-185">CLR(공용 언어 런타임) 개체를 MongoDB 컬렉션에 매핑하는 데 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-185">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="32d2d-186">이 속성을 문서의 기본 키로 지정하려면 [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm)로 주석이 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-186">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="32d2d-187">[ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) 구조체 대신 `string` 형식으로 매개 변수를 전달할 수 있도록 [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm)로 주석이 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-187">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="32d2d-188">Mongo는 `string`에서 `ObjectId`로 변환을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-188">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="32d2d-189">`BookName` 속성은 [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) 특성으로 주석이 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-189">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="32d2d-190">`Name`의 특성 값은 MongoDB 컬렉션의 속성 이름을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-190">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="32d2d-191">구성 모델 추가</span><span class="sxs-lookup"><span data-stu-id="32d2d-191">Add a configuration model</span></span>

1. <span data-ttu-id="32d2d-192">다음 데이터베이스 구성 값을 *appsettings.json*에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-192">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="32d2d-193">다음 코드를 사용하여 *BookstoreDatabaseSettings.cs* 파일을 *모델* 디렉터리에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-193">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="32d2d-194">위의 `BookstoreDatabaseSettings` 클래스는 *appsettings.json* 파일의 `BookstoreDatabaseSettings` 속성 값을 저장하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-194">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="32d2d-195">JSON 및 C# 속성 이름은 매핑 프로세스를 용이하게 하기 위해 동일한 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-195">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="32d2d-196">다음 강조 표시된 코드를 `Startup.ConfigureServices`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-196">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   <span data-ttu-id="32d2d-197">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="32d2d-197">In the preceding code:</span></span>

   * <span data-ttu-id="32d2d-198">*appsettings.json* 파일의 `BookstoreDatabaseSettings` 섹션이 바인딩되는 구성 인스턴스가 DI(종속성 주입) 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-198">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="32d2d-199">예를 들어 `BookstoreDatabaseSettings` 개체의 `ConnectionString` 속성은 *appsettings.json*의 `BookstoreDatabaseSettings:ConnectionString` 속성으로 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-199">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="32d2d-200">`IBookstoreDatabaseSettings` 인터페이스는 싱글톤 [서비스 수명](xref:fundamentals/dependency-injection#service-lifetimes)으로 DI에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-200">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="32d2d-201">삽입하면 인터페이스 인스턴스가 `BookstoreDatabaseSettings` 개체로 확인됩니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-201">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="32d2d-202">*Startup.cs*의 맨 위에 다음 코드를 추가하여 `BookstoreDatabaseSettings` 및 `IBookstoreDatabaseSettings` 참조를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-202">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="32d2d-203">CRUD 작업 서비스 추가</span><span class="sxs-lookup"><span data-stu-id="32d2d-203">Add a CRUD operations service</span></span>

1. <span data-ttu-id="32d2d-204">프로젝트 루트에 *Services* 디렉터리를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-204">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="32d2d-205">다음 코드를 사용하여 *Services* 디렉터리에 `BookService` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-205">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="32d2d-206">위의 코드에서 생성자 주입을 통해 DI에서 `IBookstoreDatabaseSettings` 인스턴스가 검색됩니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-206">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="32d2d-207">이 기술은 [구성 모델 추가](#add-a-configuration-model) 섹션에 추가된 *appsettings.json* 구성 값에 대한 액세스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-207">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="32d2d-208">다음 강조 표시된 코드를 `Startup.ConfigureServices`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-208">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="32d2d-209">위의 코드에서 `BookService` 클래스는 사용 클래스에서 생성자 주입을 지원하는 DI로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-209">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="32d2d-210">`BookService`가 `MongoClient`에 직접 종속되기 때문에 싱글톤 서비스 수명이 가장 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-210">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="32d2d-211">공식 [Mongo 클라이언트 재사용 지침](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)에 따라 `MongoClient`를 싱글톤 수명으로 DI에 등록해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-211">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="32d2d-212">*Startup.cs*의 맨 위에 다음 코드를 추가하여 `BookService` 참조를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-212">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="32d2d-213">`BookService` 클래스는 다음 `MongoDB.Driver` 멤버를 사용하여 데이터베이스에 대해 CRUD 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-213">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="32d2d-214">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; 데이터베이스 작업을 수행하기 위한 서버 인스턴스를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-214">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="32d2d-215">이 클래스의 생성자에 MongoDB 연결 문자열이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-215">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="32d2d-216">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; 작업 수행을 위한 Mongo 데이터베이스를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-216">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="32d2d-217">이 자습서에서는 인터페이스의 제네릭 [GetCollection\<TDocument>(컬렉션)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) 메서드를 사용하여 특정 컬렉션의 데이터에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-217">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="32d2d-218">이 메서드를 호출한 후 컬렉션에 대해 CRUD 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-218">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="32d2d-219">`GetCollection<TDocument>(collection)` 메서드 호출에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-219">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="32d2d-220">`collection`은 컬렉션 이름을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-220">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="32d2d-221">`TDocument`는 컬렉션에 저장된 CLR 개체 형식을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-221">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="32d2d-222">`GetCollection<TDocument>(collection)`는 컬렉션을 나타내는 [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) 개체를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-222">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="32d2d-223">이 자습서에서는 컬렉션에 대해 다음 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-223">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="32d2d-224">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; 제공된 검색 조건과 일치하는 단일 문서를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-224">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="32d2d-225">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; 제공된 검색 조건과 일치하는 컬렉션의 모든 문서를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-225">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="32d2d-226">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; 제공된 개체를 컬렉션에 새 문서로 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-226">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="32d2d-227">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; 제공된 검색 조건과 일치하는 단일 문서를 제공된 개체로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-227">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="32d2d-228">컨트롤러 추가</span><span class="sxs-lookup"><span data-stu-id="32d2d-228">Add a controller</span></span>

<span data-ttu-id="32d2d-229">다음 코드를 사용하여 *Controllers* 디렉터리에 `BooksController` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-229">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="32d2d-230">앞의 웹 API 컨트롤러는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-230">The preceding web API controller:</span></span>

* <span data-ttu-id="32d2d-231">`BookService` 클래스를 사용하여 CRUD 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-231">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="32d2d-232">GET, POST, PUT 및 DELETE HTTP 요청을 지원하는 작업 메서드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-232">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="32d2d-233">`Create` 작업 메서드에서 <xref:System.Web.Http.ApiController.CreatedAtRoute*>를 호출하여 [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) 응답을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-233">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="32d2d-234">상태 코드 201은 서버에서 새 리소스를 만드는 HTTP POST 메서드의 표준 응답입니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-234">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="32d2d-235">또한 `CreatedAtRoute`는 `Location` 헤더를 응답에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-235">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="32d2d-236">`Location` 헤더는 새로 만든 책의 URI를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-236">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="32d2d-237">웹 API 테스트</span><span class="sxs-lookup"><span data-stu-id="32d2d-237">Test the web API</span></span>

1. <span data-ttu-id="32d2d-238">앱을 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-238">Build and run the app.</span></span>

1. <span data-ttu-id="32d2d-239">`http://localhost:<port>/api/books`로 이동하여 컨트롤러의 매개 변수가 없는 `Get` 작업 메서드를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-239">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="32d2d-240">다음 JSON 응답이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-240">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="32d2d-241">`http://localhost:<port>/api/books/{id here}`로 이동하여 컨트롤러의 오버로드된 `Get` 작업 메서드를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-241">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="32d2d-242">다음 JSON 응답이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-242">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="32d2d-243">JSON serialization 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="32d2d-243">Configure JSON serialization options</span></span>

<span data-ttu-id="32d2d-244">[웹 API 테스트](#test-the-web-api) 섹션에서 반환된 JSON 응답에 대해 변경하는 두 개의 세부 정보가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-244">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="32d2d-245">속성 이름의 기본 카멜식 대/소문자는 CLR 개체의 속성 이름의 파스칼식 대/소문자와 일치하도록 변경되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-245">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="32d2d-246">`bookName` 속성은 `Name`으로 반환되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-246">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="32d2d-247">앞의 요구 사항을 충족하기 위해 다음과 같이 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-247">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="32d2d-248">ASP.NET 공유 프레임워크에서 JSON.NET이 제거되었습니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-248">JSON.NET has been removed from ASP.NET shared framework.</span></span> <span data-ttu-id="32d2d-249">[Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson)에 패키지 참조를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-249">Add a package reference to [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span></span>

1. <span data-ttu-id="32d2d-250">`Startup.ConfigureServices`에서 다음 강조 표시된 코드를 `AddMvc` 메서드 호출에 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-250">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="32d2d-251">이전 변경으로 웹 API의 직렬화된 JSON 응답에서 속성 이름은 CLR 개체 형식의 해당 속성 이름과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-251">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="32d2d-252">예를 들어 `Book` 클래스의 `Author` 속성은 `Author`로 직렬화합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-252">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="32d2d-253">*Models/Book.cs*에서 다음 [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) 특성으로 `BookName` 속성에 주석을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-253">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="32d2d-254">`Name`의 `[JsonProperty]` 특성 값은 웹 API의 직렬화된 JSON 응답의 속성 이름을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-254">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="32d2d-255">*Models/Book.cs*의 맨 위에 다음 코드를 추가하여 `[JsonProperty]` 특성 참조를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-255">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="32d2d-256">[웹 API 테스트](#test-the-web-api) 섹션에 정의된 단계를 반복합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-256">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="32d2d-257">JSON 속성 이름의 차이를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-257">Notice the difference in JSON property names.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="32d2d-258">이 자습서에서는 [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL 데이터베이스에 대해 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업을 수행하는 웹 API를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-258">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="32d2d-259">이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-259">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="32d2d-260">MongoDB 구성</span><span class="sxs-lookup"><span data-stu-id="32d2d-260">Configure MongoDB</span></span>
> * <span data-ttu-id="32d2d-261">MongoDB 데이터베이스 만들기</span><span class="sxs-lookup"><span data-stu-id="32d2d-261">Create a MongoDB database</span></span>
> * <span data-ttu-id="32d2d-262">MongoDB 컬렉션 및 스키마 정의</span><span class="sxs-lookup"><span data-stu-id="32d2d-262">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="32d2d-263">웹 API에서 MongoDB CRUD 작업 수행</span><span class="sxs-lookup"><span data-stu-id="32d2d-263">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="32d2d-264">JSON serialization 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="32d2d-264">Customize JSON serialization</span></span>

<span data-ttu-id="32d2d-265">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="32d2d-265">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="32d2d-266">전제 조건</span><span class="sxs-lookup"><span data-stu-id="32d2d-266">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="32d2d-267">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="32d2d-267">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="32d2d-268">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="32d2d-268">.NET Core SDK 2.2</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="32d2d-269">**ASP.NET 및 웹 개발** 워크로드가 설치된 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="32d2d-269">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="32d2d-270">MongoDB</span><span class="sxs-lookup"><span data-stu-id="32d2d-270">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="32d2d-271">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="32d2d-271">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="32d2d-272">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="32d2d-272">.NET Core SDK 2.2</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="32d2d-273">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="32d2d-273">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="32d2d-274">Visual Studio Code용 C#</span><span class="sxs-lookup"><span data-stu-id="32d2d-274">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [<span data-ttu-id="32d2d-275">MongoDB</span><span class="sxs-lookup"><span data-stu-id="32d2d-275">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="32d2d-276">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="32d2d-276">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="32d2d-277">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="32d2d-277">.NET Core SDK 2.2</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="32d2d-278">Mac용 Visual Studio 버전 7.7 이상</span><span class="sxs-lookup"><span data-stu-id="32d2d-278">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="32d2d-279">MongoDB</span><span class="sxs-lookup"><span data-stu-id="32d2d-279">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="32d2d-280">MongoDB 구성</span><span class="sxs-lookup"><span data-stu-id="32d2d-280">Configure MongoDB</span></span>

<span data-ttu-id="32d2d-281">Windows를 사용하는 경우 MongoDB는 기본적으로*C:\\Program Files\\MongoDB*에 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-281">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="32d2d-282">*C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin*을 `Path` 환경 변수에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-282">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="32d2d-283">이렇게 변경하면 개발 머신의 어디에서나 MongoDB에 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-283">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="32d2d-284">다음 단계에서 mongo 셸을 사용하여 데이터베이스 및 컬렉션을 만들고 문서를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-284">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="32d2d-285">mongo 셸 명령에 대한 자세한 내용은 [mongo 셸 작업](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="32d2d-285">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="32d2d-286">개발 머신에서 데이터를 저장할 디렉터리를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-286">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="32d2d-287">예를 들어 Windows의 경우 *C:\\BooksData*를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-287">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="32d2d-288">디렉터리가 없을 경우 새로 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-288">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="32d2d-289">mongo 셸은 새 디렉터리를 만들지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-289">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="32d2d-290">명령 셸을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-290">Open a command shell.</span></span> <span data-ttu-id="32d2d-291">다음 명령을 실행하여 기본 포트 27017에서 MongoDB에 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-291">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="32d2d-292">`<data_directory_path>`를 이전 단계에서 선택한 디렉터리로 바꿔야 합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-292">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="32d2d-293">다른 명령 셸 인스턴스를 엽니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-293">Open another command shell instance.</span></span> <span data-ttu-id="32d2d-294">다음 명령을 실행하여 기본 테스트 데이터베이스에 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-294">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="32d2d-295">명령 셸에서 다음을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-295">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="32d2d-296">아직 존재하지 않는 경우 *BookstoreDb*라는 데이터베이스가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-296">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="32d2d-297">데이터베이스가 있는 경우 트랜잭션을 위해 해당 연결이 열립니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-297">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="32d2d-298">다음 명령을 사용하여 `Books` 컬렉션을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-298">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="32d2d-299">다음 결과가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-299">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="32d2d-300">다음 명령을 사용하여 `Books` 컬렉션에 대한 스키마를 정의하고 두 개의 문서를 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-300">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="32d2d-301">다음 결과가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-301">The following result is displayed:</span></span>

   ```console
   {
     "acknowledged" : true,
     "insertedIds" : [
       ObjectId("5bfd996f7b8e48dc15ff215d"),
       ObjectId("5bfd996f7b8e48dc15ff215e")
     ]
   }
   ```
  
   > [!NOTE]
   > <span data-ttu-id="32d2d-302">이 문서에 표시된 ID는 이 샘플을 실행할 때의 ID와 일치하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-302">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="32d2d-303">다음 명령을 사용하여 데이터베이스의 문서를 봅니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-303">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="32d2d-304">다음 결과가 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-304">The following result is displayed:</span></span>

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

   <span data-ttu-id="32d2d-305">스키마가 각 문서에 대해 자동 생성된 `ObjectId` 형식의 `_id` 속성을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-305">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="32d2d-306">데이터베이스가 준비되었습니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-306">The database is ready.</span></span> <span data-ttu-id="32d2d-307">이제 ASP.NET Core 웹 API를 만들기 시작할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-307">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="32d2d-308">ASP.NET Core 웹 API 프로젝트 만들기</span><span class="sxs-lookup"><span data-stu-id="32d2d-308">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="32d2d-309">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="32d2d-309">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="32d2d-310">**파일** > **새로 만들기** > **프로젝트**로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-310">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="32d2d-311">**ASP.NET Core 웹 애플리케이션** 프로젝트 유형을 선택하고 **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-311">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="32d2d-312">프로젝트 이름을 *BooksApi*로 지정하고 **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-312">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="32d2d-313">**.NET Core** 대상 프레임워크 및 **ASP.NET Core 2.2**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-313">Select the **.NET Core** target framework and **ASP.NET Core 2.2**.</span></span> <span data-ttu-id="32d2d-314">**API** 프로젝트 템플릿을 선택하고 **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-314">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="32d2d-315">[NuGet 갤러리: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/)를 방문하여 MongoDB용 .NET 드라이버의 안정적인 최신 버전을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-315">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="32d2d-316">**패키지 관리자 콘솔** 창에서 프로젝트 루트로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-316">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="32d2d-317">다음 명령을 실행하여 MongoDB용 .NET 드라이버를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-317">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="32d2d-318">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="32d2d-318">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="32d2d-319">명령 셸에서 다음 명령을 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-319">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="32d2d-320">.NET Core를 대상으로 하는 새로운 ASP.NET Core 웹 API 프로젝트가 Visual Studio Code에서 생성되어 열립니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-320">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="32d2d-321">상태 표시줄의 OmniSharp 불꽃 아이콘이 녹색으로 바뀐 후 대화 상자에 **빌드 및 디버그에 필요한 자산이 'BooksApi'에서 누락되었습니다.라는 메시지가 표시됩니다. 추가할까요?** .</span><span class="sxs-lookup"><span data-stu-id="32d2d-321">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="32d2d-322">**Yes**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-322">Select **Yes**.</span></span>
1. <span data-ttu-id="32d2d-323">[NuGet 갤러리: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/)를 방문하여 MongoDB용 .NET 드라이버의 안정적인 최신 버전을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-323">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="32d2d-324">**통합 터미널**을 열고 프로젝트 루트로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-324">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="32d2d-325">다음 명령을 실행하여 MongoDB용 .NET 드라이버를 설치합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-325">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="32d2d-326">Mac용 Visual Studio</span><span class="sxs-lookup"><span data-stu-id="32d2d-326">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="32d2d-327">**파일** > **새 솔루션** > **.NET Core** > **앱**으로 이동합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-327">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="32d2d-328">**ASP.NET Core Web API** C# 프로젝트 템플릿을 선택하고 **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-328">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="32d2d-329">**대상 프레임워크** 드롭다운 목록에서 **.NET Core 2.2**를 선택하고 **다음**을 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-329">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="32d2d-330">**프로젝트 이름**으로 *BooksApi*를 입력하고 **만들기**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-330">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="32d2d-331">**솔루션** 패드에서 프로젝트의 **종속성** 노드를 마우스 오른쪽 단추로 클릭하고 **패키지 추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-331">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="32d2d-332">검색 상자에 *MongoDB.Driver*를 입력하여 *MongoDB.Driver* 패키지를 선택하고 **패키지 추가**를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-332">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="32d2d-333">**라이선스 승인** 대화 상자에서 **동의** 단추를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-333">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="32d2d-334">엔터티 모델 추가</span><span class="sxs-lookup"><span data-stu-id="32d2d-334">Add an entity model</span></span>

1. <span data-ttu-id="32d2d-335">프로젝트 루트에 *Models* 디렉터리를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-335">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="32d2d-336">다음 코드를 사용하여 *Models* 디렉터리에 `Book` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-336">Add a `Book` class to the *Models* directory with the following code:</span></span>

   ```csharp
   using MongoDB.Bson;
   using MongoDB.Bson.Serialization.Attributes;

   namespace BooksApi.Models
   {
       public class Book
       {
           [BsonId]
           [BsonRepresentation(BsonType.ObjectId)]
           public string Id { get; set; }

           [BsonElement("Name")]
           public string BookName { get; set; }

           public decimal Price { get; set; }

           public string Category { get; set; }

           public string Author { get; set; }
       }
   }
   ```

   <span data-ttu-id="32d2d-337">앞의 클래스에서 `Id` 속성은</span><span class="sxs-lookup"><span data-stu-id="32d2d-337">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="32d2d-338">CLR(공용 언어 런타임) 개체를 MongoDB 컬렉션에 매핑하는 데 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-338">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="32d2d-339">이 속성을 문서의 기본 키로 지정하려면 [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm)로 주석이 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-339">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="32d2d-340">[ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) 구조체 대신 `string` 형식으로 매개 변수를 전달할 수 있도록 [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm)로 주석이 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-340">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="32d2d-341">Mongo는 `string`에서 `ObjectId`로 변환을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-341">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="32d2d-342">`BookName` 속성은 [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) 특성으로 주석이 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-342">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="32d2d-343">`Name`의 특성 값은 MongoDB 컬렉션의 속성 이름을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-343">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="32d2d-344">구성 모델 추가</span><span class="sxs-lookup"><span data-stu-id="32d2d-344">Add a configuration model</span></span>

1. <span data-ttu-id="32d2d-345">다음 데이터베이스 구성 값을 *appsettings.json*에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-345">Add the following database configuration values to *appsettings.json*:</span></span>

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="32d2d-346">다음 코드를 사용하여 *BookstoreDatabaseSettings.cs* 파일을 *모델* 디렉터리에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-346">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="32d2d-347">위의 `BookstoreDatabaseSettings` 클래스는 *appsettings.json* 파일의 `BookstoreDatabaseSettings` 속성 값을 저장하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-347">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="32d2d-348">JSON 및 C# 속성 이름은 매핑 프로세스를 용이하게 하기 위해 동일한 이름이 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-348">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="32d2d-349">다음 강조 표시된 코드를 `Startup.ConfigureServices`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-349">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   <span data-ttu-id="32d2d-350">위의 코드에서</span><span class="sxs-lookup"><span data-stu-id="32d2d-350">In the preceding code:</span></span>

   * <span data-ttu-id="32d2d-351">*appsettings.json* 파일의 `BookstoreDatabaseSettings` 섹션이 바인딩되는 구성 인스턴스가 DI(종속성 주입) 컨테이너에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-351">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="32d2d-352">예를 들어 `BookstoreDatabaseSettings` 개체의 `ConnectionString` 속성은 *appsettings.json*의 `BookstoreDatabaseSettings:ConnectionString` 속성으로 채워집니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-352">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
   * <span data-ttu-id="32d2d-353">`IBookstoreDatabaseSettings` 인터페이스는 싱글톤 [서비스 수명](xref:fundamentals/dependency-injection#service-lifetimes)으로 DI에 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-353">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="32d2d-354">삽입하면 인터페이스 인스턴스가 `BookstoreDatabaseSettings` 개체로 확인됩니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-354">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="32d2d-355">*Startup.cs*의 맨 위에 다음 코드를 추가하여 `BookstoreDatabaseSettings` 및 `IBookstoreDatabaseSettings` 참조를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-355">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="32d2d-356">CRUD 작업 서비스 추가</span><span class="sxs-lookup"><span data-stu-id="32d2d-356">Add a CRUD operations service</span></span>

1. <span data-ttu-id="32d2d-357">프로젝트 루트에 *Services* 디렉터리를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-357">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="32d2d-358">다음 코드를 사용하여 *Services* 디렉터리에 `BookService` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-358">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="32d2d-359">위의 코드에서 생성자 주입을 통해 DI에서 `IBookstoreDatabaseSettings` 인스턴스가 검색됩니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-359">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="32d2d-360">이 기술은 [구성 모델 추가](#add-a-configuration-model) 섹션에 추가된 *appsettings.json* 구성 값에 대한 액세스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-360">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="32d2d-361">다음 강조 표시된 코드를 `Startup.ConfigureServices`에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-361">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="32d2d-362">위의 코드에서 `BookService` 클래스는 사용 클래스에서 생성자 주입을 지원하는 DI로 등록됩니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-362">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="32d2d-363">`BookService`가 `MongoClient`에 직접 종속되기 때문에 싱글톤 서비스 수명이 가장 적합합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-363">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="32d2d-364">공식 [Mongo 클라이언트 재사용 지침](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)에 따라 `MongoClient`를 싱글톤 수명으로 DI에 등록해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-364">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="32d2d-365">*Startup.cs*의 맨 위에 다음 코드를 추가하여 `BookService` 참조를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-365">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="32d2d-366">`BookService` 클래스는 다음 `MongoDB.Driver` 멤버를 사용하여 데이터베이스에 대해 CRUD 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-366">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="32d2d-367">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; 데이터베이스 작업을 수행하기 위한 서버 인스턴스를 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-367">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="32d2d-368">이 클래스의 생성자에 MongoDB 연결 문자열이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-368">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="32d2d-369">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; 작업 수행을 위한 Mongo 데이터베이스를 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-369">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="32d2d-370">이 자습서에서는 인터페이스의 제네릭 [GetCollection\<TDocument>(컬렉션)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) 메서드를 사용하여 특정 컬렉션의 데이터에 액세스합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-370">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="32d2d-371">이 메서드를 호출한 후 컬렉션에 대해 CRUD 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-371">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="32d2d-372">`GetCollection<TDocument>(collection)` 메서드 호출에서 다음을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-372">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="32d2d-373">`collection`은 컬렉션 이름을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-373">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="32d2d-374">`TDocument`는 컬렉션에 저장된 CLR 개체 형식을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-374">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="32d2d-375">`GetCollection<TDocument>(collection)`는 컬렉션을 나타내는 [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) 개체를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-375">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="32d2d-376">이 자습서에서는 컬렉션에 대해 다음 메서드를 호출합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-376">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="32d2d-377">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; 제공된 검색 조건과 일치하는 단일 문서를 삭제합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-377">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="32d2d-378">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; 제공된 검색 조건과 일치하는 컬렉션의 모든 문서를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-378">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="32d2d-379">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; 제공된 개체를 컬렉션에 새 문서로 삽입합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-379">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="32d2d-380">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; 제공된 검색 조건과 일치하는 단일 문서를 제공된 개체로 바꿉니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-380">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="32d2d-381">컨트롤러 추가</span><span class="sxs-lookup"><span data-stu-id="32d2d-381">Add a controller</span></span>

<span data-ttu-id="32d2d-382">다음 코드를 사용하여 *Controllers* 디렉터리에 `BooksController` 클래스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-382">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="32d2d-383">앞의 웹 API 컨트롤러는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-383">The preceding web API controller:</span></span>

* <span data-ttu-id="32d2d-384">`BookService` 클래스를 사용하여 CRUD 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-384">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="32d2d-385">GET, POST, PUT 및 DELETE HTTP 요청을 지원하는 작업 메서드를 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-385">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="32d2d-386">`Create` 작업 메서드에서 <xref:System.Web.Http.ApiController.CreatedAtRoute*>를 호출하여 [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) 응답을 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-386">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="32d2d-387">상태 코드 201은 서버에서 새 리소스를 만드는 HTTP POST 메서드의 표준 응답입니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-387">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="32d2d-388">또한 `CreatedAtRoute`는 `Location` 헤더를 응답에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-388">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="32d2d-389">`Location` 헤더는 새로 만든 책의 URI를 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-389">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="32d2d-390">웹 API 테스트</span><span class="sxs-lookup"><span data-stu-id="32d2d-390">Test the web API</span></span>

1. <span data-ttu-id="32d2d-391">앱을 빌드하고 실행합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-391">Build and run the app.</span></span>

1. <span data-ttu-id="32d2d-392">`http://localhost:<port>/api/books`로 이동하여 컨트롤러의 매개 변수가 없는 `Get` 작업 메서드를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-392">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="32d2d-393">다음 JSON 응답이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-393">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="32d2d-394">`http://localhost:<port>/api/books/{id here}`로 이동하여 컨트롤러의 오버로드된 `Get` 작업 메서드를 테스트합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-394">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="32d2d-395">다음 JSON 응답이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-395">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="32d2d-396">JSON serialization 옵션 구성</span><span class="sxs-lookup"><span data-stu-id="32d2d-396">Configure JSON serialization options</span></span>

<span data-ttu-id="32d2d-397">[웹 API 테스트](#test-the-web-api) 섹션에서 반환된 JSON 응답에 대해 변경하는 두 개의 세부 정보가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-397">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="32d2d-398">속성 이름의 기본 카멜식 대/소문자는 CLR 개체의 속성 이름의 파스칼식 대/소문자와 일치하도록 변경되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-398">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="32d2d-399">`bookName` 속성은 `Name`으로 반환되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-399">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="32d2d-400">앞의 요구 사항을 충족하기 위해 다음과 같이 변경합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-400">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="32d2d-401">`Startup.ConfigureServices`에서 다음 강조 표시된 코드를 `AddMvc` 메서드 호출에 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-401">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="32d2d-402">이전 변경으로 웹 API의 직렬화된 JSON 응답에서 속성 이름은 CLR 개체 형식의 해당 속성 이름과 일치합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-402">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="32d2d-403">예를 들어 `Book` 클래스의 `Author` 속성은 `Author`로 직렬화합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-403">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="32d2d-404">*Models/Book.cs*에서 다음 [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) 특성으로 `BookName` 속성에 주석을 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-404">In *Models/Book.cs*, annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="32d2d-405">`Name`의 `[JsonProperty]` 특성 값은 웹 API의 직렬화된 JSON 응답의 속성 이름을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-405">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="32d2d-406">*Models/Book.cs*의 맨 위에 다음 코드를 추가하여 `[JsonProperty]` 특성 참조를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-406">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="32d2d-407">[웹 API 테스트](#test-the-web-api) 섹션에 정의된 단계를 반복합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-407">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="32d2d-408">JSON 속성 이름의 차이를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="32d2d-408">Notice the difference in JSON property names.</span></span>

::: moniker-end

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="32d2d-409">웹 API에 인증 지원 추가</span><span class="sxs-lookup"><span data-stu-id="32d2d-409">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="next-steps"></a><span data-ttu-id="32d2d-410">다음 단계</span><span class="sxs-lookup"><span data-stu-id="32d2d-410">Next steps</span></span>

<span data-ttu-id="32d2d-411">ASP.NET Core 웹 API 빌드 방법에 대한 자세한 내용은 다음 리소스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="32d2d-411">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="32d2d-412">이 문서의 YouTube 버전</span><span class="sxs-lookup"><span data-stu-id="32d2d-412">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
