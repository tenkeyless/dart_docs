---
# title: Callable objects
title: 호출 가능한 객체
description: Dart에서 호출 가능한 객체를 생성하고 사용하는 방법을 알아보세요.
js: [{url: '/assets/js/inject_dartpad.js', defer: true}]
toc: false
prevpage:
  url: /language/extension-types
  # title: Extension types
  title: 확장 타입
nextpage:
  url: /language/class-modifiers
  # title: Class modifiers
  title: 클래스 수정자
---

<?code-excerpt replace="/ *\/\/\s+ignore_for_file:[^\n]+\n//g; /(^|\n) *\/\/\s+ignore: (stable|beta|dev)[^\n]+\n/$1/g; /(\n[^\n]+) *\/\/\s+ignore: (stable|beta|dev)[^\n]+\n/$1\n/g; /. • (lib|test)\/\w+\.dart:\d+:\d+//g"?>

Dart 클래스의 인스턴스가 함수처럼 호출되도록 하려면, `call()` 메서드를 구현합니다.

`call()` 메서드는 이를 정의하는 모든 클래스의 인스턴스가 함수를 에뮬레이트하도록 허용합니다. 
이 메서드는 (매개변수 및 반환 타입과 같은) 일반적인 [함수][functions]와 동일한 기능을 지원합니다.

다음 예에서, `WannabeFunction` 클래스는 세 개의 문자열을 가져와 공백으로 구분하고, 
느낌표를 추가하여 연결하는 `call()` 함수를 정의합니다. 
**Run**을 클릭하여 코드를 실행합니다.

<?code-excerpt "misc/lib/language_tour/callable_objects.dart"?>
```dartpad
class WannabeFunction {
  String call(String a, String b, String c) => '$a $b $c!';
}

var wf = WannabeFunction();
var out = wf('Hi', 'there,', 'gang');

void main() => print(out);
```

[functions]: /language/functions
