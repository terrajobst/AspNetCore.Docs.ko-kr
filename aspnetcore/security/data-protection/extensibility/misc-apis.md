---
title: 기타 ASP.NET Core 데이터 보호 Api
author: rick-anderson
description: ASP.NET Core 데이터 보호 ISecret 인터페이스에 알아봅니다.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/extensibility/misc-apis
ms.openlocfilehash: 114cdd6209970e46b827e403fbe79b95692d0242
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896620"
---
# <a name="miscellaneous-aspnet-core-data-protection-apis"></a><span data-ttu-id="54ec4-103">기타 ASP.NET Core 데이터 보호 Api</span><span class="sxs-lookup"><span data-stu-id="54ec4-103">Miscellaneous ASP.NET Core Data Protection APIs</span></span>

<a name="data-protection-extensibility-mics-apis"></a>

>[!WARNING]
> <span data-ttu-id="54ec4-104">다음 인터페이스 중 하나를 구현 하는 형식은 스레드로부터 안전 해야 합니다. 여러 호출자에 대 한 합니다.</span><span class="sxs-lookup"><span data-stu-id="54ec4-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="isecret"></a><span data-ttu-id="54ec4-105">ISecret</span><span class="sxs-lookup"><span data-stu-id="54ec4-105">ISecret</span></span>

<span data-ttu-id="54ec4-106">`ISecret` 인터페이스에는 암호화 키 자료와 같은 비밀 값을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="54ec4-106">The `ISecret` interface represents a secret value, such as cryptographic key material.</span></span> <span data-ttu-id="54ec4-107">다음 API 화면을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="54ec4-107">It contains the following API surface:</span></span>

* <span data-ttu-id="54ec4-108">`Length`: `int`</span><span class="sxs-lookup"><span data-stu-id="54ec4-108">`Length`: `int`</span></span>

* <span data-ttu-id="54ec4-109">`Dispose()`: `void`</span><span class="sxs-lookup"><span data-stu-id="54ec4-109">`Dispose()`: `void`</span></span>

* <span data-ttu-id="54ec4-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span><span class="sxs-lookup"><span data-stu-id="54ec4-110">`WriteSecretIntoBuffer(ArraySegment<byte> buffer)`: `void`</span></span>

<span data-ttu-id="54ec4-111">`WriteSecretIntoBuffer` 메서드는 원시 암호 값을 사용 하 여 제공 된 버퍼를 채웁니다.</span><span class="sxs-lookup"><span data-stu-id="54ec4-111">The `WriteSecretIntoBuffer` method populates the supplied buffer with the raw secret value.</span></span> <span data-ttu-id="54ec4-112">이 API는 매개 변수 버퍼 이유 반환 하는 대신는 `byte[]` 직접는이 기회를 제공 호출자 개체를 고정 하면 버퍼를 관리 되는 가비지 수집기에 대 한 보안 노출을 제한 됩니다.</span><span class="sxs-lookup"><span data-stu-id="54ec4-112">The reason this API takes the buffer as a parameter rather than returning a `byte[]` directly is that this gives the caller the opportunity to pin the buffer object, limiting secret exposure to the managed garbage collector.</span></span>

<span data-ttu-id="54ec4-113">합니다 `Secret` 의 구체적 구현 형식은 `ISecret` 경우 비밀 값을 프로세스의 메모리에 저장 됩니다.</span><span class="sxs-lookup"><span data-stu-id="54ec4-113">The `Secret` type is a concrete implementation of `ISecret` where the secret value is stored in in-process memory.</span></span> <span data-ttu-id="54ec4-114">Windows 플랫폼에서 암호 값을 통해 암호화 됩니다 [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx)합니다.</span><span class="sxs-lookup"><span data-stu-id="54ec4-114">On Windows platforms, the secret value is encrypted via [CryptProtectMemory](https://msdn.microsoft.com/library/windows/desktop/aa380262(v=vs.85).aspx).</span></span>
