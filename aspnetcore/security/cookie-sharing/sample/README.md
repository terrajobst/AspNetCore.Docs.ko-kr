# <a name="cookie-sharing-sample-app"></a>샘플 앱을 공유 하는 쿠키

본문의 예제에서는 쿠키 인증을 사용하는 세 가지 앱 간의 쿠키 공유를 보여줍니다.

| 프로젝트                             | 설명 |
| ----------------------------------- | ----------- |
| CookieAuth.Core                     | ASP.NET Core Id를 사용 하지 않고 ASP.NET Core Razor 페이지 앱 |
| CookieAuthWithIdentity.Core         | ASP.NET Core Id 사용 하 여 ASP.NET Core MVC 앱 |
| CookieAuthWithIdentity.NETFramework | ASP.NET Id를 사용 하 여 Asp.net MVC 앱 |

지침:

1. CookieAuth.Core 앱을 실행 합니다. 사용자를 등록 합니다. 사용자가 등록 하는 경우 앱 사용자를 인증 합니다. 사용자 로그 아웃 합니다.
1. 동일한 브라우저 세션에서 CookieAuthWithIdentity.Core 앱을 실행 합니다. Core 앱과 함께 사용 되는 동일한 사용자를 등록 합니다. 사용자가 등록 하는 경우 앱 사용자를 인증 합니다. 사용자 로그 아웃 합니다.
1. 동일한 브라우저 세션에서 CookieAuthWithIdentity.NETFramework 앱을 실행 합니다. 다른 앱으로 사용 되는 동일한 사용자를 등록 합니다. 사용자가 등록 하는 경우 앱 사용자를 인증 합니다. 사용자 로그 아웃 합니다.
1. 세 가지 앱 중 하나에 사용자를 로그인 합니다. 인증 쿠키는 앱 간에 공유 됩니다. 참고 사용자가 다른 두 앱에 자동으로 로그인 됩니다.
1. 앱에서 사용자 로그 아웃 합니다. 사용자가 다른 두 개의 앱에서 자동으로 서명 note 합니다.

이 샘플에 설명 된 기능을 보여 줍니다.는 [앱 간 쿠키 공유](https://docs.microsoft.com/aspnet/core/security/cookie-sharing) 항목입니다.
