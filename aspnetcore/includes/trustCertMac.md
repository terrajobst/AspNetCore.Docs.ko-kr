---
ms.openlocfilehash: 2ec079606cb48670dbc3852482fd8d401e7db44b
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59737200"
---
* <span data-ttu-id="781bf-101">다음 명령을 실행하여 HTTPS 개발 인증서를 신뢰합니다.</span><span class="sxs-lookup"><span data-stu-id="781bf-101">Trust the HTTPS development certificate by running the following command:</span></span>

    ```console
    dotnet dev-certs https --trust
    ```

* <span data-ttu-id="781bf-102">이전 명령으로 인해 다음 출력이 표시됩니다.</span><span class="sxs-lookup"><span data-stu-id="781bf-102">The preceding command displays the following output:</span></span>

    ```console
    Trusting the HTTPS development certificate was requested. If the certificate 
    is not already trusted we will run the following command:
    'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain 
    <<certificate>>'
    This command might prompt you for your password to install the certificate on the 
    system keychain.
    The HTTPS developer certificate was generated successfully.
    ```

* <span data-ttu-id="781bf-103">메시지가 표시되면 관리 사용자 이름과 암호를 입력합니다.</span><span class="sxs-lookup"><span data-stu-id="781bf-103">Enter the admin username and password if prompted.</span></span>  <span data-ttu-id="781bf-104">이제 인증서가 설치되고 신뢰할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="781bf-104">The certificate will now be installed and trusted.</span></span>

    <span data-ttu-id="781bf-105">자세한 내용은 [ASP.NET Core HTTPS 개발 인증서 신뢰](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="781bf-105">See [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) for more information.</span></span>