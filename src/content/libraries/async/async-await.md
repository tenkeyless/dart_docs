---
# title: "Asynchronous programming: futures, async, await"
title: "비동기 프로그래밍: futures, async, await"
# description: Learn about and practice writing asynchronous code in DartPad!
description: DartPad에서 비동기 코드를 작성하는 방법을 배우고 연습해 보세요!
js: [{url: '/assets/js/inject_dartpad.js', defer: true}]
---
<?code-excerpt replace="/ *\/\/\s+ignore_for_file:[^\n]+\n//g; /(^|\n) *\/\/\s+ignore:[^\n]+\n/$1/g; /(\n[^\n]+) *\/\/\s+ignore:[^\n]+\n/$1\n/g"?>
<?code-excerpt plaster="none"?>

이 튜토리얼에서는 futures와 `async` 및 `await` 키워드를 사용하여, 
비동기 코드를 작성하는 방법을 알려줍니다. 
내장된 DartPad 편집기를 사용하여 예제 코드를 실행하고, 
연습 문제를 완료하여 지식을 테스트할 수 있습니다.

이 튜토리얼을 최대한 활용하려면 다음이 필요합니다.

* [기본 Dart 구문](/language)에 대한 지식.
* 다른 언어로 비동기 코드를 작성하는 데 대한 약간의 경험.
* [`discarded_futures`][] 및 [`unawaited_futures`][] lint가 활성화.

[`discarded_futures`]: /tools/linter-rules/discarded_futures
[`unawaited_futures`]: /tools/linter-rules/unawaited_futures

이 튜토리얼은 다음 자료를 다룹니다.

* `async` 및 `await` 키워드를 사용하는 방법과 시기.
* `async` 및 `await`를 사용하면 실행 순서에 어떤 영향을 미치는지.
* `async` 함수에서 `try-catch` 표현식을 사용하여 비동기 호출의 오류를 처리하는 방법.

이 튜토리얼을 완료하는 데 걸리는 예상 시간: 40-60분.

:::note
이 페이지에서는 내장된 DartPad를 사용하여 예제와 연습문제를 표시합니다.
{% render 'dartpads-embedded-troubleshooting.md' %}
:::

이 튜토리얼의 연습은 부분적으로 완성된 코드 조각을 포함합니다. 
DartPad를 사용하여 코드를 완성하고,
**Run** 버튼을 클릭하여 지식을 테스트할 수 있습니다. 
**`main` 함수나 그 아래에 있는 테스트 코드를 편집하지 마세요**.

도움이 필요하면, 각 연습 후에 **Hint** 또는 **Solution** 드롭다운을 확장하세요.

## 비동기 코드가 중요한 이유 {:#why-asynchronous-code-matters}

비동기 연산을 통해, 프로그램은 다른 연산이 완료되는 것을 기다리는 동안, 작업을 완료할 수 있습니다. 
다음은 일반적인 비동기 연산입니다.

* 네트워크를 통해 데이터 가져오기.
* 데이터베이스에 쓰기.
* 파일에서 데이터 읽기.

이러한 비동기 계산은 일반적으로 결과를 `Future`로 제공하거나, 
결과에 여러 부분이 있는 경우 `Stream`으로 제공합니다. 
이러한 계산은 프로그램에 비동기성을 도입합니다. 
이러한 초기 비동기성을 수용하기 위해, 다른 일반 Dart 함수도 비동기화해야 합니다.

이러한 비동기 결과와 상호 작용하려면, 
`async` 및 `await` 키워드를 사용할 수 있습니다. 
대부분의 비동기 함수는, 본질적으로 비동기적인 계산에 의존하는, async Dart 함수일 뿐입니다.

### 예: 비동기 함수를 잘못 사용하는 경우 {:#example-incorrectly-using-an-asynchronous-function}

다음 예는 비동기 함수(`fetchUserOrder()`)를 사용하는 잘못된 방법을 보여줍니다. 
나중에 `async`와 `await`를 사용하여 예를 수정합니다. 
이 예를 실행하기 전에, 문제를 찾아보세요. 출력이 어떻게 될 것 같나요?

<?code-excerpt "async_await/bin/get_order_sync_bad.dart" remove="Fetching"?>
```dartpad
// 이 예제는 이렇게 비동기 Dart 코드를 작성하지 *않아야 한다는* 방법을 보여줍니다.

String createOrderMessage() {
  var order = fetchUserOrder();
  return 'Your order is: $order';
}

Future<String> fetchUserOrder() =>
    // 이 함수가 더 복잡하고 느리다고 상상해 봅시다.
    Future.delayed(
      const Duration(seconds: 2),
      () => 'Large Latte',
    );

void main() {
  print(createOrderMessage());
}
```

다음은 이 예제가 `fetchUserOrder()`가 결국 생성하는 값을 출력하지 못하는 이유입니다.

* `fetchUserOrder()`는, 딜레이 후, 
  사용자 주문을 설명하는 문자열인, "Large Latte"를 제공하는 비동기 함수입니다.
* 사용자 주문을 얻으려면, `createOrderMessage()`가 `fetchUserOrder()`를 호출하고, 
  완료될 때까지 기다려야 합니다. 
  `createOrderMessage()`는 `fetchUserOrder()`가 완료될 때까지 기다리지 *않기* 때문에, 
  `createOrderMessage()`는 `fetchUserOrder()`가 결국 제공하는 문자열 값을 가져오지 못합니다.
