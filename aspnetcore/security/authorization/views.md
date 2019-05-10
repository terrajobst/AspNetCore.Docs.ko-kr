---
title: ASP.NET Core MVC에서 뷰 기반 권한 부여
author: rick-anderson
description: 이 문서에 삽입 된 ASP.NET Core Razor 뷰 내에서 권한 부여 서비스를 활용 하는 방법을 보여 줍니다.
ms.author: riande
ms.date: 10/30/2017
uid: security/authorization/views
ms.openlocfilehash: e497c41d4dca29fed8733f18cf727804e3f06d8c
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892060"
---
# <a name="view-based-authorization-in-aspnet-core-mvc"></a>ASP.NET Core MVC에서 뷰 기반 권한 부여

하려는 개발자는 종종 표시, 숨기기 또는 그렇지 않은 경우 현재 사용자 id를 기반으로 UI를 수정 합니다. 권한 부여 서비스를 통해 MVC 보기 내에서 액세스할 수 있습니다 [종속성 주입](xref:fundamentals/dependency-injection)합니다. 사용 권한 부여 서비스를 Razor 보기에 삽입할는 `@inject` 지시문:

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService
```

모든 보기에서 권한 부여 서비스를 사용 하도록 하려는 경우 배치를 `@inject` 지시문에 *_ViewImports.cshtml* 의 파일을 *뷰* 디렉터리입니다. 자세한 내용은 [보기에 종속성 주입](xref:mvc/views/dependency-injection)을 참조하세요.

삽입 된 권한 부여 서비스를 사용 하 여 호출할 `AuthorizeAsync` 정확히 같은 방식 중 선택 [리소스 기반 권한 부여](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, "PolicyName")).Succeeded)
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```cshtml
@if (await AuthorizationService.AuthorizeAsync(User, "PolicyName"))
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

---

경우에 따라 리소스 보기 모델 됩니다. 호출할 `AuthorizeAsync` 정확히 같은 방식 중 선택 [리소스 기반 권한 부여](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit)).Succeeded)
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

```cshtml
@if (await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit))
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

---

위의 코드에서 모델을 고려해 야 정책 평가가 수행 해야 하는 리소스로 전달 됩니다.

> [!WARNING]
> 앱의 UI 요소의 토글 표시 유형 유일한 권한 부여 확인으로 의존 하지 마십시오. UI 요소를 숨기면 방해 하지 않을 수 완전히 액세스 해당 연결 된 컨트롤러 작업입니다. 예를 들어 앞의 코드 조각에 있는 단추를 것이 좋습니다. 사용자가 호출할 수는 `Edit` 상대 리소스를 자신이 알고 있는 경우 동작 메서드에 URL이 */Document/Edit/1*합니다. 이러한 이유로 `Edit` 작업 메서드 자체 권한 부여 확인을 수행 해야 합니다.
