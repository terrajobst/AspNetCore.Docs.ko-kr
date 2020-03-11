---
title: ASP.NET Core 데이터 보호
author: rick-anderson
description: 데이터 보호의 개념과 ASP.NET Core 데이터 보호 Api의 디자인 원리에 대해 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/data-protection/introduction
ms.openlocfilehash: 37f170a3e8a46ef2215b0999358d46dd402636df
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653841"
---
# <a name="aspnet-core-data-protection"></a>ASP.NET Core 데이터 보호

웹 응용 프로그램은 보안에 중요 한 데이터를 저장 해야 하는 경우가 많습니다. Windows에서는 데스크톱 응용 프로그램에 대해 DPAPI를 제공 하지만 웹 응용 프로그램에는 적합 하지 않습니다. ASP.NET Core 데이터 보호 스택은 개발자가 키 관리 및 순환을 포함 하 여 데이터를 보호 하는 데 사용할 수 있는 간단 하 고 사용 하기 쉬운 암호화 API를 제공 합니다.

ASP.NET Core data protection stack은 ASP.NET 1.x-4.x에서 &lt;machineKey&gt; 요소에 대 한 장기 대체 역할을 하도록 디자인 되었습니다. 기존 암호화 스택의 많은 단점을 해결하는 한편, 현대적인 응용 프로그램에서 접할 수 있는 대부분의 사용 사례에 즉각적으로 대응할 수 있는 솔루션을 제공하도록 설계되었습니다.

## <a name="problem-statement"></a>문제 설명

전체적인 문제를 다음 한 문장으로 간단 명료하게 정리할 수 있습니다: 나중에 검색에 대 한 신뢰할 수 있는 정보를 유지 해야 하지만 지 속성 메커니즘이 신뢰 하지 않습니다. 웹의 관점에서 다시 말하자면 "신뢰할 수 있는 상태를 신뢰할 수 없는 클라이언트를 통해서 왕복해야(Round-Trip) 한다"고 할 수 있습니다."

대표적인 사례가 인증 쿠키나 전달자 토큰(Bearer Token)의 경우입니다. 서버가 "나는 Groot고 xyz 권한을 갖고 있다"라는 토큰을 생성해서 클라이언트로 전달합니다. 이후 특정 시점에 클라이언트가 다시 서버로 해당 토큰을 제출하지만 서버로서는 클라이언트가 해당 토큰을 변조하지 않았다는 어떤한 보장이 필요합니다. 따라서 필요한 첫 번째 요건은 신뢰성(Authenticity)입니다(무결성(Integrity) 또는 변조 방지(Tamper-Proofing)하고도 합니다).

그리고 저장된 상태(정보)를 신뢰하는 주체는 서버이므로, 이 상태에 운영 환경 고유의 정보가 포함되어 있을 수도 있음을 예상할 수 있습니다. 이 정보는 파일 경로, 권한, 핸들 혹은 그 밖의 간접 참조, 또는 서버 고유의 데이터 중 일부 같은 형태를 갖출 수도 있습니다. 일반적으로 이런 정보들은 신뢰할 수 없는 클라이언트에게는 공개되지 않습니다. 따라서 필요한 두 번째 요건은 기밀성(Confidentiality)입니다.

마지막으로 현대적 응용 프로그램은 구성 요소를 기반으로 구축되므로 각 개별 구성 요소는 시스템의 다른 구성 요소들과 관계없이 이 시스템(데이터 보호 스택)을 활용할 수 있어야 합니다. 예를 들어, 전달자 토큰 구성 요소가 데이터 보호 스택을 사용한다면 역시 같은 스택을 사용하는 CSRF 방지 메커니즘의 간섭없이 동작할 수 있어야 합니다. 따라서 필요한 마지막 요건은 격리(Isolation)입니다.

더 많은 제약 조건을 규정해서 필요 요건의 범위를 좁힐 수 있습니다. 암호화 시스템 내에서 동작하는 모든 서비스를 동등하게 신뢰할 수 있으며, 직접 제어하는 서비스 외부에서 데이터가 생성되거나 사용될 필요가 없다고 가정합니다. 또한, 웹 서비스에 대한 각 요청이 암호화 시스템을 여러 번 거칠 수도 있기 때문에 최대한 빠르게 작업을 처리해야 합니다. 결론적으로 이런 시나리오에는 대칭 암호화가 적합하며, 필요해질 때까지 비대칭 암호화에 대해서는 무시할 수 있습니다.

## <a name="design-philosophy"></a>디자인 원칙

먼저 우리는 기존 스택과 관련된 문제점을 확인하는 작업에 착수했습니다. 이 작업을 마치고 기존 솔루션의 현황을 조사해본 결과, 기존 솔루션에는 원하는 기능들이 전혀 존재하지 않는다는 결론을 내렸습니다. 이후, 다음과 같은 몇 가지 기본 원칙에 따라 솔루션을 설계했습니다.

* 시스템 구성 방식이 간단해야 합니다. 이상적으로는 특별히 시스템을 구성하지 않고도(Zero-Configuration) 개발자들이 작업하는 데 전혀 지장이 없어야 합니다. 개발자가 특정 측면(예: 키 저장소)을 구성해야 할 경우, 해당 특정 구성을 간단히 처리할 수 있는 방법이 제공되어야 합니다.

