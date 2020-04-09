---
title: ASP.NET 핵심 프로젝트에서 ID에 사용자 데이터 추가, 다운로드 및 삭제
author: rick-anderson
description: ASP.NET Core 프로젝트에서 ID에 사용자 지정 사용자 데이터를 추가하는 방법을 알아봅니다. GDPR당 데이터를 삭제합니다.
ms.author: riande
ms.date: 03/26/2020
ms.custom: mvc, seodec18
uid: security/authentication/add-user-data
ms.openlocfilehash: 76b83df22381429feab80056c36dbdac1e5f20c7
ms.sourcegitcommit: 1d8f1396ccc66a0c3fcb5e5f36ea29b50db6d92a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501224"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a>ASP.NET 핵심 프로젝트에서 ID에 사용자 지정 사용자 데이터를 추가, 다운로드 및 삭제

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

이 문서는 다음 방법을 안내합니다.

* 사용자 지정 사용자 데이터를 ASP.NET Core 웹 앱에 추가합니다.
* 사용자 지정 사용자 데이터 <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> 모델을 특성에 표시하여 다운로드 및 삭제를 자동으로 사용할 수 있도록 합니다. 데이터를 다운로드하고 삭제할 수 있도록 하면 [GDPR](xref:security/gdpr) 요구 사항을 충족하는 데 도움이 됩니다.

프로젝트 샘플은 Razor Pages 웹 앱에서 만들어지지만 지침은 ASP.NET 코어 MVC 웹 앱과 유사합니다.

