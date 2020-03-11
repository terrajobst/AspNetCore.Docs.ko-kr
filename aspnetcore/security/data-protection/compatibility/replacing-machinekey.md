---
title: ASP.NET Core ASP.NET machineKey 바꾸기
author: rick-anderson
description: ASP.NET에서 machineKey를 대체 하 여 새롭고 안전한 데이터 보호 시스템을 사용할 수 있도록 하는 방법을 알아봅니다.
ms.author: riande
ms.date: 04/06/2019
uid: security/data-protection/compatibility/replacing-machinekey
ms.openlocfilehash: 2317cb50cfe63226baf336ebfc5d681d1cebe5c6
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78655083"
---
# <a name="replace-the-aspnet-machinekey-in-aspnet-core"></a>ASP.NET Core ASP.NET machineKey 바꾸기

<a name="compatibility-replacing-machinekey"></a>

ASP.NET의 `<machineKey>` 요소를 구현할 [수](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/)있습니다. 이를 활용하면 ASP.NET의 암호화 루틴을 대상으로 한 대부분의 호출을 새로운 데이터 보호 시스템을 비롯한 대체 데이터 보호 메커니즘을 통해서 라우트할 수 있습니다.

## <a name="package-installation"></a>패키지 설치

> [!NOTE]
> 새 데이터 보호 시스템은 .NET 4.5.1 이상을 대상으로 하는 기존 ASP.NET 응용 프로그램에만 설치할 수 있습니다. 응용 프로그램이 .NET 4.5 이하를 대상으로 할 경우, 설치에 실패합니다.

새로운 데이터 보호 시스템을 기존의 ASP.NET 4.5.1 이상의 프로젝트에 설치하려면 `Microsoft.AspNetCore.DataProtection.SystemWeb` 패키지를 설치하면 됩니다. 그러면 [기본 구성](xref:security/data-protection/configuration/default-settings) 설정을 사용 하 여 데이터 보호 시스템이 인스턴스화됩니다.

패키지를 설치할 때 폼 인증, 뷰 상태, ASP.NET에 대 한 호출을 포함 하 여 [대부분의 암호화 작업](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/)에 사용 하도록 지시 하는 줄을 *web.config* 에 삽입 합니다. 다음과 같이 삽입 되는 읽기 줄입니다.

```xml
<machineKey compatibilityMode="Framework45" dataProtectorType="..." />
```

>[!TIP]
> 아래 예제와 같이 "CfDJ8"로 시작 하는 `__VIEWSTATE`같은 필드를 검사 하 여 새 데이터 보호 시스템이 활성 상태 인지 확인할 수 있습니다. 이 "CfDJ8"은 데이터 보호 시스템에 의해서 보호되는 페이로드를 식별할 수 있는 매직 헤더, "09 F0 C9 F0"의 Base64 표현입니다.

```html
<input type="hidden" name="__VIEWSTATE" id="__VIEWSTATE" value="CfDJ8AWPr2EQPTBGs3L2GCZOpk...">
```

## <a name="package-configuration"></a>패키지 구성하기

데이터 보호 시스템은 별다른 구성 없이도 정상적으로 실행되는 기본 구성 설정(Default Zero-Setup Configuration)으로 인스턴스가 생성됩니다. 그러나 이 구성을 사용할 경우, 기본적으로 키가 로컬 파일 시스템에 저장되기 때문에 서버 팜에 배포된 응용 프로그램은 정상적으로 동작하지 않습니다. 이 문제점을 해결하려면 DataProtectionStartup 클래스를 상속받는 새로운 클래스를 생성한 다음, ConfigureServices 메서드를 재정의해서 데이터 보호 시스템을 구성하면 됩니다.

다음은 비활성 키가 저장되는 위치와 암호화되는 방식을 모두 재구성하는 사용자 지정 데이터 보호 구동 클래스의 예제입니다. 또한 자체적으로 응용 프로그램 이름을 지정해서 기본 응용 프로그램 격리 정책도 재정의하고 있습니다.

```csharp
using System;
using System.IO;
using Microsoft.AspNetCore.DataProtection;
using Microsoft.AspNetCore.DataProtection.SystemWeb;
using Microsoft.Extensions.DependencyInjection;

namespace DataProtectionDemo
{
    public class MyDataProtectionStartup : DataProtectionStartup
    {
        public override void ConfigureServices(IServiceCollection services)
        {
            services.AddDataProtection()
                .SetApplicationName("my-app")
                .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\myapp-keys\"))
                .ProtectKeysWithCertificate("thumbprint");
        }
    }
}
```

>[!TIP]
> SetApplicationName에 대 한 명시적 호출 대신 `<machineKey applicationName="my-app" ... />`를 사용할 수도 있습니다. 이 방법은 단지 응용 프로그램 이름만 지정하면 되는 경우, DataProtectionStartup 클래스의 파생 클래스를 굳이 만들지 않아도 되는 편리한 메커니즘을 제공해줍니다.

이 사용자 지정 구성을 사용 하도록 설정 하려면 web.config로 돌아가서 패키지 설치가 구성 파일에 추가 된 `<appSettings>` 요소를 찾습니다. 아마도 다음 비슷한 마크업과 형태를 갖고 있을 것입니다.

```xml
<appSettings>
  <!--
  If you want to customize the behavior of the ASP.NET Core Data Protection stack, set the
  "aspnet:dataProtectionStartupType" switch below to be the fully-qualified name of a
  type which subclasses Microsoft.AspNetCore.DataProtection.SystemWeb.DataProtectionStartup.
  -->
  <add key="aspnet:dataProtectionStartupType" value="" />
</appSettings>
```

그런 다음, 빈 값을 방금 생성한 `DataProtectionStartup` 파생 클래스의 정규화된 어셈블리 이름으로 채웁니다. 가령 응용 프로그램의 이름이 DataProtectionDemo라면 이 값은 다음과 같을 것입니다.

```xml
<add key="aspnet:dataProtectionStartupType"
     value="DataProtectionDemo.MyDataProtectionStartup, DataProtectionDemo" />
```

이제 응용 프로그램 내에서 새로 구성 된 데이터 보호 시스템을 사용할 준비가 되었습니다.
