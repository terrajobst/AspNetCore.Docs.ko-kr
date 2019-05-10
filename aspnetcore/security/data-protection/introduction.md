---
title: ASP.NET Core 데이터 보호
author: rick-anderson
description: 데이터 보호의 개념 및 ASP.NET Core 데이터 보호 Api의 디자인 원칙을 알아봅니다.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/data-protection/introduction
ms.openlocfilehash: 37f170a3e8a46ef2215b0999358d46dd402636df
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897990"
---
# <a name="aspnet-core-data-protection"></a>ASP.NET Core 데이터 보호

종종 웹 응용 프로그램 보안에 민감한 데이터를 저장 해야 합니다. Windows 데스크톱 응용 프로그램에 대 한 DPAPI를 제공 하지만이 웹 응용 프로그램에 적합 하지 않습니다. ASP.NET Core 데이터 보호 스택이 개발자 키 관리 등 회전 하는 데이터를 보호 하 여 간단 하 고 사용 하기 쉬운 암호화 API를 제공 합니다.

ASP.NET Core 데이터 보호 스택이을 장기 대체 제공 하도록 설계 되었기 합니다 &lt;machineKey&gt; 요소 asp.net에서 1.x-4.x 합니다. 기존 암호화 스택의 많은 단점을 해결하는 한편, 현대적인 응용 프로그램에서 접할 수 있는 대부분의 사용 사례에 즉각적으로 대응할 수 있는 솔루션을 제공하도록 설계되었습니다.

## <a name="problem-statement"></a>문제 설명

전체 문제 단일 문장과에서 간결 하 게 정의할 수 있습니다. 나중에 검색에 대 한 신뢰할 수 있는 정보를 유지 해야 하지만 지 속성 메커니즘을 신뢰 하지 않습니다. 웹의 관점에서 다시 말하자면 "신뢰할 수 있는 상태를 신뢰할 수 없는 클라이언트를 통해서 왕복해야(Round-Trip) 한다"고 할 수 있습니다."

대표적인 사례가 인증 쿠키나 전달자 토큰(Bearer Token)의 경우입니다. 서버가 "나는 Groot고 xyz 권한을 갖고 있다"라는 토큰을 생성해서 클라이언트로 전달합니다. 이후 특정 시점에 클라이언트가 다시 서버로 해당 토큰을 제출하지만 서버로서는 클라이언트가 해당 토큰을 변조하지 않았다는 어떤한 보장이 필요합니다. 따라서 필요한 첫 번째 요건은 신뢰성(Authenticity)입니다(무결성(Integrity) 또는 변조 방지(Tamper-Proofing)하고도 합니다).

그리고 저장된 상태(정보)를 신뢰하는 주체는 서버이므로, 이 상태에 운영 환경 고유의 정보가 포함되어 있을 수도 있음을 예상할 수 있습니다. 이 정보는 파일 경로, 권한, 핸들 혹은 그 밖의 간접 참조, 또는 서버 고유의 데이터 중 일부 같은 형태를 갖출 수도 있습니다. 일반적으로 이런 정보들은 신뢰할 수 없는 클라이언트에게는 공개되지 않습니다. 따라서 필요한 두 번째 요건은 기밀성(Confidentiality)입니다.

마지막으로 현대적 응용 프로그램은 구성 요소를 기반으로 구축되므로 각 개별 구성 요소는 시스템의 다른 구성 요소들과 관계없이 이 시스템(데이터 보호 스택)을 활용할 수 있어야 합니다. 예를 들어, 전달자 토큰 구성 요소가 데이터 보호 스택을 사용한다면 역시 같은 스택을 사용하는 CSRF 방지 메커니즘의 간섭없이 동작할 수 있어야 합니다. 따라서 필요한 마지막 요건은 격리(Isolation)입니다.

더 많은 제약 조건을 규정해서 필요 요건의 범위를 좁힐 수 있습니다. 암호화 시스템 내에서 동작하는 모든 서비스를 동등하게 신뢰할 수 있으며, 직접 제어하는 서비스 외부에서 데이터가 생성되거나 사용될 필요가 없다고 가정합니다. 또한, 웹 서비스에 대한 각 요청이 암호화 시스템을 여러 번 거칠 수도 있기 때문에 최대한 빠르게 작업을 처리해야 합니다. 결론적으로 이런 시나리오에는 대칭 암호화가 적합하며, 필요해질 때까지 비대칭 암호화에 대해서는 무시할 수 있습니다.

## <a name="design-philosophy"></a>디자인 원칙

먼저 우리는 기존 스택과 관련된 문제점을 확인하는 작업에 착수했습니다. 이 작업을 마치고 기존 솔루션의 현황을 조사해본 결과, 기존 솔루션에는 원하는 기능들이 전혀 존재하지 않는다는 결론을 내렸습니다. 이후, 다음과 같은 몇 가지 기본 원칙에 따라 솔루션을 설계했습니다.

* 시스템 구성 방식이 간단해야 합니다. 이상적으로는 특별히 시스템을 구성하지 않고도(Zero-Configuration) 개발자들이 작업하는 데 전혀 지장이 없어야 합니다. 개발자가 특정 측면(예: 키 저장소)을 구성해야 할 경우, 해당 특정 구성을 간단히 처리할 수 있는 방법이 제공되어야 합니다.

* 간단한 소비자 지향적인 API를 제공해야 합니다. 이 API는 올바르게 사용하기는 쉽고 잘못 사용하기는 어려워야 합니다.

