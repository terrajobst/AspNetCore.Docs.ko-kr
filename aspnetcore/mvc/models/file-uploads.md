---
title: ASP.NET Core에서 파일 업로드
author: ardalis
description: 모델 바인딩 및 스트리밍을 사용하여 ASP.NET Core MVC에서 파일을 업로드하는 방법입니다.
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
uid: mvc/models/file-uploads
ms.openlocfilehash: 831f0c84f0ff062e9e24ccbf4ca81b7143c66e66
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59468555"
---
# <a name="file-uploads-in-aspnet-core"></a><span data-ttu-id="ea3ad-103">ASP.NET Core에서 파일 업로드</span><span class="sxs-lookup"><span data-stu-id="ea3ad-103">File uploads in ASP.NET Core</span></span>

<span data-ttu-id="ea3ad-104">작성자: [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="ea3ad-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="ea3ad-105">ASP.NET MVC 작업은 소규모 파일에 대해서는 단순 모델 바인딩을, 대규모 파일에 대해서는 스트리밍을 사용하여 하나 이상의 파일 업로딩을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-105">ASP.NET MVC actions support uploading of one or more files using simple model binding for smaller files or streaming for larger files.</span></span>

[<span data-ttu-id="ea3ad-106">GitHub에서 샘플 보기 또는 다운로드</span><span class="sxs-lookup"><span data-stu-id="ea3ad-106">View or download sample from GitHub</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/models/file-uploads/sample/FileUploadSample)

## <a name="uploading-small-files-with-model-binding"></a><span data-ttu-id="ea3ad-107">모델 바인딩을 사용하여 작은 파일 업로딩</span><span class="sxs-lookup"><span data-stu-id="ea3ad-107">Uploading small files with model binding</span></span>

<span data-ttu-id="ea3ad-108">작은 파일을 업로드하기 위해 다중 파트 HTML 양식을 사용하거나 JavaScript를 사용하여 POST 요청을 생성할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-108">To upload small files, you can use a multi-part HTML form or construct a POST request using JavaScript.</span></span> <span data-ttu-id="ea3ad-109">다중 업로드된 파일을 지원하고 Razor를 사용하는 양식 예제가 아래에 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-109">An example form using Razor, which supports multiple uploaded files, is shown below:</span></span>

```html
<form method="post" enctype="multipart/form-data" asp-controller="UploadFiles" asp-action="Index">
    <div class="form-group">
        <div class="col-md-10">
            <p>Upload one or more files using this form:</p>
            <input type="file" name="files" multiple>
        </div>
    </div>
    <div class="form-group">
        <div class="col-md-10">
            <input type="submit" value="Upload">
        </div>
    </div>
</form>
```

<span data-ttu-id="ea3ad-110">파일 업로드를 지원하려면 HTML 양식에서 `multipart/form-data`의 `enctype`을 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-110">In order to support file uploads, HTML forms must specify an `enctype` of `multipart/form-data`.</span></span> <span data-ttu-id="ea3ad-111">위에 표시된 `files` 입력 요소에서는 다중 파일 업로드를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-111">The `files` input element shown above supports uploading multiple files.</span></span> <span data-ttu-id="ea3ad-112">이 입력 요소에서 `multiple` 특성을 생략하면 하나의 파일만 업로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-112">Omit the `multiple` attribute on this input element to allow just a single file to be uploaded.</span></span> <span data-ttu-id="ea3ad-113">위의 태그는 브라우저에서 다음과 같이 렌더링합니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-113">The above markup renders in a browser as:</span></span>

![파일 업로드 양식](file-uploads/_static/upload-form.png)

<span data-ttu-id="ea3ad-115">서버에 업로드된 개별 파일은 [IFormFile](/dotnet/api/microsoft.aspnetcore.http.iformfile) 인터페이스를 사용하여 [모델 바인딩](xref:mvc/models/model-binding)을 통해 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-115">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using the [IFormFile](/dotnet/api/microsoft.aspnetcore.http.iformfile) interface.</span></span> <span data-ttu-id="ea3ad-116">`IFormFile`에는 다음 구조체가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-116">`IFormFile` has the following structure:</span></span>

```csharp
public interface IFormFile
{
    string ContentType { get; }
    string ContentDisposition { get; }
    IHeaderDictionary Headers { get; }
    long Length { get; }
    string Name { get; }
    string FileName { get; }
    Stream OpenReadStream();
    void CopyTo(Stream target);
    Task CopyToAsync(Stream target, CancellationToken cancellationToken = null);
}
```

> [!WARNING]
> <span data-ttu-id="ea3ad-117">유효성 검사없이 `FileName` 속성을 의존하거나 신뢰하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-117">Don't rely on or trust the `FileName` property without validation.</span></span> <span data-ttu-id="ea3ad-118">`FileName` 속성은 표시 목적으로만 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-118">The `FileName` property should only be used for display purposes.</span></span>

<span data-ttu-id="ea3ad-119">모델 바인딩 및 `IFormFile` 인터페이스를 사용하여 파일을 업로드할 경우, 작업 메서드는 단일 `IFormFile` 또는 여러 파일을 나타내는 `IEnumerable<IFormFile>`(또는 `List<IFormFile>`)을 받아들일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-119">When uploading files using model binding and the `IFormFile` interface, the action method can accept either a single `IFormFile` or an `IEnumerable<IFormFile>` (or `List<IFormFile>`) representing several files.</span></span> <span data-ttu-id="ea3ad-120">다음 예제에서는 하나 이상의 업로드된 파일을 반복하여 로컬 파일 시스템에 저장하고 업로드된 파일의 총 수와 크기를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-120">The following example loops through one or more uploaded files, saves them to the local file system, and returns the total number and size of files uploaded.</span></span>

[!INCLUDE [GetTempFileName](../../includes/GetTempFileName.md)]

[!code-csharp[](file-uploads/sample/FileUploadSample/Controllers/UploadFilesController.cs?name=snippet1)]

<span data-ttu-id="ea3ad-121">`IFormFile` 기술을 사용하여 업로드된 파일은 처리되기 전에 웹 서버의 메모리나 디스크에 버퍼링됩니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-121">Files uploaded using the `IFormFile` technique are buffered in memory or on disk on the web server before being processed.</span></span> <span data-ttu-id="ea3ad-122">작업 메서드 내부에서 `IFormFile` 내용을 스트림으로 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-122">Inside the action method, the `IFormFile` contents are accessible as a stream.</span></span> <span data-ttu-id="ea3ad-123">로컬 파일 시스템 외에도 파일을 [Azure Blob 스토리지](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs) 또는 [Entity Framework](/ef/core/index)에 스트리밍할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-123">In addition to the local file system, files can be streamed to [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs) or [Entity Framework](/ef/core/index).</span></span>

<span data-ttu-id="ea3ad-124">Entity Framework를 사용하여 데이터베이스에 이진 파일 데이터를 저장하려면 엔터티에서 `byte[]` 형식의 속성을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-124">To store binary file data in a database using Entity Framework, define a property of type `byte[]` on the entity:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public byte[] AvatarImage { get; set; }
}
```

<span data-ttu-id="ea3ad-125">`IFormFile` 형식의 viewmodel 속성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-125">Specify a viewmodel property of type `IFormFile`:</span></span>

```csharp
public class RegisterViewModel
{
    // other properties omitted

