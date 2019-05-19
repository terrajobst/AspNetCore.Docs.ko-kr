# <a name="additional-claims-sample-app"></a>추가 클레임 샘플 앱

샘플 앱을 보여 줍니다. 방법:

* Google에서 사용자의 지정 된 이름 및 성을 가져오고 Google에서 제공 되는 값을 사용 하 여 이름 클레임을 저장 합니다.
* 사용자의 Google 액세스 토큰을 저장할 `AuthenticationProperties`합니다.

샘플 앱을 사용 합니다.

1. 앱을 등록 하 고 유효한 클라이언트 ID 및 Google 인증에 대 한 클라이언트 암호를 가져옵니다. 자세한 내용은 [Google 외부 로그인 설치](https://docs.microsoft.com/aspnet/core/security/authentication/social/google-logins)합니다.
1. 클라이언트 ID 및 앱에 클라이언트 암호를 제공 합니다 [GoogleOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) 의 `Startup.ConfigureServices`합니다.
1. 앱을 실행 하 고 클레임 내 페이지를 요청 합니다. 사용자 서명 되지 않은 경우 앱이 Google에 리디렉션합니다. Google로 로그인 합니다. Google 사용자를 앱으로 다시 리디렉션합니다 (`/MyClaims`). 사용자가 인증 하 고 클레임 내 페이지 로드 됩니다. 지정 된 이름 및 성 클레임 아래에 표시 됩니다 **사용자 클레임** Google에서 제공 되는 값을 사용 하 여 합니다. 액세스 토큰에 표시 됩니다 **인증 속성**합니다.
