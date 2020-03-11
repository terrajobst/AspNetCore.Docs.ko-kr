# <a name="aspnet-core-authorization-sample"></a>ASP.NET Core 권한 부여 샘플

이 샘플에서는 규칙에 따라 Razor Pages 권한 부여를 사용 하는 방법을 보여 줍니다. 이 샘플에서는 [Razor Pages 권한 부여 규칙](https://docs.microsoft.com/aspnet/core/security/authorization/razor-pages-authorization) 항목에 설명 된 기능을 보여 줍니다.

이 샘플의 사용자 권한 부여는 [ASP.NET Core id 없이 쿠키 인증 사용](https://docs.microsoft.com/aspnet/core/security/authentication/cookie) 항목에 설명 된 쿠키 인증 기능을 사용 합니다. 이 항목에 나와 있는 개념 및 예제는 ASP.NET Core Id를 사용 하는 앱에 동일 하 게 적용 됩니다. ASP.NET Core Identity를 사용 하는 방법에 대 한 자세한 내용은 [ASP.NET Core의 Id 소개](https://docs.microsoft.com/aspnet/core/security/authentication/identity)를 참조 하세요.

전자 메일 주소 **maria.rodriguez@contoso.com** 를 사용 하 여 암호를 사용 하 여 사용자를 인증 합니다. 사용자는 *Pages/Account/Login. cshtml* 파일의 `AuthenticateUser` 메서드에서 인증 됩니다. 실제 예제에서는 사용자가 데이터베이스에 대해 인증 됩니다.

## <a name="examples-in-this-sample"></a>이 샘플의 예제

| 기능 | Description |
| --- | --- |
| [AuthorizePage](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) | 지정 된 경로를 사용 하 여 페이지에 [AuthorizeFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) 를 추가 합니다. |
| [AuthorizeFolder](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizefolder) | 지정 된 경로를 사용 하 여 폴더의 모든 페이지에 [AuthorizeFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) 를 추가 합니다. |
| [AllowAnonymousToPage](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.allowanonymoustopage) | 지정 된 경로를 사용 하 여 페이지에 [AllowAnonymousFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.allowanonymousfilter) 를 추가 합니다. |
| [AllowAnonymousToFolder](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.allowanonymoustofolder) | 지정 된 경로를 사용 하 여 폴더의 모든 페이지에 [AllowAnonymousFilter](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.authorization.allowanonymousfilter) 를 추가 합니다. |