    public IFormFile AvatarImage { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="ea3ad-126">위에 표시된 것처럼 `IFormFile`을 작업 메서드 매개 변수 또는 viewmodel 속성으로 직접 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-126">`IFormFile` can be used directly as an action method parameter or as a viewmodel property, as shown above.</span></span>

<span data-ttu-id="ea3ad-127">`IFormFile`을 스트림에 복사하고 바이트 배열로 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-127">Copy the `IFormFile` to a stream and save it to the byte array:</span></span>

```csharp
// POST: /Account/Register
[HttpPost]
[AllowAnonymous]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Register(RegisterViewModel model)
{
    ViewData["ReturnUrl"] = returnUrl;
    if  (ModelState.IsValid)
    {
        var user = new ApplicationUser 
        {
            UserName = model.Email,
            Email = model.Email
        };
        using (var memoryStream = new MemoryStream())
        {
            await model.AvatarImage.CopyToAsync(memoryStream);
            user.AvatarImage = memoryStream.ToArray();
        }
    // additional logic omitted

    // Don't rely on or trust the model.AvatarImage.FileName property 
    // without validation.
}
```

> [!NOTE]
> <span data-ttu-id="ea3ad-128">관계형 데이터베이스에 이진 데이터를 저장하는 경우 성능에 나쁜 영향을 줄 수 있으므로 주의하세요.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-128">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>

## <a name="uploading-large-files-with-streaming"></a><span data-ttu-id="ea3ad-129">스트리밍을 사용하여 큰 파일 업로딩</span><span class="sxs-lookup"><span data-stu-id="ea3ad-129">Uploading large files with streaming</span></span>

<span data-ttu-id="ea3ad-130">파일 업로드의 크기 또는 빈도로 인해 앱에 대한 리소스 문제가 발생하는 경우 위에 표시된 모델 바인딩 접근 방식과 마찬가지로 전체를 버퍼링하는 대신 파일 업로드를 스트리밍하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-130">If the size or frequency of file uploads is causing resource problems for the app, consider streaming the file upload rather than buffering it in its entirety, as the model binding approach shown above does.</span></span> <span data-ttu-id="ea3ad-131">`IFormFile`을 사용하고 모델 바인딩이 훨씬 간단한 솔루션이지만 스트리밍을 제대로 구현하려면 여러 단계를 거쳐야 합니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-131">While using `IFormFile` and model binding is a much simpler solution, streaming requires a number of steps to implement properly.</span></span>

> [!NOTE]
> <span data-ttu-id="ea3ad-132">버퍼링된 단일 파일이 64KB를 초과하면 이 파일은 RAM에서 서버의 디스크에 있는 임시 파일로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-132">Any single buffered file exceeding 64KB will be moved from RAM to a temp file on disk on the server.</span></span> <span data-ttu-id="ea3ad-133">파일 업로드에서 사용되는 리소스(디스크, RAM)는 동시 파일 업로드 크기와 수에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-133">The resources (disk, RAM) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="ea3ad-134">스트리밍은 성능에 관한 것이 아니라 규모에 관한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-134">Streaming isn't so much about perf, it's about scale.</span></span> <span data-ttu-id="ea3ad-135">너무 많은 업로드를 버퍼링하려고 하면 메모리 또는 디스크 공간이 부족할 때 사이트가 중단됩니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-135">If you try to buffer too many uploads, your site will crash when it runs out of memory or disk space.</span></span>

<span data-ttu-id="ea3ad-136">다음 예제에서는 JavaScript/Angular를 사용하여 컨트롤러 작업에 스트리밍하는 것을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-136">The following example demonstrates using JavaScript/Angular to stream to a controller action.</span></span> <span data-ttu-id="ea3ad-137">사용자 지정 필터 특성을 사용하여 파일의 위조 방지 토큰이 생성되고 요청 본문 대신 HTTP 헤더에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-137">The file's antiforgery token is generated using a custom filter attribute and passed in HTTP headers instead of in the request body.</span></span> <span data-ttu-id="ea3ad-138">작업 메서드에서 업로드된 데이터를 직접 처리하므로 다른 필터에서 모델 바인딩을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-138">Because the action method processes the uploaded data directly, model binding is disabled by another filter.</span></span> <span data-ttu-id="ea3ad-139">작업 내에서 양식의 콘텐츠는 각 개별 `MultipartSection`을 읽고 적절하게 파일을 처리하거나 콘텐츠를 저장하는 `MultipartReader`를 사용하여 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-139">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="ea3ad-140">모든 섹션을 읽은 후 작업에서 자체 모델 바인딩을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-140">Once all sections have been read, the action performs its own model binding.</span></span>

<span data-ttu-id="ea3ad-141">초기 작업에서는 양식을 로드하고 위조 방지 토큰을 쿠키에 저장합니다(`GenerateAntiforgeryTokenCookieForAjax` 특성을 통해).</span><span class="sxs-lookup"><span data-stu-id="ea3ad-141">The initial action loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieForAjax` attribute):</span></span>

```csharp
[HttpGet]
[GenerateAntiforgeryTokenCookieForAjax]
public IActionResult Index()
{
    return View();
}
```

<span data-ttu-id="ea3ad-142">이 특성은 ASP.NET Core의 기본 제공 [위조 방지](xref:security/anti-request-forgery) 지원을 사용하여 요청 토큰으로 쿠키를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-142">The attribute uses ASP.NET Core's built-in [Antiforgery](xref:security/anti-request-forgery) support to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/sample/FileUploadSample/Filters/GenerateAntiforgeryTokenCookieForAjaxAttribute.cs?name=snippet1)]

<span data-ttu-id="ea3ad-143">Angular는 `X-XSRF-TOKEN`으로 명명된 요청 헤더에서 위조 방지 토큰을 자동으로 전달합니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-143">Angular automatically passes an antiforgery token in a request header named `X-XSRF-TOKEN`.</span></span> <span data-ttu-id="ea3ad-144">*Startup.cs*의 해당 구성에서 이 헤더를 참조하도록 ASP.NET Core MVC 앱이 구성됩니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-144">The ASP.NET Core MVC app is configured to refer to this header in its configuration in *Startup.cs*:</span></span>

[!code-csharp[](file-uploads/sample/FileUploadSample/Startup.cs?name=snippet1)]

<span data-ttu-id="ea3ad-145">아래 표시된 `DisableFormValueModelBinding` 특성은 `Upload` 작업 메서드에 대한 모델 바인딩을 사용하지 않도록 설정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-145">The `DisableFormValueModelBinding` attribute, shown below, is used to disable model binding for the `Upload` action method.</span></span>

[!code-csharp[](file-uploads/sample/FileUploadSample/Filters/DisableFormValueModelBindingAttribute.cs?name=snippet1)]

<span data-ttu-id="ea3ad-146">모델 바인딩이 사용되지 않으므로 `Upload` 작업 메서드는 매개 변수를 받아들이지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-146">Since model binding is disabled, the `Upload` action method doesn't accept parameters.</span></span> <span data-ttu-id="ea3ad-147">`ControllerBase`의 `Request` 속성으로 직접 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-147">It works directly with the `Request` property of `ControllerBase`.</span></span> <span data-ttu-id="ea3ad-148">`MultipartReader`는 각 섹션을 읽는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-148">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="ea3ad-149">파일이 GUID 파일 이름으로 저장되고 키/값 데이터가 `KeyValueAccumulator`에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-149">The file is saved with a GUID filename and the key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="ea3ad-150">모든 섹션을 읽었으면 `KeyValueAccumulator`의 내용이 양식 데이터를 모델 형식으로 바인딩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-150">Once all sections have been read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="ea3ad-151">전체 `Upload` 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-151">The complete `Upload` method is shown below:</span></span>

[!INCLUDE [GetTempFileName](../../includes/GetTempFileName.md)]

[!code-csharp[](file-uploads/sample/FileUploadSample/Controllers/StreamingController.cs?name=snippet1)]

## <a name="troubleshooting"></a><span data-ttu-id="ea3ad-152">문제 해결</span><span class="sxs-lookup"><span data-stu-id="ea3ad-152">Troubleshooting</span></span>

<span data-ttu-id="ea3ad-153">다음은 파일 업로드 및 가능한 솔루션을 사용하여 작업할 때 자주 발생하는 몇 가지 일반적인 문제입니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-153">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="unexpected-not-found-error-with-iis"></a><span data-ttu-id="ea3ad-154">IIS에서 예기치 않은 찾을 수 없음 오류</span><span class="sxs-lookup"><span data-stu-id="ea3ad-154">Unexpected Not Found error with IIS</span></span>

<span data-ttu-id="ea3ad-155">다음 오류는 파일 업로드가 서버에 구성된 `maxAllowedContentLength`를 초과했음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-155">The following error indicates your file upload exceeds the server's configured `maxAllowedContentLength`:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="ea3ad-156">기본 설정은 `30000000`이며, 약 28.6MB입니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-156">The default setting is `30000000`, which is approximately 28.6MB.</span></span> <span data-ttu-id="ea3ad-157">*web.config*를 편집하여 값을 사용자 지정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-157">The value can be customized by editing *web.config*:</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- This will handle requests up to 50MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="ea3ad-158">이 설정은 IIS에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-158">This setting only applies to IIS.</span></span> <span data-ttu-id="ea3ad-159">Kestrel에서 호스팅하는 경우 기본적으로 동작이 발생하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-159">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="ea3ad-160">자세한 내용은 [요청 제한 \<requestLimits\>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-160">For more information, see [Request Limits \<requestLimits\>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="ea3ad-161">IFormFile을 사용한 Null 참조 예외</span><span class="sxs-lookup"><span data-stu-id="ea3ad-161">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="ea3ad-162">컨트롤러가 `IFormFile`을 사용하여 업로드된 파일을 수락하고 있지만 값이 항상 null인 경우 HTML 양식에서 `enctype` 값을 `multipart/form-data`로 지정하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-162">If your controller is accepting uploaded files using `IFormFile` but you find that the value is always null, confirm that your HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="ea3ad-163">`<form>` 요소에 이 특성이 설정되어 있지 않은 경우 파일 업로드가 발생하지 않고 모든 바인딩된 `IFormFile` 인수는 null이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="ea3ad-163">If this attribute isn't set on the `<form>` element, the file upload won't occur and any bound `IFormFile` arguments will be null.</span></span>
