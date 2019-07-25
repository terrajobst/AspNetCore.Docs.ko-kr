<!-- USED in RP and MVC tutorial -->

## <a name="add-validation-rules-to-the-movie-model"></a>동영상 모델에 유효성 검사 규칙 추가

DataAnnotations 네임스페이스는 클래스 또는 속성에 선언적으로 적용되는 유효성 검사 특성의 기본 제공 세트를 제공합니다. DataAnnotations는 또한 서식 지정을 돕는 `DataType`과 같은 서식 지정 특성을 포함하며 유효성 검사를 제공하지 않습니다.

기본 제공되는 `Required`, `StringLength`, `RegularExpression` 및 `Range` 유효성 검사 특성을 활용하도록 `Movie` 클래스를 업데이트합니다.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRatingDA.cs?name=snippet1)]

이 유효성 검사 특성은 적용되는 모델 속성에 시행하려는 동작을 지정합니다.

* `Required` 및 `MinimumLength` 특성은 속성에 값이 있어야 하지만 사용자가 이 유효성 검사를 만족하기 위해 공백을 입력하는 것을 예방할 수 없다는 것을 나타냅니다.
* `RegularExpression` 특성은 입력될 수 있는 문자를 제한하는 데 사용됩니다. 앞의 코드에서 “Genre”는 다음 조건을 충족해야 합니다.

  * 문자만 사용해야 합니다.
  * 첫 글자는 대문자로 입력해야 합니다. 공백, 숫자 및 특수 문자는 허용되지 않습니다.

* `RegularExpression` “Rating”은 다음 조건을 충족해야 합니다.

  * 첫 번째 문자는 대문자여야 합니다.
  * 이어진 공백에서는 특수 문자와 숫자가 허용됩니다. “PG-13”은 등급인 경우 유효하지만 “Genre”에는 허용되지 않습니다.

* `Range` 특성은 지정된 범위 내의 값을 제한합니다.
* `StringLength` 특성을 사용하면 문자열 속성의 최대 길이와, 필요에 따라 최소 길이를 설정할 수 있습니다.
* 값 형식(예: `decimal`, `int`, `float`, `DateTime`)은 기본적으로 필요하며 `[Required]` 특성은 필요하지 않습니다.

ASP.NET Core에 의해 자동으로 적용되는 유효성 검사 규칙을 사용하면 앱을 더욱 강력하게 합니다. 또한 유효성 검사를 잊거나, 데이터베이스에 불량 데이터가 실수로 들어가지 않게 할 수 있습니다.
