---
title: ASP.NET Core Web API에서 포맷터 사용자 지정
author: rick-anderson
description: ASP.NET Core의 웹 API에서 사용자 지정 포맷터를 만들고 사용하는 방법을 알아봅니다.
ms.author: riande
ms.date: 02/08/2017
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: 122edfd4ccd06ed62e071691f421d2aeef8002b4
ms.sourcegitcommit: 488cc779fc71377d9371e7a14356113e9c7eff17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70913509"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a><span data-ttu-id="d7ca2-103">ASP.NET Core Web API에서 포맷터 사용자 지정</span><span class="sxs-lookup"><span data-stu-id="d7ca2-103">Custom formatters in ASP.NET Core Web API</span></span>

<span data-ttu-id="d7ca2-104">작성자: [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="d7ca2-104">By [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="d7ca2-105">ASP.NET Core MVC는 입력 및 출력 포맷터를 사용하여 Web API에서 데이터 교환을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-105">ASP.NET Core MVC supports data exchange in Web APIs using input and output formatters.</span></span> <span data-ttu-id="d7ca2-106">입력 포맷터는 [모델 바인딩](xref:mvc/models/model-binding)에서 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-106">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="d7ca2-107">출력 포맷터는 [응답 형식](xref:web-api/advanced/formatting)을 지정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-107">Output formatters are used to [format responses](xref:web-api/advanced/formatting).</span></span>

<span data-ttu-id="d7ca2-108">프레임워크는 JSON 및 XML에 대한 기본 제공 입력 및 출력 포맷터를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-108">The framework provides built-in input and output formatters for JSON and XML.</span></span> <span data-ttu-id="d7ca2-109">일반 텍스트에 대한 기본 제공 출력 포맷터는 제공하지만 일반 텍스트에 대한 입력 포맷터는 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-109">It provides a built-in output formatter for plain text, but doesn't provide an input formatter for plain text.</span></span>

<span data-ttu-id="d7ca2-110">이 문서에서는 사용자 지정 포맷터를 만들어 추가 형식에 대한 지원을 추가하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-110">This article shows how to add support for additional formats by creating custom formatters.</span></span> <span data-ttu-id="d7ca2-111">일반 텍스트에 대한 사용자 지정 입력 포맷터의 예제는 GitHub 의 [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs)를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-111">For an example of a custom input formatter for plain text, see [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) on GitHub.</span></span>

<span data-ttu-id="d7ca2-112">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d7ca2-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-custom-formatters"></a><span data-ttu-id="d7ca2-113">사용자 지정 포맷터를 사용하는 경우</span><span class="sxs-lookup"><span data-stu-id="d7ca2-113">When to use custom formatters</span></span>

<span data-ttu-id="d7ca2-114">[콘텐츠 협상](xref:web-api/advanced/formatting#content-negotiation) 프로세스에서 기본 제공 포맷터에 의해 지원되지 않는 콘텐츠 형식을 지원하려는 경우 사용자 지정 포맷터를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-114">Use a custom formatter when you want the [content negotiation](xref:web-api/advanced/formatting#content-negotiation) process to support a content type that isn't supported by the built-in formatters.</span></span>

<span data-ttu-id="d7ca2-115">예를 들어, 웹 API의 클라이언트 중 일부가 [Protobuf](https://github.com/google/protobuf) 형식을 처리할 수 있는 경우 더 효율적이기 때문에 해당 클라이언트에서 Protobuf를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-115">For example, if some of the clients for your web API can handle the [Protobuf](https://github.com/google/protobuf) format, you might want to use Protobuf with those clients because it's more efficient.</span></span> <span data-ttu-id="d7ca2-116">또는 웹 API에서 연락 데이터를 교환하는 데 자주 사용되는 [vCard](https://wikipedia.org/wiki/VCard) 형식으로 연락처 이름 및 주소를 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-116">Or you might want your web API to send contact names and addresses in [vCard](https://wikipedia.org/wiki/VCard) format, a commonly used format for exchanging contact data.</span></span> <span data-ttu-id="d7ca2-117">이 문서에서 제공하는 샘플 앱은 간단한 vCard 포맷터를 구현합니다.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-117">The sample app provided with this article implements a simple vCard formatter.</span></span>

## <a name="overview-of-how-to-use-a-custom-formatter"></a><span data-ttu-id="d7ca2-118">사용자 지정 포맷터를 사용하는 방법 개요</span><span class="sxs-lookup"><span data-stu-id="d7ca2-118">Overview of how to use a custom formatter</span></span>

<span data-ttu-id="d7ca2-119">사용자 지정 포맷터를 만들고 사용하는 단계는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-119">Here are the steps to create and use a custom formatter:</span></span>

* <span data-ttu-id="d7ca2-120">클라이언트에 보낼 데이터를 직렬화하려는 경우 출력 포맷터 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-120">Create an output formatter class if you want to serialize data to send to the client.</span></span>
* <span data-ttu-id="d7ca2-121">클라이언트에서 받을 데이터를 deserialize하려는 경우 입력 포맷터 클래스를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-121">Create an input formatter class if you want to deserialize data received from the client.</span></span>
* <span data-ttu-id="d7ca2-122">포맷터의 인스턴스를 [MvcOptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions)의 `InputFormatters` 및 `OutputFormatters` 컬렉션에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-122">Add instances of your formatters to the `InputFormatters` and `OutputFormatters` collections in [MvcOptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions).</span></span>

<span data-ttu-id="d7ca2-123">다음 섹션에서는 이러한 각 단계에 대한 지침 및 코드 예제를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-123">The following sections provide guidance and code examples for each of these steps.</span></span>

## <a name="how-to-create-a-custom-formatter-class"></a><span data-ttu-id="d7ca2-124">사용자 지정 포맷터 클래스를 만드는 방법</span><span class="sxs-lookup"><span data-stu-id="d7ca2-124">How to create a custom formatter class</span></span>

<span data-ttu-id="d7ca2-125">포맷터를 만들려면:</span><span class="sxs-lookup"><span data-stu-id="d7ca2-125">To create a formatter:</span></span>

* <span data-ttu-id="d7ca2-126">적절한 기본 클래스에서 클래스를 파생시킵니다.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-126">Derive the class from the appropriate base class.</span></span>
* <span data-ttu-id="d7ca2-127">생성자에서 유효한 미디어 형식 및 인코딩을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-127">Specify valid media types and encodings in the constructor.</span></span>
* <span data-ttu-id="d7ca2-128">`CanReadType`/`CanWriteType` 메서드 재정의</span><span class="sxs-lookup"><span data-stu-id="d7ca2-128">Override `CanReadType`/`CanWriteType` methods</span></span>
* <span data-ttu-id="d7ca2-129">`ReadRequestBodyAsync`/`WriteResponseBodyAsync` 메서드 재정의</span><span class="sxs-lookup"><span data-stu-id="d7ca2-129">Override `ReadRequestBodyAsync`/`WriteResponseBodyAsync` methods</span></span>
  
### <a name="derive-from-the-appropriate-base-class"></a><span data-ttu-id="d7ca2-130">적절한 기본 클래스에서 파생</span><span class="sxs-lookup"><span data-stu-id="d7ca2-130">Derive from the appropriate base class</span></span>

<span data-ttu-id="d7ca2-131">텍스트 미디어 형식(예: vCard)의 경우 [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) 또는 [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) 기본 클래스에서 파생시킵니다.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-131">For text media types (for example, vCard), derive from the [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) or [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) base class.</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=classdef)]

<span data-ttu-id="d7ca2-132">입력 포맷터 예제는 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-132">For an input formatter example, see the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

<span data-ttu-id="d7ca2-133">이진 형식의 경우 [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) 또는 [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter) 기본 클래스에서 파생시킵니다.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-133">For binary types, derive from the [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) or [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter) base class.</span></span>

### <a name="specify-valid-media-types-and-encodings"></a><span data-ttu-id="d7ca2-134">유효한 미디어 형식 및 인코딩 지정</span><span class="sxs-lookup"><span data-stu-id="d7ca2-134">Specify valid media types and encodings</span></span>

<span data-ttu-id="d7ca2-135">생성자에서 `SupportedMediaTypes` 및 `SupportedEncodings` 컬렉션에 추가하여 유효한 미디어 형식 및 인코딩을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-135">In the constructor, specify valid media types and encodings by adding to the `SupportedMediaTypes` and `SupportedEncodings` collections.</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=ctor&highlight=3,5-6)]

<span data-ttu-id="d7ca2-136">입력 포맷터 예제는 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-136">For an input formatter example, see the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

> [!NOTE]
> <span data-ttu-id="d7ca2-137">포맷터 클래스에서 생성자 종속성 주입을 수행할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-137">You can't do constructor dependency injection in a formatter class.</span></span> <span data-ttu-id="d7ca2-138">예를 들어 생성자에 로거 매개 변수를 추가하여 로거를 가져올 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-138">For example, you can't get a logger by adding a logger parameter to the constructor.</span></span> <span data-ttu-id="d7ca2-139">서비스에 액세스하려면 메서드에 전달된 컨텍스트 개체를 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-139">To access services, you have to use the context object that gets passed in to your methods.</span></span> <span data-ttu-id="d7ca2-140">[아래](#read-write) 코드 예제에서는 수행 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-140">A code example [below](#read-write) shows how to do this.</span></span>

### <a name="override-canreadtypecanwritetype"></a><span data-ttu-id="d7ca2-141">Override CanReadType/CanWriteType</span><span class="sxs-lookup"><span data-stu-id="d7ca2-141">Override CanReadType/CanWriteType</span></span>

<span data-ttu-id="d7ca2-142">`CanReadType` 또는 `CanWriteType` 메서드를 재정의하여 deserialize하거나 직렬화할 수 있는 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-142">Specify the type you can deserialize into or serialize from by overriding the `CanReadType` or `CanWriteType` methods.</span></span> <span data-ttu-id="d7ca2-143">예를 들어 `Contact` 형식에서 vCard 텍스트를 만들거나 그 반대로 수행할 수만 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-143">For example, you might only be able to create vCard text from a `Contact` type and vice versa.</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=canwritetype)]

<span data-ttu-id="d7ca2-144">입력 포맷터 예제는 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-144">For an input formatter example, see the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

#### <a name="the-canwriteresult-method"></a><span data-ttu-id="d7ca2-145">CanWriteResult 메서드</span><span class="sxs-lookup"><span data-stu-id="d7ca2-145">The CanWriteResult method</span></span>

<span data-ttu-id="d7ca2-146">일부 시나리오에서는 `CanWriteType` 대신 `CanWriteResult`를 재정의해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-146">In some scenarios you have to override `CanWriteResult` instead of `CanWriteType`.</span></span> <span data-ttu-id="d7ca2-147">다음 조건이 true인 경우 `CanWriteResult`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-147">Use `CanWriteResult` if the following conditions are true:</span></span>

* <span data-ttu-id="d7ca2-148">작업 메서드에서는 모델 클래스를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-148">Your action method returns a model class.</span></span>
* <span data-ttu-id="d7ca2-149">런타임 시 반환될 수 있는 파생 클래스가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-149">There are derived classes which might be returned at runtime.</span></span>
* <span data-ttu-id="d7ca2-150">런타임 시 작업에서 반환하는 파생 클래스를 알아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-150">You need to know at runtime which derived class was returned by the action.</span></span>

<span data-ttu-id="d7ca2-151">예를 들어 작업 메서드 서명이 `Person` 형식을 반환한다고 가정하지만 `Person`에서 파생된 `Student` 또는 `Instructor` 형식을 반환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-151">For example, suppose your action method signature returns a `Person` type, but it may return a `Student` or `Instructor` type that derives from `Person`.</span></span> <span data-ttu-id="d7ca2-152">포맷터에서 `Student` 개체만을 처리하려는 경우 `CanWriteResult` 메서드에 제공된 컨텍스트 개체에서 [개체](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) 형식을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-152">If you want your formatter to handle only `Student` objects, check the type of [Object](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) in the context object provided to the `CanWriteResult` method.</span></span> <span data-ttu-id="d7ca2-153">작업 메서드가 `IActionResult`를 반환할 때 반드시 `CanWriteResult`를 사용해야 하는 것은 아닙니다. 이 경우에 `CanWriteType` 메서드는 런타임 형식을 수신합니다.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-153">Note that it's not necessary to use `CanWriteResult` when the action method returns `IActionResult`; in that case, the `CanWriteType` method receives the runtime type.</span></span>

<a id="read-write"></a>

### <a name="override-readrequestbodyasyncwriteresponsebodyasync"></a><span data-ttu-id="d7ca2-154">ReadRequestBodyAsync/WriteResponseBodyAsync 재정의</span><span class="sxs-lookup"><span data-stu-id="d7ca2-154">Override ReadRequestBodyAsync/WriteResponseBodyAsync</span></span>

<span data-ttu-id="d7ca2-155">`ReadRequestBodyAsync` 또는 `WriteResponseBodyAsync`에서 deserialize하거나 직렬화하는 작업의 실제 작업을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-155">You do the actual work of deserializing or serializing in `ReadRequestBodyAsync` or `WriteResponseBodyAsync`.</span></span> <span data-ttu-id="d7ca2-156">다음 예제에서 강조 표시된 줄은 종속성 주입 컨테이너에서 서비스를 가져오는 방법을 보여줍니다(생성자 매개 변수에서 가져올 수 없음).</span><span class="sxs-lookup"><span data-stu-id="d7ca2-156">The highlighted lines in the following example show how to get services from the dependency injection container (you can't get them from constructor parameters).</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=writeresponse&highlight=3-4)]

<span data-ttu-id="d7ca2-157">입력 포맷터 예제는 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-157">For an input formatter example, see the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a><span data-ttu-id="d7ca2-158">사용자 지정 포맷터를 사용하도록 MVC를 구성하는 방법</span><span class="sxs-lookup"><span data-stu-id="d7ca2-158">How to configure MVC to use a custom formatter</span></span>

<span data-ttu-id="d7ca2-159">사용자 지정 포맷터를 사용하려면 `InputFormatters` 또는 `OutputFormatters` 컬렉션에 포맷터 클래스의 인스턴스를 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-159">To use a custom formatter, add an instance of the formatter class to the `InputFormatters` or `OutputFormatters` collection.</span></span>

[!code-csharp[](custom-formatters/sample/Startup.cs?name=mvcoptions&highlight=3-4)]

<span data-ttu-id="d7ca2-160">포맷터는 삽입한 순서 대로 평가됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-160">Formatters are evaluated in the order you insert them.</span></span> <span data-ttu-id="d7ca2-161">첫 번째 포맷터가 우선됩니다.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-161">The first one takes precedence.</span></span>

## <a name="next-steps"></a><span data-ttu-id="d7ca2-162">다음 단계</span><span class="sxs-lookup"><span data-stu-id="d7ca2-162">Next steps</span></span>

* <span data-ttu-id="d7ca2-163">간단한 vCard 입력 및 출력 포맷터를 구현하는 [이 문서의 샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample)입니다.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-163">[Sample app for this doc](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample), which implements simple vCard input and output formatters.</span></span> <span data-ttu-id="d7ca2-164">앱은 다음 예제와 같이 vCard를 읽고 씁니다.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-164">The apps reads and writes vCards that look like the following example:</span></span>

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
UID:20293482-9240-4d68-b475-325df4a83728
END:VCARD
```

<span data-ttu-id="d7ca2-165">vCard 출력을 확인하려면 애플리케이션을 실행하고 `http://localhost:63313/api/contacts/`(Visual Studio에서 실행할 때) 또는 `http://localhost:5000/api/contacts/`(명령줄에서 실행할 때)에 수락 헤더 "텍스트/vcard"를 포함하는 Get 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-165">To see vCard output, run the application and send a Get request with Accept header "text/vcard" to `http://localhost:63313/api/contacts/` (when running from Visual Studio) or `http://localhost:5000/api/contacts/` (when running from the command line).</span></span>

<span data-ttu-id="d7ca2-166">연락처의 메모리 내 컬렉션에 vCard를 추가하려면 콘텐츠 형식 헤더 "텍스트/vcard" 및 본문의 vCard 텍스트를 포함하는 위의 예제와 같은 형식으로 지정된 동일한 URL에 Post 요청을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="d7ca2-166">To add a vCard to the in-memory collection of contacts, send a Post request to the same URL, with Content-Type header "text/vcard" and with vCard text in the body, formatted like the example above.</span></span>
