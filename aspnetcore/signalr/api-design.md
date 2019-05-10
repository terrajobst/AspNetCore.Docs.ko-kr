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
# <a name="signalr-api-design-considerations"></a><span data-ttu-id="958f3-103">SignalR API 디자인 고려 사항</span><span class="sxs-lookup"><span data-stu-id="958f3-103">SignalR API design considerations</span></span>

<span data-ttu-id="958f3-104">작성자: [Andrew Stanton-Nurse](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="958f3-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="958f3-105">이 문서는 SignalR 기반 Api를 빌드하기 위한 지침을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="958f3-105">This article provides guidance for building SignalR-based APIs.</span></span>

## <a name="use-custom-object-parameters-to-ensure-backwards-compatibility"></a><span data-ttu-id="958f3-106">사용자 지정 개체 매개 변수를 사용 하 여 이전 버전과 호환성 확인</span><span class="sxs-lookup"><span data-stu-id="958f3-106">Use custom object parameters to ensure backwards-compatibility</span></span>

<span data-ttu-id="958f3-107">매개 변수 (클라이언트 또는 서버)에서 SignalR 허브 메서드를 추가 된 *주요 변경 내용*합니다.</span><span class="sxs-lookup"><span data-stu-id="958f3-107">Adding parameters to a SignalR hub method (on either the client or the server) is a *breaking change*.</span></span> <span data-ttu-id="958f3-108">즉, 오래 된 클라이언트/서버 적절 한 개수의 매개 변수 없이 메서드를 호출 하려고 하면 오류가 발생 합니다.</span><span class="sxs-lookup"><span data-stu-id="958f3-108">This means older clients/servers will get errors when they try to invoke the method without the appropriate number of parameters.</span></span> <span data-ttu-id="958f3-109">그러나이 속성을 사용자 지정 개체 매개 변수 추가 **되지** 크게 변경 되었습니다.</span><span class="sxs-lookup"><span data-stu-id="958f3-109">However, adding properties to a custom object parameter is **not** a breaking change.</span></span> <span data-ttu-id="958f3-110">이 클라이언트 또는 서버에서 변경 내용에 복원 력 있는 호환 되는 Api를 디자인할 수입니다.</span><span class="sxs-lookup"><span data-stu-id="958f3-110">This can be used to design compatible APIs that are resilient to changes on the client or the server.</span></span>

<span data-ttu-id="958f3-111">예를 들어, 다음과 같은 서버 쪽 API를 고려 합니다.</span><span class="sxs-lookup"><span data-stu-id="958f3-111">For example, consider a server-side API like the following:</span></span>

[!code-csharp[ParameterBasedOldVersion](api-design/sample/Samples.cs?name=ParameterBasedOldVersion)]

<span data-ttu-id="958f3-112">이 메서드를 사용 하 여 JavaScript 클라이언트 호출 `invoke` 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="958f3-112">The JavaScript client calls this method using `invoke` as follows:</span></span>

[!code-typescript[CallWithOneParameter](api-design/sample/Samples.ts?name=CallWithOneParameter)]

<span data-ttu-id="958f3-113">서버 메서드를 두 번째 매개 변수를 나중에 추가 하는 경우이 매개 변수 값 이전 버전의 클라이언트에 제공 하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="958f3-113">If you later add a second parameter to the server method, older clients won't provide this parameter value.</span></span> <span data-ttu-id="958f3-114">예를 들어:</span><span class="sxs-lookup"><span data-stu-id="958f3-114">For example:</span></span>

[!code-csharp[ParameterBasedNewVersion](api-design/sample/Samples.cs?name=ParameterBasedNewVersion)]

<span data-ttu-id="958f3-115">이전 클라이언트에이 메서드를 호출 하려고 하는 경우 이와 같은 오류를 받습니다.</span><span class="sxs-lookup"><span data-stu-id="958f3-115">When the old client tries to invoke this method, it will get an error like this:</span></span>

```
Microsoft.AspNetCore.SignalR.HubException: Failed to invoke 'GetTotalLength' due to an error on the server.
```

<span data-ttu-id="958f3-116">서버에서 다음과 같은 로그 메시지가 나타납니다.</span><span class="sxs-lookup"><span data-stu-id="958f3-116">On the server, you'll see a log message like this:</span></span>

```
System.IO.InvalidDataException: Invocation provides 1 argument(s) but target expects 2.
```

<span data-ttu-id="958f3-117">만 이전 클라이언트 하나의 매개 변수를 보냈지만 최신 서버 API 두 개의 매개 변수가 필요 합니다.</span><span class="sxs-lookup"><span data-stu-id="958f3-117">The old client only sent one parameter, but the newer server API required two parameters.</span></span> <span data-ttu-id="958f3-118">매개 변수로 사용자 지정 개체를 사용 하 여 유연성을 제공 합니다.</span><span class="sxs-lookup"><span data-stu-id="958f3-118">Using custom objects as parameters gives you more flexibility.</span></span> <span data-ttu-id="958f3-119">사용자 지정 개체를 사용 하는 원래 API를 다시 디자인할 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="958f3-119">Let's redesign the original API to use a custom object:</span></span>

[!code-csharp[ObjectBasedOldVersion](api-design/sample/Samples.cs?name=ObjectBasedOldVersion)]

<span data-ttu-id="958f3-120">이제 클라이언트 메서드를 호출 하는 개체를 사용 합니다.</span><span class="sxs-lookup"><span data-stu-id="958f3-120">Now, the client uses an object to call the method:</span></span>

[!code-typescript[CallWithObject](api-design/sample/Samples.ts?name=CallWithObject)]

<span data-ttu-id="958f3-121">매개 변수를 추가 하는 대신 속성을 추가 하 여 `TotalLengthRequest` 개체:</span><span class="sxs-lookup"><span data-stu-id="958f3-121">Instead of adding a parameter, add a property to the `TotalLengthRequest` object:</span></span>

[!code-csharp[ObjectBasedNewVersion](api-design/sample/Samples.cs?name=ObjectBasedNewVersion&highlight=4,9-13)]

<span data-ttu-id="958f3-122">이전 클라이언트 추가 단일 매개 변수를 보낼 때 `Param2` 속성에 남게 되므로 `null`합니다.</span><span class="sxs-lookup"><span data-stu-id="958f3-122">When the old client sends a single parameter, the extra `Param2` property will be left `null`.</span></span> <span data-ttu-id="958f3-123">이전 클라이언트를 확인 하 여 보낸 메시지를 검색할 수 있습니다 합니다 `Param2` 에 대 한 `null` 기본값을 적용 합니다.</span><span class="sxs-lookup"><span data-stu-id="958f3-123">You can detect a message sent by an older client by checking the `Param2` for `null` and apply a default value.</span></span> <span data-ttu-id="958f3-124">새 클라이언트는 두 매개 변수를 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="958f3-124">A new client can send both parameters.</span></span>

[!code-typescript[CallWithObjectNew](api-design/sample/Samples.ts?name=CallWithObjectNew)]

<span data-ttu-id="958f3-125">클라이언트에서 정의 하는 방법에 대 한 기술은 작동 합니다.</span><span class="sxs-lookup"><span data-stu-id="958f3-125">The same technique works for methods defined on the client.</span></span> <span data-ttu-id="958f3-126">서버 쪽에서 사용자 지정 개체를 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="958f3-126">You can send a custom object from the server side:</span></span>

[!code-csharp[ClientSideObjectBasedOld](api-design/sample/Samples.cs?name=ClientSideObjectBasedOld)]

<span data-ttu-id="958f3-127">클라이언트 쪽에서 액세스를 `Message` 매개 변수를 사용 하는 것이 아니라 속성:</span><span class="sxs-lookup"><span data-stu-id="958f3-127">On the client side, you access the `Message` property rather than using a parameter:</span></span>

[!code-typescript[OnWithObjectOld](api-design/sample/Samples.ts?name=OnWithObjectOld)]

<span data-ttu-id="958f3-128">나중에 보낸 메시지의 페이로드에를 추가 하려는 경우 개체에 속성을 추가 합니다.</span><span class="sxs-lookup"><span data-stu-id="958f3-128">If you later decide to add the sender of the message to the payload, add a property to the object:</span></span>

[!code-csharp[ClientSideObjectBasedNew](api-design/sample/Samples.cs?name=ClientSideObjectBasedNew&highlight=5)]

<span data-ttu-id="958f3-129">이전 클라이언트를 예상 하지 않습니다는 `Sender` 값 이므로 무시 됩니다.</span><span class="sxs-lookup"><span data-stu-id="958f3-129">The older clients won't be expecting the `Sender` value, so they'll ignore it.</span></span> <span data-ttu-id="958f3-130">새 클라이언트를 새 속성을 읽으려면 업데이트 하 여 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="958f3-130">A new client can accept it by updating to read the new property:</span></span>

[!code-typescript[OnWithObjectNew](api-design/sample/Samples.ts?name=OnWithObjectNew&highlight=2-5)]

<span data-ttu-id="958f3-131">새 클라이언트를 제공 하지 않는 이전 서버의 내결함성 역시이 예는 `Sender` 값입니다.</span><span class="sxs-lookup"><span data-stu-id="958f3-131">In this case, the new client is also tolerant of an old server that doesn't provide the `Sender` value.</span></span> <span data-ttu-id="958f3-132">이전 서버를 제공 하지 않으므로 `Sender` 값을 클라이언트에 액세스 하기 전에 있는지 확인 합니다.</span><span class="sxs-lookup"><span data-stu-id="958f3-132">Since the old server won't provide the `Sender` value, the client checks to see if it exists before accessing it.</span></span>