* 대신, `createOrderMessage()`는 보류 중인 작업의 표현을 가져옵니다. (완료되지 않은 future)
  다음 섹션에서 future에 대해 자세히 알아봅니다.
* `createOrderMessage()`가 사용자 주문을 설명하는 값을 가져오지 못하기 때문에, 
  이 예제는 콘솔에 "Large Latte"를 출력하지 못하고, 
  대신 "Your order is: Instance of '_Future\<String\>'"를 출력합니다.

다음 섹션에서는 future와 future를 사용하는 방법(`async` 및 `await` 사용)에 대해 알아봅니다. 
그러면 `fetchUserOrder()`가 원하는 값("Large Latte")을 콘솔에 출력하는 데, 
필요한 코드를 작성할 수 있습니다.

:::secondary 주요 용어
* **동기 연산**: 동기 연산은 완료될 때까지, 다른 연산이 실행되지 않도록 차단합니다.
* **동기 함수**: 동기 함수는 동기 연산만 수행합니다.
* **비동기 연산**: 비동기 연산이 시작되면, 다른 연산이 완료되기 전에 실행될 수 있습니다.
* **비동기 함수**: 비동기 함수는 최소한 하나의 비동기 연산을 수행하고, _동기_ 연산도 수행할 수 있습니다.
:::


## future란 무엇인가요? {:#what-is-a-future}

future(소문자 "f")는 [Future][](대문자 "F") 클래스의 인스턴스입니다. 
future는 비동기 연산의 결과를 나타내며, 
미완료(uncompleted) 또는 완료(completed)의 두 가지 상태를 가질 수 있습니다.

:::note
_미완료 (Uncompleted)_ 는 값을 생성하기 전의, future의 상태를 나타내는 Dart 용어입니다.
:::

### 미완료 (Uncompleted) {:#uncompleted}

비동기 함수를 호출하면, 완료되지 않은 future가 반환됩니다. 
해당 future는 함수의 비동기 작업이 완료되거나, 오류가 발생할 때까지 기다리고 있습니다.

### 완료 (Completed) {:#completed}

비동기 작업이 성공하면, future는 값으로 완료됩니다. 
그렇지 않으면, 오류로 완료됩니다.

#### 값으로 완료 {:#completing-with-a-value}

`Future<T>` 타입의 future는 `T` 타입의 값으로 완성됩니다. 
예를 들어, `Future<String>` 타입의 future는 문자열 값을 생성합니다. 
future가 사용 가능한 값을 생성하지 않으면, 
future의 타입은 `Future<void>`입니다.

#### 오류로 완료 {:#completing-with-an-error}

함수가 수행하는 비동기 연산이 어떤 이유로든 실패하면, 
future는 오류로 완료됩니다.

### 예: futures 소개 {:#example-introducing-futures}

다음 예에서, `fetchUserOrder()`는 콘솔에 출력한 후 완료되는 future를 반환합니다. 
사용 가능한 값을 반환하지 않기 때문에, `fetchUserOrder()`는 `Future<void>` 타입을 갖습니다. 
예를 실행하기 전에, "Large Latte" 또는 "Fetching user order..." 중 어느 것이 먼저 출력될지 예측해 보세요.

<?code-excerpt "async_await/bin/futures_intro.dart (no-error)"?>
```dartpad
Future<void> fetchUserOrder() {
  // 이 함수가 다른 서비스나 데이터베이스에서 사용자 정보를 가져오는 것을 가정해 보겠습니다.
  return Future.delayed(const Duration(seconds: 2), () => print('Large Latte'));
}

void main() {
  fetchUserOrder();
  print('Fetching user order...');
}
```

앞의 예에서, `fetchUserOrder()`가 8번째 줄에서 `print()` 호출 전에 실행되더라도, 
콘솔은 `fetchUserOrder()`의 출력("Large Latte")보다, 
8번째 줄의 출력("Fetching user order...")을 먼저 표시합니다. 
이는 `fetchUserOrder()`가 "Large Latte"를 인쇄하기 전에 지연(delay)되기 때문입니다.

### 예: 오류로 완료 {:#example-completing-with-an-error}

다음 예제를 실행하여 future가 오류로 완료되는 방식을 확인합니다. 
잠시 후에 오류를 처리하는 방법을 배우게 됩니다.

<?code-excerpt "async_await/bin/futures_intro.dart (error)" replace="/Error//g"?>
```dartpad
Future<void> fetchUserOrder() {
  // 이 함수가 사용자 정보를 가져오는 중 버그가 발견되었다고 가정해 보겠습니다.
  return Future.delayed(
    const Duration(seconds: 2),
    () => throw Exception('Logout failed: user ID is invalid'),
  );
}

void main() {
  fetchUserOrder();
  print('Fetching user order...');
}
```

이 예에서, `fetchUserOrder()`는 사용자 ID가 잘못되었다는 오류로 완료됩니다.

futures와 그것이 완료되는 방식에 대해 알아보았지만, 
비동기 함수의 결과는 어떻게 사용할까요? 
다음 섹션에서는 `async` 및 `await` 키워드로 결과를 얻는 방법을 알아봅니다.

