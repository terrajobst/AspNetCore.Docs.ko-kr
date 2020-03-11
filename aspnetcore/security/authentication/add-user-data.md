---
title: 추가, 다운로드 및 ASP.NET Core 프로젝트의 Id로 사용자 데이터를 삭제 합니다.
author: rick-anderson
description: ASP.NET Core 프로젝트에서 Id에 사용자 지정 사용자 데이터를 추가 하는 방법에 알아봅니다. GDPR에 따라 데이터를 삭제 합니다.
ms.author: riande
ms.date: 01/28/2020
ms.custom: mvc, seodec18
uid: security/authentication/add-user-data
ms.openlocfilehash: 7a67f55da0e685ed3fd5badb30e8be683411a5ae
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653283"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a>추가, 다운로드 및 ASP.NET Core 프로젝트에서 Id에 사용자 지정 사용자 데이터를 삭제 합니다.

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

이 문서는 다음 방법을 안내합니다.

* ASP.NET Core 웹 앱에 사용자 지정 사용자 데이터를 추가 합니다.
* 사용자 지정 사용자 데이터 모델을 자동으로 다운로드 및 삭제에 사용할 수 있도록 <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> 특성으로 표시 합니다. 데이터를 다운로드 하 고 삭제할 수 있도록 하는 것은 [Gdpr](xref:security/gdpr) 요구 사항을 충족 하는 데 도움이 됩니다.

프로젝트 샘플에는 Razor 페이지 웹 앱에서 만들어지지만 지침은 ASP.NET Core MVC 웹 앱에 대해 유사 합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>사전 요구 사항

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a>Razor 웹앱 만들기

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* Visual Studio **파일** 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다. [다운로드 샘플](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) 코드의 네임 스페이스와 일치 시키려는 경우 프로젝트 이름을 **WebApp1** 로 표시 합니다.
* **ASP.NET Core 웹 응용 프로그램** > **확인을** 선택 합니다.
* 드롭다운에서 **ASP.NET Core 3.0** 을 선택 합니다.
* **웹 응용 프로그램** 선택 > **확인**
* 프로젝트를 빌드 및 실행합니다.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Visual Studio **파일** 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다. [다운로드 샘플](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) 코드의 네임 스페이스와 일치 시키려는 경우 프로젝트 이름을 **WebApp1** 로 표시 합니다.
* **ASP.NET Core 웹 응용 프로그램** > **확인을** 선택 합니다.
* 드롭다운에서 **ASP.NET Core 2.2** 을 선택 합니다.
* **웹 응용 프로그램** 선택 > **확인**
* 프로젝트를 빌드 및 실행합니다.

::: moniker-end


# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a>Identity 스 캐 폴더를 실행 합니다.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 > **새 스 캐 폴드 항목** > **추가** 를 클릭 합니다.
* **스 캐 폴드 추가** 대화 상자의 왼쪽 창에서 **id** > **추가**를 선택 합니다.
* **Id 추가** 대화 상자에서 다음 옵션을 선택 합니다.
  * 기존 레이아웃 파일 *~/Pages/Shared/_Layout를 선택 합니다.*
  * 재정의 하려면 다음 파일을 선택 합니다.
    * **계정/등록**
    * **계정/관리/인덱스**
  * **+** 단추를 선택 하 여 새 **데이터 컨텍스트 클래스**를 만듭니다. 프로젝트 이름이 **WebApp1**인 경우 형식 (**WebApp1. WebApp1Context** )을 적용 합니다.
  * **+** 단추를 선택 하 여 새 **사용자 클래스**를 만듭니다. 형식 (프로젝트 이름이 **WebApp1**인 경우**WebApp1User** )을 적용 > **추가**합니다.
* **추가**를 선택합니다.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

ASP.NET Core 스 캐 폴더를 이전에 설치 하지 않은 경우 지금 설치 합니다.

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

