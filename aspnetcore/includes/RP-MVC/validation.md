
## <a name="add-validation-rules-to-the-movie-model"></a><span data-ttu-id="96598-101">동영상 모델에 유효성 검사 규칙 추가</span><span class="sxs-lookup"><span data-stu-id="96598-101">Add validation rules to the movie model</span></span>

<span data-ttu-id="96598-102">*Movie.cs* 파일을 엽니다.</span><span class="sxs-lookup"><span data-stu-id="96598-102">Open the *Movie.cs* file.</span></span> <span data-ttu-id="96598-103">DataAnnotations 네임스페이스는 클래스 또는 속성에 선언적으로 적용되는 유효성 검사 특성의 기본 제공 세트를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="96598-103">The DataAnnotations namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="96598-104">DataAnnotations는 또한 서식 지정을 돕는 `DataType`과 같은 서식 지정 특성을 포함하며 유효성 검사를 제공하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="96598-104">DataAnnotations also contains formatting attributes like `DataType` that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="96598-105">기본 제공되는 `Required`, `StringLength`, `RegularExpression` 및 `Range` 유효성 검사 특성을 활용하도록 `Movie` 클래스를 업데이트합니다.</span><span class="sxs-lookup"><span data-stu-id="96598-105">Update the `Movie` class to take advantage of the built-in `Required`, `StringLength`, `RegularExpression`, and `Range` validation attributes.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc//sample/MvcMovie22/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="96598-106">이 유효성 검사 특성은 적용되는 모델 속성에 시행하려는 동작을 지정합니다.</span><span class="sxs-lookup"><span data-stu-id="96598-106">The validation attributes specify behavior that you want to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="96598-107">`Required` 및 `MinimumLength` 특성은 속성에 값이 있어야 하지만 사용자가 이 유효성 검사를 만족하기 위해 공백을 입력하는 것을 예방할 수 없다는 것을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="96598-107">The `Required` and `MinimumLength` attributes indicate that a property must have a value; but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="96598-108">`RegularExpression` 특성은 입력될 수 있는 문자를 제한하는 데 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="96598-108">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="96598-109">앞의 코드에서 “Genre”는 다음 조건을 충족해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="96598-109">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="96598-110">문자만 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="96598-110">Must only use letters.</span></span>
  * <span data-ttu-id="96598-111">첫 글자는 대문자로 입력해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="96598-111">The first letter is required to be uppercase.</span></span> <span data-ttu-id="96598-112">공백, 숫자 및 특수 문자는 허용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="96598-112">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="96598-113">`RegularExpression` “Rating”은 다음 조건을 충족해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="96598-113">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="96598-114">첫 번째 문자는 대문자여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="96598-114">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="96598-115">이어진 공백에서는 특수 문자와 숫자가 허용됩니다.</span><span class="sxs-lookup"><span data-stu-id="96598-115">Allows special characters and numbers in  subsequent spaces.</span></span> <span data-ttu-id="96598-116">“PG-13”은 등급인 경우 유효하지만 “Genre”에는 허용되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="96598-116">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="96598-117">`Range` 특성은 지정된 범위 내의 값을 제한합니다.</span><span class="sxs-lookup"><span data-stu-id="96598-117">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="96598-118">`StringLength` 특성을 사용하면 문자열 속성의 최대 길이와, 필요에 따라 최소 길이를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="96598-118">The `StringLength` attribute lets you set the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="96598-119">값 형식(예: `decimal`, `int`, `float`, `DateTime`)은 기본적으로 필요하며 `[Required]` 특성은 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="96598-119">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="96598-120">ASP.NET Core에 의해 자동으로 적용되는 유효성 검사 규칙을 사용하면 앱을 더욱 강력하게 합니다.</span><span class="sxs-lookup"><span data-stu-id="96598-120">Having validation rules automatically enforced by ASP.NET Core helps make your app more robust.</span></span> <span data-ttu-id="96598-121">또한 유효성 검사를 잊거나, 데이터베이스에 불량 데이터가 실수로 들어가지 않게 할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="96598-121">It also ensures that you can't forget to validate something and inadvertently let bad data into the database.</span></span>