:::secondary 간단한 검토
* [Future\<T\>][Future] 인스턴스는 `T` 타입의 값을 생성합니다.
* future가 사용 가능한 값을 생성하지 않으면, future의 타입은 `Future<void>`입니다.
* future는 미완료(uncompleted) 또는 완료(completed)의 두 가지 상태 중 하나일 수 있습니다.
* future를 반환하는 함수를 호출하면, 함수는 수행할 작업을 대기열(queues)에 넣고 미완료 future를 반환합니다.
* future의 연산이 완료되면, future는 값 또는 오류로 완료됩니다.

**핵심 용어:**

* **Future**: Dart [Future][] 클래스.
* **future**: Dart `Future` 클래스의 인스턴스.
:::

## futures로 작업하기: async 및 await {:#working-with-futures-async-and-await}

`async` 및 `await` 키워드는 비동기 함수를 정의하고, 
그 결과를 사용하는 선언적 방법을 제공합니다. 
`async` 및 `await`를 사용할 때, 다음 두 가지 기본 지침을 기억하세요.

* __비동기 함수를 정의하려면, 함수 본문 앞에 `async`를 추가합니다.__
* __`await` 키워드는 `async` 함수에서만 작동합니다.__

다음은 `main()`을 동기 함수에서 비동기 함수로 변환하는 예입니다.

먼저, 함수 본문 앞에 `async` 키워드를 추가합니다.

<?code-excerpt "async_await/bin/get_order_sync_bad.dart (main-sig)" replace="/main\(\)/$& async/g; /async/[!$&!]/g; /$/ ··· }/g"?>
```dart
void main() [!async!] { ··· }
```

함수에 선언된 반환 타입이 있는 경우, 타입을 `Future<T>`로 업데이트합니다. 
여기서, `T`는 함수가 반환하는 값의 타입입니다. 
함수가 명시적으로 값을 반환하지 않는 경우, 반환 타입은 `Future<void>`입니다.

<?code-excerpt "async_await/bin/get_order.dart (main-sig)" replace="/Future<\w+\W/[!$&!]/g;  /$/ ··· }/g"?>
```dart
[!Future<void>!] main() async { ··· }
```

이제 `async` 함수가 있으므로, 
`await` 키워드를 사용하여 future가 완료될 때까지 기다릴 수 있습니다.

<?code-excerpt "async_await/bin/get_order.dart (print-order)" replace="/await/[!$&!]/g"?>
```dart
print([!await!] createOrderMessage());
```

다음 두 가지 예에서 알 수 있듯이, 
`async`와 `await` 키워드는, 
동기 코드와 매우 유사한 비동기 코드를 생성합니다. 
유일한 차이점은 비동기 예제에서 강조 표시되어 있으며, 
(창이 충분히 넓다면) 동기 예제의 오른쪽에 있습니다.

#### 예: 동기 함수 {:#example-synchronous-functions}

<?code-excerpt "async_await/bin/get_order_sync_bad.dart (no-warning)" replace="/(\s+\/\/ )(Imagine.*? is )(.*)/$1$2$1$3/g"?>
```dart
String createOrderMessage() {
  var order = fetchUserOrder();
  return 'Your order is: $order';
}

Future<String> fetchUserOrder() =>
    // 이 함수가 더 복잡하고 느리다고 상상해봅시다.
    Future.delayed(
      const Duration(seconds: 2),
      () => 'Large Latte',
    );

void main() {
  print('Fetching user order...');
  print(createOrderMessage());
}
```

```plaintext
Fetching user order...
Your order is: Instance of 'Future<String>'
```

다음 두 가지 예에서 보듯이, 동기식 코드처럼 작동합니다.

#### 예: 비동기 함수 {:#example-asynchronous-functions}

<?code-excerpt "async_await/bin/get_order.dart" replace="/(\s+\/\/ )(Imagine.*? is )(.*)/$1$2$1$3/g; /async|await/[!$&!]/g; /(Future<\w+\W)( [^f])/[!$1!]$2/g; /4/2/g"?>
```dart
[!Future<String>!] createOrderMessage() [!async!] {
  var order = [!await!] fetchUserOrder();
  return 'Your order is: $order';
}

Future<String> fetchUserOrder() =>
    // 이 함수가 더 복잡하고 느리다고 상상해봅시다.
    Future.delayed(
      const Duration(seconds: 2),
      () => 'Large Latte',
    );

[!Future<void>!] main() [!async!] {
  print('Fetching user order...');
  print([!await!] createOrderMessage());
}
```

```plaintext
Fetching user order...
Your order is: Large Latte
```

비동기 예제는 세 가지 면에서 다릅니다.

* `createOrderMessage()`의 반환 타입이, 
  `String`에서 `Future<String>`로 변경됩니다.
* `createOrderMessage()`와 `main()`의 함수 본문 앞에, 
  **`async`** 키워드가 나타납니다.
* `fetchUserOrder()`와 `createOrderMessage()` 비동기 함수를 호출하기 전에, 
  **`await`** 키워드가 나타납니다.

:::secondary 주요 용어
* **async**: 함수 본문 앞에 `async` 키워드를 사용하여, 비동기로 표시할 수 있습니다.
* **async 함수**: `async` 함수는 `async` 키워드로 레이블이 지정된 함수입니다.
* **await**: `await` 키워드를 사용하여 비동기 표현식의 완료된 결과를 가져올 수 있습니다.
  `await` 키워드는 `async` 함수 내에서만 작동합니다.