[샘플 코드 보기 또는 다운로드(다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) [방법)](xref:index#how-to-download-a-sample)

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

* Visual Studio **파일** 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다. [다운로드 샘플](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) 코드의 네임스페이스와 일치하려면 프로젝트 **WebApp1의** 이름을 지정합니다.
* **ASP.NET 핵심 웹 응용 프로그램** > **확인** 선택
* 드롭다운에서 **ASP.NET 코어 3.0** 선택
* **웹 응용 프로그램** > **확인** 선택
* 프로젝트를 빌드하고 실행합니다.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Visual Studio **파일** 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다. [다운로드 샘플](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) 코드의 네임스페이스와 일치하려면 프로젝트 **WebApp1의** 이름을 지정합니다.
* **ASP.NET 핵심 웹 응용 프로그램** > **확인** 선택
* 드롭다운에서 **코어 ASP.NET 2.2** 선택
* **웹 응용 프로그램** > **확인** 선택
* 프로젝트를 빌드하고 실행합니다.

::: moniker-end


# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a>ID 폴더 실행

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **솔루션 탐색기에서**프로젝트를 마우스 오른쪽 단추로 클릭하여**새 스캐폴드 항목** **추가** > 를 >.
* **스캐폴드 추가** 대화 상자의 왼쪽 창에서 **ID** > **추가를**선택합니다.
* ID **추가** 대화 상자에서 다음 옵션이 있습니다.
  * 기존 레이아웃 파일 *~/페이지/공유/_Layout.cshtml을* 선택합니다.
  * 재정의할 다음 파일을 선택합니다.
    * **계정/등록**
    * **계정/관리/색인**
  * 단추를 **+** 선택하여 새 **데이터 컨텍스트 클래스를**만듭니다. 형식**수락(WebApp1.Models.WebApp1Context** 프로젝트의 이름이 **WebApp1인**경우 컨텍스트).
  * 단추를 **+** 선택하여 새 **사용자 클래스를**만듭니다. 형식 수락 **(WebApp1User** 프로젝트의 이름이 **WebApp1)**> **추가**.
* **추가**를 선택합니다.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

이전에 ASP.NET 코어 scaffolder를 설치하지 않은 경우 지금 설치하십시오.

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

프로젝트(.csproj) 파일에 [Microsoft.VisualStudio.Web.CodeGeneration.Design에](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) 패키지 참조를 추가합니다. 프로젝트 디렉터리에서 다음 명령을 실행합니다.

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

다음 명령을 실행하여 Id scaffolder 옵션을 나열합니다.

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

프로젝트 폴더에서 ID scaffolder를 실행합니다.

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

[마이그레이션, 사용 인증 및 레이아웃의](xref:security/authentication/scaffold-identity#efm) 지침에 따라 다음 단계를 수행합니다.

* 마이그레이션을 만들고 데이터베이스를 업데이트합니다.
* `UseAuthentication`을 `Startup.Configure`에 추가합니다.
* 레이아웃 `<partial name="_LoginPartial" />` 파일에 추가합니다.
* 앱을 테스트합니다.
  * 사용자 등록
  * **로그아웃** 링크 옆의 새 사용자 이름을 선택합니다. 사용자 이름 및 기타 링크를 표시하려면 창을 확장하거나 탐색 모음 아이콘을 선택해야 할 수 있습니다.
  * 개인 **데이터** 탭을 선택합니다.
  * **다운로드** 단추를 선택하고 *PersonalData.json* 파일을 검사합니다.
  * 로그온한 사용자를 삭제하는 **삭제** 단추를 테스트합니다.

## <a name="add-custom-user-data-to-the-identity-db"></a>ID DB에 사용자 지정 사용자 데이터 추가

사용자 `IdentityUser` 지정 속성으로 파생 클래스를 업데이트합니다. 프로젝트 WebApp1의 이름을 지정한 경우 파일의 이름이 *영역/ID/데이터/WebApp1User.cs입니다.* 다음 코드로 파일을 업데이트합니다.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

[PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) 특성이 있는 속성은 다음과 같습니다.

* *영역/ID/페이지/계정/관리/삭제개인데이터.cshtml* 면도기 페이지가 `UserManager.Delete`호출될 때 삭제됩니다.
* *영역 / ID / 페이지 / 계정 / 관리 / 다운로드개인 데이터.cshtml* 면도기 페이지에 의해 다운로드 된 데이터에 포함되어 있습니다.

### <a name="update-the-accountmanageindexcshtml-page"></a>계정/관리/Index.cshtml 페이지 업데이트

다음 `InputModel` 강조 표시된 코드로 *영역/ID/페이지/계정/관리/Index.cshtml.cs.cs.cs에서* 업데이트합니다.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

다음 강조 표시된 태그로 *영역/ID/페이지/계정/관리/Index.cshtml을* 업데이트합니다.

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

다음 강조 표시된 태그로 *영역/ID/페이지/계정/관리/Index.cshtml을* 업데이트합니다.

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a>계정/Register.cshtml 페이지 업데이트

`InputModel` 다음 강조 표시된 코드로 *영역/ID/페이지/계정/Register.cshtml.cs에서* 업데이트합니다.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

다음 강조 표시된 태그로 *영역/ID/페이지/계정/Register.cshtml을* 업데이트합니다.

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

다음 강조 표시된 태그로 *영역/ID/페이지/계정/Register.cshtml을* 업데이트합니다.

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


프로젝트를 빌드합니다.

### <a name="add-a-migration-for-the-custom-user-data"></a>사용자 지정 사용자 데이터에 대한 마이그레이션 추가

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

비주얼 스튜디오 **패키지 관리자 콘솔에서**:

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

## <a name="test-create-view-download-delete-custom-user-data"></a>사용자 지정 사용자 데이터 만들기, 보기, 다운로드, 삭제

앱을 테스트합니다.

* 새 사용자를 등록합니다.
* 페이지에서 사용자 지정 사용자 `/Identity/Account/Manage` 데이터를 봅니다.
* 페이지에서 사용자 개인 데이터를 다운로드하고 봅니다. `/Identity/Account/Manage/PersonalData`

## <a name="add-claims-to-identity-using-iuserclaimsprincipalfactoryapplicationuser"></a>IUser클레임스프렉토리를 사용하여 ID에 클레임 추가<ApplicationUser>

인터페이스를 사용하여 ASP.NET 코어 ID에 `IUserClaimsPrincipalFactory<T>` 추가 클레임을 추가할 수 있습니다. 이 클래스는 `Startup.ConfigureServices` 메서드의 앱에 추가할 수 있습니다. 다음과 같이 클래스의 사용자 지정 구현을 추가합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddScoped<IUserClaimsPrincipalFactory<ApplicationUser>, 
        AdditionalUserClaimsPrincipalFactory>();
```

데모 코드는 `ApplicationUser` 클래스를 사용합니다. 이 클래스는 `IsAdmin` 추가 클레임을 추가하는 데 사용되는 속성을 추가합니다.

```csharp
public class ApplicationUser : IdentityUser
{
    public bool IsAdmin { get; set; }
}
```

`AdditionalUserClaimsPrincipalFactory`는 `UserClaimsPrincipalFactory` 인터페이스를 구현합니다. 에 새 역할 클레임이 `ClaimsPrincipal`추가됩니다.

```csharp
public class AdditionalUserClaimsPrincipalFactory 
        : UserClaimsPrincipalFactory<ApplicationUser, IdentityRole>
{
    public AdditionalUserClaimsPrincipalFactory( 
        UserManager<ApplicationUser> userManager,
        RoleManager<IdentityRole> roleManager, 
        IOptions<IdentityOptions> optionsAccessor) 
        : base(userManager, roleManager, optionsAccessor)
    {}

    public async override Task<ClaimsPrincipal> CreateAsync(ApplicationUser user)
    {
        var principal = await base.CreateAsync(user);
        var identity = (ClaimsIdentity)principal.Identity;

        var claims = new List<Claim>();
        if (user.IsAdmin)
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "admin"));
        }
        else
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "user"));
        }

        identity.AddClaims(claims);
        return principal;
    }
}
```

그런 다음 추가 클레임을 앱에서 사용할 수 있습니다. Razor 페이지에서 인스턴스를 `IAuthorizationService` 사용하여 클레임 값에 액세스할 수 있습니다.

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

@if ((await AuthorizationService.AuthorizeAsync(User, "IsAdmin")).Succeeded)
{
    <ul class="mr-auto navbar-nav">
        <li class="nav-item">
            <a class="nav-link" asp-controller="Admin" asp-action="Index">ADMIN</a>
        </li>
    </ul>
}
```
