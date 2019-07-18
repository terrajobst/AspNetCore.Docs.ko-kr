* 다음 명령을 실행하여 HTTPS 개발 인증서를 신뢰합니다.

    ```console
    dotnet dev-certs https --trust
    ```

* 이전 명령으로 인해 다음 출력이 표시됩니다.

    ```console
    Trusting the HTTPS development certificate was requested. If the certificate 
    is not already trusted we will run the following command:
    'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain 
    <<certificate>>'
    This command might prompt you for your password to install the certificate on the 
    system keychain.
    The HTTPS developer certificate was generated successfully.
    ```

* 메시지가 표시되면 관리 사용자 이름과 암호를 입력합니다.  이제 인증서가 설치되고 신뢰할 수 있습니다.

    자세한 내용은 [ASP.NET Core HTTPS 개발 인증서 신뢰](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)를 참조하세요.