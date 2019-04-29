---
title: ASP.NET Core의 Razor 페이지에 파일 업로드
author: guardrex
description: FileUpload 클래스를 사용 하 여 ASP.NET Core에서 Razor 페이지에 파일을 업로드 하는 방법에 알아봅니다.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 11/10/2018
ms.custom: mvc, seodec18
uid: razor-pages/upload-files
ms.openlocfilehash: a12653e5dc99ad731de63e151af6dbdbe38ffefc
ms.sourcegitcommit: 8a84ce880b4c40d6694ba6423038f18fc2eb5746
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60165224"
---
# <a name="upload-files-to-a-razor-page-in-aspnet-core"></a>ASP.NET Core의 Razor 페이지에 파일 업로드

[Luke Latham](https://github.com/guardrex)으로

이 항목을 기반으로 합니다 [샘플 앱](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample) 에서 <xref:tutorials/razor-pages/razor-pages-start>합니다.

이 항목에서는 작은 파일 업로딩에 대해 제대로 작동 하는 단순 모델 바인딩을 사용 하 여 파일을 업로드 하는 방법을 보여 줍니다. 큰 파일 스트리밍에 대한 자세한 내용은 [스트리밍으로 큰 파일 업로드](xref:mvc/models/file-uploads#uploading-large-files-with-streaming)를 참조하세요.

다음 단계에서는 동영상 일정 파일 업로드 기능이 샘플 앱에 추가됩니다. 동영상 일정은 `Schedule` 클래스로 표현됩니다. 클래스에는 일정의 두 버전이 포함됩니다. 한 버전(`PublicSchedule`)은 고객에게 제공됩니다. 다른 버전(`PrivateSchedule`)은 회사 직원들이 사용합니다. 각 버전은 별도 파일로 업로드됩니다. 이 자습서에서는 서버에 단일 게시물이 있는 페이지에서 두 개의 파일 업로드를 수행하는 방법을 보여 줍니다.

[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-pages/upload-files/samples) ([다운로드 방법](xref:index#how-to-download-a-sample))

## <a name="security-considerations"></a>보안 고려 사항

사용자에게 서버에 파일을 업로드하는 기능을 제공할 때는 주의해야 합니다. 공격자는 시스템에서 [서비스 거부](/windows-hardware/drivers/ifs/denial-of-service) 및 기타 공격을 실행할 수 있습니다. 공격이 성공할 가능성을 줄이는 몇 가지 보안 단계는 다음과 같습니다.

* 시스템의 전용 파일 업로드 영역에 파일을 업로드합니다. 그러면 업로드된 콘텐츠에 보안 조치를 더 쉽게 적용할 수 있습니다. 파일 업로드를 허용할 때 업로드 위치에 실행 권한이 사용하지 않도록 설정되어 있는지 확인합니다.
* 사용자 입력이나 업로드된 파일의 파일 이름이 아니라 앱에서 결정한 안전한 파일 이름을 사용합니다.
* 특정 승인된 파일 확장명 집합만 허용합니다.
* 서버에서 클라이언트 쪽 검사가 수행되는지 확인합니다. 클라이언트 쪽 검사는 피하기 쉽습니다.
* 업로드 크기를 확인하고 예상보다 큰 업로드를 방지합니다.
* 업로드된 콘텐츠에 대해 바이러스/맬웨어 스캐너를 실행합니다.

> [!WARNING]
> 시스템에 악성 코드를 업로드하는 행위는 흔히 다음을 수행할 수 있는 코드를 실행하기 위한 첫 단계가 됩니다.
> * 시스템을 완전히 장악합니다.
> * 시스템이 완전히 고장 나게 하는 결과로 시스템을 오버로드합니다.
> * 사용자 또는 시스템 데이터를 손상시킵니다.
> * 공용 인터페이스에 그래피티를 적용합니다.

## <a name="add-a-fileupload-class"></a>FileUpload 클래스 추가

파일 업로드 쌍을 처리할 Razor 페이지를 만듭니다. 일정 데이터를 가져오기 위해 페이지에 바인딩되는 `FileUpload` 클래스를 추가합니다. *Models* 폴더를 마우스 오른쪽 단추로 클릭합니다. **추가** > **클래스**를 선택합니다. 클래스 이름을 **FileUpload**로 지정하고 다음 속성을 추가합니다.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Models/FileUpload.cs)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Models/FileUpload.cs)]

::: moniker-end

클래스에 일정 제목에 대한 속성 및 일정의 각 두 버전에 대한 속성이 있습니다. 세 가지 속성 모두 필요하며 제목 길이는 3-60자여야 합니다.

## <a name="add-a-helper-method-to-upload-files"></a>파일을 업로드하는 도우미 메서드 추가

업로드된 일정 파일을 처리하기 위한 코드 중복을 방지하려면 먼저 정적 도우미 메서드를 추가합니다. 앱에 *유틸리티* 폴더를 만들고 다음 콘텐츠가 포함된 *FileHelpers.cs* 파일을 추가합니다. 도우미 메서드 `ProcessFormFile`은 [IFormFile](/dotnet/api/microsoft.aspnetcore.http.iformfile) 및 [ModelStateDictionary](/api/microsoft.aspnetcore.mvc.modelbinding.modelstatedictionary)를 사용하여 파일의 크기와 콘텐츠를 포함하는 문자열을 반환합니다. 콘텐츠 형식 및 길이를 확인합니다. 파일이 유효성 검사를 통과하지 못한 경우 오류가 `ModelState`에 추가됩니다.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Utilities/FileHelpers.cs)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Utilities/FileHelpers.cs)]

