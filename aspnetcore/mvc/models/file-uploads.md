---
title: ASP.NET Core에서 파일 업로드
author: guardrex
description: 모델 바인딩 및 스트리밍을 사용하여 ASP.NET Core MVC에서 파일을 업로드하는 방법입니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/31/2019
uid: mvc/models/file-uploads
ms.openlocfilehash: 04e7533aa190a4875d3f66e8665fec16abec48b3
ms.sourcegitcommit: 9e85c2562df5e108d7933635c830297f484bb775
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73462947"
---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="7565d-103">ASP.NET Core에서 파일 업로드</span><span class="sxs-lookup"><span data-stu-id="7565d-103">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="7565d-104">작성자: [Luke Latham](https://github.com/guardrex), [Steve Smith](https://ardalis.com/) 및 [Rutger Storm](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="7565d-104">By [Luke Latham](https://github.com/guardrex), [Steve Smith](https://ardalis.com/), and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7565d-105">ASP.NET Core는 소용량 파일의 경우에는 버퍼링된 모델 바인딩을 사용하여 하나 이상의 파일을 업로드하고, 대용량 파일의 경우에는 버퍼링되지 않은 스트리밍을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-105">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="7565d-106">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7565d-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="7565d-107">보안 고려 사항</span><span class="sxs-lookup"><span data-stu-id="7565d-107">Security considerations</span></span>

<span data-ttu-id="7565d-108">사용자에게 서버에 파일을 업로드하는 기능을 제공할 때는 주의해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-108">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="7565d-109">공격자는 다음을 시도할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-109">Attackers may attempt to:</span></span>

* <span data-ttu-id="7565d-110">[서비스 거부](/windows-hardware/drivers/ifs/denial-of-service) 공격을 실행.</span><span class="sxs-lookup"><span data-stu-id="7565d-110">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="7565d-111">바이러스 또는 맬웨어를 업로드.</span><span class="sxs-lookup"><span data-stu-id="7565d-111">Upload viruses or malware.</span></span>
* <span data-ttu-id="7565d-112">다른 방법으로 네트워크 및 서버를 손상.</span><span class="sxs-lookup"><span data-stu-id="7565d-112">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="7565d-113">공격이 성공할 가능성을 줄이는 보안 단계는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-113">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="7565d-114">전용 파일 업로드 영역(바람직하게는 시스템 드라이브가 아닌 위치)에 파일을 업로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-114">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="7565d-115">전용 위치를 사용하면 업로드된 파일에 대한 보안 제한을 더 쉽게 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-115">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="7565d-116">파일 업로드 위치에 대한 실행 권한을 사용하지 않도록 설정합니다.&dagger;</span><span class="sxs-lookup"><span data-stu-id="7565d-116">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="7565d-117">업로드된 파일을 앱과 동일한 디렉터리 트리에 보관하지 **마세요**.&dagger;</span><span class="sxs-lookup"><span data-stu-id="7565d-117">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="7565d-118">앱에 의해 결정된 안전한 파일 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-118">Use a safe file name determined by the app.</span></span> <span data-ttu-id="7565d-119">사용자가 제공한 파일 이름이나 업로드된 파일의 신뢰할 수 없는 파일 이름을 사용하지 마세요.&dagger; 신뢰할 수 없는 파일 이름을 표시하려면 HTML로 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-119">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="7565d-120">파일 이름을 로깅하거나 UI에 표시하는 경우를 예로 들 수 있습니다(Razor에서 자동으로 출력을 HTML로 인코딩).</span><span class="sxs-lookup"><span data-stu-id="7565d-120">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="7565d-121">앱의 디자인 사양으로 승인된 파일 확장명만 허용합니다.&dagger;</span><span class="sxs-lookup"><span data-stu-id="7565d-121">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="7565d-122">서버에서 클라이언트 쪽 검사가 수행되는지 확인합니다.&dagger; 클라이언트 쪽 검사는 피하기 쉽습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-122">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="7565d-123">업로드된 파일의 크기를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-123">Check the size of an uploaded file.</span></span> <span data-ttu-id="7565d-124">대규모로 업로드되지 않도록 최대 크기 제한을 설정합니다.&dagger;</span><span class="sxs-lookup"><span data-stu-id="7565d-124">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="7565d-125">업로드된 파일이 같은 이름의 파일을 덮어쓰면 안 되는 경우 파일을 업로드하기 전에 데이터베이스 또는 실제 스토리지에서 파일 이름을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-125">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="7565d-126">**파일이 저장되기 전에 업로드된 콘텐츠에 대해 바이러스/맬웨어 스캐너를 실행합니다.**</span><span class="sxs-lookup"><span data-stu-id="7565d-126">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="7565d-127">&dagger;샘플 앱은 조건을 충족하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-127">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="7565d-128">시스템에 악성 코드를 업로드하는 행위는 흔히 다음을 수행할 수 있는 코드를 실행하기 위한 첫 단계가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-128">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="7565d-129">시스템을 완전히 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-129">Completely gain control of a system.</span></span>
> * <span data-ttu-id="7565d-130">시스템 작동이 중단되는 결과로 시스템을 오버로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-130">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="7565d-131">사용자 또는 시스템 데이터를 손상시킵니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-131">Compromise user or system data.</span></span>
> * <span data-ttu-id="7565d-132">공용 UI에 그래피티를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-132">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="7565d-133">사용자의 파일을 수락할 때 공격 노출 영역을 줄이는 방법에 대한 자세한 내용은 다음 리소스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7565d-133">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="7565d-134">무제한 파일 업로드</span><span class="sxs-lookup"><span data-stu-id="7565d-134">Unrestricted File Upload</span></span>](https://www.owasp.org/index.php/Unrestricted_File_Upload)
> * [<span data-ttu-id="7565d-135">Azure Security: 사용자의 파일을 허용할 때 적절한 제어가 수행되는지 확인</span><span class="sxs-lookup"><span data-stu-id="7565d-135">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="7565d-136">샘플 앱의 예제를 포함하여 보안 조치를 구현하는 방법에 대한 자세한 내용은 [유효성 검사](#validation) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7565d-136">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="7565d-137">스토리지 시나리오</span><span class="sxs-lookup"><span data-stu-id="7565d-137">Storage scenarios</span></span>

<span data-ttu-id="7565d-138">파일에 대한 일반적인 스토리지 옵션은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-138">Common storage options for files include:</span></span>

* <span data-ttu-id="7565d-139">데이터베이스</span><span class="sxs-lookup"><span data-stu-id="7565d-139">Database</span></span>

  * <span data-ttu-id="7565d-140">소용량 파일 업로드의 경우 데이터베이스는 실제 스토리지(파일 시스템 또는 네트워크 공유) 옵션보다 빠른 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-140">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="7565d-141">사용자 데이터에 대한 데이터베이스 레코드를 검색할 때 파일 콘텐츠(예: 아바타 이미지)를 동시에 제공할 수 있으므로 데이터베이스는 실제 스토리지 옵션보다 편리합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-141">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="7565d-142">데이터베이스는 데이터 스토리지 서비스를 사용하는 것보다 비용이 적게 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-142">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="7565d-143">실제 스토리지(파일 시스템 또는 네트워크 공유)</span><span class="sxs-lookup"><span data-stu-id="7565d-143">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="7565d-144">대용량 파일 업로드의 경우:</span><span class="sxs-lookup"><span data-stu-id="7565d-144">For large file uploads:</span></span>
    * <span data-ttu-id="7565d-145">데이터베이스 한도 때문에 업로드 크기가 제한될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-145">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="7565d-146">실제 스토리지는 데이터베이스 스토리지보다 경제적이지 않은 경우가 자주 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-146">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="7565d-147">실제 스토리지는 데이터 스토리지 서비스를 사용하는 것보다 비용이 적게 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-147">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="7565d-148">앱의 프로세스에는 스토리지 위치에 대한 읽기 및 쓰기 권한이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-148">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="7565d-149">**실행 권한을 부여하지 마세요.**</span><span class="sxs-lookup"><span data-stu-id="7565d-149">**Never grant execute permission.**</span></span>

* <span data-ttu-id="7565d-150">데이터 스토리지 서비스(예: [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="7565d-150">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="7565d-151">서비스는 일반적으로 단일 실패 지점에 노출되는 온-프레미스 솔루션에 비해 향상된 확장성 및 복원력을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-151">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="7565d-152">서비스는 대용량 스토리지 인프라 시나리오에서 비용이 더 저렴할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-152">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="7565d-153">자세한 내용은 [빠른 시작: .NET을 사용하여 개체 스토리지에 Blob 만들기](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="7565d-153">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="7565d-154">이 항목에서는 <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>를 보여 주지만, <xref:System.IO.Stream>에서 작업할 때 <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*>를 사용하여 <xref:System.IO.FileStream>을 Blob Storage에 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-154">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="7565d-155">파일 업로드 시나리오</span><span class="sxs-lookup"><span data-stu-id="7565d-155">File upload scenarios</span></span>

<span data-ttu-id="7565d-156">파일 업로드를 위한 일반적인 방법 두 가지는 버퍼링 및 스트리밍입니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-156">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="7565d-157">**버퍼링**</span><span class="sxs-lookup"><span data-stu-id="7565d-157">**Buffering**</span></span>

<span data-ttu-id="7565d-158">전체 파일을 파일 처리 또는 저장에 사용되는 파일의 C# 표현인 <xref:Microsoft.AspNetCore.Http.IFormFile>로 읽어 들입니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-158">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="7565d-159">파일 업로드에서 사용되는 리소스(디스크, 메모리)는 동시 파일 업로드 크기와 수에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-159">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="7565d-160">앱이 너무 많은 업로드를 버퍼링하려 할 경우 메모리 또는 디스크 공간이 부족하면 사이트의 작동이 중단됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-160">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="7565d-161">파일 업로드의 크기 또는 빈도로 인해 앱 리소스가 소진되는 경우 스트리밍을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-161">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="7565d-162">버퍼링된 단일 파일이 64KB를 초과하는 경우 메모리에서 디스크의 임시 파일로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-162">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="7565d-163">소용량 파일 버퍼링은 이 항목의 다음 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-163">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="7565d-164">실제 스토리지</span><span class="sxs-lookup"><span data-stu-id="7565d-164">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="7565d-165">데이터베이스</span><span class="sxs-lookup"><span data-stu-id="7565d-165">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="7565d-166">**스트리밍**</span><span class="sxs-lookup"><span data-stu-id="7565d-166">**Streaming**</span></span>

<span data-ttu-id="7565d-167">파일은 다중 파트 요청에서 수신되며 앱에서 직접 처리하거나 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-167">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="7565d-168">스트리밍은 성능을 크게 개선하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-168">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="7565d-169">스트리밍을 통해 파일을 업로드하면 메모리 또는 디스크 공간에 대한 요구가 줄어듭니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-169">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="7565d-170">대용량 파일 스트리밍은 [스트리밍을 사용하여 대용량 파일 업로드](#upload-large-files-with-streaming) 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-170">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="7565d-171">버퍼링된 모델 바인딩을 사용하여 소용량 파일을 실제 스토리지에 업로드</span><span class="sxs-lookup"><span data-stu-id="7565d-171">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="7565d-172">소용량 파일을 업로드하려면 다중 파트 양식을 사용하거나 JavaScript를 사용하여 POST 요청을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-172">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="7565d-173">다음 예제에서는 Razor Pages 양식을 사용하여 단일 파일(샘플 앱의 *BufferedSingleFileUploadPhysical*)을 업로드하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-173">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

<span data-ttu-id="7565d-174">다음 예제는 이전 예제와 비슷하지만 다음과 같은 차이가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-174">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="7565d-175">양식의 데이터를 제출하는 데 JavaScript([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API))가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-175">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="7565d-176">유효성 검사를 수행하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-176">There's no validation.</span></span>

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

<span data-ttu-id="7565d-177">[Fetch API](https://caniuse.com/#feat=fetch)를 지원하지 않는 클라이언트에 대해 JavaScript로 양식 POST를 수행하려면 다음 방법 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-177">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="7565d-178">Fetch Polyfill(예: [window.fetch polyfill (github/fetch)](https://github.com/github/fetch))을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-178">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="7565d-179">`XMLHttpRequest`을 사용하십시오.</span><span class="sxs-lookup"><span data-stu-id="7565d-179">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="7565d-180">예:</span><span class="sxs-lookup"><span data-stu-id="7565d-180">For example:</span></span>

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

<span data-ttu-id="7565d-181">파일 업로드를 지원하려면 HTML 양식에서 `multipart/form-data`의 인코딩 유형(`enctype`)을 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-181">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="7565d-182">`files` 입력 요소가 다중 파일 업로드를 지원하려면 `<input>` 요소에 `multiple` 특성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-182">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="7565d-183">서버에 업로드된 개별 파일은 <xref:Microsoft.AspNetCore.Http.IFormFile>을 사용하여 [모델 바인딩](xref:mvc/models/model-binding)을 통해 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-183">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="7565d-184">샘플 앱은 데이터베이스 및 실제 스토리지 시나리오에 대한 다중 버퍼링된 파일 업로드를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-184">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="7565d-185">표시 및 로깅 이외에는 <xref:Microsoft.AspNetCore.Http.IFormFile>의 `FileName` 속성을 사용하지 **마세요**.</span><span class="sxs-lookup"><span data-stu-id="7565d-185">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="7565d-186">표시하거나 로깅할 경우 파일 이름을 HTML로 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-186">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="7565d-187">공격자는 전체 경로나 상대 경로를 포함하여 악의적인 파일 이름을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-187">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="7565d-188">애플리케이션에서 다음을 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-188">Applications should:</span></span>
>
> * <span data-ttu-id="7565d-189">사용자가 제공한 파일 이름에서 경로를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-189">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="7565d-190">UI 또는 로깅을 위해 HTML로 인코딩되고 경로가 제거된 파일 이름을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-190">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="7565d-191">스토리지의 새 임의 파일 이름을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-191">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="7565d-192">다음 코드는 파일 이름에서 경로를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-192">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="7565d-193">지금까지 제시한 예제에서는 보안 고려 사항을 감안하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-193">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="7565d-194">추가 정보는 다음 섹션과 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)에서 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-194">Additional information is provided by the following sections and the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="7565d-195">보안 고려 사항</span><span class="sxs-lookup"><span data-stu-id="7565d-195">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="7565d-196">유효성 검사</span><span class="sxs-lookup"><span data-stu-id="7565d-196">Validation</span></span>](#validation)

<span data-ttu-id="7565d-197">모델 바인딩 및 <xref:Microsoft.AspNetCore.Http.IFormFile>을 사용하여 파일을 업로드하는 경우 작업 메서드에서 다음을 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-197">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="7565d-198">단일 <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="7565d-198">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="7565d-199">여러 파일을 나타내는 다음 컬렉션 중 하나.</span><span class="sxs-lookup"><span data-stu-id="7565d-199">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="7565d-200">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="7565d-200">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="7565d-201">바인딩은 이름을 기준으로 양식 파일을 일치시킵니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-201">Binding matches form files by name.</span></span> <span data-ttu-id="7565d-202">예를 들어 `<input type="file" name="formFile">`의 HTML `name` 값은 바인딩된 C# 매개 변수/속성(`FormFile`)과 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-202">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="7565d-203">자세한 내용은 [이름 특성 값을 POST 메서드의 매개 변수 이름과 일치](#match-name-attribute-value-to-parameter-name-of-post-method) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7565d-203">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="7565d-204">다음 예제가 하는 일:</span><span class="sxs-lookup"><span data-stu-id="7565d-204">The following example:</span></span>

* <span data-ttu-id="7565d-205">하나 이상의 업로드된 파일을 반복합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-205">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="7565d-206">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*)을 사용하여 파일 이름을 포함하는 파일 전체 경로를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-206">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="7565d-207">앱에서 생성한 파일 이름을 사용하여 로컬 파일 시스템에 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-207">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="7565d-208">업로드된 파일의 총 수와 크기를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-208">Returns the total number and size of files uploaded.</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size, filePath });
}
```

<span data-ttu-id="7565d-209">`Path.GetRandomFileName`을 사용하여 경로 없이 파일 이름을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-209">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="7565d-210">다음 예제에서는 경로를 구성에서 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-210">In the following example, the path is obtained from configuration:</span></span>

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

<span data-ttu-id="7565d-211"><xref:System.IO.FileStream>에 전달된 경로는 *반드시* 파일 이름을 포함해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-211">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="7565d-212">파일 이름을 제공하지 않으면 런타임에 <xref:System.UnauthorizedAccessException>이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-212">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="7565d-213"><xref:Microsoft.AspNetCore.Http.IFormFile> 기술을 사용하여 업로드된 파일은 처리 전에 서버의 메모리나 디스크에 버퍼링됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-213">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="7565d-214">작업 메서드 내부에서 <xref:Microsoft.AspNetCore.Http.IFormFile> 내용을 <xref:System.IO.Stream>으로 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-214">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="7565d-215">로컬 파일 시스템 외에도 파일을 네트워크 공유 또는 파일 스토리지 서비스(예: [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs))에 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-215">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="7565d-216">업로드를 위해 여러 파일에 대해 루프를 실행하고 안전한 파일 이름을 사용하는 또 하나의 예제는 샘플 앱에서 *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs*를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7565d-216">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="7565d-217">이전 임시 파일을 삭제하지 않고 65,535개를 초과하는 파일을 만들면 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*)이 <xref:System.IO.IOException>을 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-217">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="7565d-218">65535개 파일 제한은 서버당 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-218">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="7565d-219">Windows OS에서 이 제한에 대한 자세한 내용은 다음 항목의 설명을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7565d-219">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="7565d-220">GetTempFileNameA 함수</span><span class="sxs-lookup"><span data-stu-id="7565d-220">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="7565d-221">버퍼링된 모델 바인딩을 사용하여 소용량 파일을 데이터베이스에 업로드</span><span class="sxs-lookup"><span data-stu-id="7565d-221">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="7565d-222">[Entity Framework](/ef/core/index)를 사용하여 데이터베이스에 이진 파일 데이터를 저장하려면 엔터티에서 <xref:System.Byte> 배열 속성을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-222">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="7565d-223"><xref:Microsoft.AspNetCore.Http.IFormFile>을 포함하는 클래스에 대한 페이지 모델 속성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-223">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="7565d-224"><xref:Microsoft.AspNetCore.Http.IFormFile>은 작업 메서드 매개 변수 또는 바운딩 모델 속성으로 직접 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-224"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="7565d-225">위 예제에서는 바인딩된 모델 속성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-225">The prior example uses a bound model property.</span></span>

<span data-ttu-id="7565d-226">`FileUpload`는 Razor Pages 양식에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-226">The `FileUpload` is used in the Razor Pages form:</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

<span data-ttu-id="7565d-227">양식이 서버에 POST되면 <xref:Microsoft.AspNetCore.Http.IFormFile>을 스트림으로 복사하여 데이터베이스에 바이트 배열로 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-227">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="7565d-228">다음 예제에서 `_dbContext`는 앱의 데이터베이스 컨텍스트를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-228">In the following example, `_dbContext` stores the app's database context:</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

<span data-ttu-id="7565d-229">위의 예제는 샘플 앱에서 보여 주는 시나리오와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-229">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="7565d-230">*Pages/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="7565d-230">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="7565d-231">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="7565d-231">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="7565d-232">관계형 데이터베이스에 이진 데이터를 저장할 경우 성능에 나쁜 영향을 줄 수 있으므로 주의하세요.</span><span class="sxs-lookup"><span data-stu-id="7565d-232">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="7565d-233">유효성 검사 없이 <xref:Microsoft.AspNetCore.Http.IFormFile>의 `FileName` 속성을 의존하거나 신뢰하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="7565d-233">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="7565d-234">`FileName` 속성은 반드시 HTML 인코딩 후 표시 목적으로만 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-234">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="7565d-235">제시한 예제에서는 보안 고려 사항을 감안하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-235">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="7565d-236">추가 정보는 다음 섹션과 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)에서 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-236">Additional information is provided by the following sections and the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="7565d-237">보안 고려 사항</span><span class="sxs-lookup"><span data-stu-id="7565d-237">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="7565d-238">유효성 검사</span><span class="sxs-lookup"><span data-stu-id="7565d-238">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="7565d-239">스트리밍을 사용하여 대용량 파일 업로드</span><span class="sxs-lookup"><span data-stu-id="7565d-239">Upload large files with streaming</span></span>

<span data-ttu-id="7565d-240">다음 예제에서는 JavaScript를 사용하여 컨트롤러 작업에 파일을 스트리밍하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-240">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="7565d-241">사용자 지정 필터 특성을 사용하여 파일의 위조 방지 토큰이 생성되고 요청 본문 대신 클라이언트 HTTP 헤더에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-241">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="7565d-242">작업 메서드에서 업로드된 데이터를 직접 처리하므로 다른 사용자 지정 필터에서 형식 모델 바인딩을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-242">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="7565d-243">작업 내에서 양식의 콘텐츠는 각 개별 `MultipartSection`을 읽고 적절하게 파일을 처리하거나 콘텐츠를 저장하는 `MultipartReader`를 사용하여 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-243">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="7565d-244">다중 파트 섹션을 읽은 후 작업에서 자체 모델 바인딩을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-244">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="7565d-245">초기 페이지 응답에서는 양식을 로드하고 위조 방지 토큰을 쿠키에 저장합니다(`GenerateAntiforgeryTokenCookieAttribute` 특성을 통해).</span><span class="sxs-lookup"><span data-stu-id="7565d-245">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="7565d-246">이 특성은 ASP.NET Core의 기본 제공 [위조 방지 지원](xref:security/anti-request-forgery)을 사용하여 요청 토큰으로 쿠키를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-246">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="7565d-247">`DisableFormValueModelBindingAttribute`은 모델 바인딩을 사용하지 않도록 설정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-247">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="7565d-248">샘플 앱에서 `GenerateAntiforgeryTokenCookieAttribute` 및 `DisableFormValueModelBindingAttribute`는 [Razor Pages 규칙](xref:razor-pages/razor-pages-conventions)을 사용하여 `Startup.ConfigureServices` 내 `/StreamedSingleFileUploadDb` 및 `/StreamedSingleFileUploadPhysical` 페이지의 애플리케이션 모델에 필터로 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-248">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=8-11,17-20)]

<span data-ttu-id="7565d-249">모델 바인딩은 양식을 읽지 않으므로 양식에서 바인딩된 매개 변수가 바인딩되지 않습니다(쿼리, 경로 및 헤더는 계속 작동함).</span><span class="sxs-lookup"><span data-stu-id="7565d-249">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="7565d-250">작업 메서드는 `Request` 속성으로 직접 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-250">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="7565d-251">`MultipartReader`는 각 섹션을 읽는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-251">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="7565d-252">키/값 데이터는 `KeyValueAccumulator`에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-252">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="7565d-253">다중 파트 섹션을 읽은 후 `KeyValueAccumulator`의 내용이 양식 데이터를 모델 형식으로 바인딩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-253">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="7565d-254">EF Core를 사용하여 데이터베이스에 스트리밍하기 위한 전체 `StreamingController.UploadDatabase` 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-254">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="7565d-255">`MultipartRequestHelper`(*Utilities/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="7565d-255">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="7565d-256">실제 위치로 스트리밍하기 위한 전체 `StreamingController.UploadPhysical` 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-256">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="7565d-257">샘플 앱에서 유효성 검사는 `FileHelpers.ProcessStreamedFile`에 의해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-257">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="7565d-258">유효성 검사</span><span class="sxs-lookup"><span data-stu-id="7565d-258">Validation</span></span>

<span data-ttu-id="7565d-259">샘플 앱의 `FileHelpers` 클래스는 버퍼링된 <xref:Microsoft.AspNetCore.Http.IFormFile> 및 스트리밍된 파일 업로드에 대한 여러 검사를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-259">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="7565d-260">샘플 앱에서 <xref:Microsoft.AspNetCore.Http.IFormFile> 버퍼링된 파일 업로드를 처리하려면 *Utilities/FileHelpers.cs* 파일에서 `ProcessFormFile` 메서드를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7565d-260">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="7565d-261">스트리밍된 파일의 처리는 동일한 파일의 `ProcessStreamedFile` 메서드를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7565d-261">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="7565d-262">샘플 앱에서 보여 주는 유효성 검사 처리 메서드는 업로드된 파일의 내용을 검사하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-262">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="7565d-263">대부분의 프로덕션 시나리오에서는 사용자 또는 다른 시스템에서 파일을 사용할 수 있도록 하기 전에 파일에 바이러스/맬웨어 스캐너 API를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-263">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="7565d-264">항목 샘플에서는 유효성 검사 기술에 대한 작업 예제를 제공하지만, 다음과 같은 경우가 아니면 프로덕션 앱에서 `FileHelpers` 클래스를 구현하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="7565d-264">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="7565d-265">구현을 완전히 이해합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-265">Fully understand the implementation.</span></span>
> * <span data-ttu-id="7565d-266">앱의 환경 및 사양에 맞게 구현을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-266">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="7565d-267">**이러한 요구 사항을 해결하지 않고 앱에서 보안 코드를 무분별하게 구현해서는 안 됩니다.**</span><span class="sxs-lookup"><span data-stu-id="7565d-267">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="7565d-268">내용 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="7565d-268">Content validation</span></span>

<span data-ttu-id="7565d-269">**업로드된 콘텐츠에 타사 바이러스/맬웨어 검사 API를 사용합니다.**</span><span class="sxs-lookup"><span data-stu-id="7565d-269">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="7565d-270">대용량 시나리오에서 파일 검사는 많은 서버 리소스를 요구합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-270">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="7565d-271">파일 검사로 인해 요청 처리 성능이 저하된 경우 검사 작업을 [백그라운드 서비스](xref:fundamentals/host/hosted-services)로 오프로드하는 것이 좋습니다.이 경우 앱 서버와 다른 서버에서 실행되는 서비스가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-271">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="7565d-272">일반적으로 업로드된 파일은 백그라운드 바이러스 검사 프로그램에서 검사될 때까지 격리된 영역에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-272">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="7565d-273">파일이 전달되면 파일이 일반 파일 스토리지 위치로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-273">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="7565d-274">이러한 단계는 일반적으로 파일의 검사 상태를 나타내는 데이터베이스 레코드와 함께 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-274">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="7565d-275">이러한 방법을 사용하여 앱 및 앱 서버는 요청에 응답하는 데 집중합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-275">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="7565d-276">파일 확장명 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="7565d-276">File extension validation</span></span>

<span data-ttu-id="7565d-277">업로드된 파일의 확장명을 허용된 확장명 목록에 따라 확인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-277">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="7565d-278">예:</span><span class="sxs-lookup"><span data-stu-id="7565d-278">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="7565d-279">파일 서명 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="7565d-279">File signature validation</span></span>

<span data-ttu-id="7565d-280">파일의 서명은 파일 시작 부분의 처음 몇 바이트에 의해 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-280">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="7565d-281">이러한 바이트는 확장명이 파일 내용과 일치하는지 여부를 나타내는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-281">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="7565d-282">샘플 앱은 몇 가지 일반적인 파일 형식에 대한 파일 서명을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-282">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="7565d-283">다음 예제에서는 파일에서 JPEG 이미지 파일에 대한 서명을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-283">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

<span data-ttu-id="7565d-284">추가 파일 서명을 얻으려면 [파일 서명 데이터베이스](https://www.filesignatures.net/) 및 공식 파일 사양을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7565d-284">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="7565d-285">파일 이름 보안</span><span class="sxs-lookup"><span data-stu-id="7565d-285">File name security</span></span>

<span data-ttu-id="7565d-286">실제 스토리지에 파일을 저장하는 데 클라이언트에서 제공하는 파일 이름을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="7565d-286">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="7565d-287">[Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) 또는 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*)을 사용하여 임시 스토리지에 대한 전체 경로(파일 이름을 포함)를 만들어 파일에 대한 안전한 파일 이름을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-287">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="7565d-288">Razor는 표시를 위해 속성 값을 자동으로 HTML 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-288">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="7565d-289">다음 코드는 안전하게 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-289">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="7565d-290">Razor 외부에서는 항상 사용자 요청의 파일 이름 콘텐츠를 <xref:System.Net.WebUtility.HtmlEncode*>합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-290">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="7565d-291">많은 구현에서 파일 존재 여부에 대한 확인이 포함되어야 합니다. 그렇지 않으면 파일이 같은 이름의 파일을 덮어씁니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-291">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="7565d-292">앱의 사양을 충족하는 추가 논리를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-292">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="7565d-293">크기 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="7565d-293">Size validation</span></span>

<span data-ttu-id="7565d-294">업로드된 파일의 크기를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-294">Limit the size of uploaded files.</span></span>

<span data-ttu-id="7565d-295">샘플 앱에서 파일 크기는 2MB(바이트 단위로 표시)로 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-295">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="7565d-296">이 제한은 *appsettings.json* 파일의 [구성](xref:fundamentals/configuration/index)을 통해 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-296">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="7565d-297">`FileSizeLimit`은 `PageModel` 클래스에 삽입됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-297">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

<span data-ttu-id="7565d-298">파일 크기가 제한을 초과하는 파일은 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-298">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="7565d-299">이름 특성 값을 POST 메서드의 매개 변수 이름과 일치</span><span class="sxs-lookup"><span data-stu-id="7565d-299">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="7565d-300">양식 데이터를 POST하거나 JavaScript의 `FormData`를 직접 사용하는 비 Razor 양식에서 양식의 요소 또는 `FormData`에 지정된 이름은 컨트롤러의 작업에서 매개 변수 이름과 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-300">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="7565d-301">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="7565d-301">In the following example:</span></span>

* <span data-ttu-id="7565d-302">`<input>` 요소를 사용하는 경우 `name` 특성은 값 `battlePlans`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-302">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="7565d-303">JavaScript에서 `FormData`를 사용하는 경우 이름이 값 `battlePlans`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-303">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="7565d-304">C# 메서드(`battlePlans`)의 매개 변수와 일치하는 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-304">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="7565d-305">이름이 `Upload`인 Razor Pages 페이지 처리기 메서드:</span><span class="sxs-lookup"><span data-stu-id="7565d-305">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="7565d-306">MVC POST 컨트롤러 작업 메서드:</span><span class="sxs-lookup"><span data-stu-id="7565d-306">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="7565d-307">서버 및 앱 구성</span><span class="sxs-lookup"><span data-stu-id="7565d-307">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="7565d-308">다중 파트 본문 길이 제한</span><span class="sxs-lookup"><span data-stu-id="7565d-308">Multipart body length limit</span></span>

<span data-ttu-id="7565d-309"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>에서 각 다중 파트 본문의 길이에 대한 제한을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-309"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="7565d-310">양식 섹션이 이 제한을 초과하면 구문 분석할 때 <xref:System.IO.InvalidDataException>이 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-310">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="7565d-311">기본값은 134,217,728(128MB)입니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-311">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="7565d-312">`Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 설정을 사용하여 제한을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-312">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<span data-ttu-id="7565d-313"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>는 단일 페이지 또는 작업에 대해 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>을 설정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-313"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="7565d-314">Razor Pages 앱에서 `Startup.ConfigureServices`의 [규칙](xref:razor-pages/razor-pages-conventions)을 사용하여 필터를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-314">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model.Filters.Add(
                    new RequestFormLimitsAttribute()
                    {
                        // Set the limit to 256 MB
                        MultipartBodyLengthLimit = 268435456
                    });
    });
```

<span data-ttu-id="7565d-315">Razor Pages 앱 또는 MVC 앱에서 페이지 모델 또는 작업 메서드에 필터를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-315">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="7565d-316">Kestrel 최대 요청 본문 크기</span><span class="sxs-lookup"><span data-stu-id="7565d-316">Kestrel maximum request body size</span></span>

<span data-ttu-id="7565d-317">Kestrel에서 호스트되는 앱의 경우 기본 최대 요청 본문 크기는 30,000,000바이트(약 28.6MB)입니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-317">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="7565d-318">[MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel 서버 옵션을 사용하여 제한을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-318">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            // Handle requests up to 50 MB
            options.Limits.MaxRequestBodySize = 52428800;
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="7565d-319"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>는 단일 페이지 또는 작업에 대해 [ MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size)를 설정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-319"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="7565d-320">Razor Pages 앱에서 `Startup.ConfigureServices`의 [규칙](xref:razor-pages/razor-pages-conventions)을 사용하여 필터를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-320">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddRazorPages()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model =>
                {
                    // Handle requests up to 50 MB
                    model.Filters.Add(
                        new RequestSizeLimitAttribute(52428800));
                });
    });
```

<span data-ttu-id="7565d-321">Razor Pages 앱 또는 MVC 앱에서 페이지 처리기 모델 또는 작업 메서드에 필터를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-321">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="7565d-322">`RequestSizeLimitAttribute`은 [@attribute](xref:mvc/views/razor#attribute) Razor 지시문을 사용하여 적용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-322">The `RequestSizeLimitAttribute` can also be applied using the [@attribute](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="7565d-323">기타 Kestrel 제한</span><span class="sxs-lookup"><span data-stu-id="7565d-323">Other Kestrel limits</span></span>

<span data-ttu-id="7565d-324">다른 Kestrel 제한이 Kestrel에서 호스트되는 앱에 적용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-324">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="7565d-325">최대 클라이언트 연결</span><span class="sxs-lookup"><span data-stu-id="7565d-325">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="7565d-326">요청 및 응답 데이터 속도</span><span class="sxs-lookup"><span data-stu-id="7565d-326">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="7565d-327">IIS 콘텐츠 길이 제한</span><span class="sxs-lookup"><span data-stu-id="7565d-327">IIS content length limit</span></span>

<span data-ttu-id="7565d-328">기본 요청 제한(`maxAllowedContentLength`)은 30,000,000만 바이트(약 28.6MB)입니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-328">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="7565d-329">*web.config* 파일에서 제한을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-329">Customize the limit in the *web.config* file:</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- Handle requests up to 50 MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="7565d-330">이 설정은 IIS에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-330">This setting only applies to IIS.</span></span> <span data-ttu-id="7565d-331">Kestrel에서 호스팅하는 경우 기본적으로 이 동작은 발생하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-331">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="7565d-332">자세한 내용은 [요청 제한 \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7565d-332">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="7565d-333">ASP.NET Core 모듈의 제한 사항 또는 IIS 요청 필터링 모듈의 존재로 인해 업로드가 2GB 또는 4GB로 제한될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-333">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="7565d-334">자세한 내용은 [크기가 2GB 이상인 파일을 업로드할 수 없음(aspnet/AspNetCore #2711)](https://github.com/aspnet/AspNetCore/issues/2711)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7565d-334">For more information, see [Unable to upload file greater than 2GB in size (aspnet/AspNetCore #2711)](https://github.com/aspnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="7565d-335">문제 해결</span><span class="sxs-lookup"><span data-stu-id="7565d-335">Troubleshoot</span></span>

<span data-ttu-id="7565d-336">다음은 파일 업로드 및 가능한 솔루션을 사용하여 작업할 때 자주 발생하는 몇 가지 일반적인 문제입니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-336">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="7565d-337">IIS 서버에 배포할 때 찾을 수 없음 오류</span><span class="sxs-lookup"><span data-stu-id="7565d-337">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="7565d-338">다음 오류는 업로드된 파일이 서버의 구성된 콘텐츠 길이를 초과했음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-338">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="7565d-339">제한을 높이는 방법에 대한 자세한 내용은 [IIS 콘텐츠 길이 제한](#iis-content-length-limit) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7565d-339">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="7565d-340">연결 실패</span><span class="sxs-lookup"><span data-stu-id="7565d-340">Connection failure</span></span>

<span data-ttu-id="7565d-341">연결 오류 및 서버 연결 다시 설정은 업로드된 파일이 Kestrel의 최대 요청 본문 크기를 초과함을 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-341">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="7565d-342">자세한 내용은 [Kestrel 최대 요청 본문 크기](#kestrel-maximum-request-body-size) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7565d-342">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="7565d-343">Kestrel 클라이언트 연결 제한을 조정해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-343">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="7565d-344">IFormFile 사용 시 Null 참조 예외</span><span class="sxs-lookup"><span data-stu-id="7565d-344">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="7565d-345">컨트롤러에서 <xref:Microsoft.AspNetCore.Http.IFormFile>을 사용하여 업로드된 파일을 수락하지만 값이 `null`이면 HTML 양식에서 `multipart/form-data`의 `enctype` 값을 지정하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-345">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="7565d-346">`<form>` 요소에서 이 특성이 설정되지 않으면 파일 업로드가 실행되지 않고 바인딩된 <xref:Microsoft.AspNetCore.Http.IFormFile> 인수는 모두 `null`이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-346">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="7565d-347">또한 [양식 데이터의 업로드 이름 지정이 앱의 이름 지정과 일치](#match-name-attribute-value-to-parameter-name-of-post-method)하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-347">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7565d-348">ASP.NET Core는 소용량 파일의 경우에는 버퍼링된 모델 바인딩을 사용하여 하나 이상의 파일을 업로드하고, 대용량 파일의 경우에는 버퍼링되지 않은 스트리밍을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-348">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="7565d-349">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7565d-349">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="7565d-350">보안 고려 사항</span><span class="sxs-lookup"><span data-stu-id="7565d-350">Security considerations</span></span>

<span data-ttu-id="7565d-351">사용자에게 서버에 파일을 업로드하는 기능을 제공할 때는 주의해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-351">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="7565d-352">공격자는 다음을 시도할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-352">Attackers may attempt to:</span></span>

* <span data-ttu-id="7565d-353">[서비스 거부](/windows-hardware/drivers/ifs/denial-of-service) 공격을 실행.</span><span class="sxs-lookup"><span data-stu-id="7565d-353">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="7565d-354">바이러스 또는 맬웨어를 업로드.</span><span class="sxs-lookup"><span data-stu-id="7565d-354">Upload viruses or malware.</span></span>
* <span data-ttu-id="7565d-355">다른 방법으로 네트워크 및 서버를 손상.</span><span class="sxs-lookup"><span data-stu-id="7565d-355">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="7565d-356">공격이 성공할 가능성을 줄이는 보안 단계는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-356">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="7565d-357">전용 파일 업로드 영역(바람직하게는 시스템 드라이브가 아닌 위치)에 파일을 업로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-357">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="7565d-358">전용 위치를 사용하면 업로드된 파일에 대한 보안 제한을 더 쉽게 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-358">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="7565d-359">파일 업로드 위치에 대한 실행 권한을 사용하지 않도록 설정합니다.&dagger;</span><span class="sxs-lookup"><span data-stu-id="7565d-359">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="7565d-360">업로드된 파일을 앱과 동일한 디렉터리 트리에 보관하지 **마세요**.&dagger;</span><span class="sxs-lookup"><span data-stu-id="7565d-360">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="7565d-361">앱에 의해 결정된 안전한 파일 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-361">Use a safe file name determined by the app.</span></span> <span data-ttu-id="7565d-362">사용자가 제공한 파일 이름이나 업로드된 파일의 신뢰할 수 없는 파일 이름을 사용하지 마세요.&dagger; 신뢰할 수 없는 파일 이름을 표시하려면 HTML로 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-362">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="7565d-363">파일 이름을 로깅하거나 UI에 표시하는 경우를 예로 들 수 있습니다(Razor에서 자동으로 출력을 HTML로 인코딩).</span><span class="sxs-lookup"><span data-stu-id="7565d-363">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="7565d-364">앱의 디자인 사양으로 승인된 파일 확장명만 허용합니다.&dagger;</span><span class="sxs-lookup"><span data-stu-id="7565d-364">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="7565d-365">서버에서 클라이언트 쪽 검사가 수행되는지 확인합니다.&dagger; 클라이언트 쪽 검사는 피하기 쉽습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-365">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="7565d-366">업로드된 파일의 크기를 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-366">Check the size of an uploaded file.</span></span> <span data-ttu-id="7565d-367">대규모로 업로드되지 않도록 최대 크기 제한을 설정합니다.&dagger;</span><span class="sxs-lookup"><span data-stu-id="7565d-367">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="7565d-368">업로드된 파일이 같은 이름의 파일을 덮어쓰면 안 되는 경우 파일을 업로드하기 전에 데이터베이스 또는 실제 스토리지에서 파일 이름을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-368">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="7565d-369">**파일이 저장되기 전에 업로드된 콘텐츠에 대해 바이러스/맬웨어 스캐너를 실행합니다.**</span><span class="sxs-lookup"><span data-stu-id="7565d-369">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="7565d-370">&dagger;샘플 앱은 조건을 충족하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-370">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="7565d-371">시스템에 악성 코드를 업로드하는 행위는 흔히 다음을 수행할 수 있는 코드를 실행하기 위한 첫 단계가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-371">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="7565d-372">시스템을 완전히 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-372">Completely gain control of a system.</span></span>
> * <span data-ttu-id="7565d-373">시스템 작동이 중단되는 결과로 시스템을 오버로드합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-373">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="7565d-374">사용자 또는 시스템 데이터를 손상시킵니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-374">Compromise user or system data.</span></span>
> * <span data-ttu-id="7565d-375">공용 UI에 그래피티를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-375">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="7565d-376">사용자의 파일을 수락할 때 공격 노출 영역을 줄이는 방법에 대한 자세한 내용은 다음 리소스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7565d-376">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="7565d-377">무제한 파일 업로드</span><span class="sxs-lookup"><span data-stu-id="7565d-377">Unrestricted File Upload</span></span>](https://www.owasp.org/index.php/Unrestricted_File_Upload)
> * [<span data-ttu-id="7565d-378">Azure Security: 사용자의 파일을 허용할 때 적절한 제어가 수행되는지 확인</span><span class="sxs-lookup"><span data-stu-id="7565d-378">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="7565d-379">샘플 앱의 예제를 포함하여 보안 조치를 구현하는 방법에 대한 자세한 내용은 [유효성 검사](#validation) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7565d-379">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="7565d-380">스토리지 시나리오</span><span class="sxs-lookup"><span data-stu-id="7565d-380">Storage scenarios</span></span>

<span data-ttu-id="7565d-381">파일에 대한 일반적인 스토리지 옵션은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-381">Common storage options for files include:</span></span>

* <span data-ttu-id="7565d-382">데이터베이스</span><span class="sxs-lookup"><span data-stu-id="7565d-382">Database</span></span>

  * <span data-ttu-id="7565d-383">소용량 파일 업로드의 경우 데이터베이스는 실제 스토리지(파일 시스템 또는 네트워크 공유) 옵션보다 빠른 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-383">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="7565d-384">사용자 데이터에 대한 데이터베이스 레코드를 검색할 때 파일 콘텐츠(예: 아바타 이미지)를 동시에 제공할 수 있으므로 데이터베이스는 실제 스토리지 옵션보다 편리합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-384">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="7565d-385">데이터베이스는 데이터 스토리지 서비스를 사용하는 것보다 비용이 적게 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-385">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="7565d-386">실제 스토리지(파일 시스템 또는 네트워크 공유)</span><span class="sxs-lookup"><span data-stu-id="7565d-386">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="7565d-387">대용량 파일 업로드의 경우:</span><span class="sxs-lookup"><span data-stu-id="7565d-387">For large file uploads:</span></span>
    * <span data-ttu-id="7565d-388">데이터베이스 한도 때문에 업로드 크기가 제한될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-388">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="7565d-389">실제 스토리지는 데이터베이스 스토리지보다 경제적이지 않은 경우가 자주 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-389">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="7565d-390">실제 스토리지는 데이터 스토리지 서비스를 사용하는 것보다 비용이 적게 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-390">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="7565d-391">앱의 프로세스에는 스토리지 위치에 대한 읽기 및 쓰기 권한이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-391">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="7565d-392">**실행 권한을 부여하지 마세요.**</span><span class="sxs-lookup"><span data-stu-id="7565d-392">**Never grant execute permission.**</span></span>

* <span data-ttu-id="7565d-393">데이터 스토리지 서비스(예: [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="7565d-393">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="7565d-394">서비스는 일반적으로 단일 실패 지점에 노출되는 온-프레미스 솔루션에 비해 향상된 확장성 및 복원력을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-394">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="7565d-395">서비스는 대용량 스토리지 인프라 시나리오에서 비용이 더 저렴할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-395">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="7565d-396">자세한 내용은 [빠른 시작: .NET을 사용하여 개체 스토리지에 Blob 만들기](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="7565d-396">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="7565d-397">이 항목에서는 <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>를 보여 주지만, <xref:System.IO.Stream>에서 작업할 때 <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*>를 사용하여 <xref:System.IO.FileStream>을 Blob Storage에 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-397">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="7565d-398">파일 업로드 시나리오</span><span class="sxs-lookup"><span data-stu-id="7565d-398">File upload scenarios</span></span>

<span data-ttu-id="7565d-399">파일 업로드를 위한 일반적인 방법 두 가지는 버퍼링 및 스트리밍입니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-399">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="7565d-400">**버퍼링**</span><span class="sxs-lookup"><span data-stu-id="7565d-400">**Buffering**</span></span>

<span data-ttu-id="7565d-401">전체 파일을 파일 처리 또는 저장에 사용되는 파일의 C# 표현인 <xref:Microsoft.AspNetCore.Http.IFormFile>로 읽어 들입니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-401">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="7565d-402">파일 업로드에서 사용되는 리소스(디스크, 메모리)는 동시 파일 업로드 크기와 수에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-402">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="7565d-403">앱이 너무 많은 업로드를 버퍼링하려 할 경우 메모리 또는 디스크 공간이 부족하면 사이트의 작동이 중단됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-403">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="7565d-404">파일 업로드의 크기 또는 빈도로 인해 앱 리소스가 소진되는 경우 스트리밍을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-404">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="7565d-405">버퍼링된 단일 파일이 64KB를 초과하는 경우 메모리에서 디스크의 임시 파일로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-405">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="7565d-406">소용량 파일 버퍼링은 이 항목의 다음 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-406">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="7565d-407">실제 스토리지</span><span class="sxs-lookup"><span data-stu-id="7565d-407">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="7565d-408">데이터베이스</span><span class="sxs-lookup"><span data-stu-id="7565d-408">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="7565d-409">**스트리밍**</span><span class="sxs-lookup"><span data-stu-id="7565d-409">**Streaming**</span></span>

<span data-ttu-id="7565d-410">파일은 다중 파트 요청에서 수신되며 앱에서 직접 처리하거나 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-410">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="7565d-411">스트리밍은 성능을 크게 개선하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-411">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="7565d-412">스트리밍을 통해 파일을 업로드하면 메모리 또는 디스크 공간에 대한 요구가 줄어듭니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-412">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="7565d-413">대용량 파일 스트리밍은 [스트리밍을 사용하여 대용량 파일 업로드](#upload-large-files-with-streaming) 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-413">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="7565d-414">버퍼링된 모델 바인딩을 사용하여 소용량 파일을 실제 스토리지에 업로드</span><span class="sxs-lookup"><span data-stu-id="7565d-414">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="7565d-415">소용량 파일을 업로드하려면 다중 파트 양식을 사용하거나 JavaScript를 사용하여 POST 요청을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-415">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="7565d-416">다음 예제에서는 Razor Pages 양식을 사용하여 단일 파일(샘플 앱의 *BufferedSingleFileUploadPhysical*)을 업로드하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-416">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

<span data-ttu-id="7565d-417">다음 예제는 이전 예제와 비슷하지만 다음과 같은 차이가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-417">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="7565d-418">양식의 데이터를 제출하는 데 JavaScript([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API))가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-418">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="7565d-419">유효성 검사를 수행하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-419">There's no validation.</span></span>

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

<span data-ttu-id="7565d-420">[Fetch API](https://caniuse.com/#feat=fetch)를 지원하지 않는 클라이언트에 대해 JavaScript로 양식 POST를 수행하려면 다음 방법 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-420">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="7565d-421">Fetch Polyfill(예: [window.fetch polyfill (github/fetch)](https://github.com/github/fetch))을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-421">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="7565d-422">`XMLHttpRequest`을 사용하십시오.</span><span class="sxs-lookup"><span data-stu-id="7565d-422">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="7565d-423">예:</span><span class="sxs-lookup"><span data-stu-id="7565d-423">For example:</span></span>

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

<span data-ttu-id="7565d-424">파일 업로드를 지원하려면 HTML 양식에서 `multipart/form-data`의 인코딩 유형(`enctype`)을 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-424">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="7565d-425">`files` 입력 요소가 다중 파일 업로드를 지원하려면 `<input>` 요소에 `multiple` 특성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-425">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="7565d-426">서버에 업로드된 개별 파일은 <xref:Microsoft.AspNetCore.Http.IFormFile>을 사용하여 [모델 바인딩](xref:mvc/models/model-binding)을 통해 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-426">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="7565d-427">샘플 앱은 데이터베이스 및 실제 스토리지 시나리오에 대한 다중 버퍼링된 파일 업로드를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-427">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="7565d-428">표시 및 로깅 이외에는 <xref:Microsoft.AspNetCore.Http.IFormFile>의 `FileName` 속성을 사용하지 **마세요**.</span><span class="sxs-lookup"><span data-stu-id="7565d-428">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="7565d-429">표시하거나 로깅할 경우 파일 이름을 HTML로 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-429">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="7565d-430">공격자는 전체 경로나 상대 경로를 포함하여 악의적인 파일 이름을 제공할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-430">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="7565d-431">애플리케이션에서 다음을 수행해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-431">Applications should:</span></span>
>
> * <span data-ttu-id="7565d-432">사용자가 제공한 파일 이름에서 경로를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-432">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="7565d-433">UI 또는 로깅을 위해 HTML로 인코딩되고 경로가 제거된 파일 이름을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-433">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="7565d-434">스토리지의 새 임의 파일 이름을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-434">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="7565d-435">다음 코드는 파일 이름에서 경로를 제거합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-435">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="7565d-436">지금까지 제시한 예제에서는 보안 고려 사항을 감안하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-436">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="7565d-437">추가 정보는 다음 섹션과 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)에서 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-437">Additional information is provided by the following sections and the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="7565d-438">보안 고려 사항</span><span class="sxs-lookup"><span data-stu-id="7565d-438">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="7565d-439">유효성 검사</span><span class="sxs-lookup"><span data-stu-id="7565d-439">Validation</span></span>](#validation)

<span data-ttu-id="7565d-440">모델 바인딩 및 <xref:Microsoft.AspNetCore.Http.IFormFile>을 사용하여 파일을 업로드하는 경우 작업 메서드에서 다음을 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-440">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="7565d-441">단일 <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="7565d-441">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="7565d-442">여러 파일을 나타내는 다음 컬렉션 중 하나.</span><span class="sxs-lookup"><span data-stu-id="7565d-442">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="7565d-443">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="7565d-443">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="7565d-444">바인딩은 이름을 기준으로 양식 파일을 일치시킵니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-444">Binding matches form files by name.</span></span> <span data-ttu-id="7565d-445">예를 들어 `<input type="file" name="formFile">`의 HTML `name` 값은 바인딩된 C# 매개 변수/속성(`FormFile`)과 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-445">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="7565d-446">자세한 내용은 [이름 특성 값을 POST 메서드의 매개 변수 이름과 일치](#match-name-attribute-value-to-parameter-name-of-post-method) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7565d-446">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="7565d-447">다음 예제가 하는 일:</span><span class="sxs-lookup"><span data-stu-id="7565d-447">The following example:</span></span>

* <span data-ttu-id="7565d-448">하나 이상의 업로드된 파일을 반복합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-448">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="7565d-449">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*)을 사용하여 파일 이름을 포함하는 파일 전체 경로를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-449">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="7565d-450">앱에서 생성한 파일 이름을 사용하여 로컬 파일 시스템에 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-450">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="7565d-451">업로드된 파일의 총 수와 크기를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-451">Returns the total number and size of files uploaded.</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size, filePath });
}
```

<span data-ttu-id="7565d-452">`Path.GetRandomFileName`을 사용하여 경로 없이 파일 이름을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-452">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="7565d-453">다음 예제에서는 경로를 구성에서 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-453">In the following example, the path is obtained from configuration:</span></span>

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

<span data-ttu-id="7565d-454"><xref:System.IO.FileStream>에 전달된 경로는 *반드시* 파일 이름을 포함해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-454">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="7565d-455">파일 이름을 제공하지 않으면 런타임에 <xref:System.UnauthorizedAccessException>이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-455">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="7565d-456"><xref:Microsoft.AspNetCore.Http.IFormFile> 기술을 사용하여 업로드된 파일은 처리 전에 서버의 메모리나 디스크에 버퍼링됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-456">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="7565d-457">작업 메서드 내부에서 <xref:Microsoft.AspNetCore.Http.IFormFile> 내용을 <xref:System.IO.Stream>으로 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-457">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="7565d-458">로컬 파일 시스템 외에도 파일을 네트워크 공유 또는 파일 스토리지 서비스(예: [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs))에 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-458">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="7565d-459">업로드를 위해 여러 파일에 대해 루프를 실행하고 안전한 파일 이름을 사용하는 또 하나의 예제는 샘플 앱에서 *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs*를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7565d-459">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="7565d-460">이전 임시 파일을 삭제하지 않고 65,535개를 초과하는 파일을 만들면 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*)이 <xref:System.IO.IOException>을 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-460">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="7565d-461">65535개 파일 제한은 서버당 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-461">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="7565d-462">Windows OS에서 이 제한에 대한 자세한 내용은 다음 항목의 설명을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7565d-462">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="7565d-463">GetTempFileNameA 함수</span><span class="sxs-lookup"><span data-stu-id="7565d-463">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="7565d-464">버퍼링된 모델 바인딩을 사용하여 소용량 파일을 데이터베이스에 업로드</span><span class="sxs-lookup"><span data-stu-id="7565d-464">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="7565d-465">[Entity Framework](/ef/core/index)를 사용하여 데이터베이스에 이진 파일 데이터를 저장하려면 엔터티에서 <xref:System.Byte> 배열 속성을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-465">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="7565d-466"><xref:Microsoft.AspNetCore.Http.IFormFile>을 포함하는 클래스에 대한 페이지 모델 속성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-466">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <span data-ttu-id="7565d-467"><xref:Microsoft.AspNetCore.Http.IFormFile>은 작업 메서드 매개 변수 또는 바운딩 모델 속성으로 직접 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-467"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="7565d-468">위 예제에서는 바인딩된 모델 속성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-468">The prior example uses a bound model property.</span></span>

<span data-ttu-id="7565d-469">`FileUpload`는 Razor Pages 양식에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-469">The `FileUpload` is used in the Razor Pages form:</span></span>

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

<span data-ttu-id="7565d-470">양식이 서버에 POST되면 <xref:Microsoft.AspNetCore.Http.IFormFile>을 스트림으로 복사하여 데이터베이스에 바이트 배열로 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-470">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="7565d-471">다음 예제에서 `_dbContext`는 앱의 데이터베이스 컨텍스트를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-471">In the following example, `_dbContext` stores the app's database context:</span></span>

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

<span data-ttu-id="7565d-472">위의 예제는 샘플 앱에서 보여 주는 시나리오와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-472">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="7565d-473">*Pages/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="7565d-473">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="7565d-474">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="7565d-474">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="7565d-475">관계형 데이터베이스에 이진 데이터를 저장할 경우 성능에 나쁜 영향을 줄 수 있으므로 주의하세요.</span><span class="sxs-lookup"><span data-stu-id="7565d-475">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="7565d-476">유효성 검사 없이 <xref:Microsoft.AspNetCore.Http.IFormFile>의 `FileName` 속성을 의존하거나 신뢰하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="7565d-476">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="7565d-477">`FileName` 속성은 반드시 HTML 인코딩 후 표시 목적으로만 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-477">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="7565d-478">제시한 예제에서는 보안 고려 사항을 감안하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-478">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="7565d-479">추가 정보는 다음 섹션과 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)에서 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-479">Additional information is provided by the following sections and the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="7565d-480">보안 고려 사항</span><span class="sxs-lookup"><span data-stu-id="7565d-480">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="7565d-481">유효성 검사</span><span class="sxs-lookup"><span data-stu-id="7565d-481">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="7565d-482">스트리밍을 사용하여 대용량 파일 업로드</span><span class="sxs-lookup"><span data-stu-id="7565d-482">Upload large files with streaming</span></span>

<span data-ttu-id="7565d-483">다음 예제에서는 JavaScript를 사용하여 컨트롤러 작업에 파일을 스트리밍하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-483">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="7565d-484">사용자 지정 필터 특성을 사용하여 파일의 위조 방지 토큰이 생성되고 요청 본문 대신 클라이언트 HTTP 헤더에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-484">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="7565d-485">작업 메서드에서 업로드된 데이터를 직접 처리하므로 다른 사용자 지정 필터에서 형식 모델 바인딩을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-485">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="7565d-486">작업 내에서 양식의 콘텐츠는 각 개별 `MultipartSection`을 읽고 적절하게 파일을 처리하거나 콘텐츠를 저장하는 `MultipartReader`를 사용하여 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-486">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="7565d-487">다중 파트 섹션을 읽은 후 작업에서 자체 모델 바인딩을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-487">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="7565d-488">초기 페이지 응답에서는 양식을 로드하고 위조 방지 토큰을 쿠키에 저장합니다(`GenerateAntiforgeryTokenCookieAttribute` 특성을 통해).</span><span class="sxs-lookup"><span data-stu-id="7565d-488">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="7565d-489">이 특성은 ASP.NET Core의 기본 제공 [위조 방지 지원](xref:security/anti-request-forgery)을 사용하여 요청 토큰으로 쿠키를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-489">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="7565d-490">`DisableFormValueModelBindingAttribute`은 모델 바인딩을 사용하지 않도록 설정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-490">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="7565d-491">샘플 앱에서 `GenerateAntiforgeryTokenCookieAttribute` 및 `DisableFormValueModelBindingAttribute`는 [Razor Pages 규칙](xref:razor-pages/razor-pages-conventions)을 사용하여 `Startup.ConfigureServices` 내 `/StreamedSingleFileUploadDb` 및 `/StreamedSingleFileUploadPhysical` 페이지의 애플리케이션 모델에 필터로 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-491">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="7565d-492">모델 바인딩은 양식을 읽지 않으므로 양식에서 바인딩된 매개 변수가 바인딩되지 않습니다(쿼리, 경로 및 헤더는 계속 작동함).</span><span class="sxs-lookup"><span data-stu-id="7565d-492">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="7565d-493">작업 메서드는 `Request` 속성으로 직접 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-493">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="7565d-494">`MultipartReader`는 각 섹션을 읽는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-494">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="7565d-495">키/값 데이터는 `KeyValueAccumulator`에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-495">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="7565d-496">다중 파트 섹션을 읽은 후 `KeyValueAccumulator`의 내용이 양식 데이터를 모델 형식으로 바인딩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-496">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="7565d-497">EF Core를 사용하여 데이터베이스에 스트리밍하기 위한 전체 `StreamingController.UploadDatabase` 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-497">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="7565d-498">`MultipartRequestHelper`(*Utilities/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="7565d-498">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="7565d-499">실제 위치로 스트리밍하기 위한 전체 `StreamingController.UploadPhysical` 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-499">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="7565d-500">샘플 앱에서 유효성 검사는 `FileHelpers.ProcessStreamedFile`에 의해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-500">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="7565d-501">유효성 검사</span><span class="sxs-lookup"><span data-stu-id="7565d-501">Validation</span></span>

<span data-ttu-id="7565d-502">샘플 앱의 `FileHelpers` 클래스는 버퍼링된 <xref:Microsoft.AspNetCore.Http.IFormFile> 및 스트리밍된 파일 업로드에 대한 여러 검사를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-502">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="7565d-503">샘플 앱에서 <xref:Microsoft.AspNetCore.Http.IFormFile> 버퍼링된 파일 업로드를 처리하려면 *Utilities/FileHelpers.cs* 파일에서 `ProcessFormFile` 메서드를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7565d-503">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="7565d-504">스트리밍된 파일의 처리는 동일한 파일의 `ProcessStreamedFile` 메서드를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7565d-504">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="7565d-505">샘플 앱에서 보여 주는 유효성 검사 처리 메서드는 업로드된 파일의 내용을 검사하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-505">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="7565d-506">대부분의 프로덕션 시나리오에서는 사용자 또는 다른 시스템에서 파일을 사용할 수 있도록 하기 전에 파일에 바이러스/맬웨어 스캐너 API를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-506">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="7565d-507">항목 샘플에서는 유효성 검사 기술에 대한 작업 예제를 제공하지만, 다음과 같은 경우가 아니면 프로덕션 앱에서 `FileHelpers` 클래스를 구현하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="7565d-507">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="7565d-508">구현을 완전히 이해합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-508">Fully understand the implementation.</span></span>
> * <span data-ttu-id="7565d-509">앱의 환경 및 사양에 맞게 구현을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-509">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="7565d-510">**이러한 요구 사항을 해결하지 않고 앱에서 보안 코드를 무분별하게 구현해서는 안 됩니다.**</span><span class="sxs-lookup"><span data-stu-id="7565d-510">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="7565d-511">내용 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="7565d-511">Content validation</span></span>

<span data-ttu-id="7565d-512">**업로드된 콘텐츠에 타사 바이러스/맬웨어 검사 API를 사용합니다.**</span><span class="sxs-lookup"><span data-stu-id="7565d-512">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="7565d-513">대용량 시나리오에서 파일 검사는 많은 서버 리소스를 요구합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-513">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="7565d-514">파일 검사로 인해 요청 처리 성능이 저하된 경우 검사 작업을 [백그라운드 서비스](xref:fundamentals/host/hosted-services)로 오프로드하는 것이 좋습니다.이 경우 앱 서버와 다른 서버에서 실행되는 서비스가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-514">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="7565d-515">일반적으로 업로드된 파일은 백그라운드 바이러스 검사 프로그램에서 검사될 때까지 격리된 영역에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-515">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="7565d-516">파일이 전달되면 파일이 일반 파일 스토리지 위치로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-516">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="7565d-517">이러한 단계는 일반적으로 파일의 검사 상태를 나타내는 데이터베이스 레코드와 함께 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-517">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="7565d-518">이러한 방법을 사용하여 앱 및 앱 서버는 요청에 응답하는 데 집중합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-518">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="7565d-519">파일 확장명 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="7565d-519">File extension validation</span></span>

<span data-ttu-id="7565d-520">업로드된 파일의 확장명을 허용된 확장명 목록에 따라 확인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-520">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="7565d-521">예:</span><span class="sxs-lookup"><span data-stu-id="7565d-521">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="7565d-522">파일 서명 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="7565d-522">File signature validation</span></span>

<span data-ttu-id="7565d-523">파일의 서명은 파일 시작 부분의 처음 몇 바이트에 의해 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-523">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="7565d-524">이러한 바이트는 확장명이 파일 내용과 일치하는지 여부를 나타내는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-524">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="7565d-525">샘플 앱은 몇 가지 일반적인 파일 형식에 대한 파일 서명을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-525">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="7565d-526">다음 예제에서는 파일에서 JPEG 이미지 파일에 대한 서명을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-526">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

<span data-ttu-id="7565d-527">추가 파일 서명을 얻으려면 [파일 서명 데이터베이스](https://www.filesignatures.net/) 및 공식 파일 사양을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7565d-527">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="7565d-528">파일 이름 보안</span><span class="sxs-lookup"><span data-stu-id="7565d-528">File name security</span></span>

<span data-ttu-id="7565d-529">실제 스토리지에 파일을 저장하는 데 클라이언트에서 제공하는 파일 이름을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="7565d-529">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="7565d-530">[Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) 또는 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*)을 사용하여 임시 스토리지에 대한 전체 경로(파일 이름을 포함)를 만들어 파일에 대한 안전한 파일 이름을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-530">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="7565d-531">Razor는 표시를 위해 속성 값을 자동으로 HTML 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-531">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="7565d-532">다음 코드는 안전하게 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-532">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="7565d-533">Razor 외부에서는 항상 사용자 요청의 파일 이름 콘텐츠를 <xref:System.Net.WebUtility.HtmlEncode*>합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-533">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="7565d-534">많은 구현에서 파일 존재 여부에 대한 확인이 포함되어야 합니다. 그렇지 않으면 파일이 같은 이름의 파일을 덮어씁니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-534">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="7565d-535">앱의 사양을 충족하는 추가 논리를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-535">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="7565d-536">크기 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="7565d-536">Size validation</span></span>

<span data-ttu-id="7565d-537">업로드된 파일의 크기를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-537">Limit the size of uploaded files.</span></span>

<span data-ttu-id="7565d-538">샘플 앱에서 파일 크기는 2MB(바이트 단위로 표시)로 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-538">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="7565d-539">이 제한은 *appsettings.json* 파일의 [구성](xref:fundamentals/configuration/index)을 통해 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-539">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="7565d-540">`FileSizeLimit`은 `PageModel` 클래스에 삽입됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-540">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

<span data-ttu-id="7565d-541">파일 크기가 제한을 초과하는 파일은 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-541">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="7565d-542">이름 특성 값을 POST 메서드의 매개 변수 이름과 일치</span><span class="sxs-lookup"><span data-stu-id="7565d-542">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="7565d-543">양식 데이터를 POST하거나 JavaScript의 `FormData`를 직접 사용하는 비 Razor 양식에서 양식의 요소 또는 `FormData`에 지정된 이름은 컨트롤러의 작업에서 매개 변수 이름과 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-543">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="7565d-544">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="7565d-544">In the following example:</span></span>

* <span data-ttu-id="7565d-545">`<input>` 요소를 사용하는 경우 `name` 특성은 값 `battlePlans`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-545">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="7565d-546">JavaScript에서 `FormData`를 사용하는 경우 이름이 값 `battlePlans`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-546">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="7565d-547">C# 메서드(`battlePlans`)의 매개 변수와 일치하는 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-547">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="7565d-548">이름이 `Upload`인 Razor Pages 페이지 처리기 메서드:</span><span class="sxs-lookup"><span data-stu-id="7565d-548">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="7565d-549">MVC POST 컨트롤러 작업 메서드:</span><span class="sxs-lookup"><span data-stu-id="7565d-549">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="7565d-550">서버 및 앱 구성</span><span class="sxs-lookup"><span data-stu-id="7565d-550">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="7565d-551">다중 파트 본문 길이 제한</span><span class="sxs-lookup"><span data-stu-id="7565d-551">Multipart body length limit</span></span>

<span data-ttu-id="7565d-552"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>에서 각 다중 파트 본문의 길이에 대한 제한을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-552"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="7565d-553">양식 섹션이 이 제한을 초과하면 구문 분석할 때 <xref:System.IO.InvalidDataException>이 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-553">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="7565d-554">기본값은 134,217,728(128MB)입니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-554">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="7565d-555">`Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 설정을 사용하여 제한을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-555">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<span data-ttu-id="7565d-556"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>는 단일 페이지 또는 작업에 대해 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>을 설정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-556"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="7565d-557">Razor Pages 앱에서 `Startup.ConfigureServices`의 [규칙](xref:razor-pages/razor-pages-conventions)을 사용하여 필터를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-557">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model.Filters.Add(
                    new RequestFormLimitsAttribute()
                    {
                        // Set the limit to 256 MB
                        MultipartBodyLengthLimit = 268435456
                    });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="7565d-558">Razor Pages 앱 또는 MVC 앱에서 페이지 모델 또는 작업 메서드에 필터를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-558">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="7565d-559">Kestrel 최대 요청 본문 크기</span><span class="sxs-lookup"><span data-stu-id="7565d-559">Kestrel maximum request body size</span></span>

<span data-ttu-id="7565d-560">Kestrel에서 호스트되는 앱의 경우 기본 최대 요청 본문 크기는 30,000,000바이트(약 28.6MB)입니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-560">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="7565d-561">[MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel 서버 옵션을 사용하여 제한을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-561">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            // Handle requests up to 50 MB
            options.Limits.MaxRequestBodySize = 52428800;
        });
```

<span data-ttu-id="7565d-562"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>는 단일 페이지 또는 작업에 대해 [ MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size)를 설정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-562"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="7565d-563">Razor Pages 앱에서 `Startup.ConfigureServices`의 [규칙](xref:razor-pages/razor-pages-conventions)을 사용하여 필터를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-563">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model =>
                {
                    // Handle requests up to 50 MB
                    model.Filters.Add(
                        new RequestSizeLimitAttribute(52428800));
                });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<span data-ttu-id="7565d-564">Razor Pages 앱 또는 MVC 앱에서 페이지 처리기 모델 또는 작업 메서드에 필터를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-564">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="7565d-565">기타 Kestrel 제한</span><span class="sxs-lookup"><span data-stu-id="7565d-565">Other Kestrel limits</span></span>

<span data-ttu-id="7565d-566">다른 Kestrel 제한이 Kestrel에서 호스트되는 앱에 적용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-566">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="7565d-567">최대 클라이언트 연결</span><span class="sxs-lookup"><span data-stu-id="7565d-567">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="7565d-568">요청 및 응답 데이터 속도</span><span class="sxs-lookup"><span data-stu-id="7565d-568">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="7565d-569">IIS 콘텐츠 길이 제한</span><span class="sxs-lookup"><span data-stu-id="7565d-569">IIS content length limit</span></span>

<span data-ttu-id="7565d-570">기본 요청 제한(`maxAllowedContentLength`)은 30,000,000만 바이트(약 28.6MB)입니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-570">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="7565d-571">*web.config* 파일에서 제한을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-571">Customize the limit in the *web.config* file:</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- Handle requests up to 50 MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="7565d-572">이 설정은 IIS에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-572">This setting only applies to IIS.</span></span> <span data-ttu-id="7565d-573">Kestrel에서 호스팅하는 경우 기본적으로 이 동작은 발생하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-573">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="7565d-574">자세한 내용은 [요청 제한 \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7565d-574">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="7565d-575">ASP.NET Core 모듈의 제한 사항 또는 IIS 요청 필터링 모듈의 존재로 인해 업로드가 2GB 또는 4GB로 제한될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-575">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="7565d-576">자세한 내용은 [크기가 2GB 이상인 파일을 업로드할 수 없음(aspnet/AspNetCore #2711)](https://github.com/aspnet/AspNetCore/issues/2711)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7565d-576">For more information, see [Unable to upload file greater than 2GB in size (aspnet/AspNetCore #2711)](https://github.com/aspnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="7565d-577">문제 해결</span><span class="sxs-lookup"><span data-stu-id="7565d-577">Troubleshoot</span></span>

<span data-ttu-id="7565d-578">다음은 파일 업로드 및 가능한 솔루션을 사용하여 작업할 때 자주 발생하는 몇 가지 일반적인 문제입니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-578">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="7565d-579">IIS 서버에 배포할 때 찾을 수 없음 오류</span><span class="sxs-lookup"><span data-stu-id="7565d-579">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="7565d-580">다음 오류는 업로드된 파일이 서버의 구성된 콘텐츠 길이를 초과했음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-580">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="7565d-581">제한을 높이는 방법에 대한 자세한 내용은 [IIS 콘텐츠 길이 제한](#iis-content-length-limit) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7565d-581">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="7565d-582">연결 실패</span><span class="sxs-lookup"><span data-stu-id="7565d-582">Connection failure</span></span>

<span data-ttu-id="7565d-583">연결 오류 및 서버 연결 다시 설정은 업로드된 파일이 Kestrel의 최대 요청 본문 크기를 초과함을 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-583">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="7565d-584">자세한 내용은 [Kestrel 최대 요청 본문 크기](#kestrel-maximum-request-body-size) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="7565d-584">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="7565d-585">Kestrel 클라이언트 연결 제한을 조정해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-585">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="7565d-586">IFormFile 사용 시 Null 참조 예외</span><span class="sxs-lookup"><span data-stu-id="7565d-586">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="7565d-587">컨트롤러에서 <xref:Microsoft.AspNetCore.Http.IFormFile>을 사용하여 업로드된 파일을 수락하지만 값이 `null`이면 HTML 양식에서 `multipart/form-data`의 `enctype` 값을 지정하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-587">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="7565d-588">`<form>` 요소에서 이 특성이 설정되지 않으면 파일 업로드가 실행되지 않고 바인딩된 <xref:Microsoft.AspNetCore.Http.IFormFile> 인수는 모두 `null`이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-588">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="7565d-589">또한 [양식 데이터의 업로드 이름 지정이 앱의 이름 지정과 일치](#match-name-attribute-value-to-parameter-name-of-post-method)하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="7565d-589">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="7565d-590">추가 자료</span><span class="sxs-lookup"><span data-stu-id="7565d-590">Additional resources</span></span>

* [<span data-ttu-id="7565d-591">무제한 파일 업로드</span><span class="sxs-lookup"><span data-stu-id="7565d-591">Unrestricted File Upload</span></span>](https://www.owasp.org/index.php/Unrestricted_File_Upload)
* [<span data-ttu-id="7565d-592">Azure Security: 보안 프레임: 입력 유효성 검사 | 완화</span><span class="sxs-lookup"><span data-stu-id="7565d-592">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="7565d-593">Azure Cloud 디자인 패턴: 발레 키 패턴</span><span class="sxs-lookup"><span data-stu-id="7565d-593">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
