---
title: ASP.NET Core에서 파일 업로드
author: ardalis
description: 모델 바인딩 및 스트리밍을 사용하여 ASP.NET Core MVC에서 파일을 업로드하는 방법입니다.
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
uid: mvc/models/file-uploads
ms.openlocfilehash: 3db2e08acc1552957f28c7015f9a75af4a57fdcd
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65084857"
---
# <a name="file-uploads-in-aspnet-core"></a>ASP.NET Core에서 파일 업로드

작성자: [Steve Smith](https://ardalis.com/)

ASP.NET MVC 작업은 작은 파일에 대해서는 단순 모델 바인딩을, 큰 파일에 대해서는 스트리밍을 사용하여 하나 이상의 파일 업로딩을 지원합니다.

[GitHub에서 예제 보기 또는 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/sample/FileUploadSample)

## <a name="uploading-small-files-with-model-binding"></a>모델 바인딩을 사용하여 작은 파일 업로딩

작은 파일을 업로드하기 위해서는 다중 파트 HTML 양식을 사용하거나 JavaScript를 사용하여 POST 요청을 구성할 수 있습니다. 여러 개의 업로드된 파일을 지원하는 Razor를 사용한 예제 양식은 다음과 같습니다.

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

파일 업로드를 지원하려면 HTML 양식에 `multipart/form-data`의 `enctype`을 지정해야 합니다. 위에 표시된 `files` 입력 요소에서는 다중 파일 업로드를 지원합니다. 이 입력 요소에서 `multiple` 특성을 생략하면 하나의 파일만 업로드할 수 있습니다. 위의 태그는 브라우저에서 다음과 같이 렌더링됩니다.

![파일 업로드 양식](file-uploads/_static/upload-form.png)

서버에 업로드된 개별 파일은 [IFormFile](/dotnet/api/microsoft.aspnetcore.http.iformfile) 인터페이스를 사용하여 [모델 바인딩](xref:mvc/models/model-binding)을 통해 액세스할 수 있습니다. `IFormFile`은 다음과 같은 구조를 갖고 있습니다.

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
> 유효성 검사없이 `FileName` 속성을 의존하거나 신뢰하지 마세요. `FileName` 속성은 표시 목적으로만 사용해야 합니다.

모델 바인딩 및 `IFormFile` 인터페이스를 사용하여 파일을 업로드할 경우, 작업 메서드는 단일 `IFormFile` 또는 여러 파일을 나타내는 `IEnumerable<IFormFile>`(또는 `List<IFormFile>`)을 받을 수 있습니다. 다음 예제는 하나 이상의 업로드된 파일을 반복하여 로컬 파일 시스템에 저장하고 업로드된 파일의 총 수와 크기를 반환합니다.

[!INCLUDE [GetTempFileName](../../includes/GetTempFileName.md)]

[!code-csharp[](file-uploads/sample/FileUploadSample/Controllers/UploadFilesController.cs?name=snippet1)]

`IFormFile` 기술을 사용하여 업로드된 파일은 처리되기 전에 웹 서버의 메모리나 디스크에 버퍼링됩니다. 작업 메서드 내부에서 `IFormFile` 내용을 스트림으로 액세스할 수 있습니다. 로컬 파일 시스템 외에도 파일을 [Azure Blob 스토리지](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs) 또는 [Entity Framework](/ef/core/index)에 스트리밍할 수 있습니다.

Entity Framework를 사용하여 데이터베이스에 이진 파일 데이터를 저장하려면 엔터티에서 `byte[]` 형식의 속성을 정의합니다.

```csharp
public class ApplicationUser : IdentityUser
{
    public byte[] AvatarImage { get; set; }
}
```

`IFormFile` 형식의 ViewModel 속성을 지정합니다.

```csharp
public class RegisterViewModel
{
    // other properties omitted

    public IFormFile AvatarImage { get; set; }
}
```

> [!NOTE]
> 위에서 볼 수 있는 것처럼 `IFormFile`을 작업 메서드의 매개 변수 또는 ViewModel 속성으로 직접 사용할 수 있습니다.

`IFormFile`을 스트림에 복사하고 바이트 배열로 저장합니다.

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
> 관계형 데이터베이스에 이진 데이터를 저장할 경우 성능에 나쁜 영향을 줄 수 있으므로 주의하세요.

## <a name="uploading-large-files-with-streaming"></a>스트리밍을 사용하여 큰 파일 업로딩

파일 업로드의 크기 또는 빈도로 인해 앱에 리소스 문제가 발생하는 경우, 위에서 살펴본 모델 바인딩 접근 방식처럼 전체를 버퍼링하는 대신 파일 업로드를 스트리밍하는 것이 좋습니다. `IFormFile` 및 모델 바인딩을 사용하는 방법이 훨씬 간단한 솔루션인 반면, 스트리밍을 제대로 구현하려면 여러 단계를 거쳐야 합니다.

> [!NOTE]
> 버퍼링된 단일 파일이 64KB를 초과하면 이 파일은 RAM에서 서버의 디스크에 있는 임시 파일로 이동됩니다. 파일 업로드에서 사용되는 리소스(디스크, RAM)는 동시 파일 업로드 크기와 수에 따라 달라집니다. 스트리밍은 성능에 관한 것이 아니라 규모에 관한 것입니다. 너무 많은 업로드를 버퍼링하려고 하면 메모리 또는 디스크 공간이 부족할 경우 사이트가 중단됩니다.

다음 예제는 JavaScript/Angular를 사용하여 컨트롤러 작업에 스트리밍하는 것을 보여 줍니다. 사용자 지정 필터 특성을 사용하여 파일의 위조 방지 토큰을 생성하고 요청 본문 대신 HTTP 헤더로 전달합니다. 작업 메서드에서 업로드된 데이터를 직접 처리하므로 모델 바인딩은 다른 필터에 의해 비활성됩니다. 작업 내에서 양식의 콘텐츠는 각 개별 `MultipartSection`을 읽고 적절하게 파일을 처리하거나 콘텐츠를 저장하는 `MultipartReader`를 사용하여 읽습니다. 모든 섹션을 읽은 후 작업에서 자체 모델 바인딩을 수행합니다.

초기 작업은 양식을 로드하고 위조 방지 토큰을 쿠키에 저장합니다(`GenerateAntiforgeryTokenCookieForAjax` 특성을 통해).

```csharp
[HttpGet]
[GenerateAntiforgeryTokenCookieForAjax]
public IActionResult Index()
{
    return View();
}
```

이 특성은 ASP.NET Core의 기본 제공 [위조 방지](xref:security/anti-request-forgery) 지원을 사용하여 요청 토큰으로 쿠키를 설정합니다.

[!code-csharp[](file-uploads/sample/FileUploadSample/Filters/GenerateAntiforgeryTokenCookieForAjaxAttribute.cs?name=snippet1)]

Angular는 자동으로 `X-XSRF-TOKEN`으로 명명된 요청 헤더에 위조 방지 토큰을 전달합니다. ASP.NET Core MVC 앱은 *Startup.cs*의 구성에서 이 헤더를 참조하도록 구성됩니다.

[!code-csharp[](file-uploads/sample/FileUploadSample/Startup.cs?name=snippet1)]

아래 표시된 `DisableFormValueModelBinding` 특성은 `Upload` 작업 메서드에 대해 모델 바인딩을 사용하지 않도록 설정하는 데 사용됩니다.

[!code-csharp[](file-uploads/sample/FileUploadSample/Filters/DisableFormValueModelBindingAttribute.cs?name=snippet1)]

모델 바인딩이 사용되지 않으므로 `Upload` 작업 메서드는 매개 변수를 받지 않습니다. `ControllerBase`의 `Request` 속성을 사용하여 직접 작동합니다. `MultipartReader`는 각 섹션을 읽는 데 사용됩니다. 파일은 GUID 파일 이름으로 저장되고 키/값 데이터는 `KeyValueAccumulator`에 저장됩니다. 모든 섹션을 읽었으면 `KeyValueAccumulator`의 내용이 양식 데이터를 모델 형식으로 바인딩하는 데 사용됩니다.

전체 `Upload` 메서드는 다음과 같습니다.

[!INCLUDE [GetTempFileName](../../includes/GetTempFileName.md)]

[!code-csharp[](file-uploads/sample/FileUploadSample/Controllers/StreamingController.cs?name=snippet1)]

## <a name="troubleshooting"></a>문제 해결

다음은 파일 업로드 및 가능한 솔루션을 사용하여 작업할 때 자주 발생하는 몇 가지 일반적인 문제입니다.

### <a name="unexpected-not-found-error-with-iis"></a>IIS에서 예기치 않은 찾을 수 없음 오류

다음 오류는 파일 업로드가 서버에 구성된 `maxAllowedContentLength`를 초과했음을 나타냅니다.

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

기본 설정은 `30000000`이며, 약 28.6MB입니다. *web.config*를 편집하여 값을 사용자 지정할 수 있습니다.

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

이 설정은 IIS에만 적용됩니다. Kestrel에서 호스팅하는 경우 기본적으로 이 동작은 발생하지 않습니다. 자세한 내용은 [요청 제한 \<requestLimits\>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)을 참조하세요.

### <a name="null-reference-exception-with-iformfile"></a>IFormFile 사용 시 Null 참조 예외

컨트롤러가 `IFormFile`을 사용하여 업로드된 파일을 수락하지만 항상 값이 null인 경우, HTML 양식이 `enctype` 값으로 `multipart/form-data`를 지정하고 있는지 확인합니다. `<form>` 요소에 이 특성이 설정되어 있지 않은 경우 파일 업로드가 발생하지 않고 모든 바인딩된 `IFormFile` 인수는 null이 됩니다.
