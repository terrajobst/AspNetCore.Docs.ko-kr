---
title: ASP.NET Core에서 TOTP authenticator 앱에 대 한 QR 코드 생성 사용
author: rick-anderson
description: ASP.NET Core 2 단계 인증을 사용 하는 TOTP authenticator 앱에 대 한 QR 코드 생성을 사용 하도록 설정 하는 방법을 알아봅니다.
ms.author: riande
ms.date: 08/14/2018
uid: security/authentication/identity-enable-qrcodes
ms.openlocfilehash: a7fdc86b3fe94e714e5147c89a32fce13757d1c1
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654159"
---
# <a name="enable-qr-code-generation-for-totp-authenticator-apps-in-aspnet-core"></a>ASP.NET Core에서 TOTP authenticator 앱에 대 한 QR 코드 생성 사용

::: moniker range="<= aspnetcore-2.0"

QR 코드에 ASP.NET Core 2.0 이상이 필요 합니다.

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

ASP.NET Core는 개별 인증에 대 한 인증자 응용 프로그램 지원과 함께 제공 됩니다. 두 단계 인증 (2FA) authenticator 앱의 경우는 시간 기반 일회용 암호 알고리즘 (TOTP)를 사용 하 여 권장 접근법 2FA 위한 업계 됩니다. 2FA TOTP를 사용 하 여는 SMS 2FA 하는 것이 좋습니다. Authenticator 앱은 사용자 이름과 암호를 확인 한 후 사용자가 입력 해야 하는 6 ~ 8 자리 코드를 제공 합니다. 일반적으로 인증자 앱은 스마트폰에 설치 됩니다.

ASP.NET Core 웹 앱 템플릿은 인증자을 지원 하지만 QRCode 생성에 대 한 지원을 제공 하지 않습니다. QRCode 생성기는 2FA의 설치를 용이 하 게 합니다. 이 문서에서는 2FA 구성 페이지에 [QR 코드](https://wikipedia.org/wiki/QR_code) 생성을 추가 하는 과정을 안내 합니다.

[Google](xref:security/authentication/google-logins) 또는 [Facebook](xref:security/authentication/facebook-logins)과 같은 외부 인증 공급자를 사용 하 여 2 단계 인증을 수행 하지 않습니다. 외부 로그인은 외부 로그인 공급자가 제공 하는 모든 메커니즘에 의해 보호 됩니다. 예를 들어 [Microsoft](xref:security/authentication/microsoft-logins) 인증 공급자에는 하드웨어 키 또는 다른 2fa 방법이 필요 합니다. 기본 템플릿이 "local" 2FA를 적용 한 경우 사용자는 일반적으로 사용 되는 시나리오가 아닌 2 개의 2 개의 FA 접근 방법을 충족 해야 합니다.

## <a name="adding-qr-codes-to-the-2fa-configuration-page"></a>2FA 구성 페이지에 QR 코드 추가

이러한 지침은 https://davidshimjs.github.io/qrcodejs/ 리포지토리의 *qrcode* 를 사용 합니다.

* [Qrcode javascript 라이브러리](https://davidshimjs.github.io/qrcodejs/) 를 프로젝트의 `wwwroot\lib` 폴더에 다운로드 합니다.

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* [스 캐 폴드 Identity](xref:security/authentication/scaffold-identity) 의 지침에 따라 */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*을 생성 합니다.
* */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*에서 파일 끝에 있는 `Scripts` 섹션을 찾습니다.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* *Pages/Account/manage/EnableAuthenticator. cshtml* (Razor Pages) 또는 *Views/manage* (MVC)에서 파일 끝에 있는 `Scripts` 섹션을 찾습니다.

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")
}
```

* `Scripts` 섹션을 업데이트 하 여 추가한 `qrcodejs` 라이브러리에 대 한 참조 및 QR 코드를 생성 하는 호출을 추가 합니다. 다음과 같이 표시 됩니다.

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

* 이러한 지침에 연결 되는 단락을 삭제 합니다.

앱을 실행 하 고 QR 코드를 스캔 하 여 인증자가 증명 한 코드의 유효성을 검사할 수 있는지 확인 합니다.

## <a name="change-the-site-name-in-the-qr-code"></a>QR 코드에서 사이트 이름 변경

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

QR 코드의 사이트 이름은 처음 프로젝트를 만들 때 선택한 프로젝트 이름에서 가져옵니다. */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml.cs*에서 `GenerateQrCodeUri(string email, string unformattedKey)` 메서드를 검색 하 여이를 변경할 수 있습니다.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

QR 코드의 사이트 이름은 처음 프로젝트를 만들 때 선택한 프로젝트 이름에서 가져옵니다. *Pages/Account/Manage/* ManageController (Razor Pages) 파일 또는 *Controllers/* (MVC) 파일에서 `GenerateQrCodeUri(string email, string unformattedKey)` 메서드를 검색 하 여이를 변경할 수 있습니다.

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

템플릿의 기본 코드는 다음과 같습니다.

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

`string.Format` 호출에서 두 번째 매개 변수는 솔루션 이름에서 가져온 사이트 이름입니다. 모든 값으로 변경할 수 있지만 항상 URL로 인코딩해야 합니다.

## <a name="using-a-different-qr-code-library"></a>다른 QR 코드 라이브러리 사용

QR 코드 라이브러리를 원하는 라이브러리로 바꿀 수 있습니다. HTML에는 라이브러리에서 제공 하는 메커니즘에 따라 QR 코드를 넣을 수 있는 `qrCode` 요소가 포함 되어 있습니다.

QR 코드에 대해 올바른 형식의 URL은에서 사용할 수 있습니다.

* 모델의 `AuthenticatorUri` 속성입니다.
* `qrCodeData` 요소의 속성을 `data-url` 합니다.

## <a name="totp-client-and-server-time-skew"></a>TOTP 클라이언트 및 서버 시간 오차

TOTP (시간 기반 일회용 암호) 인증은 서버 및 인증자 장치에 정확한 시간이 있는 경우에 따라 달라 집니다. 토큰은 30 초 동안만 지속 됩니다. TOTP 2FA 로그인이 실패 하는 경우 서버 시간이 정확 하 고 정확한 NTP 서비스와 동기화 될 수 있는지 확인 합니다.

::: moniker-end
