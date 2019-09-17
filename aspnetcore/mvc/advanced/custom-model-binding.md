---
title: ASP.NET Core의 사용자 지정 모델 바인딩
author: ardalis
description: 모델 바인딩을 통해 컨트롤러 작업이 ASP.NET Core의 모델 형식과 함께 직접 작동할 수 있게 하는 방법을 알아봅니다.
ms.author: riande
ms.date: 11/13/2018
uid: mvc/advanced/custom-model-binding
ms.openlocfilehash: 91f42393ffee3249f9167e10eaea7b279a7cb70b
ms.sourcegitcommit: e7c56e8da5419bbc20b437c2dd531dedf9b0dc6b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70878404"
---
# <a name="custom-model-binding-in-aspnet-core"></a><span data-ttu-id="baa53-103">ASP.NET Core의 사용자 지정 모델 바인딩</span><span class="sxs-lookup"><span data-stu-id="baa53-103">Custom Model Binding in ASP.NET Core</span></span>

<span data-ttu-id="baa53-104">작성자: [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="baa53-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="baa53-105">모델 바인딩을 통해 컨트롤러 작업에서 HTTP 요청이 아닌 모델 형식(메서드 인수로 전달된)을 직접 작업할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-105">Model binding allows controller actions to work directly with model types (passed in as method arguments), rather than HTTP requests.</span></span> <span data-ttu-id="baa53-106">들어오는 요청 데이터와 애플리케이션 모델 간의 매핑은 모델 바인더를 통해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-106">Mapping between incoming request data and application models is handled by model binders.</span></span> <span data-ttu-id="baa53-107">개발자는 사용자 지정 모델 바인더를 구현하여 기본 모델 바인딩 기능을 확장할 수 있습니다(일반적으로 개발자가 고유의 공급자를 작성할 필요는 없음).</span><span class="sxs-lookup"><span data-stu-id="baa53-107">Developers can extend the built-in model binding functionality by implementing custom model binders (though typically, you don't need to write your own provider).</span></span>

[<span data-ttu-id="baa53-108">GitHub에서 샘플 보기 또는 다운로드</span><span class="sxs-lookup"><span data-stu-id="baa53-108">View or download sample from GitHub</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/custom-model-binding/)

## <a name="default-model-binder-limitations"></a><span data-ttu-id="baa53-109">기본 모델 바인더 제한 사항</span><span class="sxs-lookup"><span data-stu-id="baa53-109">Default model binder limitations</span></span>

<span data-ttu-id="baa53-110">기본 모델 바인더는 대부분의 공용 .NET Core 데이터 형식을 지원하며 대부분의 개발자 요구 사항을 충족해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-110">The default model binders support most of the common .NET Core data types and should meet most developers' needs.</span></span> <span data-ttu-id="baa53-111">요청의 텍스트 기반 입력을 모델 형식에 직접 바인딩할 것으로 예상됩니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-111">They expect to bind text-based input from the request directly to model types.</span></span> <span data-ttu-id="baa53-112">바인딩하려면 입력을 변환해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-112">You might need to transform the input prior to binding it.</span></span> <span data-ttu-id="baa53-113">예를 들어 모델 데이터를 조회하는 데 사용할 수 있는 키를 갖고 있는 경우</span><span class="sxs-lookup"><span data-stu-id="baa53-113">For example, when you have a key that can be used to look up model data.</span></span> <span data-ttu-id="baa53-114">그 키를 기반으로 사용자 지정 모델 바인더를 사용하여 데이터를 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-114">You can use a custom model binder to fetch data based on the key.</span></span>

## <a name="model-binding-review"></a><span data-ttu-id="baa53-115">모델 바인딩 검토</span><span class="sxs-lookup"><span data-stu-id="baa53-115">Model binding review</span></span>

<span data-ttu-id="baa53-116">모델 바인딩은 작업을 수행하는 형식에 대한 특정 정의를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-116">Model binding uses specific definitions for the types it operates on.</span></span> <span data-ttu-id="baa53-117">*단순 형식*은 입력의 단일 문자열에서 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-117">A *simple type* is converted from a single string in the input.</span></span> <span data-ttu-id="baa53-118">*복합 형식*은 여러 입력 값에서 변환됩니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-118">A *complex type* is converted from multiple input values.</span></span> <span data-ttu-id="baa53-119">프레임워크는 `TypeConverter`의 존재 여부에 따라 차이점을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-119">The framework determines the difference based on the existence of a `TypeConverter`.</span></span> <span data-ttu-id="baa53-120">외부 리소스가 필요 없는 간단한 `string` -> `SomeType` 매핑이 있는 경우 형식 변환기를 만드는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-120">We recommended you create a type converter if you have a simple `string` -> `SomeType` mapping that doesn't require external resources.</span></span>

<span data-ttu-id="baa53-121">개발자 고유의 사용자 지정 모델 바인더를 만들기 전에 기존 모델 바인더가 구현되는 방식을 검토하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-121">Before creating your own custom model binder, it's worth reviewing how existing model binders are implemented.</span></span> <span data-ttu-id="baa53-122">base64로 인코딩된 문자열을 바이트 배열로 변환하는 데 사용할 수 있는 [ByteArrayModelBinder](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinder)를 고려해 보세요.</span><span class="sxs-lookup"><span data-stu-id="baa53-122">Consider the [ByteArrayModelBinder](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinder) which can be used to convert base64-encoded strings into byte arrays.</span></span> <span data-ttu-id="baa53-123">바이트 배열은 종종 파일 또는 데이터베이스 BLOB 필드로 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-123">The byte arrays are often stored as files or database BLOB fields.</span></span>

### <a name="working-with-the-bytearraymodelbinder"></a><span data-ttu-id="baa53-124">ByteArrayModelBinder 사용</span><span class="sxs-lookup"><span data-stu-id="baa53-124">Working with the ByteArrayModelBinder</span></span>

<span data-ttu-id="baa53-125">Base64로 인코딩된 문자열은 이진 데이터를 나타내는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-125">Base64-encoded strings can be used to represent binary data.</span></span> <span data-ttu-id="baa53-126">예를 들어 다음 이미지를 문자열로 인코딩할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-126">For example, the following image can be encoded as a string.</span></span>

<span data-ttu-id="baa53-127">![dotnet bot](custom-model-binding/images/bot.png "dotnet bot")</span><span class="sxs-lookup"><span data-stu-id="baa53-127">![dotnet bot](custom-model-binding/images/bot.png "dotnet bot")</span></span>

<span data-ttu-id="baa53-128">인코딩된 문자열의 일부가 다음 그림에 표시되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-128">A small portion of the encoded string is shown in the following image:</span></span>

<span data-ttu-id="baa53-129">![인코딩된 dotnet bot](custom-model-binding/images/encoded-bot.png "인코딩된 dotnet bot")</span><span class="sxs-lookup"><span data-stu-id="baa53-129">![dotnet bot encoded](custom-model-binding/images/encoded-bot.png "dotnet bot encoded")</span></span>

<span data-ttu-id="baa53-130">[샘플의 추가 정보](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/sample/CustomModelBindingSample/README.md)에 제공된 지침에 따라 base64로 인코딩된 문자열을 파일로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-130">Follow the instructions in the [sample's README](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/advanced/custom-model-binding/sample/CustomModelBindingSample/README.md) to convert the base64-encoded string into a file.</span></span>

<span data-ttu-id="baa53-131">ASP.NET Core MVC는 base64로 인코드된 문자열을 가져온 후 `ByteArrayModelBinder`를 사용하여 바이트 배열로 변환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-131">ASP.NET Core MVC can take a base64-encoded string and use a `ByteArrayModelBinder` to convert it into a byte array.</span></span> <span data-ttu-id="baa53-132">[IModelBinderProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.imodelbinderprovider)를 구현하는 [ByteArrayModelBinderProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinderprovider)는 `byte[]` 인수를 `ByteArrayModelBinder`에 매핑합니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-132">The [ByteArrayModelBinderProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.binders.bytearraymodelbinderprovider) which implements [IModelBinderProvider](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.imodelbinderprovider) maps `byte[]` arguments to `ByteArrayModelBinder`:</span></span>

```csharp
public IModelBinder GetBinder(ModelBinderProviderContext context)
{
    if (context == null)
    {
        throw new ArgumentNullException(nameof(context));
    }

    if (context.Metadata.ModelType == typeof(byte[]))
    {
        return new ByteArrayModelBinder();
    }

    return null;
}
```

<span data-ttu-id="baa53-133">개발자 고유의 사용자 지정 모델 바인더를 만들 때 고유의 `IModelBinderProvider` 형식을 구현해도 되고 [ModelBinderAttribute](/dotnet/api/microsoft.aspnetcore.mvc.modelbinderattribute)를 사용해도 됩니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-133">When creating your own custom model binder, you can implement your own `IModelBinderProvider` type, or use the [ModelBinderAttribute](/dotnet/api/microsoft.aspnetcore.mvc.modelbinderattribute).</span></span>

<span data-ttu-id="baa53-134">다음 예제에서는 `ByteArrayModelBinder`를 사용하여 base64로 인코딩된 문자열을 `byte[]`로 변환하고 그 결과를 파일에 저장하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-134">The following example shows how to use `ByteArrayModelBinder` to convert a base64-encoded string to a `byte[]` and save the result to a file:</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Controllers/ImageController.cs?name=post1&highlight=3)]

<span data-ttu-id="baa53-135">[Postman](https://www.getpostman.com/) 같은 도구를 사용하여 base64로 인코딩된 문자열을 이 api 메서드에 게시할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-135">You can POST a base64-encoded string to this api method using a tool like [Postman](https://www.getpostman.com/):</span></span>

<span data-ttu-id="baa53-136">![postman](custom-model-binding/images/postman.png "postman")</span><span class="sxs-lookup"><span data-stu-id="baa53-136">![postman](custom-model-binding/images/postman.png "postman")</span></span>

<span data-ttu-id="baa53-137">바인더가 요청 데이터를 적절한 이름의 속성 또는 인수로 바인딩할 수 있는 한, 모델 바인딩은 성공합니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-137">As long as the binder can bind request data to appropriately named properties or arguments, model binding will succeed.</span></span> <span data-ttu-id="baa53-138">다음 예제에서는 보기 모델에 `ByteArrayModelBinder`를 사용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-138">The following example shows how to use `ByteArrayModelBinder` with a view model:</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Controllers/ImageController.cs?name=post2&highlight=2)]

