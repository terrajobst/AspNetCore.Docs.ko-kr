---
title: ASP.NET Core의 요청 및 응답 작업
author: jkotalik
description: ASP.NET Core에서 요청 본문을 읽고 응답 본문을 쓰는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jkotalik
ms.custom: mvc
ms.date: 02/26/2019
uid: fundamentals/middleware/request-response
ms.openlocfilehash: b6e3cd4b79e0c062b271c65cd5ecbdb4ef80c3a1
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65085509"
---
# <a name="request-and-response-operations-in-aspnet-core"></a><span data-ttu-id="ae07e-103">ASP.NET Core의 요청 및 응답 작업</span><span class="sxs-lookup"><span data-stu-id="ae07e-103">Request and response operations in ASP.NET Core</span></span>

<span data-ttu-id="ae07e-104">작성자 [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="ae07e-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="ae07e-105">이 문서에서는 요청 본문을 읽고 응답 본문을 쓰는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="ae07e-105">This article explains how to read from the request body and write to the response body.</span></span> <span data-ttu-id="ae07e-106">미들웨어를 작성하는 경우 이러한 작업을 위한 코드를 작성해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ae07e-106">You might need to write code for these operations when you're writing middleware.</span></span> <span data-ttu-id="ae07e-107">그렇지 않으면 일반적으로 MVC 및 Razor Pages에서 작업을 처리하므로 이 코드를 작성할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ae07e-107">Otherwise, you typically don't have to write this code because the operations are handled by MVC and Razor Pages.</span></span>

<span data-ttu-id="ae07e-108">ASP.NET Core 3.0에는 요청 및 응답 본문에 대한 두 가지 추상(<xref:System.IO.Stream> 및 <xref:System.IO.Pipelines.Pipe>)이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ae07e-108">In ASP.NET Core 3.0, there are two abstractions for the request and response bodies: <xref:System.IO.Stream> and <xref:System.IO.Pipelines.Pipe>.</span></span> <span data-ttu-id="ae07e-109">요청 읽기에서 [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body)는 <xref:System.IO.Stream>이고, `HttpRequest.BodyPipe`는 <xref:System.IO.Pipelines.PipeReader>입니다.</span><span class="sxs-lookup"><span data-stu-id="ae07e-109">For request reading, [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) is a <xref:System.IO.Stream>, and `HttpRequest.BodyPipe` is a <xref:System.IO.Pipelines.PipeReader>.</span></span> <span data-ttu-id="ae07e-110">응답 쓰기에서 [HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body)는 이고, `HttpResponse.BodyPipe`는 <xref:System.IO.Pipelines.PipeWriter>입니다.</span><span class="sxs-lookup"><span data-stu-id="ae07e-110">For response writing, [HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) is a `HttpResponse.BodyPipe` is a <xref:System.IO.Pipelines.PipeWriter>.</span></span>

<span data-ttu-id="ae07e-111">스트림보다 파이프라인을 사용하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="ae07e-111">We recommend pipelines over streams.</span></span> <span data-ttu-id="ae07e-112">일부 간단한 작업에서는 스트림이 더 편리할 수도 있지만, 파이프라인은 성능상의 장점이 있고 대부분의 시나리오에서 더 편리합니다.</span><span class="sxs-lookup"><span data-stu-id="ae07e-112">Streams can be easier to use for some simple operations, but pipelines have a performance advantage and are easier to use in most scenarios.</span></span> <span data-ttu-id="ae07e-113">3.0에서 ASP.NET Core는 내부적으로 스트림 대신 파이프라인을 사용하기 시작했습니다.</span><span class="sxs-lookup"><span data-stu-id="ae07e-113">In 3.0, ASP.NET Core is starting to use pipelines instead of streams internally.</span></span> <span data-ttu-id="ae07e-114">다음과 같은 경우를 예로 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ae07e-114">Examples include:</span></span>

- `FormReader`
- `TextReader`
- `TexWriter`
- `HttpResponse.WriteAsync`

<span data-ttu-id="ae07e-115">스트림이 사라지는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="ae07e-115">Streams aren't going away.</span></span> <span data-ttu-id="ae07e-116">스트림은 .NET 전체에서 계속 사용되며, `FileStreams` 및 `ResponseCompression`과 같이 상응하는 파이프 항목이 없는 스트림 형식도 많습니다.</span><span class="sxs-lookup"><span data-stu-id="ae07e-116">They continue to be used throughout .NET, and many stream types don't have pipe equivalents, like `FileStreams` and `ResponseCompression`.</span></span>

## <a name="stream-examples"></a><span data-ttu-id="ae07e-117">스트림 예제</span><span class="sxs-lookup"><span data-stu-id="ae07e-117">Stream examples</span></span>

<span data-ttu-id="ae07e-118">새 줄에서 분할하여 전체 요청 본문을 문자열 목록으로 읽는 미들웨어를 만들려 한다고 가정해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="ae07e-118">Suppose you want to create a middleware that reads the entire request body as a list of strings, splitting on new lines.</span></span> <span data-ttu-id="ae07e-119">단순 스트림 구현은 다음 예제와 같이 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ae07e-119">A simple stream implementation might look like the following example:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]

<span data-ttu-id="ae07e-120">이 코드는 실행되지만 다음과 같은 몇 가지 문제가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ae07e-120">This code works, but there are some issues:</span></span>

- <span data-ttu-id="ae07e-121">예제에서는 `StringBuilder`에 추가하기 전에 즉시 제거되는 또 다른 문자열(`encodedString`)이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="ae07e-121">Before appending to the `StringBuilder`, the example creates another string (`encodedString`) that is thrown away immediately.</span></span> <span data-ttu-id="ae07e-122">이 프로세스는 스트림의 모든 바이트에 대해 발생하므로 결과는 전체 요청 본문의 크기만큼 추가 메모리가 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="ae07e-122">This process occurs for all bytes in the stream, so the result is extra memory allocation the size of the entire request body.</span></span>
- <span data-ttu-id="ae07e-123">예제에서는 새 줄에서 분할하기 전에 전체 문자열을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="ae07e-123">The example reads the entire string before splitting on new lines.</span></span> <span data-ttu-id="ae07e-124">바이트 배열에서 새 줄을 확인하는 것이 더 효율적입니다.</span><span class="sxs-lookup"><span data-stu-id="ae07e-124">It would be more efficient to check for new lines in the byte array.</span></span>

<span data-ttu-id="ae07e-125">이러한 문제 중 일부를 수정한 예제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="ae07e-125">Here's an example that fixes some of these issues:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

<span data-ttu-id="ae07e-126">이 예제는 다음과 같이 작성되었습니다.</span><span class="sxs-lookup"><span data-stu-id="ae07e-126">This example:</span></span>

- <span data-ttu-id="ae07e-127">줄 바꿈 문자가 없는 경우 `StringBuilder`에 전체 요청 본문을 버퍼링하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ae07e-127">Doesn't buffer the entire request body in a `StringBuilder` unless there aren't any newline characters.</span></span>
- <span data-ttu-id="ae07e-128">문자열에서 `Split`를 호출하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ae07e-128">Doesn't call `Split` on the string.</span></span>

<span data-ttu-id="ae07e-129">그러나 여전히 다음과 같은 몇 가지 문제가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ae07e-129">However, there are still are a few issues:</span></span>

- <span data-ttu-id="ae07e-130">줄 바꿈 문자가 스파스인 경우, 대부분의 요청 본문이 문자열에 버퍼링됩니다.</span><span class="sxs-lookup"><span data-stu-id="ae07e-130">If newline characters are sparse, much of the request body is buffered in the string .</span></span>
- <span data-ttu-id="ae07e-131">또한 여전히 문자열(`remainingString`)을 만들어 문자열 버퍼에 추가하므로 추가 할당이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="ae07e-131">It still creates strings (`remainingString`) and adds them to the string buffer, which results in an extra allocation.</span></span>

<span data-ttu-id="ae07e-132">이러한 문제는 수정할 수 있지만, 코드가 개선되는 사항도 없이 점점 더 복잡해집니다.</span><span class="sxs-lookup"><span data-stu-id="ae07e-132">These issues are fixable, but the code is becoming more and more complicated with little improvement.</span></span> <span data-ttu-id="ae07e-133">파이프라인은 코드 복잡성을 최소화하여 이러한 문제를 해결하는 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="ae07e-133">Pipelines provide a way to solve these problems with minimal code complexity.</span></span>

## <a name="pipelines"></a><span data-ttu-id="ae07e-134">파이프라인</span><span class="sxs-lookup"><span data-stu-id="ae07e-134">Pipelines</span></span>

<span data-ttu-id="ae07e-135">다음 예제에서는 `PipeReader`를 사용하여 동일한 시나리오를 처리하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ae07e-135">The following example shows how the same scenario can be handled using a `PipeReader`:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

<span data-ttu-id="ae07e-136">이 예제에서는 스트림 구현에 포함된 많은 문제를 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="ae07e-136">This example fixes many issues that the streams implementations had:</span></span>

- <span data-ttu-id="ae07e-137">`PipeReader`에서 사용되지 않은 바이트를 처리하므로 문자열 버퍼가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ae07e-137">There is no need for a string buffer because the `PipeReader` handles bytes that haven't been used.</span></span>
- <span data-ttu-id="ae07e-138">인코드된 문자열은 반환된 문자열 목록에 직접 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="ae07e-138">Encoded strings are directly added to the list of returned strings.</span></span>
- <span data-ttu-id="ae07e-139">문자열에서 사용하는 메모리 외에 할당 없이 문자열이 생성됩니다(`ToArray()` 호출 제외).</span><span class="sxs-lookup"><span data-stu-id="ae07e-139">String creation is allocation-free besides the memory used by the string (except the `ToArray()` call).</span></span>

## <a name="adapters"></a><span data-ttu-id="ae07e-140">어댑터</span><span class="sxs-lookup"><span data-stu-id="ae07e-140">Adapters</span></span>

<span data-ttu-id="ae07e-141">이제 `HttpRequest` 및 `HttpResponse`에서 `Body` 및 `BodyPipe` 속성을 둘 다 사용할 수 있으므로 `Body`를 다른 스트림으로 설정하면 어떻게 될까요?</span><span class="sxs-lookup"><span data-stu-id="ae07e-141">Now that both `Body` and `BodyPipe` properties are available for `HttpRequest` and `HttpResponse`, what happens when you set `Body` to a different stream?</span></span> <span data-ttu-id="ae07e-142">3.0에서는 새 어댑터 세트가 각 유형을 다른 유형에 맞게 자동으로 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="ae07e-142">In 3.0, a new set of adapters automatically adapt each type to the other.</span></span> <span data-ttu-id="ae07e-143">예를 들어 `HttpRequest.Body`을 새 스트림으로 설정하는 경우 `HttpRequest.BodyPipe`가 `HttpRequest.Body`를 래핑하는 새 `PipeReader`으로 자동 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="ae07e-143">For example, if you set `HttpRequest.Body` to a new stream, `HttpRequest.BodyPipe` is automatically set to a new `PipeReader` that wraps `HttpRequest.Body`.</span></span> <span data-ttu-id="ae07e-144">`BodyPipe` 속성을 설정하는 경우에도 동일한 동작이 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ae07e-144">The same behavior applies to setting the `BodyPipe` property.</span></span> <span data-ttu-id="ae07e-145">`HttpResponse.BodyPipe`를 새 `PipeWriter`로 설정하면 `HttpResponse.Body`가 `HttpResponse.BodyPipe`를 래핑하는 새 스트림으로 자동 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="ae07e-145">If `HttpResponse.BodyPipe` is set to a new `PipeWriter`, the `HttpResponse.Body` is automatically set to a new stream that wraps `HttpResponse.BodyPipe`.</span></span>

## <a name="startasync"></a><span data-ttu-id="ae07e-146">StartAsync</span><span class="sxs-lookup"><span data-stu-id="ae07e-146">StartAsync</span></span>

<span data-ttu-id="ae07e-147">`HttpResponse.StartAsync`는 3.0의 새로운 기능입니다.</span><span class="sxs-lookup"><span data-stu-id="ae07e-147">`HttpResponse.StartAsync` is new in 3.0.</span></span> <span data-ttu-id="ae07e-148">헤더를 수정할 수 없음을 나타내고 `OnStarting` 콜백을 실행하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ae07e-148">It is used to indicate that headers are unmodifiable and to run `OnStarting` callbacks.</span></span> <span data-ttu-id="ae07e-149">3.0-preview3에서는 `HttpRequest.BodyPipe`를 사용하기 전에 `StartAsync`을 호출해야 하며, 이후 릴리스에서는 권장 사항이 될 예정입니다.</span><span class="sxs-lookup"><span data-stu-id="ae07e-149">In 3.0-preview3, you have to call `StartAsync` before using `HttpRequest.BodyPipe`, and in future releases, it will be a recommendation.</span></span> <span data-ttu-id="ae07e-150">Kestrel을 서버로 사용하는 경우 `PipeReader`를 사용하기 전에 StartAsync를 호출하면 `GetMemory`에서 반환된 메모리가 외부 버퍼가 아닌 Kestrel의 내부 <xref:System.IO.Pipelines.Pipe>에 속하게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ae07e-150">When using Kestrel as a server, calling StartAsync before using the `PipeReader` guarantees that memory returned by `GetMemory` will belong to Kestrel's internal <xref:System.IO.Pipelines.Pipe> rather than an external buffer.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ae07e-151">추가 자료</span><span class="sxs-lookup"><span data-stu-id="ae07e-151">Additional resources</span></span>

- [<span data-ttu-id="ae07e-152">System.IO.Pipelines 소개</span><span class="sxs-lookup"><span data-stu-id="ae07e-152">Introducing System.IO.Pipelines</span></span>](https://devblogs.microsoft.com/dotnet/system-io-pipelines-high-performance-io-in-net/)
- <xref:fundamentals/middleware/write>