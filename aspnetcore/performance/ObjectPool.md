---
title: ASP.NET Core ObjectPool에서 개체 다시 사용
author: rick-anderson
description: ObjectPool을 사용 하 여 ASP.NET Core 앱의 성능을 향상 시키기 위한 팁입니다.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
uid: performance/ObjectPool
ms.openlocfilehash: 771f19e54a908b8b2cd85ff72f368f16e94a2310
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654381"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a>ASP.NET Core ObjectPool에서 개체 다시 사용

작성자, [Steve Gordon](https://twitter.com/stevejgordon), [Ryan nowak](https://github.com/rynowak)및 [Rick Anderson](https://twitter.com/RickAndMSFT)

<xref:Microsoft.Extensions.ObjectPool>는 개체의 가비지 수집을 허용 하는 대신 다시 사용 하기 위해 메모리의 개체 그룹을 유지 하는 것을 지 원하는 ASP.NET Core 인프라의 일부입니다.

관리 중인 개체가 다음과 같은 경우 개체 풀을 사용 하는 것이 좋습니다.

- 할당/초기화 비용이 많이 듭니다.
- 제한 된 리소스를 나타냅니다.
- 예측 가능 하 고 자주 사용 됩니다.

예를 들어 ASP.NET Core 프레임 워크는 일부 위치에서 개체 풀을 사용 하 여 <xref:System.Text.StringBuilder> 인스턴스를 다시 사용 합니다. `StringBuilder`는 문자 데이터를 저장 하기 위해 자체 버퍼를 할당 하 고 관리 합니다. ASP.NET Core는 정기적으로 `StringBuilder`를 사용 하 여 기능을 구현 하 고 재사용 하면 성능상의 이점을 얻을 수 있습니다.

개체 풀링은 항상 성능을 향상 시 키 지 않습니다.

- 개체의 초기화 비용이 높을 때를 제외 하 고는 일반적으로 풀에서 개체를 가져오는 속도가 느립니다.
- 풀에서 관리 하는 개체는 풀이 할당 취소 될 때까지 할당 취소 되지 않습니다.

응용 프로그램 또는 라이브러리에 대해 현실적인 시나리오를 사용 하 여 성능 데이터를 수집한 후에만 개체 풀링을 사용 합니다.

**경고: `ObjectPool` `IDisposable`를 구현 하지 않습니다. 삭제 해야 하는 형식으로는 사용 하지 않는 것이 좋습니다.**

**참고: ObjectPool은 할당할 개체 수에 대 한 제한을 두지 않습니다. 그러면 개체 수에 대 한 제한이 유지 됩니다.**

## <a name="concepts"></a>개념

<xref:Microsoft.Extensions.ObjectPool.ObjectPool`1>-기본 개체 풀 추상화입니다. 개체를 가져오고 반환 하는 데 사용 됩니다.

<xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601>-개체가 만들어지는 방법과 풀로 반환 될 때 *다시 설정* 되는 방법을 사용자 지정 하려면이 메서드를 구현 합니다. 직접 생성 하는 개체 풀에 전달할 수 있습니다. 디스크나

<xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*>는 개체 풀을 만들기 위한 팩터리 역할을 합니다.
<!-- REview, there is no ObjectPoolProvider<T> -->

ObjectPool은 여러 가지 방법으로 앱에서 사용할 수 있습니다.

* 풀 인스턴스화.
* DI ( [종속성 주입](xref:fundamentals/dependency-injection) )의 풀을 인스턴스로 등록 하는 중입니다.
* `ObjectPoolProvider<>`를 DI에 등록 하 고 공장으로 사용 합니다.

## <a name="how-to-use-objectpool"></a>ObjectPool 사용 방법

<xref:Microsoft.Extensions.ObjectPool.ObjectPool`1>를 호출 하 여 개체를 가져오고 <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> 개체를 반환 합니다.  모든 개체를 반환 하는 요구 사항은 없습니다. 개체를 반환 하지 않으면 가비지 수집 됩니다.

## <a name="objectpool-sample"></a>ObjectPool 샘플

코드는 다음과 같습니다.

* DI ( [종속성 주입](xref:fundamentals/dependency-injection) ) 컨테이너에 `ObjectPoolProvider`를 추가 합니다.
* DI 컨테이너에 `ObjectPool<StringBuilder>`를 추가 하 고 구성 합니다.
* `BirthdayMiddleware`를 추가 합니다.

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

다음 코드는를 구현 `BirthdayMiddleware`

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]