## <a name="custom-model-binder-sample"></a><span data-ttu-id="baa53-139">사용자 지정 모델 바인더 샘플</span><span class="sxs-lookup"><span data-stu-id="baa53-139">Custom model binder sample</span></span>

<span data-ttu-id="baa53-140">이 섹션에서는 다음과 같은 사용자 지정 모델 바인더를 구현하겠습니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-140">In this section we'll implement a custom model binder that:</span></span>

- <span data-ttu-id="baa53-141">들어오는 요청 데이터를 강력한 형식의 키 인수로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-141">Converts incoming request data into strongly typed key arguments.</span></span>
- <span data-ttu-id="baa53-142">Entity Framework Core를 사용하여 연결된 엔터티를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-142">Uses Entity Framework Core to fetch the associated entity.</span></span>
- <span data-ttu-id="baa53-143">연결된 엔터티를 작업 메서드에 인수로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-143">Passes the associated entity as an argument to the action method.</span></span>

<span data-ttu-id="baa53-144">다음 샘플은 `Author` 모델에서 `ModelBinder` 특성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-144">The following sample uses the `ModelBinder` attribute on the `Author` model:</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Data/Author.cs?highlight=10)]

<span data-ttu-id="baa53-145">이전 코드에서 `ModelBinder` 특성은 `Author` 작업 매개 변수를 바인딩하는 데 사용할 `IModelBinder` 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-145">In the preceding code, the `ModelBinder` attribute specifies the type of `IModelBinder` that should be used to bind `Author` action parameters.</span></span>

