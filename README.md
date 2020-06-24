# 현재 영역에 있는 변수 접근

자바 메소드 안에서 무명 내부 클래스를 정의할 때 메소드의 로컬 변수를 무명 내부 클래스에서 사용할 수 있다. 람다 안에서도 같은 일을 할 수 있다. 람다를 함수 안에서 정의하면 함수의 파라미터뿐 아니라 람다 정의의 앞에 선언된 로컬 변수까지 람다에서 모두 사용할 수 있다.

<code>
<pre>
fun printMessagesWithPrefix(messages: Collection<String>, prefix: String) {
  messages.forEach{
    println("$prefix $it")
   }
 }
>>>val error = listOf("403 Forbidden", "404 Not Found")
>>>printMessagesWithPrefix(errors, "Errors :")
</pre>
</code>
  
자바와 다른 점 중 중요한 한가지는 코틀린 람다 안에서는 파이널 변수가 아닌 변수에 접근할 수 있다는 점이다. 또한 람다 안에서 바깥의 변수를 변경해도 된다.

<code>
<pre>
fun printProblemCounts(responses: Collection<String>) {
  var clientErrors = 0
  var serverErrors = 0
  response.forEach{
    if(it.startWith("4")) {
    clientError++
    }else if(it.startWith("5")){
    serverError++
    }
   }
  println("$clientError client errors, $serverErrors server errors")
}

>>> val response = listOf("200 OK", "418 I'm a teapot", "500 Internal Server Error")
>>> printProblemCounts(response)
</pre>
</code>
이 리스트는 전달받은 상태 코드 목록에 있는 클라이언트와 서버 오류의 횟수를 센다.

코틀린에서는 자바와 달리 람다에서 람다 밖 함수에 있는 변수에 접근할 수 있고, 변경도 가능하다. 위 코드와 같은 예제의 형태를 람다 안에서 사용하는 외부 변수를 **람다가 포획한 변수** 라고 부른다고한다. 포획한 변수가 있는 람다를 저장해서 함수가 끝난 뒤에 실행해도 람다의 본문 코드는 여전히 포획한 변수를 읽거나 쓸 수 있다. 왜냐하면 파이널 변수를 포획한 경우에는 람다 코드를 변수 값과 함께 저장하기때문이다. 파이널이 아닌 변수를 포획한 경우에는 변수를 특별한 래퍼로 감싸서 나중에 읽기/수정 할 수 있게 한다음 래퍼에 대한 참조를 람다 코드와 함께 저장한다.

변수를 포획하는 예제 코드이다.
<code>
<pre>
var counter = 0
val inc = {counter++}
</pre>
</code>

하지만 함정이 하나 있는데, 람다를 이벤트 핸들러나 다른 비동기적 실행 코드로 활용하는 경우 함수 호출이 끝난 다음에 로컬 변수가 변경될 수도 있다.
예를들어 버튼을 클릭하는 횟수를 나타낼때 생각과 다르게 제대로 셀 수 없을 수 있다.
<code>
<pre>
fun tryToCountButtonClick(button: Button): Int {
  var clicks = 0
  button.click{clicks++}
  return clicks
}
</pre>
</code>

이 함수는 항상 ()을 반환한다고 한다. onClick 핸들러는 호출될 때마다 clicks의 값을 증가시키지만, 그 값의 변경을 아직 관찰할 수는 없기 때문이다. 핸들러는 tryToCountButtonClick이 clicks를 반환한 다음에 호출되기 때문이다.
