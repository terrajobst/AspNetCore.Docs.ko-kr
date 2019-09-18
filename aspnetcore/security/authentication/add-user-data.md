---
title: 추가, 다운로드 및 ASP.NET Core 프로젝트의 Id로 사용자 데이터를 삭제 합니다.
author: rick-anderson
description: ASP.NET Core 프로젝트에서 Id에 사용자 지정 사용자 데이터를 추가 하는 방법에 알아봅니다. GDPR에 따라 데이터를 삭제 합니다.
ms.author: riande
ms.date: 06/18/2019
ms.custom: mvc, seodec18
uid: security/authentication/add-user-data
ms.openlocfilehash: f5a47ffd2e068414268ed9037d4376bfd21ba1bb
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080803"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a>추가, 다운로드 및 ASP.NET Core 프로젝트에서 Id에 사용자 지정 사용자 데이터를 삭제 합니다.

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

이 아티클에서 방법.

* ASP.NET Core 웹 앱에 사용자 지정 사용자 데이터를 추가 합니다.
* 사용자 지정 사용자 데이터 모델을 <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> 특성으로 데코레이팅 하 여 자동으로 다운로드 및 삭제에 사용할 수 있도록 합니다. 충족 하는 사용 하면 데이터를 다운로드 하 고 삭제 하는 일을 할 [GDPR](xref:security/gdpr) 요구 사항입니다.

프로젝트 샘플에는 Razor 페이지 웹 앱에서 만들어지지만 지침은 ASP.NET Core MVC 웹 앱에 대해 유사 합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>전제 조건

[!INCLUDE [](~/includes/2.2-SDK.md)]

## <a name="create-a-razor-web-app"></a>Razor 웹앱 만들기

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio **파일** 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다. 프로젝트 이름을 **WebApp1** 되도록 하려는 경우의 네임 스페이스와 일치 합니다 [샘플을 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) 코드입니다.
* 선택 **ASP.NET Core 웹 응용 프로그램** > **확인**
* 드롭다운에서 **ASP.NET Core 2.2** 을 선택 합니다.
* 선택 **웹 응용 프로그램**  > **확인**
* 프로젝트를 빌드하고 실행합니다.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a>Identity 스 캐 폴더를 실행 합니다.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭 > **추가** > **스 캐 폴드 된 새 항목**합니다.
* 왼쪽된 창에서 합니다 **스 캐 폴드 추가** 대화 상자에서 **Identity** > **추가**합니다.
* 에 **ADD Id** 대화 상자에서 다음 옵션:
  * 기존 레이아웃 파일 선택 *~/Pages/Shared/_Layout.cshtml*
  * 재정의 하려면 다음 파일을 선택 합니다.
    * **계정/등록**
    * **계정 / / 인덱스 관리**
  * 선택 된 **+** 새 단추 **데이터 컨텍스트 클래스**합니다. 형식을 수락 (**WebApp1.Models.WebApp1Context** 프로젝트의 이름이 **WebApp1**).
  * 선택 된 **+** 새 단추 **사용자 클래스**합니다. 형식을 수락 (**WebApp1User** 프로젝트의 이름이 **WebApp1**) > **추가**합니다.
* 선택 **추가**합니다.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

ASP.NET Core 스 캐 폴더를 이전에 설치 하지 않은 경우 지금 설치 합니다.

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

에 대 한 패키지 참조 추가 [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) 프로젝트 (.csproj) 파일에 있습니다. 프로젝트 디렉터리에서 다음 명령을 실행 합니다.

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

지침에 따라 [마이그레이션과 UseAuthentication, 레이아웃](xref:security/authentication/scaffold-identity#efm) 다음 단계를 수행 합니다.

* 마이그레이션을 만들고 데이터베이스를 업데이트 합니다.
* `UseAuthentication`를 `Startup.Configure`에 추가합니다.
* 추가 `<partial name="_LoginPartial" />` 레이아웃 파일입니다.
* 앱을 테스트합니다.
  * 사용자 등록
  * 새 사용자 이름 선택 (옆에 **로그 아웃** 링크). 창이 확장 또는 사용자 이름 및 다른 링크를 표시 하려면 탐색 모음 아이콘을 선택 해야 합니다.
  * 선택 된 **개인 데이터** 탭 합니다.
  * 선택 합니다 **다운로드** 단추를 검사 합니다 *PersonalData.json* 파일.
  * 테스트를 **삭제** 단추를 사용자에서 로그인을 삭제 합니다.

## <a name="add-custom-user-data-to-the-identity-db"></a>Identity DB 사용자 지정 데이터 추가

업데이트 된 `IdentityUser` 사용자 지정 속성을 사용 하 여 클래스를 파생 합니다. 파일 이름은 프로젝트 구성이 WebApp1 라는 하는 경우 *Areas/Identity/Data/WebApp1User.cs*합니다. 다음 코드를 사용 하 여 파일을 업데이트 합니다.

[!code-csharp[Main](add-user-data/sample-2.2/Areas/Identity/Data/WebApp1User.cs)]

속성으로 데코 레이트 합니다 [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute?view=aspnetcore-2.1) 특성:

* 삭제 된 *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor 페이지 호출 `UserManager.Delete`합니다.
* 다운로드 한 데이터에 포함 된 *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor 페이지.

### <a name="update-the-accountmanageindexcshtml-page"></a>Account/Manage/Index.cshtml 페이지 업데이트

업데이트를 `InputModel` 에 *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* 강조 표시 된 코드를 다음으로:

[!code-csharp[Main](add-user-data/sample-2.2/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

업데이트를 *Areas/Identity/Pages/Account/Manage/Index.cshtml* 다음 강조 표시 된 태그를 사용 하 여:

[!code-html[Main](add-user-data/sample-2.2/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

### <a name="update-the-accountregistercshtml-page"></a>Account/Register.cshtml 페이지가 업데이트

업데이트를 `InputModel` 에 *Areas/Identity/Pages/Account/Register.cshtml.cs* 강조 표시 된 코드를 다음으로:

[!code-csharp[Main](add-user-data/sample-2.2/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

업데이트를 *Areas/Identity/Pages/Account/Register.cshtml* 다음 강조 표시 된 태그를 사용 하 여:

[!code-html[Main](add-user-data/sample-2.2/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

프로젝트를 빌드합니다.

### <a name="add-a-migration-for-the-custom-user-data"></a>사용자 지정 사용자 데이터에 대 한 마이그레이션을 추가합니다

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual studio에서 **패키지 관리자 콘솔**:

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a>테스트 만들기, 보기, 다운로드, 사용자 지정 사용자 데이터를 삭제 합니다.

앱을 테스트합니다.

* 새 사용자를 등록 합니다.
* 사용자 지정 사용자 데이터를 보려면를 `/Identity/Account/Manage` 페이지입니다.
* 다운로드 하 고 사용자 개인 데이터를 볼는 `/Identity/Account/Manage/PersonalData` 페이지입니다.