<span data-ttu-id="baa53-146">다음 `AuthorEntityBinder` 클래스는 Entity Framework Core 및 `authorId`를 사용하여 데이터 원본에서 엔터티를 가져와 `Author` 매개 변수를 바인딩합니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-146">The following `AuthorEntityBinder` class binds an `Author` parameter by fetching the entity from a data source using Entity Framework Core and an `authorId`:</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Binders/AuthorEntityBinder.cs?name=demo)]

> [!NOTE]
> <span data-ttu-id="baa53-147">앞의 `AuthorEntityBinder` 클래스는 사용자 지정 모델 바인더를 설명하기 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-147">The preceding `AuthorEntityBinder` class is intended to illustrate a custom model binder.</span></span> <span data-ttu-id="baa53-148">이 클래스는 조회 시나리오의 모범 사례를 설명하기 위한 것이 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-148">The class isn't intended to illustrate best practices for a lookup scenario.</span></span> <span data-ttu-id="baa53-149">조회의 경우 `authorId`를 바인딩하고 작업 메서드에서 데이터베이스를 쿼리합니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-149">For lookup, bind the `authorId` and query the database in an action method.</span></span> <span data-ttu-id="baa53-150">이 방식은 모델 바인딩 실패를 `NotFound` 사례와 구분합니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-150">This approach separates model binding failures from `NotFound` cases.</span></span>

