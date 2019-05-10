---
title: ASP.NET Core에서 ASP.NET 컴퓨터 키를 대체 합니다.
author: rick-anderson
description: 새롭고 보다 안전한 데이터 보호 시스템의 사용을 허용 하는 ASP.NET에 machineKey를 교체 하는 방법을 검색 합니다.
ms.author: riande
ms.date: 04/06/2019
uid: security/data-protection/compatibility/replacing-machinekey
ms.openlocfilehash: 2317cb50cfe63226baf336ebfc5d681d1cebe5c6
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64895340"
---
# <a name="replace-the-aspnet-machinekey-in-aspnet-core"></a><span data-ttu-id="b7804-103">ASP.NET Core에서 ASP.NET 컴퓨터 키를 대체 합니다.</span><span class="sxs-lookup"><span data-stu-id="b7804-103">Replace the ASP.NET machineKey in ASP.NET Core</span></span>

<a name="compatibility-replacing-machinekey"></a>

<span data-ttu-id="b7804-104">ASP.NET의 `<machineKey>` 요소의 구현은 [대체가 가능합니다.](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/)</span><span class="sxs-lookup"><span data-stu-id="b7804-104">The implementation of the `<machineKey>` element in ASP.NET [is replaceable](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/).</span></span> <span data-ttu-id="b7804-105">이를 활용하면 ASP.NET의 암호화 루틴을 대상으로 한 대부분의 호출을 새로운 데이터 보호 시스템을 비롯한 대체 데이터 보호 메커니즘을 통해서 라우트할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b7804-105">This allows most calls to ASP.NET cryptographic routines to be routed through a replacement data protection mechanism, including the new data protection system.</span></span>

## <a name="package-installation"></a><span data-ttu-id="b7804-106">패키지 설치하기</span><span class="sxs-lookup"><span data-stu-id="b7804-106">Package installation</span></span>

> [!NOTE]
> <span data-ttu-id="b7804-107">.NET 4.5.1을 대상으로 하는 기존 ASP.NET 응용 프로그램에 어셈블리를 설치 하거나 나중에 새 데이터 보호 시스템 수만 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b7804-107">The new data protection system can only be installed into an existing ASP.NET application targeting .NET 4.5.1 or later.</span></span> <span data-ttu-id="b7804-108">응용 프로그램이 .NET 4.5 이하를 대상으로 할 경우, 설치에 실패합니다.</span><span class="sxs-lookup"><span data-stu-id="b7804-108">Installation will fail if the application targets .NET 4.5 or lower.</span></span>

<span data-ttu-id="b7804-109">새로운 데이터 보호 시스템을 기존의 ASP.NET 4.5.1 이상의 프로젝트에 설치하려면 `Microsoft.AspNetCore.DataProtection.SystemWeb` 패키지를 설치하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7804-109">To install the new data protection system into an existing ASP.NET 4.5.1+ project, install the package Microsoft.AspNetCore.DataProtection.SystemWeb.</span></span> <span data-ttu-id="b7804-110">이 패키지는 [기본 구성](xref:security/data-protection/configuration/default-settings) 설정을 이용해서 데이터 보호 시스템의 인스턴스를 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="b7804-110">This will instantiate the data protection system using the [default configuration](xref:security/data-protection/configuration/default-settings) settings.</span></span>

<span data-ttu-id="b7804-111">이 패키지를 설치하면 폼 인증, ViewState 및 MachineKey.Protect 메서드 호출을 비롯한 [대부분의 암호화 작업](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/)에 이 패키지를 사용하도록 ASP.NET에게 지시하는 다음과 같은 라인이 *Web.config* 파일에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7804-111">When you install the package, it inserts a line into *Web.config* that tells ASP.NET to use it for [most cryptographic operations](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/), including forms authentication, view state, and calls to MachineKey.Protect.</span></span> <span data-ttu-id="b7804-112">삽입 되는 줄 아래와 같이 합니다.</span><span class="sxs-lookup"><span data-stu-id="b7804-112">The line that's inserted reads as follows.</span></span>

```xml
<machineKey compatibilityMode="Framework45" dataProtectorType="..." />
```

>[!TIP]
> <span data-ttu-id="b7804-113">`__VIEWSTATE` 같은 숨겨진 필드의 값이 "CfDJ8"로 시작하는지 검사해보면 새로운 데이터 보호 시스템이 활성화됐는지 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b7804-113">You can tell if the new data protection system is active by inspecting fields like `__VIEWSTATE`, which should begin with "CfDJ8" as in the example below.</span></span> <span data-ttu-id="b7804-114">이 "CfDJ8"은 데이터 보호 시스템에 의해서 보호되는 페이로드를 식별할 수 있는 매직 헤더, "09 F0 C9 F0"의 Base64 표현입니다.</span><span class="sxs-lookup"><span data-stu-id="b7804-114">"CfDJ8" is the base64 representation of the magic "09 F0 C9 F0" header that identifies a payload protected by the data protection system.</span></span>