::: moniker-end

### <a name="save-the-file-to-disk"></a>디스크에 파일 저장

샘플 앱은 업로드된 파일을 데이터베이스 필드에 저장합니다. 디스크에 파일을 저장하려면 [FileStream](/dotnet/api/system.io.filestream)을 사용합니다. 다음 예제에서는 `FileUpload.UploadPublicSchedule`에 저장된 파일을 `OnPostAsync` 메서드의 `FileStream`으로 복사합니다. `FileStream`은 제공된 `<PATH-AND-FILE-NAME>`의 디스크에 파일을 씁니다.

```csharp
public async Task<IActionResult> OnPostAsync()
{
    // Perform an initial check to catch FileUpload class attribute violations.
    if (!ModelState.IsValid)
    {
        return Page();
    }

    var filePath = "<PATH-AND-FILE-NAME>";

    using (var fileStream = new FileStream(filePath, FileMode.Create))
    {
        await FileUpload.UploadPublicSchedule.CopyToAsync(fileStream);
    }

    return RedirectToPage("./Index");
}
```

작업자 프로세스는 `filePath`로 지정된 위치에 대한 쓰기 권한이 있어야 합니다.

> [!NOTE]
> `filePath`에는 *파일 이름*이 포함되어야 합니다. 파일 이름이 제공되지 않는 경우 런타임 시 [UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception)이 throw됩니다.

> [!WARNING]
> 업로드된 파일은 앱과 동일한 디렉터리 트리에 보관하지 마세요.
>
> 코드 샘플에서는 악성 파일 업로드에 대한 서버 쪽 보호 기능을 제공하지 않습니다. 사용자의 파일을 수락할 때 공격 노출 영역을 줄이는 방법에 대한 자세한 내용은 다음 리소스를 참조하세요.
>
> * [무제한 파일 업로드](https://www.owasp.org/index.php/Unrestricted_File_Upload)
> * [Azure 보안: 사용자의 파일을 허용할 때 적절 한 제어가 수행 되는지 확인 합니다.](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

### <a name="save-the-file-to-azure-blob-storage"></a>Azure Blob Storage에 파일 저장

Azure Blob Storage에 파일 콘텐츠를 업로드하려면 [.NET을 사용하여 Azure Blob Storage 시작](/azure/storage/blobs/storage-dotnet-how-to-use-blobs)을 참조하세요. 이 항목에서는 [UploadFromStream](/dotnet/api/microsoft.windowsazure.storage.file.cloudfile.uploadfromstreamasync)을 사용하여 Blob 스토리지에 [FileStream](/dotnet/api/system.io.filestream)을 저장하는 방법을 보여 줍니다.

## <a name="add-the-schedule-class"></a>일정 클래스 추가

*Models* 폴더를 마우스 오른쪽 단추로 클릭합니다. **추가** > **클래스**를 선택합니다. 클래스 이름을 **Schedule**로 지정하고 다음 속성을 추가합니다.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Models/Schedule.cs)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Models/Schedule.cs)]

::: moniker-end

