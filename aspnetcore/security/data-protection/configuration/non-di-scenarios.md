---
title: ASP.NET Core 데이터 보호에 대한 비-DI 인식 시나리오
author: rick-anderson
description: 종속성 주입으로 제공되는 서비스를 사용할 수 없거나 사용하지 않는 데이터 보호 시나리오를 지원하는 방법에 대해 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/configuration/non-di-scenarios
ms.openlocfilehash: 34354c8443f6ae00bcce6ad9bdb6c11aaaa25bf8
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897310"
---
# <a name="non-di-aware-scenarios-for-data-protection-in-aspnet-core"></a>ASP.NET Core 데이터 보호에 대한 비-DI 인식 시나리오

작성자: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core, 데이터 보호, 종속성 주입, DataProtectionProvider [서비스 컨테이너에 추가 된](xref:security/data-protection/consumer-apis/overview), 종속 구성 요소에서 종속성 주입(DI)을 통해서 사용됩니다. 그러나 경우에 따라서는 이런 구성이 불가능한 경우도 있으며, 그 대표적인 사례가 기존 응용 프로그램에 데이터 보호 시스템을 추가하는 경우입니다.

이러한 시나리오를 지 원하는 합니다 [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) 패키지를 구체적 형식을 제공 [DataProtectionProvider](/dotnet/api/Microsoft.AspNetCore.DataProtection.DataProtectionProvider), 데이터 보호를 사용 하는 간단한 방법을 제공 하는 없이 DI에 의존 합니다. 합니다 `DataProtectionProvider` 구현 입력 [IDataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionprovider)합니다. 생성 `DataProtectionProvider` 만 제공 해야는 [DirectoryInfo](/dotnet/api/system.io.directoryinfo) 다음 코드 샘플에서 볼 수 있듯이 공급자의 암호화 키 저장 될 위치를 나타냅니다.

[!code-none[](non-di-scenarios/_static/nodisample1.cs)]

기본적으로 `DataProtectionProvider` 구체적인 형식을 원시 키 자료를 암호화 하지 전에 파일 시스템에 유지 합니다. 네트워크 공유 및 데이터 보호 시스템 개발자 지점 적절 한 미사용 키 암호화 메커니즘을 추론할 자동으로 수 없습니다. 시나리오를 지원 하기 위해서입니다.

또한, 기본적으로 `DataProtectionProvider` 구체 형식은 [앱을 격리](xref:security/data-protection/configuration/overview#per-application-isolation) 하지 않습니다. 동일한 키 디렉터리를 가리키는 모든 응용 프로그램들은 [매개 변수 용도 위해](xref:security/data-protection/consumer-apis/purpose-strings)가 일치하기만 하면 페이로드를 공유할 수 있습니다.

합니다 [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider) 생성자는 시스템의 동작에 맞게 사용할 수 있는 선택적 구성 콜백입니다. 아래 샘플에 대 한 명시적 호출을 사용 하 여 복원 격리를 보여 줍니다 [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname)합니다. 샘플도 자동으로 보관 되는 Windows DPAPI를 사용 하 여 키를 암호화 하는 시스템 구성 방법을 보여 줍니다. 모든 관련 컴퓨터에서 공유 인증서를 배포 하 고 시스템을 호출 하 여 인증서 기반 암호화를 사용 하도록 구성 하려면 디렉터리는 UNC 공유를 가리키는 경우 시킬 수 있습니다 [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate)합니다.

[!code-none[](non-di-scenarios/_static/nodisample2.cs)]

> [!TIP]
> `DataProtectionProvider` 구체 형식의 인스턴스를 생성하는 것은 비용이 많이 드는 작업입니다. 만약, 응용 프로그램이 이 형식의 인스턴스를 여러 개 관리하지만 모든 인스턴스가 동일한 키 저장소 디렉터리를 가리킨다면 응용 프로그램의 성능이 저하될 수 있습니다.  `DataProtectionProvider` 형식을 사용할 때 권장되는 방식은 응용 프로그램 개발자가 이 형식의 인스턴스를 한 번만 생성한 다음, 해당 단일 참조를 가능한 여러 번 재사용하는 것입니다. `DataProtectionProvider` 형식 및 이 형식으로부터 생성된 모든 [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector) 의 인스턴스는 다중 호출자에 대해 스레드로부터 안전(Thread-Safe)합니다.
