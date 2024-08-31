---
# title: The Dart type system
title: Dart 타입 시스템
# description: Why and how to write sound Dart code.
description: 왜 그리고 어떻게 좋은 Dart 코드를 작성해야 하는가.
prevpage:
  url: /language/typedefs
  title: Typedefs
nextpage:
  url: /language/patterns
  # title: Patterns
  title: 패턴
---
<?code-excerpt replace="/ *\/\/\s+ignore_for_file:[^\n]+\n//g; /([A-Z]\w*)\d\b/$1/g; /\b(main)\d\b/$1/g; /(^|\n) *\/\/\s+ignore:[^\n]+\n/$1/g; /(\n[^\n]+) *\/\/\s+ignore:[^\n]+\n/$1\n/g"?>
<?code-excerpt path-base="type_system"?>

Dart 언어는 타입 안전(type safe)합니다. 
정적 타입 검사와 [런타임 검사](#runtime-checks)를 결합하여, 
변수 값이 항상 변수의 정적 타입과 일치하는지 확인합니다. 
이를 사운드 타이핑(sound typing)이라고도 합니다. 
_타입_ 은 필수이지만, [타입 추론](#type-inference) 때문에 타입 _어노테이션_ 은 선택 사항입니다.

정적 타입 검사의 한 가지 이점은 Dart의 [정적 분석기(static analyzer)][analysis]를 사용하여, 
컴파일 타임에 버그를 찾을 수 있다는 것입니다.

대부분의 정적 분석 오류는 제네릭 클래스에 타입 어노테이션을 추가하여 수정할 수 있습니다. 
가장 일반적인 제네릭 클래스는 컬렉션 타입 `List<T>`와 `Map<K,V>`입니다.

예를 들어, 다음 코드에서 `printInts()` 함수는 정수 리스트를 출력하고, 
`main()`은 리스트를 만들어 `printInts()`에 전달합니다.

<?code-excerpt "lib/strong_analysis.dart (opening-example)" replace="/list(?=\))/[!$&!]/g"?>
```dart tag=fails-sa
void printInts(List<int> a) => print(a);

void main() {
  final list = [];
  list.add(1);
  list.add('2');
  printInts([!list!]);
}
```

이전 코드는 `printInts(list)`를 호출할 때, `list`에 대한 타입 오류(위에 강조 표시됨)를 발생시킵니다.

<?code-excerpt "analyzer-results-stable.txt" retain="/strong_analysis.*List.*argument_type_not_assignable/" replace="/-(.*?):(.*?):(.*?)-/-/g; /. • (lib|test)\/\w+\.dart:\d+:\d+//g"?>
```plaintext
error - The argument type 'List<dynamic>' can't be assigned to the parameter type 'List<int>'. - argument_type_not_assignable
```

이 오류는 `List<dynamic>`에서 `List<int>`로의 unsound 암묵적 캐스트를 강조합니다. 
`list` 변수는 정적 타입 `List<dynamic>`을 갖습니다. 
이는 초기화 선언 `var list = []`가 분석기에, 
`dynamic`보다 더 구체적인 타입 인수를 추론하기에 충분한 정보를 제공하지 않기 때문입니다. 
`printInts()` 함수는 `List<int>` 타입의 매개변수를 예상하여, 타입이 일치하지 않습니다.

목록(아래 강조 표시됨)을 생성할 때 타입 어노테이션(`<int>`)을 추가할 때, 
분석기는 문자열 인수를 `int` 매개변수에 할당할 수 없다고 불평합니다. 
`list.add('2')`에서 따옴표를 제거하면 정적 분석을 통과하고, 
오류나 경고 없이 실행되는 코드가 생성됩니다.

<?code-excerpt "test/strong_test.dart (opening-example)" replace="/<int.(?=\[)|2/[!$&!]/g"?>
```dart tag=passes-sa
void printInts(List<int> a) => print(a);

void main() {
  final list = [!<int>!][];
  list.add(1);
  list.add([!2!]);
  printInts(list);
}
```

[Try it in DartPad]({{site.dartpad}}/?id=25074a51a00c71b4b000f33b688dedd0).

## soundness란 무엇인가? {:#what-is-soundness}

*Soundness*는 프로그램이 특정 유효하지 않은 상태에 빠지지 않도록 하는 것입니다. 
sound *타입 시스템*은 표현식이 표현식의 정적 타입과 일치하지 않는 값으로 평가되는 상태에 결코 빠지지 않음을 의미합니다. 
예를 들어, 표현식의 정적 타입이 `String`인 경우, 런타임에 평가할 때 문자열만 얻을 수 있습니다.

Java 및 C#의 타입 시스템과 마찬가지로, Dart의 타입 시스템은 sound 입니다. 
정적 검사(컴파일 타임 오류)와 런타임 검사를 결합하여 soundness를 강제합니다. 
예를 들어, `String`을 `int`에 할당하는 것은 컴파일 타임 오류입니다. 
`as String`을 사용하여 객체를 `String`으로 캐스팅하면, 
객체가 `String`이 아니면 런타임 오류가 발생합니다.

## soundness란의 이점 {:#the-benefits-of-soundness}

sound 타입 시스템에는 여러 가지 이점이 있습니다.

* 컴파일 시 타입 관련 버그를 밝힙니다.<br>
  sound 타입 시스템은 코드에서 타입에 대해 모호하지 않도록 강제하므로, 
  런타임에 찾기 어려울 수 있는 타입 관련 버그가 컴파일 시에 밝혀집니다.

* 더 읽기 쉬운 코드.<br>
  값이 실제로 지정된 타입을 가지고 있다는 것을 믿을 수 있기 때문에 코드를 읽기가 더 쉽습니다. 
  sound Dart에서, 타입은 거짓말을 할 수 없습니다.

* 더 유지 관리하기 쉬운 코드.<br>
  sound 타입 시스템을 사용하면 코드 한 부분을 변경하면, 
  타입 시스템이 방금 손상된 다른 코드 부분에 대해 경고할 수 있습니다.

* 더 나은 사전 컴파일(AOT).<br>
  타입 없이도 AOT 컴파일이 가능하지만 생성된 코드는 훨씬 덜 효율적입니다.


## 정적 분석 통과를 위한 팁 {:#tips-for-passing-static-analysis}

정적 타입에 대한 대부분의 규칙은 이해하기 쉽습니다.
다음은 덜 명확한 규칙 중 일부입니다.

* 메서드를 재정의할 때 sound 반환 타입을 사용합니다.
* 메서드를 재정의할 때 sound 매개변수 타입을 사용합니다.
* 동적 리스트를 타입이 지정된 리스트로 사용하지 마십시오.

다음 타입 계층을 사용하는 예를 통해, 이러한 규칙을 자세히 살펴보겠습니다.

* Don't use a dynamic list as a typed list.

<img src="/assets/img/language/type-hierarchy.png" alt="a hierarchy of animals where the supertype is Animal and the subtypes are Alligator, Cat, and HoneyBadger. Cat has the subtypes of Lion and MaineCoon">

<a name="use-proper-return-types"></a>
### 메서드를 재정의할 때 sound 반환 타입을 사용합니다. {:#use-sound-return-types-when-overriding-methods}

서브클래스의 메서드 반환 타입은 슈퍼클래스의 메서드 반환 타입과 같은 타입이거나 하위 타입이어야 합니다. 
`Animal` 클래스의 getter 메서드를 생각해 보세요.

<?code-excerpt "lib/animal.dart (Animal)" replace="/Animal get.*/[!$&!]/g"?>
```dart
class Animal {
  void chase(Animal a) { ... }
  [!Animal get parent => ...!]
}
```

`parent` getter 메서드는 `Animal`을 반환합니다. 
`HoneyBadger` 하위 클래스에서 getter의 반환 타입을 `HoneyBadger`(또는 `Animal`의 다른 하위 타입)로 바꿀 수 있지만, 관련 없는 타입은 허용되지 않습니다.

<?code-excerpt "lib/animal.dart (HoneyBadger)" replace="/(\w+)(?= get)/[!$&!]/g"?>
```dart tag=passes-sa
class HoneyBadger extends Animal {
  @override
  void chase(Animal a) { ... }

  @override
  [!HoneyBadger!] get parent => ...
}
```

<?code-excerpt "lib/animal.dart (HoneyBadger)" replace="/HoneyBadger get/[!Root!] get/g"?>
```dart tag=fails-sa
class HoneyBadger extends Animal {
  @override
  void chase(Animal a) { ... }

  @override
  [!Root!] get parent => ...
}
```

<a name="use-proper-param-types"></a>
### 메서드를 재정의할 때 sound 매개변수 타입을 사용합니다. {:#use-sound-parameter-types-when-overriding-methods}

오버라이드된 메서드의 매개변수는 슈퍼클래스의 해당 매개변수와 같은 타입이거나 슈퍼타입이어야 합니다. 
원본 매개변수의 하위 타입으로 타입을 대체하여, 매개변수 타입을 "단단하게(tighten)" 하지 마십시오.

:::note
하위 타입을 사용해야 하는 타당한 이유가 있는 경우, 
[`covariant` 키워드](/guides/language/sound-problems#the-covariant-keyword)를 사용할 수 있습니다.
:::

`Animal` 클래스의 `chase(Animal)` 메서드를 고려해 보세요.

<?code-excerpt "lib/animal.dart (Animal)" replace="/void chase.*/[!$&!]/g"?>
```dart
class Animal {
  [!void chase(Animal a) { ... }!]
  Animal get parent => ...
}
```

`chase()` 메서드는 `Animal`을 취합니다. 
`HoneyBadger`는 무엇이든 chases 합니다.
`chase()` 메서드를 재정의하여, 무엇이든(`Object`) 취할 수 있습니다.

<?code-excerpt "lib/animal.dart (chase-Object)" replace="/Object/[!$&!]/g"?>
```dart tag=passes-sa
class HoneyBadger extends Animal {
  @override
  void chase([!Object!] a) { ... }

  @override
  Animal get parent => ...
}
```

다음 코드는 `chase()` 메서드의 매개변수를, 
`Animal`에서 `Animal`의 하위 클래스인 `Mouse`로 제한(tightens)합니다.

<?code-excerpt "lib/incorrect_animal.dart (chase-mouse)" replace="/Mouse/[!$&!]/g"?>
```dart tag=fails-sa
class [!Mouse!] extends Animal { ... }

class Cat extends Animal {
  @override
  void chase([!Mouse!] a) { ... }
}
```

이 코드는 고양이를 정의하고 악어 뒤에 보낼 수 있기 때문에, 타입 안전하지 않습니다.

<?code-excerpt "lib/incorrect_animal.dart (would-not-be-type-safe)" replace="/Alligator/[!$&!]/g"?>
```dart
Animal a = Cat();
a.chase([!Alligator!]()); // 타입 안전하지 않고, 고양이에게도 안전하지 않음.
```

### 동적 리스트를 타입이 지정된 리스트로 사용하지 마십시오. {:#dont-use-a-dynamic-list-as-a-typed-list}

`dynamic` 리스트는 여러 종류의 항목이 들어 있는 리스트를 원할 때 유용합니다. 
그러나, `dynamic` 리스트을 타입화된 리스트로 사용할 수는 없습니다.

이 규칙은 제네릭 타입의 인스턴스에도 적용됩니다.

다음 코드는 `Dog`의 `dynamic` 리스트을 만들고, 이를 `Cat` 타입의 리스트에 할당하는데, 
이는 정적 분석 중에 오류를 생성합니다.

<?code-excerpt "lib/incorrect_animal.dart (invalid-dynamic-list)" replace="/(<dynamic\x3E)(.*?)Error/[!$1!]$2Error/g"?>
```dart tag=fails-sa
void main() {
  List<Cat> foo = [!<dynamic>!][Dog()]; // Error
  List<dynamic> bar = <dynamic>[Dog(), Cat()]; // OK
}
```

## 런타임 검사 {:#runtime-checks}

런타임 검사는 컴파일 타임에 감지할 수 없는 타입 안전 문제를 처리합니다.

예를 들어, 다음 코드는 개 리스트를 고양이 리스트로 캐스팅하는 것이 오류이기 때문에, 
런타임에 예외를 throw합니다.

<?code-excerpt "test/strong_test.dart (runtime-checks)" replace="/animals as[^;]*/[!$&!]/g"?>
```dart tag=runtime-fail
void main() {
  List<Animal> animals = <Dog>[Dog()];
  List<Cat> cats = [!animals as List<Cat>!];
}
```


## 타입 추론 {:#type-inference}

분석기는 필드, 메서드, 로컬 변수 및 대부분의 제네릭 타입 인수에 대한 타입을 추론할 수 있습니다. 
분석기가 특정 타입을 추론하기에 충분한 정보가 없는 경우, `dynamic` 타입을 사용합니다.

다음은 제네릭에서 타입 추론이 작동하는 방식의 예입니다. 
이 예에서 `arguments`라는 변수는 문자열 키와 다양한 타입의 값을 쌍으로 묶는 맵을 보유합니다.

변수 타입을 명시적으로 나타낸다면, 다음과 같이 작성할 수 있습니다.

<?code-excerpt "lib/strong_analysis.dart (type-inference-1-orig)" replace="/Map<String, dynamic\x3E/[!$&!]/g"?>
```dart
[!Map<String, dynamic>!] arguments = {'argA': 'hello', 'argB': 42};
```

또는, `var` 또는 `final`을 사용하여 Dart가 타입을 추론하도록 할 수 있습니다.

<?code-excerpt "lib/strong_analysis.dart (type-inference-1)" replace="/var/[!$&!]/g"?>
```dart
[!var!] arguments = {'argA': 'hello', 'argB': 42}; // Map<String, Object>
```

맵 리터럴은 엔트리에서 타입을 추론하고, 변수는 맵 리터럴의 타입에서 타입을 추론합니다. 
이 맵에서, 키는 모두 문자열이지만, 값은 다른 타입(상한이 `Object`인 `String`과 `int`)을 갖습니다. 
따라서, 맵 리터럴은 `Map<String, Object>` 타입을 갖고, `arguments` 변수도 마찬가지입니다.

### 필드 및 메서드 추론 {:#field-and-method-inference}

지정된 타입이 없고 슈퍼클래스의 필드나 메서드를 오버라이드하는 필드나 메서드는, 
슈퍼클래스 메서드나 필드의 타입을 상속합니다.

선언되거나 상속된 타입이 없지만, 초기 값으로 선언된 필드는 초기 값에 따라 추론된 타입을 얻습니다.

### 정적 필드 추론 {:#static-field-inference}

정적 필드와 변수는 이니셜라이저에서 추론된 타입을 얻습니다. 
추론은 순환(cycle)을 만나면 실패합니다. 
(즉, 변수에 대한 타입을 추론하는 것은 해당 변수의 타입을 아는 것에 달려 있습니다)

### 지역 변수 추론 {:#local-variable-inference}

로컬 변수 타입은 초기화자가 있는 경우, 초기화자에서 추론됩니다. 이후의 할당은 고려되지 않습니다. 
이는 너무 정확한 타입이 추론될 수 있음을 의미합니다. 
만약 그렇다면, 타입 어노테이션을 추가할 수 있습니다.

<?code-excerpt "lib/strong_analysis.dart (local-var-type-inference-error)"?>
```dart tag=fails-sa
var x = 3; // x는 int로 추론됩니다.
x = 4.0;
```

<?code-excerpt "lib/strong_analysis.dart (local-var-type-inference-ok)"?>
```dart tag=passes-sa
num y = 3; // num은 double이나 int가 될 수 있습니다.
y = 4.0;
```

### 타입 인수 추론 {:#type-argument-inference}

생성자 호출 및 [제네릭 메서드](/language/generics#using-generic-methods) 호출에 대한 타입 인수는, 
발생 컨텍스트의 하향(downward) 정보와 생성자 또는 제네릭 메서드에 대한 인수의 상향(upward) 정보의 조합을 기반으로 추론됩니다. 
추론이 원하거나 기대하는 대로 작동하지 않는 경우, 항상 타입 인수를 명시적으로 지정할 수 있습니다.

<?code-excerpt "lib/strong_analysis.dart (type-arg-inference)"?>
```dart tag=passes-sa
// <int>[]라고 쓴 것처럼 추론됩니다.
List<int> listOfInt = [];

// <double>[3.0]이라고 쓴 것처럼 추론됩니다.
var listOfDouble = [3.0];

// Iterable<int>로 추론됩니다.
var ints = listOfDouble.map((x) => x.toInt());
```

마지막 예에서, `x`는 하향(downward) 정보를 사용하여 `double`로 추론됩니다. 
클로저의 반환 타입은 상향(upward) 정보를 사용하여 `int`로 추론됩니다. 
Dart는 `map()` 메서드의 타입 인수인 `<int>`를 추론할 때, 이 반환 타입을 상향 정보로 사용합니다.

## 타입 대체 {:#substituting-types}

메서드를 재정의할 때, 한 가지 타입의 것(이전 메서드에서)을 새로운 타입(새로운 메서드에서)을 가질 수 있는 것으로 대체합니다. 
마찬가지로, 함수에 인수를 전달할 때, 
한 가지 타입(선언된 타입이 있는 매개변수)을 가진 것을 다른 타입(실제 인수)을 가진 것으로 대체합니다. 
한 가지 타입을 가진 것을 하위 타입이나 상위 타입이 있는 것으로 대체할 수 있는 경우는 언제인가요?

타입을 대체할 때, _소비자(consumers)_ 와 _생산자(producers)_ 의 관점에서 생각하면 도움이 됩니다. 
소비자는 타입을 흡수하고 생산자는 타입을 생성합니다.

**소비자의 타입을 상위 타입으로, 생산자의 타입을 하위 타입으로 대체할 수 있습니다.**

간단한 타입 할당과 제네릭 타입을 사용한 할당의 예를 살펴보겠습니다.

### 간단한 타입 할당 {:#simple-type-assignment}

객체를 객체에 할당할 때, 언제 타입을 다른 타입으로 바꿀 수 있을까요? 
답은 객체가 소비자인지 생산자인지에 따라 달라집니다.

다음 타입 계층을 고려하세요.

<img src="/assets/img/language/type-hierarchy.png" alt="a hierarchy of animals where the supertype is Animal and the subtypes are Alligator, Cat, and HoneyBadger. Cat has the subtypes of Lion and MaineCoon">

`Cat c`가 _소비자_ 이고 `Cat()`가 _생산자_ 인, 다음의 간단한 할당을 생각해 보세요.

<?code-excerpt "lib/strong_analysis.dart (Cat-Cat-ok)"?>
```dart
Cat c = Cat();
```

소비적 위치에서는, 특정 타입(`Cat`)을 소비하는 것을, 
무엇이든 소비하는 것(`Animal`)으로 바꾸는 것은 안전합니다. 
즉, `Cat c`를 `Animal c`로 바꾸는 것이 허용됩니다. 
`Animal`은 `Cat`의 슈퍼타입이기 때문입니다.

<?code-excerpt "lib/strong_analysis.dart (Animal-Cat-ok)"?>
```dart tag=passes-sa
Animal c = Cat();
```

하지만 `Cat c`를 `MaineCoon c`로 바꾸면, 
슈퍼클래스가 `Lion`과 같이 다른 동작을 하는 Cat 타입을 제공할 수 있으므로, 
타입 안전성이 손상됩니다.

<?code-excerpt "lib/strong_analysis.dart (MaineCoon-Cat-err)"?>
```dart tag=fails-sa
MaineCoon c = Cat();
```

생산하는 위치에서는, 타입(`Cat`)을 생성하는 것을 더 구체적인 타입(`MaineCoon`)으로 대체하는 것은 안전합니다. 
따라서 다음이 허용됩니다.

<?code-excerpt "lib/strong_analysis.dart (Cat-MaineCoon-ok)"?>
```dart tag=passes-sa
Cat c = MaineCoon();
```

### 제네릭 타입 할당 {:#generic-type-assignment}

제네릭 타입에 대한 규칙은 동일합니까? 그렇습니다. 
동물 리스트의 계층을 고려하십시오. 
`Cat`의 `List`는 `Animal`의 `List`의 하위 타입이고, `MaineCoon`의 `List`의 상위 타입입니다.

<img src="/assets/img/language/type-hierarchy-generics.png" alt="List<Animal> -> List<Cat> -> List<MaineCoon>">

다음 예에서, `List<MaineCoon>`는 `List<Cat>`의 하위 타입이므로, 
`MaineCoon` 리스트를 `myCats`에 할당할 수 있습니다.

<?code-excerpt "lib/strong_analysis.dart (generic-type-assignment-MaineCoon)" replace="/<MaineCoon/<[!MaineCoon!]/g"?>
```dart tag=passes-sa
List<[!MaineCoon!]> myMaineCoons = ...
List<Cat> myCats = myMaineCoons;
```

다른 방향으로 가는 건 어떻습니까? `Animal` 리스트를 `List<Cat>`에 할당할 수 있나요?

<?code-excerpt "lib/strong_analysis.dart (generic-type-assignment-Animal)" replace="/<Animal/<[!Animal!]/g"?>
```dart tag=fails-sa
List<[!Animal!]> myAnimals = ...
List<Cat> myCats = myAnimals;
```

이 할당은 정적 분석을 통과하지 못합니다. 
왜냐하면, `Animal`과 같은 비 `dynamic` 타입에서는 허용되지 않는, 암묵적 다운캐스트를 생성하기 때문입니다.

이 타입의 코드를 정적 분석을 통과시키려면, 명시적 캐스트를 사용할 수 있습니다.

<?code-excerpt "lib/strong_analysis.dart (generic-type-assignment-implied-cast)" replace="/as.*(?=;)/[!$&!]/g"?>
```dart
List<Animal> myAnimals = ...
List<Cat> myCats = myAnimals [!as List<Cat>!];
```

하지만 명시적 캐스트는 캐스트되는 리스트의 실제 타입(`myAnimals`)에 따라 런타임에 실패할 수 있습니다.

### 메서드 {:#methods}

메서드를 재정의할 때, 생산자와 소비자 규칙은 여전히 ​​적용됩니다. 예를 들어:

<img src="/assets/img/language/consumer-producer-methods.png" alt="Animal class showing the chase method as the consumer and the parent getter as the producer">

소비자(예: `chase(Animal)` 메서드)의 경우, 매개변수 타입을 슈퍼타입으로 바꿀 수 있습니다. 
생산자(예: `parent` getter 메서드)의 경우, 반환 타입을 하위타입으로 바꿀 수 있습니다.

자세한 내용은 [메서드를 재정의할 때 sound 반환 타입 사용](#use-proper-return-types) 및 [메서드를 재정의할 때 sound 매개변수 타입 사용](#use-proper-param-types)을 참조하세요.


## 기타 리소스 {:#other-resources}

다음 리소스에는 sound Dart에 대한 추가 정보가 있습니다.

* [일반적인 타입 문제 수정](/guides/language/sound-problems) - 사운드 Dart 코드를 작성할 때 발생할 수 있는 오류와 이를 수정하는 방법.
* [타입 승격 실패 수정](/tools/non-promotion-reasons) - 타입 승격 오류를 수정하는 방법을 이해하고 학습합니다.
* [sound null 안전성](/null-safety) - 사운드 null 안전성을 갖춘 코드 작성에 대해 학습합니다.
* [정적 분석 커스터마이즈][analysis] - 분석 옵션 파일을 사용하여 분석기와 린터를 설정하고 커스터마이즈하는 방법.

[analysis]: /tools/analysis
[language version]: /guides/language/evolution#language-versioning
[null safety]: /null-safety