<span data-ttu-id="baa53-151">다음 코드는 작업 메서드에 `AuthorEntityBinder`를 사용하는 방법을 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-151">The following code shows how to use the `AuthorEntityBinder` in an action method:</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo2&highlight=2)]

<span data-ttu-id="baa53-152">`ModelBinder` 특성은 기본 규칙을 사용하지 않는 매개 변수에 `AuthorEntityBinder`를 적용하는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-152">The `ModelBinder` attribute can be used to apply the `AuthorEntityBinder` to parameters that don't use default conventions:</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Controllers/BoundAuthorsController.cs?name=demo1&highlight=2)]

<span data-ttu-id="baa53-153">이 예에서는 인수 이름이 기본 `authorId`가 아니기 때문에 `ModelBinder` 특성을 사용하여 매개 변수에 지정됩니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-153">In this example, since the name of the argument isn't the default `authorId`, it's specified on the parameter using the `ModelBinder` attribute.</span></span> <span data-ttu-id="baa53-154">컨트롤러와 작업 메서드 모두 작업 메서드에서 엔터티를 조회하는 것에 비해 간단합니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-154">Both the controller and action method are simplified compared to looking up the entity in the action method.</span></span> <span data-ttu-id="baa53-155">Entity Framework Core를 사용하여 작성자를 가져오는 논리는 모델 바인더로 이동되었습니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-155">The logic to fetch the author using Entity Framework Core is moved to the model binder.</span></span> <span data-ttu-id="baa53-156">`Author` 모델에 바인딩하는 메서드가 여러 개 있는 경우 상당히 간소화될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-156">This can be a considerable simplification when you have several methods that bind to the `Author` model.</span></span>

<span data-ttu-id="baa53-157">개별 모델 속성(viewmodel처럼) 또는 작업 메서드 매개 변수에 `ModelBinder` 특성을 적용하여 그 형식 또는 작업에만 해당하는 특정 모델 바인더 또는 모델을 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-157">You can apply the `ModelBinder` attribute to individual model properties (such as on a viewmodel) or to action method parameters to specify a certain model binder or model name for just that type or action.</span></span>

### <a name="implementing-a-modelbinderprovider"></a><span data-ttu-id="baa53-158">ModelBinderProvider 구현</span><span class="sxs-lookup"><span data-stu-id="baa53-158">Implementing a ModelBinderProvider</span></span>