:::

### async 및 await가 있을 때의 실행 흐름 {:#execution-flow-with-async-and-await}

`async` 함수는 첫 번째 `await` 키워드까지 동기적으로 실행됩니다. 
즉, `async` 함수 본문 내에서, 
첫 번째 `await` 키워드 이전의 모든 동기 코드가 즉시 실행됩니다.

### 예: async 함수 내에서 실행 {:#example-execution-within-async-functions}

다음 예제를 실행하여, `async` 함수 본문 내에서 실행이 어떻게 진행되는지 확인하세요. 
출력이 어떻게 될 것 같나요?

<?code-excerpt "async_await/bin/async_example.dart" remove="/\/\/ print/"?>
```dartpad
Future<void> printOrderMessage() async {
  print('Awaiting user order...');
  var order = await fetchUserOrder();
  print('Your order is: $order');
}

Future<String> fetchUserOrder() {
  // 이 함수가 더 복잡하고 느리다고 상상해봅시다.
  return Future.delayed(const Duration(seconds: 4), () => 'Large Latte');
}

void main() async {
  countSeconds(4);
  await printOrderMessage();
}

// 이 함수는 무시해도 됩니다. 
// 이 예에서는 지연 시간을 시각화하기 위해, 이것을 넣었습니다.
void countSeconds(int s) {
  for (var i = 1; i <= s; i++) {
    Future.delayed(Duration(seconds: i), () => print(i));
  }
}
```

이전 예제의 코드를 실행한 후, 2번째 줄과 3번째 줄을 뒤집은 후, 실행해보세요.

<?code-excerpt "async_await/bin/async_example.dart (swap-stmts)" replace="/\/\/ (print)/$1/g"?>
```dart
var order = await fetchUserOrder();
print('Awaiting user order...');
```

이제 `printOrderMessage()`의 첫 번째 `await` 키워드 뒤에, 
`print('Awaiting user order')`가 나타나므로, 
출력 타이밍이 바뀌는 것을 주목하세요.

### 연습: async 및 await 사용 연습 {:#exercise-practice-using-async-and-await}

다음 연습은 부분적으로 완성된 코드 조각을 포함하는 실패한 유닛 테스트입니다. 
여러분의 과제는 테스트를 통과시키는 코드를 작성하여, 연습을 완료하는 것입니다. 
`main()`을 구현할 필요는 없습니다.

비동기 연산을 시뮬레이션하려면, 제공된 다음 함수를 호출합니다.

| 함수           | 타입 시그니쳐                   | 설명                                    |
|--------------------|----------------------------------|------------------------------------------------|
| fetchRole()        | `Future<String> fetchRole()`     | 사용자 역할에 대한 간략한 설명을 가져옵니다.  |
| fetchLoginAmount() | `Future<int> fetchLoginAmount()` | 사용자가 로그인한 횟수를 가져옵니다. |

{:.table .table-striped}

#### 파트 1: `reportUserRole()` {:#part-1-reportuserrole}

`reportUserRole()` 함수에 코드를 추가하여, 다음을 수행하도록 합니다.

* 다음 문자열로 완료되는 future를 반환합니다. : `"User role: <user role>"`
  * 참고: `fetchRole()`에서 반환된 실제 값을 사용해야 합니다. 
    예제 반환 값을 복사하여 붙여넣어도 테스트가 통과되지 않습니다.
  * 예제 반환 값: `"User role: tester"`
* 제공된 함수 `fetchRole()`를 호출하여, 사용자 역할을 가져옵니다.

#### 파트 2: `reportLogins()` {:#part-2-reportlogins}

`reportLogins()`라는 `async` 함수를 구현하여, 다음을 수행합니다.

* 문자열 `"Total number of logins: <# of logins>"`를 반환합니다.
  * 참고: `fetchLoginAmount()`에서 반환된 실제 값을 사용해야 합니다.
    예제 반환 값을 복사하여 붙여넣어도 테스트가 통과되지 않습니다.
  * `reportLogins()`에서 반환된 예제 값: `"Total number of logins: 57"`
* 제공된 함수 `fetchLoginAmount()`를 호출하여 로그인 수를 가져옵니다.

