Windows에서 자체 서명된 인증서는 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate?view=win10-ps)을 사용하여 만들 수 있습니다. 지원되지 않는 예는 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)을 참조하세요.

macOS, Linux 및 Windows에서는 [OpenSSL](https://www.openssl.org/)을 사용하여 인증서를 만들 수 있습니다.