클래스는 예약된 데이터가 렌더링될 때 친숙한 제목 및 서식 지정을 만드는 `Display` 및 `DisplayFormat` 특성을 사용합니다.

::: moniker range=">= aspnetcore-2.1"

## <a name="update-the-razorpagesmoviecontext"></a>RazorPagesMovieContext 업데이트

일정의 경우 `RazorPagesMovieContext`(*Data/RazorPagesMovieContext.cs*)에서 `DbSet`을 지정합니다.

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Data/RazorPagesMovieContext.cs?highlight=17)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

## <a name="update-the-moviecontext"></a>MovieContext 업데이트

일정의 경우 `MovieContext`(*Models/MovieContext.cs*)에서 `DbSet`을 지정합니다.

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Models/MovieContext.cs?highlight=13)]

::: moniker-end

## <a name="add-the-schedule-table-to-the-database"></a>데이터베이스에 일정 테이블 추가

패키지 관리자 콘솔 (PMC)을 엽니다. **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔**합니다.

![PMC 메뉴](upload-files/_static/pmc.png)

PMC에서 다음 명령을 실행합니다. 이러한 명령은 `Schedule` 테이블을 데이터베이스에 추가합니다.

```powershell
Add-Migration AddScheduleTable
Update-Database
```

## <a name="add-a-file-upload-razor-page"></a>파일 업로드 Razor 페이지 추가

*페이지* 폴더에서 *Schedules* 폴더를 만듭니다. *일정* 폴더에서 다음 콘텐츠를 포함한 일정을 업로드하기 위한 *Index.cshtml*이라는 페이지를 만듭니다.

::: moniker range=">= aspnetcore-2.1"

[!code-cshtml[](upload-files/samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-cshtml[](upload-files/samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml)]

::: moniker-end

각 양식 그룹에는 각 클래스 속성의 이름을 표시하는 **\<레이블>** 이 포함되어 있습니다. `FileUpload` 모델의 `Display` 특성은 레이블에 대한 표시 값을 제공합니다. 예를 들어 `UploadPublicSchedule` 속성의 표시 이름이 `[Display(Name="Public Schedule")]`로 설정되어 있으면 양식이 렌더링할 때 레이블에서 “공개 일정”을 표시합니다.

각 양식 그룹은 유효성 검사 **\<span>** 을 포함합니다. 사용자의 입력이 `FileUpload` 클래스에 설정된 속성 특성을 충족하지 못하거나 `ProcessFormFile` 메서드 파일 유효성 검사 확인에 실패하는 경우 모델 유효성 검사에 실패합니다. 모델 유효성 검사에 실패하면 유용한 유효성 검사 메시지가 사용자에게 렌더링됩니다. 예를 들어 `Title` 속성은 `[Required]` 및 `[StringLength(60, MinimumLength = 3)]`으로 주석이 추가됩니다. 사용자가 제목을 입력하지 못하면 값이 필수 항목임을 나타내는 메시지가 표시됩니다. 사용자가 3자 미만 또는 60자 이상의 값을 입력하는 경우 값의 길이가 잘못되었음을 나타내는 메시지가 표시됩니다. 콘텐츠가 없는 파일이 제공되는 경우 해당 파일이 비어 있음을 나타내는 메시지가 표시됩니다.

## <a name="add-the-page-model"></a>페이지 모델 추가

*Schedules* 폴더에 페이지 모델(*Index.cshtml.cs*)을 추가합니다.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs)]

::: moniker-end

페이지 모델(*Index.cshtml.cs*의 `IndexModel`)은 `FileUpload` 클래스를 바인딩합니다.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/snapshot_samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/snapshot_samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet1)]

::: moniker-end

또한 모델은 일정(`IList<Schedule>`)의 목록을 사용하여 데이터베이스에 저장된 일정을 페이지에 표시합니다.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/snapshot_samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet2)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/snapshot_samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet2)]

::: moniker-end

페이지가 `OnGetAsync`로 로드되면 `Schedules`은 데이터베이스에서 채워지고 로드된 일정의 HTML 테이블을 생성하는 데 사용됩니다.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/snapshot_samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet3)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/snapshot_samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet3)]

::: moniker-end

