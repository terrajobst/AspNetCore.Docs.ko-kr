---
title: 기타 ASP.NET Core 데이터 보호 Api
author: rick-anderson
description: ASP.NET Core Data Protection ISecret 인터페이스에 대해 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: 114cdd6209970e46b827e403fbe79b95692d0242
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654357"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a><span data-ttu-id="3bb8a-103">기타 ASP.NET Core 데이터 보호 Api</span><span class="sxs-lookup"><span data-stu-id="3bb8a-103">Miscellaneous ASP.NET Core Data Protection APIs</span></span>

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> <span data-ttu-id="3bb8a-104">다음 인터페이스 중 하나를 구현 하는 형식은 스레드로부터 안전 해야 합니다. 여러 호출자에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="3bb8a-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="isecret"></a><span data-ttu-id="3bb8a-105">ISecret</span><span class="sxs-lookup"><span data-stu-id="3bb8a-105">ISecret</span></span>

<span data-ttu-id="3bb8a-106">`ISecret` 인터페이스는 암호화 키 자료와 같은 비밀 값을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="3bb8a-106">The `ISecret` interface represents a secret value, such as cryptographic key material.</span></span> <span data-ttu-id="3bb8a-107">다음 API 화면을 포함 합니다.</span><span class="sxs-lookup"><span data-stu-id="3bb8a-107">It contains the following API surface:</span></span>

* <span data-ttu-id="3bb8a-108">`Length`: `int`</span><span class="sxs-lookup"><span data-stu-id="3bb8a-108">`Length`: `int`</span></span>

* <span data-ttu-id="3bb8a-109">`Dispose()`: `void`</span><span class="sxs-lookup"><span data-stu-id="3bb8a-109">`Dispose()`: `void`</span></span>

* <span data-ttu-id="3bb8a-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span><span class="sxs-lookup"><span data-stu-id="3bb8a-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span></span>

<span data-ttu-id="3bb8a-111">`WriteSecretIntoBuffer` 메서드는 제공 된 버퍼를 원시 비밀 값으로 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="3bb8a-111">The `WriteSecretIntoBuffer` method populates the supplied buffer with the raw secret value.</span></span> <span data-ttu-id="3bb8a-112">이 API가 `byte[]`를 직접 반환 하지 않고 버퍼를 매개 변수로 사용 하는 이유는이로 인해 호출자가 버퍼 개체를 고정 하 여 관리 되는 가비지 수집기에 대 한 비밀 노출을 제한 하는 기회를 제공 하는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="3bb8a-112">The reason this API takes the buffer as a parameter rather than returning a `byte[]` directly is that this gives the caller the opportunity to pin the buffer object, limiting secret exposure to the managed garbage collector.</span></span>

<span data-ttu-id="3bb8a-113">`Secret` 형식은 비밀 값이 in-process 메모리에 저장 되는 `ISecret`의 구체적 구현입니다.</span><span class="sxs-lookup"><span data-stu-id="3bb8a-113">The `Secret` type is a concrete implementation of `ISecret` where the secret value is stored in in-process memory.</span></span> <span data-ttu-id="3bb8a-114">Windows 플랫폼에서 비밀 값은 [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx)를 통해 암호화 됩니다.</span><span class="sxs-lookup"><span data-stu-id="3bb8a-114">On Windows platforms, the secret value is encrypted via [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx).</span></span>