* 간단한 소비자 지향적인 API를 제공해야 합니다. 이 API는 올바르게 사용하기는 쉽고 잘못 사용하기는 어려워야 합니다.

* 개발자는 키 관리 원칙을 배울 필요가 없습니다. 시스템은 개발자를 대신해 알고리즘 선택 및 키 수명을 처리 해야 합니다. 개발자는 원시 키 자료에 대 한 액세스 권한이 없어도 됩니다.

* 가능 하면 휴지 상태의 키를 보호 해야 합니다. 시스템은 적절 한 기본 보호 메커니즘을 파악 하 고 자동으로 적용 해야 합니다.

이러한 원칙을 염두에 두면 간단 하 고 [사용 하기 쉬운](xref:security/data-protection/using-data-protection) 데이터 보호 스택을 개발 했습니다.

ASP.NET Core 데이터 보호 Api는 주로 기밀 페이로드의 무한 지 속성에 적합 하지 않습니다. [WINDOWS CNG DPAPI](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx) 및 [Azure Rights Management](/rights-management/) 와 같은 기타 기술은 무한 저장소의 시나리오에 보다 적합 하며 강력한 키 관리 기능을 제공 합니다. 즉, 기밀 데이터의 장기 보호를 위해 개발자가 ASP.NET Core 데이터 보호 Api를 사용할 필요가 없습니다.

## <a name="audience"></a>사용자

데이터 보호 시스템은 5 개의 주 패키지로 나뉩니다. 이러한 Api의 다양 한 측면은 세 가지 주요 대상을 대상으로 합니다.

1. [소비자 Api 개요](xref:security/data-protection/consumer-apis/overview) 는 응용 프로그램 및 프레임 워크 개발자를 대상으로 합니다.

   "스택 작동 방법 또는 구성 방법에 대해 알아보는 것이 좋습니다. 단순히 Api를 성공적으로 사용 하는 확률이 매우 높은 간단한 방법으로에서 일부 작업을 수행 하려고 합니다. "

2. [구성 api](xref:security/data-protection/configuration/overview) 는 응용 프로그램 개발자 및 시스템 관리자를 대상으로 합니다.

   "데이터 보호 시스템에 기본 경로 또는 설정이 필요 하지 않습니다." 라는 정보를 제공 합니다.

3. 확장성 Api는 개발자가 사용자 지정 정책을 구현 하는 것을 담당 합니다. 이러한 Api를 사용 하는 것은 드문 상황 및 숙련 된 보안 개발자로 제한 됩니다.

   "시스템 내에서 전체 구성 요소를 교체 해야 합니다. 내 요구 사항을 충족 하는 플러그 인을 빌드하기 위해 API 표면의 일반적으로 사용 되지 않는 부분을 배워야 합니다. "

## <a name="package-layout"></a>패키지 레이아웃

데이터 보호 스택은 5 개의 패키지로 구성 됩니다.

* [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/) 에는 데이터 보호 서비스를 만들기 위한 <xref:Microsoft.AspNetCore.DataProtection.IDataProtectionProvider> 및 <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> 인터페이스가 포함 되어 있습니다. 또한 이러한 형식 (예: [IDataProtector](xref:Microsoft.AspNetCore.DataProtection.DataProtectionCommonExtensions.Protect*))으로 작업 하는 데 유용한 확장 메서드가 포함 되어 있습니다. 데이터 보호 시스템이 다른 곳에서 인스턴스화되고 API를 사용 하는 경우 참조 `Microsoft.AspNetCore.DataProtection.Abstractions`.

* [AspNetCore 보호](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) 는 핵심 암호화 작업, 키 관리, 구성 및 확장성을 포함 하 여 데이터 보호 시스템의 핵심 구현을 포함 합니다. 데이터 보호 시스템 (예: <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>에 추가)을 인스턴스화하거나 해당 동작을 수정 하거나 확장 하려면 `Microsoft.AspNetCore.DataProtection`를 참조 하세요.

* [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) 에는 개발자가 유용 하지만 핵심 패키지에 속하지 않는 추가 api가 포함 되어 있습니다. 예를 들어이 패키지에는 종속성 주입 없이 파일 시스템의 위치에 키를 저장 하기 위해 데이터 보호 시스템을 인스턴스화하는 팩터리 메서드가 포함 되어 있습니다 (<xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>참조). 또한 보호 된 페이로드의 수명을 제한 하는 확장 메서드도 포함 되어 있습니다 (<xref:Microsoft.AspNetCore.DataProtection.ITimeLimitedDataProtector>참조).

* [AspNetCore 웹](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.SystemWeb/) 은 기존 ASP.NET 4.x 앱에 설치 하 여 새 ASP.NET Core 데이터 보호 스택을 사용 하도록 `<machineKey>` 작업을 리디렉션할 수 있습니다. 자세한 내용은 <xref:security/data-protection/compatibility/replacing-machinekey>을 참조하세요.

* [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Cryptography.KeyDerivation/) 는 PBKDF2 암호 해시 루틴의 구현을 제공 하며, 사용자 암호를 안전 하 게 처리 해야 하는 시스템에서 사용할 수 있습니다. 자세한 내용은 <xref:security/data-protection/consumer-apis/password-hashing>을 참조하세요.

## <a name="additional-resources"></a>추가 리소스

<xref:host-and-deploy/web-farm>
