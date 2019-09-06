---
title: ASP.NET Core의 요청 및 응답 작업
author: jkotalik
description: ASP.NET Core에서 요청 본문을 읽고 응답 본문을 쓰는 방법을 알아봅니다.
monikerRange: '>= aspnetcore-3.0'
ms.author: jukotali
ms.custom: mvc
ms.date: 08/29/2019
uid: fundamentals/middleware/request-response
ms.openlocfilehash: e992401da2d194b178afbe51a293d103def0f940
ms.sourcegitcommit: e6bd2bbe5683e9a7dbbc2f2eab644986e6dc8a87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70238145"
---
# <a name="request-and-response-operations-in-aspnet-core"></a><span data-ttu-id="f9659-103">ASP.NET Core의 요청 및 응답 작업</span><span class="sxs-lookup"><span data-stu-id="f9659-103">Request and response operations in ASP.NET Core</span></span>

<span data-ttu-id="f9659-104">작성자 [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="f9659-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="f9659-105">이 문서에서는 요청 본문을 읽고 응답 본문을 쓰는 방법을 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="f9659-105">This article explains how to read from the request body and write to the response body.</span></span> <span data-ttu-id="f9659-106">미들웨어를 작성할 때 이러한 작업에 대한 코드가 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9659-106">Code for these operations might be required when writing middleware.</span></span> <span data-ttu-id="f9659-107">미들웨어 작성 외에, MVC 및 Razor Pages에서 작업이 처리되기 때문에 사용자 지정 코드는 일반적으로 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f9659-107">Outside of writing middleware, custom code isn't generally required because the operations are handled by MVC and Razor Pages.</span></span>

<span data-ttu-id="f9659-108">요청 및 응답 본문에 대한 두 가지 추상(<xref:System.IO.Stream> 및 <xref:System.IO.Pipelines.Pipe>)이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9659-108">There are two abstractions for the request and response bodies: <xref:System.IO.Stream> and <xref:System.IO.Pipelines.Pipe>.</span></span> <span data-ttu-id="f9659-109">요청 읽기에서 [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body)는 <xref:System.IO.Stream>이고, `HttpRequest.BodyReader`는 <xref:System.IO.Pipelines.PipeReader>입니다.</span><span class="sxs-lookup"><span data-stu-id="f9659-109">For request reading, [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) is a <xref:System.IO.Stream>, and `HttpRequest.BodyReader` is a <xref:System.IO.Pipelines.PipeReader>.</span></span> <span data-ttu-id="f9659-110">응답 쓰기에서 [HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body)는 이고, `HttpResponse.BodyWriter`는 <xref:System.IO.Pipelines.PipeWriter>입니다.</span><span class="sxs-lookup"><span data-stu-id="f9659-110">For response writing, [HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) is a `HttpResponse.BodyWriter` is a <xref:System.IO.Pipelines.PipeWriter>.</span></span>

<span data-ttu-id="f9659-111">스트림보다 파이프라인이 권장됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9659-111">Pipelines are recommended over streams.</span></span> <span data-ttu-id="f9659-112">일부 간단한 작업에서는 스트림이 더 편리할 수도 있지만, 파이프라인은 성능상의 장점이 있고 대부분의 시나리오에서 더 편리합니다.</span><span class="sxs-lookup"><span data-stu-id="f9659-112">Streams can be easier to use for some simple operations, but pipelines have a performance advantage and are easier to use in most scenarios.</span></span> <span data-ttu-id="f9659-113">ASP.NET Core는 내부적으로 스트림 대신 파이프라인을 사용하기 시작했습니다.</span><span class="sxs-lookup"><span data-stu-id="f9659-113">ASP.NET Core is starting to use pipelines instead of streams internally.</span></span> <span data-ttu-id="f9659-114">다음과 같은 경우를 예로 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9659-114">Examples include:</span></span>

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

<span data-ttu-id="f9659-115">스트림이 프레임워크에서 제거되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f9659-115">Streams aren't being removed from the framework.</span></span> <span data-ttu-id="f9659-116">스트림은 .NET 전체에서 계속 사용되며, `FileStreams` 및 `ResponseCompression`과 같이 상응하는 파이프 항목이 없는 스트림 형식도 많습니다.</span><span class="sxs-lookup"><span data-stu-id="f9659-116">Streams continue to be used throughout .NET, and many stream types don't have pipe equivalents, such as `FileStreams` and `ResponseCompression`.</span></span>

## <a name="stream-examples"></a><span data-ttu-id="f9659-117">스트림 예제</span><span class="sxs-lookup"><span data-stu-id="f9659-117">Stream examples</span></span>

<span data-ttu-id="f9659-118">새 줄에서 분할하여 전체 요청 본문을 문자열 목록으로 읽는 미들웨어를 만들려 한다고 가정해 보겠습니다.</span><span class="sxs-lookup"><span data-stu-id="f9659-118">Suppose the goal is to create a middleware that reads the entire request body as a list of strings, splitting on new lines.</span></span> <span data-ttu-id="f9659-119">단순 스트림 구현은 다음 예제와 같이 표시될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9659-119">A simple stream implementation might look like the following example:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]

<span data-ttu-id="f9659-120">이 코드는 실행되지만 다음과 같은 몇 가지 문제가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9659-120">This code works, but there are some issues:</span></span>

* <span data-ttu-id="f9659-121">예제에서는 `StringBuilder`에 추가하기 전에 즉시 제거되는 또 다른 문자열(`encodedString`)이 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9659-121">Before appending to the `StringBuilder`, the example creates another string (`encodedString`) that is thrown away immediately.</span></span> <span data-ttu-id="f9659-122">이 프로세스는 스트림의 모든 바이트에 대해 발생하므로 결과는 전체 요청 본문의 크기만큼 추가 메모리가 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9659-122">This process occurs for all bytes in the stream, so the result is extra memory allocation the size of the entire request body.</span></span>
* <span data-ttu-id="f9659-123">예제에서는 새 줄에서 분할하기 전에 전체 문자열을 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="f9659-123">The example reads the entire string before splitting on new lines.</span></span> <span data-ttu-id="f9659-124">바이트 배열에서 새 줄을 확인하는 것이 더 효율적입니다.</span><span class="sxs-lookup"><span data-stu-id="f9659-124">It's more efficient to check for new lines in the byte array.</span></span>

<span data-ttu-id="f9659-125">앞의 문제 중 일부를 해결한 예제는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f9659-125">Here's an example that fixes some of the preceding issues:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

<span data-ttu-id="f9659-126">앞의 예제:</span><span class="sxs-lookup"><span data-stu-id="f9659-126">This preceding example:</span></span>

* <span data-ttu-id="f9659-127">줄 바꿈 문자가 없는 경우 `StringBuilder`에 전체 요청 본문을 버퍼링하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f9659-127">Doesn't buffer the entire request body in a `StringBuilder` unless there aren't any newline characters.</span></span>
* <span data-ttu-id="f9659-128">문자열에서 `Split`를 호출하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f9659-128">Doesn't call `Split` on the string.</span></span>

<span data-ttu-id="f9659-129">그러나 여전히 다음과 같은 몇 가지 문제가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9659-129">However, there are still are a few issues:</span></span>

* <span data-ttu-id="f9659-130">줄 바꿈 문자가 스파스인 경우, 대부분의 요청 본문이 문자열에 버퍼링됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9659-130">If newline characters are sparse, much of the request body is buffered in the string.</span></span>
* <span data-ttu-id="f9659-131">코드는 문자열(`remainingString`)을 계속 만들어 문자열 버퍼에 추가하므로 추가 할당이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="f9659-131">The code continues to create strings (`remainingString`) and adds them to the string buffer, which results in an extra allocation.</span></span>

<span data-ttu-id="f9659-132">이러한 문제는 해결할 수 있지만, 코드가 별다른 개선 사항 없이 점점 더 복잡해집니다.</span><span class="sxs-lookup"><span data-stu-id="f9659-132">These issues are fixable, but the code is becoming progressively more complicated with little improvement.</span></span> <span data-ttu-id="f9659-133">파이프라인은 코드 복잡성을 최소화하여 이러한 문제를 해결하는 방법을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f9659-133">Pipelines provide a way to solve these problems with minimal code complexity.</span></span>

## <a name="pipelines"></a><span data-ttu-id="f9659-134">파이프라인</span><span class="sxs-lookup"><span data-stu-id="f9659-134">Pipelines</span></span>

<span data-ttu-id="f9659-135">다음 예제에서는 `PipeReader`를 사용하여 동일한 시나리오를 처리하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f9659-135">The following example shows how the same scenario can be handled using a `PipeReader`:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

<span data-ttu-id="f9659-136">이 예제에서는 스트림 구현에 포함된 많은 문제를 해결합니다.</span><span class="sxs-lookup"><span data-stu-id="f9659-136">This example fixes many issues that the streams implementations had:</span></span>

* <span data-ttu-id="f9659-137">`PipeReader`에서 사용되지 않은 바이트를 처리하므로 문자열 버퍼가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f9659-137">There's no need for a string buffer because the `PipeReader` handles bytes that haven't been used.</span></span>
* <span data-ttu-id="f9659-138">인코드된 문자열은 반환된 문자열 목록에 직접 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9659-138">Encoded strings are directly added to the list of returned strings.</span></span>
* <span data-ttu-id="f9659-139">문자열에서 사용하는 메모리 외에 할당 없이 문자열이 생성됩니다(`ToArray()` 호출 제외).</span><span class="sxs-lookup"><span data-stu-id="f9659-139">String creation is allocation-free besides the memory used by the string (except the `ToArray()` call).</span></span>

## <a name="adapters"></a><span data-ttu-id="f9659-140">어댑터</span><span class="sxs-lookup"><span data-stu-id="f9659-140">Adapters</span></span>

<span data-ttu-id="f9659-141">`Body` 및 `BodyReader/BodyWriter` 속성을 `HttpRequest` 및 `HttpResponse`에 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f9659-141">Both `Body` and `BodyReader/BodyWriter` properties are available for `HttpRequest` and `HttpResponse`.</span></span> <span data-ttu-id="f9659-142">`Body`를 다른 스트림에 설정하면 새 어댑터 세트가 각 형식을 다른 형식에 맞게 자동으로 조정합니다.</span><span class="sxs-lookup"><span data-stu-id="f9659-142">When you set `Body` to a different stream, a new set of adapters automatically adapt each type to the other.</span></span> <span data-ttu-id="f9659-143">`HttpRequest.Body`를 새 스트림으로 설정하는 경우 `HttpRequest.BodyReader`가 `HttpRequest.Body`를 래핑하는 새 `PipeReader`로 자동 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9659-143">If you set `HttpRequest.Body` to a new stream, `HttpRequest.BodyReader` is automatically set to a new `PipeReader` that wraps `HttpRequest.Body`.</span></span>

## <a name="startasync"></a><span data-ttu-id="f9659-144">StartAsync</span><span class="sxs-lookup"><span data-stu-id="f9659-144">StartAsync</span></span>

<span data-ttu-id="f9659-145">`HttpResponse.StartAsync`는 헤더를 수정할 수 없음을 나타내며 `OnStarting` 콜백을 실행하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9659-145">`HttpResponse.StartAsync` is used to indicate that headers are unmodifiable and to run `OnStarting` callbacks.</span></span> <span data-ttu-id="f9659-146">Kestrel을 서버로 사용하는 경우 `PipeReader`를 사용하기 전에`StartAsync`를 호출하면 `GetMemory`에서 반환된 메모리가 외부 버퍼가 아닌 Kestrel의 내부 <xref:System.IO.Pipelines.Pipe>에 속하게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f9659-146">When using Kestrel as a server, calling `StartAsync` before using the `PipeReader` guarantees that memory returned by `GetMemory` belongs to Kestrel's internal <xref:System.IO.Pipelines.Pipe> rather than an external buffer.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f9659-147">추가 자료</span><span class="sxs-lookup"><span data-stu-id="f9659-147">Additional resources</span></span>

* [<span data-ttu-id="f9659-148">System.IO.Pipelines 소개</span><span class="sxs-lookup"><span data-stu-id="f9659-148">Introducing System.IO.Pipelines</span></span>](https://devblogs.microsoft.com/dotnet/system-io-pipelines-high-performance-io-in-net/)
* <xref:fundamentals/middleware/write>