<span data-ttu-id="baa53-159">특성을 적용하는 대신, `IModelBinderProvider`를 구현할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-159">Instead of applying an attribute, you can implement `IModelBinderProvider`.</span></span> <span data-ttu-id="baa53-160">기본 제공 프레임워크 바인더는 이 방식으로 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-160">This is how the built-in framework binders are implemented.</span></span> <span data-ttu-id="baa53-161">바인더가 작동하는 형식을 지정할 때 바인더가 허용하는 입력이 **아니라** 바인더가 생성하는 인수 형식을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-161">When you specify the type your binder operates on, you specify the type of argument it produces, **not** the input your binder accepts.</span></span> <span data-ttu-id="baa53-162">다음 바인더 공급자는 `AuthorEntityBinder`를 작업합니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-162">The following binder provider works with the `AuthorEntityBinder`.</span></span> <span data-ttu-id="baa53-163">공급자의 MVC 컬렉션에 추가할 때 `Author` 또는 `Author`-형식 매개 변수에서 `ModelBinder` 특성을 사용할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-163">When it's added to MVC's collection of providers, you don't need to use the `ModelBinder` attribute on `Author` or `Author`-typed parameters.</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Binders/AuthorEntityBinderProvider.cs?highlight=17-20)]

> <span data-ttu-id="baa53-164">참고: 앞의 코드는 `BinderTypeModelBinder`를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-164">Note: The preceding code returns a `BinderTypeModelBinder`.</span></span> <span data-ttu-id="baa53-165">`BinderTypeModelBinder`는 모델 바인더에 대한 팩터리 역할을 하며 DI(종속성 주입)를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-165">`BinderTypeModelBinder` acts as a factory for model binders and provides dependency injection (DI).</span></span> <span data-ttu-id="baa53-166">`AuthorEntityBinder`는 EF Core에 액세스하려면 DI가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-166">The `AuthorEntityBinder` requires DI to access EF Core.</span></span> <span data-ttu-id="baa53-167">모델 바인더에서 DI의 서비스를 요구하는 경우 `BinderTypeModelBinder`를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-167">Use `BinderTypeModelBinder` if your model binder requires services from DI.</span></span>

<span data-ttu-id="baa53-168">사용자 지정 모델 바인더 공급자를 사용하려면 `ConfigureServices`에서 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-168">To use a custom model binder provider, add it in `ConfigureServices`:</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Startup.cs?name=callout&highlight=5-9)]

<span data-ttu-id="baa53-169">모델 바인더를 평가할 때 공급자 컬렉션은 순서대로 검사됩니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-169">When evaluating model binders, the collection of providers is examined in order.</span></span> <span data-ttu-id="baa53-170">바인더를 반환하는 첫 번째 공급자가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-170">The first provider that returns a binder is used.</span></span>

<span data-ttu-id="baa53-171">다음 이미지는 디버거의 기본 모델 바인더를 보여줍니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-171">The following image shows the default model binders from the debugger.</span></span>

<span data-ttu-id="baa53-172">![기본 모델 바인더](custom-model-binding/images/default-model-binders.png "기본 모델 바인더")</span><span class="sxs-lookup"><span data-stu-id="baa53-172">![default model binders](custom-model-binding/images/default-model-binders.png "default model binders")</span></span>

<span data-ttu-id="baa53-173">컬렉션 끝에 공급자를 추가하면 사용자 지정 바인더가 기회를 얻기도 전에 기본 제공 모델 바인더가 호출될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-173">Adding your provider to the end of the collection may result in a built-in model binder being called before your custom binder has a chance.</span></span> <span data-ttu-id="baa53-174">이 예제에서는 `Author` 작업 인수에 사용되도록 사용자 지정 공급자를 컬렉션의 시작 부분에 추가합니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-174">In this example, the custom provider is added to the beginning of the collection to ensure it's used for `Author` action arguments.</span></span>

[!code-csharp[](custom-model-binding/sample/CustomModelBindingSample/Startup.cs?name=callout&highlight=5-9)]

### <a name="polymorphic-model-binding"></a><span data-ttu-id="baa53-175">다형 모델 바인딩</span><span class="sxs-lookup"><span data-stu-id="baa53-175">Polymorphic model binding</span></span>

