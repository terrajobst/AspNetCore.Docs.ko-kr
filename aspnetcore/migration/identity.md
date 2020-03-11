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
# <a name="migrate-authentication-and-identity-to-aspnet-core"></a><span data-ttu-id="be3a4-103">ASP.NET Core 인증 및 Id 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="be3a4-103">Migrate Authentication and Identity to ASP.NET Core</span></span>

<span data-ttu-id="be3a4-104">작성자 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="be3a4-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="be3a4-105">이전 문서에서는 [ASP.NET mvc 프로젝트에서 ASP.NET CORE mvc로 구성을 마이그레이션](xref:migration/configuration)했습니다.</span><span class="sxs-lookup"><span data-stu-id="be3a4-105">In the previous article, we [migrated configuration from an ASP.NET MVC project to ASP.NET Core MVC](xref:migration/configuration).</span></span> <span data-ttu-id="be3a4-106">이 문서에서는 등록, 로그인 및 사용자 관리 기능을 마이그레이션합니다.</span><span class="sxs-lookup"><span data-stu-id="be3a4-106">In this article, we migrate the registration, login, and user management features.</span></span>

## <a name="configure-identity-and-membership"></a><span data-ttu-id="be3a4-107">Id 및 멤버 자격 구성</span><span class="sxs-lookup"><span data-stu-id="be3a4-107">Configure Identity and Membership</span></span>

<span data-ttu-id="be3a4-108">ASP.NET MVC에서 인증 및 id 기능은 *App_Start* 폴더에 있는 *Startup.Auth.cs* 및 *IdentityConfig.cs*의 ASP.NET Identity를 사용 하 여 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3a4-108">In ASP.NET MVC, authentication and identity features are configured using ASP.NET Identity in *Startup.Auth.cs* and *IdentityConfig.cs*, located in the *App_Start* folder.</span></span> <span data-ttu-id="be3a4-109">ASP.NET Core MVC에서 이러한 기능은 *Startup.cs*에서 구성 됩니다.</span><span class="sxs-lookup"><span data-stu-id="be3a4-109">In ASP.NET Core MVC, these features are configured in *Startup.cs*.</span></span>

<span data-ttu-id="be3a4-110">`Microsoft.AspNetCore.Identity.EntityFrameworkCore`를 설치 하 고 NuGet 패키지를 `Microsoft.AspNetCore.Authentication.Cookies` 합니다.</span><span class="sxs-lookup"><span data-stu-id="be3a4-110">Install the `Microsoft.AspNetCore.Identity.EntityFrameworkCore` and `Microsoft.AspNetCore.Authentication.Cookies` NuGet packages.</span></span>

<span data-ttu-id="be3a4-111">그런 다음 *Startup.cs* 를 열고 Entity Framework 및 id 서비스를 사용 하도록 `Startup.ConfigureServices` 메서드를 업데이트 합니다.</span><span class="sxs-lookup"><span data-stu-id="be3a4-111">Then, open *Startup.cs* and update the `Startup.ConfigureServices` method to use Entity Framework and Identity services:</span></span>

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

<span data-ttu-id="be3a4-112">이 시점에서 ASP.NET MVC 프로젝트에서 아직 마이그레이션하지 않은 두 가지 형식 (`ApplicationDbContext` 및 `ApplicationUser`)이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be3a4-112">At this point, there are two types referenced in the above code that we haven't yet migrated from the ASP.NET MVC project: `ApplicationDbContext` and `ApplicationUser`.</span></span> <span data-ttu-id="be3a4-113">ASP.NET Core 프로젝트에서 새 *모델* 폴더를 만들고 이러한 형식에 해당 하는 두 개의 클래스를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="be3a4-113">Create a new *Models* folder in the ASP.NET Core project, and add two classes to it corresponding to these types.</span></span> <span data-ttu-id="be3a4-114">*/Models/IdentityModels.cs*에서 이러한 클래스의 ASP.NET MVC 버전을 찾을 수 있지만 마이그레이션된 프로젝트에서 클래스 당 파일 하나를 사용 하는 것이 더 명확 하기 때문입니다.</span><span class="sxs-lookup"><span data-stu-id="be3a4-114">You will find the ASP.NET MVC versions of these classes in */Models/IdentityModels.cs*, but we will use one file per class in the migrated project since that's more clear.</span></span>

<span data-ttu-id="be3a4-115">*ApplicationUser.cs*:</span><span class="sxs-lookup"><span data-stu-id="be3a4-115">*ApplicationUser.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;

