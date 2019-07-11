---
title: ASP.NET Core에서 ObjectPool 사용 하 여 개체 다시 사용
author: rick-anderson
description: ObjectPool를 사용 하 여 ASP.NET Core 앱의 성능을 높이기 위한 팁입니다.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
uid: performance/ObjectPool
ms.openlocfilehash: 771f19e54a908b8b2cd85ff72f368f16e94a2310
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815518"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a>ASP.NET Core에서 ObjectPool 사용 하 여 개체 다시 사용

하 여 [Steve Gordon](https://twitter.com/stevejgordon)하십시오 [Ryan Nowak](https://github.com/rynowak), 및 [Rick Anderson](https://twitter.com/RickAndMSFT)

<xref:Microsoft.Extensions.ObjectPool> 수집 될 개체를 허용 하지 않고 개체 그룹을 다시 사용 하기 위해 메모리에 유지를 지 원하는 ASP.NET Core 인프라의 일부가입니다.

개체가 관리 되는 경우 개체 풀을 사용 하는 것이 좋습니다.

- 할당/초기화 비용이 저렴 합니다.
- 일부 제한 된 리소스를 나타냅니다.
- 예측 가능 하 고 자주 사용 합니다.

예를 들어, ASP.NET Core 프레임 워크를 사용 하 여 개체 풀 일부 위치에서 다시 사용할 <xref:System.Text.StringBuilder> 인스턴스. `StringBuilder` 할당 하 고 문자 데이터를 저장할 버퍼 자체를 관리 합니다. ASP.NET Core 정기적으로 사용 하 여 `StringBuilder` 기능을 구현 하기 위해 및 다시 사용할 성능상 이점이 있습니다.

개체 풀링이 항상 성능이 향상 되지 않습니다.

- 개체의 초기화 비용이 높은 아닌 경우에 일반적으로 느립니다 풀에서 개체를 가져옵니다.
- 풀 할당 취소 될 때까지 풀에서 관리 되는 개체가 할당 취소 되지 않습니다.

실제 시나리오를 사용 하 여 앱 또는 라이브러리에 대 한 성능 데이터 수집 후에 개체 풀링을 사용 합니다.

**경고: 합니다 `ObjectPool` 구현 하지 않는 `IDisposable`합니다. 사용 하 여 삭제 해야 하는 형식과 권장 하지 않습니다.**

**참고: ObjectPool를 할당 하는 개체의 수에 한계를 설정 하지 않습니다, 그리고은 유지 하는 개체의 수에 제한을 부과 합니다.**

## <a name="concepts"></a>개념

<xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> -기본 개체 풀 추상화 합니다. 가져올 개체를 반환 하는 데 사용 합니다.

<xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> -개체를 만드는 방법을 하는 방법 및 사용자 지정 하려면이 구현 *재설정* 풀으로 반환 하는 경우. 이 직접 생성 하는 개체 풀에 전달할 수 있습니다... 또는

<xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> 개체 풀을 만들기 위한 팩터리 역할을 합니다.
<!-- REview, there is no ObjectPoolProvider<T> -->

ObjectPool 여러 가지 방법으로 앱에서 사용할 수 있습니다.

* 풀을 인스턴스화.
* 풀에서 등록할 [종속성 주입](xref:fundamentals/dependency-injection) (DI) 인스턴스로.
* 등록 된 `ObjectPoolProvider<>` DI 및 팩터리로 사용 합니다.

## <a name="how-to-use-objectpool"></a>ObjectPool를 사용 하는 방법

호출 <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> 개체를 가져오려는 및 <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> 개체를 반환 합니다.  모든 개체를 반환 하는 사항은 없습니다. 개체를 반환 하지 않습니다, 경우 가비지 수집 됩니다.

## <a name="objectpool-sample"></a>ObjectPool 샘플

다음 예를 참조하십시오.

* 추가 `ObjectPoolProvider` 에 [종속성 주입](xref:fundamentals/dependency-injection) 컨테이너 (DI).
* 추가 하 고 구성 `ObjectPool<StringBuilder>` DI 컨테이너에 있습니다.
* 추가 된 `BirthdayMiddleware`합니다.

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

다음 코드 구현 `BirthdayMiddleware`

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]