양식이 서버에 게시될 때 `ModelState`가 선택됩니다. 유효하지 않은 경우 `Schedule`이 다시 작성되고 페이지는 페이지 유효성 검사에 실패한 이유를 나타내는 하나 이상의 유효성 검사 메시지로 렌더링합니다. 유효한 경우 일정의 두 버전에 대한 파일 업로드를 완료하고 데이터를 저장하도록 새 `Schedule` 개체를 만드는 데 `FileUpload` 속성이 *OnPostAsync*에 사용됩니다. 일정이 다음 데이터베이스에 저장됩니다.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/snapshot_samples/2.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet4)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/snapshot_samples/1.x/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet4)]

::: moniker-end

## <a name="link-the-file-upload-razor-page"></a>파일 업로드 Razor 페이지 연결

*Pages/Shared/_Layout.cshtml*을 열고 일정 페이지에 도달하는 탐색 모음에 대한 링크를 추가합니다.

```cshtml
<div class="navbar-collapse collapse">
    <ul class="nav navbar-nav">
        <li><a asp-page="/Index">Home</a></li>
        <li><a asp-page="/Schedules/Index">Schedules</a></li>
        <li><a asp-page="/About">About</a></li>
        <li><a asp-page="/Contact">Contact</a></li>
    </ul>
</div>
```

## <a name="add-a-page-to-confirm-schedule-deletion"></a>일정 삭제를 확인하는 페이지 추가

사용자가 일정을 삭제하기 위해 클릭할 때 작업을 취소할 기회가 제공됩니다. 삭제 확인 페이지(*Delete.cshtml*)를 *Schedules* 폴더에 추가합니다.

::: moniker range=">= aspnetcore-2.1"

[!code-cshtml[](upload-files/samples/2.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-cshtml[](upload-files/samples/1.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml)]

::: moniker-end

페이지 모델(*Delete.cshtml.cs*)은 요청의 경로 데이터에서 `id`로 식별되는 단일 일정을 로드합니다. *Delete.cshtml.cs* 파일을 *Schedules* 폴더에 추가합니다.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/samples/2.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml.cs)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/samples/1.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml.cs)]

::: moniker-end

`OnPostAsync` 메서드는 해당 `id`에 의해 일정 삭제를 처리합니다.

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](upload-files/snapshot_samples/2.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml.cs?name=snippet1&highlight=8,12-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](upload-files/snapshot_samples/1.x/RazorPagesMovie/Pages/Schedules/Delete.cshtml.cs?name=snippet1&highlight=8,12-13)]

::: moniker-end

일정을 성공적으로 삭제한 후 `RedirectToPage`는 사용자에게 일정 *Index.cshtml* 페이지를 다시 보냅니다.

## <a name="the-working-schedules-razor-page"></a>작업 일정 Razor 페이지

페이지가 일정 제목에 대해 로드, 레이블 및 입력을 수행하면 공개 일정 및 개인 일정이 제출 단추로 렌더링됩니다.

![유효성 검사 오류가 없고 필드가 빈 초기 로드로 표시된 일정 Razor 페이지](upload-files/_static/browser1.png)

필드를 채우지 않고 **업로드** 단추를 선택하면 모델의 `[Required]` 특성을 위반하게 됩니다. `ModelState`가 잘못되었습니다. 유효성 검사 오류 메시지가 사용자에게 표시됩니다.

![유효성 검사 오류 메시지가 각 입력된 컨트롤 옆에 표시됩니다.](upload-files/_static/browser2.png)

두 개의 문자를 **제목** 필드에 입력합니다. 유효성 검사 메시지가 제목이 3-60자 사이여야 함을 나타내는 것으로 변경됩니다.

![변경된 유효성 검사 메시지](upload-files/_static/browser3.png)

하나 이상의 일정이 업로드되면 **로드된 일정** 섹션이 로드된 일정을 렌더링합니다.

![각 일정의 제목, 로드된 날짜(UTC), 공용 버전 파일 크기 및 전용 버전을 표시하는 로드된 일정의 테이블](upload-files/_static/browser4.png)

사용자가 여기서 **삭제** 링크를 클릭하면 삭제 작업을 확인하거나 취소할 수 있는 기회가 있는 삭제 확인 보기로 이동할 수 있습니다.

## <a name="troubleshooting"></a>문제 해결

문제 해결 정보는 `IFormFile` 참조에 업로드 [ASP.NET Core에서 파일 업로드: 문제 해결](xref:mvc/models/file-uploads#troubleshooting)합니다.