namespace NewMvcProject.Models
{
  public class ApplicationUser : IdentityUser
  {
  }
}
```

<span data-ttu-id="be3a4-116">*ApplicationDbContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="be3a4-116">*ApplicationDbContext.cs*:</span></span>

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

<span data-ttu-id="be3a4-117">ASP.NET Core MVC 스타터 웹 프로젝트에는 사용자의 많은 사용자 지정 또는 `ApplicationDbContext`포함 되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="be3a4-117">The ASP.NET Core MVC Starter Web project doesn't include much customization of users, or the `ApplicationDbContext`.</span></span> <span data-ttu-id="be3a4-118">실제 앱을 마이그레이션할 때 앱이 사용 하는 다른 모델 클래스 뿐만 아니라 앱의 사용자 및 `DbContext` 클래스의 사용자 지정 속성 및 메서드도 모두 마이그레이션해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be3a4-118">When migrating a real app, you also need to migrate all of the custom properties and methods of your app's user and `DbContext` classes, as well as any other Model classes your app utilizes.</span></span> <span data-ttu-id="be3a4-119">예를 들어 `DbContext`에 `DbSet<Album>`있는 경우 `Album` 클래스를 마이그레이션해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be3a4-119">For example, if your `DbContext` has a `DbSet<Album>`, you need to migrate the `Album` class.</span></span>

<span data-ttu-id="be3a4-120">이러한 파일이 준비 되 면 *Startup.cs* 파일을 `using` 문을 업데이트 하 여 컴파일할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be3a4-120">With these files in place, the *Startup.cs* file can be made to compile by updating its `using` statements:</span></span>

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Hosting;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
```

<span data-ttu-id="be3a4-121">이제 앱이 인증 및 Id 서비스를 지원할 준비가 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="be3a4-121">Our app is now ready to support authentication and Identity services.</span></span> <span data-ttu-id="be3a4-122">이러한 기능은 사용자에 게 노출 되어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="be3a4-122">It just needs to have these features exposed to users.</span></span>

## <a name="migrate-registration-and-login-logic"></a><span data-ttu-id="be3a4-123">등록 및 로그인 논리 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="be3a4-123">Migrate registration and login logic</span></span>

<span data-ttu-id="be3a4-124">앱에 대해 구성 된 Id 서비스 및 Entity Framework 및 SQL Server를 사용 하 여 구성 된 데이터 액세스를 사용 하 여 등록에 대 한 지원을 추가 하 고 앱에 로그인 할 준비가 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="be3a4-124">With Identity services configured for the app and data access configured using Entity Framework and SQL Server, we're ready to add support for registration and login to the app.</span></span> <span data-ttu-id="be3a4-125">[마이그레이션 프로세스의 이전 단계에서는](xref:migration/mvc#migrate-the-layout-file) *_Layout*의 *_LoginPartial* 에 대 한 참조를 주석으로 처리 했습니다.</span><span class="sxs-lookup"><span data-stu-id="be3a4-125">Recall that [earlier in the migration process](xref:migration/mvc#migrate-the-layout-file) we commented out a reference to *_LoginPartial* in *_Layout.cshtml*.</span></span> <span data-ttu-id="be3a4-126">이제 해당 코드로 돌아와서, 주석 처리를 제거 하 고, 필요한 컨트롤러 및 뷰를 추가 하 여 로그인 기능을 지원할 때입니다.</span><span class="sxs-lookup"><span data-stu-id="be3a4-126">Now it's time to return to that code, uncomment it, and add in the necessary controllers and views to support login functionality.</span></span>

<span data-ttu-id="be3a4-127">_Layout에서 `@Html.Partial` 줄의 주석 처리를 제거 *합니다.*</span><span class="sxs-lookup"><span data-stu-id="be3a4-127">Uncomment the `@Html.Partial` line in *_Layout.cshtml*:</span></span>

```cshtml
      <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
    </ul>
    @*@Html.Partial("_LoginPartial")*@
  </div>
</div>
```

<span data-ttu-id="be3a4-128">이제 *Views/Shared* 폴더에 *_LoginPartial* 라는 새 Razor 뷰를 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="be3a4-128">Now, add a new Razor view called *_LoginPartial* to the *Views/Shared* folder:</span></span>

<span data-ttu-id="be3a4-129">다음 코드를 사용 하 여 _LoginPartial를 업데이트 *합니다* (모든 내용 바꾸기).</span><span class="sxs-lookup"><span data-stu-id="be3a4-129">Update *_LoginPartial.cshtml* with the following code (replace all of its contents):</span></span>

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

<span data-ttu-id="be3a4-130">이 시점에서 브라우저에서 사이트를 새로 고칠 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="be3a4-130">At this point, you should be able to refresh the site in your browser.</span></span>

## <a name="summary"></a><span data-ttu-id="be3a4-131">요약</span><span class="sxs-lookup"><span data-stu-id="be3a4-131">Summary</span></span>

<span data-ttu-id="be3a4-132">ASP.NET Core ASP.NET Identity 기능에 대 한 변경 사항을 소개 합니다.</span><span class="sxs-lookup"><span data-stu-id="be3a4-132">ASP.NET Core introduces changes to the ASP.NET Identity features.</span></span> <span data-ttu-id="be3a4-133">이 문서에서는 ASP.NET Identity의 인증 및 사용자 관리 기능을 ASP.NET Core로 마이그레이션하는 방법에 대해 살펴보았습니다.</span><span class="sxs-lookup"><span data-stu-id="be3a4-133">In this article, you have seen how to migrate the authentication and user management features of ASP.NET Identity to ASP.NET Core.</span></span>