프로젝트 (.csproj) 파일에 [VisualStudio](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) 에 대 한 패키지 참조를 추가 합니다. 프로젝트 디렉터리에서 다음 명령을 실행 합니다.

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

Identity 스 캐 폴더 옵션을 나열 하려면 다음 명령을 실행 합니다.

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

프로젝트 폴더에서 Identity 스 캐 폴더를 실행 합니다.

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

[마이그레이션, UseAuthentication 및 레이아웃](xref:security/authentication/scaffold-identity#efm) 의 지침에 따라 다음 단계를 수행 합니다.

* 마이그레이션을 만들고 데이터베이스를 업데이트 합니다.
* `UseAuthentication`을 `Startup.Configure`에 추가합니다.
* 레이아웃 파일에 `<partial name="_LoginPartial" />`를 추가 합니다.
* 앱을 테스트합니다.
  * 사용자 등록
  * 새 사용자 이름 ( **로그 아웃** 링크 옆에 있는)을 선택 합니다. 창이 확장 또는 사용자 이름 및 다른 링크를 표시 하려면 탐색 모음 아이콘을 선택 해야 합니다.
  * **개인 데이터** 탭을 선택 합니다.
  * **다운로드** 단추를 선택 하 고 *PersonalData* 파일을 검사 합니다.
  * 로그온 한 사용자를 삭제 하는 **삭제** 단추를 테스트 합니다.

## <a name="add-custom-user-data-to-the-identity-db"></a>Identity DB 사용자 지정 데이터 추가

`IdentityUser` 파생 클래스를 사용자 지정 속성으로 업데이트 합니다. WebApp1 프로젝트의 이름을 지정 하는 경우 파일의 이름은 *Areas/Identity/Data/WebApp1User*입니다. 다음 코드를 사용 하 여 파일을 업데이트 합니다.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

[PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) 특성이 있는 속성은 다음과 같습니다.

* *영역/i d/페이지/계정/관리/DeletePersonalData* Razor 페이지에서 `UserManager.Delete`를 호출 하면 삭제 됩니다.
* *영역/i d/페이지/계정/관리/DownloadPersonalData* Razor 페이지를 통해 다운로드 한 데이터에 포함 됩니다.

### <a name="update-the-accountmanageindexcshtml-page"></a>Account/Manage/Index.cshtml 페이지 업데이트

다음 강조 표시 된 코드를 사용 하 여 *영역/i d/페이지/계정/관리/* r e p. s s t e m. s s o l e s `InputModel`

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

다음 강조 표시 된 태그를 사용 하 여 *영역/i d/페이지/계정/관리/인덱스를 업데이트 합니다.*

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

다음 강조 표시 된 태그를 사용 하 여 *영역/i d/페이지/계정/관리/인덱스를 업데이트 합니다.*

[!code-chtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a>Account/Register.cshtml 페이지가 업데이트

다음 강조 표시 된 코드를 사용 하 여 *영역/i d/페이지/계정/* s a s e. s s o. s s o. s s o `InputModel`

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

다음과 같이 강조 표시 된 태그를 사용 하 여 *영역/i d/페이지/계정/레지스터* 를 업데이트 합니다.

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

다음과 같이 강조 표시 된 태그를 사용 하 여 *영역/i d/페이지/계정/레지스터* 를 업데이트 합니다.

[!code-chtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


프로젝트를 빌드합니다.

### <a name="add-a-migration-for-the-custom-user-data"></a>사용자 지정 사용자 데이터에 대 한 마이그레이션을 추가합니다

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio **패키지 관리자 콘솔**에서 다음을 수행 합니다.

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a>테스트 만들기, 보기, 다운로드, 사용자 지정 사용자 데이터를 삭제 합니다.

앱을 테스트합니다.

* 새 사용자를 등록 합니다.
* `/Identity/Account/Manage` 페이지에서 사용자 지정 사용자 데이터를 확인 합니다.
* `/Identity/Account/Manage/PersonalData` 페이지에서 사용자 개인 데이터를 다운로드 하 여 봅니다.
