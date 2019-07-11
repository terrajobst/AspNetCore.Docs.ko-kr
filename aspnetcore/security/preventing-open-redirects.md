---
title: ASP.NET Core에서 오픈 리디렉션 공격 방지
author: ardalis
description: ASP.NET Core 앱에 대 한 오픈 리디렉션 공격을 방지 하는 방법을 보여 줍니다.
ms.author: riande
ms.date: 07/07/2017
uid: security/preventing-open-redirects
ms.openlocfilehash: 9d8cac8708fe9aeadba5af1287362a20df7f6bfe
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815494"
---
# <a name="prevent-open-redirect-attacks-in-aspnet-core"></a>ASP.NET Core에서 오픈 리디렉션 공격 방지

잠재적으로 쿼리 문자열 또는 폼 데이터와 같은 요청을 통해 지정 된 URL로 리디렉션하는 웹 앱을 외부, 악성 URL에 사용자를 리디렉션할 사용 하 여 변경할 수 있습니다. 이 변조 오픈 리디렉션 공격을 이라고 합니다.

때마다 응용 프로그램 논리를 지정 된 URL로 리디렉션되면 리디렉션 URL을 사용 하 여 손상 되지 않았는지를 확인 해야 합니다. ASP.NET Core 앱 (라고도 오픈 리디렉션) 오픈 리디렉션 공격 으로부터 보호 하기 위해 기본 제공 기능이 있습니다.

## <a name="what-is-an-open-redirect-attack"></a>오픈 리디렉션 공격 란?

웹 응용 프로그램 자주 사용자를 리디렉션할 로그인 페이지를 인증 해야 하는 리소스에 액세스할 때. 리디렉션 일반적으로 포함 됩니다는 `returnUrl` querystring 매개 변수를 성공적으로 로그인 한 후 원래 요청 된 URL에 반환 될 수 있도록 합니다. 사용자 인증 후가 원래 요청 된 URL로 리디렉션됩니다.

대상 URL을 요청의 쿼리 문자열에 지정 되어 있으므로 악의적인 사용자는 querystring 변조할 수 있습니다. 변조 된 querystring 사이트 외부의 악의적인 사이트에 사용자를 리디렉션할 수 있습니다. 이 기술은 오픈 리디렉션 (또는 리디렉션) 공격을 이라고 합니다.

### <a name="an-example-attack"></a>예제에서는 공격

악의적인 사용자는 사용자의 자격 증명이 나 중요 한 정보를 악의적인 사용자가 액세스를 허용 하는 데 공격을 개발할 수 있습니다. 악의적인 사용자의 공격을 시작 하려면 사용자가 사용 하 여 사이트의 로그인 페이지에 대 한 링크를 클릭을 유도 한 `returnUrl` querystring 값을 URL에 추가 합니다. 예를 들어 앱에서 고려해 야 `contoso.com` 에서 로그인 페이지를 포함 하는 `http://contoso.com/Account/LogOn?returnUrl=/Home/About`합니다. 공격은 이러한 단계를 따릅니다.

1. 사용자는 악성 링크를 클릭 `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (두 번째 URL은 "contoso**1**.com"에 없습니다 "contoso.com").
2. 사용자가 성공적으로 로그인 합니다.
3. 사용자가 리디렉션됩니다 (사이트)에서 `http://contoso1.com/Account/LogOn` (실제 사이트 처럼 정확 하 게 보이는 악의적인 사이트).
4. 사용자가 다시 로그인 (자격 증명을 사이트 악의적인 제공) 및 실제 사이트로 다시 리디렉션됩니다.

가능성이 사용자 로그인에 해당 첫 번째 시도가 실패 했음을 두 번째 시도가 성공적으로 되었는지 생각 합니다. 사용자는 대부분 해당 자격 증명이 손상 된를 인식 하지 못하는 남아 있습니다.

![오픈 리디렉션 공격 프로세스](preventing-open-redirects/_static/open-redirection-attack-process.png)

로그인 페이지 외에도 일부 사이트 리디렉션 페이지 또는 끝점을 제공합니다. 앱에는 오픈 리디렉션으로 페이지가 imagine `/Home/Redirect`합니다. 침입자가 만들, 예를 들어으로 이동 하는 전자 메일의 링크 `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`합니다. 일반적인 사용자는 URL을 살펴보고 사이트 이름으로 시작 합니다. 신뢰, 링크를 클릭 합니다. 오픈 리디렉션은 다음 사용자를 즐기세요 동일한 보이는, 피싱 사이트로 보내고 사용자 했을 것 로그인 있다고 생각 하는 사이트입니다.

## <a name="protecting-against-open-redirect-attacks"></a>오픈 리디렉션 공격 으로부터 보호

웹 응용 프로그램을 개발할 때으로 신뢰할 수 없는 모든 사용자가 제공한 데이터를 처리 합니다. 응용 프로그램 URL의 내용을 기반으로 사용자를 리디렉션하는 기능에는 이러한 리디렉션만 이루어집니다 로컬로 앱 내에서 (또는 알려진된 URL 쿼리 문자열에서 지정할 수는 없습니다 URL)를 확인 합니다.

### <a name="localredirect"></a>LocalRedirect

사용 된 `LocalRedirect` 도우미 메서드에서 기본 `Controller` 클래스:

```csharp
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

`LocalRedirect` 로컬이 아닌 URL을 지정 하는 경우 예외가 throw 됩니다. 그렇지 않은 경우 처럼 작동 합니다 `Redirect` 메서드.

### <a name="islocalurl"></a>IsLocalUrl

사용 된 [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) 리디렉션하기 전에 Url을 테스트 하는 방법.

다음 예제에서는 URL을 리디렉션하기 전에 로컬 인지 여부를 확인 하는 방법을 보여 줍니다.

```csharp
private IActionResult RedirectToLocal(string returnUrl)
{
    if (Url.IsLocalUrl(returnUrl))
    {
        return Redirect(returnUrl);
    }
    else
    {
        return RedirectToAction(nameof(HomeController.Index), "Home");
    }
}
```

`IsLocalUrl` 메서드 악의적인 사이트에 리디렉션되지 실수로 사용자를 보호 합니다. 로컬이 아닌 URL은 로컬 URL을 필요한 상황에서 제공 되는 경우 제공 된 URL의 세부 정보를 기록할 수 있습니다. 로깅 리디렉션 Url 리디렉션 공격 진단에 도움이 될 수 있습니다.
