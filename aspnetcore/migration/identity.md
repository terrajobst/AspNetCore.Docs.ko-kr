---
title: ASP.NET core 인증 및 Id 마이그레이션
author: ardalis
description: ASP.NET Core MVC 프로젝트에 ASP.NET MVC 프로젝트에서 인증 및 id를 마이그레이션하는 방법에 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
uid: migration/identity
ms.openlocfilehash: 72e62e78e37325ec47d54abbc11a875ae87fb63a
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64891880"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core"></a><span data-ttu-id="23689-103">ASP.NET core 인증 및 Id 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="23689-103">Migrate Authentication and Identity to ASP.NET Core</span></span>

<span data-ttu-id="23689-104">작성자: [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="23689-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="23689-105">이전 문서에서는 우리가 [ASP.NET Core MVC에서 ASP.NET MVC 프로젝트 구성 마이그레이션](xref:migration/configuration)합니다.</span><span class="sxs-lookup"><span data-stu-id="23689-105">In the previous article, we [migrated configuration from an ASP.NET MVC project to ASP.NET Core MVC](xref:migration/configuration).</span></span> <span data-ttu-id="23689-106">이 문서에서는 등록, 로그인 및 사용자 관리 기능을 마이그레이션할 것입니다.</span><span class="sxs-lookup"><span data-stu-id="23689-106">In this article, we migrate the registration, login, and user management features.</span></span>

## <a name="configure-identity-and-membership"></a><span data-ttu-id="23689-107">Id 및 멤버 자격 구성</span><span class="sxs-lookup"><span data-stu-id="23689-107">Configure Identity and Membership</span></span>

<span data-ttu-id="23689-108">ASP.NET MVC에서 인증 및 id 기능에 ASP.NET Id를 사용 하 여 구성 됩니다 *Startup.Auth.cs* 하 고 *IdentityConfig.cs*에 있는 합니다 *App_Start* 폴더입니다.</span><span class="sxs-lookup"><span data-stu-id="23689-108">In ASP.NET MVC, authentication and identity features are configured using ASP.NET Identity in *Startup.Auth.cs* and *IdentityConfig.cs*, located in the *App_Start* folder.</span></span> <span data-ttu-id="23689-109">ASP.NET Core mvc에서 이러한 기능에 구성 된 *Startup.cs*합니다.</span><span class="sxs-lookup"><span data-stu-id="23689-109">In ASP.NET Core MVC, these features are configured in *Startup.cs*.</span></span>

<span data-ttu-id="23689-110">설치 합니다 `Microsoft.AspNetCore.Identity.EntityFrameworkCore` 고 `Microsoft.AspNetCore.Authentication.Cookies` NuGet 패키지.</span><span class="sxs-lookup"><span data-stu-id="23689-110">Install the `Microsoft.AspNetCore.Identity.EntityFrameworkCore` and `Microsoft.AspNetCore.Authentication.Cookies` NuGet packages.</span></span>

<span data-ttu-id="23689-111">그런 다음 엽니다 *Startup.cs* 업데이트 및는 `Startup.ConfigureServices` Entity Framework 및 Id 서비스를 사용 하는 방법.</span><span class="sxs-lookup"><span data-stu-id="23689-111">Then, open *Startup.cs* and update the `Startup.ConfigureServices` method to use Entity Framework and Identity services:</span></span>

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

<span data-ttu-id="23689-112">이 시점에서 두 가지 유형이 있습니다 ASP.NET MVC 프로젝트에서 아직 마이그레이션하지 않은 우리는 위의 코드에서 참조: `ApplicationDbContext` 고 `ApplicationUser`입니다.</span><span class="sxs-lookup"><span data-stu-id="23689-112">At this point, there are two types referenced in the above code that we haven't yet migrated from the ASP.NET MVC project: `ApplicationDbContext` and `ApplicationUser`.</span></span> <span data-ttu-id="23689-113">새 *모델* 폴더에서 ASP.NET Core 프로젝트를 마우스 두 클래스를 이러한 형식에 해당 하 고 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="23689-113">Create a new *Models* folder in the ASP.NET Core project, and add two classes to it corresponding to these types.</span></span> <span data-ttu-id="23689-114">ASP.NET MVC 버전의 이러한 클래스를 찾을 수 있습니다 */Models/IdentityModels.cs*, 하지만 더 명확한 이므로로 마이그레이션된 프로젝트에서 클래스 파일을 하나씩 사용 됩니다.</span><span class="sxs-lookup"><span data-stu-id="23689-114">You will find the ASP.NET MVC versions of these classes in */Models/IdentityModels.cs*, but we will use one file per class in the migrated project since that's more clear.</span></span>

<span data-ttu-id="23689-115">*ApplicationUser.cs*:</span><span class="sxs-lookup"><span data-stu-id="23689-115">*ApplicationUser.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;

namespace NewMvcProject.Models
{
  public class ApplicationUser : IdentityUser
  {
  }
}
```

<span data-ttu-id="23689-116">*ApplicationDbContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="23689-116">*ApplicationDbContext.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Identity.EntityFramework;
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

<span data-ttu-id="23689-117">ASP.NET Core MVC 시작 웹 프로젝트의 사용자가 많은 사용자 지정을 포함 하지 않습니다 또는 `ApplicationDbContext`합니다.</span><span class="sxs-lookup"><span data-stu-id="23689-117">The ASP.NET Core MVC Starter Web project doesn't include much customization of users, or the `ApplicationDbContext`.</span></span> <span data-ttu-id="23689-118">또한 모든 사용자 지정 속성 및 앱의 사용자의 메서드를 마이그레이션해야 할 실제 앱을 마이그레이션할 때는 및 `DbContext` 클래스 뿐만 아니라 앱 활용 모델 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="23689-118">When migrating a real app, you also need to migrate all of the custom properties and methods of your app's user and `DbContext` classes, as well as any other Model classes your app utilizes.</span></span> <span data-ttu-id="23689-119">예를 들어, 경우에 `DbContext` 에 `DbSet<Album>`, 마이그레이션해야 하는 `Album` 클래스입니다.</span><span class="sxs-lookup"><span data-stu-id="23689-119">For example, if your `DbContext` has a `DbSet<Album>`, you need to migrate the `Album` class.</span></span>

<span data-ttu-id="23689-120">현재 위치에서 이러한 파일은 *Startup.cs* 파일을 업데이트 하 여 컴파일할 수 있습니다 해당 `using` 문:</span><span class="sxs-lookup"><span data-stu-id="23689-120">With these files in place, the *Startup.cs* file can be made to compile by updating its `using` statements:</span></span>

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Hosting;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
```

<span data-ttu-id="23689-121">앱 인증 및 Id 서비스를 지원 하도록 준비 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="23689-121">Our app is now ready to support authentication and Identity services.</span></span> <span data-ttu-id="23689-122">바로 이러한 기능이 사용자에 게 노출 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="23689-122">It just needs to have these features exposed to users.</span></span>

## <a name="migrate-registration-and-login-logic"></a><span data-ttu-id="23689-123">등록 및 로그인 논리를 마이그레이션</span><span class="sxs-lookup"><span data-stu-id="23689-123">Migrate registration and login logic</span></span>

<span data-ttu-id="23689-124">앱에 대해 구성 하는 Id 서비스와 Entity Framework 및 SQL Server를 사용 하 여 구성 하는 데이터 액세스 준비가 앱에 등록 및 로그인에 대 한 지원을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="23689-124">With Identity services configured for the app and data access configured using Entity Framework and SQL Server, we're ready to add support for registration and login to the app.</span></span> <span data-ttu-id="23689-125">이전에 설명한 대로 [마이그레이션 프로세스의 앞부분에 나오는](xref:migration/mvc#migrate-the-layout-file) 에 대 한 참조를 주석으로 처리 했습니다 *_LoginPartial* 에서 *_Layout.cshtml*합니다.</span><span class="sxs-lookup"><span data-stu-id="23689-125">Recall that [earlier in the migration process](xref:migration/mvc#migrate-the-layout-file) we commented out a reference to *_LoginPartial* in *_Layout.cshtml*.</span></span> <span data-ttu-id="23689-126">이제 해당 코드를 반환 합니다. 주석 처리를 제거 하지만 로그인 기능을 지원 하기 위해 뷰 필요한 컨트롤러를 추가 하는 시간입니다.</span><span class="sxs-lookup"><span data-stu-id="23689-126">Now it's time to return to that code, uncomment it, and add in the necessary controllers and views to support login functionality.</span></span>

<span data-ttu-id="23689-127">주석 처리를 제거 합니다 `@Html.Partial` 줄 *_Layout.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="23689-127">Uncomment the `@Html.Partial` line in *_Layout.cshtml*:</span></span>

```cshtml
      <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
    </ul>
    @*@Html.Partial("_LoginPartial")*@
  </div>
</div>
```

<span data-ttu-id="23689-128">이제 라는 새 Razor 뷰를 추가 *_LoginPartial* 에 *Views/Shared* 폴더:</span><span class="sxs-lookup"><span data-stu-id="23689-128">Now, add a new Razor view called *_LoginPartial* to the *Views/Shared* folder:</span></span>

<span data-ttu-id="23689-129">업데이트 *_LoginPartial.cshtml* 다음 코드를 사용 하 여 (모든 내용을 바꾸기):</span><span class="sxs-lookup"><span data-stu-id="23689-129">Update *_LoginPartial.cshtml* with the following code (replace all of its contents):</span></span>

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

<span data-ttu-id="23689-130">이때 브라우저에서 사이트를 새로 고칠 수 해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="23689-130">At this point, you should be able to refresh the site in your browser.</span></span>

## <a name="summary"></a><span data-ttu-id="23689-131">요약</span><span class="sxs-lookup"><span data-stu-id="23689-131">Summary</span></span>

<span data-ttu-id="23689-132">ASP.NET Core에서는 ASP.NET Id 기능을 변경 합니다.</span><span class="sxs-lookup"><span data-stu-id="23689-132">ASP.NET Core introduces changes to the ASP.NET Identity features.</span></span> <span data-ttu-id="23689-133">이 문서에서는 ASP.NET Core에 ASP.NET Id의 인증 및 사용자 관리 기능을 마이그레이션하는 방법에 살펴보았습니다.</span><span class="sxs-lookup"><span data-stu-id="23689-133">In this article, you have seen how to migrate the authentication and user management features of ASP.NET Identity to ASP.NET Core.</span></span>
