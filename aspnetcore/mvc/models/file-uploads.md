---
title: ASP.NET Core에서 파일 업로드
author: guardrex
description: 모델 바인딩 및 스트리밍을 사용하여 ASP.NET Core MVC에서 파일을 업로드하는 방법입니다.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/02/2019
uid: mvc/models/file-uploads
ms.openlocfilehash: de8bfee22e39dfc5a6ed254cf0555887891d4590
ms.sourcegitcommit: d81912782a8b0bd164f30a516ad80f8defb5d020
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72179299"
---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="f1b1f-103">ASP.NET Core에서 파일 업로드</span><span class="sxs-lookup"><span data-stu-id="f1b1f-103">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="f1b1f-104">작성자: [Luke Latham](https://github.com/guardrex), [Steve Smith](https://ardalis.com/) 및 [Rutger Storm](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="f1b1f-104">By [Luke Latham](https://github.com/guardrex), [Steve Smith](https://ardalis.com/), and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f1b1f-105">ASP.NET Core는 소용량 파일의 경우에는 버퍼링된 모델 바인딩을 사용하여 하나 이상의 파일을 업로드하고, 대용량 파일의 경우에는 버퍼링되지 않은 스트리밍을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-105">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="f1b1f-106">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f1b1f-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="f1b1f-107">보안 고려 사항</span><span class="sxs-lookup"><span data-stu-id="f1b1f-107">Security considerations</span></span>

<span data-ttu-id="f1b1f-108">사용자에게 서버에 파일을 업로드하는 기능을 제공할 때는 주의해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-108">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="f1b1f-109">공격자는 다음을 시도할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-109">Attackers may attempt to:</span></span>

* <span data-ttu-id="f1b1f-110">[서비스 거부](/windows-hardware/drivers/ifs/denial-of-service) 공격을 실행.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-110">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="f1b1f-111">바이러스 또는 맬웨어를 업로드.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-111">Upload viruses or malware.</span></span>
* <span data-ttu-id="f1b1f-112">다른 방법으로 네트워크 및 서버를 손상.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-112">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="f1b1f-113">공격이 성공할 가능성을 줄이는 보안 단계는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-113">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="f1b1f-114">시스템의 전용 파일 업로드 영역(바람직하게는 시스템 드라이브가 아닌 위치)에 파일을 업로드합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-114">Upload files to a dedicated file upload area on the system, preferably to a non-system drive.</span></span> <span data-ttu-id="f1b1f-115">전용 위치를 사용하면 업로드된 파일에 대한 보안 제한을 더 쉽게 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-115">Using a dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="f1b1f-116">파일 업로드 위치에 대한 실행 권한을 사용하지 않도록 설정합니다.&dagger;</span><span class="sxs-lookup"><span data-stu-id="f1b1f-116">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="f1b1f-117">업로드된 파일을 앱과 동일한 디렉터리 트리에 보관하지 마세요.&dagger;</span><span class="sxs-lookup"><span data-stu-id="f1b1f-117">Never persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="f1b1f-118">앱에 의해 결정된 안전한 파일 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-118">Use a safe file name determined by the app.</span></span> <span data-ttu-id="f1b1f-119">사용자가 제공한 파일 이름이나 업로드된 파일의 신뢰할 수 없는 파일 이름을 사용하지 마세요.&dagger; UI 또는 로깅 메시지에서 신뢰할 수 없는 파일 이름을 표시하려면 값을 HTML로 인코딩하세요.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-119">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; To display an untrusted file name in a UI or in a logging message, HTML-encode the value.</span></span>
* <span data-ttu-id="f1b1f-120">특정 승인된 파일 확장명 집합만 허용합니다.&dagger;</span><span class="sxs-lookup"><span data-stu-id="f1b1f-120">Only allow a specific set of approved file extensions.&dagger;</span></span>
* <span data-ttu-id="f1b1f-121">파일 형식 서명을 확인하여 사용자가 위장한 파일을 업로드하지 못하도록 합니다.&dagger; 예를 들어 사용자가 *.txt* 확장명을 사용하여 *.exe* 파일을 업로드할 수 없어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-121">Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension.</span></span>
* <span data-ttu-id="f1b1f-122">서버에서도 클라이언트 쪽 검사가 수행되는지 확인합니다.&dagger; 클라이언트 쪽 검사는 피하기 쉽습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-122">Verify that client-side checks are also performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="f1b1f-123">업로드된 파일의 크기를 확인하고 예상보다 큰 업로드를 방지합니다.&dagger;</span><span class="sxs-lookup"><span data-stu-id="f1b1f-123">Check the size of an uploaded file and prevent uploads that are larger than expected.&dagger;</span></span>
* <span data-ttu-id="f1b1f-124">업로드된 파일이 같은 이름의 파일을 덮어쓰면 안 되는 경우 파일을 업로드하기 전에 데이터베이스 또는 실제 스토리지에서 파일 이름을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-124">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="f1b1f-125">**파일이 저장되기 전에 업로드된 콘텐츠에 대해 바이러스/맬웨어 스캐너를 실행합니다.**</span><span class="sxs-lookup"><span data-stu-id="f1b1f-125">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="f1b1f-126">&dagger;샘플 앱은 조건을 충족하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-126">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="f1b1f-127">시스템에 악성 코드를 업로드하는 행위는 흔히 다음을 수행할 수 있는 코드를 실행하기 위한 첫 단계가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-127">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="f1b1f-128">시스템을 완전히 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-128">Completely gain control of a system.</span></span>
> * <span data-ttu-id="f1b1f-129">시스템 작동이 중단되는 결과로 시스템을 오버로드합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-129">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="f1b1f-130">사용자 또는 시스템 데이터를 손상시킵니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-130">Compromise user or system data.</span></span>
> * <span data-ttu-id="f1b1f-131">공용 UI에 그래피티를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-131">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="f1b1f-132">사용자의 파일을 수락할 때 공격 노출 영역을 줄이는 방법에 대한 자세한 내용은 다음 리소스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-132">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="f1b1f-133">무제한 파일 업로드</span><span class="sxs-lookup"><span data-stu-id="f1b1f-133">Unrestricted File Upload</span></span>](https://www.owasp.org/index.php/Unrestricted_File_Upload)
> * [<span data-ttu-id="f1b1f-134">Azure Security: 사용자의 파일을 허용할 때 적절한 제어가 수행되는지 확인</span><span class="sxs-lookup"><span data-stu-id="f1b1f-134">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="f1b1f-135">샘플 앱의 예제를 포함하여 보안 조치를 구현하는 방법에 대한 자세한 내용은 [유효성 검사](#validation) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-135">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="f1b1f-136">스토리지 시나리오</span><span class="sxs-lookup"><span data-stu-id="f1b1f-136">Storage scenarios</span></span>

<span data-ttu-id="f1b1f-137">파일에 대한 일반적인 스토리지 옵션은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-137">Common storage options for files include:</span></span>

* <span data-ttu-id="f1b1f-138">데이터베이스</span><span class="sxs-lookup"><span data-stu-id="f1b1f-138">Database</span></span>

  * <span data-ttu-id="f1b1f-139">소용량 파일 업로드의 경우 데이터베이스는 실제 스토리지(파일 시스템 또는 네트워크 공유) 옵션보다 빠른 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-139">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="f1b1f-140">사용자 데이터에 대한 데이터베이스 레코드를 검색할 때 파일 콘텐츠(예: 아바타 이미지)를 동시에 제공할 수 있으므로 데이터베이스는 실제 스토리지 옵션보다 편리합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-140">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="f1b1f-141">데이터베이스는 데이터 스토리지 서비스를 사용하는 것보다 비용이 적게 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-141">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="f1b1f-142">실제 스토리지(파일 시스템 또는 네트워크 공유)</span><span class="sxs-lookup"><span data-stu-id="f1b1f-142">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="f1b1f-143">대용량 파일 업로드의 경우:</span><span class="sxs-lookup"><span data-stu-id="f1b1f-143">For large file uploads:</span></span>
    * <span data-ttu-id="f1b1f-144">데이터베이스 한도 때문에 업로드 크기가 제한될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-144">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="f1b1f-145">실제 스토리지는 데이터베이스 스토리지보다 경제적이지 않은 경우가 자주 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-145">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="f1b1f-146">실제 스토리지는 데이터 스토리지 서비스를 사용하는 것보다 비용이 적게 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-146">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="f1b1f-147">앱의 프로세스에는 스토리지 위치에 대한 읽기 및 쓰기 권한이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-147">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="f1b1f-148">**실행 권한을 부여하지 마세요.**</span><span class="sxs-lookup"><span data-stu-id="f1b1f-148">**Never grant execute permission.**</span></span>

* <span data-ttu-id="f1b1f-149">데이터 스토리지 서비스(예: [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="f1b1f-149">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="f1b1f-150">서비스는 일반적으로 단일 실패 지점에 노출되는 온-프레미스 솔루션에 비해 향상된 확장성 및 복원력을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-150">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="f1b1f-151">서비스는 대용량 스토리지 인프라 시나리오에서 비용이 더 저렴할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-151">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="f1b1f-152">자세한 내용은 [빠른 시작: .NET을 사용하여 개체 스토리지에 Blob 만들기](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="f1b1f-152">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="f1b1f-153">이 항목에서는 <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>를 보여 주지만, <xref:System.IO.Stream>에서 작업할 때 <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*>를 사용하여 <xref:System.IO.FileStream>을 Blob Storage에 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-153">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="f1b1f-154">파일 업로드 시나리오</span><span class="sxs-lookup"><span data-stu-id="f1b1f-154">File upload scenarios</span></span>

<span data-ttu-id="f1b1f-155">파일 업로드를 위한 일반적인 방법 두 가지는 버퍼링 및 스트리밍입니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-155">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="f1b1f-156">**버퍼링**</span><span class="sxs-lookup"><span data-stu-id="f1b1f-156">**Buffering**</span></span>

<span data-ttu-id="f1b1f-157">전체 파일을 파일 처리 또는 저장에 사용되는 파일의 C# 표현인 <xref:Microsoft.AspNetCore.Http.IFormFile>로 읽어 들입니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-157">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="f1b1f-158">파일 업로드에서 사용되는 리소스(디스크, 메모리)는 동시 파일 업로드 크기와 수에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-158">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="f1b1f-159">앱이 너무 많은 업로드를 버퍼링하려 할 경우 메모리 또는 디스크 공간이 부족하면 사이트의 작동이 중단됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-159">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="f1b1f-160">파일 업로드의 크기 또는 빈도로 인해 앱 리소스가 소진되는 경우 스트리밍을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-160">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="f1b1f-161">버퍼링된 단일 파일이 64KB를 초과하는 경우 메모리에서 디스크의 임시 파일로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-161">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="f1b1f-162">소용량 파일 버퍼링은 이 항목의 다음 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-162">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="f1b1f-163">실제 스토리지</span><span class="sxs-lookup"><span data-stu-id="f1b1f-163">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="f1b1f-164">데이터베이스</span><span class="sxs-lookup"><span data-stu-id="f1b1f-164">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="f1b1f-165">**스트리밍**</span><span class="sxs-lookup"><span data-stu-id="f1b1f-165">**Streaming**</span></span>

<span data-ttu-id="f1b1f-166">파일은 다중 파트 요청에서 수신되며 앱에서 직접 처리하거나 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-166">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="f1b1f-167">스트리밍은 성능을 크게 개선하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-167">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="f1b1f-168">스트리밍을 통해 파일을 업로드하면 메모리 또는 디스크 공간에 대한 요구가 줄어듭니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-168">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="f1b1f-169">대용량 파일 스트리밍은 [스트리밍을 사용하여 대용량 파일 업로드](#upload-large-files-with-streaming) 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-169">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="f1b1f-170">버퍼링된 모델 바인딩을 사용하여 소용량 파일을 실제 스토리지에 업로드</span><span class="sxs-lookup"><span data-stu-id="f1b1f-170">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="f1b1f-171">소용량 파일을 업로드하려면 다중 파트 양식을 사용하거나 JavaScript를 사용하여 POST 요청을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-171">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="f1b1f-172">다음 예제에서는 Razor Pages 양식을 사용하여 단일 파일(샘플 앱의 *BufferedSingleFileUploadPhysical*)을 업로드하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-172">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="f1b1f-173">다음 예제는 이전 예제와 비슷하지만 다음과 같은 차이가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-173">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="f1b1f-174">양식의 데이터를 제출하는 데 JavaScript([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API))가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-174">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="f1b1f-175">유효성 검사를 수행하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-175">There's no validation.</span></span>

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

<span data-ttu-id="f1b1f-176">[Fetch API](https://caniuse.com/#feat=fetch)를 지원하지 않는 클라이언트에 대해 JavaScript로 양식 POST를 수행하려면 다음 방법 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-176">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="f1b1f-177">Fetch Polyfill(예: [window.fetch polyfill (github/fetch)](https://github.com/github/fetch))을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-177">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="f1b1f-178">`XMLHttpRequest`을 사용하십시오.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-178">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="f1b1f-179">예:</span><span class="sxs-lookup"><span data-stu-id="f1b1f-179">For example:</span></span>

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

<span data-ttu-id="f1b1f-180">파일 업로드를 지원하려면 HTML 양식에서 `multipart/form-data`의 인코딩 유형(`enctype`)을 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-180">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="f1b1f-181">`files` 입력 요소가 다중 파일 업로드를 지원하려면 `<input>` 요소에 `multiple` 특성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-181">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="f1b1f-182">서버에 업로드된 개별 파일은 <xref:Microsoft.AspNetCore.Http.IFormFile>을 사용하여 [모델 바인딩](xref:mvc/models/model-binding)을 통해 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-182">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="f1b1f-183">샘플 앱은 데이터베이스 및 실제 스토리지 시나리오에 대한 다중 버퍼링된 파일 업로드를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-183">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

> [!WARNING]
> <span data-ttu-id="f1b1f-184">유효성 검사 없이 <xref:Microsoft.AspNetCore.Http.IFormFile>의 `FileName` 속성을 의존하거나 신뢰하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-184">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="f1b1f-185">`FileName` 속성은 반드시 값을 HTML 인코딩한 후 표시 목적으로만 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-185">The `FileName` property should only be used for display purposes and only after HTML encoding the value.</span></span>
>
> <span data-ttu-id="f1b1f-186">지금까지 제시한 예제에서는 보안 고려 사항을 감안하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-186">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="f1b1f-187">추가 정보는 다음 섹션과 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)에서 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-187">Additional information is provided by the following sections and the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="f1b1f-188">보안 고려 사항</span><span class="sxs-lookup"><span data-stu-id="f1b1f-188">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="f1b1f-189">유효성 검사</span><span class="sxs-lookup"><span data-stu-id="f1b1f-189">Validation</span></span>](#validation)

<span data-ttu-id="f1b1f-190">모델 바인딩 및 <xref:Microsoft.AspNetCore.Http.IFormFile>을 사용하여 파일을 업로드하는 경우 작업 메서드에서 다음을 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-190">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="f1b1f-191">단일 <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-191">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="f1b1f-192">여러 파일을 나타내는 다음 컬렉션 중 하나.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-192">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="f1b1f-193">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="f1b1f-193">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="f1b1f-194">바인딩은 이름을 기준으로 양식 파일을 일치시킵니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-194">Binding matches form files by name.</span></span> <span data-ttu-id="f1b1f-195">예를 들어 `<input type="file" name="formFile">`의 HTML `name` 값은 바인딩된 C# 매개 변수/속성(`FormFile`)과 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-195">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="f1b1f-196">자세한 내용은 [이름 특성 값을 POST 메서드의 매개 변수 이름과 일치](#match-name-attribute-value-to-parameter-name-of-post-method) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-196">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="f1b1f-197">다음 예제가 하는 일:</span><span class="sxs-lookup"><span data-stu-id="f1b1f-197">The following example:</span></span>

* <span data-ttu-id="f1b1f-198">하나 이상의 업로드된 파일을 반복합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-198">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="f1b1f-199">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*)을 사용하여 파일 이름을 포함하는 파일 전체 경로를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-199">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="f1b1f-200">앱에서 생성한 파일 이름을 사용하여 로컬 파일 시스템에 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-200">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="f1b1f-201">업로드된 파일의 총 수와 크기를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-201">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="f1b1f-202">`Path.GetRandomFileName`을 사용하여 경로 없이 파일 이름을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-202">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="f1b1f-203">다음 예제에서는 경로를 구성에서 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-203">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="f1b1f-204"><xref:System.IO.FileStream>에 전달된 경로는 *반드시* 파일 이름을 포함해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-204">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="f1b1f-205">파일 이름을 제공하지 않으면 런타임에 <xref:System.UnauthorizedAccessException>이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-205">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="f1b1f-206"><xref:Microsoft.AspNetCore.Http.IFormFile> 기술을 사용하여 업로드된 파일은 처리 전에 서버의 메모리나 디스크에 버퍼링됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-206">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="f1b1f-207">작업 메서드 내부에서 <xref:Microsoft.AspNetCore.Http.IFormFile> 내용을 <xref:System.IO.Stream>으로 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-207">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="f1b1f-208">로컬 파일 시스템 외에도 파일을 네트워크 공유 또는 파일 스토리지 서비스(예: [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs))에 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-208">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="f1b1f-209">업로드를 위해 여러 파일에 대해 루프를 실행하고 안전한 파일 이름을 사용하는 또 하나의 예제는 샘플 앱에서 *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs*를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-209">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="f1b1f-210">이전 임시 파일을 삭제하지 않고 65,535개를 초과하는 파일을 만들면 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*)이 <xref:System.IO.IOException>을 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-210">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="f1b1f-211">65535개 파일 제한은 서버당 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-211">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="f1b1f-212">Windows OS에서 이 제한에 대한 자세한 내용은 다음 항목의 설명을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-212">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="f1b1f-213">GetTempFileNameA 함수</span><span class="sxs-lookup"><span data-stu-id="f1b1f-213">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="f1b1f-214">버퍼링된 모델 바인딩을 사용하여 소용량 파일을 데이터베이스에 업로드</span><span class="sxs-lookup"><span data-stu-id="f1b1f-214">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="f1b1f-215">[Entity Framework](/ef/core/index)를 사용하여 데이터베이스에 이진 파일 데이터를 저장하려면 엔터티에서 <xref:System.Byte> 배열 속성을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-215">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="f1b1f-216"><xref:Microsoft.AspNetCore.Http.IFormFile>을 포함하는 클래스에 대한 페이지 모델 속성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-216">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="f1b1f-217"><xref:Microsoft.AspNetCore.Http.IFormFile>은 작업 메서드 매개 변수 또는 바운딩 모델 속성으로 직접 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-217"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="f1b1f-218">위 예제에서는 바인딩된 모델 속성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-218">The prior example uses a bound model property.</span></span>

<span data-ttu-id="f1b1f-219">`FileUpload`는 Razor Pages 양식에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-219">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="f1b1f-220">양식이 서버에 POST되면 <xref:Microsoft.AspNetCore.Http.IFormFile>을 스트림으로 복사하여 데이터베이스에 바이트 배열로 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-220">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="f1b1f-221">다음 예제에서 `_dbContext`는 앱의 데이터베이스 컨텍스트를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-221">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="f1b1f-222">위의 예제는 샘플 앱에서 보여 주는 시나리오와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-222">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="f1b1f-223">*Pages/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f1b1f-223">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="f1b1f-224">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="f1b1f-224">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="f1b1f-225">관계형 데이터베이스에 이진 데이터를 저장하는 경우 성능에 나쁜 영향을 줄 수 있으므로 주의하세요.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-225">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="f1b1f-226">유효성 검사 없이 <xref:Microsoft.AspNetCore.Http.IFormFile>의 `FileName` 속성을 의존하거나 신뢰하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-226">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="f1b1f-227">`FileName` 속성은 반드시 HTML 인코딩 후 표시 목적으로만 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-227">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="f1b1f-228">제시한 예제에서는 보안 고려 사항을 감안하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-228">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="f1b1f-229">추가 정보는 다음 섹션과 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)에서 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-229">Additional information is provided by the following sections and the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="f1b1f-230">보안 고려 사항</span><span class="sxs-lookup"><span data-stu-id="f1b1f-230">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="f1b1f-231">유효성 검사</span><span class="sxs-lookup"><span data-stu-id="f1b1f-231">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="f1b1f-232">스트리밍을 사용하여 대용량 파일 업로드</span><span class="sxs-lookup"><span data-stu-id="f1b1f-232">Upload large files with streaming</span></span>

<span data-ttu-id="f1b1f-233">다음 예제에서는 JavaScript를 사용하여 컨트롤러 작업에 파일을 스트리밍하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-233">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="f1b1f-234">사용자 지정 필터 특성을 사용하여 파일의 위조 방지 토큰이 생성되고 요청 본문 대신 클라이언트 HTTP 헤더에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-234">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="f1b1f-235">작업 메서드에서 업로드된 데이터를 직접 처리하므로 다른 사용자 지정 필터에서 형식 모델 바인딩을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-235">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="f1b1f-236">작업 내에서 양식의 콘텐츠는 각 개별 `MultipartSection`을 읽고 적절하게 파일을 처리하거나 콘텐츠를 저장하는 `MultipartReader`를 사용하여 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-236">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="f1b1f-237">다중 파트 섹션을 읽은 후 작업에서 자체 모델 바인딩을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-237">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="f1b1f-238">초기 페이지 응답에서는 양식을 로드하고 위조 방지 토큰을 쿠키에 저장합니다(`GenerateAntiforgeryTokenCookieAttribute` 특성을 통해).</span><span class="sxs-lookup"><span data-stu-id="f1b1f-238">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="f1b1f-239">이 특성은 ASP.NET Core의 기본 제공 [위조 방지 지원](xref:security/anti-request-forgery)을 사용하여 요청 토큰으로 쿠키를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-239">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="f1b1f-240">`DisableFormValueModelBindingAttribute`은 모델 바인딩을 사용하지 않도록 설정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-240">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="f1b1f-241">샘플 앱에서 `GenerateAntiforgeryTokenCookieAttribute` 및 `DisableFormValueModelBindingAttribute`는 [Razor Pages 규칙](xref:razor-pages/razor-pages-conventions)을 사용하여 `Startup.ConfigureServices` 내 `/StreamedSingleFileUploadDb` 및 `/StreamedSingleFileUploadPhysical` 페이지의 애플리케이션 모델에 필터로 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-241">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=8-11,17-20)]

<span data-ttu-id="f1b1f-242">모델 바인딩은 양식을 읽지 않으므로 양식에서 바인딩된 매개 변수가 바인딩되지 않습니다(쿼리, 경로 및 헤더는 계속 작동함).</span><span class="sxs-lookup"><span data-stu-id="f1b1f-242">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="f1b1f-243">작업 메서드는 `Request` 속성으로 직접 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-243">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="f1b1f-244">`MultipartReader`는 각 섹션을 읽는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-244">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="f1b1f-245">키/값 데이터는 `KeyValueAccumulator`에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-245">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="f1b1f-246">다중 파트 섹션을 읽은 후 `KeyValueAccumulator`의 내용이 양식 데이터를 모델 형식으로 바인딩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-246">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="f1b1f-247">EF Core를 사용하여 데이터베이스에 스트리밍하기 위한 전체 `StreamingController.UploadDatabase` 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-247">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="f1b1f-248">`MultipartRequestHelper`(*Utilities/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="f1b1f-248">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="f1b1f-249">실제 위치로 스트리밍하기 위한 전체 `StreamingController.UploadPhysical` 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-249">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="f1b1f-250">샘플 앱에서 유효성 검사는 `FileHelpers.ProcessStreamedFile`에 의해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-250">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="f1b1f-251">유효성 검사</span><span class="sxs-lookup"><span data-stu-id="f1b1f-251">Validation</span></span>

<span data-ttu-id="f1b1f-252">샘플 앱의 `FileHelpers` 클래스는 버퍼링된 <xref:Microsoft.AspNetCore.Http.IFormFile> 및 스트리밍된 파일 업로드에 대한 여러 검사를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-252">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="f1b1f-253">샘플 앱에서 <xref:Microsoft.AspNetCore.Http.IFormFile> 버퍼링된 파일 업로드를 처리하려면 *Utilities/FileHelpers.cs* 파일에서 `ProcessFormFile` 메서드를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-253">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="f1b1f-254">스트리밍된 파일의 처리는 동일한 파일의 `ProcessStreamedFile` 메서드를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-254">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="f1b1f-255">샘플 앱에서 보여 주는 유효성 검사 처리 메서드는 업로드된 파일의 내용을 검사하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-255">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="f1b1f-256">대부분의 프로덕션 시나리오에서는 사용자 또는 다른 시스템에서 파일을 사용할 수 있도록 하기 전에 파일에 바이러스/맬웨어 스캐너 API를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-256">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="f1b1f-257">항목 샘플에서는 유효성 검사 기술에 대한 작업 예제를 제공하지만, 다음과 같은 경우가 아니면 프로덕션 앱에서 `FileHelpers` 클래스를 구현하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-257">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="f1b1f-258">구현을 완전히 이해합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-258">Fully understand the implementation.</span></span>
> * <span data-ttu-id="f1b1f-259">앱의 환경 및 사양에 맞게 구현을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-259">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="f1b1f-260">**이러한 요구 사항을 해결하지 않고 앱에서 보안 코드를 무분별하게 구현해서는 안 됩니다.**</span><span class="sxs-lookup"><span data-stu-id="f1b1f-260">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="f1b1f-261">내용 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="f1b1f-261">Content validation</span></span>

<span data-ttu-id="f1b1f-262">**업로드된 콘텐츠에 타사 바이러스/맬웨어 검사 API를 사용합니다.**</span><span class="sxs-lookup"><span data-stu-id="f1b1f-262">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="f1b1f-263">대용량 시나리오에서 파일 검사는 많은 서버 리소스를 요구합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-263">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="f1b1f-264">파일 검사로 인해 요청 처리 성능이 저하된 경우 검사 작업을 [백그라운드 서비스](xref:fundamentals/host/hosted-services)로 오프로드하는 것이 좋습니다.이 경우 앱 서버와 다른 서버에서 실행되는 서비스가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-264">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="f1b1f-265">일반적으로 업로드된 파일은 백그라운드 바이러스 검사 프로그램에서 검사될 때까지 격리된 영역에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-265">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="f1b1f-266">파일이 전달되면 파일이 일반 파일 스토리지 위치로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-266">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="f1b1f-267">이러한 단계는 일반적으로 파일의 검사 상태를 나타내는 데이터베이스 레코드와 함께 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-267">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="f1b1f-268">이러한 방법을 사용하여 앱 및 앱 서버는 요청에 응답하는 데 집중합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-268">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="f1b1f-269">파일 확장명 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="f1b1f-269">File extension validation</span></span>

<span data-ttu-id="f1b1f-270">업로드된 파일의 확장명을 허용된 확장명 목록에 따라 확인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-270">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="f1b1f-271">예:</span><span class="sxs-lookup"><span data-stu-id="f1b1f-271">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="f1b1f-272">파일 서명 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="f1b1f-272">File signature validation</span></span>

<span data-ttu-id="f1b1f-273">파일의 서명은 파일 시작 부분의 처음 몇 바이트에 의해 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-273">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="f1b1f-274">이러한 바이트는 확장명이 파일 내용과 일치하는지 여부를 나타내는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-274">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="f1b1f-275">샘플 앱은 몇 가지 일반적인 파일 형식에 대한 파일 서명을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-275">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="f1b1f-276">다음 예제에서는 파일에서 JPEG 이미지 파일에 대한 서명을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-276">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="f1b1f-277">추가 파일 서명을 얻으려면 [파일 서명 데이터베이스](https://www.filesignatures.net/) 및 공식 파일 사양을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-277">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="f1b1f-278">파일 이름 보안</span><span class="sxs-lookup"><span data-stu-id="f1b1f-278">File name security</span></span>

<span data-ttu-id="f1b1f-279">실제 스토리지에 파일을 저장하는 데 클라이언트에서 제공하는 파일 이름을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-279">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="f1b1f-280">[Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) 또는 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*)을 사용하여 임시 스토리지에 대한 전체 경로(파일 이름을 포함)를 만들어 파일에 대한 안전한 파일 이름을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-280">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="f1b1f-281">Razor는 표시를 위해 속성 값을 자동으로 HTML 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-281">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="f1b1f-282">다음 코드는 안전하게 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-282">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="f1b1f-283">Razor 외부에서는 항상 사용자 요청의 파일 이름 콘텐츠를 <xref:System.Net.WebUtility.HtmlEncode*>합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-283">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="f1b1f-284">많은 구현에서 파일 존재 여부에 대한 확인이 포함되어야 합니다. 그렇지 않으면 파일이 같은 이름의 파일을 덮어씁니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-284">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="f1b1f-285">앱의 사양을 충족하는 추가 논리를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-285">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="f1b1f-286">크기 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="f1b1f-286">Size validation</span></span>

<span data-ttu-id="f1b1f-287">업로드된 파일의 크기를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-287">Limit the size of uploaded files.</span></span>

<span data-ttu-id="f1b1f-288">샘플 앱에서 파일 크기는 2MB(바이트 단위로 표시)로 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-288">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="f1b1f-289">이 제한은 *appsettings.json* 파일의 [구성](xref:fundamentals/configuration/index)을 통해 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-289">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="f1b1f-290">`FileSizeLimit`은 `PageModel` 클래스에 삽입됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-290">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="f1b1f-291">파일 크기가 제한을 초과하는 파일은 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-291">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="f1b1f-292">이름 특성 값을 POST 메서드의 매개 변수 이름과 일치</span><span class="sxs-lookup"><span data-stu-id="f1b1f-292">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="f1b1f-293">양식 데이터를 POST하거나 JavaScript의 `FormData`를 직접 사용하는 비 Razor 양식에서 양식의 요소 또는 `FormData`에 지정된 이름은 컨트롤러의 작업에서 매개 변수 이름과 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-293">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="f1b1f-294">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="f1b1f-294">In the following example:</span></span>

* <span data-ttu-id="f1b1f-295">`<input>` 요소를 사용하는 경우 `name` 특성은 값 `battlePlans`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-295">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="f1b1f-296">JavaScript에서 `FormData`를 사용하는 경우 이름이 값 `battlePlans`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-296">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="f1b1f-297">C# 메서드(`battlePlans`)의 매개 변수와 일치하는 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-297">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="f1b1f-298">이름이 `Upload`인 Razor Pages 페이지 처리기 메서드:</span><span class="sxs-lookup"><span data-stu-id="f1b1f-298">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="f1b1f-299">MVC POST 컨트롤러 작업 메서드:</span><span class="sxs-lookup"><span data-stu-id="f1b1f-299">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="f1b1f-300">서버 및 앱 구성</span><span class="sxs-lookup"><span data-stu-id="f1b1f-300">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="f1b1f-301">다중 파트 본문 길이 제한</span><span class="sxs-lookup"><span data-stu-id="f1b1f-301">Multipart body length limit</span></span>

<span data-ttu-id="f1b1f-302"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>에서 각 다중 파트 본문의 길이에 대한 제한을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-302"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="f1b1f-303">양식 섹션이 이 제한을 초과하면 구문 분석할 때 <xref:System.IO.InvalidDataException>이 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-303">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="f1b1f-304">기본값은 134,217,728(128MB)입니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-304">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="f1b1f-305">`Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 설정을 사용하여 제한을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-305">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="f1b1f-306"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>는 단일 페이지 또는 작업에 대해 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>을 설정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-306"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="f1b1f-307">Razor Pages 앱에서 `Startup.ConfigureServices`의 [규칙](xref:razor-pages/razor-pages-conventions)을 사용하여 필터를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-307">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="f1b1f-308">Razor Pages 앱 또는 MVC 앱에서 페이지 모델 또는 작업 메서드에 필터를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-308">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="f1b1f-309">Kestrel 최대 요청 본문 크기</span><span class="sxs-lookup"><span data-stu-id="f1b1f-309">Kestrel maximum request body size</span></span>

<span data-ttu-id="f1b1f-310">Kestrel에서 호스트되는 앱의 경우 기본 최대 요청 본문 크기는 30,000,000바이트(약 28.6MB)입니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-310">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="f1b1f-311">[MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel 서버 옵션을 사용하 여 제한을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-311">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="f1b1f-312"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>는 단일 페이지 또는 작업에 대해 [ MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size)를 설정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-312"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="f1b1f-313">Razor Pages 앱에서 `Startup.ConfigureServices`의 [규칙](xref:razor-pages/razor-pages-conventions)을 사용하여 필터를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-313">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="f1b1f-314">Razor Pages 앱 또는 MVC 앱에서 페이지 처리기 모델 또는 작업 메서드에 필터를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-314">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="f1b1f-315">`RequestSizeLimitAttribute`은 [@attribute](xref:mvc/views/razor#attribute) Razor 지시문을 사용하여 적용할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-315">The `RequestSizeLimitAttribute` can also be applied using the [@attribute](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="f1b1f-316">기타 Kestrel 제한</span><span class="sxs-lookup"><span data-stu-id="f1b1f-316">Other Kestrel limits</span></span>

<span data-ttu-id="f1b1f-317">다른 Kestrel 제한이 Kestrel에서 호스트되는 앱에 적용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-317">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="f1b1f-318">최대 클라이언트 연결</span><span class="sxs-lookup"><span data-stu-id="f1b1f-318">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="f1b1f-319">요청 및 응답 데이터 속도</span><span class="sxs-lookup"><span data-stu-id="f1b1f-319">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="f1b1f-320">IIS 콘텐츠 길이 제한</span><span class="sxs-lookup"><span data-stu-id="f1b1f-320">IIS content length limit</span></span>

<span data-ttu-id="f1b1f-321">기본 요청 제한(`maxAllowedContentLength`)은 30,000,000만 바이트(약 28.6MB)입니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-321">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="f1b1f-322">*web.config* 파일에서 제한을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-322">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="f1b1f-323">이 설정은 IIS에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-323">This setting only applies to IIS.</span></span> <span data-ttu-id="f1b1f-324">Kestrel에서 호스팅하는 경우 기본적으로 동작이 발생하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-324">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="f1b1f-325">자세한 내용은 [요청 제한 \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-325">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="f1b1f-326">ASP.NET Core 모듈의 제한 사항 또는 IIS 요청 필터링 모듈의 존재로 인해 업로드가 2GB 또는 4GB로 제한될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-326">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="f1b1f-327">자세한 내용은 [크기가 2GB 이상인 파일을 업로드할 수 없음(aspnet/AspNetCore #2711)](https://github.com/aspnet/AspNetCore/issues/2711)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-327">For more information, see [Unable to upload file greater than 2GB in size (aspnet/AspNetCore #2711)](https://github.com/aspnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="f1b1f-328">문제 해결</span><span class="sxs-lookup"><span data-stu-id="f1b1f-328">Troubleshoot</span></span>

<span data-ttu-id="f1b1f-329">다음은 파일 업로드 및 가능한 솔루션을 사용하여 작업할 때 자주 발생하는 몇 가지 일반적인 문제입니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-329">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="f1b1f-330">IIS 서버에 배포할 때 찾을 수 없음 오류</span><span class="sxs-lookup"><span data-stu-id="f1b1f-330">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="f1b1f-331">다음 오류는 업로드된 파일이 서버의 구성된 콘텐츠 길이를 초과했음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-331">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="f1b1f-332">제한을 높이는 방법에 대한 자세한 내용은 [IIS 콘텐츠 길이 제한](#iis-content-length-limit) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-332">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="f1b1f-333">연결 실패</span><span class="sxs-lookup"><span data-stu-id="f1b1f-333">Connection failure</span></span>

<span data-ttu-id="f1b1f-334">연결 오류 및 서버 연결 다시 설정은 업로드된 파일이 Kestrel의 최대 요청 본문 크기를 초과함을 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-334">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="f1b1f-335">자세한 내용은 [Kestrel 최대 요청 본문 크기](#kestrel-maximum-request-body-size) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-335">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="f1b1f-336">Kestrel 클라이언트 연결 제한을 조정해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-336">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="f1b1f-337">IFormFile을 사용한 Null 참조 예외</span><span class="sxs-lookup"><span data-stu-id="f1b1f-337">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="f1b1f-338">컨트롤러에서 <xref:Microsoft.AspNetCore.Http.IFormFile>을 사용하여 업로드된 파일을 수락하지만 값이 `null`이면 HTML 양식에서 `multipart/form-data`의 `enctype` 값을 지정하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-338">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="f1b1f-339">`<form>` 요소에서 이 특성이 설정되지 않으면 파일 업로드가 실행되지 않고 바인딩된 <xref:Microsoft.AspNetCore.Http.IFormFile> 인수는 모두 `null`이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-339">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="f1b1f-340">또한 [양식 데이터의 업로드 이름 지정이 앱의 이름 지정과 일치](#match-name-attribute-value-to-parameter-name-of-post-method)하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-340">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f1b1f-341">ASP.NET Core는 소용량 파일의 경우에는 버퍼링된 모델 바인딩을 사용하여 하나 이상의 파일을 업로드하고, 대용량 파일의 경우에는 버퍼링되지 않은 스트리밍을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-341">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="f1b1f-342">[예제 코드 살펴보기 및 다운로드](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([다운로드 방법](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f1b1f-342">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="f1b1f-343">보안 고려 사항</span><span class="sxs-lookup"><span data-stu-id="f1b1f-343">Security considerations</span></span>

<span data-ttu-id="f1b1f-344">사용자에게 서버에 파일을 업로드하는 기능을 제공할 때는 주의해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-344">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="f1b1f-345">공격자는 다음을 시도할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-345">Attackers may attempt to:</span></span>

* <span data-ttu-id="f1b1f-346">[서비스 거부](/windows-hardware/drivers/ifs/denial-of-service) 공격을 실행.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-346">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="f1b1f-347">바이러스 또는 맬웨어를 업로드.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-347">Upload viruses or malware.</span></span>
* <span data-ttu-id="f1b1f-348">다른 방법으로 네트워크 및 서버를 손상.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-348">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="f1b1f-349">공격이 성공할 가능성을 줄이는 보안 단계는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-349">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="f1b1f-350">시스템의 전용 파일 업로드 영역(바람직하게는 시스템 드라이브가 아닌 위치)에 파일을 업로드합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-350">Upload files to a dedicated file upload area on the system, preferably to a non-system drive.</span></span> <span data-ttu-id="f1b1f-351">전용 위치를 사용하면 업로드된 파일에 대한 보안 제한을 더 쉽게 적용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-351">Using a dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="f1b1f-352">파일 업로드 위치에 대한 실행 권한을 사용하지 않도록 설정합니다.&dagger;</span><span class="sxs-lookup"><span data-stu-id="f1b1f-352">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="f1b1f-353">업로드된 파일을 앱과 동일한 디렉터리 트리에 보관하지 마세요.&dagger;</span><span class="sxs-lookup"><span data-stu-id="f1b1f-353">Never persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="f1b1f-354">앱에 의해 결정된 안전한 파일 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-354">Use a safe file name determined by the app.</span></span> <span data-ttu-id="f1b1f-355">사용자가 제공한 파일 이름이나 업로드된 파일의 신뢰할 수 없는 파일 이름을 사용하지 마세요.&dagger; UI 또는 로깅 메시지에서 신뢰할 수 없는 파일 이름을 표시하려면 값을 HTML로 인코딩하세요.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-355">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; To display an untrusted file name in a UI or in a logging message, HTML-encode the value.</span></span>
* <span data-ttu-id="f1b1f-356">특정 승인된 파일 확장명 집합만 허용합니다.&dagger;</span><span class="sxs-lookup"><span data-stu-id="f1b1f-356">Only allow a specific set of approved file extensions.&dagger;</span></span>
* <span data-ttu-id="f1b1f-357">파일 형식 서명을 확인하여 사용자가 위장한 파일을 업로드하지 못하도록 합니다.&dagger; 예를 들어 사용자가 *.txt* 확장명을 사용하여 *.exe* 파일을 업로드할 수 없어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-357">Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension.</span></span>
* <span data-ttu-id="f1b1f-358">서버에서도 클라이언트 쪽 검사가 수행되는지 확인합니다.&dagger; 클라이언트 쪽 검사는 피하기 쉽습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-358">Verify that client-side checks are also performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="f1b1f-359">업로드된 파일의 크기를 확인하고 예상보다 큰 업로드를 방지합니다.&dagger;</span><span class="sxs-lookup"><span data-stu-id="f1b1f-359">Check the size of an uploaded file and prevent uploads that are larger than expected.&dagger;</span></span>
* <span data-ttu-id="f1b1f-360">업로드된 파일이 같은 이름의 파일을 덮어쓰면 안 되는 경우 파일을 업로드하기 전에 데이터베이스 또는 실제 스토리지에서 파일 이름을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-360">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="f1b1f-361">**파일이 저장되기 전에 업로드된 콘텐츠에 대해 바이러스/맬웨어 스캐너를 실행합니다.**</span><span class="sxs-lookup"><span data-stu-id="f1b1f-361">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="f1b1f-362">&dagger;샘플 앱은 조건을 충족하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-362">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="f1b1f-363">시스템에 악성 코드를 업로드하는 행위는 흔히 다음을 수행할 수 있는 코드를 실행하기 위한 첫 단계가 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-363">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="f1b1f-364">시스템을 완전히 제어합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-364">Completely gain control of a system.</span></span>
> * <span data-ttu-id="f1b1f-365">시스템 작동이 중단되는 결과로 시스템을 오버로드합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-365">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="f1b1f-366">사용자 또는 시스템 데이터를 손상시킵니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-366">Compromise user or system data.</span></span>
> * <span data-ttu-id="f1b1f-367">공용 UI에 그래피티를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-367">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="f1b1f-368">사용자의 파일을 수락할 때 공격 노출 영역을 줄이는 방법에 대한 자세한 내용은 다음 리소스를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-368">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="f1b1f-369">무제한 파일 업로드</span><span class="sxs-lookup"><span data-stu-id="f1b1f-369">Unrestricted File Upload</span></span>](https://www.owasp.org/index.php/Unrestricted_File_Upload)
> * [<span data-ttu-id="f1b1f-370">Azure Security: 사용자의 파일을 허용할 때 적절한 제어가 수행되는지 확인</span><span class="sxs-lookup"><span data-stu-id="f1b1f-370">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="f1b1f-371">샘플 앱의 예제를 포함하여 보안 조치를 구현하는 방법에 대한 자세한 내용은 [유효성 검사](#validation) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-371">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="f1b1f-372">스토리지 시나리오</span><span class="sxs-lookup"><span data-stu-id="f1b1f-372">Storage scenarios</span></span>

<span data-ttu-id="f1b1f-373">파일에 대한 일반적인 스토리지 옵션은 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-373">Common storage options for files include:</span></span>

* <span data-ttu-id="f1b1f-374">데이터베이스</span><span class="sxs-lookup"><span data-stu-id="f1b1f-374">Database</span></span>

  * <span data-ttu-id="f1b1f-375">소용량 파일 업로드의 경우 데이터베이스는 실제 스토리지(파일 시스템 또는 네트워크 공유) 옵션보다 빠른 경우가 많습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-375">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="f1b1f-376">사용자 데이터에 대한 데이터베이스 레코드를 검색할 때 파일 콘텐츠(예: 아바타 이미지)를 동시에 제공할 수 있으므로 데이터베이스는 실제 스토리지 옵션보다 편리합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-376">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="f1b1f-377">데이터베이스는 데이터 스토리지 서비스를 사용하는 것보다 비용이 적게 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-377">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="f1b1f-378">실제 스토리지(파일 시스템 또는 네트워크 공유)</span><span class="sxs-lookup"><span data-stu-id="f1b1f-378">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="f1b1f-379">대용량 파일 업로드의 경우:</span><span class="sxs-lookup"><span data-stu-id="f1b1f-379">For large file uploads:</span></span>
    * <span data-ttu-id="f1b1f-380">데이터베이스 한도 때문에 업로드 크기가 제한될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-380">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="f1b1f-381">실제 스토리지는 데이터베이스 스토리지보다 경제적이지 않은 경우가 자주 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-381">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="f1b1f-382">실제 스토리지는 데이터 스토리지 서비스를 사용하는 것보다 비용이 적게 들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-382">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="f1b1f-383">앱의 프로세스에는 스토리지 위치에 대한 읽기 및 쓰기 권한이 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-383">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="f1b1f-384">**실행 권한을 부여하지 마세요.**</span><span class="sxs-lookup"><span data-stu-id="f1b1f-384">**Never grant execute permission.**</span></span>

* <span data-ttu-id="f1b1f-385">데이터 스토리지 서비스(예: [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span><span class="sxs-lookup"><span data-stu-id="f1b1f-385">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="f1b1f-386">서비스는 일반적으로 단일 실패 지점에 노출되는 온-프레미스 솔루션에 비해 향상된 확장성 및 복원력을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-386">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="f1b1f-387">서비스는 대용량 스토리지 인프라 시나리오에서 비용이 더 저렴할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-387">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="f1b1f-388">자세한 내용은 [빠른 시작: .NET을 사용하여 개체 스토리지에 Blob 만들기](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span><span class="sxs-lookup"><span data-stu-id="f1b1f-388">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="f1b1f-389">이 항목에서는 <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>를 보여 주지만, <xref:System.IO.Stream>에서 작업할 때 <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*>를 사용하여 <xref:System.IO.FileStream>을 Blob Storage에 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-389">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="f1b1f-390">파일 업로드 시나리오</span><span class="sxs-lookup"><span data-stu-id="f1b1f-390">File upload scenarios</span></span>

<span data-ttu-id="f1b1f-391">파일 업로드를 위한 일반적인 방법 두 가지는 버퍼링 및 스트리밍입니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-391">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="f1b1f-392">**버퍼링**</span><span class="sxs-lookup"><span data-stu-id="f1b1f-392">**Buffering**</span></span>

<span data-ttu-id="f1b1f-393">전체 파일을 파일 처리 또는 저장에 사용되는 파일의 C# 표현인 <xref:Microsoft.AspNetCore.Http.IFormFile>로 읽어 들입니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-393">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="f1b1f-394">파일 업로드에서 사용되는 리소스(디스크, 메모리)는 동시 파일 업로드 크기와 수에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-394">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="f1b1f-395">앱이 너무 많은 업로드를 버퍼링하려 할 경우 메모리 또는 디스크 공간이 부족하면 사이트의 작동이 중단됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-395">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="f1b1f-396">파일 업로드의 크기 또는 빈도로 인해 앱 리소스가 소진되는 경우 스트리밍을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-396">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="f1b1f-397">버퍼링된 단일 파일이 64KB를 초과하는 경우 메모리에서 디스크의 임시 파일로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-397">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="f1b1f-398">소용량 파일 버퍼링은 이 항목의 다음 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-398">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="f1b1f-399">실제 스토리지</span><span class="sxs-lookup"><span data-stu-id="f1b1f-399">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="f1b1f-400">데이터베이스</span><span class="sxs-lookup"><span data-stu-id="f1b1f-400">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="f1b1f-401">**스트리밍**</span><span class="sxs-lookup"><span data-stu-id="f1b1f-401">**Streaming**</span></span>

<span data-ttu-id="f1b1f-402">파일은 다중 파트 요청에서 수신되며 앱에서 직접 처리하거나 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-402">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="f1b1f-403">스트리밍은 성능을 크게 개선하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-403">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="f1b1f-404">스트리밍을 통해 파일을 업로드하면 메모리 또는 디스크 공간에 대한 요구가 줄어듭니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-404">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="f1b1f-405">대용량 파일 스트리밍은 [스트리밍을 사용하여 대용량 파일 업로드](#upload-large-files-with-streaming) 섹션에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-405">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="f1b1f-406">버퍼링된 모델 바인딩을 사용하여 소용량 파일을 실제 스토리지에 업로드</span><span class="sxs-lookup"><span data-stu-id="f1b1f-406">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="f1b1f-407">소용량 파일을 업로드하려면 다중 파트 양식을 사용하거나 JavaScript를 사용하여 POST 요청을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-407">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="f1b1f-408">다음 예제에서는 Razor Pages 양식을 사용하여 단일 파일(샘플 앱의 *BufferedSingleFileUploadPhysical*)을 업로드하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-408">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="f1b1f-409">다음 예제는 이전 예제와 비슷하지만 다음과 같은 차이가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-409">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="f1b1f-410">양식의 데이터를 제출하는 데 JavaScript([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API))가 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-410">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="f1b1f-411">유효성 검사를 수행하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-411">There's no validation.</span></span>

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

<span data-ttu-id="f1b1f-412">[Fetch API](https://caniuse.com/#feat=fetch)를 지원하지 않는 클라이언트에 대해 JavaScript로 양식 POST를 수행하려면 다음 방법 중 하나를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-412">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="f1b1f-413">Fetch Polyfill(예: [window.fetch polyfill (github/fetch)](https://github.com/github/fetch))을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-413">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="f1b1f-414">`XMLHttpRequest`을 사용하십시오.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-414">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="f1b1f-415">예:</span><span class="sxs-lookup"><span data-stu-id="f1b1f-415">For example:</span></span>

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

<span data-ttu-id="f1b1f-416">파일 업로드를 지원하려면 HTML 양식에서 `multipart/form-data`의 인코딩 유형(`enctype`)을 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-416">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="f1b1f-417">`files` 입력 요소가 다중 파일 업로드를 지원하려면 `<input>` 요소에 `multiple` 특성을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-417">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="f1b1f-418">서버에 업로드된 개별 파일은 <xref:Microsoft.AspNetCore.Http.IFormFile>을 사용하여 [모델 바인딩](xref:mvc/models/model-binding)을 통해 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-418">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="f1b1f-419">샘플 앱은 데이터베이스 및 실제 스토리지 시나리오에 대한 다중 버퍼링된 파일 업로드를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-419">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

> [!WARNING]
> <span data-ttu-id="f1b1f-420">유효성 검사 없이 <xref:Microsoft.AspNetCore.Http.IFormFile>의 `FileName` 속성을 의존하거나 신뢰하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-420">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="f1b1f-421">`FileName` 속성은 반드시 값을 HTML 인코딩한 후 표시 목적으로만 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-421">The `FileName` property should only be used for display purposes and only after HTML encoding the value.</span></span>
>
> <span data-ttu-id="f1b1f-422">지금까지 제시한 예제에서는 보안 고려 사항을 감안하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-422">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="f1b1f-423">추가 정보는 다음 섹션과 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)에서 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-423">Additional information is provided by the following sections and the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="f1b1f-424">보안 고려 사항</span><span class="sxs-lookup"><span data-stu-id="f1b1f-424">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="f1b1f-425">유효성 검사</span><span class="sxs-lookup"><span data-stu-id="f1b1f-425">Validation</span></span>](#validation)

<span data-ttu-id="f1b1f-426">모델 바인딩 및 <xref:Microsoft.AspNetCore.Http.IFormFile>을 사용하여 파일을 업로드하는 경우 작업 메서드에서 다음을 허용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-426">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="f1b1f-427">단일 <xref:Microsoft.AspNetCore.Http.IFormFile>.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-427">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="f1b1f-428">여러 파일을 나타내는 다음 컬렉션 중 하나.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-428">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="f1b1f-429">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="f1b1f-429">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="f1b1f-430">바인딩은 이름을 기준으로 양식 파일을 일치시킵니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-430">Binding matches form files by name.</span></span> <span data-ttu-id="f1b1f-431">예를 들어 `<input type="file" name="formFile">`의 HTML `name` 값은 바인딩된 C# 매개 변수/속성(`FormFile`)과 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-431">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="f1b1f-432">자세한 내용은 [이름 특성 값을 POST 메서드의 매개 변수 이름과 일치](#match-name-attribute-value-to-parameter-name-of-post-method) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-432">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="f1b1f-433">다음 예제가 하는 일:</span><span class="sxs-lookup"><span data-stu-id="f1b1f-433">The following example:</span></span>

* <span data-ttu-id="f1b1f-434">하나 이상의 업로드된 파일을 반복합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-434">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="f1b1f-435">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*)을 사용하여 파일 이름을 포함하는 파일 전체 경로를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-435">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="f1b1f-436">앱에서 생성한 파일 이름을 사용하여 로컬 파일 시스템에 파일을 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-436">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="f1b1f-437">업로드된 파일의 총 수와 크기를 반환합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-437">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="f1b1f-438">`Path.GetRandomFileName`을 사용하여 경로 없이 파일 이름을 생성합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-438">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="f1b1f-439">다음 예제에서는 경로를 구성에서 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-439">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="f1b1f-440"><xref:System.IO.FileStream>에 전달된 경로는 *반드시* 파일 이름을 포함해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-440">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="f1b1f-441">파일 이름을 제공하지 않으면 런타임에 <xref:System.UnauthorizedAccessException>이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-441">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="f1b1f-442"><xref:Microsoft.AspNetCore.Http.IFormFile> 기술을 사용하여 업로드된 파일은 처리 전에 서버의 메모리나 디스크에 버퍼링됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-442">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="f1b1f-443">작업 메서드 내부에서 <xref:Microsoft.AspNetCore.Http.IFormFile> 내용을 <xref:System.IO.Stream>으로 액세스할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-443">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="f1b1f-444">로컬 파일 시스템 외에도 파일을 네트워크 공유 또는 파일 스토리지 서비스(예: [Azure Blob Storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs))에 저장할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-444">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="f1b1f-445">업로드를 위해 여러 파일에 대해 루프를 실행하고 안전한 파일 이름을 사용하는 또 하나의 예제는 샘플 앱에서 *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs*를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-445">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="f1b1f-446">이전 임시 파일을 삭제하지 않고 65,535개를 초과하는 파일을 만들면 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*)이 <xref:System.IO.IOException>을 throw합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-446">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="f1b1f-447">65535개 파일 제한은 서버당 제한입니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-447">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="f1b1f-448">Windows OS에서 이 제한에 대한 자세한 내용은 다음 항목의 설명을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-448">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="f1b1f-449">GetTempFileNameA 함수</span><span class="sxs-lookup"><span data-stu-id="f1b1f-449">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="f1b1f-450">버퍼링된 모델 바인딩을 사용하여 소용량 파일을 데이터베이스에 업로드</span><span class="sxs-lookup"><span data-stu-id="f1b1f-450">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="f1b1f-451">[Entity Framework](/ef/core/index)를 사용하여 데이터베이스에 이진 파일 데이터를 저장하려면 엔터티에서 <xref:System.Byte> 배열 속성을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-451">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="f1b1f-452"><xref:Microsoft.AspNetCore.Http.IFormFile>을 포함하는 클래스에 대한 페이지 모델 속성을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-452">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="f1b1f-453"><xref:Microsoft.AspNetCore.Http.IFormFile>은 작업 메서드 매개 변수 또는 바운딩 모델 속성으로 직접 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-453"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="f1b1f-454">위 예제에서는 바인딩된 모델 속성을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-454">The prior example uses a bound model property.</span></span>

<span data-ttu-id="f1b1f-455">`FileUpload`는 Razor Pages 양식에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-455">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="f1b1f-456">양식이 서버에 POST되면 <xref:Microsoft.AspNetCore.Http.IFormFile>을 스트림으로 복사하여 데이터베이스에 바이트 배열로 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-456">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="f1b1f-457">다음 예제에서 `_dbContext`는 앱의 데이터베이스 컨텍스트를 저장합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-457">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="f1b1f-458">위의 예제는 샘플 앱에서 보여 주는 시나리오와 비슷합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-458">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="f1b1f-459">*Pages/BufferedSingleFileUploadDb.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f1b1f-459">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="f1b1f-460">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="f1b1f-460">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="f1b1f-461">관계형 데이터베이스에 이진 데이터를 저장하는 경우 성능에 나쁜 영향을 줄 수 있으므로 주의하세요.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-461">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="f1b1f-462">유효성 검사 없이 <xref:Microsoft.AspNetCore.Http.IFormFile>의 `FileName` 속성을 의존하거나 신뢰하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-462">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="f1b1f-463">`FileName` 속성은 반드시 HTML 인코딩 후 표시 목적으로만 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-463">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="f1b1f-464">제시한 예제에서는 보안 고려 사항을 감안하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-464">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="f1b1f-465">추가 정보는 다음 섹션과 [샘플 앱](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)에서 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-465">Additional information is provided by the following sections and the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="f1b1f-466">보안 고려 사항</span><span class="sxs-lookup"><span data-stu-id="f1b1f-466">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="f1b1f-467">유효성 검사</span><span class="sxs-lookup"><span data-stu-id="f1b1f-467">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="f1b1f-468">스트리밍을 사용하여 대용량 파일 업로드</span><span class="sxs-lookup"><span data-stu-id="f1b1f-468">Upload large files with streaming</span></span>

<span data-ttu-id="f1b1f-469">다음 예제에서는 JavaScript를 사용하여 컨트롤러 작업에 파일을 스트리밍하는 방법을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-469">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="f1b1f-470">사용자 지정 필터 특성을 사용하여 파일의 위조 방지 토큰이 생성되고 요청 본문 대신 클라이언트 HTTP 헤더에 전달됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-470">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="f1b1f-471">작업 메서드에서 업로드된 데이터를 직접 처리하므로 다른 사용자 지정 필터에서 형식 모델 바인딩을 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-471">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="f1b1f-472">작업 내에서 양식의 콘텐츠는 각 개별 `MultipartSection`을 읽고 적절하게 파일을 처리하거나 콘텐츠를 저장하는 `MultipartReader`를 사용하여 읽습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-472">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="f1b1f-473">다중 파트 섹션을 읽은 후 작업에서 자체 모델 바인딩을 수행합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-473">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="f1b1f-474">초기 페이지 응답에서는 양식을 로드하고 위조 방지 토큰을 쿠키에 저장합니다(`GenerateAntiforgeryTokenCookieAttribute` 특성을 통해).</span><span class="sxs-lookup"><span data-stu-id="f1b1f-474">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="f1b1f-475">이 특성은 ASP.NET Core의 기본 제공 [위조 방지 지원](xref:security/anti-request-forgery)을 사용하여 요청 토큰으로 쿠키를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-475">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="f1b1f-476">`DisableFormValueModelBindingAttribute`은 모델 바인딩을 사용하지 않도록 설정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-476">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="f1b1f-477">샘플 앱에서 `GenerateAntiforgeryTokenCookieAttribute` 및 `DisableFormValueModelBindingAttribute`는 [Razor Pages 규칙](xref:razor-pages/razor-pages-conventions)을 사용하여 `Startup.ConfigureServices` 내 `/StreamedSingleFileUploadDb` 및 `/StreamedSingleFileUploadPhysical` 페이지의 애플리케이션 모델에 필터로 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-477">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="f1b1f-478">모델 바인딩은 양식을 읽지 않으므로 양식에서 바인딩된 매개 변수가 바인딩되지 않습니다(쿼리, 경로 및 헤더는 계속 작동함).</span><span class="sxs-lookup"><span data-stu-id="f1b1f-478">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="f1b1f-479">작업 메서드는 `Request` 속성으로 직접 작동합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-479">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="f1b1f-480">`MultipartReader`는 각 섹션을 읽는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-480">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="f1b1f-481">키/값 데이터는 `KeyValueAccumulator`에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-481">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="f1b1f-482">다중 파트 섹션을 읽은 후 `KeyValueAccumulator`의 내용이 양식 데이터를 모델 형식으로 바인딩하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-482">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="f1b1f-483">EF Core를 사용하여 데이터베이스에 스트리밍하기 위한 전체 `StreamingController.UploadDatabase` 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-483">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="f1b1f-484">`MultipartRequestHelper`(*Utilities/MultipartRequestHelper.cs*):</span><span class="sxs-lookup"><span data-stu-id="f1b1f-484">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="f1b1f-485">실제 위치로 스트리밍하기 위한 전체 `StreamingController.UploadPhysical` 메서드는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-485">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="f1b1f-486">샘플 앱에서 유효성 검사는 `FileHelpers.ProcessStreamedFile`에 의해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-486">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="f1b1f-487">유효성 검사</span><span class="sxs-lookup"><span data-stu-id="f1b1f-487">Validation</span></span>

<span data-ttu-id="f1b1f-488">샘플 앱의 `FileHelpers` 클래스는 버퍼링된 <xref:Microsoft.AspNetCore.Http.IFormFile> 및 스트리밍된 파일 업로드에 대한 여러 검사를 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-488">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="f1b1f-489">샘플 앱에서 <xref:Microsoft.AspNetCore.Http.IFormFile> 버퍼링된 파일 업로드를 처리하려면 *Utilities/FileHelpers.cs* 파일에서 `ProcessFormFile` 메서드를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-489">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="f1b1f-490">스트리밍된 파일의 처리는 동일한 파일의 `ProcessStreamedFile` 메서드를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-490">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="f1b1f-491">샘플 앱에서 보여 주는 유효성 검사 처리 메서드는 업로드된 파일의 내용을 검사하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-491">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="f1b1f-492">대부분의 프로덕션 시나리오에서는 사용자 또는 다른 시스템에서 파일을 사용할 수 있도록 하기 전에 파일에 바이러스/맬웨어 스캐너 API를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-492">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="f1b1f-493">항목 샘플에서는 유효성 검사 기술에 대한 작업 예제를 제공하지만, 다음과 같은 경우가 아니면 프로덕션 앱에서 `FileHelpers` 클래스를 구현하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-493">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="f1b1f-494">구현을 완전히 이해합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-494">Fully understand the implementation.</span></span>
> * <span data-ttu-id="f1b1f-495">앱의 환경 및 사양에 맞게 구현을 수정합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-495">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="f1b1f-496">**이러한 요구 사항을 해결하지 않고 앱에서 보안 코드를 무분별하게 구현해서는 안 됩니다.**</span><span class="sxs-lookup"><span data-stu-id="f1b1f-496">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="f1b1f-497">내용 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="f1b1f-497">Content validation</span></span>

<span data-ttu-id="f1b1f-498">**업로드된 콘텐츠에 타사 바이러스/맬웨어 검사 API를 사용합니다.**</span><span class="sxs-lookup"><span data-stu-id="f1b1f-498">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="f1b1f-499">대용량 시나리오에서 파일 검사는 많은 서버 리소스를 요구합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-499">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="f1b1f-500">파일 검사로 인해 요청 처리 성능이 저하된 경우 검사 작업을 [백그라운드 서비스](xref:fundamentals/host/hosted-services)로 오프로드하는 것이 좋습니다.이 경우 앱 서버와 다른 서버에서 실행되는 서비스가 있을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-500">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="f1b1f-501">일반적으로 업로드된 파일은 백그라운드 바이러스 검사 프로그램에서 검사될 때까지 격리된 영역에 저장됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-501">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="f1b1f-502">파일이 전달되면 파일이 일반 파일 스토리지 위치로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-502">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="f1b1f-503">이러한 단계는 일반적으로 파일의 검사 상태를 나타내는 데이터베이스 레코드와 함께 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-503">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="f1b1f-504">이러한 방법을 사용하여 앱 및 앱 서버는 요청에 응답하는 데 집중합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-504">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="f1b1f-505">파일 확장명 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="f1b1f-505">File extension validation</span></span>

<span data-ttu-id="f1b1f-506">업로드된 파일의 확장명을 허용된 확장명 목록에 따라 확인해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-506">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="f1b1f-507">예:</span><span class="sxs-lookup"><span data-stu-id="f1b1f-507">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="f1b1f-508">파일 서명 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="f1b1f-508">File signature validation</span></span>

<span data-ttu-id="f1b1f-509">파일의 서명은 파일 시작 부분의 처음 몇 바이트에 의해 결정됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-509">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="f1b1f-510">이러한 바이트는 확장명이 파일 내용과 일치하는지 여부를 나타내는 데 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-510">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="f1b1f-511">샘플 앱은 몇 가지 일반적인 파일 형식에 대한 파일 서명을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-511">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="f1b1f-512">다음 예제에서는 파일에서 JPEG 이미지 파일에 대한 서명을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-512">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="f1b1f-513">추가 파일 서명을 얻으려면 [파일 서명 데이터베이스](https://www.filesignatures.net/) 및 공식 파일 사양을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-513">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="f1b1f-514">파일 이름 보안</span><span class="sxs-lookup"><span data-stu-id="f1b1f-514">File name security</span></span>

<span data-ttu-id="f1b1f-515">실제 스토리지에 파일을 저장하는 데 클라이언트에서 제공하는 파일 이름을 사용하지 마세요.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-515">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="f1b1f-516">[Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) 또는 [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*)을 사용하여 임시 스토리지에 대한 전체 경로(파일 이름을 포함)를 만들어 파일에 대한 안전한 파일 이름을 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-516">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="f1b1f-517">Razor는 표시를 위해 속성 값을 자동으로 HTML 인코딩합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-517">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="f1b1f-518">다음 코드는 안전하게 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-518">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="f1b1f-519">Razor 외부에서는 항상 사용자 요청의 파일 이름 콘텐츠를 <xref:System.Net.WebUtility.HtmlEncode*>합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-519">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="f1b1f-520">많은 구현에서 파일 존재 여부에 대한 확인이 포함되어야 합니다. 그렇지 않으면 파일이 같은 이름의 파일을 덮어씁니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-520">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="f1b1f-521">앱의 사양을 충족하는 추가 논리를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-521">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="f1b1f-522">크기 유효성 검사</span><span class="sxs-lookup"><span data-stu-id="f1b1f-522">Size validation</span></span>

<span data-ttu-id="f1b1f-523">업로드된 파일의 크기를 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-523">Limit the size of uploaded files.</span></span>

<span data-ttu-id="f1b1f-524">샘플 앱에서 파일 크기는 2MB(바이트 단위로 표시)로 제한됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-524">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="f1b1f-525">이 제한은 *appsettings.json* 파일의 [구성](xref:fundamentals/configuration/index)을 통해 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-525">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="f1b1f-526">`FileSizeLimit`은 `PageModel` 클래스에 삽입됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-526">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="f1b1f-527">파일 크기가 제한을 초과하는 파일은 거부됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-527">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="f1b1f-528">이름 특성 값을 POST 메서드의 매개 변수 이름과 일치</span><span class="sxs-lookup"><span data-stu-id="f1b1f-528">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="f1b1f-529">양식 데이터를 POST하거나 JavaScript의 `FormData`를 직접 사용하는 비 Razor 양식에서 양식의 요소 또는 `FormData`에 지정된 이름은 컨트롤러의 작업에서 매개 변수 이름과 일치해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-529">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="f1b1f-530">다음 예제에서는</span><span class="sxs-lookup"><span data-stu-id="f1b1f-530">In the following example:</span></span>

* <span data-ttu-id="f1b1f-531">`<input>` 요소를 사용하는 경우 `name` 특성은 값 `battlePlans`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-531">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="f1b1f-532">JavaScript에서 `FormData`를 사용하는 경우 이름이 값 `battlePlans`로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-532">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="f1b1f-533">C# 메서드(`battlePlans`)의 매개 변수와 일치하는 이름을 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-533">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="f1b1f-534">이름이 `Upload`인 Razor Pages 페이지 처리기 메서드:</span><span class="sxs-lookup"><span data-stu-id="f1b1f-534">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="f1b1f-535">MVC POST 컨트롤러 작업 메서드:</span><span class="sxs-lookup"><span data-stu-id="f1b1f-535">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="f1b1f-536">서버 및 앱 구성</span><span class="sxs-lookup"><span data-stu-id="f1b1f-536">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="f1b1f-537">다중 파트 본문 길이 제한</span><span class="sxs-lookup"><span data-stu-id="f1b1f-537">Multipart body length limit</span></span>

<span data-ttu-id="f1b1f-538"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>에서 각 다중 파트 본문의 길이에 대한 제한을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-538"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="f1b1f-539">양식 섹션이 이 제한을 초과하면 구문 분석할 때 <xref:System.IO.InvalidDataException>이 throw됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-539">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="f1b1f-540">기본값은 134,217,728(128MB)입니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-540">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="f1b1f-541">`Startup.ConfigureServices`에서 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 설정을 사용하여 제한을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-541">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="f1b1f-542"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute>는 단일 페이지 또는 작업에 대해 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit>을 설정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-542"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="f1b1f-543">Razor Pages 앱에서 `Startup.ConfigureServices`의 [규칙](xref:razor-pages/razor-pages-conventions)을 사용하여 필터를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-543">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="f1b1f-544">Razor Pages 앱 또는 MVC 앱에서 페이지 모델 또는 작업 메서드에 필터를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-544">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="f1b1f-545">Kestrel 최대 요청 본문 크기</span><span class="sxs-lookup"><span data-stu-id="f1b1f-545">Kestrel maximum request body size</span></span>

<span data-ttu-id="f1b1f-546">Kestrel에서 호스트되는 앱의 경우 기본 최대 요청 본문 크기는 30,000,000바이트(약 28.6MB)입니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-546">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="f1b1f-547">[MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel 서버 옵션을 사용하여 제한을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-547">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="f1b1f-548"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute>는 단일 페이지 또는 작업에 대해 [ MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size)를 설정하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-548"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="f1b1f-549">Razor Pages 앱에서 `Startup.ConfigureServices`의 [규칙](xref:razor-pages/razor-pages-conventions)을 사용하여 필터를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-549">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="f1b1f-550">Razor Pages 앱 또는 MVC 앱에서 페이지 처리기 모델 또는 작업 메서드에 필터를 적용합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-550">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="f1b1f-551">기타 Kestrel 제한</span><span class="sxs-lookup"><span data-stu-id="f1b1f-551">Other Kestrel limits</span></span>

<span data-ttu-id="f1b1f-552">다른 Kestrel 제한이 Kestrel에서 호스트되는 앱에 적용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-552">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="f1b1f-553">최대 클라이언트 연결</span><span class="sxs-lookup"><span data-stu-id="f1b1f-553">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="f1b1f-554">요청 및 응답 데이터 속도</span><span class="sxs-lookup"><span data-stu-id="f1b1f-554">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="f1b1f-555">IIS 콘텐츠 길이 제한</span><span class="sxs-lookup"><span data-stu-id="f1b1f-555">IIS content length limit</span></span>

<span data-ttu-id="f1b1f-556">기본 요청 제한(`maxAllowedContentLength`)은 30,000,000만 바이트(약 28.6MB)입니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-556">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="f1b1f-557">*web.config* 파일에서 제한을 사용자 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-557">Customize the limit in the *web.config* file:</span></span>

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

<span data-ttu-id="f1b1f-558">이 설정은 IIS에만 적용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-558">This setting only applies to IIS.</span></span> <span data-ttu-id="f1b1f-559">Kestrel에서 호스팅하는 경우 기본적으로 동작이 발생하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-559">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="f1b1f-560">자세한 내용은 [요청 제한 \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-560">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="f1b1f-561">ASP.NET Core 모듈의 제한 사항 또는 IIS 요청 필터링 모듈의 존재로 인해 업로드가 2GB 또는 4GB로 제한될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-561">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="f1b1f-562">자세한 내용은 [크기가 2GB 이상인 파일을 업로드할 수 없음(aspnet/AspNetCore #2711)](https://github.com/aspnet/AspNetCore/issues/2711)을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-562">For more information, see [Unable to upload file greater than 2GB in size (aspnet/AspNetCore #2711)](https://github.com/aspnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="f1b1f-563">문제 해결</span><span class="sxs-lookup"><span data-stu-id="f1b1f-563">Troubleshoot</span></span>

<span data-ttu-id="f1b1f-564">다음은 파일 업로드 및 가능한 솔루션을 사용하여 작업할 때 자주 발생하는 몇 가지 일반적인 문제입니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-564">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="f1b1f-565">IIS 서버에 배포할 때 찾을 수 없음 오류</span><span class="sxs-lookup"><span data-stu-id="f1b1f-565">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="f1b1f-566">다음 오류는 업로드된 파일이 서버의 구성된 콘텐츠 길이를 초과했음을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-566">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="f1b1f-567">제한을 높이는 방법에 대한 자세한 내용은 [IIS 콘텐츠 길이 제한](#iis-content-length-limit) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-567">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="f1b1f-568">연결 실패</span><span class="sxs-lookup"><span data-stu-id="f1b1f-568">Connection failure</span></span>

<span data-ttu-id="f1b1f-569">연결 오류 및 서버 연결 다시 설정은 업로드된 파일이 Kestrel의 최대 요청 본문 크기를 초과함을 나타낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-569">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="f1b1f-570">자세한 내용은 [Kestrel 최대 요청 본문 크기](#kestrel-maximum-request-body-size) 섹션을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-570">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="f1b1f-571">Kestrel 클라이언트 연결 제한을 조정해야 할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-571">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="f1b1f-572">IFormFile을 사용한 Null 참조 예외</span><span class="sxs-lookup"><span data-stu-id="f1b1f-572">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="f1b1f-573">컨트롤러에서 <xref:Microsoft.AspNetCore.Http.IFormFile>을 사용하여 업로드된 파일을 수락하지만 값이 `null`이면 HTML 양식에서 `multipart/form-data`의 `enctype` 값을 지정하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-573">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="f1b1f-574">`<form>` 요소에서 이 특성이 설정되지 않으면 파일 업로드가 실행되지 않고 바인딩된 <xref:Microsoft.AspNetCore.Http.IFormFile> 인수는 모두 `null`이 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-574">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="f1b1f-575">또한 [양식 데이터의 업로드 이름 지정이 앱의 이름 지정과 일치](#match-name-attribute-value-to-parameter-name-of-post-method)하는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="f1b1f-575">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="f1b1f-576">추가 자료</span><span class="sxs-lookup"><span data-stu-id="f1b1f-576">Additional resources</span></span>

* [<span data-ttu-id="f1b1f-577">무제한 파일 업로드</span><span class="sxs-lookup"><span data-stu-id="f1b1f-577">Unrestricted File Upload</span></span>](https://www.owasp.org/index.php/Unrestricted_File_Upload)
* [<span data-ttu-id="f1b1f-578">Azure Security: 보안 프레임: 입력 유효성 검사 | 완화</span><span class="sxs-lookup"><span data-stu-id="f1b1f-578">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="f1b1f-579">Azure Cloud 디자인 패턴: 발레 키 패턴</span><span class="sxs-lookup"><span data-stu-id="f1b1f-579">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
