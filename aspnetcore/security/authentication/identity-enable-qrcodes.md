---
title: ASP.NET Core에서 TOTP authenticator 앱에 대 한 QR 코드 생성 사용
author: rick-anderson
description: ASP.NET Core 2 단계 인증을 사용 하는 TOTP authenticator 앱에 대 한 QR 코드 생성을 활성화 하는 방법을 알아봅니다.
ms.author: riande
ms.date: 08/14/2018
uid: security/authentication/identity-enable-qrcodes
ms.openlocfilehash: a7fdc86b3fe94e714e5147c89a32fce13757d1c1
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896730"
---
# <a name="enable-qr-code-generation-for-totp-authenticator-apps-in-aspnet-core"></a>ASP.NET Core에서 TOTP authenticator 앱에 대 한 QR 코드 생성 사용

::: moniker range="<= aspnetcore-2.0"

QR 코드는 ASP.NET Core 2.0 이상이 필요합니다.

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

ASP.NET Core 개별 인증을 위한 authenticator 응용 프로그램에 대 한 지원을 제공합니다. 두 단계 인증 (2FA) authenticator 앱의 경우는 시간 기반 일회용 암호 알고리즘 (TOTP)를 사용 하 여 권장 접근법 2FA 위한 업계 됩니다. 2FA TOTP를 사용 하 여는 SMS 2FA 하는 것이 좋습니다. Authenticator 앱에는 사용자가 자신의 사용자 이름과 암호를 확인 한 후 입력 해야 하는 6 to 8 자리 코드를 제공 합니다. 일반적으로 authenticator 앱은 스마트 폰에 설치 됩니다.

ASP.NET Core 웹 앱 템플릿을 인증자를 지원 하지만 QRCode 생성에 대 한 지원을 제공 하지 않습니다. QRCode 생성기 2FA 설치 하는 간소화할 수 있습니다. 이 문서는 과정을 안내 추가 [QR 코드](https://wikipedia.org/wiki/QR_code) 2FA 구성 페이지를 생성 합니다.

2 단계 인증에 같은 외부 인증 공급자를 사용 하 여 발생 하지 않습니다 [Google](xref:security/authentication/google-logins) 하거나 [Facebook](xref:security/authentication/facebook-logins)합니다. 외부 로그인 보호는 어떤 메커니즘을 통해 외부 로그인 공급자를 제공 합니다. 예를 들어, 합니다 [Microsoft](xref:security/authentication/microsoft-logins) 하드웨어 키 또는 2FA는 다른 방법은 인증 공급자가 필요 합니다. 기본 템플릿 "local" 2FA를 적용 하는 경우 사용자는 두 가지 경우에 자주 사용된 되지 않는 2FA 방법을 충족 하기 위해 필요는 합니다.

## <a name="adding-qr-codes-to-the-2fa-configuration-page"></a>2FA 구성 페이지에 QR 코드를 추가합니다.

이러한 지침을 사용 *qrcode.js* 에서 https://davidshimjs.github.io/qrcodejs/ 리포지토리.

* 다운로드 합니다 [qrcode.js javascript 라이브러리](https://davidshimjs.github.io/qrcodejs/) 에 `wwwroot\lib` 프로젝트의 폴더입니다.

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* 지침을 따릅니다 [스 캐 폴드 Identity](xref:security/authentication/scaffold-identity) 생성할 */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*합니다.
* */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*를 찾습니다는 `Scripts` 파일의 끝에 있는 섹션:

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* *Pages/Account/Manage/EnableAuthenticator.cshtml* (Razor 페이지) 또는 *Views/Manage/EnableAuthenticator.cshtml* (MVC)을 찾습니다는 `Scripts` 파일의 끝에 있는 섹션:

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")
}
```

* 업데이트를 `Scripts` 섹션에 대 한 참조를 추가 하는 `qrcodejs` 추가한 라이브러리 및 QR 코드 생성에 대 한 호출 합니다. 다음과 같이 표시 됩니다.

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")

    <script type="text/javascript" src="~/lib/qrcode.js"></script>
    <script type="text/javascript">
        new QRCode(document.getElementById("qrCode"),
            {
                text: "@Html.Raw(Model.AuthenticatorUri)",
                width: 150,
                height: 150
            });
    </script>
}
```

* 이러한 지침에 대 한 링크를 제공 하는 단락을 삭제 합니다.

앱을 실행 하 고 QR 코드를 스캔 하 고 인증자 입증 하는 코드의 유효성을 검사 수 있는지를 확인 합니다.

## <a name="change-the-site-name-in-the-qr-code"></a>QR 코드에서 사이트 이름 변경

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

처음 프로젝트를 만들 때 선택한 프로젝트 이름에서 QR 코드에서 사이트 이름을 가져옵니다. 검색 하 여 변경할 수 있습니다 합니다 `GenerateQrCodeUri(string email, string unformattedKey)` 의 메서드를 */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml.cs*합니다.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

처음 프로젝트를 만들 때 선택한 프로젝트 이름에서 QR 코드에서 사이트 이름을 가져옵니다. 검색 하 여 변경할 수 있습니다 합니다 `GenerateQrCodeUri(string email, string unformattedKey)` 의 메서드를 *Pages/Account/Manage/EnableAuthenticator.cshtml.cs* (Razor 페이지) 파일 또는 *Controllers/ManageController.cs* (MVC) 파일입니다.

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

템플릿에서 기본 코드를 아래와 같습니다.

```csharp
private string GenerateQrCodeUri(string email, string unformattedKey)
{
    return string.Format(
        AuthenticatorUriFormat,
        _urlEncoder.Encode("Razor Pages"),
        _urlEncoder.Encode(email),
        unformattedKey);
}
```

에 대 한 호출에서 두 번째 매개 변수 `string.Format` 은 사이트 이름에서 솔루션 이름을 가져옵니다. 모든 값을 변경할 수 있습니다 하지만 항상 URL 인코딩 이어야 합니다.

## <a name="using-a-different-qr-code-library"></a>다른 QR 코드 라이브러리를 사용 하 여

기본 라이브러리를 사용 하 여 QR 코드 라이브러리를 바꿀 수 있습니다. HTML에는 `qrCode` 라이브러리 제공 하는 요소는 어떤 메커니즘에서 QR 코드를 배치할 수 있습니다.

QR 코드에 대 한 올바른 형식의 URL은에서 사용할 수 있는 합니다.

* `AuthenticatorUri` 모델의 속성입니다.
* `data-url` 속성에는 `qrCodeData` 요소입니다.

## <a name="totp-client-and-server-time-skew"></a>TOTP 클라이언트 및 서버 시간 오차

TOTP (시간 기반 일회용 암호) 인증을 정확 하 게 시간이 서버 및 인증 장치에 따라 달라 집니다. 토큰 30 초 동안 지속 됩니다. TOTP 2FA 로그인 실패 하는 경우에 서버에 정확 하 고 정확한 NTP 서비스에 동기화 된 것이 좋습니다 인지 확인 합니다.

::: moniker-end
