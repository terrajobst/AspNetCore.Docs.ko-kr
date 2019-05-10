---
title: SignalR API 디자인 고려 사항
author: anurse
description: 앱의 버전 간 호환성을 위해 SignalR Api를 디자인 하는 방법에 알아봅니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 11/06/2018
uid: signalr/api-design
ms.openlocfilehash: 3f17bf055b793e8fc91fbcc15f668928ca261f77
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897810"
---
# <a name="signalr-api-design-considerations"></a>SignalR API 디자인 고려 사항

작성자: [Andrew Stanton-Nurse](https://twitter.com/anurse)

이 문서는 SignalR 기반 Api를 빌드하기 위한 지침을 제공 합니다.

## <a name="use-custom-object-parameters-to-ensure-backwards-compatibility"></a>사용자 지정 개체 매개 변수를 사용 하 여 이전 버전과 호환성 확인

매개 변수 (클라이언트 또는 서버)에서 SignalR 허브 메서드를 추가 된 *주요 변경 내용*합니다. 즉, 오래 된 클라이언트/서버 적절 한 개수의 매개 변수 없이 메서드를 호출 하려고 하면 오류가 발생 합니다. 그러나이 속성을 사용자 지정 개체 매개 변수 추가 **되지** 크게 변경 되었습니다. 이 클라이언트 또는 서버에서 변경 내용에 복원 력 있는 호환 되는 Api를 디자인할 수입니다.

예를 들어, 다음과 같은 서버 쪽 API를 고려 합니다.

[!code-csharp[ParameterBasedOldVersion](api-design/sample/Samples.cs?name=ParameterBasedOldVersion)]

이 메서드를 사용 하 여 JavaScript 클라이언트 호출 `invoke` 다음과 같습니다.

[!code-typescript[CallWithOneParameter](api-design/sample/Samples.ts?name=CallWithOneParameter)]

서버 메서드를 두 번째 매개 변수를 나중에 추가 하는 경우이 매개 변수 값 이전 버전의 클라이언트에 제공 하지 않습니다. 예를 들어:

[!code-csharp[ParameterBasedNewVersion](api-design/sample/Samples.cs?name=ParameterBasedNewVersion)]

이전 클라이언트에이 메서드를 호출 하려고 하는 경우 이와 같은 오류를 받습니다.

```
Microsoft.AspNetCore.SignalR.HubException: Failed to invoke 'GetTotalLength' due to an error on the server.
```

서버에서 다음과 같은 로그 메시지가 나타납니다.

```
System.IO.InvalidDataException: Invocation provides 1 argument(s) but target expects 2.
```

만 이전 클라이언트 하나의 매개 변수를 보냈지만 최신 서버 API 두 개의 매개 변수가 필요 합니다. 매개 변수로 사용자 지정 개체를 사용 하 여 유연성을 제공 합니다. 사용자 지정 개체를 사용 하는 원래 API를 다시 디자인할 보겠습니다.

[!code-csharp[ObjectBasedOldVersion](api-design/sample/Samples.cs?name=ObjectBasedOldVersion)]

이제 클라이언트 메서드를 호출 하는 개체를 사용 합니다.

[!code-typescript[CallWithObject](api-design/sample/Samples.ts?name=CallWithObject)]

매개 변수를 추가 하는 대신 속성을 추가 하 여 `TotalLengthRequest` 개체:

[!code-csharp[ObjectBasedNewVersion](api-design/sample/Samples.cs?name=ObjectBasedNewVersion&highlight=4,9-13)]

이전 클라이언트 추가 단일 매개 변수를 보낼 때 `Param2` 속성에 남게 되므로 `null`합니다. 이전 클라이언트를 확인 하 여 보낸 메시지를 검색할 수 있습니다 합니다 `Param2` 에 대 한 `null` 기본값을 적용 합니다. 새 클라이언트는 두 매개 변수를 보낼 수 있습니다.

[!code-typescript[CallWithObjectNew](api-design/sample/Samples.ts?name=CallWithObjectNew)]

클라이언트에서 정의 하는 방법에 대 한 기술은 작동 합니다. 서버 쪽에서 사용자 지정 개체를 보낼 수 있습니다.

[!code-csharp[ClientSideObjectBasedOld](api-design/sample/Samples.cs?name=ClientSideObjectBasedOld)]

클라이언트 쪽에서 액세스를 `Message` 매개 변수를 사용 하는 것이 아니라 속성:

[!code-typescript[OnWithObjectOld](api-design/sample/Samples.ts?name=OnWithObjectOld)]

나중에 보낸 메시지의 페이로드에를 추가 하려는 경우 개체에 속성을 추가 합니다.

[!code-csharp[ClientSideObjectBasedNew](api-design/sample/Samples.cs?name=ClientSideObjectBasedNew&highlight=5)]

이전 클라이언트를 예상 하지 않습니다는 `Sender` 값 이므로 무시 됩니다. 새 클라이언트를 새 속성을 읽으려면 업데이트 하 여 사용할 수 있습니다.

[!code-typescript[OnWithObjectNew](api-design/sample/Samples.ts?name=OnWithObjectNew&highlight=2-5)]

새 클라이언트를 제공 하지 않는 이전 서버의 내결함성 역시이 예는 `Sender` 값입니다. 이전 서버를 제공 하지 않으므로 `Sender` 값을 클라이언트에 액세스 하기 전에 있는지 확인 합니다.
