---
title: ASP.NET Core에서 해시 암호
author: rick-anderson
description: ASP.NET Core 데이터 보호 Api를 사용 하 여 암호를 해시 하는 방법에 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: 70301ffffbaaf3c5ff0642b19b80e40be83aa438
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896650"
---
# <a name="hash-passwords-in-aspnet-core"></a>ASP.NET Core에서 해시 암호

데이터 보호 코드 베이스에는 암호화 키 파생 함수를 제공해주는 *Microsoft.AspNetCore.Cryptography.KeyDerivation* 패키지가 포함되어 있습니다. 이 패키지는 독립적인 구성 요소로서 데이터 보호 시스템의 나머지 다른 부분에 의존하지 않습니다. 이 패키지는 완벽히 독립적으로 사용이 가능합니다. 다만 편의상 소스가 데이터 보호 코드 베이스와 함께 위치해 있을 뿐입니다.

이 패키지는 [PBKDF2 알고리즘](https://tools.ietf.org/html/rfc2898#section-5.2)을 이용해서 비밀번호 해싱을 수행하는 `KeyDerivation.Pbkdf2` 메서드를 지원합니다. 이 API는 .NET 프레임워크의 기존 [Rfc2898DeriveBytes 형식](/dotnet/api/system.security.cryptography.rfc2898derivebytes)과 매우 유사하지만, 세 가지 중요한 차이점이 존재합니다:

1. `KeyDerivation.Pbkdf2` 메서드는 다양한 PRF를 사용할 수 있는 반면 (현재 `HMACSHA1`, `HMACSHA256`, 그리고 `HMACSHA512`를 지원), `Rfc2898DeriveBytes` 형식은 `HMACSHA1`만 지원합니다.

2. `KeyDerivation.Pbkdf2` 메서드는 현재 운영 체제를 감지해서 가장 최적화된 구현 루틴을 선택하기 때문에 상황에 따라 훨씬 향상된 성능을 제공합니다. (Windows 8에서 `Rfc2898DeriveBytes`보다 약 10배에 가까운 성능을 보여줍니다.)

3. `KeyDerivation.Pbkdf2` 메서드는 호출자가 모든 매개 변수를 지정해야 합니다 (솔트, PRF, 그리고 반복 횟수까지). 반면 `Rfc2898DeriveBytes` 형식은 이에 대한 기본값들을 제공해줍니다.

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

참조 된 [소스 코드](https://github.com/aspnet/Identity/blob/master/src/Core/PasswordHasher.cs) ASP.NET Core Id에 대 한 `PasswordHasher` 실제 유형을 사용 사례입니다.