```html
<input type="hidden" name="__VIEWSTATE" id="__VIEWSTATE" value="CfDJ8AWPr2EQPTBGs3L2GCZOpk...">
```

## <a name="package-configuration"></a><span data-ttu-id="b7804-115">패키지 구성하기</span><span class="sxs-lookup"><span data-stu-id="b7804-115">Package configuration</span></span>

<span data-ttu-id="b7804-116">데이터 보호 시스템은 별다른 구성 없이도 정상적으로 실행되는 기본 구성 설정(Default Zero-Setup Configuration)으로 인스턴스가 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7804-116">The data protection system is instantiated with a default zero-setup configuration.</span></span> <span data-ttu-id="b7804-117">그러나 이 구성을 사용할 경우, 기본적으로 키가 로컬 파일 시스템에 저장되기 때문에 서버 팜에 배포된 응용 프로그램은 정상적으로 동작하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="b7804-117">However, since by default keys are persisted to the local file system, this won't work for applications which are deployed in a farm.</span></span> <span data-ttu-id="b7804-118">이 문제점을 해결하려면 DataProtectionStartup 클래스를 상속받는 새로운 클래스를 생성한 다음, ConfigureServices 메서드를 재정의해서 데이터 보호 시스템을 구성하면 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7804-118">To resolve this, you can provide configuration by creating a type which subclasses DataProtectionStartup and overrides its ConfigureServices method.</span></span>

<span data-ttu-id="b7804-119">다음은 비활성 키가 저장되는 위치와 암호화되는 방식을 모두 재구성하는 사용자 지정 데이터 보호 구동 클래스의 예제입니다.</span><span class="sxs-lookup"><span data-stu-id="b7804-119">Below is an example of a custom data protection startup type which configured both where keys are persisted and how they're encrypted at rest.</span></span> <span data-ttu-id="b7804-120">또한 자체적으로 응용 프로그램 이름을 지정해서 기본 응용 프로그램 격리 정책도 재정의하고 있습니다.</span><span class="sxs-lookup"><span data-stu-id="b7804-120">It also overrides the default app isolation policy by providing its own application name.</span></span>

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
> <span data-ttu-id="b7804-121">명시적으로 SetApplicationName을 호출하는 대신, `<machineKey applicationName="my-app" ... />`처럼 지정해도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="b7804-121">You can also use `<machineKey applicationName="my-app" ... />` in place of an explicit call to SetApplicationName.</span></span> <span data-ttu-id="b7804-122">이 방법은 단지 응용 프로그램 이름만 지정하면 되는 경우, DataProtectionStartup 클래스의 파생 클래스를 굳이 만들지 않아도 되는 편리한 메커니즘을 제공해줍니다.</span><span class="sxs-lookup"><span data-stu-id="b7804-122">This is a convenience mechanism to avoid forcing the developer to create a DataProtectionStartup-derived type if all they wanted to configure was setting the application name.</span></span>

<span data-ttu-id="b7804-123">이 사용자 지정 구성을 적용하려면, 다시 Web.config 파일로 돌아가서 패키지 설치 시 추가된 `<appSettings>` 요소를 찾습니다.</span><span class="sxs-lookup"><span data-stu-id="b7804-123">To enable this custom configuration, go back to Web.config and look for the `<appSettings>` element that the package install added to the config file.</span></span> <span data-ttu-id="b7804-124">아마도 다음 비슷한 마크업과 형태를 갖고 있을 것입니다.</span><span class="sxs-lookup"><span data-stu-id="b7804-124">It will look like the following markup:</span></span>

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

<span data-ttu-id="b7804-125">그런 다음, 빈 값을 방금 생성한 `DataProtectionStartup` 파생 클래스의 정규화된 어셈블리 이름으로 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="b7804-125">Fill in the blank value with the assembly-qualified name of the DataProtectionStartup-derived type you just created.</span></span> <span data-ttu-id="b7804-126">가령 응용 프로그램의 이름이 DataProtectionDemo라면 이 값은 다음과 같을 것입니다.</span><span class="sxs-lookup"><span data-stu-id="b7804-126">If the name of the application is DataProtectionDemo, this would look like the below.</span></span>

```xml
<add key="aspnet:dataProtectionStartupType"
     value="DataProtectionDemo.MyDataProtectionStartup, DataProtectionDemo" />
```

<span data-ttu-id="b7804-127">새로 구성 된 데이터 보호 시스템 응용 프로그램 내에서 사용할 준비가 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="b7804-127">The newly-configured data protection system is now ready for use inside the application.</span></span>