* 개발자는 키 관리 하기 위한 원칙을 알아봅니다 해서는 안 됩니다. 알고리즘 선택 및 개발자를 대신해 키 수명 시스템 처리 해야 합니다. 이상적으로 개발자 원시 키 자료에 액세스를 해야 적입니다.

* 가능한 경우 미사용 키를 보호 되어야 합니다. 시스템에서는 적절 한 기본 보호 메커니즘을 파악 하 고 자동으로 적용 해야 합니다.

단순 하 고 염두에서 이러한 원칙을 사용 하 여 개발할 [사용 하기 쉬운](xref:security/data-protection/using-data-protection) 데이터 보호 스택이 있습니다.

ASP.NET Core 데이터 보호 Api는 하지 주로 기밀 페이로드 무기한 유지 됩니다. 와 같은 다른 기술이 [Windows CNG DPAPI](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx) 하 고 [Azure Rights Management](/rights-management/) 무기한 저장 하는 시나리오에 보다 적합 한 마찬가지로 강력한 키 관리 기능을 갖습니다. 즉, ASP.NET Core 데이터 보호 Api를 사용 하 여 기밀 데이터의 장기 보호에 대 한 개발자를 금지 하는 항목이 없는 합니다.

## <a name="audience"></a>대상 사용자

데이터 보호 시스템 5 주 패키지도 나뉩니다. 이러한 Api의 다양 한 측면에는 세 가지 주요 대상; 대상

1. 합니다 [소비자 Api 개요](xref:security/data-protection/consumer-apis/overview) 응용 프로그램 및 프레임 워크 개발자 대상으로 합니다.

   "스택에서 작동 하는 방법에 대 한 또는 구성 하는 방법에 대 한 자세한 내용을 알아보려면 원하는 하지 않습니다. 간단히 수행 하려는 일부 작업에서 간단한 방식으로 최대한 성공적으로 Api를 사용 하 여 높은 확률을 사용 하 여. "

2. 합니다 [구성 Api](xref:security/data-protection/configuration/overview) 응용 프로그램 개발자 및 시스템 관리자를 대상으로 합니다.

   "내 환경에 필요한 기본이 아닌 경로 또는 설정을 데이터 보호 시스템에 알릴 하고자 합니다."

3. 확장성 Api 대상 개발자가 사용자 지정 정책을 구현 해야 합니다. 이러한 Api에 드문 경우로 제한 되 고 보안 인식 개발자가 발생 합니다.

   "해야 진정으로 고유한 동작 요구 사항 때문에 시스템 내에서 전체 구성 요소를 대체 합니다. 의사가 내 요구 사항을 충족 하는 플러그 인을 빌드하려면 드물게 사용 되는 부분 API 화면에 알아봅니다. "

## <a name="package-layout"></a>패키지 레이아웃

데이터 보호 스택이 다섯 개의 패키지로 구성 됩니다.

* [Microsoft.AspNetCore.DataProtection.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/) 포함 된 <xref:Microsoft.AspNetCore.DataProtection.IDataProtectionProvider> 및 <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> 데이터 보호 서비스를 만들기 위한 인터페이스입니다. 또한 이러한 형식을 사용 하기 위한 유용한 확장 메서드 포함 (예를 들어 [IDataProtector.Protect](xref:Microsoft.AspNetCore.DataProtection.DataProtectionCommonExtensions.Protect*)). 데이터 보호 시스템을 다른 곳에서 인스턴스화될 API를 사용 하는 경우 참조 `Microsoft.AspNetCore.DataProtection.Abstractions`합니다.

* [Microsoft.AspNetCore.DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) 핵심 암호화 작업, 키 관리, 구성 및 확장성을 포함 하 여 데이터 보호 시스템의 핵심 구현은 포함 합니다. 데이터 보호 시스템을 인스턴스화하기 위해 (예를 들어, 추가 하는 <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>) 또는 참조를 수정 하거나 해당 동작 확장 `Microsoft.AspNetCore.DataProtection`합니다.

* [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) -개발자가 유용 하지만 코어 패키지에 속하지 않는 추가 Api가 포함 됩니다. 이 패키지에서 종속성 주입 하지 않고 파일 시스템 위치에 키를 저장 하는 데이터 보호 시스템을 인스턴스화하기 위해 팩터리 메서드를 포함 하는 예를 들어 (참조 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>). 또한 보호 된 페이로드의 수명 제한에 대 한 확장 메서드를 포함 (참조 <xref:Microsoft.AspNetCore.DataProtection.ITimeLimitedDataProtector>).

* [Microsoft.AspNetCore.DataProtection.SystemWeb](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.SystemWeb/) 리디렉션할 기존 ASP.NET 4.x 앱에 설치할 수 있습니다 해당 `<machineKey>` 새 ASP.NET Core 데이터 보호 스택이 사용 하는 작업입니다. 자세한 내용은 <xref:security/data-protection/compatibility/replacing-machinekey>을 참조하세요.

* [Microsoft.AspNetCore.Cryptography.KeyDerivation](https://www.nuget.org/packages/Microsoft.AspNetCore.Cryptography.KeyDerivation/) 루틴 해시 PBKDF2 암호의 구현을 제공 및 사용자 암호를 안전 하 게 처리 해야 하는 시스템에서 사용할 수 있습니다. 자세한 내용은 <xref:security/data-protection/consumer-apis/password-hashing>을 참조하세요.

## <a name="additional-resources"></a>추가 자료

<xref:host-and-deploy/web-farm>
