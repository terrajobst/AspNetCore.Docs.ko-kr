---
title: ASP.NET Core 인증 및 Id 마이그레이션
author: ardalis
description: ASP.NET MVC 프로젝트에서 ASP.NET Core MVC 프로젝트로 인증 및 id를 마이그레이션하는 방법에 대해 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
uid: migration/identity
ms.openlocfilehash: f821930dbd36de18db31104cddf34c563009a506
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653013"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core"></a>ASP.NET Core 인증 및 Id 마이그레이션

작성자 [Steve Smith](https://ardalis.com/)

이전 문서에서는 [ASP.NET mvc 프로젝트에서 ASP.NET CORE mvc로 구성을 마이그레이션](xref:migration/configuration)했습니다. 이 문서에서는 등록, 로그인 및 사용자 관리 기능을 마이그레이션합니다.

## <a name="configure-identity-and-membership"></a>Id 및 멤버 자격 구성

ASP.NET MVC에서 인증 및 id 기능은 *App_Start* 폴더에 있는 *Startup.Auth.cs* 및 *IdentityConfig.cs*의 ASP.NET Identity를 사용 하 여 구성 됩니다. ASP.NET Core MVC에서 이러한 기능은 *Startup.cs*에서 구성 됩니다.

`Microsoft.AspNetCore.Identity.EntityFrameworkCore`를 설치 하 고 NuGet 패키지를 `Microsoft.AspNetCore.Authentication.Cookies` 합니다.

그런 다음 *Startup.cs* 를 열고 Entity Framework 및 id 서비스를 사용 하도록 `Startup.ConfigureServices` 메서드를 업데이트 합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add EF services to the services container.
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

     services.AddMvc();
}
```

이 시점에서 ASP.NET MVC 프로젝트에서 아직 마이그레이션하지 않은 두 가지 형식 (`ApplicationDbContext` 및 `ApplicationUser`)이 있습니다. ASP.NET Core 프로젝트에서 새 *모델* 폴더를 만들고 이러한 형식에 해당 하는 두 개의 클래스를 추가 합니다. */Models/IdentityModels.cs*에서 이러한 클래스의 ASP.NET MVC 버전을 찾을 수 있지만 마이그레이션된 프로젝트에서 클래스 당 파일 하나를 사용 하는 것이 더 명확 하기 때문입니다.

*ApplicationUser.cs*:

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;

namespace NewMvcProject.Models
{
  public class ApplicationUser : IdentityUser
  {
  }
}
```

*ApplicationDbContext.cs*:

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;
using Microsoft.Data.Entity;

namespace NewMvcProject.Models
{
    public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }

        protected override void OnModelCreating(ModelBuilder builder)
        {
            base.OnModelCreating(builder);
            // Customize the ASP.NET Core Identity model and override the defaults if needed.
            // For example, you can rename the ASP.NET Core Identity table names and more.
            // Add your customizations after calling base.OnModelCreating(builder);
        }
    }
}
```

ASP.NET Core MVC 스타터 웹 프로젝트에는 사용자의 많은 사용자 지정 또는 `ApplicationDbContext`포함 되지 않습니다. 실제 앱을 마이그레이션할 때 앱이 사용 하는 다른 모델 클래스 뿐만 아니라 앱의 사용자 및 `DbContext` 클래스의 사용자 지정 속성 및 메서드도 모두 마이그레이션해야 합니다. 예를 들어 `DbContext`에 `DbSet<Album>`있는 경우 `Album` 클래스를 마이그레이션해야 합니다.

이러한 파일이 준비 되 면 *Startup.cs* 파일을 `using` 문을 업데이트 하 여 컴파일할 수 있습니다.

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Hosting;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
```

이제 앱이 인증 및 Id 서비스를 지원할 준비가 되었습니다. 이러한 기능은 사용자에 게 노출 되어야 합니다.

## <a name="migrate-registration-and-login-logic"></a>등록 및 로그인 논리 마이그레이션

앱에 대해 구성 된 Id 서비스 및 Entity Framework 및 SQL Server를 사용 하 여 구성 된 데이터 액세스를 사용 하 여 등록에 대 한 지원을 추가 하 고 앱에 로그인 할 준비가 되었습니다. [마이그레이션 프로세스의 이전 단계에서는](xref:migration/mvc#migrate-the-layout-file) *_Layout*의 *_LoginPartial* 에 대 한 참조를 주석으로 처리 했습니다. 이제 해당 코드로 돌아와서, 주석 처리를 제거 하 고, 필요한 컨트롤러 및 뷰를 추가 하 여 로그인 기능을 지원할 때입니다.

_Layout에서 `@Html.Partial` 줄의 주석 처리를 제거 *합니다.*

```cshtml
      <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
    </ul>
    @*@Html.Partial("_LoginPartial")*@
  </div>
</div>
```

이제 *Views/Shared* 폴더에 *_LoginPartial* 라는 새 Razor 뷰를 추가 합니다.

다음 코드를 사용 하 여 _LoginPartial를 업데이트 *합니다* (모든 내용 바꾸기).

```cshtml
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager

@if (SignInManager.IsSignedIn(User))
{
    <form asp-area="" asp-controller="Account" asp-action="Logout" method="post" id="logoutForm" class="navbar-right">
        <ul class="nav navbar-nav navbar-right">
            <li>
                <a asp-area="" asp-controller="Manage" asp-action="Index" title="Manage">Hello @UserManager.GetUserName(User)!</a>
            </li>
            <li>
                <button type="submit" class="btn btn-link navbar-btn navbar-link">Log out</button>
            </li>
        </ul>
    </form>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="" asp-controller="Account" asp-action="Register">Register</a></li>
        <li><a asp-area="" asp-controller="Account" asp-action="Login">Log in</a></li>
    </ul>
}
```

이 시점에서 브라우저에서 사이트를 새로 고칠 수 있습니다.

## <a name="summary"></a>요약

ASP.NET Core ASP.NET Identity 기능에 대 한 변경 사항을 소개 합니다. 이 문서에서는 ASP.NET Identity의 인증 및 사용자 관리 기능을 ASP.NET Core로 마이그레이션하는 방법에 대해 살펴보았습니다.