```dartpad theme="dark"
// 파트 1
// 제공된 async 함수 fetchRole()을 호출하여, 사용자 역할을 반환합니다.
Future<String> reportUserRole() async {
  // TODO: 여기에 reportUserRole 함수를 구현합니다.
}

// 파트 2
// TODO: 여기에 reportLogins 함수를 구현합니다.
// 제공된 async 함수 fetchLoginAmount()를 호출하여, 사용자가 로그인한 횟수를 반환합니다.
reportLogins() {}

// 다음 함수는 시간이 걸릴 수 있는 비동기 작업을 시뮬레이션하기 위해 제공됩니다.

Future<String> fetchRole() => Future.delayed(_halfSecond, () => _role);
Future<int> fetchLoginAmount() => Future.delayed(_halfSecond, () => _logins);

// 다음 코드는 당신의 솔루션을 테스트하고 피드백을 제공하는 데 사용됩니다.
// 읽거나 수정할 필요가 없습니다.

void main() async {
  print('Testing...');
  List<String> messages = [];
  const passed = 'PASSED';
  const testFailedMessage = 'Test failed for the function:';
  const typoMessage = 'Test failed! Check for typos in your return value';
  try {
    messages
      ..add(_makeReadable(
          testLabel: 'Part 1',
          testResult: await _asyncEquals(
            expected: 'User role: administrator',
            actual: await reportUserRole(),
            typoKeyword: _role,
          ),
          readableErrors: {
            typoMessage: typoMessage,
            'null':
                'Test failed! Did you forget to implement or return from reportUserRole?',
            'User role: Instance of \'Future<String>\'':
                '$testFailedMessage reportUserRole. Did you use the await keyword?',
            'User role: Instance of \'_Future<String>\'':
                '$testFailedMessage reportUserRole. Did you use the await keyword?',
            'User role:':
                '$testFailedMessage reportUserRole. Did you return a user role?',
            'User role: ':
                '$testFailedMessage reportUserRole. Did you return a user role?',
            'User role: tester':
                '$testFailedMessage reportUserRole. Did you invoke fetchRole to fetch the user\'s role?',
          }))
      ..add(_makeReadable(
          testLabel: 'Part 2',
          testResult: await _asyncEquals(
            expected: 'Total number of logins: 42',
            actual: await reportLogins(),
            typoKeyword: _logins.toString(),
          ),
          readableErrors: {
            typoMessage: typoMessage,
            'null':
                'Test failed! Did you forget to implement or return from reportLogins?',
            'Total number of logins: Instance of \'Future<int>\'':
                '$testFailedMessage reportLogins. Did you use the await keyword?',
            'Total number of logins: Instance of \'_Future<int>\'':
                '$testFailedMessage reportLogins. Did you use the await keyword?',
            'Total number of logins: ':
                '$testFailedMessage reportLogins. Did you return the number of logins?',
            'Total number of logins:':
                '$testFailedMessage reportLogins. Did you return the number of logins?',
            'Total number of logins: 57':
                '$testFailedMessage reportLogins. Did you invoke fetchLoginAmount to fetch the number of user logins?',
          }))
      ..removeWhere((m) => m.contains(passed))
      ..toList();

    if (messages.isEmpty) {
      print('Success. All tests passed!');
    } else {
      messages.forEach(print);
    }
  } on UnimplementedError {
    print(
        'Test failed! Did you forget to implement or return from reportUserRole?');
  } catch (e) {
    print('Tried to run solution, but received an exception: $e');
  }
}

const _role = 'administrator';
const _logins = 42;
const _halfSecond = Duration(milliseconds: 500);

// 테스트 헬퍼.
String _makeReadable({
  required String testResult,
  required Map<String, String> readableErrors,
  required String testLabel,
}) {
  if (readableErrors.containsKey(testResult)) {
    var readable = readableErrors[testResult];
    return '$testLabel $readable';
  } else {
    return '$testLabel $testResult';
  }
}

// 테스트에 사용되는 Assertions 입니다.
Future<String> _asyncEquals({
  required String expected,
  required dynamic actual,
  required String typoKeyword,
}) async {
  var strActual = actual is String ? actual : actual.toString();
  try {
    if (expected == actual) {
      return 'PASSED';
    } else if (strActual.contains(typoKeyword)) {
      return 'Test failed! Check for typos in your return value';
    } else {
      return strActual;
    }
  } catch (e) {
    return e.toString();
  }
}
```

<details>
  <summary title="Expand for a hint on the async-await exercise.">힌트</summary>

  `reportUserRole` 함수에 `async` 키워드를 추가하는 걸 기억하셨나요?

  `fetchRole()`를 호출하기 전에 `await` 키워드를 사용하는 걸 기억하셨나요?

  기억하세요: `reportUserRole`은 `Future`를 반환해야 합니다.

</details>

<details>
  <summary title="Expand for the solution of the async-await exercise.">솔루션</summary>

  ```dart
  Future<String> reportUserRole() async {
    final username = await fetchRole();
    return 'User role: $username';
  }
  
  Future<String> reportLogins() async {
    final logins = await fetchLoginAmount();
    return 'Total number of logins: $logins';
  }
  ```

</details>

## 오류 처리 {:#handling-errors}

`async` 함수에서 오류를 처리하려면, try-catch를 사용하세요.

<?code-excerpt "async_await/bin/try_catch.dart (try-catch)" remove="print(order)"?>
```dart
try {
  print('Awaiting user order...');
  var order = await fetchUserOrder();
} catch (err) {
  print('Caught error: $err');
}
```

