---
title: ASP.NET Core에서 Razor Pages 권한 부여 규칙
author: rick-anderson
description: 사용자에 게 권한을 부여 하 고 익명 사용자가 페이지 또는 페이지의 폴더에 액세스 하도록 허용 하는 규칙으로 페이지에 대 한 액세스를 제어 하는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2019
uid: security/authorization/razor-pages-authorization
ms.openlocfilehash: 00fc487c6ac802f213bcf83994ecc2b1a1468589
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653115"
---
# <a name="razor-pages-authorization-conventions-in-aspnet-core"></a>ASP.NET Core에서 Razor Pages 권한 부여 규칙

::: moniker range=">= aspnetcore-3.0"

Razor Pages 앱에서 액세스를 제어 하는 한 가지 방법은 시작할 때 권한 부여 규칙을 사용 하는 것입니다. 이러한 규칙을 통해 사용자에 게 권한을 부여 하 고 익명 사용자가 페이지의 개별 페이지 또는 폴더에 액세스할 수 있습니다. 이 항목에서 설명 하는 규칙은 액세스를 제어 하는 [권한 부여 필터](xref:mvc/controllers/filters#authorization-filters) 를 자동으로 적용 합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

샘플 앱은 [ASP.NET Core id 없이 쿠키 인증](xref:security/authentication/cookie)을 사용 합니다. 이 항목에 나와 있는 개념 및 예제는 ASP.NET Core Id를 사용 하는 앱에 동일 하 게 적용 됩니다. ASP.NET Core Id를 사용 하려면 <xref:security/authentication/identity>의 지침을 따르세요.

## <a name="require-authorization-to-access-a-page"></a>페이지에 액세스 하기 위한 권한 부여 필요

<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>를 통해 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> 규칙을 사용 하 여 지정 된 경로에 있는 페이지에 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>를 추가 합니다.

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

지정 된 경로는 확장명이 없고 슬래시만 포함 하는 Razor Pages 루트 상대 경로인 뷰 엔진 경로입니다.

[권한 부여 정책을](xref:security/authorization/policies)지정 하려면 [AuthorizePage 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*)를 사용 합니다.

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> `[Authorize]` 필터 특성을 사용 하 여 페이지 모델 클래스에 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>을 적용할 수 있습니다. 자세한 내용은 [권한 부여 필터 특성](xref:razor-pages/filter#authorize-filter-attribute)을 참조 하세요.

## <a name="require-authorization-to-access-a-folder-of-pages"></a>페이지의 폴더에 액세스 하기 위한 권한 부여 필요

<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>를 통해 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> 규칙을 사용 하 여 지정 된 경로에 있는 폴더의 모든 페이지에 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>를 추가 합니다.

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

지정 된 경로는 Razor Pages 루트 상대 경로인 뷰 엔진 경로입니다.

[권한 부여 정책을](xref:security/authorization/policies)지정 하려면 [AuthorizeFolder 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*)를 사용 합니다.

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a>영역 페이지에 액세스 하기 위한 권한 부여 필요

<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>를 통해 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> 규칙을 사용 하 여 지정 된 경로의 영역 페이지에 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>를 추가 합니다.

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

페이지 이름은 지정 된 영역에 대 한 페이지 루트 디렉터리에 상대적인 확장명이 없는 파일의 경로입니다. 예를 들어 파일 *영역/i d/페이지/관리/계정. cshtml* 의 페이지 이름은 */Manage/Accounts*입니다.

[권한 부여 정책을](xref:security/authorization/policies)지정 하려면 [AuthorizeAreaPage 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*)를 사용 합니다.

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a>영역 폴더에 액세스 하기 위한 권한 부여 필요

<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>를 통해 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> 규칙을 사용 하 여 지정 된 경로에 있는 폴더의 모든 영역에 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>를 추가 합니다.

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

폴더 경로는 지정 된 영역에 대 한 페이지 루트 디렉터리를 기준으로 하는 폴더의 경로입니다. 예를 들어 *영역/i d/페이지/관리/관리/* 관리에서 파일의 폴더 경로는/ *관리*입니다.

[권한 부여 정책을](xref:security/authorization/policies)지정 하려면 [AuthorizeAreaFolder 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*)를 사용 합니다.

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a>페이지에 대 한 익명 액세스 허용

<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>를 통해 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> 규칙을 사용 하 여 지정 된 경로에 있는 페이지에 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>를 추가 합니다.

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

지정 된 경로는 확장명이 없고 슬래시만 포함 하는 Razor Pages 루트 상대 경로인 뷰 엔진 경로입니다.

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a>페이지의 폴더에 대 한 익명 액세스 허용

<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>를 통해 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> 규칙을 사용 하 여 지정 된 경로에 있는 폴더의 모든 페이지에 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>를 추가 합니다.

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

지정 된 경로는 Razor Pages 루트 상대 경로인 뷰 엔진 경로입니다.

## <a name="note-on-combining-authorized-and-anonymous-access"></a>권한 있는 액세스 및 익명 액세스 결합에 대 한 참고 사항

페이지의 폴더에 권한 부여가 필요 하다 고 지정 하 고 해당 폴더의 페이지에서 익명 액세스를 허용 하도록 지정 하는 것은 유효 합니다.

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

그러나 반대의 경우는 유효 하지 않습니다. 익명 액세스를 위해 페이지의 폴더를 선언한 다음 권한 부여가 필요한 해당 폴더 내에 페이지를 지정할 수 없습니다.

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

개인 페이지에 대 한 권한 부여가 실패 합니다. <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>와 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 페이지에 모두 적용 되는 경우 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>가 우선적으로 적용 되 고 액세스를 제어 합니다.

## <a name="additional-resources"></a>추가 리소스

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Razor Pages 앱에서 액세스를 제어 하는 한 가지 방법은 시작할 때 권한 부여 규칙을 사용 하는 것입니다. 이러한 규칙을 통해 사용자에 게 권한을 부여 하 고 익명 사용자가 페이지의 개별 페이지 또는 폴더에 액세스할 수 있습니다. 이 항목에서 설명 하는 규칙은 액세스를 제어 하는 [권한 부여 필터](xref:mvc/controllers/filters#authorization-filters) 를 자동으로 적용 합니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

샘플 앱은 [ASP.NET Core id 없이 쿠키 인증](xref:security/authentication/cookie)을 사용 합니다. 이 항목에 나와 있는 개념 및 예제는 ASP.NET Core Id를 사용 하는 앱에 동일 하 게 적용 됩니다. ASP.NET Core Id를 사용 하려면 <xref:security/authentication/identity>의 지침을 따르세요.

## <a name="require-authorization-to-access-a-page"></a>페이지에 액세스 하기 위한 권한 부여 필요

<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>를 통해 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> 규칙을 사용 하 여 지정 된 경로에 있는 페이지에 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>를 추가 합니다.

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

지정 된 경로는 확장명이 없고 슬래시만 포함 하는 Razor Pages 루트 상대 경로인 뷰 엔진 경로입니다.

[권한 부여 정책을](xref:security/authorization/policies)지정 하려면 [AuthorizePage 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*)를 사용 합니다.

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> `[Authorize]` 필터 특성을 사용 하 여 페이지 모델 클래스에 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>을 적용할 수 있습니다. 자세한 내용은 [권한 부여 필터 특성](xref:razor-pages/filter#authorize-filter-attribute)을 참조 하세요.

## <a name="require-authorization-to-access-a-folder-of-pages"></a>페이지의 폴더에 액세스 하기 위한 권한 부여 필요

<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>를 통해 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> 규칙을 사용 하 여 지정 된 경로에 있는 폴더의 모든 페이지에 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>를 추가 합니다.

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

지정 된 경로는 Razor Pages 루트 상대 경로인 뷰 엔진 경로입니다.

[권한 부여 정책을](xref:security/authorization/policies)지정 하려면 [AuthorizeFolder 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*)를 사용 합니다.

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a>영역 페이지에 액세스 하기 위한 권한 부여 필요

<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>를 통해 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> 규칙을 사용 하 여 지정 된 경로의 영역 페이지에 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>를 추가 합니다.

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

페이지 이름은 지정 된 영역에 대 한 페이지 루트 디렉터리에 상대적인 확장명이 없는 파일의 경로입니다. 예를 들어 파일 *영역/i d/페이지/관리/계정. cshtml* 의 페이지 이름은 */Manage/Accounts*입니다.

[권한 부여 정책을](xref:security/authorization/policies)지정 하려면 [AuthorizeAreaPage 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*)를 사용 합니다.

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a>영역 폴더에 액세스 하기 위한 권한 부여 필요

<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>를 통해 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> 규칙을 사용 하 여 지정 된 경로에 있는 폴더의 모든 영역에 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter>를 추가 합니다.

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

폴더 경로는 지정 된 영역에 대 한 페이지 루트 디렉터리를 기준으로 하는 폴더의 경로입니다. 예를 들어 *영역/i d/페이지/관리/관리/* 관리에서 파일의 폴더 경로는/ *관리*입니다.

[권한 부여 정책을](xref:security/authorization/policies)지정 하려면 [AuthorizeAreaFolder 오버 로드](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*)를 사용 합니다.

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a>페이지에 대 한 익명 액세스 허용

<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>를 통해 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> 규칙을 사용 하 여 지정 된 경로에 있는 페이지에 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>를 추가 합니다.

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

지정 된 경로는 확장명이 없고 슬래시만 포함 하는 Razor Pages 루트 상대 경로인 뷰 엔진 경로입니다.

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a>페이지의 폴더에 대 한 익명 액세스 허용

<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>를 통해 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> 규칙을 사용 하 여 지정 된 경로에 있는 폴더의 모든 페이지에 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>를 추가 합니다.

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

지정 된 경로는 Razor Pages 루트 상대 경로인 뷰 엔진 경로입니다.

## <a name="note-on-combining-authorized-and-anonymous-access"></a>권한 있는 액세스 및 익명 액세스 결합에 대 한 참고 사항

권한 부여가 필요한 페이지의 폴더를 지정 하 고 해당 폴더의 페이지에서 익명 액세스를 허용 하도록 지정 하는 것은 유효 합니다.

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

그러나 반대의 경우는 유효 하지 않습니다. 익명 액세스를 위해 페이지의 폴더를 선언한 다음 권한 부여가 필요한 해당 폴더 내에 페이지를 지정할 수 없습니다.

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

개인 페이지에 대 한 권한 부여가 실패 합니다. <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>와 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 페이지에 모두 적용 되는 경우 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter>가 우선적으로 적용 되 고 액세스를 제어 합니다.

## <a name="additional-resources"></a>추가 리소스

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end