<span data-ttu-id="baa53-176">파생 형식의 다른 모델에 바인딩하는 것을 다형 모델 바인딩이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-176">Binding to different models of derived types is known as polymorphic model binding.</span></span> <span data-ttu-id="baa53-177">요청 값을 특정 파생 모델 형식에 바인딩해야 하는 경우 사용자 지정 모델 바인딩이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-177">Custom model binding is required when the request value must be bound to the specific derived model type.</span></span> <span data-ttu-id="baa53-178">이 방법이 필요하지 않은 한 다형 모델 바인딩은 하지 않는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-178">Unless this approach is required, we recommend avoiding polymorphic model binding.</span></span> <span data-ttu-id="baa53-179">다형 모델 바인딩을 사용하면 바인딩된 모델에 대해 추론하기 어려워집니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-179">Polymorphic model binding makes it difficult to reason about the bound models.</span></span> <span data-ttu-id="baa53-180">그러나 앱에 다형 모델 바인딩이 필요한 경우 구현은 다음 코드와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-180">However, if an app requires polymorphic model binding, an implementation might look like the following code:</span></span>

<span data-ttu-id="baa53-181">파생 형식의 다른 모델에 바인딩하는 것을 다형 모델 바인딩이라고 합니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-181">Binding to different models of derived types is known as polymorphic model binding.</span></span> <span data-ttu-id="baa53-182">요청 값을 특정 파생 모델 형식에 바인딩해야 하는 경우 사용자 지정 모델 바인딩이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-182">Custom model binding is required when the request value must be bound to the specific derived model type.</span></span> <span data-ttu-id="baa53-183">다형 모델 바인딩에는 다음과 같은 특성이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-183">Polymorphic model binding:</span></span>

* <span data-ttu-id="baa53-184">모든 언어와 상호 운용할 수 있도록 설계된 REST API에는 일반적이지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-184">Isn't typical for a REST API that's designed to interoperate with all languages.</span></span>
* <span data-ttu-id="baa53-185">바인딩된 모델에 대해 추론하기 어려워집니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-185">Makes it difficult to reason about the bound models.</span></span>

<span data-ttu-id="baa53-186">그러나 앱에 다형 모델 바인딩이 필요한 경우 구현은 다음 코드와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-186">However, if an app requires polymorphic model binding, an implementation might look like the following code:</span></span>

[!code-csharp[](custom-model-binding/3.0sample/PolymorphicModelBinding/ModelBinders/PolymorphicModelBinder.cs?name=snippet)]

## <a name="recommendations-and-best-practices"></a><span data-ttu-id="baa53-187">권장 사항 및 모범 사례</span><span class="sxs-lookup"><span data-stu-id="baa53-187">Recommendations and best practices</span></span>

<span data-ttu-id="baa53-188">사용자 지정 모델 바인더:</span><span class="sxs-lookup"><span data-stu-id="baa53-188">Custom model binders:</span></span>

- <span data-ttu-id="baa53-189">상태 코드를 설정하거나 결과를 반환하려고 시도하면 안 됩니다(예를 들어 404 찾을 수 없음).</span><span class="sxs-lookup"><span data-stu-id="baa53-189">Shouldn't attempt to set status codes or return results (for example, 404 Not Found).</span></span> <span data-ttu-id="baa53-190">모델 바인딩이 실패하면 [작업 필터](xref:mvc/controllers/filters) 또는 작업 메서드 자체의 논리에서 오류를 처리해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-190">If model binding fails, an [action filter](xref:mvc/controllers/filters) or logic within the action method itself should handle the failure.</span></span>
- <span data-ttu-id="baa53-191">작업 메서드에서 반복 코드와 교차 편집 문제를 제거하는 데 가장 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-191">Are most useful for eliminating repetitive code and cross-cutting concerns from action methods.</span></span>
- <span data-ttu-id="baa53-192">일반적으로 문자열을 사용자 지정 형식으로 변환하는 데 사용되지 않습니다. [`TypeConverter`](/dotnet/api/system.componentmodel.typeconverter)가 더 좋은 옵션입니다.</span><span class="sxs-lookup"><span data-stu-id="baa53-192">Typically shouldn't be used to convert a string into a custom type, a [`TypeConverter`](/dotnet/api/system.componentmodel.typeconverter) is usually a better option.</span></span>