`async` 함수 내에서, 
동기 코드에서와 같은 방식으로 [try-catch 클로저](/language/error-handling#catch)를 작성할 수 있습니다.

### 예: async 및 await으로 try-catch {:#example-async-and-await-with-try-catch}

다음 예제를 실행하여 비동기 함수의 오류를 처리하는 방법을 확인하세요. 
출력이 어떻게 될 것 같나요?

<?code-excerpt "async_await/bin/try_catch.dart"?>
```dartpad
Future<void> printOrderMessage() async {
  try {
    print('Awaiting user order...');
    var order = await fetchUserOrder();
    print(order);
  } catch (err) {
    print('Caught error: $err');
  }
}

Future<String> fetchUserOrder() {
  // 이 함수가 더 복잡하다고 상상해 보세요.
  var str = Future.delayed(
      const Duration(seconds: 4),
      () => throw 'Cannot locate user order');
  return str;
}

void main() async {
  await printOrderMessage();
}
```

### 연습: 오류 처리 연습 {:#exercise-practice-handling-errors}

다음 연습은 이전 섹션에서 설명한 접근 방식을 사용하여, 
비동기 코드로 오류를 처리하는 연습을 제공합니다. 
비동기 연산을 시뮬레이션하기 위해, 코드는 제공된 다음 함수를 호출합니다.

| 함수           | 타입 시그니쳐                      | 설명                                                      |
|--------------------|-------------------------------------|------------------------------------------------------------------|
| fetchNewUsername() | `Future<String> fetchNewUsername()` | 이전 username을 대체할 수 있는 새 username을 반환합니다. |

{:.table .table-striped}

`async`와 `await`를 사용하여, 다음을 수행하는 비동기 `changeUsername()` 함수를 구현합니다.

* 제공된 비동기 함수 `fetchNewUsername()`를 호출하고 결과를 반환합니다.
  * `changeUsername()`의 반환 값 예: `"jane_smith_92"`
* 발생하는 어떠한 오류를 catch 하고, 오류의 문자열 값을 반환합니다.
  * [toString()]({{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/ArgumentError/toString.html) 메서드를 사용하여, [Exceptions]({{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Exception-class.html)와 [Errors]({{site.dart-api}}/{{site.sdkInfo.channel}}/dart-core/Error-class.html)를 모두 문자열화할 수 있습니다.

```dartpad theme="dark"
// TODO: 여기에 changeUsername을 구현합니다.
changeUsername() {}

// 다음 함수는 시간이 오래 걸리고, 
// 잠재적으로 예외가 발생할 수 있는, 
// 비동기 연산을 시뮬레이션하기 위해 제공됩니다.

Future<String> fetchNewUsername() =>
    Future.delayed(const Duration(milliseconds: 500), () => throw UserError());

class UserError implements Exception {
  @override
  String toString() => 'New username is invalid';
}

// 다음 코드는 당신의 솔루션을 테스트하고 피드백을 제공하는 데 사용됩니다.
// 읽거나 수정할 필요가 없습니다.

void main() async {
  final List<String> messages = [];
  const typoMessage = 'Test failed! Check for typos in your return value';

  print('Testing...');
  try {
    messages
      ..add(_makeReadable(
          testLabel: '',
          testResult: await _asyncDidCatchException(changeUsername),
          readableErrors: {
            typoMessage: typoMessage,
            _noCatch:
                'Did you remember to call fetchNewUsername within a try/catch block?',
          }))
      ..add(_makeReadable(
          testLabel: '',
          testResult: await _asyncErrorEquals(changeUsername),
          readableErrors: {
            typoMessage: typoMessage,
            _noCatch:
                'Did you remember to call fetchNewUsername within a try/catch block?',
          }))
      ..removeWhere((m) => m.contains(_passed))
      ..toList();

    if (messages.isEmpty) {
      print('Success. All tests passed!');
    } else {
      messages.forEach(print);
    }
  } catch (e) {
    print('Tried to run solution, but received an exception: $e');
  }
}

// 테스트 헬퍼.
String _makeReadable({
  required String testResult,
  required Map<String, String> readableErrors,
  required String testLabel,
}) {
  if (readableErrors.containsKey(testResult)) {
    final readable = readableErrors[testResult];
    return '$testLabel $readable';
  } else {
    return '$testLabel $testResult';
  }
}

Future<String> _asyncErrorEquals(Function fn) async {
  final result = await fn();
  if (result == UserError().toString()) {
    return _passed;
  } else {
    return 'Test failed! Did you stringify and return the caught error?';
  }
}

Future<String> _asyncDidCatchException(Function fn) async {
  var caught = true;
  try {
    await fn();
  } on UserError catch (_) {
    caught = false;
  }

  if (caught == false) {
    return _noCatch;
  } else {
    return _passed;
  }
}

const _passed = 'PASSED';
const _noCatch = 'NO_CATCH';
```

<details>
  <summary title="Expand for a hint on the error-handling exercise.">힌트</summary>

  `changeUsername`을 구현하여, `fetchNewUsername`에서 문자열을 반환하거나, 
  실패하면, 발생하는 모든 오류의 문자열 값을 반환합니다.

  기억하세요: [try-catch 문](/language/error-handling#catch)을 사용하여, 
  오류를 catch 하고, 처리할 수 있습니다.

</details>

<details>
  <summary title="Expand for the solution of the error-handling exercise.">솔루션</summary>

  ```dart
  Future<String> changeUsername() async {
    try {
      return await fetchNewUsername();
    } catch (err) {
      return err.toString();
    }
  }
  ```

</details>

## 연습: 모두 합치기 {:#exercise-putting-it-all-together}

이제 배운 것을 마지막 연습으로 연습할 시간입니다. 
비동기 연산을 시뮬레이션하기 위해, 
이 연습은 비동기 함수 `fetchUsername()`과 `logoutUser()`를 제공합니다.

| 함수        | 타입 시그니쳐                   | 설명                                                                   |
|-----------------|----------------------------------|-------------------------------------------------------------------------------|
| fetchUsername() | `Future<String> fetchUsername()` | 현재 사용자와 연관된 이름을 반환합니다.                            |
| logoutUser()    | `Future<String> logoutUser()`    | 현재 사용자에서 로그아웃을 수행하고 로그아웃된 username을 반환합니다. |

{:.table .table-striped}

다음을 작성하세요.

####  파트 1: `addHello()` {:#part-1-addhello}

* 단일 `String` 인수를 취하는, 함수 `addHello()`를 작성합니다.
* `addHello()`는 `'Hello '`로 시작하는 `String` 인수를 반환합니다.<br>
  예: `addHello('Jon')`는 `'Hello Jon'`을 반환합니다.

####  파트 2: `greetUser()` {:#part-2-greetuser}

* 인수를 받지 않는, 함수 `greetUser()`를 작성합니다.
* 사용자 이름을 가져오기 위해, `greetUser()`는 제공된 비동기 함수 `fetchUsername()`를 호출합니다.
* `greetUser()`는 `addHello()`를 호출하고, 사용자 이름을 전달하고, 
  결과를 반환하여 사용자에 대한 인사말을 만듭니다.<br>
  예: `fetchUsername()`이 `'Jenny'`를 반환하면, 
  `greetUser()`는 `'Hello Jenny'`를 반환합니다.

####  파트 3: `sayGoodbye()` {:#part-3-saygoodbye}

* 다음을 수행하는 함수 `sayGoodbye()`를 작성하세요.
  * 인수를 받지 않습니다.
  * 오류를 catch 합니다.
  * 제공된 비동기 함수 `logoutUser()`를 호출합니다.
* `logoutUser()`가 실패하면, `sayGoodbye()`는 당신이 원하는 아무 문자열을 반환합니다.
* `logoutUser()`가 성공하면, 
  `sayGoodbye()`는 문자열 `'<result> Thanks, see you next time'`을 반환합니다. 
  여기서 `<result>`는 `logoutUser()`를 호출하여 반환된 문자열 값입니다.

```dartpad theme="dark"
// 파트 1
addHello(String user) {}

// 파트 2
// 제공된 async 함수 fetchUsername()을 호출하여, username을 반환합니다.
greetUser() {}

// 파트 3
// 제공된 async 함수 logoutUser()를 호출하여, 사용자를 로그아웃합니다.
sayGoodbye() {}

// 다음 기능은 당신의 솔루션에서 사용할 수 있도록 제공됩니다.

Future<String> fetchUsername() => Future.delayed(_halfSecond, () => 'Jean');

Future<String> logoutUser() => Future.delayed(_halfSecond, _failOnce);

// 다음 코드는 당신의 솔루션을 테스트하고 피드백을 제공하는 데 사용됩니다.
// 읽거나 수정할 필요가 없습니다.

void main() async {
  const didNotImplement =
      'Test failed! Did you forget to implement or return from';

  final List<String> messages = [];

  print('Testing...');
  try {
    messages
      ..add(_makeReadable(
          testLabel: 'Part 1',
          testResult: await _asyncEquals(
              expected: 'Hello Jerry',
              actual: addHello('Jerry'),
              typoKeyword: 'Jerry'),
          readableErrors: {
            _typoMessage: _typoMessage,
            'null': '$didNotImplement addHello?',
            'Hello Instance of \'Future<String>\'':
                'Looks like you forgot to use the \'await\' keyword!',
            'Hello Instance of \'_Future<String>\'':
                'Looks like you forgot to use the \'await\' keyword!',
          }))
      ..add(_makeReadable(
          testLabel: 'Part 2',
          testResult: await _asyncEquals(
              expected: 'Hello Jean',
              actual: await greetUser(),
              typoKeyword: 'Jean'),
          readableErrors: {
            _typoMessage: _typoMessage,
            'null': '$didNotImplement greetUser?',
            'HelloJean':
                'Looks like you forgot the space between \'Hello\' and \'Jean\'',
            'Hello Instance of \'Future<String>\'':
                'Looks like you forgot to use the \'await\' keyword!',
            'Hello Instance of \'_Future<String>\'':
                'Looks like you forgot to use the \'await\' keyword!',
            '{Closure: (String) => dynamic from Function \'addHello\': static.(await fetchUsername())}':
                'Did you place the \'\$\' character correctly?',
            '{Closure \'addHello\'(await fetchUsername())}':
                'Did you place the \'\$\' character correctly?',
          }))
      ..add(_makeReadable(
          testLabel: 'Part 3',
          testResult: await _asyncDidCatchException(sayGoodbye),
          readableErrors: {
            _typoMessage:
                '$_typoMessage. Did you add the text \'Thanks, see you next time\'?',
            'null': '$didNotImplement sayGoodbye?',
            _noCatch:
                'Did you remember to call logoutUser within a try/catch block?',
            'Instance of \'Future<String>\' Thanks, see you next time':
                'Did you remember to use the \'await\' keyword in the sayGoodbye function?',
            'Instance of \'_Future<String>\' Thanks, see you next time':
                'Did you remember to use the \'await\' keyword in the sayGoodbye function?',
          }))
      ..add(_makeReadable(
          testLabel: 'Part 3',
          testResult: await _asyncEquals(
              expected: 'Success! Thanks, see you next time',
              actual: await sayGoodbye(),
              typoKeyword: 'Success'),
          readableErrors: {
            _typoMessage:
                '$_typoMessage. Did you add the text \'Thanks, see you next time\'?',
            'null': '$didNotImplement sayGoodbye?',
            _noCatch:
                'Did you remember to call logoutUser within a try/catch block?',
            'Instance of \'Future<String>\' Thanks, see you next time':
                'Did you remember to use the \'await\' keyword in the sayGoodbye function?',
            'Instance of \'_Future<String>\' Thanks, see you next time':
                'Did you remember to use the \'await\' keyword in the sayGoodbye function?',
            'Instance of \'_Exception\'':
                'CAUGHT Did you remember to return a string?',
          }))
      ..removeWhere((m) => m.contains(_passed))
      ..toList();

    if (messages.isEmpty) {
      print('Success. All tests passed!');
    } else {
      messages.forEach(print);
    }
  } catch (e) {
    print('Tried to run solution, but received an exception: $e');
  }
}

// 테스트 헬퍼
String _makeReadable({
  required String testResult,
  required Map<String, String> readableErrors,
  required String testLabel,
}) {
  String? readable;
  if (readableErrors.containsKey(testResult)) {
    readable = readableErrors[testResult];
    return '$testLabel $readable';
  } else if ((testResult != _passed) && (testResult.length < 18)) {
    readable = _typoMessage;
    return '$testLabel $readable';
  } else {
    return '$testLabel $testResult';
  }
}

Future<String> _asyncEquals({
  required String expected,
  required dynamic actual,
  required String typoKeyword,
}) async {
  final strActual = actual is String ? actual : actual.toString();
  try {
    if (expected == actual) {
      return _passed;
    } else if (strActual.contains(typoKeyword)) {
      return _typoMessage;
    } else {
      return strActual;
    }
  } catch (e) {
    return e.toString();
  }
}

Future<String> _asyncDidCatchException(Function fn) async {
  var caught = true;
  try {
    await fn();
  } on Exception catch (_) {
    caught = false;
  }

  if (caught == true) {
    return _passed;
  } else {
    return _noCatch;
  }
}

const _typoMessage = 'Test failed! Check for typos in your return value';
const _passed = 'PASSED';
const _noCatch = 'NO_CATCH';
const _halfSecond = Duration(milliseconds: 500);

String _failOnce() {
  if (_logoutSucceeds) {
    return 'Success!';
  } else {
    _logoutSucceeds = true;
    throw Exception('Logout failed');
  }
}

bool _logoutSucceeds = false;
```

<details>
  <summary title="Expand for a hint on the 'Putting it all together' exercise.">힌트</summary>

  `greetUser` 및 `sayGoodbye` 함수는 비동기적이어야 하지만, 
  `addHello`는 일반적인 동기적 함수여야 합니다.

  기억하세요: [try-catch 문](/language/error-handling#catch)을 사용하여, 
  오류를 catch 하고 처리할 수 있습니다.

</details>

<details>
  <summary title="Expand for the solution of the 'Putting it all together' exercise.">솔루션</summary>

  ```dart
  String addHello(String user) => 'Hello $user';
  
  Future<String> greetUser() async {
    final username = await fetchUsername();
    return addHello(username);
  }
  
  Future<String> sayGoodbye() async {
    try {
      final result = await logoutUser();
      return '$result Thanks, see you next time';
    } catch (e) {
      return 'Failed to logout user: $e';
    }
  }
  ```

</details>

## 어떤 린트가 futures에 적합할까요? {:#which-lints-work-for-futures}

async 및 futures 작업 중 발생하는 일반적인 실수를 파악하려면, 
다음 린트를 [활성화](/tools/analysis#individual-rules)하세요.

* [`discarded_futures`][]
* [`unawaited_futures`][]

[`discarded_futures`]: /tools/linter-rules/discarded_futures
[`unawaited_futures`]: /tools/linter-rules/unawaited_futures

## 다음은 무엇인가요? {:#whats-next}

축하합니다. 튜토리얼을 마쳤습니다! 
더 자세히 알아보고 싶다면, 다음에 가야 할 곳에 대한 몇 가지 제안이 있습니다.

- [DartPad]({{site.dartpad}})로 놀아보세요.
- 다른 [튜토리얼](/tutorials)을 시도해보세요.
- Dart에서 futures와 비동기 코드에 대해 자세히 알아보세요.
  - [Streams 튜토리얼](/libraries/async/using-streams):
    비동기 이벤트 시퀀스를 사용하는 방법을 알아보세요.
  - [Dart의 동시성](/language/concurrency):
    Dart에서 동시성을 구현하는 방법을 이해하고 알아보세요.
  - [비동기 지원](/language/async):
    비동기 코딩을 위한 Dart의 언어 및 라이브러리 지원을 살펴보세요.
  - [Google의 Dart 비디오][Dart 비디오]:
    비동기 코딩에 대한 비디오를 하나 이상 시청하세요.
- [Dart SDK](/get-dart)를 받으세요!

[Dart videos]: {{site.yt.playlist}}PLjxrf2q8roU0Net_g1NT5_vOO3s_FR02J
[Future]: {{site.dart-api}}/{{site.sdkInfo.channel}}/dart-async/Future-class.html
[style guide]: /effective-dart/style
[documentation guide]: /effective-dart/documentation
[usage guide]: /effective-dart/usage
[design guide]: /effective-dart/design
