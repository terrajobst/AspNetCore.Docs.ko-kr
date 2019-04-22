---
title: ASP.NET Core 및 MongoDB를 사용하여 웹 API 빌드
author: prkhandelwal
description: 이 자습서에서는 MongoDB NoSQL 데이터베이스를 사용하여 ASP.NET Core 웹 API를 빌드하는 방법을 보여 줍니다.
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 01/31/2019
uid: tutorials/first-mongo-app
ms.openlocfilehash: 95a5f8bdb4b302d6bdae7b5809b54f1b263e6ee4
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59012866"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a>ASP.NET Core 및 MongoDB를 사용하여 웹 API 만들기

작성자: [Pratik Khandelwal](https://twitter.com/K2Prk) 및 [Scott Addie](https://twitter.com/Scott_Addie)

이 자습서에서는 [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL 데이터베이스에 대해 CRUD(만들기, 읽기, 업데이트 및 삭제) 작업을 수행하는 웹 API를 만듭니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * MongoDB 구성
> * MongoDB 데이터베이스 만들기
> * MongoDB 컬렉션 및 스키마 정의
> * 웹 API에서 MongoDB CRUD 작업 수행

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-mongo-app/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>전제 조건

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* [.NET Core SDK 2.2 이상](https://www.microsoft.com/net/download/all)
* [Visual Studio 2017 버전 15.9 이상](https://www.visualstudio.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017)(**ASP.NET 및 웹 개발** 워크로드 필요)
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [.NET Core SDK 2.2 이상](https://www.microsoft.com/net/download/all)
* [Visual Studio Code](https://code.visualstudio.com/download)
* [Visual Studio Code용 C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [MongoDB](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* [.NET Core SDK 2.2 이상](https://www.microsoft.com/net/download/all)
* [Mac용 Visual Studio 버전 7.7 이상](https://www.visualstudio.com/downloads/)
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a>MongoDB 구성

Windows를 사용하는 경우 MongoDB는 기본적으로*C:\\Program Files\\MongoDB*에 설치됩니다. *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin*을 `Path` 환경 변수에 추가합니다. 이렇게 변경하면 개발 머신의 어디에서나 MongoDB에 액세스할 수 있습니다.

다음 단계에서 mongo 셸을 사용하여 데이터베이스 및 컬렉션을 만들고 문서를 저장합니다. mongo 셸 명령에 대한 자세한 내용은 [mongo 셸 작업](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell)을 참조하세요.

1. 개발 머신에서 데이터를 저장할 디렉터리를 선택합니다. 예를 들어 Windows의 경우 *C:\\BooksData*를 선택합니다. 디렉터리가 없을 경우 새로 만듭니다. mongo 셸은 새 디렉터리를 만들지 않습니다.
1. 명령 셸을 엽니다. 다음 명령을 실행하여 기본 포트 27017에서 MongoDB에 연결합니다. `<data_directory_path>`를 이전 단계에서 선택한 디렉터리로 바꿔야 합니다.

    ```console
    mongod --dbpath <data_directory_path>
    ```

1. 다른 명령 셸 인스턴스를 엽니다. 다음 명령을 실행하여 기본 테스트 데이터베이스에 연결합니다.

    ```console
    mongo
    ```

1. 명령 셸에서 다음을 실행합니다.

    ```console
    use BookstoreDb
    ```

    아직 존재하지 않는 경우 *BookstoreDb*라는 데이터베이스가 생성됩니다. 데이터베이스가 있는 경우 트랜잭션을 위해 해당 연결이 열립니다.

1. 다음 명령을 사용하여 `Books` 컬렉션을 만듭니다.

    ```console
    db.createCollection('Books')
    ```

    다음 결과가 표시됩니다.

    ```console
    { "ok" : 1 }
    ```

1. 다음 명령을 사용하여 `Books` 컬렉션에 대한 스키마를 정의하고 두 개의 문서를 삽입합니다.

    ```console
    db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
    ```

    다음 결과가 표시됩니다.

    ```console
    {
      "acknowledged" : true,
      "insertedIds" : [
        ObjectId("5bfd996f7b8e48dc15ff215d"),
        ObjectId("5bfd996f7b8e48dc15ff215e")
      ]
    }
    ```

1. 다음 명령을 사용하여 데이터베이스의 문서를 봅니다.

    ```console
    db.Books.find({}).pretty()
    ```

    다음 결과가 표시됩니다.

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

    스키마가 각 문서에 대해 자동 생성된 `ObjectId` 형식의 `_id` 속성을 추가합니다.

데이터베이스가 준비되었습니다. 이제 ASP.NET Core 웹 API를 만들기 시작할 수 있습니다.

## <a name="create-the-aspnet-core-web-api-project"></a>ASP.NET Core 웹 API 프로젝트 만들기

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. **파일** > **새로 만들기** > **프로젝트**로 이동합니다.
1. **ASP.NET Core 웹 애플리케이션**을 선택하고 프로젝트 이름을 *BooksApi*로 지정한 다음, **확인**을 클릭합니다.
1. **.NET Core** 대상 프레임워크 및 **ASP.NET Core 2.2**를 선택합니다. **API** 프로젝트 템플릿을 선택하고 **확인**을 클릭합니다.
1. [NuGet 갤러리: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/)를 방문하여 MongoDB용 .NET 드라이버의 안정적인 최신 버전을 확인합니다. **패키지 관리자 콘솔** 창에서 프로젝트 루트로 이동합니다. 다음 명령을 실행하여 MongoDB용 .NET 드라이버를 설치합니다.

    ```powershell
    Install-Package MongoDB.Driver -Version {VERSION}
    ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. 명령 셸에서 다음 명령을 실행합니다.

    ```console
    dotnet new webapi -o BooksApi
    code BooksApi
    ```

    .NET Core를 대상으로 하는 새로운 ASP.NET Core 웹 API 프로젝트가 Visual Studio Code에서 생성되어 열립니다.

1. ‘빌드 및 디버그에 필요한 자산이 ‘BooksApi’에서 누락되었습니다. 추가하시겠습니까?’ 알림이 표시되면 **예**를 클릭합니다. 
1. [NuGet 갤러리: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/)를 방문하여 MongoDB용 .NET 드라이버의 안정적인 최신 버전을 확인합니다. **통합 터미널**을 열고 프로젝트 루트로 이동합니다. 다음 명령을 실행하여 MongoDB용 .NET 드라이버를 설치합니다.

    ```console
    dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
    ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. **파일** > **새 솔루션** > **.NET Core** > **앱**으로 이동합니다.
1. **ASP.NET Core Web API** C# 프로젝트 템플릿을 선택하고 **다음**을 클릭합니다.
1. **대상 프레임워크** 드롭다운 목록에서 **.NET Core 2.2**를 선택하고 **다음**을 클릭합니다.
1. **프로젝트 이름**으로 *BooksApi*를 입력한 다음, **만들기**를 클릭합니다.
1. **솔루션** 패드에서 프로젝트의 **종속성** 노드를 마우스 오른쪽 단추로 클릭하고 **패키지 추가**를 선택합니다.
1. 검색 상자에 *MongoDB.Driver*를 입력하고 *MongoDB.Driver* 패키지를 선택한 다음, **패키지 추가**를 클릭합니다.
1. **라이선스 승인** 대화 상자에서 **동의** 단추를 클릭합니다.

---

## <a name="add-a-model"></a>모델 추가

1. 프로젝트 루트에 *Models* 디렉터리를 추가합니다.
1. 다음 코드를 사용하여 *Models* 디렉터리에 `Book` 클래스를 추가합니다.

    [!code-csharp[](first-mongo-app/sample/BooksApi/Models/Book.cs)]

앞의 클래스에서 `Id` 속성은

* CLR(공용 언어 런타임) 개체를 MongoDB 컬렉션에 매핑하는 데 필요합니다.
* 이 속성을 문서의 기본 키로 지정하려면 `[BsonId]`로 주석이 추가됩니다.
* `ObjectId` 대신 `string` 형식으로 매개 변수를 전달할 수 있도록 `[BsonRepresentation(BsonType.ObjectId)]`로 주석이 추가됩니다. Mongo는 `string`에서 `ObjectId`로 변환을 처리합니다.

클래스의 기타 속성은 `[BsonElement]` 특성으로 주석이 추가됩니다. 특성 값은 MongoDB 컬렉션의 속성 이름을 나타냅니다.

## <a name="add-a-crud-operations-class"></a>CRUD 작업 클래스 추가

1. 프로젝트 루트에 *Services* 디렉터리를 추가합니다.
1. 다음 코드를 사용하여 *Services* 디렉터리에 `BookService` 클래스를 추가합니다.

    [!code-csharp[](first-mongo-app/sample/BooksApi/Services/BookService.cs?name=snippet_BookServiceClass)]

1. *appsettings.json*에 MongoDB 연결 문자열을 추가합니다.

    [!code-csharp[](first-mongo-app/sample/BooksApi/appsettings.json?highlight=2-4)]

    앞의 `BookstoreDb` 속성은 `BookService` 클래스 생성자에서 액세스됩니다.

1. `Startup.ConfigureServices`에서 `BookService` 클래스를 종속성 삽입 시스템에 등록합니다.

    [!code-csharp[](first-mongo-app/sample/BooksApi/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

    앞의 서비스 등록은 사용 클래스에서 생성자 삽입을 지원하는 데 필요합니다.

`BookService` 클래스는 다음 `MongoDB.Driver` 멤버를 사용하여 데이터베이스에 대해 CRUD 작업을 수행합니다.

* `MongoClient` &ndash; 데이터베이스 작업을 수행하기 위한 서버 인스턴스를 읽습니다. 이 클래스의 생성자에 MongoDB 연결 문자열이 제공됩니다.

    [!code-csharp[](first-mongo-app/sample/BooksApi/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* `IMongoDatabase` &ndash; 작업 수행을 위한 Mongo 데이터베이스를 나타냅니다. 이 자습서에서는 인터페이스의 일반 `GetCollection<T>(collection)` 메서드를 사용하여 특정 컬렉션의 데이터에 액세스합니다. 이 메서드를 호출한 후 컬렉션에 대해 CRUD 작업을 수행할 수 있습니다. `GetCollection<T>(collection)` 메서드 호출에서 다음을 수행합니다.
  * `collection`은 컬렉션 이름을 나타냅니다.
  * `T`는 컬렉션에 저장된 CLR 개체 형식을 나타냅니다.

`GetCollection<T>(collection)`은 컬렉션을 나타내는 `MongoCollection` 개체를 반환합니다. 이 자습서에서는 컬렉션에 대해 다음 메서드를 호출합니다.

* `Find<T>` &ndash; 제공된 검색 조건과 일치하는 컬렉션의 모든 문서를 반환합니다.
* `InsertOne` &ndash; 제공된 개체를 컬렉션에 새 문서로 삽입합니다.
* `ReplaceOne` &ndash; 제공된 검색 조건과 일치하는 단일 문서를 제공된 개체로 바꿉니다.
* `DeleteOne` &ndash; 제공된 검색 조건과 일치하는 단일 문서를 삭제합니다.

## <a name="add-a-controller"></a>컨트롤러 추가

1. 다음 코드를 사용하여 *Controllers* 디렉터리에 `BooksController` 클래스를 추가합니다.

    [!code-csharp[](first-mongo-app/sample/BooksApi/Controllers/BooksController.cs)]

    앞의 웹 API 컨트롤러는 다음과 같습니다.

    * `BookService` 클래스를 사용하여 CRUD 작업을 수행합니다.
    * GET, POST, PUT 및 DELETE HTTP 요청을 지원하는 작업 메서드를 포함합니다.
    * <xref:System.Web.Http.ApiController.CreatedAtRoute*> 메서드는 서버에서 새 리소스를 만드는 HTTP POST 메서드의 표준 응답인 201 응답을 반환합니다. `CreatedAtRoute`는 응답에 대한 위치 헤더도 추가합니다. 위치 헤더는 새로 만들어진 할 일 항목의 URI를 지정합니다. [10.2.2 201 Created](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)(10.2.2 201 생성됨)를 참조하세요.
1. 앱을 빌드하고 실행합니다.
1. 브라우저에서 `http://localhost:<port>/api/books`로 이동합니다. 다음 JSON 응답이 표시됩니다.

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

## <a name="next-steps"></a>다음 단계

ASP.NET Core 웹 API 빌드 방법에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [이 문서의 Youtube 버전](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
